&nbsp;

<p align="center">
  <a href="https://firepress.org/">
    <img src="https://user-images.githubusercontent.com/6694151/50166045-2cc53000-02b4-11e9-8f7f-5332089ec331.jpg" width="340px" alt="FirePress" />
  </a>
</p>

<p align="center">
    <a href="https://firepress.org/">FirePress.org</a> |
    <a href="https://play-with-ghost.com/">play-with-ghost</a> |
    <a href="https://github.com/firepress-org/">GitHub</a> |
    <a href="https://twitter.com/askpascalandy">Twitter</a>
    <br /> <br />
    <a href="https://travis-ci.org/firepress-org/ghostfire">
        <img src="https://images.microbadger.com/badges/version/devmtl/ghostfire.svg" alt="version" />
    </a>
    <a href="https://hub.docker.com/r/devmtl/ghostfire">
        <img src="https://images.microbadger.com/badges/image/devmtl/ghostfire.svg" alt="Downloads" />
    </a>
    <a href="https://travis-ci.org/firepress-org/ghostfire">
        <img src="https://travis-ci.org/firepress-org/ghostfire.svg" alt="CICD build" />
    </a>
</p>

&nbsp;

# [ghostfire](https://github.com/firepress-org/ghostfire)

<br>

## What is this?

**Docker image** — This is a Docker image to run Ghost (v 2.x.x) in a container 🐳. Fully compatible with a simple `docker run`, Docker Swarm or Kubernetes. Ensure you have Docker installed on your machine.

