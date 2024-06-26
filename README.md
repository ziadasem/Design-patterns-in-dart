# Exploring Design Patterns in Dart 
Design patterns in Dart are explored in this article, providing insights into their importance, implementation, and utility. A selection of patterns from the three main categories (creational, structural, and behavioral), is focused on, with examples in Dart demonstrating their usage. References from authoritative sources such as Refactoring Guru and tutorials from TutorialsPoint are used.

## 1. Introduction
In software engineering, a design pattern is a general repeatable solution to a commonly occurring problem in software design. A design pattern isn't a finished design that can be transformed directly into code. It is a description or template for how to solve a problem that can be used in many different situations.

### Patterns vs algorithms:

Patterns are often confused with algorithms because both concepts describe typical solutions to some known problems. 

While an algorithm always defines a **clear set of actions that can achieve some goal**, a pattern is **a more high-level description of a solution**. The code of the same pattern applied to two different programs may be different.

for example, take the `binary search algorithm` and `Factory pattern` as an example. This algorithm implies certain clear steps to implement. for example, the List should sorted, three checks should be made, and the update of index.

while for Factory pattern it is stated as to create an object without exposing the creation logic to the client and refer to newly created object using a common interface. without providing concise steps or methods, as it is free to the developer.

So, to learn a design pattern, a different mindset is required than the mindset of using an algorithm.

