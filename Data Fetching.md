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

const URL = "API주소";

async function getMovies(){
    const response = await fetch (URL);
    const json = await response.json();
    return json
}

// NextJS가 이 component에서 await를 해야 하기에 async 함수
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
- 이를 해결하기 위해 loading 컴포넌트를 따로 만듬

## Loading Component

```tsx
// app/(home)/loading.tsx
export default function Loading(){
    return <h2>Loading...</h2>
}
```

- 로딩이 발생할 수 있는 페이지와 같은 위치에 `loading.tsx` 파일로 만들어야 적용이 됨
- server component가 fetch하는 중에 보일 페이지를 구현, fetch가 끝나면 await 된 component로 변경
- 검색 엔진은 fetch가 완료된 화면을 봄(확실 x)

## Parallel Requests

- 두개의 API를 호출하여 페이지를 완성시킨다고 가정
  - ex) movie Detail 페이지는 영화에 대한 정보를 가져오는 API, 트레일러 가져오는 API를  호출
  - 렌더링에 필요한 시간 = 영화 정보 API 시간 + 트레일러 API 시간으로 순차적으로 처리가 됨

```tsx
import { API_URL } from "../../../(home)/page"

async function getMovie(id:string){
    const response = await fetch(`${API_URL}/${id}`);
    return response.json();
}

async function getVideos(id:string){
    const response = await fetch(`${API_URL}/${id}/videos`);
    return response.json();
}

export default async function MovieDetail({params:{id},}:
    {
        params:{id:string}
    }
){
    // const movie = await getMovie(id); 
    // const videos = await getVideos(id);
    // 이와 같이 코드 작성시 movie 가 할당된 이후 videos 작업을 시작
    const [movie,videos] = await Promise.all([getMovie(id),getVideos(id)]);
    return <h1>{movie.title}</h1>
}
```

