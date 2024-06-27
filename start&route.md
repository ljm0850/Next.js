# 1. NextJs Intro

- typescript를 기준으로 작성되었습니다.
- 기본적으로 React, Vue.js를 알고 있기에 생략된 개념이 많습니다.

## Next.Js - start 

```
npx create-next-app@latest
== 
npm install react@latest next@latest react-dom@latest 를 줄인 명령어
```

```json
// npm run dev
// 이 때 dev는 package.json의 scripts를 "dev":"next dev"로 설정해두어서 가능,
// package.json 일부
{
  "scripts": {
    "dev": "next dev"
  },
}
```

# 2. ROUTING

## Defining Routes

- [React 에서의 route 설정](https://github.com/ljm0850/React/blob/master/Route.md)

### 구조

- `app/page.tsx` : root 페이지
- `app/folder-name` : app 밑에 folderName 폴더를 생성
  - `/folder-name`이 route 주소가 됨
  - 즉 `www.도메인.주소/folder-name` 주소가 생성이 됨
  - 그 후 folder-name에 page.tsx를 만들어 해당 주소가 보여줄 화면을 작성, 없을 경우 404 not found

```tsx
// ./app/ljm-next/page.tsx
// ./ljm-next 페이지에서 보여줄 화면
export default function LjmNext(){
    return <h1>Ljm NextJs test</h1>
}

// ./app/company/sales/page.tsx
// ./company/sales 페이지에서 보여줄 화면
// company에는 page.tsx 파일이 없을 경우 ./company 에서는 404 not found
export default function Sales(){
    return <h1>아기 신발 팝니다. 신어 본 적은 없어요</h1>
}
```

- Page.tsx 파일이 없다면 폴더를 만들어도 실제 경로에 포함되지도 않고, 렌더링 되지도 않음
- app 하위에 폴더를 만들어서 페이지가 아닌 코드를 작성해도 되기는 하지만, 클린코드 측면에서는 고민해볼 문제

```tsx
// ./app/ljm-next/component/ljm-next-component.tsx
function CleanCode(){
  return 
}
```



## 404 NOT FOUND

- App 폴더 밑에 `not-found.tsx` 파일 생성시 적용됨

```tsx
// ./app/not-found.tsx
export default function NotFound(){
    return <h1>Not Found</h1>
}
```



## Navigation

- 코드 가독성을 위해 components 폴더는 app과 같은 위치에 폴더를 생성하였음

```tsx
// components/navigation.tsx
"use client"; // usePathname hook을 사용하기 위해 작성
import Link from "next/link";
import { usePathname } from "next/navigation"; // 경로명을 알려주는 hook
export default function Navigation(){
    const path = usePathname();
    console.log(path)
    return (
    <nav>
        <ul>
            <li>
                <Link href="/">Home</Link> {path === "/"?"🔥":""}
            </li>
            <li>
                <Link href="/ljm-next">Ljm-next</Link> {path === "/ljm-next"?"🔥":""}
            </li>
        </ul>
    </nav>
        );
}
```

```tsx
// ./app/page.tsx
// Navigation은 layout에 두는 것이 일반적
import Navigation from "../components/navigation";

export default function Tomato(){
    return <div>
        <Navigation/>
        <h1>Hello!</h1>
    </div>
}
```

### React 방식으로 Navigation 구현하기

```tsx
"use client"
import Link from "next/link"
import styles from "../styles/movie.module.css"
import { useRouter } from "next/navigation"

interface IMovieProps {
    title: string,
    id: string,
    poster_path:string
}

export default function Movie({title,id,poster_path}:IMovieProps){
    const router = useRouter();
    const onClick = () => {
        router.push(`/movies/${id}`)
    }

    return (
        <div className={styles.movie}>
                <img src={poster_path} alt={title} onClick={onClick} />
                {/* <img src={poster_path} alt={title}/> */}
                <Link href={`/movies/${id}`}>{title}</Link>
        </div>
    )
}
```

- React에서는 onClick과 같은 이벤트를 통해 네비게이션 구현
- 서버 관점에서 보면 onClick 같은 이벤트는 존재하지 않음
  - "use client" 사용
- `use Router` import 주소가 **"next/navigation"**
  - "next/router"는 NEXT13 이전 버전에서 사용



## client & server rendering

- 렌더링 : 코드를 브라우저가 이해할 수 있는 HTML로 변환하는 작업

### Client side rendering

- 클라이언트(브라우저)에서 렌더링 하는 방식
- Javascript 엔진에 의해 HTML이 작성됨
  - 사용자가 맨 처음 페이지 접속시에는 body에 아무런 값도 존재하지 않음
  - 이후 script에 작성된 코드에 의해 HTML이 작성됨
- Javascript 코드 전체를 다운 받아야 하기에 초기 로딩이 오래걸림
- SEO 검색 엔진 최적화에 문제 발생
  - 검색 엔진은 기본적으로 HTML에 어떤 데이터가 들어가 있는지를 확인하는 확인
  - Javascript가 작동하지 않기에 빈 웹페이지를 검색 엔진이 탐색하게 됨

### Server side rendering

- Next.JS는 기본적으로 server side rendering
  - 그렇기에 Javascript가 활성화 되지 않아도 HTML이 작성되어 있음



## Hydration

- 단순 HTML이 렌더링 된 이후 HTML을 React application으로 초기화 하는 작업
  - 네비게이션 코드의 경우 JavaScript 작동 전에는 <a href="https://github.com/ljm0850">` 형식으로 구현
  - 이후 Javascript가 작동이 되면서 React component로 변경되어 Link component로 작용이 됨

```tsx
"use client";
export default function HydrationTest(){
    const [count,setCount] = useState(0);
    return (
        <ul>
            <li>
                <button onClick={()=>setCount(c=>c+1)}>{count}</button>
            </li>
        </ul>
        );
}
```

- 위 페이지의 소스코드를 보면 button 태그 안에 0으로 render되어 있음
  - 그렇기에 JavaScript 작동 전에는 클릭해도 변화가 없음
  - Javascript가 활성화 되면 eventLisener가 적용되면서 변화 작동



## "use client"

- Next.js 과거 버전에는 모든 component에 use client가 적용됨
- 모든 component가 hydration이 적용되는 것이 아님
- "use client"를 맨 위에 갖고 있는 component들만 hydration이 적용됨
  - 그렇기에 useState, usePathName 등 dynamic 값들이 사용될 경우 use client를 사용해야함
- 즉 client에서 렌더링 된다는 것이 아닌 backend에서 렌더링 되고, hydrate 및 interactive 됨을 의미

- use client가 적용되지 않은 component의 코드는 server에서만 작동되고 client에 보내지지 않기에 API KEY 등을 고민할 필요가 없음



## layout

- application 빌드시 재사용 되는 요소들이 들어있음
  - ex) Navigation, side bar, footer ...

- nextJS의 동작은 layout component로 가서 export default 컴포넌트를 먼저 렌더링 후 page.tsx 파일을 렌더링 하는 방식

```html
<Layout>
  <Home />
</Layout>
```

- 위와 같은 방식으로 렌더링 된다고 생각하면 편함

```tsx
import Navigation from "../components/navigation"

export const metadata = {
  title: 'Next.js',
  description: 'Generated by Next.js',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>
        <Navigation />
        {children}
      </body>
    </html>
  )
}

