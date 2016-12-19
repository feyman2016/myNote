## Use Interval to fetch data and refresh the view

If you have a normal way to fetch data using a service like the following:
	
	@Component({
	  selector: 'vmse-list',
	  encapsulation: ViewEncapsulation.None,
	  styles: [require('./vmseList.scss')],
	  template: require('./vmseList.html')
	}) 
	export class VmseList implements OnInit {
	
	  source: LocalDataSource = new LocalDataSource();
	
	  constructor(
	              private _vmseService:VmseService,
	             ) {
	
	      
	     
	  }
		    
	private getAllVmse():void {
	      this._vmseService.getAllVmse()
	      .then((data) => {
	        this.source.load(data)
	      })
	        .catch(onerror => {
	            console.log(onerror);
	        });
	    }
	
	  ngOnInit() {
	         this.getAllVmse();
	
	}

it will get data by the _vmseService  
 if you wanna fetch data by interval,just use the `IntervalObservable` module 

**1.import the `IntervalObservable` module :**

	import { IntervalObservable } from 'rxjs/observable/IntervalObservable';

**2.modify the ngOnInit() :**  

		  ngOnInit() {
	         
	        IntervalObservable.create(5000).subscribe(() => {
	            this.getAllVmse();
	        });
	  }

