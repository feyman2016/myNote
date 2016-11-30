##　How do I navigate to a sibling route?


accepted
If you are using the new router (3.0.0-beta2), you can use the ActivatedRoute to navigate to relative path as follow:

    constructor(private router: Router, private r:ActivatedRoute) {} 


    goToContact() {
      this.router.navigate(["../contacts"], { relativeTo: this.r });
    }
    
##  what is difference between forRoot and for Child?

- 　**forRoot** creates a module that contains all the directives, the given routes, and the router service itself.  

- 　**forChild** creates a module that contains all the directives and the given routes, but does not include the router service.
