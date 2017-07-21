## Notes to use openstack api

#### 1 . auth_url

If you use auth_url in openstack v3 , then you should add domain as listed, because there's some bug in the official guide.

https://ask.openstack.org/en/question/95377/python-openstacksdk-how-to-authorite/

#### 2. add user to role 

there's a bug for the add user to role, as mentioned [here](https://bugs.launchpad.net/python-openstacksdk/+bug/1680446)

