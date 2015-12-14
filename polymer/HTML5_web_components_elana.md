omponents

## 5 Problems that Web Components Solve

1. undescriptive markup
2. no component standard
3. style conflicts
4. no native template standards 
5. no native bundling

## Web Components
### Built using four technologies
1. __templates__ - define inert reusable markup
2. __custom elements__ - extend HTML
3. __shadow DOM__ - provides encapsulation of markup and styling
4. __imports__ - support bundling html, javascript and CSS files

* These technologies are not yet supported in all major browsers - __Chrome__ does have full native support
* jonrimmer.github.io.are-we-componentized-yet (check support)
* webcomponents.js polyfill library
* Web components are the foundation to Polymer; Polymer is an abstraction of web components

### What level of abstraction makes sense for web components?
* Any level - entire app to smallest module
* Think of the web as a nested set of components

## Templates

* HTML5 template tag
* Defining template tags 
* Activating templates
* Injecting data
* Nested templates

### Today's common approach to templates

#### Stuff markup into a script tag with a custom type attribute

Handlebars example: 

		<script id="entry-template" type="text/x-handlebars-template">template content</script>
		
Kendo UX example: 

		<script id="entry-template" type="text/x-kendo-template">template content</script>
		
		
* Popular because nothing in the script tag renders, but nothing in these tags is parsed as HTML markup so it's not part of the DOM, so the browser is storing this content as string. 
* This leads to using innerHTML for copying the template
* Can lead to cross-site scripting (XSS) vulnerabilities


#### Stuff templates into hidden DOM elements (display: none or extreme margin settings)

	<div style="display: none;" id="my-template"></div>
	
* Easy and safe to clone because you don't have to use innerHTML; it's just a DOM element
* Everything inside the tag still runs; may throw 404s


Need a solution that allows us to define a template using native HTML, Javascript and CSS...

	<template>
	
### Template Tag

* All markup in the template tag is inert until it is cloned and utilized on the page
* The content of the template tag is hidden from traditional selection techniques because the content of the template tag is not traversed like other DOM elements; have to use the content proprty of the template to access the elements inside. 
* Place the template tag anywhere on the page (head, body, etc.)

