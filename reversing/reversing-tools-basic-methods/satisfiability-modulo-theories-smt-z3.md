# Satisfiability Modulo Theories \(SMT\)  - Z3

Very basically, this tool will help us to find values for variables that need to satisfy some conditions and calculating them by hand will be so annoying. Therefore, you can indicate to Z3 the conditions the variables need to satisfy and it will find some values \(if possible\).

## Basic Operations

### Booleans/And/Or/Not

```python
#pip3 install z3-solver
from z3 import *
s = Solver() #The solver will be given the conditions

x = Bool("x") #Declare the symbos x, y and z
y = Bool("y")
z = Bool("z")

# (x or y or !z) and y
s.add(And(Or(x,y,Not(z)),y))
s.check() #If response is "sat" then the model is satifable, if "unsat" something is wrong
print(s.model()) #Print valid values to satisfy the model
```

### Ints/Simplify/Reals

```python
from z3 import *

x = Int('x')
y = Int('y')
#Simplify a "complex" ecuation
print(simplify(And(x + 1 >= 3, x**2 + x**2 + y**2 + 2 >= 5)))
#And(x >= 2, 2*x**2 + y**2 >= 3)

#Note that Z3 is capable to treat irrational numbers (An irrational algebraic number is a root of a polynomial with integer coefficients. Internally, Z3 represents all these numbers precisely.)
#so you can get the decimals you need from the solution
r1 = Real('r1')
r2 = Real('r2')
#Solve the ecuation
print(solve(r1**2 + r2**2 == 3, r1**3 == 2))
#Solve the ecuation with 30 decimals
set_option(precision=30)
print(solve(r1**2 + r2**2 == 3, r1**3 == 2))
```

### Printing Model

```python
from z3 import *

x, y, z = Reals('x y z')
s = Solver()
s.add(x > 1, y > 1, x + y > 3, z - x < 10)
s.check()

m = s.model()
print ("x = %s" % m[x])
for d in m.decls():
    print("%s = %s" % (d.name(), m[d]))
```

## Machine Arithmetic

Modern CPUs and main-stream programming languages use arithmetic over **fixed-size bit-vectors**. Machine arithmetic is available in Z3Py as **Bit-Vectors**.

```python
from z3 import *

x = BitVec('x', 16) #Bit vector variable "x" of length 16 bit
y = BitVec('y', 16)

e = BitVecVal(10, 16) #Bit vector with value 10 of length 16bits
a = BitVecVal(-1, 16)
b = BitVecVal(65535, 16)
print(simplify(a == b)) #This is True!
a = BitVecVal(-1, 32)
b = BitVecVal(65535, 32)
print(simplify(a == b)) #This is False
```

### Signed/Unsigned Numbers

Z3 provides special signed versions of arithmetical operations where it makes a difference whether the **bit-vector is treated as signed or unsigned**. In Z3Py, the operators **&lt;, &lt;=, &gt;, &gt;=, /, % and &gt;&gt;** correspond to the **signed** versions. The corresponding **unsigned** operators are **ULT, ULE, UGT, UGE, UDiv, URem and LShR.**

```python
from z3 import *

# Create to bit-vectors of size 32
x, y = BitVecs('x y', 32)
solve(x + y == 2, x > 0, y > 0)

# Bit-wise operators
# & bit-wise and
# | bit-wise or
# ~ bit-wise not
solve(x & y == ~y)
solve(x < 0)

# using unsigned version of < 
solve(ULT(x, 0))
```

### Functions

**Interpreted functio**ns such as arithmetic where the **function +** has a **fixed standard interpretation** \(it adds two numbers\). **Uninterpreted functions** and constants are **maximally flexible**; they allow **any interpretation** that is **consistent** with the **constraints** over the function or constant.

Example: f applied twice to x results in x again, but f applied once to x is different from x.

```python
from z3 import *

x = Int('x')
y = Int('y')
f = Function('f', IntSort(), IntSort())
s = Solver()
s.add(f(f(x)) == x, f(x) == y, x != y)
s.check()
m = s.model()
print("f(f(x)) =", m.evaluate(f(f(x))))
print("f(x)    =", m.evaluate(f(x)))
```
