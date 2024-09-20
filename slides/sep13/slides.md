---
theme: seriph
title: September 13th, 2024
---

# September 13th, 2024

Memory-viz & PyTA

---
layout: quote
---

# The Task - Memory-viz

Investigate the behavior of the `--output` flag and ensure it is working as expected.

The `--output=<path>` flag takes in a path, and writes the output of the generated memory model to the path.

```bash
$ ls
test.json
$ npx memory-viz test.json --output=output.svg
$ ls
test.json output.svg
```

---

# Plan

1. Investigate the current behavior
2. Clarify the expected behavior
3. Write tests for the expected behavior
4. Make changes to the code to ensure tests pass

---

# The Current Behavior

1. Unable to create an output in the current directory
```bash
$ npx memory-viz test.json --output=test.svg
Error: Output path <pwd>/test.svg does not exist.
```
2. When the output is a directory, the output file takes the name of the directory
```bash
$ npx memory-viz test.json --output=./directory
$ ls directory
directory.svg
```
3. Unable to create an output in a directory when the path is a file
```bash
$ ls
dir/
$  npx memory-viz test.json --output=dir/output.svg 
Error: Output path <pwd>/dir/output.svg does not exist.
```

---
layout: quote
---

# Expected Behavior
- Only allow user to specify a file to a path
  - `./<filename>`, `./<directory>/<filename>`
- Users are not allowed to use a directory as an input
  - paths such as `./<directory>` are not allowed
- If any directories in the path did not exit, throw an error
- If a file already exists, overwrite the original


---
layout: two-cols-header
---

# The Big Problem

::left::

**How to tell if a path is a file or a directory?**
- Usually, we use the `<dir>/` to indicate a directory, and `<filename>` to indicate a file
- On different OS, it may be different
- When using Node's built-in `path.parse`, it's not always clear what we are working with
```js
> path.parse('./fileOrDir')
{ root: '', dir: '.', base: 'file', ext: '', name: 'file' }
> path.parse('./dir/')
{ root: '', dir: '.', base: 'dir', ext: '', name: 'dir' }
> path.parse('fileOrDir')
{ root: '', dir: '', base: 'fileOrDir', ext: '', name: 'fileOrDir' }
> path.parse('./file.svg')
{ root: '', dir: '.', base: 'file.svg', ext: '.svg', name: 'file' }
```

- `path.parse` by itself is not enough to tell us if the input is a directory or a file

::right::

**The solution**
- Use file system functions to check for the path
- `fs.statSync(path).isDirectory()` will tell you if a path is a directory


---

# Challenges & Learnings
- Trying TDD, spent a lot of time understanding the exists test, and writing new ones
- Learning the outputs of Node's `path`
- Figure out how to use the fs functions to check if the path is a file or a directory

---

# Work Done
1. Fixed the flag so it works as the requirement
2. Added tests to ensure the fix works
3. Added documentation to clarify the correct usage of the flag
4. Made a PR and merged it

https://github.com/david-yz-liu/memory-viz/pull/84

---

# Summary
1. Identified three issues with the `--output` flag
2. Clarified the requirements with David 
3. Added tests for the requirements
4. Made PR & addressed comments

---

# PyTA Investigations
- Look into how the `snapshot` function works
- Look into how the `accumulation_table` works

---

# Challenges so far 
- PyCharm debugger is not working! :(
  - Demo
  - If you had this error before please let me know!

---
