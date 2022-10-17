---
Title : "Github 블로그 기록"
Date : 2022-10-17
---

### 유튜브 영상 첨부하기
1. 그냥 본문에 첨부하기
```
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
```yaml
{% raw %}{% include video id="-PVofD2A9t8" provider="youtube" %}{% endraw %}
```
