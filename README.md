#Web
#pull Docker Images

[ec2-user@ip-172-31-1-66 ~]$ docker pull httpd

ec2-user@ip-172-31-1-66 ~]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
httpd        latest    10fd72f437c4   2 months ago   148MB

[ec2-user@ip-172-31-1-66 ~]$ 
[ec2-user@ip-172-31-1-66 ~]$ docker run -d httpd
1a60467050244038e180d5d67fd99d3a9de36b9cd9d942322642fbd148d02dc9
[ec2-user@ip-172-31-1-66 ~]$ 

[ec2-user@ip-172-31-1-66 ~]$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS                      PORTS     NAMES
1a6046705024   httpd          "httpd-foreground"   2 minutes ago    Up 2 minutes                80/tcp    serene_bell
[ec2-user@ip-172-31-1-66 ~]$ 

[ec2-user@ip-172-31-1-66 ~]$ docker exec -it 1a6046705024 /bin/bash
root@1a6046705024:/usr/local/apache2# 
root@1a6046705024:/usr/local/apache2# cd htdocs/
root@1a6046705024:/usr/local/apache2/htdocs# ls
index.html
root@1a6046705024:/usr/local/apache2/htdocs# cat index.html 
<html><body><h1>It works!</h1></body></html>
root@1a6046705024:/usr/local/apache2/htdocs# echo '<html><body><h1>It should work!</h1></body></html>' >index.html 
root@1a6046705024:/usr/local/apache2/htdocs# cat index.html 
<html><body><h1>It should work!</h1></body></html>
root@1a6046705024:/usr/local/apache2/htdocs# exit

[ec2-user@ip-172-31-1-66 ~]$ sudo docker commit 1a6046705024 devpipeline/httpd:test
sha256:8be986c76804ef2ce9f17c3add96a420b7706a504d6cde795633629c349ef14e

[ec2-user@ip-172-31-1-66 ~]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
devpipeline/httpd        test    8be986c76804   7 seconds ago   148MB
httpd        latest    10fd72f437c4   2 months ago    148MB

[ec2-user@ip-172-31-1-66 ~]$ docker run -d --name apps -p 8080:80 devpipeline/httpd:test
f899a692a5020eb861f0fee8fed1f471bff9b3e5a66b3575289571962690ef30
[ec2-user@ip-172-31-1-66 ~]$ 

[ec2-user@ip-172-31-1-66 ~]$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS                      PORTS                                   NAMES
f899a692a502   devpipeline/httpd:test   "httpd-foreground"   8 seconds ago    Up 8 seconds                0.0.0.0:8080->80/tcp, :::8080->80/tcp   apps
1a6046705024   httpd          "httpd-foreground"   9 minutes ago    Up 9 minutes                80/tcp                                  serene_bell

[ec2-user@ip-172-31-1-66 ~]$ curl localhost:8080
<html><body><h1>It should work!</h1></body></html>
[ec2-user@ip-172-31-1-66 ~]$ 

[ec2-user@ip-172-31-1-66 ~]$ docker login
Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

Username: devpipeline
Password: 
WARNING! Your password will be stored unencrypted in /home/ec2-user/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded


[ec2-user@ip-172-31-1-66 ~]$ sudo docker commit 1a6046705024 devpipeline/httpd:v1
sha256:d28580b58d0a90fe4c3ba9930f0efb23106b1f9c97ab1c411bc8661ebcc05919
[ec2-user@ip-172-31-1-66 ~]$ 

[ec2-user@ip-172-31-1-66 ~]$ docker images
REPOSITORY          TAG       IMAGE ID       CREATED          SIZE
devpipeline/httpd   v1        d28580b58d0a   5 seconds ago    148MB

[ec2-user@ip-172-31-1-66 ~]$ 
[ec2-user@ip-172-31-1-66 ~]$ docker push devpipeline/httpd:v1
The push refers to repository [docker.io/devpipeline/httpd]
5d5b7a4b2112: Layer already exists 
8e92d33aa718: Layer already exists 
b8d276594499: Layer already exists 
417809be0bd7: Layer already exists 
5f70bf18a086: Layer already exists 
fe0988bb067f: Layer already exists 
ea680fbff095: Layer already exists 
v1: digest: sha256:f528bfaef244fb589243ff3cbb8b32f8d94791e86a7e94997f20cac32deb119e size: 1779
[ec2-user@ip-172-31-1-66 ~]$ 

