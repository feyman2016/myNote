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

## change the sbt repo(details):


	down vote
	accepted
	Step 1: Follow the instructions at Detailed Topics: Proxy Repositories, which I have summarised and added to below:

	(If you are using Artifactory, you can skip this step.) Create an entirely separate Maven proxy repository (or group) on your corporate Maven repository, to proxy ivy-style repositories such as these two important ones:

	http://repo.typesafe.com/typesafe/ivy-releases/
	http://repo.scala-sbt.org/scalasbt/sbt-plugin-releases/
	This is needed because some repository managers cannot handle Ivy-style and Maven-style repositories being mixed together.
	Create a file repositories, listing both your main corporate repository and any extra one that you created in step 1, in the format shown below:
	[repositories]
	  my-maven-proxy-releases: http://repo.example.com/maven-releases/
	  my-ivy-proxy-releases: http://repo.example.com/ivy-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]
	Either save that file in the .sbt directory inside your home directory, or specify it on the sbt command line (you will need to specify if you have disabled sharing):
	sbt -Dsbt.repository.config=<path-to-your-repo-file>
	Good news for those using older versions of sbt: Even though, in the sbt 0.12.0 launcher jar at least, the boot properties files for older sbt versions don't contain the required line (the one that mentions repository.config), it will still work for those versions of sbt if you edit those files to add the required line, and repackage them into the sbt 0.12.0 launcher jar! This is because the feature is implemented in the launcher, not in sbt itself. And the sbt 0.12.0 launcher is claimed to be able to launch all versions of sbt, right back to 0.7!

	Step 2: To make sure external repositories are not being used, remove the default repositories from your resolvers. This can be done in one of three ways:

	Add the command line option -Dsbt.override.build.repos=true mentioned on the Detailed Topics page above. This will cause the repositories you specified in the file to override any repositories specified in any of your sbt files. This might only work in sbt 0.12 and above, though - I haven't tried it yet.
	Having the same effect as 1, you can use overrideBuildResolvers := true, with the advantage that you can control the projects where it is applicable, depending on which scope (a project / ThisBuild / Global) you define it in. This works in sbt 0.13.
	Use fullResolvers := Seq( resolver(s) for your corporate maven repositories ) in your build files, instead of resolvers ++= or resolvers := or whatever you used to use.
	Finally, note that the sbt launcher script has a bug in reading the sbtopts file, so if you decide to put your common sbt command-line options in there, make sure the last line of the file ends in a newline (Emacs in particular can fail to ensure this, unless configured to do so).
