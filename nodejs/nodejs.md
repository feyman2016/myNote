## nodejs



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


