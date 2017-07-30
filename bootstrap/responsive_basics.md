## Responsive basics for bootstrap

 You may have heard of bootstrap cause it's very popular, this article mainly demonstrates some responsive basics for bootstrap.

#### 1.Chrome debug tools

- Chrome device simulator

  This may simulator most popular mobile devices on chrome debug tools for responsive web development ,just:

  `option+cmd+c` to open the chrome debug tool, the simulator is just at the left of the `Elements` button.

  Tips, button at the left of the simulator is also useful, you can get the DOM by click the elements in the html page.

- Udacity front end feedback

  it is a chrome plugin which can give you a feedback when you change DOM in chrome debug tool.

  Here is the [GitHub link](https://github.com/udacity/frontend-grading-engine)

  ​



#### 2.Responsive Basics

- **Basic conceptions**:

  **HP**:hardware pixel

  **DIP**: Device Independent pixels( it is normally smaller than the real deivce's pixels, and it have no dependency on the real device's pixels )

  **DPR**:Device pixel ratio (.HP/DIP )

  Say, if the HP is 1960 px , the DIP is 960, then the DPR is 2.

  ​

- **responsive images:**

  a little trick about CSS is that it **allows its content to overflow** the container,say ,if you have:

  ```
  <div>
  	<img src= "XXXXXX">
  	</img>
  </div>
  ```

  if the `img` is too large , it may exceeds it's container `div. 

   

  To solve this, just add the following in CSS files:

  ```
  img {
    max-width : 100%;
  }
  ```

- Avoid too little buttons

  ```
  nav a, button {	
  		min-width : 48 px;
  		min-height : 48 px;
  }

  ```

  ​