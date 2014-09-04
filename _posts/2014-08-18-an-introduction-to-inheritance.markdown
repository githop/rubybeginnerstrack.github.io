---
layout: post
title:  An Introduction to Inheritance
date:   2014-08-18
---

Note: if you're more of a visual person, my annotated slides are available here: <a href='http://www.slideshare.net/SavannahWorth'>http://www.slideshare.net/SavannahWorth</a>

As you start working on larger and more complicated projects, it’s likely you’ll end up with a lot of classes. Organizing all this code becomes complicated quickly; however, Ruby has ways to give our classes structure and define relationships between them. Inheritance is one such tool.

 Using inheritance, a relationship is established between two classes in which one class can reference the other. This reference allows our class to gain access to the methods of the class it is inheriting from. This has several important benefits for us as programmers.

First, it makes it easier for us to follow what’s going on, and keep track of which class does what.
	Second, it reduces repetition in our code. By letting one class use a method from another class, we eliminate the need to copy the same method in both classes. This is huge if we need to go back and change that method later. Cutting down repetition makes it easier to reuse our code, and helps reduce bugs in our code.
	Finally, inheritance allows the creation of specialized classes that share a common set of methods. When we talk about things such as creating controllers in Rails, this benefit of inheritance is key.

So that’s what inheritance can do for us. Before we see some code, let’s talk about classes a little. Making a class is easy—give it a name, fill it full of methods and variables, pretty straightforward. But to really get what’s going on with inheritance, we have to understand the idea of references—these labels we put on enclosed bits of code so Ruby can find them later.

Imagine that when we make a class, it is a box that is filled with code. And that box has a duct-tape label on the side that has our class’s name written on it, so that when Ruby goes looking for some code within that class, it can just read labels of classes. It doesn't need to go into the box until it finds the label it’s looking for. We can also refer to this label as a class pointer.

The methods within our class have their own names/labels, which work much the same. Ruby uses the name to find a method within a class, so it doesn’t have to look at the code within the method until it finds the one it’s looking for. Inheritance lets one class reference the methods in another class's box by the method name.

There’s no magic happening here with these pointers—they’re just words. Say we have a class called Planet with a method called ’noun’. When the ’noun’ method is called on an instance of our Planet class, all Ruby does is look for a method called ’noun’ within the Planet class. These pointers are the only links between the code that's called and the classes and methods we’ve already written, hence why they’re so important!

So, inheritance. Inheritance works best in ‘is-a’ type relationships. The example classes I’m going to be using throughout this post are Planet, IceGiant, and GasGiant. IceGiants and GasGiants are types of Planets—in other words, an IceGiant is a type of Planet, and therefore should do planet-y type things, like orbit and be big and such. Just like you’d have an easier time explaining what an ice giant is to someone who is already knows what a planet is, you would have a much easier time defining the IceGiant class if you had a Planet class to build off of. Inheritance to the rescue!

Also, key terms: the inheritance relationship is between a parent class and a child class, where the child class inherits from the parent class. You’ll also hear the term superclass and subclass, where the subclass inherits from the superclass. It’s good to be familiar with both terminology pairs.

Code time! Here’s a simple version of what these classes could look like:

```ruby
class Planet
  def noun
    puts "Planet!"
  end
end

# This defines a class, GasGiant, which inherits from the Planet class
class GasGiant < Planet
  def material
    puts "Gas!"
  end
end

# This defines a class, IceGiant, which inherits from the Planet class
class IceGiant < Planet
  def material
    puts "Ice!"
  end
end
```

This defines three classes, Planet, IceGiant, and GasGiant, and makes IceGiant and GasGiant inherit from the Planet class. All of the inheritance magic happens on the very first line of the class definition. Following the name of the class is the < symbol followed by the name of the class that will become the parent, which is Planet in both these cases.

That's it! Now our IceGiant class and our GasGiant class will have access to the 'noun' method from our Planet class. Running the following code produces the commented outputs:

```ruby
neptune = IceGiant.new

neptune.material #=> 'Ice!'

neptune.noun #=> 'Planet!'
```
Even though the noun method was called directly on our neptune object, an instance of the IceGiant class, it worked just fine.

Let’s step through the method lookup and see what’s happening here. All method calls in Ruby work the same—they look for a method with the name we called within the class of the object the method was called on. So calling the ‘noun' method on the neptune object causes Ruby to go and look in the IceGiant class for a method called ‘noun'. The IceGiant class doesn’t have it’s own ‘noun' method, so if IceGiant was not inheriting from Planet, this method lookup would return a no method error.

