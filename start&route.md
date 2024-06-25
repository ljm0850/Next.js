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

```typescript
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

```typescript
// ./app/ljm-next/component/ljm-next-component.tsx
function CleanCode(){
  return 
}
```



## 404 NOT FOUND

- App 폴더 밑에 `not-found.tsx` 파일 생성시 적용됨

```typescript
// ./app/not-found.tsx
export default function NotFound(){
    return <h1>Not Found</h1>
}
```



## Navigation

- 코드 가독성을 위해 components 폴더는 app과 같은 위치에 폴더를 생성하였음

```typescript
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

```typescript
// ./app/page.tsx
// client, server 렌더링 배우기 전이라 수정 될 수 있음
import Navigation from "../components/navigation";

export default function Tomato(){
    return <div>
        <Navigation/>
        <h1>Hello!</h1>
    </div>
}
```



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

```typescript
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



