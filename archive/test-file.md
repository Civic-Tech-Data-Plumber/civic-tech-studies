> Sandbox  used to test samples of markup language.

#### Diving Deeper
📌 `variable` is a loop variable that takes on the value of the current item in the `iterable` during each iteration.  
And an `iterable` is the collection of items you want to loop through.  
***Iteration = the repetition of a process or utterance***

You can alter the flow of a for loop using control statements: 
- `break`: Exits the loop entirely, regardless of whether the iterable has been exhausted.
- `continue`: Skips the current iteration and proceeds to the next item in the sequence.
- `pass`: Acts as a placeholder when a statement is syntactically required but no action is desired.
- `else` clause: A block that executes only if the loop completes all iterations without encountering a break statement.

### To reiterate our iterations 😛
`for` item `in` set_of_items `do` x, y and return z.
## `with` - Safe resource handling (Important!)
```python
with sqlite3.connect("courtlistener.db") as conn:
    ...
```
This sample code basically means, “Open this resource, and guarantee it gets closed.”  
It replaces `conn.close()`  
*I'll need to cover this topic later, as I still need to learn a bit more about it.*

## `try` / `except` is used for error handling
```python
try:
    conn.execute(query)
except Exception as e:
    print(e)
```
Used to:
- Prevent crashes
- Log failures
- Keep pipelines running

## `class` an uncommon, but useful feature
```python
class CourtCase:
    ...
```
*Another topic I need to learn more about before divulging too much.*

## `if __name__ == "__main__":` fundamental to Python literacy
What __name__ means

Every Python file has a built-in variable called __name__.
If the file is run directly, Python sets:
```python
  __name__ = "__main__"
```

If the file is imported by another file, Python sets:

  ```python
__name__ = "ice_violence_queries"
```
