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
