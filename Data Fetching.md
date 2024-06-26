# Data Fetching

## Client Side

- 기존 React에서 data Fetching 하던 방식

```tsx
"use client"
import { useEffect, useState } from "react";

export default function HomePage(){
    const [isLoading, setIsLoading] = useState(true);
    const [movies, setMovies] = useState([]);
    const getMovies = async() =>{
        const response = await fetch(API 주소를 입력해 주세요);
        const json = await response.json();
        setMovies(json);
        setIsLoading(false);
    }
    useEffect(()=>{
        getMovies();
    },[]);

    return <div>
        { isLoading? "Loading ..." : JSON.stringify(movies)}
    </div>
}

// 브라우저에서 호출하면 Network 탭에서 호출을 알 수 있기에, API 키같은 것을 넣을 수 없음
// 이 방식은 로딩 중인 것을 확인해야 하기에 로딩중인 것을 구현해야함
```

## Sever Side

```tsx
// .app/(home)/page.tsx
export const metadata = {
    title:"home",
};

const URL = "https://nomad-movies.nomadcoders.workers.dev/movies";

async function getMovies(){
    const response = await fetch (URL);
    const json = await response.json();
    return json
}

// await를 쓰기 위해 async 함수로 만듬
export default async function HomePage(){
    const movies = await getMovies();
    return <div> {JSON.stringify(movies)}</div>
}
```

- 최초 home 화면 접속시 API 요청으로 인해 로딩이 발생
  - API 요청 응답 속도만큼의 로딩 발생
- ljm-next 페이지에 있다가 home으로 돌아와도 로딩, 새로고침이 없음
  - fetch된 url을 자동으로 캐싱해주기에 한번 가져온 후 다시 가져올 필요가 없음
  - 클라이언트에서 API 요청을 보내지 않음
  - 백엔드에서 API를 요청하고, 이를 캐싱하여

 ### 문제점

- 백엔드에서 API 요청이 완료 되어 렌더링이 끝나기 전까진 사용자는 화면에 아무것도 보이지 않음
- 이로 인해 로딩상태인 것을 왼쪽 상단 탭부분에서만 확인이 가능함
- 이를 해결하기 위해 loading 파일을 따로 만듬

