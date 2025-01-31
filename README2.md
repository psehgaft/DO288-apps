# DO288 Containerized Example Applications

This repository contains a collection of sample containerized applications.  To complete the course you need to fork this repo into your personal Github account.

## No. 1

```sh
oc new-app --name [name] --build-env npm_config_registry=[nexus-url] nodejs:16-ubi8~[git-url]#[git-branch] --context-dir[git-context-dir]
oc logs -f bc/[build-config-name]
# Fix package.json (:)
oc start-buld -follow bc/[build-config-name]
```

## No2. 

```sh
podman run --name test -it rhscl/httpd-24-rhel7 bash

vi [dir].s2i/bin/assemble

 CUSTOMIZATION STARTS HERE 

echo "---> Installing application source"
cp -Rf /tmp/src/*.html ./

DATE=date "+%b %d, %Y @ %H:%M %p"

echo "---> Creating info page"
echo "Page built on $DATE" >> ./info.html
echo "Proudly served by Apache HTTP Server version $HTTPD_VERSION" >> ./info.html

 CUSTOMIZATION ENDS HERE 

oc new-app --name [name] httpd:2.4~[git-url]#[git-branch] --context-dir[git-context-dir]
```

## No3. 

```sh
oc describe bc/[build-config-name]
oc set build-hook bc/[build-config-name] --post-commit --command -- exec python smthg.py 
oc start-build bc/[build-config-name]
```


## No4. 

```docker
FROM registry.access.redhat.com/rhscl/nodejs-6-rhel7
EXPOSE 3000
# Make all Node.js apps use /opt/app-root as the main folder (APP_ROOT).
RUN yum --disablerepo=* --enablerepo="rhel-7-server-rpms" && \
    yum update && \
    yum install -y httpd && \
    yum clean all -y && \
    mkdir -p /opt/app-root/

WORKDIR /opt/app-root

# Copy the package.json to APP_ROOT
ONBUILD COPY package.json /opt/app-root

# Install the dependencies
ONBUILD RUN npm install

# Copy the app source code to APP_ROOT
ONBUILD COPY src /opt/app-root

# Start node server on port 3000
CMD [ "npm", "start" ]

RUN chgrp -R 0 directory && chmod -R g=u directory
```

Service Account

```sh
oc create serviceaccount myserviceaccount
oc patch dc/demo-app --patch '{"spec":{"template":{"spec":{"serviceAccountName": "myserviceaccount"}}}}'
oc adm policy add-scc-to-user anyuid -z myserviceaccount
```

## No5. 

```sh
podman build --layers=false -t do288-hello-java ./hello-java
```

```docker
FROM registry.access.redhat.com/ubi8/ubi:8.0 1

MAINTAINER Red Hat Training <training@redhat.com>

# DocumentRoot for Apache
ENV DOCROOT=/var/www/html 

RUN   yum install -y --no-docs --disableplugin=subscription-manager httpd && \ 
      yum clean all --disableplugin=subscription-manager -y && \
      echo "Hello from the httpd-parent container!" > ${DOCROOT}/index.html

EXPOSE 8080

LABEL io.openshift.expose-services="8080:http"

# Allows child images to inject their own content into DocumentRoot
ONBUILD COPY src/ ${DOCROOT}/

LABEL io.k8s.description="A basic Apache HTTP Server child image, uses ONBUILD" \
      io.k8s.display-name="Apache HTTP Server" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="apache, httpd"

RUN sed -i "s/Listen 80/Listen 8080/g" /etc/httpd/conf/httpd.conf \
RUN sed -i "s/#ServerName www.example.com:80/ServerName 0.0.0.0:8080/g" \
    /etc/httpd/conf/httpd.conf

# This stuff is needed to ensure a clean start
RUN rm -rf /run/httpd && mkdir /run/httpd

RUN chgrp -R 0 /var/log/httpd /var/run/httpd && \
    chmod -R g=u /var/log/httpd /var/run/httpd

# Run as the root user
USER 1001 

# Launch httpd
CMD /usr/sbin/httpd -DFOREGROUND


oc patch dc/demo-app --patch '{"spec":{"template":{"spec":{"serviceAccountName": "myserviceaccount"}}}}'
```

## No6. 

```sh
oc patch configmap/myconf --patch '{"data":{"key1":""}}'
oc patch secret/mysecret --patch '{"data":{"password":""}}'

oc set env deployment/mydcname --from configmap/myconf
oc set volume deployment/mydcname --add -t configmap -m /path/to/mount/volume --name myvol --configmap-name myconf

oc set env deployment/mydcname --from secret/mysecret
oc set volume deployment/mydcname --add -t secret -m /path/to/mount/volume --name myvol --secret-name mysecret
```

## No7. 

```sh

oc set probe deployment probes --liveness --get-url=http://:8080/healthz --initial-delay-seconds=2 --timeout-seconds=2
oc set probe deployment probes --readiness --get-url=http://:8080/ready --initial-delay-seconds=2 --timeout-seconds=2

```

## No8. 

```sh

helm create app1

vi values.yaml

mariadb:
  auth:
    username: quotes
    password: quotespwd
    database: quotesdb
  primary:
    podSecurityContext:
      enabled: false
    containerSecurityContext:
      enabled: false

vi templates/deployment.yml

imagePullPolicy: {{ .Values.image.pullPolicy }}
env:
  {{- range .Values.env }}
- name: {{ .name }}
  value: {{ .value }}
  {{- end }}

vi chart.yaml

dependencies:
- name: mariadb
  version: 11.0.13
  repository: https://charts.bitnami.com/bitnami

helm dependency update

```
## No9. 

```sh



```
