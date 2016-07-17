![](https://github.com/donpayne/tutorials/raw/master/html/img/post_01.png)
Inspired by Susan Simkins, *[www.pluralsight.com](http://www.pluralsight.com/courses/introduction-building-website-layouts-2184)*

## Analyzing Layouts
Many sites have common layout elements.  
* Primary Navigation
* Secondary Navigation
* Logo or Title
* Hero or Featured Content
* Main Content
* Footer

![](https://github.com/donpayne/tutorials/raw/master/html/img/Nokia-USA-vs-Samsung-Wirify-wireframe-comparison.png)

## Analyzing UI Patterns
Use and arrange common components to feature what is important in your site.

![](https://github.com/donpayne/tutorials/raw/master/html/img/Layout%20UI%20Patterns.png)

![](https://github.com/donpayne/tutorials/raw/master/html/img/Z-Layout.jpg)

## Website Plan and Analyzing Patterns
Organize the layout using the **Mobile First** methodology.  Start small, then work up in complexity.  Start with a mockup of each page.

![](https://github.com/donpayne/tutorials/raw/master/html/img/home-wireframe.jpg)
![](https://github.com/donpayne/tutorials/raw/master/html/img/wireframe-location.jpg)
![](https://github.com/donpayne/tutorials/raw/master/html/img/wireframe-menu.jpg)

## The Fluid Nature of the Web
By default, the web is responsive in its implementation of html markup.  Don't screw it up by setting static widths to everything. Instead, use % widths to maintain fluidity.

**Note**

When floating elements, it is important to set the display to 'block' to ensure that the height of the container element is not collapsed to 0.  Another nice benefit of 'block' is that it will cause the elements to expand to the full with of it's container.

## Progressive Enhancement and Hierarchy
The practice of adding technology in layers to a website to ensure that if all else fails, content can still be accessed. Emphasizes the "separation of concerns."

HTML (content) > CSS (presentation) > Javascript (behavior)

## HTML5 Sectioning Elements
Sectioning elements are elements whose intended use is to divide content up into logical sections. This allows for the creation of a semantic outline and for styles to be applied to grouped content.  For more info see [How to Use The HTML5 Sectioning Elements](http://blog.teamtreehouse.com/use-html5-sectioning-elements).

* Address
* Article
* Aside
* Header
* Footer
* Section
* Nav
* Div

## Using HTML5 Sectioning Elements
What does it mean to have HTML that is Semantic?  To use the most descriptive and meaningful elements as possible when marking up your content. 

This is one of the key motivations for Custom Elements in the Web Components Specification under the W3C.  For more info see [webcomponents.org](http://webcomponents.org/).

## HTML5 Sectioning Elements Continued
Go through the HTML markup and section off each logical group of content in the most appropriate use of sectioning elements.

## Building Navigation
Setup your Primary Navigation using the nav element with an un-ordered list of items.  I am partial to [Bootstrap](http://getbootstrap.com/components/#navbar).
```html
<nav class="navbar navbar-default">
  <div class="container-fluid">
    <!-- Brand and toggle get grouped for better mobile display -->
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">Brand</a>
    </div>

    <!-- Collect the nav links, forms, and other content for toggling -->
    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <li class="active"><a href="#">Link <span class="sr-only">(current)</span></a></li>
        <li><a href="#">Link</a></li>
        <li class="dropdown">
          <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown <span class="caret"></span></a>
          <ul class="dropdown-menu">
            <li><a href="#">Action</a></li>
            <li><a href="#">Another action</a></li>
            <li><a href="#">Something else here</a></li>
            <li role="separator" class="divider"></li>
            <li><a href="#">Separated link</a></li>
            <li role="separator" class="divider"></li>
            <li><a href="#">One more separated link</a></li>
          </ul>
        </li>
      </ul>
      <form class="navbar-form navbar-left" role="search">
        <div class="form-group">
          <input type="text" class="form-control" placeholder="Search">
        </div>
        <button type="submit" class="btn btn-default">Submit</button>
      </form>
      <ul class="nav navbar-nav navbar-right">
        <li><a href="#">Link</a></li>
        <li class="dropdown">
          <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown <span class="caret"></span></a>
          <ul class="dropdown-menu">
            <li><a href="#">Action</a></li>
            <li><a href="#">Another action</a></li>
            <li><a href="#">Something else here</a></li>
            <li role="separator" class="divider"></li>
            <li><a href="#">Separated link</a></li>
          </ul>
        </li>
      </ul>
    </div><!-- /.navbar-collapse -->
  </div><!-- /.container-fluid -->
</nav>
```

## Adding Images
Good idea to wrap images with an anchor tag to allow for clickable links.  Also, add an 'alt' attribute to the image tag for better clarity.

## Adding External Fonts
Use a style tile to establish what styles you want to maintain on your site.

![](https://github.com/donpayne/tutorials/raw/master/html/img/style-tile.jpg)

Using Google Fonts
```html
<link href='http://fonts.googleapis.com/css?family=Alegreya+SC|Alegreya:400italic,400,700' rel='stylesheet' type='text/css' >
```

Using Adobe Typekits (Chronos Pro)
```html
<script src='//use.typekit.net/ouy7etw.js'></script>
<script>try{Typekit.load();}catch(e){}</script>
```

## CSS Resets

## Typographic Scales and Vertical Rhythm

## Global Styles

## Links

## Understanding the Cascade: Source Order

## Understanding the Cascade: Importance

## Understanding the Cascade: Inheritance

## Classes and IDs

## Understanding the Cascade: Specificity

## Understanding Classes and IDs

## Adding a Page Wrapper

## Global Logo Styles

## Styling the Navigation

## Navigation Continued

## Home Page Styles

## Styling Featured Sections

## Styling the Buttons

## Styling the Menu Page

## Styling the Location and Hours Page

## Media Queries

## Creating Responsive Navigation

## Styling Landing Text and Responsive Type

## Media Queries for Featured Sections

## Media Queries for Featured Sections Continued

## Styling the Gallery

## Creating a Sidebar

## Testing: Validation, Contrast, and Cross-browser

## Final Notes on CSS Architecture