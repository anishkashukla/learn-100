### What is serialization?
````
Serialization is the process of converting complex data structures, such as objects and their fields, into a "flatter" format that can be sent and received as a sequential stream of bytes. Serializing data makes it much simpler to:

Write complex data to inter-process memory, a file, or a database
Send complex data, for example, over a network, between different components of an application, or in an API call
Crucially, when serializing an object, its state is also persisted. In other words, the object's attributes are preserved, along with their assigned values.
````
### Serialization vs deserialization
````
Deserialization is the process of restoring this byte stream to a fully functional replica of the original object, in the exact state as when it was serialized. 
The website's logic can then interact with this deserialized object, just like it would with any other object.
Many programming languages offer native support for serialization. Exactly how objects are serialized depends on the language. 
Some languages serialize objects into binary formats, whereas others use different string formats, with varying degrees of human readability. 
Note that all of the original object's attributes are stored in the serialized data stream, including any private fields. 
To prevent a field from being serialized, it must be explicitly marked as "transient" in the class declaration.

Be aware that when working with different programming languages, serialization may be referred to as marshalling (Ruby) or pickling (Python). These terms are synonymous with "serialization" in this context.
````
### What is insecure deserialization?
````
Insecure deserialization is when user-controllable data is deserialized by a website. This potentially enables an attacker to manipulate serialized objects in order to pass harmful data into the application code.

It is even possible to replace a serialized object with an object of an entirely different class. Alarmingly, objects of any class that is available to the website will be deserialized and instantiated, regardless of which class was expected. 
For this reason, insecure deserialization is sometimes known as an "object injection" vulnerability.

An object of an unexpected class might cause an exception. By this time, however, the damage may already be done. 
Many deserialization-based attacks are completed before deserialization is finished. This means that the deserialization process itself can initiate an attack, even if the website's own functionality does not directly interact with the malicious object. 
For this reason, websites whose logic is based on strongly typed languages can also be vulnerable to these techniques.
````
### How do insecure deserialization vulnerabilities arise ?
````
Insecure deserialization typically arises because there is a general lack of understanding of how dangerous deserializing user-controllable data can be. 
Ideally, user input should never be deserialized at all.

However, sometimes website owners think they are safe because they implement some form of additional check on the deserialized data. 
This approach is often ineffective because it is virtually impossible to implement validation or sanitization to account for every eventuality. 
These checks are also fundamentally flawed as they rely on checking the data after it has been deserialized, which in many cases will be too late to prevent the attack.

Vulnerabilities may also arise because deserialized objects are often assumed to be trustworthy. 
Especially when using languages with a binary serialization format, developers might think that users cannot read or manipulate the data effectively. 
However, while it may require more effort, it is just as possible for an attacker to exploit binary serialized objects as it is to exploit string-based formats.

Deserialization-based attacks are also made possible due to the number of dependencies that exist in modern websites. 
A typical site might implement many different libraries, which each have their own dependencies as well. 
This creates a massive pool of classes and methods that is difficult to manage securely. As an attacker can create instances of any of these classes, it is hard to predict which methods can be invoked on the malicious data. 
This is especially true if an attacker is able to chain together a long series of unexpected method invocations, passing data into a sink that is completely unrelated to the initial source. 
It is, therefore, almost impossible to anticipate the flow of malicious data and plug every potential hole.

In short, it can be argued that it is not possible to securely deserialize untrusted input.
````
### What is the impact of insecure deserialization?
````
The impact of insecure deserialization can be very severe because it provides an entry point to a massively increased attack surface. 
It allows an attacker to reuse existing application code in harmful ways, resulting in numerous other vulnerabilities, often remote code execution.

Even in cases where remote code execution is not possible, insecure deserialization can lead to privilege escalation, arbitrary file access, and denial-of-service attacks.
````
### Exploiting insecure deserialization vulnerabilities

### How to identify insecure deserialization
````
Identifying insecure deserialization is relatively simple regardless of whether you are whitebox or blackbox testing.