**What is Ghost?** — Ghost is an open source software that lets you create your website with a blog. See the [FAQ section](https://play-with-ghost.com/ghost-themes/faq/#what-is-ghost) for more details.

<br>

## Why forking the official Docker image?

- [x] Use a complexe but easy to follow **multi-stage build**. This docker image is much smaller. See details below.
- [x] Use a `node-core` layer in order to **not include** npm, yarn, npx and friends in the final docker image
- [x] **Compress** `node` using `upx`
- [x] Use [**tini**](https://github.com/krallin/tini#why-tini)
- [x] Use a better `config.production.json` **template**. These overide the [default one](https://github.com/TryGhost/Ghost/blob/0faf89b5abcf6de747d4b309cdac364a863c71dc/core/server/config/defaults.json#L82)
- [x] Have `curl` in the final image to support **`healthchecks`**
- [x] Enhanced **unit tests with scanners** (Aqua Security, Trivy) when Travis CI does it's thing
- [x] Using **LABELS** based on the opencontainer standard
- [x] Uninstall the `ghost cli` to save some space in the final docker image
- [x] Use `npm cache clean --force` to safe some space
- [x] The Docker image is **multi-arch ready**: `AMD64`, `ARM64`, `ARM`
- [x] Feature requests are [tracked in our issues](https://github.com/firepress-org/ghostfire/labels/feature%20request)

Overall, I do my best to apply **best practices**. Please let me know if something can be improved :)


#### Comparing docker image sizes

```
multi-stage / using my node-core
devmtl/ghostfire:2.23.4-77c5e56                 201 MB (74 MO)

multi-stage / simply using node_10.16-alpine
devmtl/ghostfire:2.23.3-bf541c7                 246 MB (79 MO)

single-stage docker official
ghost:alpine  / simply using node_10.16-alpine  284MB (123 MB)
```

<br>

## Live Demo

Want to try Ghost quickly? This is for you!

[play-with-ghost.com](https://play-with-ghost.com/) is a playground to learn about Ghost. What's remarkable here, is that you have the option to log into the admin panel of each live demo available, by using dummy credentials.

In short, you can try Ghost on the spot without having to sign-up!

<br>

[![pwg-video-preview-e](https://user-images.githubusercontent.com/6694151/50233512-9bbc8a80-0381-11e9-83bb-f29a67000378.jpg)
](https://play-with-ghost.com/)

<br>

## How to use this docker image?

First, find the latest docker images tags 🐳.

#### Docker hub
Find the latest tags here: 
[https://hub.docker.com/r/devmtl/ghostfire/tags/](https://hub.docker.com/r/devmtl/ghostfire/tags/)

#### Travis CI
[https://travis-ci.org/firepress-org/ghostfire](https://travis-ci.org/firepress-org/ghostfire)

At this point, this docker image has been pulled more than **11 millions of time**!

![docker-hub](https://user-images.githubusercontent.com/6694151/53067692-4c8af700-34a3-11e9-9fcf-9c7ad169a91b.jpg)

#### Option #1 (let the script work for you):

- Run the script by typing: `./runup.sh`

#### Option #2:

```
GHOSTFIRE_IMG="devmtl/ghostfire:2.23.4-77c5e56"

docker run -d \
—name ghostblog \
-p 2368:2368 \
-e url=http://localhost:2368 \
${GHOSTFIRE_IMG}
```

#### Option #3 (Stateful with configs):

```
GHOSTFIRE_IMG="devmtl/ghostfire:2.23.4-77c5e56"

docker run -d \
—name ghostblog \
-p 2368:2368 \
-e url=http://localhost:2368 \
-v /myuser/localpath/ghost/content:/var/lib/ghost/content \
-v /myuser/localpath/ghost/content:/var/lib/ghost/config.production.json \
${GHOSTFIRE_IMG}
```

To configure the `config.production.json` refer the [ghost docs](https://docs.ghost.org/concepts/config/).

#### master branch (stable) tags 🐳

For the **stable** branch, I recommend using the tag from the **first line**:

```
devmtl/ghostfire:2.23.4-77c5e56 (best practice)
devmtl/ghostfire:2.23.4
devmtl/ghostfire:stable
```

#### edge branch (dev) tags 🐳

For the **edge** branch (for dev), I recommend using the tag from the **last line**:

```
devmtl/ghostfire:edge-2.23.4-7d64db0
devmtl/ghostfire:edge-2.23.4
devmtl/ghostfire:edge
```

<br>

## DevOps best practices

⚠️ Workflow warning. You would expect that we would merge `edge` into `master`. We don’t do this. Instead, think of it as independent projects. The main reason is that the **.travis.yml is not the same in those two branches**.

Let's understand our processes.

Because we run a lot of websites in production using this image, we prefer to do UAT (unit acceptance tests) using the `edge` branch. **In other words, it’s a manual checkpoint to avoid a crash at scale.** DevOps best practices are essential to us. Many checkpoints ensure this Docker image for Ghost software runs smoothly.

It also has the advantage of keeping a clean commit history in the master branch (without doing git-fu all the time).

In this post « [How we update hundreds of Ghost's websites on Docker Swarm?](https://firepress.org/en/how-we-update-hundreds-of-ghosts-websites-on-docker-swarm/) », we explain how we deploy Ghost in production and which best practices we do follow.

## Enhanced unit tests (screen shots from Travis CI)

![unit-test-a](https://user-images.githubusercontent.com/6694151/61649557-b80a0800-ac7f-11e9-85b7-425e5456cb2d.jpg)
<br>
![unit-test-b](https://user-images.githubusercontent.com/6694151/61649559-b80a0800-ac7f-11e9-9154-cd6f5264af71.jpg)
<br>
![unit-test-c](https://user-images.githubusercontent.com/6694151/61649558-b80a0800-ac7f-11e9-9638-4b3241e4dcee.jpg)

<br>

## Developing Ghost themes locally

I open sourced [my setup here](https://github.com/firepress-org/ghost-local-dev-in-docker). It’s a workflow to run Ghost locally within a Docker container. Once your local paths are defined, it’s enjoyable and easy to work **between many themes**.

<br>

## Random stuff

**Breaking change**. If you still run Ghost 0.11.xx (not recommanded!), be aware of the container's path difference.

```
- Ghost 2.x.x is: /var/lib/ghost/content
- Ghost 1.x.x is: /var/lib/ghost/content
- Ghost 0.11.x is: /var/lib/ghost
```

**SQLite Database**

This Docker image for Ghost uses SQLite. There is nothing special to configure.

**What is the Node.js version?**

We follow the latest Node supported version. See this in the Dockerfile.

```
docker exec <container-id> node --version
```

You can also see this information in the Travis CI logs.

<br>

## FirePress Hosting

At FirePress we empower entrepreneurs and small organizations to create their websites on top of [Ghost](https://firepress.org/en/faq/#what-is-ghost).

At the moment, our **pricing** for hosting one Ghost website is $15 (Canadian dollars). This price will be only available for our first 100 new clients, starting May 1st, 2019 🙌. [See our pricing section](https://firepress.org/en/pricing/) for details.

More details [about this annoucement](https://forum.ghost.org/t/host-your-ghost-website-on-firepress/7092/1) on Ghost's forum.

<br>

## Contributing

The power of communities pull request and forks means that `1 + 1 = 3`. You can help to make this repo a better one! Here is how:

1. Fork it
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request

Check this post for more details: [Contributing to our Github project](https://pascalandy.com/blog/contributing-to-our-github-project/). Also, by contributing you agree to the [Contributor Code of Conduct on GitHub](https://pascalandy.com/blog/contributor-code-of-conduct-on-github/). It's plain common sense really.

<br>

## License

- This git repo is under the **GNU V3** license. [Find it here](https://github.com/pascalandy/GNU-GENERAL-PUBLIC-LICENSE/blob/master/LICENSE.md).
- The Ghost’s software is under the **MIT** license. [Find it here](https://ghost.org/license/).

<br>

## Sources & Fork

- This Git repo is available at [https://github.com/firepress-org/ghostfire](https://github.com/firepress-org/ghostfire)
- Forked from the [official](https://github.com/docker-library/ghost/) Ghost image

<br>

## Why all this work?

Our [mission](https://firepress.org/en/our-mission/) is to empower freelancers and small organizations to build an outstanding mobile-first website.

Because we believe your website should speak up in your name, we consider our mission completed once your site has become your impresario.

For more info about the man behind the startup, check out my [now page](https://pascalandy.com/blog/now/). You can also follow me on Twitter [@askpascalandy](https://twitter.com/askpascalandy).

— The FirePress Team 🔥📰
