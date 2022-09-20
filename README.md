# Custom Portainer Stacks

Custom Portainer stacks are basically just Docker compose files with a few extra bits of metadata added to them. This allows you to easily deploy them to your Portainer instance.

## How to use

Before you can use these stacks, you need to have a Portainer instance running. You can find instructions on how to do that [here](https://portainer.readthedocs.io/en/stable/deployment.html). Once you have Portainer running, go to the "Network" tab and create a new network called `infra`. This is where all of the containers will be deployed to. You can name it whatever you want, but you'll need to change the `networks` section of the compose files to match.

After you have you can deploy the stacks:

1. Pick a stack you want to deploy
2. Go to your Portainer instance
3. Click on "App Templates" in the sidebar
4. Click on "Custom Templates"
5. Click on "Add Custom Template"
6. Either copy and paste the contents of the `docker-compose.yml` file, upload the file or
7. Click "Repository"
8. Enter the URL of the repository and the path to the `docker-compose.yml` file in the "Compose path" field, e.g.: `booksonic-air/docker-compose.yml`
9. You'll now see the stack in your Portainer instance. Click on it and click "Deploy the stack".

We recommend you use the "Repository" method, as it will make it easier to update the stacks in the future.

Those are the basic steps. If you want to learn more about Portainer, check out their [documentation](https://portainer.readthedocs.io/en/stable/).

P.S.: as we're using the `infra` network, you'll need to make sure that the containers you're deploying don't use the same ports. If you're deploying multiple instances of the same stack, you'll need to change the ports in the compose file. Also, all timezones are set to `America/Bahia` by default. If you want to change that, you'll need to edit the compose file.

Talking about editing compose files, you can use Portainer's stack editor to make changes to the compose files. You can also use the editor to add environment variables to the stacks that support and/or require them. Some stacks also have configuration files in their folders. You can edit those files to make changes to the stacks.

### Accessing the apps

Please check the documentation for each app to find out how to access and set it up. None of the stacks publish any ports, so you'll need to use a reverse proxy to access them. We recommend [caddy](https://caddyserver.com/) as it's easy to set up and configure and offers automatic HTTPS. You can find instructions on how to set it up [here](https://caddyserver.com/docs/install). You can also use [Traefik](https://traefik.io/) or [nginx](https://nginx.org/en/) or whatever you feel like (there are some haproxy nerds out there somewhere). Below is an example Caddyfile:

```Caddyfile
booksonic.example.com {
    encode zstd gzip
    reverse_proxy localhost:4040
}
```

Yeah, that's it. You can now access Booksonic Air at `booksonic.example.com`.

### Directory structure

Below is a directory structure for the volumes used by the stacks. You can change the paths to whatever you want, but you'll need to change the `volumes` section of the compose files to match.

```text
data/
├─ bazarr/
│  ├─ config/
├─ booksonic/
│  ├─ audiobooks/
│  ├─ config/
│  ├─ othermedia/
│  ├─ podcasts/
├─ foundry/
│  ├─ cache/
│  ├─ data/
│  ├─ foundryvtt/
├─ ghost/
│  ├─ default/
├─ jellyfin/
│  ├─ anime/
│  ├─ config/
│  ├─ movies/
│  ├─ music/
│  ├─ tv/
├─ jmusicbot/
│  ├─ playlists/
│  ├─ config.txt
│  ├─ serversettings.json
├─ lazylibrarian/
│  ├─ books/
│  ├─ config/
├─ lidarr/
│  ├─ config/
├─ mariadb/
│  ├─ default/
│  ├─ my.cnf
├─ ombi/
│  ├─ config/
├─ prowlarr/
│  ├─ config/
├─ qbittorrent/
│  ├─ config/
│  ├─ downloads/
├─ radarr/
│  ├─ config/
├─ readarr/
│  ├─ config/
├─ sonarr/
│  ├─ config/
├─ wordpress/
│  ├─ default/
```

Please note that some stacks use the same volumes/folders as others. That's either because they share the same data or because they use the same database. For example, `qbittorrent` is our torrent client and `radarr` is our movie library organizer. Radarr not only uses qBittorrent as a download client, but is also able to automatically sync the downloaded files to Jellyfin. The same goes for most of the other `*arr` apps. So we recommend that if you don't exactly know what you're doing, you don't change the paths.

I'll add a script that automatically creates the directory structure for you in the future. Until then, you'll have to do it manually. Good luck!

### Updating

To update a stack you can do the following:

1. Go to your Portainer instance
2. Click on "Stacks" in the sidebar
3. Click on the stack you want to update
4. Click on "Editor"
5. Click on "Update the stack"
6. Check "Pull latest image version"
7. Click "Update"
8. Wait for the stack to update

### Uninstalling

To uninstall a stack you can do the following:

1. Go to your Portainer instance
2. Click on "Stacks" in the sidebar
3. Click on the stack you want to uninstall
4. Click on "Delete this stack"
5. Wait for the stack to be deleted

That process will leave ununsed images behind. To remove them you can do the following:

1. Go to your Portainer instance
2. Click on "Images" in the sidebar
3. Click on the image you want to remove
4. Click on "Delete this image"
5. Wait for the image to be deleted

### Backups

In production we use btrfs for both our underlying filesystem and our Docker storage driver. That allow us to generate backups of the volumes using `btrfs subvolume snapshot` and dispatch it to remote locations using `btrfs send` and `btrfs receive`. Another option is to use `rsync` to copy the volumes or folders to a remote location.

Even another option is to use the `docker-volume-backup` to backup the volumes. You can find instructions on how to do that [here](https://github.com/jareware/docker-volume-backup). Restoring backups is also possible using the same tool although it's not as easy as it is with `btrfs` (all options are boring and tedious as per usual with backups).

## How to add your own

1. Fork this repository
2. Create a new folder for your stack
3. Add a `docker-compose.yml` file
4. Submit a pull request

## Available stacks

I just ran `tree -L 1 --sort name ./portainer-stacks` and removed the files in the root directory because I'm lazy:

```text
portainer-stacks/
├── bazarr
├── booksonic-air
├── foundry
├── ghost
├── glitchip
├── jellyfin
├── jmusicbot
├── lazylibrarian
├── lidarr
├── ombi
├── prowlarr
├── qbittorrent
├── radarr
├── readarr
├── revanced-releases-api
├── sonarr
└── wordpress
```
