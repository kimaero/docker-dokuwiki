kimaero/dokuwiki
==================

[DokuWiki](https://www.dokuwiki.org/dokuwiki) docker container with preinstalled 
NGINX and Supervisord.

Configuration is similar to [istepanov/dokuwiki](https://hub.docker.com/r/istepanov/dokuwiki/), 
but with bootstrap3 template and a number of useful plugins preinstalled.

### How to run

First, run new dokuwiki container:

    docker run -d -p 8000:80 --name dokuwiki kimaero/dokuwiki

Then setup dokuwiki using installer at URL `http://localhost:8000/install.php`

### How to make data persistent

To make sure data won't be deleted if container is removed, create an empty 
container named `dokuwiki-data` and attach DokuWiki container's volumes to it. 
Volumes won't be deleted if at least one container owns them.

    # create data container
    docker run --volumes-from dokuwiki --name dokuwiki-data busybox

    # now you can safely delete dokuwiki container
    docker stop dokuwiki && docker rm dokuwiki

    # to restore dokuwiki, create new dokuwiki container and attach dokuwiki-data volume to it
    docker run -d -p 8000:80 --volumes-from dokuwiki-data --name dokuwiki kimaero/dokuwiki

### How to backup data

    # create dokuwiki-backup.tar.gz archive in current directory using temporaty container
    docker run --rm --volumes-from dokuwiki -v $(pwd):/backup ubuntu tar zcvf /backup/dokuwiki-backup.tar.gz /var/dokuwiki-storage

### How to restore from backup

    #create new dokuwiki container, but don't start it yet
    docker create -p 8000:80 --name dokuwiki istepanov/dokuwiki:2.0

    # create data container for persistency (optional)
    docker run --volumes-from dokuwiki --name dokuwiki-data busybox

    # restore from backup using temporary container
    docker run --rm --volumes-from dokuwiki -w / -v $(pwd):/backup ubuntu tar xzvf /backup/dokuwiki-backup.tar.gz

    # start dokuwiki
    docker start dokuwiki
