# Reducing Redux: Roll your own state management

^ Hi. My name is Tim and I'm a developer at Braintree in Chicago and today I'm going to talk to you about Redux.

^Now before I begin, how many people are actually using redux in production? Raise your hands and I'm going to ask you to keep them up. 

^Now how many really want to use redux, but haven't gotten the chance yet? Keep your hands up.

^Ok. And now how many people are really just trying to figure out what redux is but they're busy, so they want a stranger to tell them if they should? Great, now switch your hands!

^Ok. My goal is, by the end of this talk, is to give everyone with theirs hands up the tools to answer this question: 

---

# Should I use Redux?
^ Sound good? Now, you are probably holding up your not dominant hand. In celebration for the bright future ahead of us, everybody do your best to high five the person next to you

^Have you ever asked yourself "why does redux exist?" Maybe you'd say "oh, it makes state management easier. Or because it works well with react. Or it's a more "functional" way to do JS. WRONG.

^Show live demo of time travel debugging

^Redux exists because Dan Abramov thought this was cool. If you use Redux and you are not doing this EVERY DAY, and you are not having the time of your life, you are missing 90% of the value proposition of Redux. Now you think I'm joking but...

---

# Why Redux Exists

^ This is on the landing page of the redux website

> I wrote Redux...to implement logging, hot reloading, time travel, universal apps, record and replay, without any buy-in from the developer.
-- Dan Abramov

^ Now he also said some other things about it too, but notice that he didn't say "because this is the single best way to manage state". Or "because John from sales is breathing down my neck because his new account just sign a huge contract based on a feature that we need yesterday." Alright, that's not Dan's problem, John. But seriously, a lot of thought and care went into the design and it has clear goals that is trying to accomplish. That also means that it has things that it's not very concerned with at all. So if you've ever thought "redux sucks because it doesn't do X" or "i need all these extra addons to do anything", you're right. Be skeptical. Embrace that feeling because that means you've identified a design problem you have to solve. And you can only solve the problems you know you have.

---

# Evil and its roots

> ...premature optimization is the root of all evil.
-- Sir Tony Hoare

---

# Evil and its roots

^ Generalize that to

> Using the wrong tool for the job is the root of all evil.
-- Me

^ We're going to go through Redux and brake down what tools it offers us. And what you're going to notice is that all of this stuff is really straightforward and simple. Redux is only 500 lines of code. I counted last night on github. It's 536. So we're going to look at some simplified source code and discuss what it does, and then we're going to talk about how you can apply these same concepts to your own applications separately from Redux.

---

# The Three Principles of Redux

1. Single source of truth
2. State is read-only
3. Changes are made with pure functions

---

# Principle 1: Single source of truth

> The state of your whole application is stored in an object tree within a single store.

---

# Object tree

```javascript
{
  app: {
    offline: true
  },
  views: {
    sort: 'name' 
  },
  preferences: {
    theme: 'dark'
  },
  users: []
}
```

^  This concept is appealing to a lot of people. Om. Game/rendering engines. Easy to comprehend. Simple. Serializable

---

# createStore()

```javascript
function createStore(reducer) {
    var state;
    var listeners = []

    function getState() {
        return state
    }
    
    function subscribe(listener) {
        listeners.push(listener)
        return unsubscribe() {
            var index = listeners.indexOf(listener)
            listeners.splice(index, 1)
        }
    }
    
    function dispatch(action) {
        state = reducer(state, action)
        listeners.forEach(listener => listener())
    }

    dispatch({})

    return { dispatch, subscribe, getState }
}
```

---

# createStore()

```javascript
function createStore(reducer) {
  const state = reducer({}, {});
  
  return action => {
    state = reducer(state, action)
    
    return state
  }
}

const store = createStore(reducer) 
const nextState = store({type: 'ADD'}) // dispatch()
const currentState = store({}) // getState()
```

^ If you like the idea of reducers you don't care about listeners, you can implement this yourself in 5 lines of code. It just boils down to a partially applied function.

---

# Principle 2: State is read-only

> The only way to change the state is to emit an action, an object describing what happened.

---

# "Emitting" an "action"

```javascript
const action = {type: 'ADD', payload: 'totally optional'}
store.dispatch(action)
```

---

# Action creators and encapsulation

```javascript
const addUser = (first, last) => {
  const name = `${first} ${last}`
  
  return {
    type: 'ADD_USER',
    payload: {name}
  }
}
```

^ As we saw in the last slide, actions creators are not required by redux. They are, however, considered to be consistent with redux's design principles, so I wanted to bring them up.

