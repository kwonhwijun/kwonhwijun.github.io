---
title : "Github 블로그 기록"
tate : 2022-10-17
categories : 
    - Github
---
### 포스팅이 이뤄지지 않은 경우
1. 지금 블로그의 기준 시간대가 미국으로 되어 있어, 심야에 새벽 1,2시에 글을 올리는 경우 한국에서는 날짜가 지났지만, 미국 기준으로 지나지 않는 경우가 있었다. 
_config.yml 파일에 다음 속성을 추가하면 된다.
```yaml
future : true
```

### 코드 블록 생성하기
```yaml
    ```{원하는 언어}
    Code 1
    Code 2
    ```
```

2. 연구중

This post lives in the future and is dated {{ page.date | date: "%c" }}. When building Jekyll with the `--future` flag it should appear.

3. {% raw %}{% include video id="-PVofD2A9t8" provider="youtube" %}{% endraw %}
이렇게 앞뒤로 raw를 붙여주면, 코드를 그 자체로 넣을 수 있다.

### 유튜브 영상 첨부하기
1. 그냥 본문에 첨부하기
``` yaml
{% include video id="-PVofD2A9t8" provider="youtube" %}
```

2. Header로 첨부하기
Header에 다음 속성을 추가해주고
```yaml
header:
  video:
    id: -PVofD2A9t8 
    provider: youtube
```
본문에는 이렇게 적어준다.

