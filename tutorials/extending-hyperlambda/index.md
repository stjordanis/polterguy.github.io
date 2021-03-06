# Extending Hyperlambda with C#

[Our previous tutorial](/tutorials/send-email) showed
how Hyperlambda is more than just CRUD. In this tutorial, we
will show how Hyperlambda is more than Hyperlambda, by creating
our own extension slot using C#.
Notice, if you prefer to watch video tutorials, here’s a video
where I walk you through everything.

<div style="position:relative; padding-bottom:56.25%; padding-top:30px; height:0; overflow:hidden;margin-top:4rem;margin-bottom:4rem;">
<iframe width="560" height="315" style="position:absolute; top:0; left:0; width:100%; height:100%;" src="https://www.youtube.com/embed/ou7T9YeIQAw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

Expand your _"backend/slots/"_ folder in Visual Studio (Code), and create a new file.
Name your file _"Add.cs_", and put the following content into it.

```csharp
using System.Linq;
using magic.node;
using magic.node.extensions;
using magic.signals.contracts;

namespace tutorials.examples
{
    [Slot(Name = "tutorials.add")]
    public class Add : ISlot
    {
        public void Signal(ISignaler signaler, Node input)
        {
            var arg1 = input.Children.First().Get<int>();
            var arg2 = input.Children.Skip(1).First().Get<int>();
            input.Value = arg1 + arg2;
            input.Clear();
        }
    }
}
```

You have now created your first Hyperlambda
keyword. The above keyword, simply adds two integer values, and
returns the result. If you recompile your Magic backend, you can
immediately consume your keyword in an HTTP endpoint, by creating
a new file in your _"/modules/tutorials/"_ folder, and name your
file _"add.get.hl"_. Put the following content into your file.

```
.arguments
   arg1:int
   arg2:int
add:x:../*/tutorials.add
   get-nodes:x:@.arguments/*
tutorials.add
unwrap:x:+/*
return
   result:x:@tutorials.add
```

Then go to your endpoints, and invoke _"add"_. Make sure
you pass in integer values for its _"arg1"_ and _"arg2"_ QUERY
parameters. After invoking your endpoint your result will
resemble the following.

```json
{
  "result": 7
}
```

## Explanation

Fundamentally, there is _zero difference_ between the above
`ISlot` class you just created, and any other slot you invoke
using Hyperlambda - It's _the exact same thing_ in fact.

The way this works, is that as Magic starts, it will run
through your AppDomain, find all classes implementing the
`ISlot` interface, retrieve its `Slot` attribute's name
property, and use that as the _"key"_ for your class. Later
as you invoke the slot in Hyperlambda, the key will be used
to retrieve an instance of your class, and its `Signal`
method will be invoked, passing in the slot invocation node
as its argument. This allows you to pass any arbitrary
arguments from Hyperlambda into your C# methods.

Hence, there is no real difference between Hyperlambda,
the `Node` class, _"lambda"_ and C#. They all tie beautifully
into each other, extending each other, where the other side
of the equation have some sort of weakness.

### Async slots

If you want to create async slots, you'll have to
implement the `ISlotAsync` interface. This will ensure
that Magic will automatically use your async slot,
if possible. You can
of course implement _both_ interfaces on the same class,
and also have multiple `Slot` attributes, with different
names on the same class too.

## Expressions

So far, we have largely ignored lambda expressions.
However, now they become increasingly important to understand. An
expression is basically any value of a node in Hyperlambda that
have a type declaration of `:x:`. The type declaration can be
found between its name and its value. Below is an example.

```
.foo:x:@.arguments/*
```

The above expression, which is the same expression as the
one we used in our above **[get-nodes]** invocation, happens
to return all nodes that are inside of our **[.arguments]**
node. To translate it into humanly understandable language,
it becomes as follows.

> Find the first node who's name is '.arguments', then return that node's children to me

Inside of our C# slots again, we can evaluate such expressions
if we want to. The above `Get` method, in our C# slot, only returns
the raw value of our slot. But if you use its `GetEx` version instead,
all expressions will be evaluated recursively, until we're left with a
non-expression value, and then that value will be returned.

This means that the following Hyperlambda ...

```
.arguments
   foo:int:5
get-value:x:@.arguments/*/foo
```

... will result in that the value of the **[get-value]** node above
will be `5` after evaluation. Hyperlambda expressions are basically just
piped `IEnumerable` objects. This results in _"chained Linq statements"_,
allowing you to query your Hyperlambda's node structure.
However, we will dive deeper into expressions in our next article.

## Custom controllers, Razor pages, etc

There is nothing preventing you from combining your Hyperlambda
endpoints, with custom C# Controller endpoints, as long as
you resolve your endpoints with anything _but_ _"magic"_ as
its first URL segment. This allows you to combine traditional C#,
MVC, or Web APIs with Magic, utilizing the best from both worlds.
Feel free to add any amount of Razor pages, Views, Controllers,
etc, to your Magic apps. However, this is beyond
the scope of this documentation, and you might have to fiddle with
your `Startup` class if you choose to do this.

* [Expressions](/tutorials/expressions)
