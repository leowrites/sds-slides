---
theme: apple-basic
layout: intro-image
image: https://images.unsplash.com/photo-1590647030647-0bc9c30cc07e?q=80&w=1760&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
---

<div class="absolute top-10">
  <span class="font-700">
    Leo, September 20th, 2024
  </span>
</div>

<div class="absolute bottom-10">
  <h1>Week 2 Presentation</h1>
  <p>Adventures in PyTA</p>
</div>


---

# Table of content
<Toc maxDepth="1" mode="onlySiblings"/>


---

# A Recap

<div v-click.hide = '1'>

- Clarified behaviors on `--output` and made corrections
</div>

- Began looking into how the `snapshot` function works

- Began looking into how the `AccumulationTable` works

<div v-click.hide = '1'>

- Got an error when running the PyCharm debugger on tests!
  - If you get an error, try updating PyCharm
</div>

---

# PyTA Investigations

Some questions for the two functions to investigate:

`snapshot`
<v-clicks depth="2">

- What does `snapshot` do?
  - Captures all variables in the current stack and optionally stores them in a JSON file
- What are the limitations of this function?
  - It works pretty well in general, but it can error when a value cannot be serialized
- What are the technical details I should know?
  - All the information we want is stored inside a `frame` object, which you can obtain through the `inspect` module

</v-clicks>

---
layout: my-two-col
---

::left::

## Input

```python
str1 = "Hello World!"
arr = [1, 2, 3, 4]

snapshot()
```

<br/>

::right::

## Output

```json
[
    {
        "__main__": {
            "str1": "Hello World!"
            "arr": [1, 2, 3, 4]
        }
    }
]
```


---

`AccumulationTable`
<v-clicks depth=2>

- What is the `AccumulationTable`?
  - It is a context manager that tracks the changes in the loop variables and/or loop accumulators

Context manager?
  - What is it?
    - It is a wrapper to manage your resources, they are especially useful if you have some setup and teardown code
    - Everything a context manager can do can be done with a `try` and `finally` block
  - Why do we need a context manager?
    - It's nicer to use and more readable than using `try` and `finally` blocks

</v-clicks>

---

## Examples

### Context Manager with a `try` and `finally` block
```python {all|2-3|4-5|all} twoslash
file = open('some_file', 'w')
try:
    file.write('Hola!')
finally:
    file.close()
```

<br/>


### Context Manager with a `with` block
```python {all|4-5|6-7|9-10|all}
class File(object):
    def __init__(self, file_name, method):
        self.file_obj = open(file_name, method)
    def __enter__(self):
        return self.file_obj
    def __exit__(self, type, value, traceback):
        self.file_obj.close()

with File('some_file', 'w') as opened_file:
    opened_file.write('Hola!')
```

Source: [Python Tips](https://book.pythontips.com/en/latest/context_managers.html)

---

## Anything else to learn from `AccumulationTable`?

<v-clicks>

How do we actually track the changes in the loop variables and/or loop accumulators?
  - `AccumulationTable` used a custom *trace function* using `frame.f_trace`

```python
def _trace_loop(self, frame: types.FrameType, event: str, _arg: Any) -> None:
    if event == "line" and frame.f_lineno == self._loop_lineno:
        self._record_iteration(frame)

current_frame.f_trace = _trace_loop
```

- With this function, we can run some code on each line of code (such as tracking changes in the loop variables)
- This is very useful for my task this week >>

</v-clicks>

---

# The New Task

<v-clicks>

#### **Create a new context manager that would:**
- call `snapshot` on every single line of code.
- output a memory diagram of the stack at that line.

#### **Thinking back to `snapshot` and `AccumulationTable`...**

- `snapshot` lets us take a snapshot of the stack at a given point
- `frame.f_trace` lets us use a custom trace function to run `snapshot` on every line of code
- Context manager lets us manage the setup and teardown of the trace function
  - This ensures the trace function is only active when we want it to be (a.k.a. within the `with` block)

#### **Questions**
- The call stack can be deep, what variables do we want to capture?
   - All variables in the current stack
- If there are function calls in the `with` block, should we step into it?
   - Yes, but it will be for the future

</v-clicks>

---

# Initial Tests
- The context manager can be called
- The context manager can capture one primitive variable
- The context manager can capture multiple primitive variables
- The context manager can capture a list of primitive values
- The context manager can capture a list of variables
- The context manager can capture mutations of a variable
- The context manager can capture mutations of a list

---

# Progress

- Created the context manager such that:
   - It calls `snapshot` on every line of code
   - It outputs a memory diagram when `snapshot` is called

- The context manager passes the tests mentioned previously

Demo

---

# Challenges

<v-clicks depth=2>

- Debugging the trace function
  - The PyCharm debugger uses the trace function. If you overwrite it, it will not longer work
  - This is also why it was important to clean up the trace function after the `with` block!
- Understanding the limitations of the `snapshot` function
  - It dumps the snapshot into a JSON file, but not all values can be serialized
  - TODO: make `snapshot` handle values that cannot be JSON serialized
- Writing tests in separate files to avoid capturing variables from the testing library
- Coming up with a name for the context manager

</v-clicks>


---

# Lessons
<v-clicks depth=2>

- What is a context manager and how to create your own
- How to use the `inspect` function to get information about the stack
- How to use `frame.f_trace` to run some code on each line
- A benefit of TDD:
  - I wasn't sure if the existing code worked with loops. By writing tests first, I verified it worked without any modifications to the code

</v-clicks depth>

--- 

# Weekly Summary

<br/>

## This week:
- Investigated how `snapshot` and `AccumulationTable` work
- Created a new context manager that calls `snapshot` on every line of code
- Passed initial tests for the context manager
- Identified challenges and lessons learned

<br/>

## Next week:
- Make a PR to let `snapshot` handle values that cannot be serialized
- Write more tests for the context manager and make a PR
- Examine the test results from Memory-viz and identify areas of improvements for Memory-viz


---

# Any Questions?

You might wonder...
- How do you use `inspect` to get information about the stack?
- How do you use `sys.settrace` to run some code on each line?
- If the trace function conflicts with the PyCharm debugger, how do you test/debug your code effectively?
- What happens if the snapshot function is run on a large or deeply nested data structure? Will performance be an issue?

If you are interested in technical details, please let me know!
