## About pip install

### pip install libs by a requirements.txt

if you have a requirements.txt like:

	BeautifulSoup==3.2.0
	Django==1.3
	Fabric==1.2.0
	Jinja2==2.5.5
	PyYAML==3.09
	Pygments==1.4
	SQLAlchemy==0.7.1
	South==0.7.3
	amqplib==0.6.1
	anyjson==0.3

you can install them by:

	pip install -r /path/to/requirements.txt

you can create requirements.txt the by:

	pip freeze > requirements.txt

###pip install too slow

you can try to add internal images as follows:

	http://pypi.douban.com/  豆瓣
	http://pypi.hustunique.com/  华中理工大学
	http://pypi.sdutlinux.org/  山东理工大学
	http://pypi.mirrors.ustc.edu.cn/  中国科学技术大学

put one of them in "~/.pip/pip.conf" as follows:

	[global]
	index-url = http://pypi.douban.com/simple

or execute a pip install with parameters:

	pip install web.py -i http://pypi.douban.com/simple

