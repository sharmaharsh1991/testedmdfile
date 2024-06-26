## Setup a development environment  from scratch

1. Take clone from git by using the below command.

   1. `git clone https://nylemalik@github.com/nylemalik/prosal.git`
   2. `cd prosal`
   3. `git branch`
   4. `git checkout develop`
   5. `git pull origin develop`

2. Install docker and docker compose
3. From the root of the project
   ` Run : docker-compose up -d --build`
4. Add this file under this path : nginx\conf\app.conf  
   Add this data in this file :
   ```sh
   server {
   listen 80;
   index index.php index.html;
   error_log /var/log/nginx/error.log;
   access_log /var/log/nginx/access.log;
   client_max_body_size 100M;
   root /var/www/public;
   location ~ \.php$ {
   try_files $uri =404;
   fastcgi_split_path_info ^(.+\.php)(/.+)$;
   fastcgi_pass app:9000;
   fastcgi_index index.php;
   include fastcgi_params;
   fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
   fastcgi_param PATH_INFO $fastcgi_path_info;
   }
   location / {
   try_files $uri $uri/ /index.php?$query_string;
   gzip_static on;
   }
   }
   ```
5. `sudo docker exec nginx nginx -s reload`
6. Check docker container
   `docker-compose ps `
7. Create .env file in root directory and make changes according to credential of mongo db atlas database.
8. Run command inside the docker container
   1. `docker exec -it app /bin/bash (app is container, You need to run this command then run all command that is below)`
   2. `composer install --ignore-platform-reqs`
   3. `composer update`
   4. `npm install`
   5. `php artisan optimize`
   6. `npm run dev`

## Backup From Atlas Live DB  

1. docker exec -it mongodb /bin/bash (this command take you inside the continer then you run the below command)
   ```sh
   mongodump --uri="mongodb+srv://URL" --out "/var/www/html/path_to_store_mongodb" (URL is from mongo db atlas from where you want to export )
   docker cp mongodb:/var/www/html/path_to_store_mongodb/prosal  path_where_you_want_to_store_db_from_docker
   ```
2. First create databse in MongoDB Atlas then  Import MONGODB databse to atlas DB

```sh
   mongorestore --uri mongoatlas_uri/prosal  path_from_where_you_import_db
```

If all is fine then your site is working fine. Then you can open your site on localhost : [http://localhost:8100](http://localhost:8100)

## Steps to deploy changes to production:

Let suppose you have made change in the file and you have checked all is fine and you are working in the deveop branch directly.
Then you need follow the steps to deploy your changes :

1.  `git add 'you need put file name'`
2.  `git commit -m 'Enter commit message'`
3.  `git push origin develop`

Connect to ssh and then run below command

1. `sudo su`
2. `cd /var/html/prosal`
3. `git pull origin develop (if issue in the pull then run these 2 command)`<br>
   i)`git stash`<br>
   ii)`git pull origin develop`
4. `docker exec -it app /bin/bash`
5. `rm -rf node_modules`
6. `rm -rf package-lock.json`
7. `cd public/js`
8. `rm -rf index.js`
9. `cd ..`
10. `npm install`
11. `php artisan optimize`
12. `npm run dev`

##### After installing any package if site is not working, then run following command :<bR>

1.  `composer update`

These are the steps to deploy changes to production.
