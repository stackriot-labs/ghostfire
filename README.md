# ghostfire

## Continuous integration (CI) status

- [![Build Status](https://travis-ci.org/firepress-org/ghostfire.svg)](https://travis-ci.org/firepress-org/ghostfire)
- [![](https://images.microbadger.com/badges/image/devmtl/ghostfire.svg)](https://microbadger.com/images/devmtl/ghostfire "Get your own image badge on microbadger.com")
- [![](https://images.microbadger.com/badges/version/devmtl/ghostfire.svg)](https://microbadger.com/images/devmtl/ghostfire "Get your own version badge on microbadger.com")


## What is this

It’s a Docker image for running Ghost V2 in a container. We don’t support V1 anymore. 

**What is Ghost?** — Ghost is an open source software that lets you create your website with a blog. See the [FAQ section](https://play-with-ghost.com/ghost-themes/faq/#what-is-ghost) for more details.


##  Live Demo (online)

[play-with-ghost.com](https://play-with-ghost.com/) is a playground to learn about Ghost. Of course you can see Ghost themes. The biggest attraction here is having the ability to log in the **admin panel** by using the available credentials.

In short, you can try Ghost on the spot without having to sign-up!

[![play-with-ghost-splash](https://user-images.githubusercontent.com/6694151/50162402-419dc580-02ac-11e9-92b7-f75f8f2c08e1.jpg)](https://play-with-ghost.com/)


#### A little video that show you what you can do

[![pwg-video-preview-b](https://user-images.githubusercontent.com/6694151/50162583-bc66e080-02ac-11e9-937f-741f6d388d40.jpg)](https://play-with-ghost.com/#video)

## How to use this image

To run Ghost in a Docker container, here is the setup I use in production. Just execute `runup.sh` bash script and you are good to go. This setup also allows you to run your blog under a subdirectory like `mysite.com/blog`.

Ensure you have Docker installed on your server. To update your Ghost container, just stop the container and execute to runup.sh again.

**Option #1** *(prefered)*:
- Run the script: `./runup.sh`

**Option #2**:

```
GHOSTFIRE_IMG="devmtl/ghostfire:2.8.0-fa4877b"

docker run -d \
—name ghostblog \
-p 2368:2368 \
-e url=http://localhost:2368 -e NODE_ENV=production \
${GHOSTFIRE_IMG}
```

**Option #3 (Stateful)**

Don’t forget to change the path on your local drive!

```
GHOSTFIRE_IMG="devmtl/ghostfire:2.8.0-fa4877b"

docker run -d \
—name ghostblog \
-p 2368:2368 \
-e url=http://localhost:2368 -e NODE_ENV=production \
-v /myuser/local-path/ghost/content:/var/lib/ghost/content \
${GHOSTFIRE_IMG}
```

### Find the most recent docker image 🐳

Instead of using edge use the **stable image**.

- **Docker hub** — https://hub.docker.com/r/devmtl/ghostfire/tags/
- **Travis** — https://travis-ci.org/firepress-org/ghostfire

As an example:

### master branch (stable) tags looks like:

```
IMAGE_SHA_SHORT=devmtl/ghostfire:2.8.0-fa4877b
IMAGE_DATE=devmtl/ghostfire:20181211_15H4408
IMAGE_VERSION=devmtl/ghostfire:2.8.0
IMAGE_FULL=devmtl/ghostfire:2.8.0-fa4877b-20181211_15H4408
```

I recommend to follow the tag $IMAGE_VERSION

### edge branch tags looks like:

```
IMAGE_SHA_SHORT=devmtl/ghostfire:2.9.0-edge-f44b15e
IMAGE_DATE=devmtl/ghostfire:2.9.0-edge-20181217_18H5249
IMAGE_VERSION=devmtl/ghostfire:2.9.0-edge
IMAGE_EDGE=devmtl/ghostfire:edge
```


### edge vs. master branches

⚠️ Warning - I don’t merge edge into master! Those two branches are like two independant project.

Because I run a lot of websites in production using this image, I prefer to do my UAT (tests) using a dedicated `edge` branch.

Once I confirm the edge build PASS, I update the Dockerfile in the `master` branch as well. At this point, I’m reaaaaaally confident the docker image is working correctly.

![ghostfire-screen-2018-07-12_21h46](https://user-images.githubusercontent.com/6694151/42668147-195cfb74-861d-11e8-9d61-d847da6147f9.jpg)


## Developing Ghost theme, my setup

I open sourced [my setup here](https://github.com/firepress-org/ghost-local-dev-in-docker). It’s a workflow to run Ghost locally within a Docker container. Once your local paths are define it’s really fun and easy to work between many themes.


## Various

**Breaking change**

- Ghost 2.x.x is: /var/lib/ghost/content
- Ghost 1.x.x is: /var/lib/ghost/content
- Ghost 0.11.x is: /var/lib/ghost

*If you still run Ghost 0.11.xx, be aware of the container's path difference.*

**SQLite Database**

This Docker image for Ghost uses SQLite. There is nothing special to configure.

**What is the Node.js version?**

```
docker exec <container-id> node --version
```

You can also see this information in the Dockerfile and in the Travis builds.


## Why forking the official Dockerfile?

I tweaked few elements like:

- Ghost container is running under [tini](https://github.com/krallin/tini#why-tini)
- A cleaner ENV VAR management
- Uninstall the `ghost cli` to safe few bytes in the docker image
- In the future, the image will support multi-stage build


## Contributing

The power of communities pull request and forks means that `1 + 1 = 3`. Help me make this repo a better one!

1. Fork it
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request


## Copyright & License

- This git repo is under the **GNU** license information. [Find it here](https://github.com/pascalandy/GNU-GENERAL-PUBLIC-LICENSE).
- The Ghost’s software, is under the **MIT** license. [Find it here](https://ghost.org/license/).


## Sources & Fork

- **linear-build** — This Git repo is available [here](https://github.com/firepress-org/ghostfire). It’s based on the official [Ghost image](https://github.com/docker-library/ghost/tree/7eb6348d2a5493546577508d2cbae0a9922e1390/1/alpine)
- **multi-build** — A multi-build version might interest you:<br> https://github.com/mmornati/docker-ghostblog.


## FirePress Hosting

**At FirePress we do one thing and we do it with our whole heart: we host fully managed Ghost’s websites**. The idea behind FirePress is to empower freelancers and small organizations to be able to build an outstanding mobile-first website.

We also offer a **workshop** where participants ends up with a website/blog they can easily operate themselves. Details are coming soon. It is available in those cities:

- Montréal - Canada
- Toronto - Canada
- New-York - USA
- Québec City - Canada

Because we believe your website should speak up in your name, we consider our mission completed once your site has become [your impresario](https://play-with-ghost.com/ghost-themes/why-launching-your-next-website-with-firepress/). Start your [free trial here](https://play-with-ghost.com/ghost-themes/free-10-day-trial/).


## Keep in touch

- [Pascal Andy’s « now page »](https://pascalandy.com/blog/now/)
- Follow me on [Twitter](https://twitter.com/askpascalandy)
- Find more Ghost Themes on [play-with-ghost.com](https://play-with-ghost.com/)