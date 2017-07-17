# Reducing Redux

^Have you ever asked yourself "why does redux exist?" Maybe you'd say "oh, it makes state management easier. Or because it works well as model layer with react. Or it's a more "functional" way to do JS. WRONG.

^Show live demo of time travel debugging

^Redux exists because Dan Abramov thought this was cool. Seriously! Next slide.

---

# Why Redux Exists

> I wrote Redux...to implement logging, hot reloading, time travel, universal apps, record and replay, without any buy-in from the developer.
> -- Dan Abramov

^If you use Redux and you are not doing this EVERY DAY, and you are not having the time of your life, you are missing 90% of the value proposition of Redux. And I feel sorry for you.

---

# You Probably Don't Need Redux
Overview of talk

- What Redux was designed to do
- What it was not

<!-- redux seems Scary -->

---

# Design Goals

- Developer experience
- Easy to reason about
- Debuggable

---

# The Three Principles of Redux


---

# Single source of truth

The state of your whole application is stored in an object tree within a single store.

Global state and dataflow.  om. 

<!-- I like the idea of global state (on the `<App>` component or equivalent) -->

---

# State is read-only

The only way to change the state is to emit an action, an object describing what happened.

Immutability. Purity. Referential transparency.
<!-- I like the idea of dispatching actions to that global state and having it propagate down -->

---

# Changes are made with pure functions

To specify how the state tree is transformed by actions, you write pure reducers.

Reducers are pretty cool. Explore why.

Backbone demo

<!-- I am ok with the state being done with Reducers -->

---

# What Redux is not

<!-- but I also want (some of) the state to be offline... -->

http://codewinds.com/assets/article/redux-logic-nodevember.pdf#25

---

# Citations

http://redux.js.org/
http://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-1/
http://codewinds.com/assets/article/redux-logic-nodevember.pdf