### Elements of a Design Pattern 
![mindmap](https://i.ibb.co/wh8kF6G/ele.jpg)

### Classification of design patterns
```
											Classification of design patterns
												    |
												    |
		___________________________________________________________________________________________________________________________________________
		↓										    ↓							  ↓
		↓									            ↓							  ↓
	Creational patterns								     Structural patterns				Behavioral patterns
provide object creation mechanisms that 					Patterns that describes how to assemble 		take care of effective communication and the 
increase flexibility and reuse of existing					objects and classes together as a larger   		assignment of responsibilities between objects
code.											flexible structure
(Factory Method, Abstract Factory , Singleton)  				(Adapter Method, Bridge method , Facade)		(Command Method, Strategy, Observer)

```
## 2. Creational Design Patterns

### 2.1 Factory Method Design pattern
* separates the object creation logic from the client code
* a factory can  return different objects belonging to the same super-class, with the same method

when you have buttons class; one for Windows, one for MacOS, one for Web, one for Android, and one for IOS. you can create as many classes as you want to instantiate, **OR, you can use a factory design pattern**.

**Problem**:
suppose we write a cross-platform program that renders a button according to the running OS, and we have this piece of code:
```dart
return Scaffold (
	body:
		isWeb ? WebButton()
		: isAndroid ? AndroidButton()
		: isMacOS ? MacButton() 
		: isWindows ? WindowsButton()
		: IosButton()
)
```
as you can see many ternary if-conditions are written in to decide which object to pass.

**The Solution**:
all of these objects are `Button`, so they can implement Button interface, and every subclass will override its methods 
```dart
class Button { //no interface in dart
	void onClick(){}
	void render(){}
}
```
and for the derived classes:
```dart
class WButton implements Button{
	@Override
	void draw(){ print("draw windows button");}

	@Override
	void onClick(){ print("draw windows button");}
}

class AButton implements Button{
	@Override
	void draw(){ print("draw Android button");}

	@Override
	void onClick(){ print("draw Android button");}
}

class WebButton implements Button{
	@Override
	void draw(){ print("draw web button");}

	@Override
	void onClick(){ print("draw web button");}
}
```

**Create button factory class**
After creating objects that implement the same interface, now we will create a Factory class, which is a class with the creation logic that returns the appropriate Button once.
```dart
class ButtonFactory {
	Button getButton(){ 
		if (isWeb){
			return WebButton();
		}else if (isAndroid){
			return AndroidButton();
		}....
}
```

now update the first code snippet to fix the issue:
```dart
return Scaffold (
	body:
		ButtonFactory.getButton(),
)
```
now we can add more buttons without affecting the client code.

### 2.2 Singleton Design pattern
creats one instance of object in the runtime, it is useful for classes that connects to database or classes that loads configuration
```dart
class Calender {
	 Calender._() {} //disable calling to the constructor by makeing the constructor private
	 static Calender _instance;

	 static getInstance() {
		if (_instance == null) {
			_instance =  Calender();
		}
		return _instance ;
	 }
}
```


## 3. Structural Design Patterns

### 3.1 Adapter Design Patterns

The adapter design pattern is a structural design that allows two objects with different interfaces to collaborate.

  ![block digaram for classes](https://i.ibb.co/5xz02K0/Capture.png)

We want the Media Player class to collaborate with Video Editor to take advantage of Video Player, but both of them are incompatible classes, hence we will build an ADAPTER.  
  
```dart
class  ImageViewer {
	void  showImage(){ }
}

class  VideoPlayer {
	void  showVideo(){}
}


class  MediaPlayer  implements  ImageViewer {

	@override
	void  showImage(){
		print("Image");
	}
}

class  VideoEditor  implements  VideoPlayer {
	@override
	void  showVideo(){
		print("video");
	}
}

void  main(){

	MediaPlayer  mediaPlayer  =  MediaPlayer();
	mediaPlayer.showVideo(); //we want to add this to media player,
	//and media player isn't a type of video player
	//hence we will use an adapter/wrapper
}
```
  

The adapter:

 ```dart  

class  MediaPlayerAdapter  implements  VideoPlayer{

	ImageViewer  imageViewer ;
	MediaPlayerAdapter(this.imageViewer);

	@override
	void  showVideo(){
		//show Video logic
	}
}

 
void  main(){
	MediaPlayer  mediaPlayer  =  MediaPlayer();
	//VideoPlayer videoPlayer = VideoPlayer() ;
	MediaPlayerAdapter  adapter  =  MediaPlayerAdapter(mediaPlayer);
	adapter.showVideo();
	adapter.imageViewer.showImage();
}
```

Now they can be interfaced

  

### 3.2 Decorative Design Patterns


A decorator is a structural design pattern that lets you attach new behaviors to objects by placing these objects inside special wrapper objects that contain the behaviors.

As a structural pattern, this pattern allows to attachment of new functionality to the class without altering the base one or creating a new one.


**Example**:

We have a file reader, text reader, and image reader classes all of them are sub-classes (variants) from the base class Reader. For every reader, it has two variants which are the Encrypted reader and the translated reader. I.e. (i.e. encrypted file reader, translated text reader, …).

How many classes do we want to create?

We need to create 3(file, text, and image) * 2(encrypted and translated) = 6 classes. Now suppose we have a new variant and two new readers how many new classes we should make?

(3 + 2 ) * (2+1) = 18 total, 18-6 = 12!


For just adding three new types we will write a new 12 classes. This will ruin the structure of the code. The solution is to use the decorator design pattern.

The fact that all readers belong to the same interface and the result sub-class is just a reader with added functionality. we can make a class that takes a reader as input and attaches to it a functionality then outputs it.

![block diagram](https://i.ibb.co/vDNjd5D/Capture.png)

Here an object of File Reader is passed to an object of Encryption Class and outputs Encrypted Files Reader. Note the following Figure six objects are created from five objects only if we want a new variant we only create a new Decorator object instead of writing three objects

![block diagram](https://i.ibb.co/5xsp4V8/Capture.png)

```dart
class  Reader {
	String  read(){return  "N/A";}
	void  write(String  data){}
}


class  FileReader  implements  Reader {
	String  value  =  "" ;  
	
	@override
	String  read() {
		return  "$value File" ;
	}


	@override
	void  write(String  value) {
		this.value  =  value ;
	}
}

 
class  TextReader  implements  Reader {

	String  value  =  "" ;
	
	@override
	String  read() {
		return  "$value Text" ;
	}

	@override
	void  write(String  value) {
		this.value  =  value ;
	}

}

class  ImageReader  implements  Reader {

	String  value  =  "" ;
	
	@override
	String  read() {
		return  "$value image" ;
	}

	@override
	void  write(String  value) {
		this.value  =  value ;
	}

}

class  EncryptedReaderDecorator  implements  Reader{

	late  Reader  wrapee ;
	EncryptedReaderDecorator(this.wrapee);

	@override
	String  read() {
		return  _decode(wrapee.read()) ;
	}

	@override
	void  write(String  value) {
		wrapee.write(_encode(value));
	}
  
	String  _decode(String  value){
		return  value.replaceAll("encode", "decoded") ; //for demo only
	}

	String  _encode(String  value){
		return  "${value}encode" ; //for demo only
	}
}

class  TranslatedReaderDecorator  implements  Reader{

	late  Reader  wrapee ;
	TranslatedReaderDecorator(this.wrapee);

	@override
	String  read() {
		return  _translate(wrapee.read()) ;
	}

	@override
	void  write(String  value) {
		wrapee.write(_translate(value));
	}

	String  _translate(String  value){
		return  value.replaceAll("a", "أ") ; //for demo only
	}
}

void  main(){

	EncryptedReaderDecorator  encryptedFile  = EncryptedReaderDecorator(FileReader());
	EncryptedReaderDecorator  encryptedText  =  EncryptedReaderDecorator(TextReader());
	EncryptedReaderDecorator  encryptedImage  =  EncryptedReaderDecorator(ImageReader());

	TranslatedReaderDecorator  translatedFile  =  TranslatedReaderDecorator(FileReader());
	TranslatedReaderDecorator  translatedText  =  TranslatedReaderDecorator(TextReader());
	TranslatedReaderDecorator  translatedImage  =  TranslatedReaderDecorator(ImageReader());

	encryptedFile.write("Test ");
	encryptedText.write("Test ");
	encryptedImage.write("Test ");

	print(encryptedFile.read());
	print(encryptedText.read());
	print(encryptedImage.read());
	
	translatedFile.write("Aaa ");
	translatedText.write("Aaa ");
	translatedImage.write("Aaa ");

	print(translatedFile.read());
	print(translatedText.read());
	print(translatedImage.read());
	
	//Try to add new Varient
}
```
  

  

  

Output:
```
Test decoded File

Test decoded Text

Test decoded image

Aaa File

Aaa Text

Aأأ imaأge
```
  
  

### 3.3 Facade Design Patterns

The facade design pattern is to use a class that hides the complexities of initialization and features of different classes and provides a limited interface with limited functionality (a bit required) compared with the original one.

  ```dart
//Video Conversion is done through the following steps:
// 1- read the input file using suitable codec
// 2- play the input video
// 3- read the video with the desired codec
// 4- fix the audio
// 5 - return the video file

class  VideoFile {
	late  String  name ;
	late  String  codec ;
	VideoFile(this.name, this.codec);
}

class  VideoCodec {
	late  String  type ;
}

class  Mp4Codec  implements  VideoCodec {
	@override
	String  type  =  "Mp4";
}

  

class  AVICodec  implements  VideoCodec {
	@override
	String  type  =  "avi";
}  

//use factory design pattern
class  CodecFactoty {
	static  VideoCodec  createCodec(String  codecType){

		if (codecType  ==  "mp4"){
			return  Mp4Codec();
		}else  if (codecType  ==  "avi"){
			return  AVICodec();
		}else{
			throw  UnsupportedError("no video codec is avaliable for this type ${codecType}");
		}
	}
}

//class that do some complex stuff
class  BitRateReader{

	static  VideoFile  readVideo(VideoFile  inputFile, VideoCodec  inputCodec){
	print("reading file ${inputFile.name} with input codec ${inputCodec.type}" );
	return  inputFile;
	}
	
	static  VideoFile  writeVideo(VideoFile  inputFile, VideoCodec  outputCoding){
		print("writing file ${inputFile.name} with output codec ${outputCoding.type}" );
		return  inputFile ;
	}
}

//class that do some complex stuff
class  AudioMixer {

	static  VideoFile  fix(VideoFile  result){
		print("AudioMixer: fixing audio...");
		return  result;
	}

}

//hide all complexities of video conversion

class  VideoConversionFacade {
	VideoFile  convertFile(VideoFile  file, String  output){
		
		VideoCodec  inputCodec  =  CodecFactoty.createCodec(file.codec);
		VideoCodec  outputCodec  =  CodecFactoty.createCodec(output);
		VideoFile  intermediateVideo  =  BitRateReader.readVideo(file, inputCodec);
		VideoFile  outputVideoFile  =  BitRateReader.writeVideo(intermediateVideo, outputCodec);
		VideoFile  result  =  AudioMixer.fix(outputVideoFile);
		return  result;

	}
}

void  main(){

//here we will use only two functions that hides all of these complexities
VideoFile  inputFile  =  VideoFile("file", "mp4");

//with facade
VideoFile  convertedFile  =  VideoConversionFacade().convertFile(inputFile, "avi");

//without Facade
VideoCodec  inputCodec  =  CodecFactoty.createCodec(inputFile.codec);
VideoCodec  outputCodec  =  CodecFactoty.createCodec("avi");
VideoFile  intermediateVideo  =  BitRateReader.readVideo(convertedFile, inputCodec);
VideoFile  outputVideoFile  =  BitRateReader.writeVideo(intermediateVideo, outputCodec);
VideoFile  result  =  AudioMixer.fix(outputVideoFile);

// the client should know all of these step and use them in correct order
}
```
  

## 4. Behavioral Design Patterns

Behavioral design patterns concern algorithms and assigning responsibilities between objects. Unlike structural patterns, the Behavioral pattern doesn’t group many objects into a single one it just defines the rules of communication between objects.

### 4.1 Observer Design Patterns

  

A behavioral pattern that is used when some objects need to be updated when other objects are updated (a many-to-many relationship), it is mainly used in GUI applications like setState in StatefulWidget in Flutter framework.

  

There are two types of objects in the Observer mechanism, Publisher and subscriber; a publisher is the object that notifies its subscribers when its state is changed.

  

In this example, a many-to-many relationship between Publishers and Subscribers is used.

![](https://lh7-us.googleusercontent.com/docsz/AD_4nXckR4Pi4ErZGlnDLvBhQ3XU9XHYMxZDBgYr43UdNpS3Jj5jX4SpkyyIKw_CY7TcxBBND7HB-rFL6J7DXxgJQbHuwRRupng9wueJC-XFiN_CKhDjOFPBywC1UItfL2ooCfLRwopOr0tos-qTusUCwRDvt62G?key=ez7LbFDoMbwacBlAAOF7Qg)

  
**For publishers**:

A publisher should maintain a list of subscribers and a function that calls their update function (of Subscriber) when the client needs to do so. So for publisher interface should contain a subscriber list and notify listeners function and the attach observer function.


The implementation for the previous functions is the same for every sub-class so I removed the implementation to a separate concrete class and made every new publisher extend it.
  
```dart
//publishers
import  'subscribers.dart';

abstract  class  PublisherInterface {
	abstract  List<SubscriberInterface>  subscribers ;
	void  attachListener(SubscriberInterface  subscriber);
	void  notifyListeners(var  val);
}

//we can split the publisher logic to concrete abstract class and
//allow every subclass to extend its logic

class  Publisher  implements  PublisherInterface {
	@override
	List<SubscriberInterface>  subscribers  = [];

	@override
	void  attachListener(SubscriberInterface  subscriber) {
		subscribers.add(subscriber);
	}

	@override
	void  notifyListeners(var  value) {
		for (var  subscriber  in  subscribers) {
			subscriber.update(value);
		}
	}
}

class  ConcretePublisher  extends  Publisher {
	@override
	List<SubscriberInterface>  subscribers  = [];
	var  watchedVariableInt  =  10 ;
	var  watchedVariableString  =  "Value" ;
	
	void  updateIntState(var  _watchedVariable){
		watchedVariableInt  =  _watchedVariable;
		notifyListeners(_watchedVariable);
	}

	void  updateStringState(var  _watchedVariable){
		watchedVariableString  =  _watchedVariable;
		notifyListeners(_watchedVariable);
	}

}
```

**For Subscribers**:

Every subscriber should have a different implementation in case of notification that a state is changed so a subscriber interface should contain an update function.

```dart
//subscribers
import  'publishers.dart';

abstract  class  SubscriberInterface {
	void  update(var  val);
}

class  Subscriber  implements  SubscriberInterface {

	List<Publisher>  publishers  = [];
	@override
	void  update(var  newValue) {
		print("the new value is ${newValue}");
	}

	void  registerToPublisher(Publisher  publisher) {
		publisher.attachListener(this);
	}
}
```
  
**For Client Code**:
I used many publishers with many subscribers

```dart
//Observer Example
import  'publishers.dart';
import  'subscribers.dart';

void  main(){
	ConcretePublisher  publisher1  =  ConcretePublisher();
	ConcretePublisher  publisher2  =  ConcretePublisher();

	Subscriber  subscriber1  =  Subscriber();
	Subscriber  subscriber2  =  Subscriber();
	Subscriber  subscriber3  =  Subscriber();
  
	subscriber1.registerToPublisher(publisher1);
	subscriber1.registerToPublisher(publisher2);
	
	subscriber2.registerToPublisher(publisher1);
	subscriber3.registerToPublisher(publisher2);

	publisher1.updateIntState(30);
	publisher2.updateStringState("Some String");
}
```
  
```
the new value is 30
the new value is 30
the new value is Some String
the new value is Some String
```
  

### 4.2 Strategy Design Patterns

When you have a family of algorithms for a certain task that changes by changing the environment (e.g. picking an image from Android isn’t like picking an image from Windows in Flutter) you can make a bulk of switch cases that degrade the code for every new update. Also if you are not sure which algorithm should be used in runtime (e.g. imagine you have a calculator application and you want the user to select a certain operation every operation has its own algorithm) you can use a strategy design pattern.

The strategy design pattern suggests splitting the algorithms into separate classes and passing objects from these classes into the main class.

![block diagram](https://i.ibb.co/T88y6tc/Capture.png)  


In this example: in flutter, the File Picker widget returns different file objects for each platform and every object requires a different handling algorithm. Here we will demonstrate only the concept without the real implementation of handling.

We use a factory class as an application for the multiple classes and as another example for this design pattern.

  

**Image File Classes and its factory**:
```dart
//use factory design pattern for handling different Image Files classes on different OSs
abstract  class  ImageFile{
	abstract  String  type ;
}

class  AndroidImageFile  implements  ImageFile{
	@override
	String  type  =  "android";
}

class  IosImageFile  implements  ImageFile{
	@override
	String  type  =  "ios";
}

class  WindowsImageFile  implements  ImageFile{
	@override
	String  type  =  "windows";
}

class  MacImageFile  implements  ImageFile{
	@override
	String  type  =  "mac";
}

class  WebImageFile  implements  ImageFile{
	@override
	String  type  =  "web";
}

class  ImageFileFactory {

	ImageFile  createImageFile(String  platform){
		switch (platform) {
			case  "android":
				return  AndroidImageFile();
			case  "ios":
				return  IosImageFile();
			case  "web":
				return  WebImageFile();
			case  "windows":
				return  WindowsImageFile();
			case  "mac":
				return  MacImageFile();
			default:
				throw  Exception("platform isn't supported");
		}

	}
}
```

**ImagePicker Strategy example and Client Code**:
```dart
//demo for picking image from different OS
//Android, ios, windows, mac, linux, web
import  'image_file_factory.dart';

abstract  class  PickImageStrategy {
	ImageFile  pickImage();
}

//implement different algorithm for each class
class  PickAndroidImage  implements  PickImageStrategy {

	@override
	ImageFile  pickImage() {
		print("android image");
		return  AndroidImageFile();
	}
}

class  PickIosImage  implements  PickImageStrategy {

	@override
	ImageFile  pickImage() {
		print("ios image");
		return  IosImageFile();
	}

}

class  PickWindowsImage  implements  PickImageStrategy {

	@override
	ImageFile  pickImage() {
		print("windows image");
		return  WindowsImageFile();
	}
}

class  PickMacImage  implements  PickImageStrategy {

	@override
	ImageFile  pickImage() {
		print("mac image");
		return  MacImageFile();
	}

}

class  PickWebImage  implements  PickImageStrategy {

	@override
	ImageFile  pickImage() {
		print("web image");
		return  WebImageFile();
	}

}

class  ImagePicker {

	ImageFile  pickImage(PickImageStrategy  strategy) {
		return  strategy.pickImage();
	}

}

void  main(){
	ImagePicker  imagePicker  =  ImagePicker();
	imagePicker.pickImage(PickAndroidImage());
}
```
  
```
android image
```
