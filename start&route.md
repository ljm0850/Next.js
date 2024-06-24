# NextJs Intro

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

# ROUTING

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
"use client";
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