[ec2-user@ip-172-31-1-66 ~]$ ls
my-first-folder
 
[ec2-user@ip-172-31-1-66 ~]$ cd my-first-folder

[ec2-user@ip-172-31-1-66 my-first-folder]$ ls
dockerfile  file.txt  index.html

[ec2-user@ip-172-31-1-66 my-first-folder]$ cat index.html 
<html><body><h1>It should work with Docker file!</h1></body></html>
[ec2-user@ip-172-31-1-66 my-first-folder]$

[ec2-user@ip-172-31-1-66 my-first-folder]$ cat dockerfile 
#pull image from Docker hub
FROM httpd
#copy index.html to container
COPY index.html /usr/local/apache2/htdocs/index.html
[ec2-user@ip-172-31-1-66 my-first-folder]$

[ec2-user@ip-172-31-1-66 my-first-folder]$ docker build . -t devpipeline/httpd:V2
[+] Building 0.4s (7/7) FINISHED                                                                                                               docker:default
 => [internal] load build definition from dockerfile                                                                                                     0.1s
 => => transferring dockerfile: 218B                                                                                                                     0.0s
 => [internal] load metadata for docker.io/library/httpd:latest                                                                                          0.0s
 => [internal] load .dockerignore                                                                                                                        0.0s
 => => transferring context: 2B                                                                                                                          0.0s
 => [internal] load build context                                                                                                                        0.1s
 => => transferring context: 164B                                                                                                                        0.0s
 => [1/2] FROM docker.io/library/httpd:latest                                                                                                            0.1s
 => [2/2] COPY index.html /usr/local/apache2/htdocs/index.html                                                                                           0.0s
 => exporting to image                                                                                                                                   0.0s
 => => exporting layers                                                                                                                                  0.0s
 => => writing image sha256:b4be5d1a2af6baf7c231b94a69c67e86cdce2fdc89968ceb9f779b3995485a7f                                                             0.0s
 => => naming to docker.io/devpipeline/httpd:V2                                                                                                          0.0s
[ec2-user@ip-172-31-1-66 my-first-folder]$ 

[ec2-user@ip-172-31-1-66 my-first-folder]$ docker images
REPOSITORY          TAG       IMAGE ID       CREATED             SIZE
devpipeline/httpd   V2        b4be5d1a2af6   13 seconds ago      148MB
devpipeline/httpd   v1        d28580b58d0a   26 minutes ago      148MB
 
[ec2-user@ip-172-31-1-66 my-first-folder]$ docker run -d --name myapps -p 8081:80 devpipeline/httpd:V2 
149b132e041f4ff11e79291bf82a39f35c321e7b5ff86cf9d6c5b40c9f156416
[ec2-user@ip-172-31-1-66 my-first-folder]$ 
[ec2-user@ip-172-31-1-66 my-first-folder]$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND              CREATED              STATUS                     PORTS                                   NAMES
149b132e041f   devpipeline/httpd:V2   "httpd-foreground"   About a minute ago   Up About a minute          0.0.0.0:8081->80/tcp, :::8081->80/tcp   myapps
[ec2-user@ip-172-31-1-66 my-first-folder]$ 

[ec2-user@ip-172-31-1-66 my-first-folder]$ docker push devpipeline/httpd:V2
The push refers to repository [docker.io/devpipeline/httpd]
686507bb9d2a: Pushed 
8e92d33aa718: Layer already exists 
b8d276594499: Layer already exists 
417809be0bd7: Layer already exists 
5f70bf18a086: Layer already exists 
fe0988bb067f: Layer already exists 
ea680fbff095: Layer already exists 
V2: digest: sha256:458e4ec6dc67a847962102f454f199107a584853c7df2b6d62b07332f3595b2a size: 1779
[ec2-user@ip-172-31-1-66 my-first-folder]$

======================================================================
