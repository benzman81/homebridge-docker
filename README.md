# Homebridge on Synology

[This Docker image](https://hub.docker.com/r/marcoraddatz/homebridge/) helps you to easily setup [Homebridge](https://github.com/nfarina/homebridge) on your Synology, but also runs on QNAP devices or Raspberry Pies. If you're using a Synology, please make sure to run at least DSM 6, otherwise you [might have to go the harder way](https://marcoraddatz.com/en/2016/07/13/run-homebridge-on-a-synology/) via SSH/shell scripts.

## Installation (on Synology)

### 1. Download Image

First of all, login to your Synology DSM, open Docker and download `marcoraddatz/homebridge` from within the registry.

![Registry Overview.](https://raw.githubusercontent.com/marcoraddatz/homebridge-docker/master/doc/docker-1.png)

### 2. Prepare your scripts

Create a folder via DSM File Station at `/volume1/docker/homebridge` and put your `config.json` and **either** your `package.json` or `install.sh` (recommended) in there. If you'd like to develop an own plugin or debug Homebridge, also copy the `.env` file.

![File Station.](https://raw.githubusercontent.com/marcoraddatz/homebridge-docker/master/doc/file-station-1.png)

Here are two examples of how your setup files can look like:

#### package.json

```json
{
  "name": "DiskStationHomeBridge",
  "description": "HomeKit support for the impatient.",
  "version": "0.1.1",
  "license": "ISC",
  "dependencies": {
    "homebridge-harmonyhub": "0.2.0-alpha",
    "homebridge-netatmo": "^0.0.4",
    "homebridge-nukiio": "^0.0.3",
    "homebridge-synology": "^0.1.0"
  }
}
```

#### install.sh

```shell
#!/bin/bash

npm install -g homebridge-harmonyhub
npm install -g homebridge-netatmo
npm install -g homebridge-nukiio
npm install -g homebridge-synology
```

Note that you can also use this file to install server dependencies. Since version 2.1 all commands get executed as root.

### 3. Start Container

After your plugins are configured, you're now ready to launch the Docker container.

![Launch container.](https://raw.githubusercontent.com/marcoraddatz/homebridge-docker/master/doc/docker-2.png)

![Click 'Advanced Settings'.](https://raw.githubusercontent.com/marcoraddatz/homebridge-docker/master/doc/docker-3.png)

![Link local configs.](https://raw.githubusercontent.com/marcoraddatz/homebridge-docker/master/doc/docker-4.png)

Link the `/volume1/docker/homebridge` to `/root/.homebridge`, otherwise, the configuration files won’t be loaded (within DSM the path is only shown as `/docker/homebridge`. Make sure you **don’t** click "read only".

![Allow container to work act as host.](https://raw.githubusercontent.com/marcoraddatz/homebridge-docker/master/doc/docker-5.png)

Don't forget to click the *Use the same network as Docker Host* checkbox!

![That's it!](https://raw.githubusercontent.com/marcoraddatz/homebridge-docker/master/doc/docker-6.png)

Once you launch (or restart) the container, Homebridge and all its dependencies will be installed. To verify that Homebridge is running or if an error occurs, you can use the container's log (Container → Details → Log) to find out the reason why.

## Command line

There are many Docker commands that can be run via the `homebridge.sh` script. Please have a look [at the file](homebridge.sh), it should be self-explaining.

## Plugin development

If you intend to develop a plugin or Homebridge is not running as it's supposed to, you might want to start Homebridge with debugging options. To do so, copy the sample `.env` file to `/volume1/docker/homebridge` and set the desired environment. Don't forget to place your plugins manually under `/volume1/docker/homebridge/plugins` and (re-) start the container. See the [Homebridge docs](https://github.com/nfarina/homebridge#plugin-development) for details.

### .env options

All `.env` variables are optional.

#### HOMEBRIDGE_ENV

- **production** _(default)_  
Starts Homebridge without any options.
- **development**  
Starts Homebridge with plugin support.
- **debug**  
Starts Homebridge with plugin support and maximum debugging info.

#### HOMEBRIDGE_VERSION

To avoid breaks with either new Homebridge releases or iOS updates, you can force the installation of a specific Homebridge version. `HOMEBRIDGE_VERSION` accepts any release tag (f.e "0.4.17") or valid NPM version range.

## Troubleshooting

In case, that Homebridge doesn't run as expected, please follow these steps for debugging:

### 1. Verify your `config.json`

Many issues appear because of invalid JSON. Before you open a ticket, please make sure that the syntax is ok. A good way to verify your config is to use the [jsonlint.com](http://jsonlint.com) validator.

### 2. Make sure that plugins have been disabled

Most issues aren't related to this package, nor to Homebridge --  they are created by outdated plugins or wrong configuration. This package focuses on making Homebridge run as easy as possible. So if Homebridge runs without any plugins, enable them step by step until you find out, which plugin creates the error. You should then contact the plugin's creator.

## License

This project is licensed under Apache License V2 (see [LICENSE](LICENSE)).

## Changelog

Changes can be found in the [changelog.md](CHANGELOG.md).

## Thanks
- [Chris Brandlehner](https://github.com/cbrandlehner/homebridge-docker)
- [Parker Smith](https://github.com/psmith3/synology-docker-homebridge)
- Christian Haugen
