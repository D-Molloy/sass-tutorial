# SASS Cheatsheet
- https://sass-lang.com/documentation
- compiled scss overwrites destination css every time - only modify scss
- web-prefixes are added by compiler


## Variables
- vars compile to actual css - more stable than css vars
```scss
$primary-color: #272727;

body{
  background-color: $primary-color;
}
```

## Maps
- similar to JS objects - key/value pairs
```scss
$font-weights: (
  "regular":400,
  "medium": 500,
  "bold": 700,
);
body {
  font-weight: map-get($font-weights,bold);
}
```

## Nesting
- one of the best feature of SCSS
- might not scale well - better to just use classes to avoid over nesting element selectors.
```scss
.main{
  width: 80%;
  margin: 0 auto;
  h1{
    font-size: map-get($font-sizes, heading);
  }
  /*
   - can type full class name
   .main__paragraph{}
   or shorthand it with `&` (=== the parent)
   - need to interpolate the & so a separate rule isn't created in the css :
   .main__paragraph{} 
   ...we want: 
  `.main .main__paragraph{}`
  */
  #{&}__paragraph{
    font-weight: map-get($font-weights,bold);
    font-size: map-get($font-sizes, regular);
    &:hover{
      color: $accent-color;
    }
  }
}
```

## Paritals
- reusable code snippets
- _filename.scss indicates a partial 
- a partial file won't generate it's own file when compiled
```scss
// importing partials
@import "./resets"; //_resets.scss
@import "./variables"; //_variables.scss
```

## Functions
- used to streamline more bulky rules (like map-get)
- return a value that can then become the value for a CSS property or become something that might be passed to another function or mixin
```scss
// functions
@function weight($weight-name){
  @return map-get($font-weights,$weight-name);
}
```

## Mixins
- should define style
- very similar to a function but the main difference between the two is that mixins output lines of Sass code that will compile directly into CSS styles, while functions return a value that can then become the value for a CSS property or become something that might be passed to another function or mixin
```scss
@mixin flexCenter($direction){
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: $direction;
}
.main{
  @include flexCenter(column);
}
============
// if the arg passed is false, the light class won't be compiled into the css
@mixin theme($light-theme:true){
  @if $light-theme{
    background-color: lighten($primary-color, 100%);
    color: darken($text-color, 100%)
  }
}
.light{
  @include theme($light-theme:true)
}
------------
<body class="light">

==============
//_variables.scss
$mobile: 800px
//main.scss
@mixin mobile{
  @media (max-width: $mobile){
    @content;
  }

.main{
  //...
  // change the flex direction to column/font to red at vw<=800px
  @include mobile{
    flex-direction: column;
    color:red;
  }
}
```

## Extensions
- extend an element to inherit styles from another element
```scss
  #{&}__paragraph1{
    font-weight:weight(thin);
    font-size: size(regular);
    &:hover{
      color: $accent-color;
    }
  }
  #{&}__paragraph2{
    @extend .main__paragraph1;
    &:hover{
      color: purple
    }
  }
```

## Calculations
- css calc() allows mixing of types:
```css
width: calc(80%-400px)
```
- sass requires the same types