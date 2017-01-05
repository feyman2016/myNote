## cannot ping or ssh to VMs by floatingIP in certain tenants

if you cannot just ping or ssh to VMs by floatingIP in certain tenants while in other tenants no such problems exist, may be it is the mis-configuration of the security group 

check that in the host as follows:

	root@openvmse21:/home/vmse/workspace# source demo-openrc.sh 
	root@openvmse21:/home/vmse/workspace# neutron security-group-list 
	+--------------------------------------+---------+----------------------------------------------------------------------+
	| id                                   | name    | security_group_rules                                                 |
	+--------------------------------------+---------+----------------------------------------------------------------------+
	| f8e765a5-b478-4710-9eea-82307efce9c0 | default | egress, IPv4                                                         |
	|                                      |         | egress, IPv4, 1-65535/tcp, remote_ip_prefix: 0.0.0.0/0               |
	|                                      |         | egress, IPv4, 1-65535/udp, remote_ip_prefix: 0.0.0.0/0               |
	|                                      |         | egress, IPv4, icmp, remote_ip_prefix: 0.0.0.0/0                      |
	|                                      |         | egress, IPv6                                                         |
	|                                      |         | ingress, IPv4, 1-65535/tcp, remote_ip_prefix: 0.0.0.0/0              |
	|                                      |         | ingress, IPv4, 1-65535/udp, remote_ip_prefix: 0.0.0.0/0              |
	|                                      |         | ingress, IPv4, icmp, remote_ip_prefix: 0.0.0.0/0                     |
	|                                      |         | ingress, IPv4, remote_group_id: f8e765a5-b478-4710-9eea-82307efce9c0 |
	|                                      |         | ingress, IPv6, remote_group_id: f8e765a5-b478-4710-9eea-82307efce9c0 |
	+--------------------------------------+---------+----------------------------------------------------------------------+
	root@openvmse21:/home/vmse/workspace# source vuic-openrc.sh 
	root@openvmse21:/home/vmse/workspace# neutron security-group-list 
	+--------------------------------------+---------+----------------------------------------------------------------------+
	| id                                   | name    | security_group_rules                                                 |
	+--------------------------------------+---------+----------------------------------------------------------------------+
	| 99bf1937-6de8-4a0f-8e78-f3c747ba6639 | default | egress, IPv4                                                         |
	|                                      |         | egress, IPv6                                                         |
	|                                      |         | ingress, IPv4, remote_group_id: 99bf1937-6de8-4a0f-8e78-f3c747ba6639 |
	|                                      |         | ingress, IPv6, remote_group_id: 99bf1937-6de8-4a0f-8e78-f3c747ba6639 |
	+--------------------------------------+---------+----------------------------------------------------------------------+

so it seems that the tenant vuic didn't add access rules,just add these rules by the dashboard


### generally,security groups will be disabled in the openstack cluster,then there will be no issues like these. 


