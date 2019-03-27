---
layout: post
title: Markdown quick reference cheat sheet
tags: [Markdown, Cookbook]
--- 

### Header
- # Header 1
- ## Header 2
- ### Header 3
- #### Header 4
- ##### Header 5
- ###### Header 6

### Emphasis
Use `*Emphasize* or _emphasize_` for *Emphasize* _emphasize_  
Use `**Strong** or __Strong__` for **Strong** __Strong__  

### Lists
- Underorder 1  
- Underorder 2  
 - Underoder 2.1  
 - Underorder 2.2

### Links
Just use `http://github.com` for an automatic link http://github.com.  
Or use a `[link](http://github.com "Title")` for a [link](http://github.com "Title") with title  
Or use referenced link for the url : 
```
Some text with [a link][1] and
another [link][2].

[1]: http://example.com/ "Title"
[2]: http://example.org/ "Title"
```
Result : 
Some text with [a link][1] and
another [link][2].

[1]: http://example.com/ "Title"
[2]: http://example.org/ "Title"

### Images
##### Inline image 
`Avatar: ![Alt](/img/avatar/chibit.png "Title")`  
for Avatar: ![Alt|20x20](/img/avatar/chibit.png "Title")

##### Referenced image 
```
Avatar: ![Alt][image_1]

[image_1]: /img/avatar/chibit.png
```
Result :
Avatar: ![Alt][image_1]

[image_1]: /img/avatar/chibit.png

##### Linked Images
```
Linked logo: [![alt text](https://www.google.com/s2/favicons?domain=www.google.com)](http://www.google.com "Title")
```
Result :  
Linked logo: [![alt text](https://www.google.com/s2/favicons?domain=www.google.com)](http://www.google.com "Title")

### Footnotes
```
I have more [^1] to say up here.

[^1]: To say down here.
```
Result : 
I have more [^1] to say up here.

[^1]: To say down here.
