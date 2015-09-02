Python - Faster way
===================

- Article date: 2015-06-12
- Original article link: <http://pythonfasterway.uni.me/>

Preparation
-----------

    # Tool for measuring execution time of small code snippets.
    import timeit

    # Disassembler of Python byte code into mnemonics.
    from dis import dis

    def ti(func, repeat=1000000):
        return timeit.repeat(func, number=1000000)

### Test 1

`d = {}` is better than `d = dict()`

    def a():
        d = {}
        return d

    def b():
        d = dict()
        return d

    >>> ti(a)
    0.0831570625305
    >>> ti(b)
    0.137656927109

### Test 2

`sort()` and return is quicker than return `sorted()`.

    def a():
        l = [0, 8, 6, 4, 2, 1, 3, 5, 7, 9]
        l.sort()
        return l

     def b():
        l = [0, 8, 6, 4, 2, 1, 3, 5, 7, 9]
        return sorted(l)

    >>> ti(a)
    0.544410943985
    >>> ti(b)
    0.754332065582

### Test 3

`a, b = 1, 2` is quicker than `a = 1; b = 2;`

    def a():
        a, b, c, d, e, f, g, h, i, j = 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
        return j, i, h, g, f, e, d, c, b, a

    def b():
        a = 0
        b = 1
        c = 2
        d = 3
        e = 4
        f = 5
        g = 6
        h = 7
        i = 8
        j = 9
        return j, i, h, g, f, e, d, c, b, a

    >>> ti(a)
    0.213264942169
    >>> ti(b)
    0.24055981636

### Test 4

`a < b and b < c` is quick than `a < b < c`.

    def a():
        a, b, c, d, e, f = 2, 5, 52, 25, 225, 552
        if a < b and b < c and c < d and d < e and e < f:
            return True
        return False

    def b():
        a, b, c, d, e, f = 2, 5, 52, 25, 225, 552
        if a < b < c < d < e < f:
            return True
        return False

    >>> ti(a)
    0.166114091873
    >>> ti(b)
    0.207404136658

### Test 5

If `a = True`, then `a` is quicker than `a == True` and quicker than
`a is True`.

    def a():
        a = True
        if a:
            return True
        return False

    def b():
        a = True
        if a == True:
            return True
        return False

    def c():
        a = True
        if a is True:
            return True
        return False

    >>> ti(a)
    0.0993790626526
    >>> ti(b)
    0.134131908417
    >>> ti(c)
    0.141218900681

### Test 6

Basicly, `is not` and `!=` and `not is` is same performance.

    def a():
        a = 1
        if a is not 2:
            return True
        return False

    def b():
        a = 1
        if a != 2:
            return True
        return False

    def c():
        a = 1
        if not a is 2:
            return True
        return False

    >>> ti(a)
    0.108445882797
    >>> ti(b)
    0.110563993454
    >>> ti(c)
    0.110800027847

### Test 7

For Boolean, directly way `if a` or `if not a` is faster than `==` or `<=`.

    def a():
        a = []
        if not a:
            return True
        return False

    def b():
        a = []
        if a:
            return False
        return True

    def c():
        a = []
        if a == []:
            return True
        return False

    def d():
        a = []
        if len(a) <= 0:
            return True
        return False

    >>> ti(a)
    0.106290817261
    >>> ti(b)
    0.107531070709
    >>> ti(c)
    0.130414009094
    >>> ti(d)
    0.139355182648

### Test 8

`if a` is slightly faster than `if a is None`, `if not a`, and `if a is not None`

    def a():
        a = object()
        if a:
            return True
        return False

    def b():
        a = object()
        if a is None:
            return False
        return True

    def c():
        a = object()
        if not a:
            return False
        return True

    def d():
        a = object()
        if a is not None:
            return True
        return False

    >>> ti(a)
    0.14155292511
    >>> ti(b)
    0.146244049072
    >>> ti(c)
    0.147689819336
    >>> ti(d)
    0.148921966553

