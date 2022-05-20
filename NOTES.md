# Notes

## Very cool way to prepare tests

```jsx
function renderApp() {
  const utils = render(
    <AuthProvider user={{user: mockUser}}>
      <App />
    </AuthProvider>,
  )

  const userDisplayPre = utils.container.querySelector('pre')
  return {
    ...utils,
    submitButton: screen.getByText(/✔/),
    resetButton: screen.getByText(/reset/i),
    taglineInput: screen.getByLabelText(/tagline/i),
    bioInput: screen.getByLabelText(/bio/i),
    waitForLoading: () =>
      waitForElementToBeRemoved(() => screen.getByText(/\.\.\./i)),
    userDisplayPre,
    getDisplayData: () => JSON.parse(userDisplayPre.textContent),
  }
}
```

## The Context Module Functions Pattern

Allows you to encapsulate a complex set of state changes into a
utility function which can be tree-shaken and lazily loaded.

Encapsulating Context state changing functions within context module / file.
If using reducer, expecting 'dispatch' to be passed as parameter to helper function

```jsx
function updateUser(dispatch, user, updates) {
  dispatch({type: 'start update', updates})
  userClient.updateUser(user, updates).then(
    updatedUser => dispatch({type: 'finish update', updatedUser}),
    error => dispatch({type: 'fail update', error}),
  )
}
```

## The Compound (composite) Components Pattern

The Compound Components Pattern enables you to provide a set of
components that implicitly share state for a simple yet powerful declarative API
for reusable components.

This way of composing exposes state and callbacks to all children,
allowing us to craft custom UI components that are extendable

```jsx
function Toggle({children}) {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)

  return React.Children.map(children, child =>
    React.cloneElement(child, {on, toggle})
  )
}

// Later we can use Toggle to only hold the state and logic
  return (
    <Toggle>
      <ToggleOn>The button is on</ToggleOn>
      <ToggleOff>The button is off</ToggleOff>
      <span>Hello</span>
      <ToggleButton style={{background: 'green'}} />
    </Toggle>
  )
```

OR...
We can use React.Context to be able to retrieve state and logic
no matter how the 'deeply-nested' components are.

```jsx
function Toggle({children}) {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)

  return (
    <ToggleContext.Provider value={{on, toggle}}>
      {children}
    </ToggleContext.Provider>
  )
}

// Then inside children components
function ToggleButton({...props}) {
  const {on, toggle} = useToggle()
  return <Switch on={on} onClick={toggle} {...props} />
}
```