During auditing, you should look at all data being passed into the website and try to identify anything that looks like serialized data. Serialized data can be identified relatively easily if you know the format that different languages use. In this section, we'll show examples from both PHP and Java serialization. Once you identify serialized data, you can test whether you are able to control it.
````
### PHP serialization format
````
PHP uses a mostly human-readable string format, with letters representing the data type and numbers representing the length of each entry. For example, consider a User object with the attributes:

$user->name = "carlos";
$user->isLoggedIn = true;

When serialized, this object may look something like this:

O:4:"User":2:{s:4:"name":s:6:"carlos"; s:10:"isLoggedIn":b:1;}

This can be interpreted as follows:

O:4:"User" - An object with the 4-character class name "User"
2 - the object has 2 attributes
s:4:"name" - The key of the first attribute is the 4-character string "name"
s:6:"carlos" - The value of the first attribute is the 6-character string "carlos"
s:10:"isLoggedIn" - The key of the second attribute is the 10-character string "isLoggedIn"
b:1 - The value of the second attribute is the boolean value true
The native methods for PHP serialization are serialize() and unserialize(). If you have source code access, you should start by looking for unserialize() anywhere in the code and investigating further.
````
### Java serialization format
````
Some languages, such as Java, use binary serialization formats. This is more difficult to read, but you can still identify serialized data if you know how to recognize a few tell-tale signs. For example, serialized Java objects always begin with the same bytes, which are encoded as ac ed in hexadecimal and rO0 in Base64.

Any class that implements the interface java.io.Serializable can be serialized and deserialized. If you have source code access, take note of any code that uses the readObject() method, which is used to read and deserialize data from an InputStream.
````
### Manipulating serialized objects
````
Exploiting some deserialization vulnerabilities can be as easy as changing an attribute in a serialized object. As the object state is persisted, you can study the serialized data to identify and edit interesting attribute values. 
You can then pass the malicious object into the website via its deserialization process. This is the initial step for a basic deserialization exploit.

Broadly speaking, there are two approaches you can take when manipulating serialized objects. 
You can either edit the object directly in its byte stream form, or you can write a short script in the corresponding language to create and serialize the new object yourself. 
The latter approach is often easier when working with binary serialization formats.
````
### Modifying object attributes
````
When tampering with the data, as long as the attacker preserves a valid serialized object, the deserialization process will create a server-side object with the modified attribute values.

As a simple example, consider a website that uses a serialized User object to store data about a user's session in a cookie. 
If an attacker spotted this serialized object in an HTTP request, they might decode it to find the following byte stream:

O:4:"User":2:{s:8:"username";s:6:"carlos";s:7:"isAdmin";b:0;}

The isAdmin attribute is an obvious point of interest. An attacker could simply change the boolean value of the attribute to 1 (true), re-encode the object, and overwrite their current cookie with this modified value. 
In isolation, this has no effect. However, let's say the website uses this cookie to check whether the current user has access to certain administrative functionality:

$user = unserialize($_COOKIE);
if ($user->isAdmin === true) {
// allow access to admin interface
}

This vulnerable code would instantiate a User object based on the data from the cookie, including the attacker-modified isAdmin attribute. 
At no point is the authenticity of the serialized object checked. This data is then passed into the conditional statement and, in this case, would allow for an easy privilege escalation.

This simple scenario is not common in the wild. However, editing an attribute value in this way demonstrates the first step towards accessing the massive amount of attack-surface exposed by insecure deserialization.
````
### Modifying data types
````
We've seen how you can modify attribute values in serialized objects, but it's also possible to supply unexpected data types.

PHP-based logic is particularly vulnerable to this kind of manipulation due to the behavior of its loose comparison operator (==) when comparing different data types. 
For example, if you perform a loose comparison between an integer and a string, PHP will attempt to convert the string to an integer, meaning that 5 == "5" evaluates to true.

Unusually, this also works for any alphanumeric string that starts with a number. In this case, PHP will effectively convert the entire string to an integer value based on the initial number. The rest of the string is ignored completely. 
Therefore, 5 == "5 of something" is in practice treated as 5 == 5.

