Docker production environment for running LWA Redmine.

## Initial Deployment

1. Pull a `busybox` image to get a very lightweight container to play with:

        $ docker pull busybox

2. Create a data volume container that will hold all MySQL data:

        $ docker create --name redminedbdata busybox

   Never delete this container or **you will loose** all MySQL data! Also ensure a
   backup procedure is active and well tested.
   
   See http://blog.arungupta.me/docker-mysql-persistence/

3. Grab an official MySQL image

        $ docker pull mysql
        $ docker run -d --volumes-from redminedbdata \
            -e MYSQL_ROOT_PASSWORD=<root_psswd> -e MYSQL_DATABASE=redmine -e MYSQL_USER=redmine \
            -e MYSQL_PASSWORD=<redmine_psswd> --name tmpdb mysql

4. Create user and database by connecting to the mysql container:

        $ docker exec -ti db /bin/bash
        $ mysql
        $ mysql> CREATE USER 'lwa'@'%' IDENTIFIED BY '=<redmine_psswd>';
        $ mysql> CREATE DATABASE 'redmine';
        $ mysql> GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'%'

5. The data is stored in the `redminedbdata` container, it's safe to stop and delete the
   running MySQL container:

        $ docker stop tmpdb
        $ docker rm tmpdb

6. Use docker-compose to run production setup