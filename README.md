## Commands
  * build `docker-compose build`
  * run `docker-compose up ` or `docker-compose up -d` 
  * view running containers `docker ps`

## Docker for an Existing Rails Application

### Step 1: dockerize your Rails app
  First we need to tell Docker how to build the image we want to run our Rails app.  To do that create a `Dockerfile` at the root of your Rails application.
  Refer the [Dockerfile]

### Step 2: configure the application server
  Last line of our Dockerfile references a script that does not yet exist. We need to create it, so go ahead and create a directory under config/ named “containers” and paste the following into a script named “app_cmd.sh”:
   Refer [ unicorn trigger ]
   
  The script contains the command we want Docker to run when it initializes a container from our image. It will start the Unicorn server that will process our source code. We put the command in a script because we want the $RAILS_ENV environment variable honored at runtime  (i.e. when the container starts). This will not work if we put the command directly in our Dockerfile. That’s because Docker strips out environment variables from the build host in order to keep builds consistent across all the different platforms it supports.
   
  Our Unicorn server is configured with the -c option, which references a file we have to create. Open a file named config/containers/unicorn.rb.
   Refer [unicorn server code]
     
### Step 3: introduce Docker Compose
   Since our application will be running across multiple containers it would be nice to control them all as one. That is what Docker Compose does for us. To get our app started with Docker Compose create a file docker-compose.yml([Refer]) in the root of your Rails app.
  In order to build from this config you’ll need to define `RAILS_ENV` on whatever host you plan to build on. Without it Compose will default the value to a blank string and give you a warning. You’ll also need to create a .env file at the root of your Rails app. In .env you can define environment variables and use them to configure your application
### Step 4: containerize your database
  Under the links: section of our docker-compose.yml we reference a container named “db”. This will be the container we use to run our Postgres database. Creating that container is dead simple.
     ```
      # service configuration for our web server
      web:
      
        # set the build context to the root of the Rails app
        build: .
      
        # build with a different Dockerfile
        dockerfile: config/containers/Dockerfile-nginx
        
        # makes the web container aware of the app container
        links:
          - app
        
        # expose the port we configured Nginx to bind to
        ports:
          - "80:80"
     ```
###References
   * http://chrisstump.online/2016/02/20/docker-existing-rails-application/
   * https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-14-04
   * https://docs.docker.com/compose/rails/