This becomes even stranger when comparing a string the integer 0:

0 == "Example string" // true

Why? Because there is no number, that is, 0 numerals in the string. PHP treats this entire string as the integer 0.

Consider a case where this loose comparison operator is used in conjunction with user-controllable data from a deserialized object. This could potentially result in dangerous logic flaws.

$login = unserialize($_COOKIE)
if ($login['password'] == $password) {
// log in successfully
}

Let's say an attacker modified the password attribute so that it contained the integer 0 instead of the expected string. 
As long as the stored password does not start with a number, the condition would always return true, enabling an authentication bypass. 
Note that this is only possible because deserialization preserves the data type. If the code fetched the password from the request directly, the 0 would be converted to a string and the condition would evaluate to false.

Be aware that when modifying data types in any serialized object format, it is important to remember to update any type labels and length indicators in the serialized data too. Otherwise, the serialized object will be corrupted and will not be deserialized.

When working directly with binary formats, we recommend using the Hackvertor extension, available from the BApp store. With Hackvertor, you can modify the serialized data as a string, and it will automatically update the binary data, adjusting the offsets accordingly. This can save you a lot of manual effort.
````
### Using application functionality
````
As well as simply checking attribute values, a website's functionality might also perform dangerous operations on data from a deserialized object. 
In this case, you can use insecure deserialization to pass in unexpected data and leverage the related functionality to do damage.

For example, as part of a website's "Delete user" functionality, the user's profile picture is deleted by accessing the file path in the $user->image_location attribute. 
If this $user was created from a serialized object, an attacker could exploit this by passing in a modified object with the image_location set to an arbitrary file path. Deleting their own user account would then delete this arbitrary file as well.

This example relies on the attacker manually invoking the dangerous method via user-accessible functionality. 
However, insecure deserialization becomes much more interesting when you create exploits that pass data into dangerous methods automatically. This is enabled by the use of "magic methods".
````
### Magic methods
````
Magic methods are a special subset of methods that you do not have to explicitly invoke. Instead, they are invoked automatically whenever a particular event or scenario occurs. Magic methods are a common feature of object-oriented programming in various languages. They are sometimes indicated by prefixing or surrounding the method name with double-underscores.

Developers can add magic methods to a class in order to predetermine what code should be executed when the corresponding event or scenario occurs. 
Exactly when and why a magic method is invoked differs from method to method. One of the most common examples in PHP is __construct(), which is invoked whenever an object of the class is instantiated, similar to Python's __init__. 
Typically, constructor magic methods like this contain code to initialize the attributes of the instance. However, magic methods can be customized by developers to execute any code they want.

Magic methods are widely used and do not represent a vulnerability on their own. But they can become dangerous when the code that they execute handles attacker-controllable data, for example, from a deserialized object. 
This can be exploited by an attacker to automatically invoke methods on the deserialized data when the corresponding conditions are met.

Most importantly in this context, some languages have magic methods that are invoked automatically during the deserialization process. 
For example, PHP's unserialize() method looks for and invokes an object's __wakeup() magic method.

In Java deserialization, the same applies to the readObject() method, which essentially acts like a constructor for "re-initializing" a serialized object. 
The ObjectInputStream.readObject() method is used to read data from the initial byte stream. However, serializable classes can also declare their own readObject() methods as follows:

private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {...};

This allows the class to control the deserialization of its own fields more closely. Crucially, a readObject() method declared in exactly this way acts as a magic method that is invoked during deserialization.

You should pay close attention to any classes that contain these types of magic methods. They allow you to pass data from a serialized object into the website's code before the object is fully deserialized. This is the starting point for creating more advanced exploits.`
````
### Injecting arbitrary objects
````
As we've seen, it is occasionally possible to exploit insecure deserialization by simply editing the object supplied by the website. However, injecting arbitrary object types can open up many more possibilities.

In object-oriented programming, the methods available to an object are determined by its class. 
Therefore, if an attacker can manipulate which class of object is being passed in as serialized data, they can influence what code is executed after, and even during, deserialization.

