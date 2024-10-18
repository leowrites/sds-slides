---
theme: apple-basic
layout: intro-image
image: https://images.unsplash.com/photo-1726534168836-59dff8524925?q=80&w=3474&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
---

<div class="absolute top-10">
  <span class="font-700">
    Leo, September 27th, 2024
  </span>
</div>

<div class="absolute bottom-10">
  <h1>Week 3</h1>
  <p>Challenges in PyTA</p>
</div>

---
layout: two-cols
layoutClass: gap-16
---

# A Recap

<br/>
<v-click at="1">

## Last Week
- Created a new `SnapshotManager` that calls `snapshot` on every line of code
- Passed initial tests for the `SnapshotManager`
</v-click>

<v-click at="2">

## This week (The plan)
- Make a PR to let `snapshot` handle values that cannot be serialized
- Write more tests for the `SnapshotManager` and make a PR <v-click at="3"> (WIP) </v-click>
</v-click>


::right::

<v-click at="1">

<br/>
<br/>
<br/>

`snapshot`: A function that captures variables in the current stack.

</v-click>

---

# Progress
- Submitted and [merged the PR](https://github.com/pyta-uoft/pyta/pull/1085) for `snapshot`
- Encountered 3 challenges while finalizing the `SnapshotManager`
  - Snapshot testing
  - Inconsistent behavior for trace function across different Python versions
  - Failing tests


---

# Challenge 1: Perform snapshot testing

A way to test if the output of a function is consistent


<Transform scale="0.7">

![alt text](https://miro.medium.com/v2/resize:fit:4800/format:webp/1*jkHc9syW5rfNhoTZ55ysyQ.png)
</Transform>

---

# Challenge 1: Perform snapshot testing

A way to test if the output of a function is consistent

## The challenge

- Looked to use a library `pytest-snapshot` to do it
- For various reasons, thought it wasn't going to work with our particular use case
- Gave up and attempted to manually achieve snapshot testing
- Made every thing a lot more complicated than it had to be
- Talked to David
- Turned out we could use the library to simplify the tests significantly

--- 

# Challenge 1: Perform snapshot testing

A way to test if the output of a function is consistent

## The Lesson
If it's too complicated, you are probably overthinking it 

--- 

# Challenge 2: Inconsistent behaviors across different Python versions

Inconsistent behavior for trace function across different Python versions

## The challenge

Trace function:
```python {*}{lines:true}
def _trace_func(self, frame: types.FrameType, event: str, _arg: Any) -> None:
    if event == "line" and frame.f_locals:
        print(f'Tracing line {frame.f_lineno}')
```

> Reminder: a trace function executes on every event

Usage:
```python {*}{lines:true}
def func_multi_line(output_path=None) -> None:
    with SnapshotManager(output_filepath=output_path, include=("func_multi_line",)):
        num = 123
        dummy_line = "123"
```

---
layout: statement
---

# Question
What do you think will happen when `func_multi_line` runs?

<div style="text-align: start">
```python {*}{lines:true}
def _trace_func(self, frame: types.FrameType, event: str, _arg: Any) -> None:
    if event == "line" and frame.f_locals:
        print(f'Tracing line {frame.f_lineno}')
```

```python {*}{lines:true}
def func_multi_line(output_path=None) -> None:
    with SnapshotManager(output_filepath=output_path, include=("func_multi_line",)):
        num = 123
        dummy_line = "123"
```

</div>

<div style="text-align: start">

## Some options:
<div class="grid grid-cols-3 gap-4">
    <div>

Option 1:
```
Tracing line 2
Tracing line 3
Tracing line 4
```
    </div>
    <div>

Option 2: <v-click at="1">< Python 3.10</v-click>
```
Tracing line 3
Tracing line 4
```
    </div>
    <div>

Option 3: <v-click at="1">>= Python 3.10</v-click>
```
Tracing line 3
Tracing line 4
Tracing line 2
```
    </div>
</div>
</div>


---

# Challenge 2: Inconsistent behaviors across different Python versions

```python {*}{lines:true}
def func_multi_line(output_path=None) -> None:
    with SnapshotManager(output_filepath=output_path, include=("func_multi_line",)):
        num = 123
        dummy_line = "123"
```

But why does versions >= Python 3.10 go back to line 2?
- I'm not sure
- It seems like there is an inconsistency with line numbers when we are using a trace function
- This is could be fixed by an [issue](https://github.com/python/cpython/issues/86989) in Jan 2021 after the release of Python 3.9

Which correctly produces the output?
- Versions >= Python 3.10
- Versions < Python 3.10 does not include the last line where `dummy_line` is assigned
- The current solution is to only make the `SnapshotManager` available for Python versions >= 3.10

---

# Weekly Summary

<br/>

## This week
- Make a PR to let `snapshot` handle values that cannot be serialized
- Investigated three obstacles and wrote more tests

<br/>

## Next week:
- Request a review from David
- Examine the test results from Memory-viz and identify areas of improvements for Memory-viz


---

# Any Questions?

You might wonder...
- Why did you initially think pytest-snapshot wouldn't work for your use case?
- What is the significance of the line numbers in the trace function output?

If you are interested in technical details, please let me know!