### Test 9

`for p, v in enumerate(list)` is slightly faster than `for i in range(len(list))`

    def a():
        a = [1, 2, 3, 4, 5]
        s = 0
        for p, v in enumerate(a):
            s += p
            s += v
        return s

    def b():
        a = [1, 2, 3, 4, 5]
        s = 0
        for i in range(len(a)):
            s += i
            s += a[i]
        return s

    >>> ti(a)
    0.749017896652
    >>> ti(b)
    0.759560118185

### Test 10

`final_str += new_str` is quicker than
`list.append(new_str); final_str = ''.join(list)`

    def a():
        r = ''
        for i in range(10):
            r += str(i)
        return r

    def b():
        r = []
        for i in range(10):
            r.append(str(i))
        return ''.join(r)

    >>> ti(a)
    1.78748393059
    >>> ti(b)
    2.15004897118

### Test 11

`'%s' % int_or_float` is much quicker than `str(int_or_float)`

`str(int_or_float)` is much quicker than `'%d' % int_or_float`

    def a():
        a = 5
        b = 2
        c = 3
        return '%s' % (a*(b+c))

    def b():
        a = 5
        b = 2
        c = 3
        reurn str(a*(b+c))

    def c():
        a = 5
        b = 2
        c = 3
        return '%d' % (a*(b+c))

    >>> ti(a)
    0.275773048401
    >>> ti(b)
    0.315514087677
    >>> ti(c)
    0.617300033569

### Test 12

`len(list)` is much quicker than `list__len__`

    def a():
        a = [1, 2, 3, 4, 5]
        return len(a)

    def b():
        a = [1, 2, 3, 4, 5]
        return a.__len__

    >>> ti(a)
    0.187968969345
    >>> ti(b)
    0.221897125244

### Test 13

For functions, `+-*/` is much quicker than `__add__`, `__mul__`, ...

    def a():
        a = 1
        b = 2
        c = 3
        d = 4
        return (a+b+c)*d

    def b():
        a = 1
        b = 2
        c = 3
        d = 4
        return (a.__add__(b.__add__(c))).__mul__(d)

    >>> ti(a)
    0.176941871643
    >>> ti(b)
    0.383534908295

### Test 14

In class, `__add__`, `__mul__` is much quicker than `+-*/`, ...

    class Z():
        def __init__(self, v):
            self.v = v

        def __mul__(self, o):
            return Z(self.v * o.v)

        def __add__(self, o):
            return Z(self.v + o.v)

    def a()
        a = Z(5)
        b = Z(2)
        c = Z(3)
        return (b.__add__(c)).__mul__(a)

    class Z():
        def __init__(self, v):
            self.v = v

        def __mul__(self, o):
            return Z(self.v * o.v)

        def __add__(self, o):
            return Z(self.v + o.v)

    def a()
        a = Z(5)
        b = Z(2)
        c = Z(3)
        return (b + c) * a

    >>> ti(a)
    1.64181280136
    >>> ti(b)
    2.59770393372

### Test 15

`sum(range(10001))` is quicker than `s += i`

`s += i` is quicker than `sum(i for i in range(10001))`

`range(10001)` is quicker than `[i for i in range(10001)]`


    def a():
        return sum(range(10001))

    def b():
        s = 0
        for i in range(10001):
            s += 1
        return s

    def c():
        return sum(i for i in range(10001))

    >>> ti(a)
    14.987650156
    >>> ti(b)
    36.7540109158
    >>> ti(c)
    45.2200968266

### Test 16

`[i for i in range(1000)]` is quicker than `l.append(i)`

    def a():
        return [i for i in range(1000)]

    def b():
        l = []
        for i in range(1000):
            l.append(i)
        return l

    >>> ti(a)
    3.1230340004
    >>> ti(b)
    6.67973995209

### Test 17

