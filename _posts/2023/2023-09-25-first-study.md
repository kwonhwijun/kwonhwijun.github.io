---
title : 복습을 도전해보자
date : 2023-09-25
categories : 
    - Blog
---

# 네이버 웹툰 크롤링하기

### 다운로드 함수 불러오기
```python
from requests import request
from requests.exceptions import HTTPError
from time import sleep

def download(url, params={}, method='GET', retries=3):
    resp = None
    j
    try:
        resp = request(method, url,
                       params=params if method=='GET' else {},
                       data=params if method=='POST' else {},
                       headers={'user-agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36'})
        resp.raise_for_status()
        # 아니면,
        # if resp.status_code != 200:
    except HTTPError as e:
        if 500 <= e.response.status_code:
            if retries > 0:
                sleep(3)
                resp = download(url, params=params,
                                method=method,
                                retries=retries-1)
            else:
                print('재방문 횟수 초과')
        else:
            print('Request', resp.request.headers)
            print('Response', e.response.headers)
        
    return resp

```

## 쿠키
- 쿠키를 이용해서 로그인 할 수 있다.
- 개발자 도구 Application에서 Cookie 정보를 찾을 수 있다. 





# numpy, pandas