## note with http

### remember to add function handleError:

	    private handleError(error: any): Promise<any> {
	  console.error('An error occurred', error); // for demo purposes only
	  return Promise.reject(error.message || error);

### when using toPromise() function,remember:

	import 'rxjs/add/operator/toPromise';

### when you provide a function with promise in a  service,remember to handle the promise when you call the function in some component,or you might be caught in an error like this:
	ZoneAwarePromise {__zone_symbol__state: null, __zone_symbol__value: Array[0]}

#### 1.in login.service.ts:
	    execLogin(username:string,password:string):Promise<any> {

         return this.http
        .post( this.loginUrl, JSON.stringify({username:username,password:password}), {headers:this.headers})
        .toPromise()
        .then(res => res.json())
        .catch(this.handleError);
    }

    private handleError(error: any): Promise<any> {
     console.error('An error occurred', error); // for demo purposes only
     return Promise.reject(error.message || error);
}

#### 2. in login.component.ts:
	
      this._loginService.execLogin(this.username.value,this.password.value)
      .then(result => console.log(result));

#### 3.use .then to handle promise, .catch to catch error

## if you want use proxy with webpack-dev-server, have a look at this :

* in the file:login.service.ts:

    private headers = new Headers({'Content-Type': 'application/json'});

    private loginUrl = '/api/v1.0/login';

    execLogin(username:string,password:string):Promise<any> {

         return this.http
        .post( this.loginUrl, JSON.stringify({username:username,password:password}), {headers:this.headers})
        .toPromise()
        .then(res => res.json())
        .catch(this.handleError);
    }

* in the file : 	webpack.dev.js:
	    devServer: {
      proxy: {
      '/api/v1.0/login': {
        target: 'http://16.28.224.56:8090',
        secure: false,
        changeOrigin: true
      }
    }
    }
	