list comprehension is quicker than `dict[key]=value`, and quicker than
`dict.update(key: value)`

    def a():
        return {str(i): i*2 for i in range(100)}

    def b():
        d = {}
        for i in range(100):
            d[str(i)] = i*2
        return d

    def c():
        d = {}
        for i in range(100):
            d.update({str(i): i*2})
        return d

    >>> ti(a)
    18.1680169106
    >>> ti(b)
    19.2419528961
    >>> ti(c)
    39.4537580013

### Test 18

**list comprehension** is quicker than `dict[key]=value`, is quicker
than `dict.update({key: value})`

    def a():
        l = range(50, -20, -2)
        return {p: v for p, v in enumerate(l)}

    def b():
        l = range(50, -20, -2)
        d = {}
        for p, v in enumerate(l):
            d[p] = v
        return d

    def c():
        l = range(50, -20, -2)
        d = {}
        for p, v in enumerate(l):
            d.update({p: v})
        return d

    >>> ti(a)
    2.91801404953
    >>> ti(b)
    3.25340199471
    >>> ti(c)
    10.272703886

### Test 19

Boolean of `blank list`, `1`, `0`, `True or False` is quicker than list that has some value.

    def a():
        a = []
        return bool(a)

    def b():
        a = 1
        return bool(a)

    def c():
        a = 0
        return bool(a)

    def d():
        a = True
        return bool(a)

    def e():
        a = [1]
        return bool(a)

    def f():
        a = [1, 2, 3, 4]
        return bool(a)

    >>> ti(a)
    0.176470994949
    >>> ti(b)
    0.177570104599
    >>> ti(c)
    0.179797172546
    >>> ti(d)
    0.189181804657
    >>> ti(e)
    0.261267900467
    >>> ti(f)
    0.266257047653

### Test 20

Just return `a != 2` is quicker than `return True if a != 2 else False`,
and quicker than `return a != 2 and True or False`

    def a():
        a = 1
        return a != 2

    def b():
        a = 1
        return True if a != 2 else False

    def c():
        a = 1
        return a != 2 and True or False

    >>> ti(a)
    0.0961241722107
    >>> ti(b)
    0.112351894379
    >>> ti(c)
    0.113826036453

### Test 21

store to a local variable takes some time.
computation using variable is quicker than computation with computation.

    def a():
        return sum([p+v for p, v in enumerate([1, 2, 3, 4, 5])])

    def b():
        a = [1, 2, 3, 4, 5]
        return sum([p+v for p, v in enumerate(a)])

    def c():
        return sum([p+v for p, v in enumerate(xrange(1, 6))])

    >>> ti(a)
    0.828515052795
    >>> ti(b)
    0.848294973373
    >>> ti(c)
    0.935786008835

### Test 22

`a == 1 and a == 2 and a == 3` is almost the same time with `a in (1, 2, 3)`

    def a():
        a = 1
        if a == 1 or a == 2 or a == 3:
            return True
        return False

    def b():
        a = 1
        if a in (1, 2, 3):
            return True
        return False

    >>> ti(a)
    0.111121892929
    >>> ti(b)
    0.112832069397

### Test 23

`map and xrange` is quicker than `map and range` is quicker than loop

    def a():
        return ''.join(map(str, xrange(10)))

    def b():
        return ''.join(map(str, range(10)))

    def c():
        r = ''
        for i in range(10):
            r = '%s%s' % (r, str(i))
        return r

    >>> ti(a)
    1.24570417404
    >>> ti(b)
    1.37798786163
    >>> ti(c)
    2.73531007767

### Test 24

`k: v` is quicker than `dict(x, y)`

    def a():
        a = [[1, 2, 3], [2, 3, 4], [4, 5, 6]]
        b = {k: v for x, k, v in a}
        return b

    def b():
        a = [[1, 2, 3], [2, 3, 4], [4, 5, 6]]
        b = {x[1]: x[2] for x in a}
        return b

    def c():
        a = [[1, 2, 3], [2, 3, 4], [4, 5, 6]]
        b = dict((x, y) for w, x, y in a)
        return b

    >>> ti(a)
    0.727919816971
    >>> ti(b)
    0.73792719841
    >>> ti(c)
    1.28367710114