Deserialization methods do not typically check what they are deserializing. This means that you can pass in objects of any serializable class that is available to the website, and the object will be deserialized. 
This effectively allows an attacker to create instances of arbitrary classes. The fact that this object is not of the expected class does not matter. 
The unexpected object type might cause an exception in the application logic, but the malicious object will already be instantiated by then.

If an attacker has access to the source code, they can study all of the available classes in detail. 
To construct a simple exploit, they would look for classes containing deserialization magic methods, then check whether any of them perform dangerous operations on controllable data. The attacker can then pass in a serialized object of this class to use its magic method for an exploit.

Classes containing these deserialization magic methods can also be used to initiate more complex attacks involving a long series of method invocations, known as a "gadget chain".
````
### Gadget chains
````
A "gadget" is a snippet of code that exists in the application that can help an attacker to achieve a particular goal. 
An individual gadget may not directly do anything harmful with user input. However, the attacker's goal might simply be to invoke a method that will pass their input into another gadget. 
By chaining multiple gadgets together in this way, an attacker can potentially pass their input into a dangerous "sink gadget", where it can cause maximum damage.

It is important to understand that, unlike some other types of exploit, a gadget chain is not a payload of chained methods constructed by the attacker. 
All of the code already exists on the website. The only thing the attacker controls is the data that is passed into the gadget chain. 
This is typically done using a magic method that is invoked during deserialization, sometimes known as a "kick-off gadget".

In the wild, many insecure deserialization vulnerabilities will only be exploitable through the use of gadget chains. 
This can sometimes be a simple one or two-step chain, but constructing high-severity attacks will likely require a more elaborate sequence of object instantiations and method invocations. 
Therefore, being able to construct gadget chains is one of the key aspects of successfully exploiting insecure deserialization.
````
### Working with pre-built gadget chains
````
Manually identifying gadget chains can be a fairly arduous process, and is almost impossible without source code access. Fortunately, there are a few options for working with pre-built gadget chains that you can try first.

There are several tools available that can help you construct gadget chains with minimal effort. These tools provide a range of pre-discovered gadget chains that have been exploited on other websites. 
Having identified an insecure deserialization vulnerability on your target site, you can use these tools to try and exploit it, even if you do not have access to the source code. This approach is made possible due to the widespread use of libraries that contain exploitable gadget chains. 
For example, if a gadget chain that relies on Java's Apache Commons Collections library can be exploited on one website, any other website that implements this library may also be exploitable using the same chain.

One such tool for Java deserialization exploits is "ysoserial". You simply specify a library that you think the target application is using, then provide a command that you want to try and execute. 
The tool creates an appropriate serialized object based on a gadget chain it knows for the given library. This still involves a certain amount of trial and error, but it is considerably less labor-intensive than constructing your own gadget chains manually.

Most languages that frequently suffer from insecure deserialization vulnerabilities have equivalent proof-of-concept tools. For example, for PHP-based sites you can use "PHP Generic Gadget Chains" (PHPGGC).

It is important to note that it is not the presence of a gadget chain in the website's code, or any of its libraries, that is responsible for the vulnerability. The vulnerability is the deserialization of user-controllable data - the gadget chain is just a means of manipulating the flow of this data once it has been injected. 
This also applies to various memory corruption vulnerabilities that rely on deserialization of untrusted data. Therefore, websites may still be vulnerable even if they do somehow manage to plug every possible gadget chain.
````
### Creating your own exploit
````
When off-the-shelf gadget chains and documented exploits are unsuccessful, you will need to create your own exploit.

To successfully build your own gadget chain, you will almost certainly need source code access. The first step is to study this source code to identify a class that contains a magic method that is invoked during deserialization. Assess the code that this magic method executes to see if it directly does anything dangerous with user-controllable attributes. This is always worth checking just in case.

If the magic method is not exploitable on its own, it can serve as your "kick-off gadget" for a gadget chain. Study any methods that the kick-off gadget invokes. Do any of these do something dangerous with data that you control? If not, take a closer look at each of the methods that they subsequently invoke, and so on.

