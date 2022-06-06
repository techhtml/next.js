---
description: Next.js supports including CSS files as Global CSS or CSS Modules, using `styled-jsx` for CSS-in-JS, or any other CSS-in-JS solution! Learn more here.
---

# 빌트인 CSS 지원

<details open>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/basic-css">Basic CSS Example</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-tailwindcss">With Tailwind CSS</a></li>
  </ul>
</details>

Next.js에서는 JavaScript 파일로부터 CSS 파일을 불러올 수 있습니다. 이는 Next.js가 [`import`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/import)의 개념을 JavaScript 너머로 확장했기 때문입니다.

## 전역적 스타일 시트 추가하기

애플리케이션에 스타일 시트를 추가하려면, `pages/_app.js` 내에 CSS 파일을 불러오십시오.

예를 들어, 다음과 같은 `styles.css`라는 스타일 시트를 추가한다고 가정해 보겠습니다.

```css
body {
  font-family: 'SF Pro Text', 'SF Pro Icons', 'Helvetica Neue', 'Helvetica',
    'Arial', sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

[`pages/_app.js` 파일](/docs/advanced-features/custom-app.md)이 없다면 이를 생성합니다.
다음으로, `styles.css` 파일을 [`불러옵`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)니다.

```jsx
import '../styles.css'

// This default export is required in a new `pages/_app.js` file.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

이 스타일(`styles.css`)은 애플리케이션의 모든 페이지 및 컴포넌트에 적용될 것입니다.
스타일 시트의 전역적 속성 때문에, 충돌을 피하기 위해서는 스타일을 **[`pages/_app.js`](/docs/advanced-features/custom-app.md) 안에서만 불러와야** 합니다.

개발 단계에서, 스타일 시트를 이런 식으로 표현하면 스타일을 편집할 때 핫 리로드(hot reload)됩니다. 즉, 애플리케이션 상태를 유지할 수 있습니다.

실제 제품에서, 모든 CSS 파일은 자동으로 축소된 하나의 `.css` 파일로 연결됩니다.

### `node_modules`로부터 스타일 불러오기

Next.js 9.5.4 버전부터는 `node_modules`에서 CSS 파일을 불러오는 것이 애플리케이션 어디에서든 허용됩니다.

`bootstrap` 또는 `nprogress`와 같은 전역적 스타일 시트의 경우, `pages/_app.js` 내에서 파일을 불러와야 합니다.
예를 들어:

For global stylesheets, like `bootstrap` or `nprogress`, you should import the file inside `pages/_app.js`.
For example:

```jsx
// pages/_app.js
import 'bootstrap/dist/css/bootstrap.css'

export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

서드 파티 컴포넌트에서 필요한 CSS 파일을 불러오기 위해서는 다음과 같이 할 수 있습니다.
예를 들어:


```tsx
// components/ExampleDialog.js
import { useState } from 'react'
import { Dialog } from '@reach/dialog'
import VisuallyHidden from '@reach/visually-hidden'
import '@reach/dialog/styles.css'

function ExampleDialog(props) {
  const [showDialog, setShowDialog] = useState(false)
  const open = () => setShowDialog(true)
  const close = () => setShowDialog(false)

  return (
    <div>
      <button onClick={open}>Open Dialog</button>
      <Dialog isOpen={showDialog} onDismiss={close}>
        <button className="close-button" onClick={close}>
          <VisuallyHidden>Close</VisuallyHidden>
          <span aria-hidden>×</span>
        </button>
        <p>Hello there. I am a dialog</p>
      </Dialog>
    </div>
  )
}
```

## 컴포넌트 레벨 CSS 추가하기

Next.js는 `[name].module.css` 파일 네이밍 컨벤션을 사용하여 [CSS 모듈](https://github.com/css-modules/css-modules)을 지원합니다.

CSS 모듈은 고유한 클래스 이름을 자동으로 생성하여 CSS의 범위를 로컬로 지정합니다.
이렇게 하면 충돌에 대한 걱정 없이 다른 파일에서 동일한 CSS 클래스 이름을 사용할 수 있습니다.

이러한 특성 때문에 CSS 모듈은 컴포넌트 레벨 CSS를 포함하는 이상적인 방법입니다.
CSS 모듈 파일은 **애플리케이션의 어느 곳에서나 가져올 수 있습니다**.

예를 들어, `components/` 폴더 내의 재사용 가능한 `Button` 컴포넌트를 가정해 보겠습니다.

첫째로, 다음 내용과 함께 `components/Button.module.css`를 생성합니다.

```css
/*
You do not need to worry about .error {} colliding with any other `.css` or
`.module.css` files!
*/
.error {
  color: white;
  background-color: red;
}
```

다음으로, 위의 CSS 파일을 불러와서 사용하는 `components/Button.js`를 생성합니다.

```jsx
import styles from './Button.module.css'

