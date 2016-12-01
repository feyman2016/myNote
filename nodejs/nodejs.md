## nodejs on Windows 7

I hate Windows!!!!!



- Fixing npm permissions


 https://docs.npmjs.com/getting-started/fixing-npm-permissions


- TypeError: webpack.validateSchema is not a function

npm always update,which may cause some mismatch

Looks like npm bug, since webpack-dev-server@2.1.0-beta.11 requires webpack@^2.1.0-beta.26 but npm failed to install it.

The easiest way to avoid the issue without updating too much is to change dependency in package.json to

  		"webpack-dev-server": "2.1.0-beta.10",

Instead of something like

		  "webpack-dev-server": "^2.1.0-beta.9",

"^" char before version says "compatible with". Removing it sticks to the version exactly.

Don't forget to run npm install or npm update afterwards.

- If meet this:
	
	MSBUILD : error MSB3428: Could not load the Visual C++ component "VCBuild.exe". To fix this, 1 
) install the .NET Framework 2.0 SDK, 2) install Microsoft Visual Studio 2005 or 3) add the lo 
cation of the component to the system path if it is installed elsewhere.

try this:
	
		npm install --global --production windows-build-tools
		(when you run the command above,you might meet this prompt:node v7.1.0 has a known bug with windows,then you can downgrade node to v7.0.0 )

and if it does not solve your problem, install visual studio 2012(I use visual studio 2012 professional)

- error MSB6006: "CL.exe" exited with code -1073741515 

try this way :
Add this to the  PATH environment variables:

	C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE  and  C:\Program Files (x86)\Microsoft Visual Studio 	12.0\SDK\v3.5\Bin   to PATH enviroment variable in System Variable

Copy those files:  

	msobj110.dll
	mspdb110.dll
	mspdbcore.dll
	mspdbsrv.exe

From

	C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE

to

	C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin





## nodejs on Ubuntu 14.04

-   just use the `nvm` to install,following this:  
https://www.digitalocean.com/community/tutorials/how-to-install-node-js-with-nvm-node-version-manager-on-a-vps
