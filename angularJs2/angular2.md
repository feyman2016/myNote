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
	
## if you wanna set interval,just do this:

	import { IntervalObservable } from 'rxjs/observable/IntervalObservable';

	@Component({
		XXX
	})
	export class XXX {
		constructor(){
			IntervalObservable.create(5000).subscribe(() => do something)//this will set a observalbe with 5 seconds interval
		}
	}


##  Wait for Angular 2 to load/resolve model before rendering view/template

the following answer describes the data fetching and the template rendering order and gives you a way to solve the `TypeError: Cannot read property 'firstName' of null in [null]` error~~

http://stackoverflow.com/questions/34731869/wait-for-angular-2-to-load-resolve-model-before-rendering-view-template

the `Elvis Operator ( ?. ) ` canbe used to skip the no data error keep the app rolling~~


##  Angular2 - error if don't check if {{object.field}} exists

	http://stackoverflow.com/questions/34910928/angular2-error-if-dont-check-if-object-field-exists
	
##  Problems with Observable.interval - data is not updating in template, ngFor is incorrect length, get polling data on startup


this ticked answer mentioned that the unproper use of  `changeDetection: ChangeDetectionStrategy.OnPush ` would prevent the change!

additionally, this is a usable http.get in interval to fetch data and update the view

http://stackoverflow.com/questions/35599714/problems-with-observable-interval-data-is-not-updating-in-template-ngfor-is-i

## When you met this:
	zone.js:390 Error: Uncaught (in promise): TypeError: ctorParameters.map is not a function
	TypeError: ctorParameters.map is not a function

might caused by the angular2 version and some certain npm package mismatch
