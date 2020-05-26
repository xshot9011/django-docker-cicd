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

## step 2: preparing to deploy

sign in to you aws account 
searching service name "elastic beanstalk"
this is use to run and manage web apps, appropriate when you are running only 1 container at the time

"elastic beanstalk" >> create application >> fill the application name 
application is like a workspace outsource, to actually have some target to deploy to we have to create environment

go to that apllication and create environment >> web server environment >>  >> platform: docker >> apllication code: sample application >> create env... and keep waiting

ืnow: we have env containe load balancer and our container, in our rnv there is url, being to replace by out app

## step 3: deploying

```txt
sudo: required
services: 
    - docker
before_install:
    - docker build -t bigdev2000/django-docker-cicd -f Dockerfile.dev .
script:
    - docker run bigdev2000/django-docker-cicd python manage.py test
# tell travis how to deploy our code
deploy:
    # deploy to the host server
    provider: elasticbeanstalk
    # where we are create elasticbeanstalk look in env's urls, there is region 
    region: "us-east-2"
    # name of application that we used to create above
    # the fiest word before env's name is your app's name on aws
    app: "app-django"
    # env's name, the actuall app is running inside of env not an app
    env: "AppDjango-env"
    # travsi will copy all your file in git repo to "s3 bucket", hardrive running on aws
    # then travis will tell elasticbeanstalk, "hey i just upload new file, use that to redeploy"
    # to get a name follow >>>> find bucket name <<<<
    bucket_name: "elasticbeanstalk-us-east-2-956710473097"
    # where is the path inside that bucket, use the same name as your application
    bucket_path: "app-django"
    # select where the branch to deploy 
    on: 
        branch: master
    # api key that travis use to access aws account
    # >>>> find api key <<<<
    # $ sign mean I have a key for you but you have to go to local environment in travis-ci
    access_key_id: $AWS_ACCESS_KEY
    secret_access_key: $AWS_SECRET_KEY
```

>>> find bucket name <<<<
s3 bucket automatically generate for you when you create elasticbeanstalk application
all we has to, just find the s3 name
search "s3" service >> find something name "elasticbeanstalk-region-" 

>>> find api key <<<<
go to "IAM" service, use to manage access to aws resource

on left hand side >> Users >> add user >> fill username >> click programmatic access and aws management console access >> next

permission if you dont have your own permission find an exist in stead >> fileter with "AWSElasticBeanstalkFullAccess" >> check that >> next >> next >> create user

after creating user, secret access key will be given and show only one time... so note it somewhere ..

we will not put that key directly on github or travis.yml file

we will use environment secret provide by travis ci >> go to our app in travis ci >> setting >> Environment Variables >> add key: value
note: make sure you turn off display value in build log
first key: AWS_ACCESS_KEY: [access key id]
next key: AWS_SECRET_KEY: [Secret access key]

now our key is encrypted in travis-ci