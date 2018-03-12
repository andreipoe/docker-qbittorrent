docker-qbittorrent for AArch64
================================================

This is a Docker image for running [qBittorrent](http://www.qbittorrent.org/) on AArch64. It is adapted from [wernight's Alpine-based x86 image](https://github.com/wernight/docker-qbittorrent). Below is a slightly modified extract of the original project's readme.

About qbittorrent-nox
---------------------

qBittorrent NoX is the headless with remote web interface version of qBittorrent BitTorrent client.

This image is:

  * **Small**: Based on an Alpine Linux Docker image.
  * **Simple**: Exposes correct ports, configured for remote access.
  * **Secure**: Runs as non-root user with random UID/GID `520`.

Usage
-----

### Obtaining the image

You can download the image from the [Docker Hub](https://hub.docker.com/r/andreipoe/qbittorrent-aarch64/):

    $ docker pull andreipoe/qbittorrent-aarch64

Alternatively, you can clone the GitHub repository and build the image locally:

    $ git clone https://github.com/andreipoe/docker-qbittorrent-aarch64.git
    $ cd docker-qbittorrent-aarch64
    $ docker build -t qbittorrent-aarch64 . 

### Running the image

All mounts and ports are optional and qBittorrent will work even with only:

    $ docker run andreipoe/qbittorrent-aarch64

... however that way some ports used to connect to peers are not exposed, accessing the
web interface requires you to proxy port 8080, and all settings as well as downloads will
be lost if the container is removed.

So let's create some directories as user 520 (`qbittorrent`):

    $ mkdir config torrents downloads
    $ chown 520 config torrents downloads

... and start using this command:

	$ docker run -d \
		-p 8080:8080 -p 6881:6881/tcp -p 6881:6881/udp \
		-v $PWD/config:/config \
		-v $PWD/torrents:/torrents \
		-v $PWD/downloads:/downloads \
		andreipoe/qbittorrent-aarch64

... to have webUI running on [http://localhost:8080](http://localhost:8080) (username: `admin`, password: `adminadmin`) with config in the following locations mounted:

  * `/config`: qBittorrent configuration files
  * `/torrents`: Torrent files
  * `/downloads`: Download location

It is probably a good idea to add `--restart=unless-stopped` so the container restarts if it goes down.

You can change `6081` to some random  port number (also change in the settings). However, note that as of version 4.0, you _have to_ use the same Web UI port on the host and inside the container, otherwise you will only see a white page.

_Note: For the container to run, the legal notice had to be automatically accepted. By running the container, you are accepting its terms. Toggle the flag in `qBittorrent.conf` to display the notice again._

_Note: `520` was chosen randomly to prevent running as root or as another known user on your system; at least until [issue #11253](https://github.com/docker/docker/pull/11253) is fixed._
