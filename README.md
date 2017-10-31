# React: The Wrong Way

This is a few patterns I have seen never implemented inside react libraries or examples, and so I figured it would 
be pretty funny to point them out as the wrong way to use React.

## 1.

Mutating State Directly

```Javascript
class Input extends React.Component {
  state = {
    value: ''
  }
  updateValue(val) {
    this.state.value += val;
    this.forceUpdate();
  }
  render() {
    return (
      <input
        type="text"
        value={this.state.value}
        onChange={e => this.updateValue(e.target.value)}
      />
    )
  }
}
```

## 2.

Keeping state low

```Javascript
class Form extends React.Component {
  handleFormUpload() {
    const { value: name } = this.name.state;
    const { value: password } = this.password.state;
    window.alert(`Name: ${name}, password: ${password}`);
  }
  render() {
    return (
      <div>
        <Input type="text" ref={comp => this.name = comp} />
        <Input type="password" ref={comp => this.password = comp} />
        <button onClick={e => this.handleFormUpload()}>Login</button>
      </div>
    )
  }
}

class Input extends React.Component {
  state = { value: '' }
  render() {
    return (
      <input
        type={this.props.type}
        value={this.state.value}
        onChange={e => this.setState({ value: e.target.value })}
      />
    )
  }
}
```

## 3.

Inheritance over Composition

```Javascript
class Component extends React.Component {
  state = {}
  updateState = updater => {
    this.setState(updater);
  }
  getState = () => {
    return this.state;
  }
  render() {
    return this.props.children;
  }
}

class Input extends Component {
  handleChange = e => {
    const value = e.target.value
    this.updateState(() => ({value}));
  }
  render() {
    return (
      <input
        type="text"
        value={this.getState().value || ''}
        onChange={this.handleChange}
      />
    )
  }
}
```

## 4.

Using React to manage only state and not render html

```jsx
let input

const makeInput = ({ value, onChange, disabled = false }) => {
  if (!input) {
    input = document.body.appendChild(document.createElement('input'))
    input.addEventListener('input', onChange)
  }
  input.value = value
  input.disabled = disabled
}

class Input extends React.Component {
  state = {
    value: '',
  }
  handleInputChange = e => {
    const value = e.target.value
    this.setState(() => ({ value }))
  }
  render() {
    makeInput({
      value: this.state.value,
      onChange: this.handleInputChange,
      disabled: this.state.value.length > 10,
    })
    return null
  }
}
```