Repeat this process, keeping track of which values you have access to, until you either reach a dead end or identify a dangerous sink gadget into which your controllable data is passed.

Once you've worked out how to successfully construct a gadget chain within the application code, the next step is to create a serialized object containing your payload. This is simply a case of studying the class declaration in the source code and creating a valid serialized object with the appropriate values required for your exploit. As we have seen in previous labs, this is relatively simple when working with string-based serialization formats.

Working with binary formats, such as when constructing a Java deserialization exploit, can be particularly cumbersome. When making minor changes to an existing object, you might be comfortable working directly with the bytes. However, when making more significant changes, such as passing in a completely new object, this quickly becomes impractical. It is often much simpler to write your own code in the target language in order to generate and serialize the data yourself.

When creating your own gadget chain, look out for opportunities to use this extra attack surface to trigger secondary vulnerabilities.

By carefully studying the source code, you can discover longer gadget chains that potentially allow you to construct high-severity attacks, often including remote code execution.
````
### PHAR deserialization
````
So far, we've looked primarily at exploiting deserialization vulnerabilities where the website explicitly deserializes user input. However, in PHP it is sometimes possible to exploit deserialization even if there is no obvious use of the unserialize() method.

PHP provides several URL-style wrappers that you can use for handling different protocols when accessing file paths. One of these is the phar:// wrapper, which provides a stream interface for accessing PHP Archive (.phar) files.

The PHP documentation reveals that PHAR manifest files contain serialized metadata. Crucially, if you perform any filesystem operations on a phar:// stream, this metadata is implicitly deserialized. This means that a phar:// stream can potentially be a vector for exploiting insecure deserialization, provided that you can pass this stream into a filesystem method.

In the case of obviously dangerous filesystem methods, such as include() or fopen(), websites are likely to have implemented counter-measures to reduce the potential for them to be used maliciously. However, methods such as file_exists(), which are not so overtly dangerous, may not be as well protected.

This technique also requires you to upload the PHAR to the server somehow. One approach is to use an image upload functionality, for example. If you are able to create a polyglot file, with a PHAR masquerading as a simple JPG, you can sometimes bypass the website's validation checks. If you can then force the website to load this polyglot "JPG" from a phar:// stream, any harmful data you inject via the PHAR metadata will be deserialized. As the file extension is not checked when PHP reads a stream, it does not matter that the file uses an image extension.

As long as the class of the object is supported by the website, both the __wakeup() and __destruct() magic methods can be invoked in this way, allowing you to potentially kick off a gadget chain using this technique.
````
### Exploiting deserialization using memory corruption
````
Even without the use of gadget chains, it is still possible to exploit insecure deserialization. If all else fails, there are often publicly documented memory corruption vulnerabilities that can be exploited via insecure deserialization. These typically lead to remote code execution.

Deserialization methods, such as PHP's unserialize() are rarely hardened against these kinds of attacks, and expose a huge amount of attack surface. This is not always considered a vulnerability in its own right because these methods are not intended to handle user-controllable input in the first place.
````
### How to prevent insecure deserialization vulnerabilities
````
Generally speaking, deserialization of user input should be avoided unless absolutely necessary. The high severity of exploits that it potentially enables, and the difficulty in protecting against them, outweigh the benefits in many cases.

If you do need to deserialize data from untrusted sources, incorporate robust measures to make sure that the data has not been tampered with. For example, you could implement a digital signature to check the integrity of the data. However, remember that any checks must take place before beginning the deserialization process. Otherwise, they are of little use.

If possible, you should avoid using generic deserialization features altogether. Serialized data from these methods contains all attributes of the original object, including private fields that potentially contain sensitive information. Instead, you could create your own class-specific serialization methods so that you can at least control which fields are exposed.

Finally, remember that the vulnerability is the deserialization of user input, not the presence of gadget chains that subsequently handle the data. Don't rely on trying to eliminate gadget chains that you identify during testing. It is impractical to try and plug them all due to the web of cross-library dependencies that almost certainly exist on your website. At any given time, publicly documented memory corruption exploits are also a factor, meaning that your application may be vulnerable regardless
````