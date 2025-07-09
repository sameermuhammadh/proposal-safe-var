# Proposal: `safe-var` (Stage 0)

## 🧠 Summary

Introduce a new directive: `"use safe-var"` that enables a safer behavior for `var` declarations by preventing access before initialization — effectively placing `var` in a Temporal Dead Zone (TDZ), similar to `let` and `const`.

This improves clarity, safety, and education without breaking backward compatibility.

---

## ✨ Motivation

The `var` keyword, while still valid in JavaScript, has long been a source of confusion due to its hoisting behavior:

```js
console.log(x); // ✅ undefined — unintuitive for many
var x = 10;
```

This behavior:

- Introduces hidden bugs
- Confuses beginners
- Breaks expectations around scope safety

In contrast, `let` and `const` use TDZ (Temporal Dead Zone), which throws a `ReferenceError` if accessed before declaration:

```js
console.log(y); // ❌ ReferenceError
let y = 10;
```

We propose an opt-in mode to bring `var` behavior in line with `let`/`const`, under a new directive: `"use safe-var"`.

---

## ✅ Proposal: `"use safe-var"`

When a file or function includes the directive `"use safe-var"`, all `var` declarations:

- Are \*\*not initialized to \*\*\`\` at the top of their scope
- Are placed in a **temporal dead zone (TDZ)**
- \*\*Throw a \*\*\`\` if accessed before initialization

### 🔍 Example

```js
"use safe-var";

console.log(a); // ❌ ReferenceError: Cannot access 'a' before initialization
var a = 100;
```

### 🔁 Without `safe-var`

```js
console.log(a); // ✅ undefined
var a = 100;
```

---

## 🧪 Behavior Changes Summary

| Feature            | `var` (default)             | `var` with `"use safe-var"`         |
| ------------------ | --------------------------- | ----------------------------------- |
| Hoisting           | Yes                         | Declaration is hoisted, but no init |
| Init Value         | `undefined`                 | No init until line of declaration   |
| Access Before Init | Allowed (gives `undefined`) | ❌ ReferenceError                    |
| Scope              | Function                    | Function                            |

---

## 💡 Why This Matters

- **Bridges the mental gap** between `var`, `let`, and `const`
- Helps beginners avoid silent bugs
- Enables safer legacy code migration
- Still supports function scoping (no block scope added)
- Purely opt-in — no breaking changes

---

## 🔧 Syntax and Semantics

### Syntax

```js
"use safe-var";
```

- Only valid at the top of scripts or function bodies

### Semantics

- Declaration of `var` is hoisted
- Initialization is deferred until runtime line of `var x = ...`
- Any access before that line throws a `ReferenceError`

---

## 🌐 Adoption Strategy

- Use `"use safe-var"` in new codebases or educational environments
- Tooling/linting support can auto-insert directive for new files
- Gradual migration for older projects wanting safer `var`

---

## 🔮 Future Ideas (Optional Extensions)

- **Loose TDZ mode**: Emits a console warning instead of an error
- \*\*New keyword \*\*\`\`: A hybrid scoped version of `var`
- **Enable in modules automatically** (optional future enhancement)

---

## 📦 Implementation Notes

- TDZ support already exists in JS engines for `let` and `const`
- Could reuse internal TDZ tracking for `var` when directive is enabled

---

## 📚 Historical Context: Why TDZ Exists but `var` Gets `undefined`

> 💡 "`var` was created before TDZ even existed. In ES3/ES5, JavaScript wasn’t strict — it quietly gave you `undefined` when accessing variables too early.\
> But this silent behavior caused too many bugs, even if it *seemed* harmless.\
> That’s why TDZ (Temporal Dead Zone) was introduced in ES6 — to protect you from accessing variables before they’re truly ready."
>
> Changing how `var` behaves today would break countless older websites.\
> That’s why `"use safe-var"` is **opt-in** — a bridge from legacy behavior to modern safety.

This is not just a safety improvement — it’s a **mental clarity win** for developers and learners alike.

---

## 📣 Call to Community

We invite community and engine contributors to explore this proposal and help shape it further.

This aims to give `var` a safe path forward — without breaking history, and without forcing critical thinking where simplicity is better.

---

## 🧑‍💻 Authors & Contributors

- **Shahaadh** (proposer)
- GPT-4 (assistive drafting)

---

## 🏁 Status

- Stage: **0** (Strawman)
- Open for discussion, community feedback, and champion nomination

