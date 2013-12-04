#Font Awesome with Sass

Font Awesome 4.0.1　the iconic font designed for Bootstrap.

The full suite of pictographic icons, examples, and documentation can be found at [http://fontawesome.io](http://fontawesome.io).  Stay up to date on Twitter at [http://twitter.com/fontawesome](http://twitter.com/fontawesome).

###使用说明

Font Awesome Sass在[原版本](https://github.com/FortAwesome/font-awesome-sass)进行过改良，主要借用了原版本的变量。其使用依赖于W3cplusSass库中的`_css3.scss`，另外独立为其创建了一个功能模块“font-awesome”。下面依赖于:

```
+font-awesome
+--fonts
|--_variables.scss
|--_font_awesome.scss
```

整个模块依赖于`font-face`、`icon`、`get-icons`三个mixins和一个`match`函数：

####font-face mixin

```
//-----------------------------------------
//thanks: https://github.com/thoughtbot/bourbon/blob/master/app/assets/stylesheets/css3/_font-face.scss
//eg.
//@include font-face(SourceSansPro, '/fonts/Source_Sans_Pro/SourceSansPro-Regular');
//@include font-face(SourceSansPro, '/fonts/Source_Sans_Pro/SourceSansPro-Bold', bold);
//@include font-face(SourceSansPro, '/fonts/Source_Sans_Pro/SourceSansPro-Italic', normal, italic);
// Rails asset-pipeline - place fonts in app/assets/fonts/
//@include font-face(SourceSansPro, 'Source_Sans_Pro/SourceSansPro-Regular', normal, $asset-pipeline: true);
//------------------------------------------

//order of the includes matters, and it is: normal,bold,italic,bold+italic
@mixin font-face($font-family,$file-path,$font-weight:normal,$font-style:normal,$asset-pipeline: false){
	@font-face {
		font: {
			family: $font-family;
			weight: $font-weight;
			style: $font-style;
		} 
		
		@if $asset-pipeline == true {
			src: font-url("#{$file-path}.eot");
			src: font-url("#{$file-path}.eot?#iefix") format("embedded-opentype"),
					 font-url("#{$file-path}.woff") format("woff"),
					 font-url("#{$file-path}.ttf") format("truetype"),
					 font-url("#{$file-path}.svg##{$font-family}") format("svg");
		}
		@else {
			src: url("#{$file-path}.eot");
			src: url("#{$file-path}.eot?#iefix") format("embedded-opentype"),
					 url("#{$file-path}.woff") format("woff"),
					 url("#{$file-path}.ttf") format("truetype"),
					 url("#{$file-path}.svg##{$font-family}") format("svg");	
		}
	}
}
```
主要用来调用web字体。

####icon mixin

```
//----------------------------------------------
// create icon by font face
// 1. @include font-face($fa-family-name,$file-path,normal,$asset-pipeline:false);
// 2. @include icon;
// 3. @include icon("before",false,"battery-low")
// OR
// 1. @include font-face($fa-family-name,$file-path,normal,$asset-pipeline:false);
// 2. @include icon("before",true,"battery-low")
//----------------------------------------------
//For adding icons to elements using css pseudo-elements
@mixin icon($position:"before",$styles:true,$icon:false){
	//Either a :before or :after pseudo-element, defaulting to :before
	&:#{$position} {
		@if $icon {
			//icon has been specified
			content: match($icons,$icon);
		}
		@if $styles {
			//Suportive icon styles have been specified
			font: {
				family: $fa-family-name;
				style: normal;
				weight: normal;
				variant: normal;
			}
			text-transform: none;
			speak: none;
			line-height: 1;
			-webkit-font-smoothing: antialiased;
		}
		//Include any extra rules supplied for the pseudo-element
		@content;
	}
}
```
主要用来实现字体图标的样式。

####get-icons mixin

```
//open all icons
// True: @include get-icons(true);open all icons
// False: @include get-icons(); not open all icons

@mixin get-icons($All-icons:false) {
	@if $All-icons {
		@each $icon in $icons {
			$name: nth($icon, 1);
			.#{$fa-css-prefix}#{$name}{
				@include icon("before",false,$name);
			}
		}
	}
}
```
设置一个开关，如果为真，生成所有的font awesome所有图标，如果为假不生成所有图标。

####match function

```
//To sumulate associative arrays
//source:http://hugogiraudel.com/2013/08/12/sass-functions/#mapping

@function match($haystack,$needle){
	@each $item in $haystack {
		$index:index($item,$needle);
		@if $index {
			$return: if($index == 1, 2, $index);
			@return nth($item,$return);
		}
	}
	@return false;
}
```

模拟一个map函数功能。

**上面几个mixin和function都放置在核心库lib/core目录下的`_css3.scss`中**。

最后调用是关键。为了方便调用这个模块，单独在模块中创建了`_font_awesome.scss`文件，用来引用模块所需的文件与功能：

```
@import "variables.scss";
@import "../../core/css3.scss";

@include font-face($fa-family-name,$file-path,normal,$asset-pipeline:false);

[class^="#{$fa-css-prefix}"],
[class*="#{$fa-css-prefix}"] {
	@include icon;
}

//How to use
//1. open one icon with style true
//.#{$fa-css-prefix}glass{
//	@include icon("before",true,"glass");
//}
//2. open one icon with style false
//.#{$fa-css-prefix}music{
//	@include icon("before",false,"music");
//}
//3. open all icons
//@include get-icons(true);
```
最后只需要在实际引用的过程中调用这个文件：

```
@import "../../lib/function.scss";
@import "../../lib/module/font-awesome/font_awesome.scss";

.#{$fa-css-prefix}glass{
	@include icon("before",true,"glass");
}
.#{$fa-css-prefix}music{
	@include icon("before",false,"music");
}

@include get-icons(true);
```
**特别提醒，保持路径的正确性。**

###License

- The Font Awesome font is licensed under SIL OFL 1.1 - http://scripts.sil.org/OFL
- Font Awesome CSS, LESS, and SASS files are licensed under MIT License - http://opensource.org/licenses/mit-license.html
- Font Awesome documentation licensed under CC BY 3.0 -http://creativecommons.org/licenses/by/3.0/
- Attribution is no longer required in Font Awesome 3.0, but much appreciated:"Font Awesome by Dave Gandy - http://fontawesome.io"

###Author - Dave Gandy

- Email: dave@fontawesome.io
- Twitter: http://twitter.com/davegandy
- Work: Lead Product Designer @ Kyruus - http://kyruus.com