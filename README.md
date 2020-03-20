MultiDocker application:

This uses docker-compose locally for development and is an example of creating an app that has multiple docker containers that communicate with each other. I'm not totally sure what the function of the client container is in production, to serve up the bundled js code for the app? There's a node server and worker as well, and we use postgres and redis to store stuff. Also uses create-react-app under the hood.

Travis for deploying, elastic beanstalk + RDS + elasticache, travis builds and pushes docker images to docker hub, and a zipped built app into s3 for eb to pick up. I imagine ECR could be used here instead.

Here's some notes that might be helpful later:

after adding sec group to each service,
gather env var names from docker-compose file (from api), and add them all to elastic beanstalk, these are shared among all containers in the app
create iam user for travis to use to push
take those creds and store them as env vars inside travis site
then we adjust the .travis.yml file to include a deploy step, giving it an s3 bucket to dump the built app (with the Dockerrun file) into for eb to pick up. we're dumping the whole app, but it really only needs this file, as the 4 images are built by travis and pushed separately up to docker hub. eb just has to read the Dockerrun file, then go fetch them.
Unclear if i need to manually make a bucket name in this folder.
I'm going to make it anyways, just in case.

Commit and push, github gets the changed master branch, travis listening, travis uses the yml file to do its thing

somehow eb knows to look in s3 for a changed file, and apply it to this app..not sure how. it's pissed tho, because we didn't specify how much memory we want allocated to each container in the Dockerrun file
added that in, and now it Just Works


unwind:

delete the 3 aws apps, then vpc -> security groups, iam user
