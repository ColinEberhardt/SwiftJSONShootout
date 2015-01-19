# Swift JSON Shoot-Out

## Introduction

I'm not entirely sure why, but parsing JSON in Swift appears to be one of the
most popular topics to write about. Maybe it's a right of passage to becoming a
Swift blogger? Anyway, it seems like a good time to get involved.

There have been some truly excellent blog posts about using JSON with Swift, but
they mainly focus on the theory behind using the functional aspects of the new
language to the best effect. This is great, and I recommend you read them, but
we don't necessarily want to have to learn a whole new programming paradigm to
implement the network data layer of our app.

This article is a pragmatic review of the current options available for JSON
parsing in Swift, covering some of the most popular new libraries.

All approaches rely on Cocoa's `NSJSONSerialization` class to handle the JSON
string to Foundation object parsing. The interesting question is what happens at
this point. In the Swift world, the output of 
`JSONObjectWithData(_:, options:, error:)` is an `AnyObject?` blob. What can we
do with this? We know it's made up of Foundation objects such as `NSArray`, 
`NSDictionary`, `NSNumber` etc, but the structure is dependent on the schema of
the JSON.

First of all we'll take a look at what we'd actually like from a JSON parser, in
an ideal world, before reviewing the naïve approaches you'd expect as a seasoned
objective-C developer. Then we'll consider two new frameworks that have popped
up in the last few months, explaining their underlying concepts and reviewing
how close they come to our ideal scenario.


### Wishlist

JSON is a great serialization technology due to its simple specification, and
accessibility to both humans and machines. However, it quickly becomes unwieldy
within the context of an application. Most software design architectures have
the concept of a model layer - that is a selection of objects used to model the
data structure that you application acts upon. It is these model objects that
the JSON should be turned in to once inside the application.

Since the `NSJSONSerialization` class has no knowledge of the specific model
layer within your application, it translates the JSON into the generic types
within Foundation. It is the next step - translating these Foundation types into
our data model - that is important.

Our parser should leverage the type-safety that underlies Swift, and also not
allow partial objects to be created.

As you'll see, satisfying these requirements is not too difficult in a
'best-case' scenario, but becomes increasingly difficult when attempting to cope
with errors in the JSON data structure.

### Other approaches

Before diving into the problem from a Swift point of view, let's take a moment
to review how other languages handle JSON.

C# offers an approach which uses dynamic objects. That is to say that the
structure of the objects is not known at compile time, but instead they are
created at runtime. In some respects, this is a lot like the behavior of
`NSJSONSerialization`, with the extension of using properties on a dynamic
objects instead of a dictionary keyed on strings. This approach is not typesafe,
in that the type-checker has no knowledge of the dynamic objects, and therefore
lets you do whatever you wish with them in the code. It isn't until runtime
(i.e. once the JSON has been parsed) that you discover particular properties
don't exist, or are of the incorrect type.

Sticking with C#, there are alternative approaches that automatically
deserialize JSON into pre-defined model objects through reflection and
annotations. Since your define the model objects in code, you retain the type
safety you're used to, and the annotations/reflection mean that you don't
repeat yourself.

Ideally we'd like to use the latter of these two approaches in Swift. Swift
doesn't yet support reflection or annotations, so we can't get quite the same
functionality, but how close can we get?

### Accompanying Project

This article has accompanying code to demonstrate the different approaches to
JSON parsing in Swift, and it takes the form of three playgrounds, combined
together in a workspace. The workspace also contains projects for the three
framework dependencies - SwiftyJSON, Argo and Runes. Combining everything in a
workspace allows you to use dependencies within playgrounds.

Carthage was used to import the dependencies, but since they have been committed
into the repo, you shouldn't need to worry about it. You will, however, need to
build the frameworks in the workspace. The playgrounds are for OSX, so select
each of __ArgoMac__ and __SwiftJSONOX__ from the build schemes menu and then
build it. Then the playgrounds will work as expected.

## Naïve Parsing

- First approach looks at how to deal with the raw JSON structure. 
- Can use `valueForKeyPath`
- Not very safe
- Involves lots of optional nesting
- Alternatively, can cast the Cocoa objects to their Swift counterparts and
extract data that way.
- Marginally safer
- Still a huge optional nesting tree

## SwiftyJSON

- Open source library to assist with parsing of JSON
- Under the hood it involves specification of a JSON enum as a type to represent
the JSON structure
- Also got lots of implicit type conversions to simplify optional chaining
- In our required situation, still ends up with a tree of optionals to ensure
model object is completely formed
- Good for extracting specific values from a JSON structure

## Argo

- A 'pure-functional' approach to JSON parsing
- Designed to populate model objects directly from the JSON stream
- Copes with primitives automatically
- Requires a `decode` method to be written for custom objects (since there's no
reflection)
- Written in a functional style, so succinct, but complex for a first-timer
- Built in support for incomplete model objects


## Conclusion

- Can't get what I want yet
- Argo is the closest, and doesn't involve a huge amount of code
- However, it can be difficult to get your head around at first
- With reflection, the `decode` method in Argo could be replaced with a default,
which would get exactly what I want


