# Gist-Reveal.it

[Gist-Reveal.it](http://gist-reveal.it/) is an open source slideshow templating service that makes it easy to create, edit, present, and share [Reveal.js](https://github.com/hakimel/reveal.js) slides on the web.

Just store any Revealjs-compatible [HTML](https://github.com/hakimel/reveal.js#markup) or [Markdown](https://github.com/hakimel/reveal.js#markdown) content in a github gist, then add the resulting gist id to the end of any gist-reveal site url to view the resulting templated presentation.

Conference organizers can host their own modified gist-reveal templating service to provide a consistent slideshow theme for all of the presentations at their event:

 * [gist-reveal.it](http://gist-reveal.it/af84d40e58c5c2a908dd)
 * [dockercon-slides.com](http://dockercon-slides.com/af84d40e58c5c2a908dd)

### Application Config

The following environment variables can be used to autoconfigure the application:

Variable Name  | Contents   |  Default Value
---------------|------------|---------------
DEFAULT_GIST   | The default gist id slideshow content for the site | af84d40e58c5c2a908dd
GH_CLIENT_SECRET | GitHub client secret | unset
GH_CLIENT_ID | GitHub client ID | unset
GA_TRACKER | Google Analytics tracker token | unset
PORT | The server port number | 8080
IP_ADDR | The server IP address | 0.0.0.0
REVEAL_WEB_HOST | The site's hostname | localhost
REVEAL_SOCKET_SECRET | the site's broadcast token (alphanumeric) | randomly generated

See [`plugin/hosted/index.js`](https://github.com/ryanj/gist-reveal.it/edit/master/plugin/hosted/index.js) for more information about the site's configuration options.

### Broadcasting Slide Transitions

Administrators can configure the application's `REVEAL_SOCKET_SECRET` to broadcast slide transitions using Reveal's [socket Multiplexing support](https://github.com/hakimel/reveal.js#multiplexing).

Presenters who know the site's `REVEAL_SOCKET_SECRET` value can configure their browser as a presentation device using the `setToken` querystring param:

    http://YOUR_REVEAL_HOST_URL/?setToken=REVEAL_SOCKET_SECRET_VALUE

This token will be stored in the browser's `localStorage` area (per host url) as `localStorage.secret`. To reconfigure your browser as a client device (as a listener), use the `clearToken` querystring param:

    http://YOUR_REVEAL_HOST_URL/?clearToken

### Local Development

Start this project locally by running `npm install` followed by `npm start`.

## OpenShift Hosting

[![Build Status](https://ci-shifter.rhcloud.com:443/buildStatus/icon?job=slide-build)](http://slide-shifter.rhcloud.com)

This application can be launched on any OpenShift cloud using the `rhc` command-line tool:

```bash
rhc app create gistreveal nodejs-0.10 \
--from code=http://github.com/ryanj/gist-reveal.it \ 
DEFAULT_GIST=YOUR_DEFAULT_GIST_ID \ 
GH_CLIENT_SECRET=YOUR_GH_CLIENT_SECRET \ 
GH_CLIENT_ID=YOUR_GH_CLIENT_ID \ 
REVEAL_SOCKET_SECRET=0P3N-S0URC3 \ 
GA_TRACKER=YOUR_GA_TRACKER
```

Or, [click here to launch on the web](https://openshift.redhat.com/app/console/application_types/custom?name=reveal&initial_git_url=https%3A%2F%2Fgithub.com/ryanj/gist-reveal.it.git&cartridges[]=nodejs-0.10)!

Then, use the `rhc env set` command to publish [configuration strings](#application-config) into the application's system environment.

## Docker 

To run [the docker image](https://registry.hub.docker.com/u/ryanj/gist-reveal.it/) locally on port `8080`:

    docker pull ryanj/gist-reveal.it
    docker run -d -p 8080:8080 ryanj/gist-reveal.it

[Environment variables](#Application_Config) can be passed into the Docker container in order to configure the websocket relay, or to change the default slideshow content: 

    docker run -e "REVEAL_WEB_HOST=YOUR_HOSTNAME_HERE" -e "REVEAL_SOCKET_SECRET=0P3N-S0URC3" -e "DEFAULT_GIST=YOUR_DEFAULT_GIST_ID" ryanj/gist-reveal.it
    
### OpenShiftM5 & Kubernetes 

A [sample kubernetes pod configuration file](https://github.com/ryanj/gist-reveal.it/blob/master/reveal-pod.json) is included for running [this project's Docker build](https://registry.hub.docker.com/u/ryanj/gist-reveal.it/) on [an OriginM5 hosting environment](https://github.com/openshift/origin#getting-started):

```bash
export DEFAULT_GIST=YOUR_DEFAULT_GIST_ID 
export GH_CLIENT_SECRET=YOUR_GH_CLIENT_SECRET 
export GH_CLIENT_ID=YOUR_GH_CLIENT_ID 
export REVEAL_SOCKET_SECRET=0P3N-S0URC3 
export GA_TRACKER=YOUR_GA_TRACKER
$GOPATH/src/github.com/openshift/origin/_output/go/bin/openshift kube create pods -c ~/src/gist-reveal.it/reveal-pod.json
```

## License

[gist-reveal,it](http://gist-reveal.it/) was created at the first [DockerCon Hackathon](http://blog.docker.com/2014/07/dockercon-video-dockercon-hackathon-winners/) by [@ryanj](https://github.com/ryanj) and [@fkautz](https://github.com/fkautz).

[Reveal.js](https://github.com/hakimel/reveal.js) is MIT licensed
Copyright (C) 2014 Hakim El Hattab, http://hakim.se
