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