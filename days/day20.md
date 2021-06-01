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
### Common obstacles to exploiting file path traversal vulnerabilities
````
Many applications that place user input into file paths implement some kind of defense against path traversal attacks, and these can often be circumvented.

If an application strips or blocks directory traversal sequences from the user-supplied filename, then it might be possible to bypass the defense using a variety of techniques.

You might be able to use an absolute path from the filesystem root, such as filename=/etc/passwd, to directly reference a file without using any traversal sequences.

You might be able to use nested traversal sequences, such as ....// or ....\/, which will revert to simple traversal sequences when the inner sequence is stripped.

You might be able to use various non-standard encodings, such as ..%c0%af or ..%252f, to bypass the input filter.

If an application requires that the user-supplied filename must start with the expected base folder, such as /var/www/images, then it might be possible to include the required base folder followed by suitable traversal sequences. For example:

filename=/var/www/images/../../../etc/passwd

If an application requires that the user-supplied filename must end with an expected file extension, such as .png, then it might be possible to use a null byte to effectively terminate the file path before the required extension. For example:

filename=../../../etc/passwd%00.pngs
````