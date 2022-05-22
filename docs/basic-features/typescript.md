---
description: Next.js supports TypeScript by default and has built-in types for pages and the API. You can get started with TypeScript in Next.js here.
---

# 타입스크립트(TypeScript)

<details>
  <summary><b>버전 히스토리</b></summary>

| 버전   | 변경사항                                                                                                                              |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `v12.0.0` | 보다 빠른 타입스크립트와 TSX 컴파일을 위해 [SWC](https://nextjs.org/docs/advanced-features/compiler)를 기본으로 사용합니다.    |
| `v10.2.1` | `tsconfig.json`에서 [증분적 타입 체크 (Incremental type checking)](https://www.typescriptlang.org/tsconfig#incremental)가 활성화 되면 해당 기능이 지원됩니다. |

</details>

Next.js는 자동 설정(zero-configuration set up)과 페이지(Pages), API 등의 다양한 케이스에 대한 빌트인 타입 제공 등, [타입스크립트](https://www.typescriptlang.org/)에 대한 종합적인 경험을 제공합니다.  

- [타입스크립트 스타터를 클론하여 배포하는 방법](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fvercel%2Fnext.js%2Ftree%2Fcanary%2Fexamples%2Fwith-typescript&project-name=with-typescript&repository-name=with-typescript)
- [타입스크립트를 활용한 예시 보기](https://github.com/vercel/next.js/tree/canary/examples/with-typescript)

## `create-next-app` 지원

타입스크립트를 활용한 프로젝트는 다음과 같이 `--ts, --typescript` 플래그를 활용한  [`create-next-app`](https://nextjs.org/docs/api-reference/create-next-app) 명령어를 통해 생성이 가능합니다:

```
npx create-next-app@latest --ts
# 또는
yarn create next-app --typescript
# 또는
pnpm create next-app -- --ts
```

## 진행중인 프로젝트에서의 타입스크립트 사용 방법

진행중인 프로젝트에서 타입스크립트의 활용은 루트(root) 폴더에 내용이 없는 `tsconfig.json`을 생성하여 시작할 수 있습니다:

```bash
touch tsconfig.json
```

Next.js는 해당 파일을 디폴트 값으로 설정하게 됩니다. 추가로 [컴파일러 옵션](https://www.typescriptlang.org/docs/handbook/compiler-options.html) 을 통해 `tsconfig.json`을 커스터마이징 할 수 있습니다.


`next.config.js` 파일 내 프롭(prop)인 `typescript.tsconfigPath`를 설정함으로서 tsconfig.json 파일을 상대 경로로 제공할 수 있습니다.

`v12.0.0`버전부터 Next.js에서는 보다 빠른 타입스크립트와 TSX 컴파일링을 위해 [SWC](https://nextjs.org/docs/advanced-features/compiler)의 사용을 기본으로 설정하였습니다.

> `.babelrc` 파일이 존재하는 경우, Next.js는 타입스크립트를 다루기 위해 바벨(Babel)을 사용합니다. 해당 경우는 몇 가지 [주의 사항](https://babeljs.io/docs/en/babel-plugin-transform-typescript#caveats)이 있으며, 추가적으로 [컴파일러 옵션에 대한 설정  방법이 상이합니다](https://babeljs.io/docs/en/babel-plugin-transform-typescript#typescript-compiler-options).

`next`(기존의 `npm run dev` 또는 `yarn dev`)명령어를 사용하여 실행하면 Next.js는 다음과 같이 설정을 위한 필수 패키지에 대한 가이드를 제공합니다:

```bash
npm run dev

# 다음과 같은 설명이 표시됩니다: 
#
# 다음의 명령어를 사용하여 TypeScript, @types/react, 그리고 @types/node 를 설치하세요:
#
#         yarn add --dev typescript @types/react @types/node
#
# ...
```

`.js` 를 `.tsx` 파일로 변환하고 타입스크립트의 장점을 확인할 수 있는 준비가 완료되었습니다!

> 프로젝트 루트에 `next-env.d.ts`파일이 생성될 것 입니다. 해당 파일은 타입스크립트 컴파일러가 Next.js 타입(Next.js types)을 놓치지 않고 변환할 수 있도록 돕습니다. 해당 파일은 언제든지 변경사항이 적용될 수 있기 때문에 **수정할 수 없습니다**.

> 기본 모드인 `strict` 모드가 해재됩니다. 타입스크립트의 사용이 익숙해졌다면, `tsconfig.json`에서 해당 모드를 실행하는 것을 권장합니다. 

> `next-env.d.ts`파일을 수정하는 것 보다 `additional.d.ts`과 같은 추가 파일을 생성하여 타입을 추가할 수 있으며, `tsconfig.json`의 [`include`](https://www.typescriptlang.org/tsconfig#include) 배열에 참조할 수 있습니다.  

기본적으로 Next.js는 `next build` 명령어를 통해 타입 체크를 진행할 것입니다. 개발 중 코드 에디터를 사용하여 타입 체크 하는 것을 권장합니다.

에러 리포트를 원치 않는다면, [타입스크립크 에러 무시하기](/docs/api-reference/next.config.js/ignoring-typescript-errors.md) 자료를 참고하세요.

## 정적 생성 (Static Generation)과 서버 사이드 렌더링 (Server-side Rendering)

`getStaticProps`, `getStaticPaths`, 그리고 `getServerSideProps`을 사용하는 경우, 각각 `GetStaticProps`, `GetStaticPaths`, 그리고 `GetServerSideProps`를 사용할 수 있습니다:

```ts
import { GetStaticProps, GetStaticPaths, GetServerSideProps } from 'next'

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
}

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
}

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
}
```

> `getInitialProps`을 사용하는 경우, [이 페이지의 안내를 참고하세요](/docs/api-reference/data-fetching/get-initial-props.md#typescript).

## API 라우트

다음의 예시는 API 라우트를 위한 빌트인 타입을 사용하는 방법입니다:

```ts
import type { NextApiRequest, NextApiResponse } from 'next'

export default (req: NextApiRequest, res: NextApiResponse) => {
  res.status(200).json({ name: 'John Doe' })
}
```

또한 응답받을 데이터의 타입을 정할수도 있습니다:

```ts
import type { NextApiRequest, NextApiResponse } from 'next'

type Data = {
  name: string
}

export default (req: NextApiRequest, res: NextApiResponse<Data>) => {
  res.status(200).json({ name: 'John Doe' })
}
```

## 커스텀 `App`

[커스텀 `App`](/docs/advanced-features/custom-app.md)이 있다면, 빌트인 타입인 `AppProps`을 사용한 후, 다음과 같이 파일명을 `./pages/_app.tsx` 변경할 수 있습니다:

```ts
import type { AppProps } from 'next/app'

export default function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}
```

## 경로 명칭 (path aliases) 와 베이스 Url (baseUrl)

Next.js는 기본적으로 `tsconfig.json` `"paths"` 그리고 `"baseUrl"` 에 대한 옵션을 지원합니다.

해당 기능에 대해 더 알아보려면 [모듈 경로 명칭 (path aliases)을 문서화하는 방법](/docs/advanced-features/module-path-aliases.md).

## next.config.js에 대한 타입 체크

`next.config.js` 파일은 바벨 또는 타입스크립트에 의해 파싱되지 않기 때문에 반드시 자바스크립트 파일이어야 합니다. 하지만 아래와 같이 JSDoc을 활용해 IDE에 타입 체크를 추가할 수 있습니다:

```js
// @ts-check

/**
 * @type {import('next').NextConfig}
 **/
const nextConfig = {
  /* config options here */
}

module.exports = nextConfig
```

## 증분적 타입 체크 (Incremental type checking)

버전 `v10.2.1` 부터 크기가 큰 어플리케이션의 타입 체크를 위해 Next.js는 `tsconfig.json`에서 활성화 됨에 따라 [증분적 타입 체크](https://www.typescriptlang.org/tsconfig#incremental)를 지원합니다. 

[최대 퍼포먼스](https://devblogs.microsoft.com/typescript/announcing-typescript-4-3/#lazier-incremental)를 경험하기 위해 타입스크립트 버전 `v4.3.2` 이상일것을 권장합니다.  

## 타입스크립트 에러 무시하기

Next.js는 프로젝트 내 타입스크립트 에러가 발생하면 **프로덕션 빌드**(`next build`)를 실패(fail)시킵니다. 

Next.js가 어플리케이션 내 에러를 감안하고 생산코드를 생성하도록 하려면, 빌트인 타입 체크 스텝을 해제 하여야 합니다.  

해재 시 빌드 중 타입 체크가 확실히 진행 될 수 있도록 하여야 하며, 그렇지 않을 시 위험할 수 있습니다. 

`next.config.js`을 연 후 `typescript` config의 `ignoreBuildErrors` 옵션을 설정합니다:

```js
module.exports = {
  typescript: {
    // !! WARN !!
    // Dangerously allow production builds to successfully complete even if
    // your project has type errors.
    // !! WARN !!
    ignoreBuildErrors: true,
  },
}
```
