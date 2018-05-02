# Media Orchestra

A docker-compose file with

+ [madslundt/cloud-media-scripts](https://github.com/madslundt/docker-cloud-media-scripts) Rclone, PlexDrive, UnionFS
+ Plex
+ RuTorrent
+ Portainer
+ Sonarr
+ Radarr
+ Jackett

## Getting Started

### Prerequisites

What things you need to install the software and how to install them

fuser

```
$ sudo apt install fuser
```

docker

```
$ sudo apt update
$ sudo apt install \
      apt-transport-https \
      ca-certificates \
      curl \
      software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository \
     "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     stable"
$ sudo apt update
$ sudo apt install docker-ce
$ sudo usermod -aG docker $USER
```

docker-compose

```
$ sudo curl -L https://github.com/docker/compose/releases/download/1.21.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

# Setup
After the docker image has been setup and running, Rclone and Plexdrive need to be configured.

## Rclone
Setup Rclone run `docker exec -ti <DOCKER_CONTAINER> rclone_setup`

### With encryption
3 remotes are needed when using encryption:
1. First one is for the Google drive connection
2. Second one is for the Google drive on-the-fly encryption/decryption
3. Third and last one is for the local encryption/decryption

 - Endpoint to your cloud storage.
	- Create new remote [**Press N**]
	- Give it a name example gd
	- Choose Google Drive [**Press 8**]
	- If you have a client id paste it here or leave it blank
	- Choose headless machine [**Press N**]
	- Open the url in your browser and enter the verification code
 - Encryption and decryption for your cloud storage.
	- Create new remote [**Press N**]
	- Give it the same name as specified in the environment variable `RCLONE_CLOUD_ENDPOINT` but without colon (:) (*default gd-crypt*)
	- Choose Encrypt/Decrypt a remote [**Press 5**]
	- Enter the name of the endpoint created in cloud-storage appended with a colon (:) and the subfolder on your cloud. Example `gd:/Media` or just `gd:` if you have your files in root in the cloud.
	- Choose how to encrypt filenames. I prefer option 2 Encrypt the filenames
	- Choose to either generate your own or random password. I prefer to enter my own.
	- Choose to enter pass phrase for the salt or leave it blank. I prefer to enter my own.
 - Encryption and decryption for your local storage.
	- Create new remote [**Press N**]
	- Give it the same name as specified in the environment variable `RCLONE_LOCAL_ENDPOINT` but without colon (:) (*default local-crypt*)
	- Choose Encrypt/Decrypt a remote [**Press 5**]
	- Enter the encrypted folder: **/cloud-encrypt**. If you are using subdirectory append it to it. Example /cloud-encrypt/Media
	- Choose the same filename encrypted as you did with the cloud storage.
	- Enter the same password as you did with the cloud storage.
	- Enter the same pass phrase as you did with the cloud storage.


### Without encryption
1 remote is needed to connect rclone to Google drive:
 - Endpoint to your cloud storage.
	- Create new remote [**Press N**]
	- Give it the same name as specified in the environment variable `RCLONE_CLOUD_ENDPOINT` but without the colon (:)
	- Choose Google Drive [**Press 7**]
	- If you have a client id paste it here or leave it blank
	- Choose headless machine [**Press N**]
	- Open the url in your browser and enter the verification code

Rclone documentation if needed [click here](https://rclone.org/docs/)

## Plexdrive
Setup Plexdrive to the cloud. Run the command `docker exec -ti <DOCKER_CONTAINER> plexdrive_setup`

Plexdrive documentation if needed [click here](https://github.com/dweidenfeld/plexdrive/tree/4.0.0)

# Commands
Upload local files to cloud run: `docker exec <DOCKER_CONTAINER> cloudupload`

Remove local files run `docker exec <DOCKER_CONTAINER> rmlocal`

Check if everything is running `docker exec <DOCKER_CONTAINER> check`

Empty trash on Plex Media Server but only if mount is up `docker exec <DOCKER_CONTAINER> emptytrash`

`cloudupload` and `rmlocal` can be ran with arguments. All arguments are passed to rclone.
For example it is possible to run `docker exec <DOCKER_CONTAINER> cloudupload -v` to get verbose on the rclone operations in cloudupload.

# Cron jobs
Setup cron jobs to upload and remove local files:
 - `@daily docker exec <DOCKER_CONTAINER> cloudupload`
 - `@weekly docker exec <DOCKER_CONTAINER> rmlocal`
 
 
# Documentation & Credits
[madslundt/cloud-media-scripts](https://github.com/madslundt/docker-cloud-media-scripts)