---
description: 'Learn about client-side data fetching, and how to use SWR, a data fetching React hook library that handles caching, revalidation, focus tracking, refetching on interval and more.'
---

# 클라이언트 사이드에서 데이터 가져오기

클라이언트 사이드에서 데이터를 가져오는 것은 페이지에 검색 엔진 최적화 인덱싱이 필요하지 않거나, 데이터를 프리렌더링 할 필요가 없거나, 페이지의 내용이 자주 업데이트 되어야 할 때 유용합니다. 서버 사이드 렌더링 API와 달리, 컴포넌트 레벨에서도 클라이언트 사이드에서 데이터를 가져올 수 있습니다.

페이지 레벨에서 수행한다면 데이터를 런타임에 불러오게 되며, 페이지의 내용은 데이터가 바뀌면 업데이트됩니다. 컴포넌트 레벨에서 사용한다면 데이터를 컴포넌트 마운트 시 불러오며, 컴포넌트의 내용은 데이터가 바뀌면 업데이트됩니다.

클라이언트 사이트에서 데이터를 가져오면 애플리케이션의 퍼포먼스 및 페이지 로딩 속도에 영향을 줄 수 있다는 것을 유념하셔야 합니다. 이는 컴포넌트 또는 페이지가 마운트될 때 데이터를 가져오고, 데이터가 캐싱되지 않기 때문입니다.

## useEffect를 사용하여 클라이언트 사이드에서 데이터 가져오기

다음의 예는 useEffect hook을 사용해 클라이언트 사이트에서 데이터를 가져오는 방법을 보여줍니다.

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

## SWR을 사용하여 클라이언트 사이드에서 데이터 가져오기

Next.js 팀은 데이터 페칭을 위한 React hook 라이브러리인 [**SWR**](https://swr.vercel.app/)을 만들었습니다. 클라이언트 사이드에서 데이터를 가져온다면 SWR을 사용하는 것이 **강력히 권장**됩니다. SWR은 캐싱, 재검증, 포커스 추적, 일정 간격을 둔 리페칭 등등을 처리합니다.

위와 동일한 예시를 사용하여, SWR을 사용해 프로필 데이터를 가져올 것입니다. SWR은 자동으로 데이터를 캐싱해주고 데이터가 스테일(stale)해지면 자동으로 재검증합니다.

SWR 사용에 대한 더 많은 정보를 얻으려면 [SWR docs](https://swr.vercel.app/docs/getting-started)를 확인하세요.

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

다음으로 무엇을 해야 하는지에 대해 더 알고 싶으시다면, 다음의 섹션을 추천드립니다:

<div class="card">
  <a href="/docs/routing/introduction.md">
    <b>라우팅:</b>
    <small>Next.js의 라우팅에 대해 더 알아보세요.</small>
  </a>
</div>
