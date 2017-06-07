## CICD

This manual is mainly about how to deploy gitlab, jenkins and artifactory with docker container(ahh, not the gitlab :-(    ).

#### Gitlab

Install the community-edition of the gitlab(non-docker)

1. Get [official](https://docs.gitlab.com/) Omnibus version and follow the install [instructions](https://docs.gitlab.com/omnibus/README.html)

2. To simplify, we use the LDAP server for authentication

   (1) First, we need a LDAP server:

   follow the [LDAP server install guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-openldap-and-phpldapadmin-on-an-ubuntu-14-04-server) on digital ocean before the step `Install phpLDAPadmin to Manage LDAP with a Web Interface` (because we dont need that). You should be careful with the step `Reconfigure slapd to Select Better Settings` because it will set `DNS domain name`,`Organization name` , the `DNS domain name` is very important and will be used later,please keep in mind. And also , there are some concepts important here ([Reference](http://www.tuicool.com/articles/ZFbYruI))

```
o– organization（组织-公司）
ou – organization unit（组织单元/部门）
c - countryName（国家）
dc - domainComponent（域名组件）
sn – suer name（真实名称）
cn - common name（常用名称）
dn - distinguished name（专有名称）


```

​	To simplify , we can just think that LDAP use a tree-like structure for storing, a certain record(some specific user) might contains high-level tags as `c - countryName`   ,  and group-level tags as `ou – organization unit` and user-level tags as `uid`.  `dc - domainComponent` is component of `DNS domain name`,say the `DNS domain name: gitlab.dev` is made of `dc:gitlab,and dc:dev`. Finnaly, the  `dn - distinguished name` is a full record that contains all the tags from high-level to low-level ,for example `ou=Users,dc=gitlab,dc=dev`  is a group name Users, and `dn: uid=bianhongyu,ou=Users,dc=gitlab,dc=dev` is a specific user of this group.

​	In my case,  when I follow this [guide ](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-openldap-and-phpldapadmin-on-an-ubuntu-14-04-server), i use `gitlab.dev` as the  `DNS domain name`  and `example ` as `Organization name`(I think this one is not important). And I use `admin/admin` as my admin account.

Till now, we have our ldap sever now, let's check the 389 port  has been utilized now:

```
root@vmse-Standard-PC-i440FX-PIIX-1996:/home/vmse/workspace/ldap/generateAccount# netstat -ntlp|grep 389
tcp        0      0 0.0.0.0:389             0.0.0.0:*               LISTEN      18705/slapd     
tcp6       0      0 :::389                  :::*                    LISTEN      18705/slapd    
```



and check the admin record by :

```
ldapsearch -x -b "dc=gitlab,dc=dev"
```

this command will get all the records matchs `"dc=gitlab,dc=dev"`

```
root@vmse:/home# ldapsearch -x -b "dc=gitlab,dc=dev"
ldapsearch -x -b "dc=gitlab,dc=dev"
# extended LDIF
#
# LDAPv3
# base <dc=gitlab,dc=dev> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# gitlab.dev
dn: dc=gitlab,dc=dev
objectClass: top
objectClass: dcObject
objectClass: organization
o: example
dc: gitlab

# admin, gitlab.dev
dn: cn=admin,dc=gitlab,dc=dev
objectClass: simpleSecurityObject
objectClass: organizationalRole
cn: admin
description: LDAP administrator
```

Till now we have only `admin` user, and the  `LDAP  password` is `admin` in my case.

(2) Second step would be add Groups use `ldapadd` command:

​	Be careful, you are likely to get error like `ldap_bind: Invalid credentials (49)`

- create a `group.ldif ` file

  ```
  root@vmse-Standard-PC-i440FX-PIIX-1996:/home/vmse/workspace/ldap/generateAccount# cat group1.ldif 
  dn: ou=Users,dc=gitlab,dc=dev
  objectClass: organizationalUnit
  ou: Users
  ```

-  add group to ldap server:

  ```
  root@vmse:/home/vmse/# ldapadd -x  -W -D "cn=admin,dc=gitlab,dc=dev" -f group1.ldif 
  Enter LDAP Password: 
  adding new entry "ou=Users,dc=gitlab,dc=dev"

  ```

  this will successfully add a new group , the `-D`  represents  ` your DN here. i.e Distinguished name to authenticate in the server`  , in this case , we have get the `admin dn` info by the cmd `ldapsearch -x -b "dc=gitlab,dc=dev"` as shown above. If you offered the wrong `-D parameter` ,you might got the notorious  `ldap_bind: Invalid credentials (49)`

- confirm we have added group successfully:

  ```
  root@vmse-Standard-PC-i440FX-PIIX-1996:/home/vmse/workspace/ldap/generateAccount# ldapsearch -x -b "dc=gitlab,dc=dev"
  # extended LDIF
  #
  # LDAPv3
  # base <dc=gitlab,dc=dev> with scope subtree
  # filter: (objectclass=*)
  # requesting: ALL
  #

  # gitlab.dev
  dn: dc=gitlab,dc=dev
  objectClass: top
  objectClass: dcObject
  objectClass: organization
  o: example
  dc: gitlab

  # admin, gitlab.dev
  dn: cn=admin,dc=gitlab,dc=dev
  objectClass: simpleSecurityObject
  objectClass: organizationalRole
  cn: admin
  description: LDAP administrator

  # Users, gitlab.dev
  dn: ou=Users,dc=gitlab,dc=dev
  objectClass: organizationalUnit
  ou: Users

  ```

- create a user profile :

  ```
  root@vmse-Standard-PC-i440FX-PIIX-1996:/home/vmse/workspace/ldap/generateAccount# cat xuanhaoran.ldif 
  dn: uid=xuanhaoran,ou=Users,dc=gitlab,dc=dev
  objectClass: organizationalPerson
  objectClass: person
  objectClass: top
  objectClass: inetOrgPerson
  objectClass: posixAccount
  objectClass: shadowAccount
  uid: xuanhaoran
  sn: xuanhaoran
  givenName: xuanhaoran
  cn: xuanhaoran
  displayName: xuanhaoran
  uidNumber: 10000
  gidNumber: 10000
  userPassword: p@ssword
  gecos: xuanhaoran
  loginShell: /bin/bash
  homeDirectory: /profiles/xuanhaoran
  mail: xuanhaoran.cn@gmail.com
  telephoneNumber: 000-000-0000
  st: NY
  manager: uid=xuanhaoran,ou=Users,dc=gitlab,dc=dev
  shadowExpire: -1
  shadowFlag: 0
  shadowWarning: 7
  shadowMin: 8
  shadowMax: 999999
  shadowLastChange: 10877
  ```

  the `dn: uid=xuanhaoran,ou=Users,dc=gitlab,dc=dev` is  important , despite the `uid=xuanhaoran` , it is exactly the same as the group we have added `ou=Users,dc=gitlab,dc=dev`, which will make sure the user is in the group.

- add the user:

  ```
  root@vmse:/home/vmse/# ldapadd -x  -W -D "cn=admin,dc=gitlab,dc=dev" -f xuanhaoran.ldif
  Enter LDAP Password: 
  adding new entry "uid=xuanhaoran,ou=Users,dc=gitlab,dc=dev"
  ```

- confirm the adding user step:

  ```
  root@vmse:/home/vmse# ldapsearch -x -b "dc=gitlab,dc=dev" |grep dn
  dn: dc=gitlab,dc=dev
  dn: cn=admin,dc=gitlab,dc=dev
  dn: ou=Users,dc=gitlab,dc=dev
  dn: uid=xuanhaoran,ou=Users,dc=gitlab,dc=dev
  ```

  Till now , we have finished the ldap server confifuration part! :)

  There are useful [ref1](http://www.tuicool.com/articles/ZFbYruI) and [ref2](http://www.thegeekstuff.com/2015/02/openldap-add-users-groups) which you can refer to.


3 Configure gitlab to use LDAP

```
vi /etc/gitlab/gitlab.rb
```

and follow the [official instructions](https://docs.gitlab.com/omnibus/settings/ldap.html) 

in my case , the related conf is :

```
gitlab_rails['ldap_enabled'] = true
gitlab_rails['ldap_servers'] = YAML.load <<-'EOS' # remember to close this block with 'EOS' below
ldap:
  enable: true
  host: 'gitlab.dev'  //the dns we've set
  base: 'dc=gitlab,dc=dev'  //dns is made of 2 dc here
  port: 389  //usually 389,the ldap server port
  uid: 'uid'  //needn't change
  method: 'plain' # "ssl" or "plain"  //'plain' usually coms up with '389'
  bind_dn: 'dc=gitlab,dc=dev' //just like base
  password: 'vmse@123'  // the gitlab password, have nothing to do with the ldap
EOS
```

and the reload:

```
sudo gitlab-ctl reconfigure
```

BTW, if you want to change the git repo addr, it is the `external_url ` in the `/etc/gitlab/gitlab.rb`,e.g:

```
external_url 'http://16.28.221.79'
```

also, reload :

```
sudo gitlab-ctl reconfigure
```



Till now, the Configuration is done, then you can login by the LDAP way :)

  

  ​

  ​



#### Jenkins

```
docker pull jenkins

docker run --name myjenkins -d -t -p 8080:8080 -p 50000:50000 -v \ /var/lib/jenkins:/var/jenkins_home jenkins
```

Please be attentive: the directory `/var/lib/jenkins` should be accessd by the jenkins user from the docker container.So maybe you need use `chmod` upon the dir `/var/lib/jenkins`

#### Artifactory

​	This guide is for installing artifactory on ubuntu14.04, and java should be installed before that

Follow this guide to install java :

`https://hostpresto.com/community/tutorials/how-to-install-jfrog-artifactory-on-ubuntu-14-04/`

And then follow the [official](https://www.jfrog.com/confluence/display/RTF/Installing+on+Linux+Solaris+or+Mac+OS#InstallingonLinuxSolarisorMacOS-RPMorDebianInstallation) guide to install artifactory:

```
#: echo "deb https://jfrog.bintray.com/artifactory-pro-debs xenial main" | sudo tee -a  \ /etc/apt/sources.list
#: curl https://bintray.com/user/downloadSubjectPublicKey?username=jfrog | sudo apt-key add -
#: sudo apt-get update
#: sudo apt-get install jfrog-artifactory-oss
#: service artifactory start | stop
```



#### Docker-Registry

- Follow [this guide](https://docs.docker.com/registry/#alternatives) to install 

  ```
  docker run -d -p 5000:5000 --name registry-srv registry:2
  ```

  ​

- Confirm docker-registry installed successfully

  ```
  # from other VM with docker environment
  ~: docker push 16.28.221.80:5000/mysecond
  Error response from daemon: Get https://10.10.239.222:5000/v1/_ping: http: server gave HTTP response to HTTPS client

  ~: touch /etc/docker/daemon.json
  ~: echo '{ "insecure-registries":    ["$your-registry-external-ip:5000"] }' >  \ /etc/docker/daemon.json
  ~: sudo service docker restart
  ~: docker push 16.28.221.80:5000/mysecond  //will success
  ```

  ​

- Install the web-UI for docker-registry:

  ```
  docker run -it -d  -p 8080:8080 --name registry-web --link registry-srv -e REGISTRY_URL=http://16.28.221.80:5000/v2 -e REGISTRY_NAME=16.28.221.80:5000 hyper/docker-registry-web 
  ```

  Then access the docker registry by `16.28.221.80:8080`

  ​

  #### DNS

  Dns can be set using [skydns](https://github.com/skynetservices/skydns) and etcd

  ```
  root@dns-server:/etc/init.d# docker ps -a
  CONTAINER ID        IMAGE                        COMMAND                  CREATED             STATUS              PORTS                                                                NAMES
  c0816ffdc297        skynetservices/skydns        "/skydns"                12 months ago       Up 48 minutes                                                                            skydns
  6e3643d77e78        quay.io/coreos/etcd:v2.2.5   "/etcd -name etcd0 -a"   12 months ago       Up 48 minutes       0.0.0.0:2379-2380->2379-2380/tcp, 0.0.0.0:4001->4001/tcp, 7001/tcp   etcd

  ```

  ​

  ​

  ​





