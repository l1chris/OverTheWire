## Level 0 &rarr; Level 1

This is quite simple. After logging in we are presented with a message saying "You can find the password for the next level on this page". 
If we open the browser's developer tools and take a look at the source code we can find the solution in an HTML comment inside the div tag with the id "content".

## Level 1 &rarr; Level 2

The only real difference to the previous level is that rightclicking is blocked. We can still open the developer tools with the corresping key combination and can find the solution again in the source code.

## Level 2 &rarr; Level 3

This time we are presented with a message saying there is nothing on this page. But if we look at the source code we can find an HTML img tag. If we follow the source we find an image with just a pixel in it.
At first I thought that the solution might be hidden somewhere inside the PNG file but this is not the case. Instead, we have to take a look at the image path:

> http://natas2.natas.labs.overthewire.org/files/pixel.png

The PNG file is located inside a folder named "files". If we navigate to this directory we can find a text file with the solution.

## Level 3 &rarr; Level 4

This is bit trickier. In the source code we can find an HTML comment saying that this time not even Google can find the solution. What is this hinting at? 
Googles webcrawler maybe? :) If we take a look we can indeed find a robots.txt file in the root. This file shows the following content:

``` 
User-agent: *
Disallow: /s3cr3t/
``` 

Look what's disallowed, if we access this directory we find the solution.

## Level 4 &rarr; Level 5