### Template activation 

	var template = document.querySelector( #mytemplate );
	var clone - documetn.importNode( template.content, true ); // template content copies everything between template tags
	//second parameter determines whether it is a deep copy
	dcoument.body.appendChild( clone );
	
#### Code Example
	
	<template>
	<p> I'm coming from a template </p>
	alert( 'hi!' );
	</template>
	
	<script> 
	var template = document.querySelector('template');
	var clone = document.importNode( template.content, true );
	document.body.appendChild( clone );
	console.log( template );
	</script>
	
### Inject Dynamic Data into Templates

* Inject data before cloning by manipulating the template clone



		// Get a reference to the template	
		var template = document.querySelector('template');
					
		// Use document.importNode to clone the template's content	
		var clone = document.importNode(template.content, true);
					
		//Change the target element within the template as desired
				clone.querySelector('.verb').textContent = 'awesome';
			
		// Append element to page
		document.body.appendChild(clone);
		
		
### Example of injecting dynamic data into a clone

	<template>
	<p>		
	<span class="adjective"></span> template has been copied <span class="number-of-copies></span>
	</p>
	</template
	
	<button onclick="copyTemplate()">Copy Template</button>
	
	<script>
	var numOfCopies = 0;
	
	function copyTemplate() {
		var template = document.querySelector('template');
		var clone = document.importNode(template.content, true);
		
		//replace placeholders
		clone.querySelector('.adjective').textContent = 'awesome';
		clone.querySelector('.number-of-copies').textContent = ++numberOfCopies;
		
		document.body.appendChild(clone);
	}	
	</script>	
		

### Nested Templates

* Must manually clone both parent and child templates if templates are nested
* document.querySelecter returns the __first__ selector on the page - be aware of this. 


## Custom Elements

Two new core functionalities

1. Define your own HTML element (name must have a dash - think of the first dash as a namespace)
2. Extend existing elements by adding an 'is' attribute

		<input type="text" is="search">
		
### Registering and Using Custom Elements

Simple three step process:

1. Create a prototype for the custom element

		var	slickTabs - Object.create(HTMLElement.prototype);
		//Add properties and functions to prototype here
	
	* Typically define a created callback function
	* Create shadow root and utilize desired template to populate the shadow root 
	* A document tree is a node tree [ DOM ] whose root node is a document. Any element can host zero or one associated node trees, called a shadow tree . A shadow host is an element that hosts one shadow tree.
	* Note that when creating new custom elements, the parameter that you pass in will be HTMLElement.prototype since custom elements are derived from prototpe
	* Exception when you are extending an HTML element - put that element into the arguments instead
	* List of elelment prototypes: Use Text Editor	
		
2. 	Register the element via registerElement

		document.registerElement('slick-tabs');
		
3. Use it: Add to DOM or place tag on the page

		document.body.appendChild(new SlickTabs());
		
 
### Extend Custom Elements

	var XFooProto = Object.create(HTMLElement.prototype);
	
	var XFooExtended = document.registerElement('x-foo-extended', {
		prototype: XFooProto,
		extends: 'x-foo'
	});
	
	
### Four Ways to Instantiate a Custom Element

1. Create a tag in the markup

		<pluralsight-comment />
		
2. new Operator

		var comment = new PluralsightCOmment();
		// Need to append to the DOM seperately
		
3. createElement

		var comment = document.createElement('pluralsight-comment');
		
4. innerHTML

		el.innerHTML = '<pluralsight-comment />;
		

### Three Ways to Instantiate Extended Elements

1. Declare in markup 

		<button is="super-button">
		// "super button is a type of button"
		
2. JavaScript

		var button = document.createElement('button', 'super-button');
		
3. New operator

		document.bidy.appendChild(new superButton());
		
												
### Lifecycle Callback Methods

#### Four Lifecycle Methods

Hook up behaviors that make custom elements act and feel fully native; optional

* createdCallback - instance is created
* attachedCallback - instance is inserted into the DOM
* detachedCallback - instance removed from the DOM
* attributeChangedCallback	- attributes are added, removed or updated

### Namespacing Approaches

How do we avoid naming collisions as the entire web begins to create web compnoents?

* Use the first word, before the first dash, as the namespace
* No formal standard has emerged for naming yet

#### Four proposed conventions

1. Company name
2. Brand name
3. Application name
4. GitHub username


## Shadow DOM

### Problem that the Shadow DOM solves

* scoping problem - currently write highy specific selectors to avoid other parts of the page

### Light DOM vs Shadow DOM

* __Light DOM__ - the standard DOM - what you see on the page
* __Shadow DOM__ - hides away complexity 
* __Logical DOM__  - umbrella term that covers both light and shadow DOM


* Shadow DOM has been in use for awhile
* Show user agent Shadow DOM in dev tools to see Shadow DOM of native controls

### Shadow DOM Hacks

* Shadow DOM encapsulates DOM subtrees and styles
* iframes commonly used to ensure seperate styling - a popular hack to shadow DOM like behavior
* Shadow frame is like an iframe without all the baggage and a friendlier API
* Canvas tags are hindered by low accessibility, SEO issues and difficult to easily compose items together; cannot leverage existing HTML inputs


### Create the Shadow DOM

Three step process...

1. Select a shadow host
2. Create a shadow root
3. Add elements (the same way that you append elements to the light DOM)


		<template>
			<h1>Hello world from the shadow DOM</h1>
		</template>
		
		<div id="host>
			<script>
				var host = document.querySelector('template');
				var root = host.createShadowRoot();
				root.appendChild(document.importNode(template.content, true));
			</script>
		</div>

* Regular styling will not work on the shadow DOM
* You can define styles within the template tag that will only affect the shadow DOM

### Shadow DOM Terminology 

* __shadow root__ - the root node of a shadow tree
* __shadow host__ - the element in the light DOM that hosts the shadow DOM
* __shadow boundry__ - 	encapsulates styling rules that are defined within a given piece of the shadow DOM (there are some ways to get sround this)
	* Simpler selectors
	* Simpler markup
	* __Each shadow DOM has its own scope__ 
	* enhanced readability
	* Avoids accidental styling
	
### ShadowRoot DOM Methods

These light DOM methods will NOT work on the shadow DOM unless they are used as methods of Shadow DOM elements:

* getElementsById()
* getElementsbyClassName()
* getElementsByTagName()
* getElementsByTagNameNS()
* querySelector()
* querySelectorAll()

		<div id="shadow-host"></div>
		
		<script>
		var template = document.querySelector('template');
		var host = document.querySelector('#shadow-host');
		var root = host.createShadowRoot();
		root.appendChild(document.imposrtNode(template.content, true);
		
		console.log(document.querySelector('hi'));
		
		console.log(root.querySelector('hi'));
		
		</script>
		
### JavaScript is not Encapsulated in the Shadow DOM

* Javascript defined within the tempate tag will still be traversed and manipulated with the light DOM and be attached to the window element
* The markup inside the shadow DOM is still protected from JavaSript manipulation because it cannot be traversed with traditional selection methods
* You can use IIFEs to encapsulate code


## Shadow DOM Insertion Points and Events

* Content insertions points
* Shadow insertion points
* Event quirks

### Content Insertion Points

	<content>
	
* Invite certain markup that is defined in the light DOM to render in the shadow DOM; invitation to elements from the light DOM to join shadow DOM
* HTML elements are compositional 
* Use "select" attribute to declare a selector that specifies the type of content you're looking for
* A way to create a declarative API for shadow DOM
* Empty tag invites anything that has not already been inserted

		// Custom web component on page
		<comment-form>
			<h1> Tell us what's on your mind!</h1>
			<p class="instructions"> Fill out this form </p>
		</comment-form>	
		<p class="footer">Privacy Policy</p>
		
		Random Text // Will not show up because there is now content insertion point that matches this text. 
		
		// Template definitions
		<template>
			<fieldset>
				<legend>
					<content select="h1"></content>
				</legend>
				<content select="p.instructions"></content>
				<br/>
				
				<input type="submit" value="Submit Comment">
				
				<content select=".footer"></content>
	
			</fieldset>
		</template>
		
		
		//Script defines shadow DOM and invokes component
		<script>
			var commentProto = Object.create(HTMLElement.prototype);
			
			commentProto.createCallback = function() {
				var template = document.querySelector('template');
				var shadow = this.createShadowRoot();
				shadow.appendChild(document.importNode(template.content, true));
			}
				
			document.registerElement('comment-form', {
				prototype: commentProto
			});
		
		</script>


### Insertion Points vs Distributed Nodes

* The content tag creates an __insertion point__
* Elements inserted at this point are __distributed nodes__ (and they are merely displayed at the insertion point. __They are not actually inserted.__
* The content tag says "show this content from the light DOM here
* The distributed node remains part of the light DOM
* Any content distributed here cannot be traversed with traditional APIs


### Content Selectors




