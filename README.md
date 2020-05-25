# workflow

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

activate the repo in travis and push it to master branch