---
description: Learn how to share components and state between Next.js pages with Layouts.
---

# Layouts

<details open>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/layout-component">layout-component</a></li>
  </ul>
</details>

React 모델은 우리가 하나의 [페이지](/docs/basic-features/pages.md)를 여러 컴포넌트로 분해할 수 있도록 해 줍니다. 이 컴포넌트들 중 다수는 페이지 사이에서 재사용됩니다. 예를 들어, 모든 페이지에서 동일한 네비게이션 바와 푸터를 사용할 수 있습니다.

```jsx
// components/layout.js

import Navbar from './navbar'
import Footer from './footer'

export default function Layout({ children }) {
  return (
    <>
      <Navbar />
      <main>{children}</main>
      <Footer />
    </>
  )
}
```

## 예제

### 커스텀 앱에서 하나의 레이아웃을 공유하는 경우

애플리케이션 전체에 단 하나의 레이아웃만 있다면, [커스텀 앱](/docs/advanced-features/custom-app.md)을 만들어 애플리케이션을 해당 레이아웃으로 감쌀 수 있습니다. `<Layout />` 컴포넌트는 페이지가 바뀔 때 재사용되기 때문에, 컴포넌트 상태(예: input 값들)는 유지될 것입니다.

```jsx
// pages/_app.js

import Layout from '../components/layout'

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  )
}
```

### 페이지 단위 레이아웃

다양한 레이아웃이 필요하다면, `getLayout` 속성을 페이지에 추가해 레이아웃을 위한 React 컴포넌트를 리턴하도록 할 수 있습니다. 이로 인해 _페이지 단위_로 레이아웃을 정의할 수 있습니다. 함수를 리턴하고 있기 때문에, 원한다면 복잡한 중첩 레이아웃을 만들 수도 있습니다.

```jsx
// pages/index.js

import Layout from '../components/layout'
import NestedLayout from '../components/nested-layout'

export default function Page() {
  return {
    /** Your content */
  }
}

Page.getLayout = function getLayout(page) {
  return (
    <Layout>
      <NestedLayout>{page}</NestedLayout>
    </Layout>
  )
}
```

```jsx
// pages/_app.js

export default function MyApp({ Component, pageProps }) {
  // Use the layout defined at the page level, if available
  const getLayout = Component.getLayout || ((page) => page)

  return getLayout(<Component {...pageProps} />)
}
```

페이지를 이동할 때, 우리는 싱글 페이지 애플리케이션 (SPA) 경험을 제공하기 위해 페이지 상태(input 값, 스크롤 위치 등)를 *유지*하고 싶습니다.

이 레이아웃 패턴은 상태를 지속할 수 있게 하는데, 페이지 전환 시 React 컴포넌트 트리가 관리되기 때문입니다. 컴포넌트 트리를 통해 React에서 상태를 유지하기 위해 어떤 요소가 변경되었는지 알 수 있습니다.

> **주의**: 이 과정은 [재조정](https://reactjs.org/docs/reconciliation.html)이라고 하며, React는 이 과정을 통해 어떤 요소들이 바뀌었는지 이해합니다.

### TypeScript 사용 시

TypeScript를 사용할 때, 페이지에서 먼저 `getLayout` 함수를 포함하는 새로운 타입을 생성해야 합니다. 그 다음에, 이전에 만든 타입을 사용하기 위해 `Component` 속성을 오버라이딩하는 `AppProps`에서 사용할 새로운 타입을 생성해야 합니다.

```tsx
// pages/index.tsx

import type { ReactElement } from 'react'
import Layout from '../components/layout'
import NestedLayout from '../components/nested-layout'

export default function Page() {
  return {
    /** Your content */
  }
}

Page.getLayout = function getLayout(page: ReactElement) {
  return (
    <Layout>
      <NestedLayout>{page}</NestedLayout>
    </Layout>
  )
}
```

```tsx
// pages/_app.tsx

import type { ReactElement, ReactNode } from 'react'
import type { NextPage } from 'next'
import type { AppProps } from 'next/app'

type NextPageWithLayout = NextPage & {
  getLayout?: (page: ReactElement) => ReactNode
}

type AppPropsWithLayout = AppProps & {
  Component: NextPageWithLayout
}

export default function MyApp({ Component, pageProps }: AppPropsWithLayout) {
  // Use the layout defined at the page level, if available
  const getLayout = Component.getLayout ?? ((page) => page)

  return getLayout(<Component {...pageProps} />)
}
```

### 데이터 Fetching

레이아웃 내에서 `useEffect` 또는 [SWR](https://swr.vercel.app/)과 같은 라이브러리를 사용하여 클라이언트 사이드에서 데이터를 fetch할 수 있습니다. 이 파일은 [페이지](/docs/basic-features/pages.md)가 아니기 때문에, 현재로서는 `getStaticProps` 또는 `getServerSideProps`를 사용할 수 없습니다.

```jsx
// components/layout.js

import useSWR from 'swr'
import Navbar from './navbar'
import Footer from './footer'

export default function Layout({ children }) {
  const { data, error } = useSWR('/api/navigation', fetcher)

  if (error) return <div>Failed to load</div>
  if (!data) return <div>Loading...</div>

  return (
    <>
      <Navbar links={data.links} />
      <main>{children}</main>
      <Footer />
    </>
  )
}
```

다음으로 무엇을 해야 하는지에 대해 더 알고 싶으시다면, 다음의 섹션을 추천드립니다:

<div class="card">
  <a href="/docs/basic-features/pages.md">
    <b>Pages:</b>
    <small>Next.js에서 페이지란 무엇인지에 대해 더 알아보세요.</small>
  </a>
</div>

<div class="card">
  <a href="/docs/advanced-features/custom-app.md">
    <b>Custom App:</b>
    <small>Next.js가 어떻게 페이지를 초기화하는지에 대해 더 알아보세요.</small>
  </a>
</div>
