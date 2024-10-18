---
background: ../../IMG_3215.jpeg
---

<div class="absolute top-60">

# MemoryViz Oct 11th
</div>

<div class="absolute top-80">

### Adding Type Support

</div>

<div class="absolute bottom-10">

Leo

</div>

<div class="absolute bottom-10 right-10">

Image: Kelowna
</div>

---

# A Recap
- Worked on finishing `SnapshotTracer`

<br/>

# This Week
- Analyze the MemoryViz source code to identify improvements
  - Opened Issue: [PyTA & MemoryViz improvement discoveries #91](https://github.com/david-yz-liu/memory-viz/issues/91)
- Add typing to the MemoryViz source code

---

```
Uncaught TypeError: Cannot read properties of undefined (reading 'money')
```

---

# What is TypeScript?
- A superset of JavaScript: It is JavaScript with static type checking
- Offers type safety: You won't be able to compile the program if there are type errors

---

# Why TypeScript?

Type safety is very good for catching unexpected bugs

```javascript

function giveMoney(person, amount) {
    person.money += amount
}

```

A lot of things can go wrong with this very simple function

- What if person is `undefined`?

<div class="grid grid-cols-2 gap-4">
```javascript
giveMoney(undefined, 0)
```

```
Uncaught TypeError: Cannot read properties of undefined (reading 'money')
```
</div>

- What if `amount` is not a number?
<div class="grid grid-cols-2 gap-4">
```javascript
const person = { money: 1 }
giveMoney(person, "100")
person.money
```

```
'1100'
```
</div>

- What if instead of `person.money`, the field is actually called `person.balance`?

This can never happen with TypeScript (if you use it properly)


---

# Why TypeScript?

Let's try again with TypeScript

```typescript

type Person {
    name: string
    money: number
}

function giveMoney(person: Person, amount: number) {
    person.money += amount
}

```

TypeScript won't allow you to run the program!
<div>
```typescript
giveMoney(undefined, 0)
```
</div>
```typescript
giveMoney({}, 0)
```
```typescript
const person = {
    name: 'Leo',
    money: NaN
}
giveMoney(person, 100)
```


---

# A Practical Example
Imagine you are writing a React app and fetching from some API
```javascript
const result = await fetch('fakedata.com')
```

How do you know what comes back?

How do you ensure your component can handle the result?

With TypeScript, you can define what you *expect* to come back
```typescript
type Response {
    json: {
        ... some fields
    }
    ... some other stuff
}

const result = await fetch('fakedata.com') as Partial<Response>
if (result.json) {  // TypeScript forces you to check json is a valid field
    ... do stuff
}
```


---

# Another Practical Example

- My current internship uses MongoDB. Unlike SQL, it has has no **schema** and it has no **migrations**. This means:
  - You don't know what fields your model has
  - You also don't know the types for these fields
  - In contrast, SQL has a schema with data types and migration files, so your data stays consistent

---

Image you are using JavaScript, and you have a Person model. In MongoDB, both instances below are valid instances of the Person:
<div class="grid grid-cols-2 gap-4">

```typescript
const person1 = {
    name: "Leo",
    money: 100
}
```

```typescript
const person2 = {
    name: "Leo",
    balance: 100
}
```

</div>

- Without a schema, you really don't know what the database will return
- This makes it extremely easy to have `undefined` errors
- Since you are using JavaScript, every time you use a `Person`, you are guessing what fields the returned `Person` would have. **It only takes one missed check to crash your program**
- Now imagine a model with 200+ fields!

<br />

### The Solution?
<div class="grid grid-cols-2 gap-4">

<div>

Use TypeScript!
- Now, it's impossible for you to access a field that does not exist. You will be forced to handle it.
</div>

```typescript
type Person {
    name?: string
    money?: number
    balance?: number
}
```

</div>

---

# Impact on Codebase

MemoryViz has one user facing API `draw`: 

```javascript
function draw(objects, automation, configuration): MemoryModel
```

Without a clear and precise documentation, it's hard to know what you should pass for these parameters

However, if I just gave you the types, it suddenly become a lot easier. It can *almost* replace the need for documentation 
```typescript
type DrawnEntity {
    name?: string;
    type?: string;
    x?: number;
    y?: number;
    id?: number | null;
    value?: any;
    style?: Styles;
    height?: number;
    width?: number;
    ... other fields
}
```
With changes like this throughout the codebase, it's easy to use the API, and easier to build the API.


---

# Learnings & Challenges
- Learning the codebase to understand how to type the variables and parameters

---

# Next Week
- Address some comments on the [PR for adding the TypeScript types](https://github.com/david-yz-liu/memory-viz/pull/92)


--- 


# Maybe you wonder...

- Cons for TypeScript
- Why the background image is Kelowna
