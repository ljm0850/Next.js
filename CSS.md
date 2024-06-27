## CSS

- import 해서 사용할 것이기에 위치는 상관 없음

```css
/* ./styles/global.css */
body {
    background-color: black;
}
```

```tsx
// ./app/layout.tsx
// 글로벌로 사용할 css이기에 가장 기본이 되는 ./app/layout.tsx에서 import
import "../styles/global.css"
```

### Module.css

```css
/*
글로벌이 아닌 css 작성시 사용
경험 상 classname을 이용하는게 좋은듯

./styles/navigation.module.css
프로젝트에서 해당 css가 사용되는 파일(navigationPage) 옆에 생성했었는데, 고민 해봐야 할듯
*/

.nav {
    background-color: red;
}
```

```tsx
// ./components/navigation.tsx 일부
// 프로젝트에서 이와 같이 많이 사용했었음
<nav className="nav"> </nav>
```

- 이렇게 사용시 다른 파일에 navigation class를 만들고, global styles만 사용시 충돌이 발생하거나 class가 겹칠 수 있음
  - 프로젝트에서 팀원이 작성한 css가 어디서 적용되는건지 알 수 없어서 4시간을 고생했던 기억..

```tsx
import styles from "../styles/navigation.module.css";

<nav className={styles.nav}> </nav>
```

- 이와 같이 적용시 실제 HTML에 적용된 class를 보면 `navigation_nav___adf` 같이 뒤에 추가 값을 주어 class를 겹치지 않게 함



