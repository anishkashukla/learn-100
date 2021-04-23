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