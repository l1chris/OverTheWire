## Level 0 &rarr; Level 1

This is quite simple. After logging in we are presented with a message saying "You can find the password for the next level on this page". 
If we open the browser's developer tools and take a look at the source code we can find the solution in an HTML comment inside the div tag with the id "content".

## Level 1 &rarr; Level 2

The only real difference to the previous level is that rightclicking is blocked. We can still open the developer tools with the corresping key combination and can find the solution again in the source code.

## Level 2 &rarr; Level 3

This time we are presented with a message saying there is nothing on this page. But if we look at the source code we can find an HTML img tag. If we follow the source we find an image with just a pixel in it.
At first I thought that the solution might be hidden somewhere inside the PNG file but this is not the case. Instead, we have to take a look at the image path:

> `http://natas2.natas.labs.overthewire.org/files/pixel.png`

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

Here we are presented with a message saying that access is disallowed and that authorized requests should come only from `http://natas5.natas.labs.overthewire.org/`.
If we click the refresh button on the page it says that we are accessing from `http://natas4.natas.labs.overthewire.org/index.php`, our current address.
So what we apparently have to do is to somehow make the server belief we are accessing from natas5. 

Lets take a look at what request headers are set in the HTTP protocol. 
If we click on the refresh button and open the network tab in the developer tools, we can see that the Referer header is set to our current address.
In the Mozilla Developer Documentation this header is described as follows:

``` 
The Referer HTTP request header contains the absolute or partial address from which a resource has been requested.
```

It looks like if we are able to set this field to the requested domain, we should get access.

To achieve this, we can use Burp Suite. If we open the Intercept-tab in the Proxy tool we can open Burp's Browser and open the website there. 
If we then start to intercept and click the Refresh button on the page, we are able to modify the Referer header. 
After changing this header to the requested domain, we can click on Forward. 
Ta-da! The website now shows us the solution for this level.

## Level 5 &rarr; Level 6

This is similar to the previous level. First we are presented with a message saying that we are not logged in. 
If we look at the HTTP headers we can see a Cookie with `loggedin=0`.
With Burp's Proxy we can intercept this packet and set this field to `loggedin=1`.
When we then forward the modified packet we are presented with the solution.

## Level 6 &rarr; Level 7

If we look at the source code provided in the link we find a PHP script which includes a file from "includes/secret.inc". 
This file creates a `$secret` variable and assigns a value to it. 
As the PHP script compares the user input to this value, we can write it in the input field on the website and submit it to get the solution for this level.

## Level 7 &rarr; Level 8

In the source code we can find a hint saying that the password is located at `/etc/natas_webpass/natas8`.
If we click on the About link, we see that the URL changes to `index.php?page=about`. 
We can play around with the query parameter `page=` and set it to other values.
Setting it to `page=error`, for example, results in an error displayed on the page stating that there is no such file or directory. 

As we know from the hint that there is file under `/etc/natas_webpass/natas8`, we can set query parameter to `page=/etc/natas_webpass/natas8`, presenting us the solution.

## Level 8 &rarr; Level 9

This level again provides us with a link to some source code, which again contains a PHP script. 
At the start of the script we find a variable `$encodedSecret` with some value assigned to it. 

Down below in the script we see that the user input is passed to function `encodeSecret`. 
This function takes the input and first encodes it to base64, reverses the result and finally performs a binary to hex conversion.
To get the correct input, we have to take the value assigned to `$encodedSecret` and reverse this operations.

There are multiple ways to achieve this, we can start by saving the encoded secret in a file named input.txt.
To reverse the binary to hex conversion, we can use the *xxd* command in the shell as follows:

```
xxd -r -p input.txt output.bin
```

Here, the *-r* option indicates that we want to revert a hex to binary, *-d* is set to give us a plain string.
We also specifiy the result to be saved in a file called output.bin.
Next we can reverse the result with the *rev* command and store it in a file called unreverse.txt:

```
cat output.bin | rev >> unreverse.txt
```

Now we have a base64 encoded string which we want to decode:

```
cat unreverse.txt | base64 --decode >> secret.txt
```

After this step, the secret is saved in a file named secret.txt
When we input the secret in the website and submit it, we are presented with the solution.