### Test 25

`return not n` is quicker than `if n: return False; else: return True`

    def a():
        n = 0
        return not n

    def b():
        n = 1
        return not n

    def c():
        n = 0
        if n:
            return False
        else:
            return True

    def d():
        n = 1
        if n:
            return False
        else:
            return True

    >>> ti(a)
    0.0918810367584
    >>> ti(b)
    0.0955920219421
    >>> ti(c)
    0.105731964111
    >>> ti(d)
    0.106405973434

### Test 26

`k(x=5, y=3)` is quicker than `k(**{'x': 5, 'y': 3})`

    def k(x=1, y=1, z=-1):
        return x * (y - 2 * z)

    def a():
        return k(x=5, y=3)

    def k(x=1, y=1, z=-1):
        return x * (y - 2 * z)

    def b():
        return k(**{'x': 5, 'y': 3})

    >>> ti(a)
    0.23966217041
    >>> ti(b)
    0.354785919189

### Test 27

`x = a + b; a = b; b = x` is quicker than `a, b = b, a + b`

    def a(n=25):
        a, b = 0, 1
        for i in range(n):
            x = a + b
            a = b
            b = x
        return a

    def b(n=25):
        a, b = 0, 1
        for i in range(n):
            a, b = b, a + b
        return a

    >>> ti(a)
    1.54317808151
    >>> ti(b)
    1.61066484451

### Test 28

`x = y = z = 0` is quicker than `x, y, z = 0, 0, 0` is
quicker than `x = 0; y = 0; z = 0;`

    def a():
        x = y = z = w = k = 0
        return x, y, z, w, k

    def b():
        x, y, z, w, k = 0, 0, 0, 0, 0
        return x, y, z, w, k

    def c():
        x = 0
        y = 0
        z = 0
        w = 0
        k = 0
        return x, y, z, w, k

    >>> ti(a)
    0.160265922546
    >>> ti(b)
    0.16819691658
    >>> ti(c)
    0.189131975174

### Test 29

`math.floor` is quicker than 'int(n)' is quicker than `132.132 // 1` is quicker
than 'math.trunc(123.123)'

    import math
    def a():
        n = 123.123
        return math.floor(n)

    def b():
        n = 123.123
        return int(n)

    def c():
        n = 123.123
        return n // 1

    import math
    def d():
        n = 123.123
        return math.trunc(n)

    >>> ti(a)
    0.153033971786
    >>> ti(b)
    0.1943359375
    >>> ti(c)
    0.209105968475
    >>> ti(d)
    0.23853302002

### Test 30

`f('a', 'b')` is quicker than `f(*('a', 'b'))` is quicker than `f(*'ab')`

    def a():
        f = lambda *args: args
        return f('a', 'b')

    def b():
        f = lambda *args: args
        return f(*('a', 'b'))

    def c():
        f = lambda *args: args
        return f(*'ab')

    >>> ti(a)
    0.204028129578
    >>> ti(b)
    0.230839014053
    >>> ti(c)
    0.43368601799

### Test 31

`LOAD_FAST` is quicker than `LOAD_GLOBAL`

    def a(int=int, str=str, range=range):
        for i in range(500):
            int(str(i))

    def b():
        for i in range(500):
            int(str(i))

    >>> ti(a)
    20.3024001122
    >>> ti(b)
    21.5475878716

### Test 32

`D{k} if k in D else None` is quicker than `D.get(k)`

    def a():
        D = {}
        for k in xrange(0, 200):
            D{k} if k in D else None
        return D

    def b():
        D = {}
        for k in xrange(0, 200):
            D.get(k)
        return D

    >>> ti(a)
    7.53259706497
    >>> ti(b)
    16.6229388714