// children(props)는 home,ljm-next 등 의 컴포넌트
```

### 특정 주소부터 layout 필요할 때

```tsx
// app/ljm-next/layout.tsx
// ljm-next/ 이후(ljm-next/....주소)로는 레이아웃이 적용이 됨
export default function LjmNextLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <div>
        {children}
        &copy; Next JS 좋은데?
    </div>
  )
}
```

## route groups

- `(폴더명)`
- Logical group을 만드는 방법

- nextJS는 폴더가 곧 route(URL) 가 되기에 폴더로 분류할 경우 문제가 발생
- route(URL)를 만들지 않고  파일을 하나의 폴더에 묶는 방법

- `Page.tsx`에 사용되는 코드를 폴더로 분류하는데 이용할 수 있음



## Metadata

- 꼭 내보내야 하는 object
- 페이지의 head 부분에 표시되는 것

```tsx
// layout.tsx 일부
export const metadata = {
  title: '웹 페이지 상단에 뜨는 이름',
  description: '페이지에 대한 설명',
}
```

- Metadata는 병합되는 방식

```tsx
// ljm-next/layout.tsx 일부
export const metadata = {
    title: '병합',
    description: 'ㅋㅋ',
  }
```

- 병합되는 방식이기에 ljm-next 페이지의 layout에 위와 같이 설정하면 head의 title과 description이 변경됨

- title의 값은 **객체**도 가능

```tsx
// app/layout.tsx 일부
import { Metadata } from "next"
export const metadata : Metadata = {
    title: {
        template : "%s | app",
        default: "Loading..."
    },
    description: 'next.JS를 배우는 중입니다',
  };
// app/(home)/page.tsx 일부
export const metadata = {
    title:"home",
};
// app/ljm-next/layout.tsx 일부
export const metadata = {
    title: 'ljm-next',
  }

// 이와 같이 만들면 home 화면에서는 title이 home | app
// ljm-next 화면에서는 ljm | app
// 그 외의 화면(metadata에 title이 없는)에서는 "Loading..."
```

### 주의

- metadata는 페이지나 레이아웃에서만 내보낼 수 있음, 하위 컴포넌트에서 내보내기 불가
- 서버 컴포넌트에서만 있을 수 있음



## Dynamic Routes

- `/movies/:id` 같은 주소
- `/(movies)/movies/[id]/page.tsx`

- 위와 같이 폴더와 파일을 만들경우 /movies/13245 에 접속시 화면이 구현됨

```tsx
// (movies)/movies/[id]/page.tsx
// export default function MovieDetail({params:{id},}:
// {params : {id : string};}) 형식으로 
export default function MovieDetail(props){
    console.log(props)
    return <h1>Movie {id}</h1>
}

// URL이 /movies/123?region=kr&page=2 이라고 가정
// { params: { id: '123' }, searchParams: { region : 'kr', page:'2'} } 이 sever(백엔드)에 출력됨

// params만 필요할 경우 아래와 같이
export default function MovieDetail({params:{id},}:{params:{id:string}}){
    return <h1>Movie {id}</h1>
}
```

