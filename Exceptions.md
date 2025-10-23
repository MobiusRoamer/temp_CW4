### Exceptions
Exceptions handling is a security layer that recognises runtime errors and still allow the normal flow of a program to continue.

| **Checked Exceptions**                                                                                             | **Unchecked (Runtime) Exceptions**                                                                                                               |
|--------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| Compiler **forces** the handling of checked exceptions (must be caught or declared with `throws`).                 | Handling is **not required** by the compiler — the program may handle them, but it’s optional.                                                   |
| Represent **recoverable** situations that a well-written program should anticipate and handle.                     | Represent **programming errors** or unexpected conditions that are usually not meant to be recovered from.                                       |
| Subclasses of `Exception` (but **not** of `RuntimeException`).                                                     | Subclasses of `RuntimeException`.                                                                                                                |
| Example: `IOException`, `SQLException`, `FileNotFoundException`.                                                   | Example: `NullPointerException`, `ArrayIndexOutOfBoundsException`, `ArithmeticException`, `IllegalArgumentException`.                            |
| Must be handled with `try–catch` or declared using `throws` in the method signature.                               | May occur anywhere — no need to declare them with `throws`.                                                                                      |
| Occur due to **external factors** (I/O, database, network, user input, etc.).                                      | Occur due to **logical or programming errors** (bad data, null references, division by zero, etc.).                                              |
| Example:                                                                                                           | Example:                                                                                                  |
| ```java                                                                                                            | ```java                                                                                                  |
| try {                                                                                                              | int x = 5 / 0; // ArithmeticException                                                                    |
|     FileReader fr = new FileReader("file.txt");                                                                    | String s = null; s.length(); // NullPointerException                                                     |
| } catch (IOException e) {                                                                                          |                                                                                                          |
|     e.printStackTrace();                                                                                           |                                                                                                          |
| }                                                                                                                  |                                                                                                          |
| ```                                                                                                                | ```                                                                                                      |

## (Checked Exception) Try with Resources
In Java, a resource is any external system object that your program uses — like a file, socket, database connection, or input/output stream.
These objects occupy limited system resources outside the java virtual machine (e.g., file descriptors, network ports).
When we are done using them, we must release those system-level handles — that’s what “closing” means.

Most I/O-related operations (file reading, database access, network communication) throw checked exceptions such as IOException or SQLException. It usually requires a three-block structure
```
BufferedReader br = null;
try {
    ...
} catch (IOException e) {
    ...
} finally {
    ...
}

```
TEDIOUS. Try-with-resource solves this problem: 
```
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) { // the resource!
    ...
} catch (IOException e) {
    ...
}
```
It ensures that resources are automatically closed and removes the need for an explicit finally block calling `close()`. 
