### A lighter closeable iterator from Expression Atlas

The Atlas webapp code has [this interface](https://github.com/gxa/atlas/blob/master/base/src/main/java/uk/ac/ebi/atlas/commons/streams/ObjectInputStream.java) :
```
import java.io.Closeable;

public interface ObjectInputStream<T> extends Closeable {
    // returns null when stream is empty
    T readNext();
}
```

The comment at the top links to some stackoverflow post and has a feel of somebody who tried to pick the best abstraction for something the webapp does a lot - iterate through a file and close it afterwards.

You'd want to use it like that:
```
ObjectInputStream<T> stream =  new ...

T t;
while(t=stream.readNext()!=null){
process(t);
}
stream.close();
```

Of course that doesn't play nicely with exceptions that might interrupt the execution - and then the stream won't be closed, oh dear. The old Java way is to put the `stream.close()` in the finally block,  and Java 7 lets you "try with resources" and both of these are already quite a rigid structure your code has to have: a while block within a try block.

I'd much prefer to ignore the exceptions. We're putting the webapp on its own VM, mounted the filesystem all right, surely if the app starts to have problems with opening and reading through files it's not a problem it can solve by itself. Java's type system won't let me do that without polluting the type signature of the method: part of being `java.io.Closeable` means you're going to throw `IOException`s. A stronger version of these semantics is being `java.io.AutoCloseable` where `close()` throws an `Exception` which is meant to really encourage a try-with-resources block. 

At some point someone in the project made an [adapter](https://github.com/gxa/atlas/blob/master/base/src/main/java/uk/ac/ebi/atlas/profiles/IterableObjectInputStream.java) that turns an `ObjectInputStream<T>` into an `Iterable<T>`. Feel free to think how you'd do it - you have a `readNext()`  which returns a [sentinel value](https://en.wikipedia.org/wiki/Sentinel_value) of `null`  when there's no more to read, and you need to provide a `hasNext()` and `next()`. 

OH NO DID YOU FORGET THE `close()`! Well we did at first - or maybe not becuse the adapter was originally for something that didn't actually require closing. I used it when rewriting the backend for our experiment page, and caused a resource leak that made it through test but after the release the webapp eventually ran out of file handles to hog.

Now it's all solved - the stream will be closed after you iterate until the end, and you can
```
for(T t = new IterableObjectInputStream<>(openStream())){
process(t);
}
```
If you do `new IterableObjectInputStream<>(openStream()).iterator().next()` there will still be a leak but there's a comment at the top of the class that warns you to not do it. 

We started with one constraint that can't be expressed through Java's type system - an object that needs `close()` called on it at least once - and swapped it for a second one, a collection that once instantiated must be traversed fully.  Java 8 introduces `java.util.stream` which is AutoCloseable - and it is different still, the programmer is meant to know whether or not they should put it in the try-with-resources block.  

The possibility of somebody forgetting to close a file handle comes with Java just like memory leaks are possible in C. [You can guard against it with convention and high professional standards](https://www.joelonsoftware.com/2005/05/11/making-wrong-code-look-wrong/), and Java's checked exceptions try to nudge you towards the right thing, but in a way that is crude and often inappropriate. For some projects it's appropriate to write them in a language with a stronger type system that will make many mistakes impossible since all your file handles will be damn sure closed because you'll only be able to use them in a tiny main() method at the end of your program. Sometimes you want to [read the file into one large string](http://search.cpan.org/~uri/File-Slurp-9999.19/lib/File/Slurp.pm) and use a language that will only tell you "oh this is so naughty go ahead". A yet different language makes it [very convenient to close your file handles](https://docs.python.org/2/reference/compound_stmts.html#with), and is sternly clear that this is what you should be doing.

<i>
 The Tao gave birth to machine language. Machine language gave birth to the assembler.
The assembler gave birth to the compiler. Now there are ten thousand languages.
Each language has its purpose, however humble. Each language expresses the Yin and Yang of software. Each language has its place within the Tao.
But do not program in COBOL if you can avoid it.  [(source)](http://www.mit.edu/~xela/tao.html) 
</i>








