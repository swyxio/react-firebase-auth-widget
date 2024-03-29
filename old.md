# React Identity Widget

This is a React port of https://github.com/netlify/netlify-identity-widget, that is able to connect to either Firebase or Netlify Identity with a drop in user interface.

## demo

no demo yet but the source is in `/examples`.

## usage

we require some peer dependencies:

```bash
yarn add react-netlify-identity-widget @reach/dialog @reach/tabs @reach/visually-hidden
```

and the styles are optional but provided. here's how to use `IdentityModal, useIdentityContext, IdentityContextProvider`:

```tsx
import React from 'react'
import './App.css'
import IdentityModal, { useIdentityContext, IdentityContextProvider } from 'react-netlify-identity-widget'
import 'react-netlify-identity-widget/styles.css'

function App() {
  const url = 'https://your-identity-instance.netlify.com/' // supply the url of your Netlify site instance. VERY IMPORTANT. no point putting in env var since this is public anyway
  return (
    <IdentityContextProvider url={url}>
      <AuthStatusView />
    </IdentityContextProvider>
  )
}
export default App

function AuthStatusView() {
  const identity = useIdentityContext()
  const [dialog, setDialog] = React.useState(false)
  const name =
    (identity && identity.user && identity.user.user_metadata && identity.user.user_metadata.name) || 'NoName'
  const isLoggedIn = identity && identity.isLoggedIn
  return (
    <div>
      <div>
        <button className="btn" onClick={() => setDialog(true)}>
          {isLoggedIn ? `Hello ${name}, Log out here!` : 'Log In'}
        </button>
      </div>
      <IdentityModal showDialog={dialog} onCloseDialog={() => setDialog(false)} />
    </div>
  )
}
```

You may also code split the Modal if you wish with `React.lazy` and `React.Suspense`:

```js
// code split the modal til you need it!
const IdentityModal = React.lazy(() => import('react-netlify-identity-widget'))

function AuthStatusView() {
  // ...
  return (
    <div className="App">
      {/** ... */}
      <React.Suspense fallback="loading...">
        <IdentityModal showDialog={dialog} onCloseDialog={() => setDialog(false)} />
      </React.Suspense>
    </div>
  )
}
```

## Gatsby plugin

You may get a little help configuring RNIW for usage with Gatsby by using https://github.com/sw-yx/gatsby-plugin-netlify-identity. Read its README for more info.

## local dev

```bash
yarn
yarn build
yarn link
cd example
yarn && yarn link "react-netlify-identity"
yarn start # to see the example run
```

# tsdx bug

we use https://github.com/ds300/patch-package to patch this bug https://github.com/jaredpalmer/tsdx/issues/36

## List of Alternatives

**Lowest level JS Library**: If you want to use the official Javascript bindings to GoTrue, Netlify's underlying Identity service written in Go, use https://github.com/netlify/gotrue-js

**React bindings**: If you want a thin wrapper over Gotrue-js for React, `react-netlify-identity` is a "headless" library, meaning there is no UI exported and you will write your own UI to work with the authentication. https://github.com/sw-yx/react-netlify-identity. If you want a drop-in UI, there is yet another library that wraps `react-netlify-identity`: https://github.com/sw-yx/react-netlify-identity-widget

**High level overlay**: If you want a "widget" overlay that gives you a nice UI out of the box, with a somewhat larger bundle, check https://github.com/netlify/netlify-identity-widget

**High level popup**: If you want a popup window approach also with a nice UI out of the box, and don't mind the popup flow, check https://github.com/netlify/netlify-auth-providers