export function Button() {
  return (
    <button
      type="button"
      // Note how the "error" class is accessed as a property on the imported
      // `styles` object.
      className={styles.error}
    >
      Destroy
    </button>
  )
}
```

CSS 모듈은 _선택적 기능_ 이며 **`.module.css` 확장자로 끝나는 파일이 함께 있어야만 가능**합니다.
일반적인 `<link>` 스타일 시트와 전역 CSS 파일 또한 지원됩니다.

실제 제품에서, 모든 CSS 모듈 파일은 자동으로 축소 및 코드 분할된 여러 개의 `.css` 파일로 연결됩니다.
이 `.css` 파일들은 애플리케이션의 hot execution path를 나타내므로, 애플리케이션이 페인트(paint) 할 수 있는 최소한의 CSS가 로딩되는 것을 보장합니다.

## Sass 지원

Next.js는 `.scss` 와 `.sass` 확장자 파일을 사용하여 Sass를 불러옵니다.
CSS 모듈과 `.module.scss` 또는 `.module.sass` 확장자 파일을 통해 컴포넌트 레벨 Sass를 사용할 수 있습니다.

Next.js의 빌트인 Sass 지원을 사용하기 전에, [`sass`](https://github.com/sass/sass)를 설치했는지 확인하십시오:

```bash
npm install --save-dev sass
```

Sass 지원은 위에서 서술한 빌트인 CSS 지원과 동일한 이점과 제한이 있습니다.

> **주의**: Sass는 [두개의 다른 문법](https://sass-lang.com/documentation/syntax)을 지원합니다.
> `.scss` 확장자에서는 [SCSS 문법](https://sass-lang.com/documentation/syntax#scss)을 사용해야 하고,
> `.sass` 확장자에서는 [들여쓰기 문법 ("Sass")](https://sass-lang.com/documentation/syntax#the-indented-syntax)을 사용해야 합니다.
>
> 어떤 것을 선택해야 할지 잘 모르겠다면 CSS의 상위 집합인 `.scss` 확장자로 시작하세요. 들여쓰기 문법("Sass")을 배우지 않아도 됩니다.

### Sass 옵션 커스터마이징하기

Sass 컴파일러를 정의하고 싶다면 `next.config.js` 내에 `sassOptions`을 사용하면 됩니다.

`includePaths`를 추가하는 예시입니다:

```js
const path = require('path')

module.exports = {
  sassOptions: {
    includePaths: [path.join(__dirname, 'styles')],
  },
}
```

### Sass 변수

Next.js는 CSS 모듈 파일에서 내보낸 Sass 변수를 지원합니다.

내보낸 `primaryColor` Sass 변수를 사용하는 예제:

```scss
/* variables.module.scss */
$primary-color: #64FF00

:export {
  primaryColor: $primary-color
}
```

```js
// pages/_app.js
import variables from '../styles/variables.module.scss'

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout color={variables.primaryColor}>
      <Component {...pageProps} />
    </Layout>
  )
}
```

## CSS-in-JS

<details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styled-jsx">Styled JSX</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styled-components">Styled Components</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-emotion">Emotion</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-linaria">Linaria</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-tailwindcss-emotion">Tailwind CSS + Emotion</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styletron">Styletron</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-cxs">Cxs</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-aphrodite">Aphrodite</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-fela">Fela</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-stitches">Stitches</a></li>
  </ul>
</details>


기존 CSS-in-JS 솔루션 어떤 것이든 사용할 수 있습니다.
가장 간단한 예시는 인라인 스타일입니다:

```jsx
function HiThere() {
  return <p style={{ color: 'red' }}>hi there</p>
}

export default HiThere
```

고립된 범위의 CSS에 대한 지원을 제공하기 위해 [styled-jsx](https://github.com/vercel/styled-jsx)를 번들로 제공합니다.
목표는 웹 컴포넌트와 비슷한 "섀도우 CSS"를 제공하는 것이지만, 유감스럽게도 서버 렌더링을 지원하지 않으며 JS 전용입니다.

다른 인기 있는 CSS-in-JS 솔루션(예: Styled Components)에 대한 위의 예를 참조하세요.

`styled-jsx`를 사용하는 컴포넌트는 다음과 같이 생겼습니다:

```jsx
function HelloWorld() {
  return (
    <div>
      Hello world
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  )
}

export default HelloWorld
```

더 많은 예시는 [styled-jsx 문서](https://github.com/vercel/styled-jsx)를 참조하세요.

## FAQ

### JavaScript가 비활성화되어도 작동하나요?

네, JavaScript가 비활성화되더라도 CSS는 프로덕션 빌드(`next start`)에서 로딩됩니다. [Fast Refresh](https://nextjs.org/blog/next-9-4#fast-refresh)와 함께 최고의 개발자 경험을 제공하기 위해, 개발하는 동안 JavaScript를 활성화시키는 것을 권장합니다.

## 관련 정보

다음으로 무엇을 해야 하는지에 대해 더 알고 싶으시다면, 다음의 섹션을 추천드립니다:

<div class="card">
  <a href="/docs/advanced-features/customizing-postcss-config.md">
    <b>PostCSS Config 커스터마이징하기:</b>
    <small>Next.js에서 추가한 PostCSS config 및 플러그인을 나만의 것으로 확장하세요.</small>
  </a>
</div>
