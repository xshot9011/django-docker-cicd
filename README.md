# workflow

## step 1: preparation for deploying

we want to create .travis.yml file, telling travis how to run docker how to run an test
now create ".travis.yml" file

4 things we need to do
>>1.tell travis we need a copy of docker running 
>>2.build our image using Dockerfile
>>3.tell travis how to run test suite
>>4.tell travis how to deploy our code to aws

```txt
# .travis.yml
# sudo permission is required
sudo: required
# tell travis that we need docker cli pre install >> travis will install it
services: 
    - docker
# give the series of command before our test are run
# this will build the docker image
before_install:
    - docker build -t bigdev2000/django-docker-cicd -f Dockerfile.dev .
# command that need to be execute to run our test
# if exited status is not 0, travis will think it is error
# becarefull when running test, some test are continous and exist process, you need to make sure command test run only onece
script:
    - docker run bigdev2000/django-docker-cicd python manage.py test
```

activate the repo in travis then push it to master branch

## step 2: deploy

sign in to you aws account 
searching service name "elastic beanstalk"
this is use to run and manage web apps, appropriate when you are running only 1 container at the time

"elastic beanstalk" >> create application >> fill the application name 
application is like a workspace outsource, to actually have some target to deploy to we have to create environment

go to that apllication and create environment >> web server environment >>  >> platform: docker >> apllication code: sample application >> create env... and keep waiting

ืnow: we have env containe load balancer and our container, in our rnv there is url, being to replace by out app