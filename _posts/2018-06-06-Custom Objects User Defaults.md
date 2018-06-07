---
layout: post
title: "Save Custom Objects on UserDefaults"
featured-img: Code/ac3
categories: [Guides]
---

# Save Custom Objects to User Defaults

### User Defaults
*An interface to the user’s defaults database, where you store key-value pairs persistently across launches of your app.* [Apple Developer Documentation](https://developer.apple.com/documentation/foundation/userdefaults)

Its basically a dictionary with persistency, where you can save information under a key, and get it back later. This information will survive even if you shut down your app, so the User Defaults can be a pretty interesting way to save information.

### Custom Objects
Any class created by you basically.
Let's make an example using 3 attributes, a String, and Integer and an Image.

```swift
import UIKit

class CustomObject {
	var name: String = ""
	var code: Int = ""
	var image: UIImage = UIImage(named: "test")
	
	init(name: String, code: Int, image: UIImage) {
		self.name = name
		self.code = code
		self.image = image
	}
}
```
##### Here we provided default values for the attributes, so we can also use the default builder for the class

Now, for some reason, we want to save an instance of this class (a custom object) into the User Defaults.

If you try to do that by just setting the object for a key on the UserDefaults, you will get an error:

```swift
UserDefaults.standard.set(CustomObject(), forKey: <anyKey>)
```

What you need to do, is make your class inherit from NSObject, and also make it a NSCoding class.

```swift
class CustomObject: NSObject, NSCoding { ...
```

This, will allow the class to be "encoded", and transformed into and object that can be saved on the UserDefaults. But its not just that, now your class need to conform to those protocols. We are going to need to implement the **encode** and the **convenience init** methods.

### Encode and Convenience Init

The encode, will transform the customObject into a format that the UserDefaults can save, its basically a data value, stored under a key = a dictionary.

```swift
func encode(with aCoder: NSCoder) {
    aCoder.encode(name, forKey: "name")
    aCoder.encode(code, forKey: "code")
    aCoder.encode(UIImageJPEGRepresentation(image, 0.5), forKey: "image")
}
```
The UIImage is not a directly encodable object, what you need to do is convert it into Data, for that we used the ```UIImageJPEGRepresentation``` method, where you send the UIImage, with a compression factor, and you'll get a JPEG representation of the same image, which can be encoded.

And then, we implement the convenience init, which is the method that will be called to restore the object from the information stored on the User Defaults, it gets all the *dictionary* retrieved, and use the keys to get the values back. Afetr that, it calls the builder of the class with the retrieved values:

```swift
required convenience init?(coder aDecoder: NSCoder) {
    let name = aDecoder.decodeObject(forKey: "name") as! String
    let name = aDecoder.decodeObject(forKey: "code") as! Int
    let image = UIImage(data: aDecoder.decodeObject(forKey: "image") as! Data)
    self.init(name: name, code: code, image: image!)
}
```
Great! Now we can save our custom object into the UserDefaults.

### Save and Retrieve from UserDefaults

But still, we cannot save the object *directly*, we can only save the *encoded object*, so we need to encode it before we can save.

Lets make a method to save the Object into the UserDefaults:


```swift
func saveCustomObject(_ customObject: CustomObject) {
    let encodedObject: Data = NSKeyedArchiver.archivedData(withRootObject: customObject)
    UserDefaults.standatd.set(encodedObject, forKey: "customObjectKey")
}
```

And another method to get it back:

```swift
class func getDecodedCustomObject() -> CustomObject {
	let data = UserDefaults.standard.data(forKey: "customObjectKey")
    let decodedObject = NSKeyedUnarchiver.unarchiveObject(with: data) as! CustomObject
    return decodedObject
}
```

Aaaaaand it's done! You can now save your custom object into the UserDefaults!

## Review

It may look complicated, but it's actually very simple to save a custom object into the UserDefaults:

- Make your custom object inherit from NSObject and implement NSCoding protocol
- Implement the **encode** and **convenience init** methods
     - Encode will turn your object into Data, which is "savable" on the UserDefaults
     - the convenience init will restore the Object from the Data information 
- Encode the object, and save as *Data* on UserDefaults
- Retrieve the *Data* from the UserDefaults, and decode it to make the object back

Here's the full code of our **CustomObject** class:

```swift
class CustomObject {
	var name: String = ""
	var code: Int = ""
	var image: UIImage = UIImage(named: "test")
	
	init(name: String, code: Int, image: UIImage) {
		self.name = name
		self.code = code
		self.image = image
	}
	
	/// Here, we encode all the attributes
	/// transform into a data information, saved under a Key
	func encode(with aCoder: NSCoder) {
	    aCoder.encode(name, forKey: "name")
	    aCoder.encode(code, forKey: "code")
	    aCoder.encode(UIImageJPEGRepresentation(image, 0.5), forKey: "image")
	}
	
	/// Retrieve the Data form and turn into a CustomObject
	required convenience init?(coder aDecoder: NSCoder) {
	    let name = aDecoder.decodeObject(forKey: "name") as! String
	    let name = aDecoder.decodeObject(forKey: "code") as! Int
	    let image = UIImage(data: aDecoder.decodeObject(forKey: "image") as! Data)
	    self.init(name: name, code: code, image: image!)
	}
}
```


> If you are saving big objects or informations into the UserDefaults, which include images, large texts or complex structures, maybe you should look for a Database alternative, instead of storing everything inside the UserDefaults. Iwould recomend taking a look into the [Realm Database](https://realm.io), which is very simple, fast, and really useful.

---
Hope you liked it! 🤖
###### Cover image <a href='https://www.freepik.com/free-vector/memphis-pattern_1177561.htm'>designed by Freepik</a>

###### [UserDefaults • Custom • Object • Save • Encode • Decode • User • Defaults • Swift • iOS • Xcode • Swift4]