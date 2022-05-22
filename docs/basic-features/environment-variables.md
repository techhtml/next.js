---
description: Learn to add and access environment variables in your Next.js application.
---

# 환경 변수

> 이 문서는 Next.js 9.4 이상 버전에 적용됩니다. 이전 버전의 Next.js를 사용하고 계신다면, 업그레이드 하거나 [next.config.js의 환경 변수](/docs/api-reference/next.config.js/environment-variables.md)를 참고하세요.

<details open>
  <summary><b>예제</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/environment-variables">환경 변수</a></li>
  </ul>
</details>

Next.js는 환경 변수를 빌트인으로 지원하는데, 다음과 같은 일들을 할 수 있습니다: 

- [환경 변수를 로딩하기 위해 `.env.local` 사용하기](#loading-environment-variables)
- [접두사 `NEXT_PUBLIC_`을 붙여 브라우저에 환경 변수 노출하기](#exposing-environment-variables-to-the-browser)

## 환경 변수 로딩하기

Next.js는 `.env.local`을 `process.env`로 로딩하는 기능을 빌트인으로 지원합니다.

`.env.local` 예시:

```bash
DB_HOST=localhost
DB_USER=myuser
DB_PASS=mypassword
```

이렇게 하면 `process.env.DB_HOST`, `process.env.DB_USER`, `process.env.DB_PASS`는 자동적으로 Node.js 환경에 로딩되어 [Next.js data fetching methods](/docs/basic-features/data-fetching/overview.md)와 [API routes](/docs/api-routes/introduction.md)에서도 사용할 수 있게 됩니다.

[`getStaticProps`](/docs/basic-features/data-fetching/get-static-props.md)를 사용한 예시:

```js
// pages/index.js
export async function getStaticProps() {
  const db = await myDB.connect({
    host: process.env.DB_HOST,
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
  })
  // ...
}
```

> **주의**: Next.js는 서버 전용 시크릿(secret)을 안전하게 유지하기 위해 빌드 시 `process.env.*`를 정확한 값으로 대체합니다.
> 이는 `process.env`가 표준 JavaScript 객체가 아니라는 뜻이며, 따라서 [구조 분해 할당](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)을 사용할 수 없습니다.
> 환경 변수는 `const { PUBLISHABLE_KEY } = process.env`와 같이 참조해서는 _안되며_, `process.env.PUBLISHABLE_KEY`와 같이 참조해야 합니다.

> **주의**: Next.js는 `.env*` 파일안의 변수(`$VAR`)를 자동적으로 확장할 것입니다.
> 이로 인해 다음과 같이 다른 시크릿(secret)들을 참조할 수 있습니다:
>
> ```bash
> # .env
> HOSTNAME=localhost
> PORT=8080
> HOST=http://$HOSTNAME:$PORT
> ```
>
> 실제 값 `$`을 포함한 변수를 사용하고 싶다면, `\$`로 이스케이핑 처리를 해야 합니다.
>
> 예시:
>
> ```bash
> # .env
> A=abc
>
> # becomes "preabc"
> WRONG=pre$A
>
> # becomes "pre$A"
> CORRECT=pre\$A
> ```

> **주의**: `/src` 폴더를 사용한다면, Next.js가 .env 파일을 부모 폴더에서만 로딩하고 `/src` 폴더에서는 로딩하지 않을 것임을 유념하세요.

## 브라우저에서 환경 변수 노출하기

기본적으로 환경 변수는 Node.js 환경에서만 이용할 수 있으며, 브라우저에 노출되지 않습니다.

변수를 브라우저에 노출하기 위해서는 변수에 접두사 `NEXT_PUBLIC_`을 붙여야 합니다. 예를 들어:

```bash
NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
```

이렇게 하면 `process.env.NEXT_PUBLIC_ANALYTICS_ID`가 Node.js 환경에 자동으로 로드되어 코드의 어느 곳에서나 사용할 수 있습니다. `NEXT_PUBLIC_` 접두사로 인해 값이 브라우저로 전송된 JavaScript에 인라인됩니다. 이는 빌드 시 발생하므로 프로젝트 빌드 시점에 `NEXT_PUBLIC_` 환경 변수 설정이 완료된 상태여야 합니다.

```js
// pages/index.js
import setupAnalyticsService from '../lib/my-analytics-service'

// NEXT_PUBLIC_ANALYTICS_ID can be used here as it's prefixed by NEXT_PUBLIC_
setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID)

function HomePage() {
  return <h1>Hello World</h1>
}

export default HomePage
```

## 기본 환경 변수

일반적으로 `.env.local` 파일 하나만이 필요합니다. 그러나, 종종 `development` (`next dev`) 또는 `production` (`next start`) 환경을 위한 기본값을 추가할 수 있습니다.

Next.js의 `.env` (모든 환경), `.env.development` (개발 환경), and `.env.production` (프로덕션 환경)에서 기본값을 설정할 수 있습니다.

`.env.local`은 언제나 기본 값을 오버라이딩 합니다.

> **주의**: `.env`, `.env.development`, `.env.production` 파일은 기본값을 정의하므로 저장소에 포함되어 있어야 합니다. **`.env*.local`은 `.gitignore`에 추가되어야 하며**, 해당 파일들은 의도적으로 제외되어야 합니다. `.env.local`은 시크릿(secret)이 저장되는 파일입니다.

## Vercel의 환경 변수

[Vercel](https://vercel.com)에 Next.js 애플리케이션을 배포할 때, 환경 변수를 [프로젝트 설정](https://vercel.com/docs/environment-variables)에서 정의해주어야 합니다.

모든 형태의 환경 변수는 거기서 정의해야 합니다. 심지어 개발을 위한 환경 변수더라도 말입니다. 이는 후에 [로컬 기기로 다운로드](https://vercel.com/docs/environment-variables#development-environment-variables) 할 수 있습니다.

[개발 환경 변수](https://vercel.com/docs/environment-variables#development-environment-variables)를 정의했다면 다음 명령어를 사용하여 환경 변수를 `.env.local`에 가져와 로컬 기기에서 사용할 수 있습니다.

```bash
vercel env pull .env.local
```

Vercel CLI를 사용하여 배포하는 경우 [`.vercelignore`](https://vercel.com/guides/prevent-uploading-sourcepaths-with-vercelignore?query=vercelignore#allowlist)에 업로드되면 안되는 파일들(일반적으로 `.gitignore`에 포함되는 파일들과 동일)이 포함되어 있는지 확인하십시오.

## 테스트 환경 변수

`development`와 `production` 환경 외에도 `test`라는 제3의 옵션이 있습니다. 개발 또는 프로덕션 환경에서 기본값을 설정한 것과 같은 방식으로 `testing` 환경을 위한 `.env.test` 파일에 기본값을 설정할 수 있습니다. (`development`, `production` 환경보다는 드물게 사용합니다.)

이는 오로지 테스트를 위해 구체적인 환경 변수를 설정해야 하는 `jest` 또는 `cypress` 같은 테스팅 도구를 사용할 때 유용합니다. 테스트 기본값은 `NODE_ENV`가 `test`로 설정되면 로딩되는데, 일반적으로 테스팅 도구가 처리하므로 직접 해야 할 필요는 없습니다.

`test` 환경과 그 외 환경에서는 반드시 명심해야 하는 다른 점이 있는데, `test` 환경에서는 언제나 동일한 결과물을 내놓아야 하기 때문에 `.env.local`을 로딩하지 않는다는 것입니다. `.env.local`을 제외한 상태로 서로 다른 환경에서 기본값을 사용함으로써 모든 환경 변수 기본값을 사용하는 테스트 실행 환경을 만들 수 있습니다.

> **주의**: 기본값 환경 변수와 비슷하게, `.env.test` 파일은 저장소에 포함되어야 하고 `.env.test.local` 파일은 포함되어서는 안됩니다. 이는 `.env*.local`이 `.gitignore`로 인해 제외될 것이기 때문입니다.

단위 테스트를 실행하는 동안 `@next/env` 패키지의 `loadEnvConfig` 기능을 활용하여 Next.js와 동일한 방식으로 환경 변수를 로딩할 수 있습니다.

```js
// The below can be used in a Jest global setup file or similar for your testing set-up
import { loadEnvConfig } from '@next/env'

export default async () => {
  const projectDir = process.cwd()
  loadEnvConfig(projectDir)
}
```

## 환경 변수 로딩 순서

`NODE_ENV`로 설정된 환경에 따라, 환경 변수는 다음 소스들의 위부터 아래 순서로 로딩됩니다. 모든 환경에서 `env`는 다음의 소스들에 의해 오버라이딩 되지 않습니다:

`NODE_ENV=production`

1. `.env.production.local`
1. `.env.local`
1. `.env.production`
1. `.env`

`NODE_ENV=development`

1. `.env.development.local`
1. `.env.local`
1. `.env.development`
1. `.env`

`NODE_ENV=test`

1. `.env.test.local`
1. `.env.test`
1. `.env`

> **주의:** `.env.local`은 `NODE_ENV=test`일 때 로딩되지 않습니다.
