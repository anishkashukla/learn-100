### What is directory traversal?
````
Directory traversal (also known as file path traversal) is a web security vulnerability that allows an attacker to read arbitrary files on the server that is running an application. 
This might include application code and data, credentials for back-end systems,and sensitive operating system files. 
In some cases, an attacker might be able to write to arbitrary files on the server, allowing them to modify application data or behavior, and ultimately take full control of the server.
````
### Reading arbitrary files via directory traversal
````
Consider a shopping application that displays images of items for sale. Images are loaded via some HTML like the following:

<img src="/loadImage?filename=218.png">

The loadImage URL takes a filename parameter and returns the contents of the specified file. The image files themselves are stored on disk in the location /var/www/images/. 
To return an image, the application appends the requested filename to this base directory and uses a filesystem API to read the contents of the file. 
In the above case, the application reads from the following file path:

/var/www/images/218.png

The application implements no defenses against directory traversal attacks, so an attacker can request the following URL to retrieve an arbitrary file from the server's filesystem:

https://insecure-website.com/loadImage?filename=../../../etc/passwd

This causes the application to read from the following file path:

/var/www/images/../../../etc/passwd

The sequence ../ is valid within a file path, and means to step up one level in the directory structure. The three consecutive ../ sequences step up from /var/www/images/ to the filesystem root, and so the file that is actually read is:

/etc/passwd

On Unix-based operating systems, this is a standard file containing details of the users that are registered on the server.

On Windows, both ../ and ..\ are valid directory traversal sequences, and an equivalent attack to retrieve a standard operating system file would be:

https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
````