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
vi [dir].s2i/bin/assemble
-- CUSTOMIZATONS -- 
cp -Rf [old-path]/*.html ./
DATE= `date "+%b %d, %Y"`
echo "[text] $DATE" >> ./info.html
oc new-app --name [name] httpd:2.4~[git-url]#[git-branch] --context-dir[git-context-dir]
```

## No3. 

```sh
oc describe bc/[build-config-name]
oc set build-hook bc/[build-config-name] --post-commit --command -- exec python smthg.py 
oc start-build bc/[build-config-name]
```


## No3. 

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
