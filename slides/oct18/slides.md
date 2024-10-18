---
background: ./IMG_4127.jpeg
---

<div class="absolute top-60">

# MemoryViz Oct 18th
</div>

<div class="absolute top-80">

### MemoryViz Bug Fixes

</div>

<div class="absolute bottom-10">

Leo

</div>

<div class="absolute bottom-10 right-10">

Image: Mount Fuji
</div>
---

## Progress

<img src="./backlog.png">

---

# String Exceeding Box

### Issue 1: With the highlight style, the text will exceed the box
<img src="./long-string-highlight-before.svg">

### Issue 2: With the highlight style, the quotations are hidden
<img src="./quotation-issue.png">

---

# Writing Tests
1. The text will not exceed the box with any styles
2. The quotations will not exceed the box with any styles

---

# Debugging and the issues
<img src="./drawobject.png">

---

# Debugging and the issues
```js
 /**
  * Return the length of this text.
  * @param s - The given text.
  */
 getTextLength(s: string): number {
     return s.length * 12;
 }
```

<v-clicks>

## Recall the issues:
1. With certain styles, the text will exceed the box
2. With certain styles, the quotations are hidden

Do you notice anything missing in this function? 
- Multiplier is hard coded to 12, regardless of font size
- The quotations are not a part of `s`, so their length was ignored


</v-clicks>

---

# Debugging and the issues

## Change 1: Take into account the style
```js {monaco-diff}
getTextLength(s: string): number {
   return s.length * 12;
}
~~~
getTextLength(s: string, textStyle?: CSS.PropertiesHyphen): number {
    if (textStyle?.["font-size"]) {
        return s.length * parseInt(textStyle["font-size"]) * 0.6;
    }
    return s.length * 12;
}
```

---

# Debugging and the issues

## Change 2: Account for the quotations
```js {monaco-diff}
const value = "David is Cool!";

getTextLength(`${String(value)}`)
~~~
const value = "David is Cool!";

getTextLength(`"${String(value)}"`)
```

---

# Results

## Before:
<img src="./long-string-highlight-before.svg">


## After:
<img src="./long-string-highlight-after.svg">

---

# Results

## Before:
<img src="./quotation-issue.png">


## After:
<img src="./quotation-fixed.png">

---

# The Challenge? - Demo

---

# Empty JSON Error