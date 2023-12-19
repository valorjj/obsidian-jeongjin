
> 알고리즘 문제 풀면서 다른 사람들이 풀이에 사용한 내용들 위주로 학습

## `_` 의미

```python
    board = [[0 for _ in range(m+1)] for _ in range(n+1)]
    dist = [[[-1, -1] for _ in range(m+1)] for _ in range(n+1)]
```

### 의미가 없는 변수명인 경우 활용

```python
for _ in range(5):
	print("hi")
```


```python
from collections import deque

    queue = deque([(1,1,1)])
```


## tuple

```python
hole = set([tuple(h) for h in hole])
```

- 리스트는 [] 튜플은 ()
- 튜플에 속한 원소는 변경 불가능

