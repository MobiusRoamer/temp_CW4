# Closure, Func_Interface

## TL;DR
Functional interface is a structure, closure is a behaviour.

A functional interface is an interface with exactly one abstract method.
It defines the shape of a function. Lambdas in Java are compiled as small objects that implement these interfaces.

A closure happens when a lambda uses variables from its surrounding scope.
Those variables are captured — copied from the stack to the heap —
so the lambda can still use them even after the method returns.

Functional interfaces make lambdas possible (so we can pass a functional type argument),
and closures make lambdas powerful (they remember outer variables).


Why do we need functional interface if we can just use lambdas to pass functional arguments? A critical reason is closure.

First it is worth clarifying how Java manages memory behind the scenes

**(Important) How Java handles data behind the scenes**

We can picture java memory management as consisting of three areas working together:
1. Method area (metaspace)
2. Heap
3. Stack

| **Category** | **Stored In** | **Lifetime / When Freed** | **Example Code** | **Notes** |
|---------------|---------------|----------------------------|------------------|-----------|
| **Primitive local variables** | Stack (inside the current method’s stack frame) | Disappear when the method returns | `int a = 5;` | Fast access; values copied between methods |
| **Object references (locals)** | Stack | Disappear when the method returns | `Dog d = new Dog();` | Only the reference is on the stack; the actual object is on the heap |
| **Objects and arrays** | Heap | Until no reference points to them → garbage collected | `new Dog()` or `new int[10]` | Shared between methods and threads |
| **Static variables / class fields** | Method Area / Heap | Exist until the class is unloaded by the JVM | `Dog.count` | Belong to the class, not any instance |
| **Method bytecode & class metadata** | Method Area (Metaspace) | Persist for the lifetime of the loaded class | `Dog.bark()` | Contains compiled bytecode, constant pool, type info |
| **Thread stack frames** | Stack | Created per method call; removed on return | — | Each thread has its own call stack |

## Closure (Runtime)
Consider the following example

Suppose we call
```
Runnable makePrinter() {
    int n = 10;                   // (stack, inside makePrinter)
    return () -> System.out.println(n); // closure captures n
}
```
Normally, when makePrinter() ends: (this is a thread stored in stack, see final case in the memory management table above) so it is stored in its stack frame disappears, 
so n (which was local) would vanish.

But the lambda we return uses `n` later, so Java must preserve its value.
That’s where the closure mechanism steps in.

Behind the scenes Java does the following
```
class MakePrinter$$Lambda implements Runnable {
    private final int captured_n;   // copied from stack to heap
    MakePrinter$$Lambda(int n) {
        this.captured_n = n;
    }

    public void run() {
        System.out.println(captured_n);
    }
}
```
The stack variable `n` is copied into the lambda object’s field `captured_n`,
and since the lambda object lives on the heap, that value now outlives the stack frame.
So effectively we have preserved `n` beyond just one instance call. **Closure refers to the runtime behaviour where 
a function (like a lambda) captures variables from its surrounding scope and keeps them alive (copied to the heap storing objects and arrays).**.

## Functional Interface (Compile Time)
How does functional interface work together with closure?
Closure is expressed through functional interface. In our example, `Runnable` is a functional interface because it has a single abstract method. 
The lambda, being the expression `() -> System.out.println()` is a lambda that provides the implementation of the functional interface. The closure happens 'when the lambda captures the printed 
value `x + base`. To summarise, closure preserves objects in the environment for lambda execution. 


