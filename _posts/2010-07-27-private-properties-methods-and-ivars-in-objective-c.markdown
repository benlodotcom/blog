---
layout: post
title:  "Private properties, methods and ivars in Objective-C"
date:   2010-07-27 22:43:07
categories: ios
---

To make nice OO code it is important not to show everything, OO programming requires modesty. But, in objective-c keeping methods, properties, and instance variables private is not as straightforward as in other languages (java for instance) .

I'll describe in this article what is encapsulation and why it is important. Then we'll see how keep things private in our objc code.

##Keep things encapsulated - Don't make everything public !

In this article, we will use the word encapsulation as a way of hiding (or better not showing) useless information.

Let's start with a small analogy to define encapsulation.

If you want your car repaired you give it to a mechanic, ask him to fix it and then come back later to pick it up. Hopefully, your car is now fully functional again, you don't know what the mechanic did but still the result is there. It is the same with an object, you give it data and then ask it to do a certain task without caring about the inside processing done inside the object.

Ok good but why is it interesting to do so ?

-   First everybody is not a mechanic, what you want is your car repaired, but you don't know what problem it has and what tools to use to fix it. So you simply say "I want my car fixed", then this message, apparently simple, fires a very complex process. The mechanic will diagnose the problem, find the right way, and use the right tools to fix it. Then it will give you back your car. You see, a very simple message hides a very complex work. And this is interesting because somebody who doesn't know anything about repairing cars can still have it done very simply, using the services of another person. It's the same with objects, with simple messages - ie method callings - you can get a certain result without having the knowledge of all the necessary process to get this result.
-   Encapsulation has another advantage. Let's say that the mechanic received a brand-new tool, he is going to use it while repairing cars. So it modifies the way he repairs cars, but it doesn't change anything for the customer who will still come and say "I want my car fixed". In a program if tasks are encapsulated, the programmer can change some pieces of code in an object without affecting the rest of the program.

It is important that an object exposes only a few methods and variables to others not to break this encapsulation. It leads us to the core of this article: how to deal with the visibility of ivar, methods and properties in objective-c.

##Making ivars private public or protected

To achieve this goal apple provide a fairly straightforward solution to this problem. There are four keywords to define the visibility of ivars while declaring them in our interface :

`@public`: The ivar is accessible everywhere.
`@protected`: The ivar is accessible within the class that declares it and within classes that inherit it.
`@private`: The ivar is accessible only within the class that declares it.
`@packaged`: On the iPhone this keyword is equivalent as `@public`. On versions of OSX which are using the modern 64 bits runtime, the ivar is accessible within classes belonging to the same framework as the class that declares it
These keywords define a block, all the ivars declared after the keyword have the desired visibility, the block ends when another keyword is encountered. If no keywords are present `@protected` is assumed.

Here is a small sample that shows how it works.

{% gist 492234 gistfile1.m %}

##Making methods and properties private

Objective-c has no notions of public/private/protected methods, and as properties are just a convenient way to declare getter and setter methods, it is the same for properties. Ok so it's not possible end of the article ...

No I'm kidding there are ways to make your methods and properties private. What we have to do is simply not declare these methods and properties in the header of our class. But, if we don't want to end up with a bunch of warnings - and a very good practice in objective-c is considering warnings as if they were errors ;-) - we have to declare them somewhere in the .m file (or in a .h file that we import in your .m) . We have two possibilities one that I prefer over the other: categories and class extensions.

###Using categories

First and foremost this method is in my opinion not adapted and if I had the choice I'll definitely go for the second one. But, as it is not so rare to see it in code - even in apple samples - I'll describe it. What we do is defining a category in our .m file where we will declare all our private methods and properties. Here is a sample code which shows how to do it.

{% gist 492234 gistfile2.m %}

But this method has some flaws :

-   You can't use synthesize to generate the implementation of your properties. So basically you don't have all the advantages of using properties.
-   You have to implement the methods and properties in their own `@implementation` block. This is more personal taste but I like having all my methods grouped by functionality, and having every private method scattered in other implementation blocks doesn't help.
-   You can't redeclare a property and change it's reading attributes in a category - cf "Note about properties".

###Using class extensions

The syntax to declare a class extension is the same as the one for a category with an empty name. Moreover, we don't have to define another implementation bloc, the implementation has to be done inside the main implementation block. Here is a sample.

{% gist 492234 gistfile3.m %}

This method is the one I use, we don't have any of the flaws described for the method using categories.

###Note about properties

Properties can be declared as `readwrite` or `readonly`.

Using the class extension method - not possible with categories - and `@synthesize` we can have a property with `readonly` access publicly and `readwrite` access privately. To do so we declare your property as `readonly` in your header and redeclare it as `readwrite` in your class extension.

{% gist 492234 gistfile4.m %}

{% gist 492234 gistfile5.m %}

##Conclusion

This is the end of this article, now you know everything about private methods, iVar and properties. If you have any questions the comment section is waiting for you.

Ben