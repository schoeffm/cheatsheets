
# rename a docker-image (when you've missed to set a tag on creation)
docker tag 1cf76 myUserName/imageName:0.1.0

# build an image from any file (not necessarily a Dockerfile)
# Notice: ADD's in such pseudo-Dockerfiles wont work
docker build -t schoeffm/new_name:latest - < somefile


[several_docker_files]:http://blog.bacongobbler.com/2013/07/26/dockerfile-workflow/
