
[문제](https://school.programmers.co.kr/learn/courses/19344/lessons/242259)

### memo

![[Pasted image 20231225192652.png]]

![[Pasted image 20231225192646.png]]

- y 좌표 기준, 석유가 존재하는 곳 dfs
- 가로: 500 세로: 500
- 0: 빈 땅, 1: 석유 존재

> 못 풀었다.

못 푼 이유
- 석유를 만나는 경우, 매 경우에 dfs 알고리즘을 적용하려고 함
	- 시행횟수가 너무 많아짐
- 각 부분을 구분해야할 것 같다고 생각은 했으나
	- 구현을 어떻게 해야할 지 감을 못 잡음

## 다른 사람 풀이 1

1. 접근 전략 수립
2. 유효성 분석
3. 구체화

- 한번 연산하면 더 이상 계산이 필요 없는 부분을 나누기
	- 각 `fragment` 에 인덱스를 부여하고, 해당 `fragment` 의 크기를 `Map` 에 저장한다.
- `fragment` 를 찾을 때, 인접한 노드를 찾아야 하므로 `BFS` 로 푼다.
	- `Queue` + while 문
- 해당 문제는 `BFS` 를 통해 알아낸 정보를 이용한 구현 문제
	- 반복문을 돌면서 (이미 계산이 끝난) `framgent` 에 해당하는 인덱스만 확인하면 된다.


```java
import java.util.*;

class Solution {
static int rSize, cSize;
    static int[][] fragments;
    static int fragmentIdx = 1;
    static Map<Integer, Integer> fragmentsInfo = new HashMap<>();
    static boolean[][] visited;
    static int[] dirR = {-1, 1, 0, 0};
    static int[] dirC = {0, 0, -1, 1};

    public int solution(int[][] land) {
        int answer = 0;
        rSize = land.length;
        cSize = land[0].length;
        fragments = new int[rSize][cSize];
        visited = new boolean[rSize][cSize];

        for (int r = 0; r < rSize; r++) {
            for (int c = 0; c < cSize; c++) {
	            // 0 인 경우
                if (visited[r][c] || land[r][c] == 0) {
                    continue;
                }
				// 1 인 경우
                initFragment(land, r, c);
            }
        }


        for (int c = 0; c < cSize; c++) {
            Set<Integer> fragmentTypes = new HashSet<>();
            int tmpMaxAmount = 0;
            for (int r = 0; r < rSize; r++) {
                if (fragments[r][c] > 0) {
                    fragmentTypes.add(fragments[r][c]);
                }
            }

			// Set 에 기록된 인덱스에 해당하는 사이즈를 기록한다.
            for (Integer frg : fragmentTypes) {
                tmpMaxAmount += fragmentsInfo.get(frg);
            }

            answer = Math.max(answer, tmpMaxAmount);
        }

        return answer;
    }

    static void initFragment(int[][] land, int r, int c) {
        visited[r][c] = true;
        fragments[r][c] = fragmentIdx;
        Queue<int[]> q = new LinkedList<>();
        q.add(new int[]{r, c});
        int size = 0;

        while (!q.isEmpty()) {
            int[] cur = q.poll();
            size++;

            for (int d = 0; d < 4; d++) {
                int nxtR = cur[0] + dirR[d];
                int nxtC = cur[1] + dirC[d];

                if (nxtR < 0 || nxtR >= rSize || nxtC < 0 || nxtC >= cSize || visited[nxtR][nxtC]
                        || land[nxtR][nxtC] == 0) {
                    continue;
                }

                visited[nxtR][nxtC] = true;
                fragments[nxtR][nxtC] = fragmentIdx;
                q.add(new int[]{nxtR, nxtC});
            }
        }

        fragmentsInfo.put(fragmentIdx++, size);
    }
}
```


## 다른사람 풀이 2

```python
from collections import deque
def solution(land):
    answer = 0
    n = len(land)
    m = len(land[0])
    dx = [0,0,1,-1]
    dy = [1,-1,0,0]
    # 1차원으로 차원 내리기
    result = [0 for i in range(m+1)]
    # 방문 배열 선언
    visited = [[0 for i in range(m)] for j in range(n)]
    def bfs(a, b):
        count = 0
        visited[a][b] = 1
        q = deque()
        q.append((a,b))
        min_y, max_y = b, b
        while q:
            x,y = q.popleft()
            min_y = min(min_y, y)
            max_y = max(max_y, y)
            count += 1
            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]
                if nx < 0 or ny < 0 or nx >= n or ny >= m:
                    continue
                if visited[nx][ny] == 0 and land[nx][ny] == 1:
                    visited[nx][ny] = 1
                    q.append((nx,ny))
        
        for i in range(min_y, max_y+1):
            result[i] += count
    
    for i in range(n):
        for j in range(m):
            if visited[i][j] == 0 and land[i][j] == 1:
                bfs(i,j)
    answer = max(result)
    return answer
```