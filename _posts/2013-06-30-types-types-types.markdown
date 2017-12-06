---
layout: post
title:  "Types Types Types"
date:   2013-06-30 13:04:58 +0000
categories: blog csharp cplusplus fsharp pascal ruby
---

Back in the 80’s Mötley Crüe sang about [Girls, Girls, Girls](https://www.youtube.com/watch?v=64HF10lsq54). In 2013 I’m going to write about Types, types, types. Rock and Roll!

I’ve been coding for a few years (though not as many as a lot of folks!). I’ve learnt (and totally forgotten) x86 assembler, C, C++, Pascal, Delphi, Java, C#, F#, Ruby and a few other oddities ([Amiga Amos](https://en.wikipedia.org/wiki/AMOS_(programming_language)) anyone?)

Every language has it’s own peculiarities for creating new types. I thought it could be fun to explore from C (1969) through to F# (2005) to see what’s changed. So I need a simple type I can declare in a bunch of languages – I’m going to ignore method syntax (for this post) and concentrate on plain ol’ data. Something like a 3D vector seems ideal….

# C

{% highlight c %}
typedef struct { double x; double y; double z; } vector;
vector v = { 1.0, 1.0, 1.0 };
{% endhighlight %}

Starting with C, it’s amazing to see how clear and concise a type definition is (especially for a language generally considered “low-level” these days.

# C++

{% highlight c++ %}
class Vector
{
    private:
        double x;
        double y;
        double z;
    public:
        Vector(double px, double py, double pz) : x(px), y(py), z(pz)
        {
        }

        double GetX()
        {
             return x;
        }

        double GetY()
        {
             return y;
        }

        double GetZ()
        {
             return z;
        }
};

Vector v = Vector(1.0,1.0,1.0);
Vector *v = new Vector(1.0,1.0,1.0);

{% endhighlight %}

Whats interesting about the C++ version of this is that it’s immutable (I’ve ignored const for the purpose of keeping this simple (mostly because I’ve forgotten the *correct* way to do it in C++)) but sufficiently more complex. The use of a class has allowed us to protect the underlying internals from modification externally which is something that is desirable from a simplicity point of view and makes multi-threaded applications easier to reason about due to lack of data races, but at the expense of code clarity.

# C\#


{% highlight c# %}
class Vector
{
  private readonly double x;
  private readonly double y;
  private readonly double z;
  public Vector(double x, double y, double z)
  {
    this.x = x;
    this.y = y;
    this.z = z;
  }

  public double X { get { return x; } }
  public double Y { get { return y; } }
  public double Z { get { return z; } }
}

var v = new Vector(1.0,1.0,1.0);

{% endhighlight %}

Like the C++ version this version is immutable (though this time I’ve bothered to do it properly with readonly (because I *can* remember how to do it in C#)). Given C# is a close C++ relative it’s interesting to see how a simple class definition is so different. For a “modern” language it’s still quite verbose

# Pascal


{% highlight pascal %}
type 
  TVector = class
  private
    x : real;
    y : real;
    z : real;
  public
    Property X : real read x;
    Property Y : real read y;
    Property Z : real read z;
    Constructor Create(x : real, y : real, z : real);
 end;

implementation

constructor TVector.Create(x : real, y : real, z: real);
begin
 self.x := x;
 self.y := y;
 self.z := z;
end;

v := TVector.Create(1.0,1.0,1.0);

{% endhighlight %}

I thought I’d include Delphi at this point as it also has a link to C# – via Anders Hejsberg. As far as I remember there is no way to have immutability in a class other than through data-hiding in Delphi – I may be wrong (or it may have changed since the last version I used – Delphi 6). Code wise it’s a similar length to the C++ and C# pieces of code, and the biggest innovation (for its time) was to offer properties – members of a class that could be accessed like fields, but were really method calls.

# Typescript

{% highlight typescript %}
export class Vector3D {
   constructor (public x: number, public y: number, public z: number) {
   }
}

{% endhighlight %}

While we’re on an Anders tangent, heres a look at how to do it in Typescript. Compared to the C# and Delphi code it’s clear to see the succinctness. It lacks the immutability of those versions and in many ways is closest to the C version.

# Ruby

{% highlight ruby %}
class Vector
   attr_reader :X
   attr_reader :Y
   attr_reader :Z
   def initialize(x,y,z)
      @X = x
      @Y = y
      @Z = z
      @X.freeze
      @Y.freeze
      @Z.freeze
   end
end

v1 = Vector.new(1,1,1)
{% endhighlight %}

Backing out of Anders-land for a moment a quick look at Ruby – a language which sells itself on developer productivity. The immutable version (note the freeze calls) clocks in with quite a large definition. Even without the Freeze calls it’s pretty unclear whats going on – lots of abbreviated keywords and symbols in a fairly heavyweight definition

# F\#


{% highlight f# %}
type Vector(x:float, y : float, z : float) =
    member vector.X = x
    member vector.Y = y
    member vector.Z = z
{% endhighlight %}

F# provides more options when it comes to creating new types. The most complex(!) is above clocking in at 4 lines. You could simplify this down to 1 line using F# records like so:


{% highlight f# %}
type Vector = { X : float; Y : float; Z : float }
let v = { X = 1.0; Y = 1.0; Z = 1.0 }
{% endhighlight %}

Which brings us down to 1 line, is completely immutable, and even handles equality completely correctly.

What’s perhaps the strangest phenomenon here is that the definition in F# is almost indistinguishable from the C version! See:


{% highlight f# %}
typedef struct { double x; double y; double z; } vector;
vector v = { 1.0, 1.0, 1.0 };

type Vector = { X : float; Y : float; Z : float }
let v = { X = 1.0; Y = 1.0; Z = 1.0 }
{% endhighlight %}

The key differences are that the C version is mutable and to make it immutable would make it slightly more verbose, and that the C version the value is stack allocated as opposed to on the heap. Again to do this in C would be a little more work ([especially](https://stackoverflow.com/questions/2219001/how-to-initialize-const-members-of-structs-on-the-heap) if it were immutable). [Phil Trelford](http://trelford.com/blog/post/FS4C11.aspx) wrote once that “If you’re interested in augmenting your high performance C++ code with some high level functional code, look beyond the syntax and you may find [F#](http://www.tryfsharp.org/) more familiar and feature rich.”. The same is definitely true for C programmers too!

What’s especially interesting is that there isn’t any clear common ancestry between C and F#.

# Conclusion

The fact that the F# and C versions of the type are virtually identical goes to show that all the diversions in the meantime have accounted for very little in terms of code simplicity. C is considered as a low level language due to its closeness to the underlying assembly language it bakes down to, yet a modern high level language is virtually indistinguishable (and offers several tangible benefits). In fact given the ceremony involved in the C++, C#, Delphi and Ruby implementations you’d be inclined to think that all that code was to somehow help the compiler by giving it more information to do its job, rather than helping the developer do theirs.