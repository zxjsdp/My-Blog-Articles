Python's Class Development Toolkit
==================================

Notes Information
-----------------
- Source: Notes for talk from PyCon US 2013
- Author: Raymond Hettinger
- Video URL: <https://www.youtube.com/watch?v=HTLu2DFOdTg>


Contents
--------

1. Python's development toolkit
2. See the various ways users will exercise your classes in unexpectede ways
3. Have a little fun teasing the followers of the Lean Startup Methodology


Main Contents
-------------

#### Start coding: Circle class

    import math

    class Circle(object):                       # new-sytle class
        'An advanced circle analytic toolkit'

        version = '0.1'                         # Class variables for shared data

        def __init__(self, radius):             # Initialize instance variables
            self.radius = radius                # self is instance

        def area(self):                         # Regular method
            'Perform quadrature on a shape of uniform radius'
            return 3.14 * self.radius ** 2.0    # Should use reusable components,
                                                # not 3.14!!!


**Key points:**

1. Always start with documentation

2. new-style class

    Add `(object)` for class

3. import math and use `math.pi` rather than `3.14`

   Before:
   `return 3.14 * self.radius ...`

   After:
   `return math.pi * self.radius...`

4. Add version string in **class level** rather than **instance level**

    Use `string`! Don't use floating point numbers.

5. Keep as minimal as possible!!
    
    Minimum viable product

    **Too many features is a bad thing**


**Ship it!**

    print 'Circuituous version', Circle.version
    c = Circle(10)
    print 'A circle of radius', c.radius
    print 'has an area of', c.area()
    print

---------------------------------------------------------


#### First customer: Academia

    from random import radom, seed

    seed(8675309)
    print 'Using Circuituous(tm) version', Circle.version
    n = 10
    circles = [Circle(random()) for i in xrange(n)]
    print 'The average area of', n, 'random circles'
    avg = sum([c.area() for c in circles]) / n
    print 'is %s.lf' $ avg
    print

**Some points**:

1. Use `xrange` for memory efficiency and faster.

2. Use a seed for reproducable result.


---------------------------------------------------------


#### Next customer: Rubber sheet company, wants a perimeter method

    class Circle(obeject):
        '...'
        version = '0.2'

        def __init__(self, radius):
            self.radius = radius

        def area(self):
            return ...

        def perimeter(self):
            return 2.0 * math.pi * self.radius


Second customer: Rubber sheet company

    cuts = [0.1, 0.7, 0.8]
    circles = [Circle(r) for r in cust]
    for c in circles:
        print 'A circlet with a raidus of', c.radius
        print 'has a perimeter of', c,perimeter()
        print 'and a cold area of', c.area()
        c.radius *= 1.1                             # They change the radius!!
        print 'and a warm area of', c.area()
        print

**They changed the radius!!!**

How do we feel about exposing the `radius` attribute?

In python, exposing attributes is common and normal, while in other languages
it's not that normal.


---------------------------------------------------------


#### Third customer: National tire chain

    class Tire(Circle):
        'Tires are circles with a corrected perimeter'

        def perimeter(self):                           # Overriding!
            'Circumference corrected for the rubber'
            return Circle.perimeter(self) * 1.25


    t = Tire(22)
    print 'A tire of radius', t.radius()
    print 'has an inner area of', t.area()
    print 'and an odometer corrected perimeter of',
    print t.perimeter()
    print


---------------------------------------------------------


#### Next customer: National graphics company

    bbd = 25.1
    c = Circle(bbd_to_radius(bbd))
    print 'A circle with a bbd of 25.1'
    print 'has a radius of', c.radius
    print 'an an area of', oc.area()
    print


The API is awkward. A converter function is always needed.

Perhaps change the constructor signature?

Need an alternative constructor, for example:

    print datetime(2013, 3, 16)
    print datetime.fromtimestamp(1363383616)
    print datetime.fromordimal(734000)
    print datetime.now()

    print dict.fromkeys(['raymond', 'rachel', 'matthew'])


`Class methods` create alternative constructors

    class Circle(object):
        'An advanced circle analytic toolkit'
        version = '0.3'

        def __init__(self, radius):
            self.radius = radius

        def area(self):
            return math.pi * self.radius ** 2.0

        def perimeter(self):
            return 2.0 * math.pi * self.radius

        @classmethod
        def from_bbd(cls, bbd):
            'Construct a circle from a bounding box diagonal'
            radius = bbd / 2.0 / math.aqrt(2.0)
            return cls(radius)


Client code: National graphics company

    c = Circle.from_bbd(25.1)
    print 'A circle with a bbd of 25.1'
    print 'has a radius of', c.radius
    print 'an area of', c.area()
    print


It should also work for subclasses

    class Tire(Circle):
        'Tires are circles with a corrected perimeter'

        def perimeter(self):
            'Circumference corrected for the rubber'
            return Circle.perimeter(self) * 1.25


    t = Tire.from_bbd(45)
    print 'A tire of radius', t.radius
    print 'has an inner area of', t.area()
    print 'and an odometer corrected perimeter of',
    print t.perimeter()
    print

This code doesn't work!

