##　How do I navigate to a sibling route?


accepted
If you are using the new router (3.0.0-beta2), you can use the ActivatedRoute to navigate to relative path as follow:

    constructor(private router: Router, private r:ActivatedRoute) {} 


    goToContact() {
      this.router.navigate(["../contacts"], { relativeTo: this.r });
    }
 
-   some more reference about router navigation:

    When performing relative navigation involving routes that start with ./ and ../, could we make the {relativeTo: this.currentActivatedRoute} implicit?

    Example, make:

    // from within /team/3, perform a relative navigation to /team/3/details
    this.router.navigate(['./details'], {relativeTo: this.route});
    ...be equivalent to:

    // from within /team/3, perform a relative navigation to /team/3/details
    this.router.navigate('./details');
    Another example, make:

    // from within /team/3, perform a relative navigation to /team/4
    this.router.navigate(['../4'], {relativeTo: this.route});
    ...be equivalent to:

    // from within /team/3, perform a relative navigation to /team/4
    this.router.navigate('../4');
    
    
##  what is difference between forRoot and for Child?

- 　**forRoot** creates a module that contains all the directives, the given routes, and the router service itself.  

- 　**forChild** creates a module that contains all the directives and the given routes, but does not include the router service.
