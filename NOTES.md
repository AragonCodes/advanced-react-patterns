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