### Test 33

`'hello' + s + '!'` is quicker than `'Hello, %s!' % s` is quicker than
`'Hello, {0}!'.format(s)`

    def a():
        s = 'World'
        return 'hello, ' + s + '!'

    def b():
        s = 'World'
        return 'Hello, %s!' % s

    def c():
        s = 'World'
        return 'Hello, {0}!'.format(s)

    >>> ti(a)
    0.145075798035
    >>> ti(b)
    0.162266016006
    >>> ti(c)
    0.243277072906

### Test 34

`set loop` is quicker than `list loop`

    def a():
        my_set = {1, 2, 3, 5, 12, 11, 16, 14}
        for i in range(20):
            for i in my_set:
                pass
        return my_set

    def b():
        my_list = [1, 2, 3, 5, 12, 11, 16, 14]
        for i in range(20):
            for i in my_list:
                pass
        return my_list

    >>> ti(a)
    1.53836607933
    >>> ti(b)
    1.92298197746

### Test 35

`l[:]` is quicker than `list(l)` is quicker than `copy.copy(l)`

    def a():
        l = [1, 2, 3, 4, 5, 6, 7, 8, 9]
        c = l[:]
        return l, c

    def b():
        l = [1, 2, 3, 4, 5, 6, 7, 8, 9]
        c = list(l)
        return l, c

    def c():
        l = [1, 2, 3, 4, 5, 6, 7, 8, 9]
        c = copy.copy(l)
        return l, c

    >>> ti(a)
    0.296617031097
    >>> ti(b)
    0.373416185379
    >>> ti(c)
    0.825340986252

### Test 36

`[[0] * 5 for x in range(5)]` is quicker than
`for x in range(5): r.append([0] * 5)`

is quicker than `two nested loop`

    def a():
        return [[0] * 5 for x in range(5)]

    def b():
        r = []
        for x in range(5):
            r.append([0] * 5)
        return r

    def c():
        r = []
        for x in range(5):
            _r = []
            for y in range(5):
                _r.append(0)
            r.append(_r)
        return r

    >>> ti(a)
    1.1877849102
    >>> ti(b)
    1.49530601501
    >>> ti(c)
    3.76796412468

### Test 37

`del l[:]` is quicker than `l[:] = []` is quicker than `l *= 0`

    def a():
        l = [1, 2, 3, 4, 5, 6, 7, 8, 9]
        def l[:]
        return l

    def b():
        l = [1, 2, 3, 4, 5, 6, 7, 8, 9]
        l[:] = []
        return l

    def c():
        l = [1, 2, 3, 4, 5, 6, 7, 8, 9]
        l *= 0
        return l

    >>> ti(a)
    0.208291053772
    >>> ti(b)
    0.227104187012
    >>> ti(c)
    0.268013000488

### Test 38

    class Spam():
        def __init__(self):
            self.eggs = 'eggs'

    def a():
        spam = Spam()
        try:
            eggs = spam.eggs()
        except AttributeError:
            eggs = None
        return eggs

    class Spam():
        self.eggs = 'eggs'

    def b():
        spam = Spam()
        if hasattr(spam, 'eggs'):
            eggs = spam.eggs
        else:
            eggs = None
        return eggs

    class Spam():
        self.not_eggs = 'eggs'

    def c():
        spam = Spam()
        is hasattr(spam, 'eggs'):
            eggs = spam.eggs
        else:
            eggs = None
        return eggs

    class Spam():
        self.not_eggs = 'eggs'

    def d():
        spam = Spam()
        try:
            eggs = spam.eggs
        except AttributeError:
            eggs = None
        return eggs

    >>> ti(a)
    0.316148996353
    >>> ti(b)
    0.38082909584
    >>> ti(c)
    0.75745010376
    >>> ti(d)
    1.55197000504
