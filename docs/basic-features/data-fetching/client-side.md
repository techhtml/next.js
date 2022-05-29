---
description: 'Learn about client-side data fetching, and how to use SWR, a data fetching React hook library that handles caching, revalidation, focus tracking, refetching on interval and more.'
---

// is this okay?
# 클라이언트 사이드 데이터 페칭
# Client-side data fetching

클라이언트 사이드 데이터 페칭은 페이지에 검색 엔진 최적화 인덱싱이 필요하지 않거나, 데이터를 프리렌더링 할 필요가 없거나, 페이지의 내용이 자주 업데이트 되어야 할 때 유용합니다. 서버 사이드 렌더링 API와 달리, 클라이언트 사이드 데이터 페칭은 컴포넌트 레벨에서 사용할 수 있습니다.

Client-side data fetching is useful when your page doesn't require SEO indexing, when you don't need to pre-render your data, or when the content of your pages needs to update frequently. Unlike the server-side rendering APIs, you can use client-side data fetching at the component level.

// 주어 붙일지 말지
(클라이언트 사이드 데이터 페칭을) 페이지 레벨에서 수행한다면 데이터를 런타임에 불러오게 되며, 페이지의 내용은 데이터가 바뀌면 업데이트됩니다. 컴포넌트 레벨에서 사용한다면 데이터를 컴포넌트 마운트 시 불러오며, 컴포넌트의 내용은 데이터가 바뀌면 업데이트됩니다.

If done at the page level, the data is fetched at runtime, and the content of the page is updated as the data changes. When used at the component level, the data is fetched at the time of the component mount, and the content of the component is updated as the data changes.


클라이언트 사이트 데이터 페칭을 사용하면 애플리케이션의 퍼포먼스 및 페이지 로딩 속도에 영향을 줄 수 있다는 것을 유념하셔야 합니다. 이는 컴포넌트 또는 페이지가 마운트될 때 데이터 페칭이 진행되고, 데이터가 캐싱되지 않기 때문입니다.

It's important to note that using client-side data fetching can affect the performance of your application and the load speed of your pages. This is because the data fetching is done at the time of the component or pages mount, and the data is not cached.


## useEffect를 사용한 클라이언트 사이드 데이터 페칭
## Client-side data fetching with useEffect

다음의 예는 useEffect hook을 사용해 클라이언트 사이트에서 데이터를 불러오는 방법을 보여줍니다.

The following example shows how you can fetch data on the client side using the useEffect hook.

```jsx
function Profile() {
  const [data, setData] = useState(null)
  const [isLoading, setLoading] = useState(false)

  useEffect(() => {
    setLoading(true)
    fetch('api/profile-data')
      .then((res) => res.json())
      .then((data) => {
        setData(data)
        setLoading(false)
      })
  }, [])

  if (isLoading) return <p>Loading...</p>
  if (!data) return <p>No profile data</p>

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.bio}</p>
    </div>
  )
}
```

## SWR을 사용한 클라이언트 사이드 데이터 페칭
## Client-side data fetching with SWR


Next.js 팀은 데이터 페칭을 위한 React hook 라이브러리인 [**SWR**](https://swr.vercel.app/)을 만들었습니다. 클라이언트 사이드에서 데이터를 불러온다면 SWR을 사용하는 것이 **강력히 권장**됩니다. SWR은 캐싱, 리밸리데이션, 포커스 추적, 일정 간격을 둔 리페칭 등등을 처리합니다.
// 리밸리데이션? 재검증? reteching on intervals?

The team behind Next.js has created a React hook library for data fetching called [**SWR**](https://swr.vercel.app/). It is **highly recommended** if you are fetching data on the client-side. It handles caching, revalidation, focus tracking, refetching on intervals, and more.


위와 같은 예를 사용하여, 프로필 데이터를 불러오기 위해 SWR을 사용해 볼 것입니다. SWR은 자동으로 데이터를 캐싱해주고 데이터가 stale해지면 자동으로 revalidate합니다.
// stale, revalidate

Using the same example as above, we can now use SWR to fetch the profile data. SWR will automatically cache the data for us and will revalidate the data if it becomes stale.


SWR 사용에 대한 더 많은 정보를 얻으려면 [SWR docs](https://swr.vercel.app/docs/getting-started)를 확인하세요.

For more information on using SWR, check out the [SWR docs](https://swr.vercel.app/docs/getting-started).

```jsx
import useSWR from 'swr'

const fetcher = (...args) => fetch(...args).then((res) => res.json())

function Profile() {
  const { data, error } = useSWR('/api/profile-data', fetcher)

  if (error) return <div>Failed to load</div>
  if (!data) return <div>Loading...</div>

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.bio}</p>
    </div>
  )
}
```

## 관련 정보
## Related

다음으로 무엇을 해야 하는지에 대해 더 알고 싶으시다면, 다음의 섹션을 추천드립니다:
For more information on what to do next, we recommend the following sections:

<div class="card">
  <a href="/docs/routing/introduction.md">
    <b>라우팅:</b>
    <small>Next.js의 라우팅에 대해 더 알아보세요.</small>
  </a>
</div>
