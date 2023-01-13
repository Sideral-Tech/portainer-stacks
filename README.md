# Sideral Technologies - Custom Portainer Stacks

Sideral Technologies has developed custom Portainer stacks, which are essentially Docker compose files with additional metadata for easy deployment to a Portainer instance.

## How to Use

To use these stacks, a Portainer instance must first be running. Instructions for deploying a Portainer instance can be found [here](https://portainer.readthedocs.io/en/stable/deployment.html). Once Portainer is running, navigate to the "Network" tab and create a new network called `infra`. This is where all containers will be deployed. The network can be named as desired, but the `networks` section of the compose files must be updated to match.

To deploy the stacks:
1. Select the desired stack
2. Access the Portainer instance
3. Navigate to "App Templates" in the sidebar
4. Select "Custom Templates"
5. Click "Add Custom Template"
6. Input the contents of the `docker-compose.yml` file through copy and paste, file upload or 
7. Click "Repository"
8. Enter the URL of the repository and the path to the `docker-compose.yml` file in the "Compose path" field, e.g. `booksonic-air/docker-compose.yml`
9. The stack will now be visible in the Portainer instance and can be deployed by clicking on the stack and selecting "Deploy the stack".

Sideral Technologies recommends using the "Repository" method for ease of future updates.

For more information on Portainer, refer to their [documentation](https://portainer.readthedocs.io/en/stable/).

Please note that as the `infra` network is used, care must be taken to ensure that the deployed containers do not use the same ports. If multiple instances of the same stack are being deployed, the ports in the compose file must be adjusted accordingly. Additionally, all timezones are set to `America/Bahia` by default. If a different timezone is desired, the compose file must be edited accordingly.

The compose files can be edited using Portainer's stack editor. Additionally, environment variables can be added to stacks that support and/or require them. Some stacks also have configuration files located in their respective folders, which can be edited to make changes to the stacks.

### Accessing the apps

To access and set up the apps, refer to the documentation for each app. As none of the stacks publish any ports, a reverse proxy must be used for access. Sideral Technologies recommends using [caddy](https://caddyserver.com/) for its ease of set up and configuration, as well as automatic HTTPS. Instructions for set up can be found [here](https://caddyserver.com/docs/install). Alternatives such as [Traefik](https://traefik.io/) and [nginx](https://nginx.org/en/) can also be used at the user's discretion. An example Caddyfile is shown below:

```Caddyfile
booksonic.example.com {
    encode zstd gzip
    reverse_proxy localhost:4040
}
```

With this configuration, Booksonic Air can be accessed at booksonic.example.com.

### Directory structure

The directory structure for the volumes used by the stacks is shown below. These paths can be adjusted to the user's preference, but the volumes section of the compose files must be updated accordingly.

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

Please note that some stacks use the same volumes/folders as others. This is because they share the same data or because they use the same database. For example, `qbittorrent` is our torrent client and `radarr` is our movie library organizer. Radarr not only uses qBittorrent as a download client, but is also able to automatically sync the downloaded files to Jellyfin. The same applies for most of the other `*arr` apps. Therefore, we recommend that you do not change the paths unless you are certain of what you are doing.

In the future, we will provide a script that automatically creates the directory structure for you. Until then, you will have to do it manually.

### Updating a stack

1. Go to your Portainer instance
2. Click on "Stacks" in the sidebar
3. Click on the stack you want to update
4. Click on "Editor"
5. Click on "Update the stack"
6. Check "Pull latest image version"
7. Click "Update"
8. Wait for the stack to update

### Uninstalling a stack

1. Go to your Portainer instance
2. Click on "Stacks" in the sidebar
3. Click on the stack you want to uninstall
4. Click on "Delete this stack"
5. Wait for the stack to be deleted
6. Click on "Images" in the sidebar
7. Click on the image you want to remove
8. Click on "Delete this image"
9. Wait for the image to be deleted

### Backups

At Sideral Technologies, we utilize btrfs for both our underlying filesystem and our Docker storage driver to ensure the safety and security of our data. This allows us to easily generate backups of our volumes through the use of `btrfs subvolume snapshot` and seamlessly dispatch them to remote locations through the use of `btrfs send` and `btrfs receive`. Additionally, we also employ the use of `rsync` to copy our volumes or folders to remote locations as an added measure of protection.

Another option for volume backups is the use of `docker-volume-backup`. Instructions for implementation can be found [here](https://github.com/jareware/docker-volume-backup). Restoring backups is also possible through the use of this tool, although the process may not be as streamlined as it is with `btrfs`.

## How to add new stacks

1. Fork this repository
2. Create a new folder for your stack
3. Add a `docker-compose.yml` file
4. Submit a pull request

## Available stacks

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

## Note

Let it be known that any and all scripts, tools, and solutions developed by Sideral Technologies are for the exclusive use of our organization. Any unauthorized distribution or sharing of these resources will be met with severe consequences. Any modifications made to these solutions must be approved and tested by Sideral Technologies prior to implementation in any production setting. Any deviation from these guidelines will not be tolerated.

## Disclaimer

Sideral Technologies will not be held responsible for any unauthorized or illegal use of this script. Use of the stacks in this repository is at your own risk and any consequences resulting from such use will be borne solely by the user.
