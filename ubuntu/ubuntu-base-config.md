## apt-get update error

if got "Hash Sum Mismatch" error,first try:

	sudo rm -vf /var/lib/apt/lists/*
	sudo apt-get update

if it doesn't work , consider to change the mirror:
	
	vi /etc/apt/sources.list
	
	deb http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse
	deb http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse
	deb http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse
	deb http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse
	deb http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse
	deb-src http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse
	deb-src http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse
	deb-src http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse
	deb-src http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse
	deb-src http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse
	
