# NGINX PCF Demo 


## Set Up

### Requirements
```
Install PCF Dev: https://docs.pivotal.io/pcf-dev/

cf dev start -f ./pcfdev-v1.2.0-darwin.tgz 

cf login -a https://api.dev.cfdev.sh --skip-ssl-validation

#admin/admin

#cfdev-org

cf domains
#Getting domains in org cfdev-org as admin...
#name               status   type   details
#dev.cfdev.sh       shared  

cf create-shared-domain tcp.dev.cfdev.sh --router-group default-tcp
#name               status   type   details
#dev.cfdev.sh       shared          
#tcp.dev.cfdev.sh   shared   tcp  

```

### Building and Deploying locally into PCF
```
./build_local.sh

# gorouter (HTTP)
cf push nextgen-api-gateway -f ./target/nextgen-api-gateway/manifest.yml

# cf router (TCP) should be default deployment for 10X capacity and variable performance increase
cf push nextgen-api-gateway -f ./target/nextgen-api-gateway/manifest.yml -d tcp.dev.cfdev.sh --random-route

cf routes
#Getting routes for org cfdev-org / space cfdev-space as admin ...
#space         host   domain             port   path   type   apps                  service
#cfdev-space          tcp.dev.cfdev.sh   1042          tcp    nextgen-api-gateway

# http://tcp.dev.cfdev.sh:1042
```


### Building and Deploying with Jenkins into PCF 
```

docker build --tag pcf-jenkins .

docker volume create jenkins-volume

# docker run -p 8090:8080 -p 50000:50000 -v jenkins-volume:/var/jenkins_home -v DIRECTORY-OF-N+-BINARY:/var/jenkins_home/nginx pcf-jenkins

docker run -p 8080:8080 -p 50000:50000 -v jenkins-volume:/var/jenkins_home -v /Users/frank.zhu/nginx/officialDemos/nginxPlusKey:/var/jenkins_home/nginx pcf-jenkins

# Go to Jenkins
# http://localhost:8080

# Using Blue Ocean to create the pipeline using existing Jenkinsfile

# Change the variables in Jenkins step: "log into PCF".

# Manual start build pipeline

```

### Useful PCF commands:
```
cf app nextgen-api-gateway

cf scale nextgen-api-gateway -i 3

cf delete nextgen-api-gateway -f

cf domains
#Getting domains in org cfdev-org as admin...
#name               status   type   details
#dev.cfdev.sh       shared          
#tcp.dev.cfdev.sh   shared   tcp 

cf router-groups

cf logs nextgen-api-gateway --recent

cf ssh nextgen-api-gateway

```


