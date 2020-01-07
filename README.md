# devops-on-k8s
在K8S中部署DevOps工具链




## 直接使用的Docker运行
```
#!/bin/bash

LOCALDIR=/data/devops

gitlab(){
  docker run  --rm -d \
    --publish 443:443 --publish 8088:8088 --publish 2222:22 \
    --name gitlab \
    --volume ${LOCALDIR}/gitlab/config:/etc/gitlab \
    --volume ${LOCALDIR}/gitlab/logs:/var/log/gitlab \
    --volume ${LOCALDIR}/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:12.6.0-ce.0
}

nexus(){
  docker run -id \
    --privileged=true --name=nexus3 \
    -p 8081:8081 \
    -v ${LOCALDIR}/nexus3/nexus-data:/nexus-data \
    sonatype/nexus3:3.20.1
}



sonar(){
  docker run --rm -d --name sonarqube \
    -p 9000:9000 \
    -v ${LOCALDIR}/sonar/sonarqube_conf:/opt/sonarqube/conf \
    -v ${LOCALDIR}/sonar/sonarqube_extensions:/opt/sonarqube/extensions \
    -v ${LOCALDIR}/sonar/sonarqube_logs:/opt/sonarqube/logs \
    -v ${LOCALDIR}/sonar/sonarqube_data:/opt/sonarqube/data \
    sonarqube:7.9.2-community

}

ldap(){
    docker run -p 389:389 --name myopenldap  \
	--env LDAP_ORGANISATION="devops" --env LDAP_DOMAIN="devops.com" --env LDAP_ADMIN_PASSWORD="ldap123" --detach osixia/openldap
}


ldapadmin(){
    docker run -d --privileged -p 10004:80 --name myphpldapadmin \
         --env PHPLDAPADMIN_HTTPS=false --env PHPLDAPADMIN_LDAP_HOSTS=192.168.1.200 --detach osixia/phpldapadmin

}
case $1 in
    "gitlab")
	    gitlab
	    ;;
    "sonar")
	    sonar
	    ;;
    "ldap")
	    ldap
	    ;;
    "ldapadmin")
	    ldapadmin
	    ;;
    "nexus")
	    nexus
	    ;;
    "*")
	    ;;
esac


```


## K8S中迁移