However, we do have an inheritance relationship! This gives Ruby another place to look. The IceGiant class is actually going to contain a little label of its own that says its parent is ‘Planet’. Ruby can follow that label back to the Planet class. It’ll then rummage through Planet class’s methods, and find a ‘noun' method. It runs that code, and that’s it! Our method call has been fulfilled, and returns the string ‘Planet!’.

It’s important at this point to note that the IceGiant class never gets the ‘noun' method—it just gains a reference to it. In addition, the inheritance relationship only goes one direction. If we made an instance of the Planet class, and attempted to call the 'material' method on it, it would return a no method error. Method calls only move up the method chain—never back down.

Method calls only are looking for something with the right method name in the class definition, so what happens if there is a method in the child class that has the same name as a method in the parent class? If we alter the IceGiant class like so:

```ruby
class IceGiant < Planet
  def material
    puts "Ice!"
  end

  def noun
    puts "Ice Giant!"
  end
end
```

so that now it has its own 'noun' method while still inheriting from the Planet class, and then run

```ruby
neptune = IceGiant.new

neptune.noun #=> 'Ice Giant!'
```
you can see that now our method call only gives the code from the IceGiant class’s 'noun' method. Our parent class’s 'noun' method was never touched.

This makes sense when we consider how method calls work. The program is searching a method called 'noun', and the IceGiant class is the first place it looks. There's never any need for to go look in the Planet class's code.

Making a method in the child class with the same name as one in the parent class will block access to the parent class's method. Sometimes this is good; other times we'll want to run some code from the parent class’s method, and also include some specialized code in a child class's method. I've seen this happen a lot with 'initialize'. When this is the case, Ruby provides a way to get access to the parent class's method from the child class's. It's the 'super' keyword.

We edit our IceGiant class to look like this:

```ruby
class IceGiant < Planet
  def material
    puts "Ice!"
  end

  def noun
    super
    puts "Ice Giant!"
  end
end
```

So now the Ice Giant class has the super keyword in its 'noun' method. Now if we call the noun method on our Ice Giant instance object...

```ruby
neptune = IceGiant.new

neptune.material #=> 'Ice!'

neptune.noun
  #=> 'Planet!'
  #=> 'Ice Giant!'
 ```

 And now it returns two strings! First ‘Planet!' and then ‘Ice Giant!'. You'll notice this mirrors the structure of the method itself. On the first line is the super keyword, and the Planet string is returned first. Then the second line is puts ‘Ice Giant!'. If we moved the super keyword to the last line of the method, the ‘Ice Giant!' string  would be returned first and the ‘Planet!' string second.

 What super is doing is pretty simple. When our program encounters it within a method, it knows to go up and look in the parent class for another method that shares the current method's name. So we're in ‘noun', Ruby hits 'super', it goes up to the Planet class and looks for another method named 'noun'. It's all about the names--that's the only connection between our two methods. Once it finishes running this parent method, it drops back into the child method, and finishes running the code there. So when 'super' came first, the ‘Planet!' string was returned and then the ‘Ice Giant!' string. There's more to 'super' than I can cover now, and it's worth some research. However, moving on to some more general notes about inheritance.

 Ruby has single inheritance (you can model multiple inheritance using modules, but that's a whole other topic). Basically, that means you can only put one class name following the <. However, a class can inherit from a class that is inheriting from another class, so that the class in the middle is a parent to one class and a child to another. These roles only exist in relation to each other--otherwise our classes are just regular classes. Now, if there is a method call on an object of the lowest class on the chain, it would be able to look in its parent, and then the parent of its parent! You can expand this chain infinitely, so that you have parents of parents of parents and probably will never be able to figure out which code is getting run (read: use with caution).

You'll hear sometimes that using inheritance is a bad idea. Inheritance is a tool. Sometimes it's the right one and sometimes not. It’s great for ‘is-a’ type relationships. Like here, IceGiant is a type of Planet, and therefore should do Planet-y type things. On the other hand, an IceGiant has a Moon, but if we had a Moon class we likely wouldn’t want it to be inheriting from the IceGiant class since that is a ‘has-a’ type relationship rather than an ‘is-a’ type relationship. We don’t really want our Moon to do IceGiant things. In such ‘has-a’ type cases, there are alternatives to inheritance we can use, specifically modules and composition.

 Anyway, that’s all I got! Please hit me up with any comments/questions on twitter; my handle is <a href='https://twitter.com/Savannahdworth'>@savannahdworth</a>.

 Thanks!

 Savannah Worth
