<h1 align="center">
  <a href="https://xuset.github.io/planktos/">
    <img src="https://xuset.github.io/planktos/planktos-logo.png" width="35" alt="planktos">
  </a>
  Planktos
</h1>

<p align="center">
  <a href="https://travis-ci.org/xuset/planktos">
    <img alt="Build Status" src="https://travis-ci.org/xuset/planktos.svg?branch=master">
  </a>
  <a href="https://npmjs.org/package/planktos">
    <img alt="NPM" src="https://img.shields.io/npm/v/planktos.svg">
  </a>
  <a href="https://www.paypal.me/xuset">
    <img alt="Donate" src="https://img.shields.io/badge/Donate-PayPal-green.svg">
  </a>
</p>

<p align="center">
  <a href="https://saucelabs.com/u/xuset-planktos">
    <img alt="Sauce Test Status" src="https://saucelabs.com/browser-matrix/xuset-planktos.svg">
  </a>
</p>

Planktos enables websites to serve their static content over BitTorrent by turning users into seeders. That means that users viewing a website with Planktos are also serving the website to other users. This allows website owners to significantly reduce hosting costs for static content, scale in real-time without provisioning more web servers, and prevent user impact during an outage. Planktos works in vanilla Chrome and Firefox (no browser extensions needed).

Installing Planktos into a website is as simple as including the Planktos `install.js` script and using the Planktos command line interface to bundle the website's static files into a torrent. For typical use cases, Planktos is designed to work out of the box, and for more specialized use cases, Planktos has a very simple interface for customization.

A special thanks to the [WebTorrent](https://webtorrent.io) project, which is used extensively in Planktos.

## Why

- Let users load your static assets from each other, instead of hitting your web servers
- After a user visits your website for the first time, he/she will be able to load it even if your web servers go down
- Planktos auto scales your website when traffic increases
- Integrating Planktos into your website is stupidly simple

## Setup

The Planktos command line interface (CLI) copies the Planktos library files and packages your website's files into a torrent. To install the tool run:

`npm install -g planktos`

Change your current working directory to the root of your website. To copy the Planktos library files and package your website into a torrent run:

`planktos [directories and/or files...]`

In the previous step, the Planktos CLI copied the service worker, named `planktos.sw.js`, into your website's root directory. The service worker needs to be registered using the below install script (or manually):

`<script src="/planktos/install.js"></script>`

After updating your website's files you have to run the Planktos CLI again. If you already have a build script for your website, you should add the Planktos command to it.

That was it. To test that everything is working as expected, use your browser's developer tools to inspect the network requests your website makes.

Requirements for Planktos Websites:
 * The site must be served over HTTPS (or HTTP on localhost), because service workers can only be registered on secure websites
 * The web server must support the HTTP Range header, because the server is used as the initial seeder (see WebTorrent webseed). Most web servers support this feature; however, some, like Python's _simplehttpserver_, do not.

## How it Works

The Planktos CLI creates a snapshot of the website's static files and bundles everything into a torrent so users can download and seed the website.

When the webpage is loaded for the first time, Planktos registers a service worker that intercepts all HTTP requests made by the webpage. When the service worker intercepts a request, Planktos checks to see if the requested file is present in the torrent. If the file is in the torrent, it is downloaded from peers, otherwise, it is downloaded over HTTP as it normally would be. All torrent files are stored in the persistent IndexedDB storage so a given file only has to be downloaded once regardless of how many times it is requested. Invalidating the users cache just requires creating a new torrent with the updated files.

Due to the fact that service workers cannot use the [WebRTC](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API) API, the actual downloading of torrents is delegated to a Planktos controlled webpage. Planktos accomplishes this by injecting a downloader script into the webpage when the fetch request is intercepted. See the [W3C issue](https://github.com/w3c/webrtc-pc/issues/230) for more info on WebRTC in service workers.

## Limitations

_Disclaimer:_ Planktos is still in early stages of development, and is not recommended for production use yet.

Planktos relies on cutting edge browser APIs, including WebRTC and service workers, that have not been adopted in all browsers. In cases where any required APIs are not supported, Planktos defaults to loading webpages over HTTP as the browser normally would. It seems that development of most of these APIs is in progress for all major browsers, so we are hopeful that Planktos will work in all browsers in the near future.

Blocking Issues:
 * No streaming support. The requested file must be downloaded in it's entirety before it can be displayed to the user. Currently, only chrome supports streaming from service workers while Firefox has an [open issue](https://bugzilla.mozilla.org/show_bug.cgi?id=1128959) for it.
 * Sharding into multiple torrents is not currently supported, so Planktos will be infeasible for large websites

## Documentation

[Read the docs here](https://github.com/xuset/planktos/wiki)

## Contribute

Contributions are always welcome!

Once you have some changes, you can test them with: `npm test`

Or to automatically run the tests when files are changed: `npm run watch`

Some helpful tidbits:
 * The browser tests may occasionally timeout if the browser is not focused
 * Tests will fail on some systems with `TypeError: Cannot read property 'unmask' of undefined` because of an incompatible optional dependency of a dependency. To fix this just delete the `node_modules/bufferutil`




## License

MIT. Copyright (c) Austin Middleton.
