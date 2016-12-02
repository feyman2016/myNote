## using sbt to download the dependencies for the first time

-    cd to the project directory]

execute:

		sbt

normally,you cannot download successfully because the `chinese greatwall`

-  dont be afraid,we can try the proxies:

**shadowsocks or lantern:**
usually these proxies have local proxy port,lantern uses 8787 as default, shadowsocks can be configed ,but usually 8081

then based on your local port,you can try(say you use shadowsocks) :

	sbt -Dhttp.proxyHost=127.0.0.1 -Dhttp.proxyPort=8081 -Dhttps.proxyHost=127.0.0.1 -Dhttps.proxyPort=8081


-	then you can use intellij to import your project **:)**