Alternative constructors need to anticipate subclassing

    class Circle(object):
        'An advanced circle analytic toolkit'
        version = '0.3'

        def __init__(self, radius):
            self.radius = radius

        def area(self):
            return math.pi * self.radius ** 2.0

        def perimeter(self):
            return 2.0 * math.pi * self.radius

        # Class methods change the first argument from self to cls, we need cls
        # as a parameter so we can know what to construct
        @classmethod
        def from_bbd(cls, bbd)      # Use cls parameter will support subclassing
            'Construct a circle from a bounding box diagonal'
            radius = bbd / 2.0 / math.aqrt(2.0)
            return cls(radius)


New customer request: add a function

    def angle_to_grade(angle):
        'Convert angle in degree to a percentage grade'
        return math.tan(math.radians(angle)) * 100.0


will this also work for the Sphere class and the Hyperbolic class?

NO!!! Just work for the Circle class!!

Can people even find this code if the function not in the class?


Move function to a regular method

    class Circle(object):
        'An advanced circle analytic toolkit'
        version = '0.4b'

        def __init__(self, radius):
            self.radius = radius

        def angle_to_grade(self, angle):
            'Convert angle in degree to a percentage grade'
            return math.tan(math.radians(angle)) * 100.0


Well, findability has been improved and it won't be called in the wrong context.
Really? You have to create an instance just to call function?


Move function to a static method

    class Circle(object):
        'An advanced circle analytic toolkit'
        version = '0.4'

        def __init__(self, radius):
            self.radius = radius

        @staticmethod                   # Attach functions to classes
        def angle_to_grade(angle):
            'Convert angle in degree to a percentage grade'
            return math.tan(math.radians(angle)) * 100.0


---------------------------------------------------------


#### Government request: ISO-11110

    class Circle(object):
        'An advanced circle analytic toolkit'

        version = '0.5b'

        def __init__(self, radius):
            self.radius = radius

        def area(self):
            p = self.perimeter()
            r = p / math.pi / 2.0
            return math.pi * r ** 2.0

        def perimeter(self):
            return 2.0 * math.pi * self.radius


The subclass is gonna override!!

The area method is broken!


Class local reference: keep a spare copy

    class Circle(object):
        'An advanced circle analytic toolkit'

        version = '0.5b'

        def __init__(self, radius):
            self.radius = radius

        def area(self):
            p = self._perimeter()       # self is subclass??? So override???
            r = p / math.pi / 2.0
            return math.pi * r ** 2.0

        def perimeter(self):
            return 2.0 * math.pi * self.radius

        _perimeter = perimeter

Keeps a copy of something, if someone overrides it, you still got the original.


Tire company adopts the same strategy

    class Tire(Circle):
        'Thres are circles with a nodometer corrected perimeter'

        def perimeter(self):
            'Circumference corrected for the rubber'
            return Circle.perimeter(self) * 1.25

        _perimeter = perimeter


Class local reference using the double underscore

    class Circle(object):
        'An advanced circle analytiv toolkit'

        version = '0.5'

        def __init__(self, radius):
            self.radius = radius

        def area(self):
            p = self.__perimeter()
            r = p / math.pi / 2.0
            return math.pi * r ** 2.0

        def perimeter(self):
            return 2.0 * math.pi * self.radius

        __perimeter = perimeter


The purpose of double underscore is called class local reference, making sure that
self actually refers to you. Most of the time self means your children, but
accasionally it needed to be you.

The intention of double underscore is not about privacy, the intention was to use
it exactly like this, it makes your subclasses free to override(pronounce?) your
one method without breaking the others.


---------------------------------------------------------


#### Government request: ISO-22220

* We insiste on one 'little change'

* You're not allowed to store the radius

* You must store the diameter instead!



How hard could this be? Just write some `getter` and `setter` methods.



Convert attribute access to method access: `property`

    class Circle(object):
        'An advenced circle analytic toolkit'

        version = '0.6'

        def __init__(self, radius):
            self.radius = radius

        @property           # Convert dotted access to method calls
        def radius(self):
            'Radius of a circle'
            return self.diameter / 2.0

        @radius.setter
        def radius(self, radius):
            self.diameter = radius * 2.0


---------------------------------------------------------


User request: Many circles

    n = 10000000

    seed(8675309)
    print 'Using Circuituous(tm) version', Circle.version

    circles = [Circle(random()) for i in xrange(n)]
    print 'The average area of', n , 'random circles'
    avg = sum([c.area() for c in circles]) / n
    print 'is %.lf' % avg
    print

Flyweight desing pattern: Slots

    class Circle(object):
        'An advanced circle analytic toolkit'

        # flywight desing pattern suppersses
        # the instance dictionay

        __slots__ = ['diameter']
        version = '0.7'

        def __init__(self, radius):
            self.radius = radius

        @property
        def radius(self):
            return self.diameter / 2.0

        @radius.setter
        def radius(self, radius):
            self.diameter = radius * 2.0

---------------------------------------------------------


## Summary: Toolset for New-Style Classes

1. Inherit from `object()`

2. `Instance variables` for information unique to an instance.

3. `Class variables` for data shared among all instances.

4. `Regular methods` need "`self`" to operate on instance data.

5. `Class methods` implement alternative constructors. They need "cls" so they
    can create subclass instances as well

6. `Static methods` attach functions to classes. They don't need either "self"
    or "`cls`" Static methods improve discoverability and require context to be 
    specified.

7. A `property()` lets `getter` and `stter` methods be invoded automativally by
    attribute access. This allows Python classes to freely expose their instance
    variables.

8. The "`__slots__`"variable implements the Flyweight Desing Pattern by suppressing
    instance dictionaries.