^ What I like about them is they give you two things: an easy and concise way to generate actions and a place to put business logic. Remember that reducers have to be pure, so you if you want to add a date or a unique id to something, those are impure by definition. Action creators, on the other hand, can be whatever you want them to be. They're a helpful tool.

^ One of the biggest complaints about redux is the boilerplate that comes from actions and action creators. Some people have come up with ways to reduce the boilerplate, but it'll be on you to decide how to address it. When I'm working on app, the repetition from boilerplate is often very far down my list of problems, so this doesn't tend to bother me much. You may feel differently. If the boilerplate outweighs the benefits you get from action creators, the easiest solution is to not use them. Do what works for you.

---

# Principle 3: Changes are made with pure functions

> To specify how the state tree is transformed by actions, you write pure reducers.

---

# Function purity

```javascript
function pure(a, b) {
  return a + b
}

function impure(a, b) {
  return new Date(a)
}

async function impureRequest(url) {
  return await fetch(url)
}
```

^ This is the underpinning of what makes redux "easy to reason about". When you structure your state as the output of pure functions, your application, or at least parts of it, becomes deterministic. 

^ You can apply this anywhere, not just redux or state. Do this as much as humanly possible. Try to isolate your side effects in separate functions that do nothing but perform the side effect. It makes your more testable, portable, and comprehensible.

---

# What's a reducer?

```javascript
const reducer = (state, action) => state
```

---

# reduce()

^ Eric Elliott explanation

```javascript
[].reduce(reducer, initialState)

const reducer = (accumulator, value) => newAccumulator

const initialState = 0 // or {}, [], '', etc


const initialState = 0
const add = (a, b) => a + b

[1, 2, 3].reduce(add, 0) // 6


const actions = [
  { type: 'ADD', payload: 1 },
  { type: 'ADD', payload: 2 },
  { type: 'ADD', payload: 3 }
]

const reducer = (state, action) => {total: state.total + action.payload}

actions.reduce(reducer, {total: 0}) // {total: 6}
```

---

# combineReducers()

```javascript
const reducers = {
  users: reduceUsers,
  comments: reduceComments
}

function combineReducers(reducers) {
  var reducerKeys = Object.keys(reducers)

  return function combination(state, action) {
    var nextState = {}

    reducerKeys.forEach(key => {
      const reducer = reducers[key]

      nextState[key] = reducer(state[key], action)
    })

    return nextState;
  }
}

const reducer = combineReducers(reducers)
```

^ Modular, separates concerns, scales. If you need to add another branch to your state tree, it's just a matter of adding another reducer when you combine them. This isn't really life-changing, but it's a useful feature.

^ And that's it for the three principles. We're done. You now understand all the underlying concepts of redux. Single source of truth, state is read-only, changes are pure functions.

---

# Middleware and what Redux is not

> [Thunks were] literally the "I hope people will come up with something better" solution, didn't expect it to catch on so much
-- Dan Abramov

![inline 100%](/Users/tim/Projects/reducing-redux/redux-logic-nodevember-25.png)

^ Middleware exists in redux as a acknowledgement that there are some problems it doesn't have answers to.

^ The biggest problem that people find when they use redux is that it gives a place to put your state, but it doesn't tell you where to put the business logic that generates that state. State management is huge and as you saw, redux only attempts to address certain aspects of it.

^ This is such a big problem that if you take one thing away from this talk, it's that if you use redux and generally, for any application that you would consider complex, you need to devote time to this problem. If you don't do the work up front, you will definitely do it later down the road, and it's going to be a lot harder when you have to refactor half your app to make it work.

^ These are hard problems and there are no silver bullets. And It's never going to be perfect and that can feel daunting. But that's ok. We're making progress. This is way better than wrangling collections and models in giant backbone apps. And I know, I did it for years. You don't have to rely on other people's tools. Their tools are for their problems. So trust yourself. Use your tools. And make new ones to solve your own problems.

---

# Redux reduces to

- Object tree
- Single source of truth
- Encapsulation
- Partial application
- Pure functions
- Determinism
- Reduce

^ Starting with partial application, these are all functional programming concepts. They are not special to redux. Learn them. Use them. 

---

# Sources

- http://redux.js.org/
- http://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-1/
- http://codewinds.com/blog/2016-11-20-nodevember-redux-logic.html
- http://argelius.github.io/react-onsenui-redux-weather/
- https://medium.com/javascript-scene/10-tips-for-better-redux-architecture-69250425af44

---

# "You ~~Might Not~~ Probably Don't Need Redux"

Every tool you add to your project comes with a price: complexity

Good design is knowing the price of your tools and how to weigh the costs against the benefits

^ Hopefully you've learned how to do that a little bit better today

---

# Please clap
