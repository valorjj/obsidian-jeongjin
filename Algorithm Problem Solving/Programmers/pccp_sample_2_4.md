[source](https://school.programmers.co.kr/learn/courses/15009/lessons/121690)


> Do not afraid of searching uncharted territory. You will get it if you just proceed one by one.

가장 쉽게 생각나고 이해되는 방향을 바라보자

## With Python

> Python 으로 푸는것이 더 현명한 것 같다.

### sol1

```python
from collections import deque
def solution(n, m, hole):
    arr = [[[999999] * (m+1) for i in range(n+1)] for i in range(2)]
    def canGo(y,x):
	    # state as a global variable
        nonlocal arr
        return y>=1 and y<=n and x>=1 and x<=m and arr[0][y][x]!=-1
    for y,x in hole: arr[0][y][x],arr[1][y][x] = -1,-1
    q = deque([(0,1,1)])
    arr[0][1][1]=0
    while q:
        use,y,x = q.popleft()
        dir = [(0,1),(0,-1),(1,0),(-1,0)]
        for dy,dx in dir:
            ny,nx = y+dy,x+dx
            if canGo(ny,nx) and arr[use][ny][nx]>arr[use][y][x]+1:
                arr[use][ny][nx]=arr[use][y][x]+1
                q.append((use,ny,nx))
            nny,nnx = ny+dy, nx+dx
            if use==0 and canGo(nny,nnx) and arr[1][nny][nnx]>arr[0][y][x]+1:
                arr[1][nny][nnx]=arr[0][y][x]+1
                q.append((1,nny,nnx))
    answer = min(arr[0][n][m],arr[1][n][m])
    return answer if answer!=999999 else -1
```

### sol2

```python
from collections import deque

def solution(n, m, hole):
    dp = [[[-1, -1] for _ in range(m + 1)] for __ in range(n + 1)]
    dp[1][1][0] = 0

    mp = [[0 for _ in range(m + 1)] for __ in range(n + 1)]
    for x, y in hole: mp[x][y] = 1

    q = deque()
    q.append((1, 1, 0))

    while q:
        x, y, b = q.popleft()

        for dx, dy in [[0, 1], [1, 0], [0, -1], [-1, 0]]:
            for s in range(2):
                if b == 1 and s == 1: continue
                nx, ny, nb = x + dx * (s + 1), y + dy * (s + 1), b | s

                if nx < 1 or ny < 1 or nx > n or ny > m or mp[nx][ny] > 0 or dp[nx][ny][nb] != -1: continue

                q.append((nx, ny, nb))
                dp[nx][ny][nb] = dp[x][y][b] + 1

    res = dp[n][m][1]
    if res == -1 or (dp[n][m][0] >= 0 and res > dp[n][m][0]): res = dp[n][m][0]

    return res
```

### sol3

```python
import heapq

def solution(n, m, hole):
    answer = -1

    board = [[1 for _ in range(n)] for _ in range(m)]
    for h in hole:
        board[h[1] - 1][h[0] - 1] = 0
    board[-1][-1] = 2

    visited = [[[int(1e9) for _ in range(n)] for _ in range(m)] for _ in range(2)]

    # time, r, c, can_jump
    dr, dc = [1, -1, 0, 0], [0, 0, 1, -1]
    h = [(0, 0, 0, 1)]
    while h:
        t, r, c, can_jump = heapq.heappop(h)

        if board[r][c] == 2:
            answer = t
            break

        for i in range(4):
            nr, nc = r + dr[i], c + dc[i]
            if 0 <= nr < m and 0 <= nc < n and t + 1 < visited[can_jump][nr][nc] and board[nr][nc]:
                visited[can_jump][nr][nc] = t + 1
                heapq.heappush(h, (t + 1, nr, nc, can_jump))

            if can_jump:
                nr, nc = nr + dr[i], nc + dc[i]
                if 0 <= nr < m and 0 <= nc < n and t + 1 < visited[can_jump][nr][nc] and board[nr][nc]:
                    visited[0][nr][nc] = t + 1
                    heapq.heappush(h, (t + 1, nr, nc, 0))

    return answer
```


### sol4

```python
from collections import deque

def solution(n, m, hole):
    dx = [1,-1,0,0]
    dy = [0,0,1,-1]
    hole = set([tuple(h) for h in hole])
    visited = [[[0] * 2 for _ in range(m + 1)] for _ in range(n + 1)]
    visited[1][1][1] = 1
    q = deque([(1, 1, 1)])

    while q:
        x, y, s = q.popleft()

        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]

            if nx <= 0 or ny <= 0 or nx > n or ny > m:
                continue
            if (nx, ny) in hole:
                continue
            if visited[nx][ny][s]:
                continue

            q.append((nx, ny, s))
            visited[nx][ny][s] = visited[x][y][s] + 1

        if not s:
            continue

        for i in range(4):
            nx = x + dx[i] * 2
            ny = y + dy[i] * 2

            if nx <= 0 or ny <= 0 or nx > n or ny > m:
                continue
            if (nx, ny) in hole:
                continue
            if visited[nx][ny][0]:
                continue

            q.append((nx, ny, 0))
            visited[nx][ny][0] = visited[x][y][s] + 1

    res1, res2 = visited[n][m]

    if not res1 and not res2:
        return -1
    elif res1:
        return res1 - 1
    elif res2:
        return res2 - 1
    return min(res1, res2) - 1
```

### sol5

```python
from collections import deque
import sys

dx = [0, 0, 1, -1]
dy = [1, -1, 0, 0]

def solution(n, m, hole):
    answer = -1
    maps = [[[sys.maxsize] * n for _ in range(m)] for _ in range(2)] ## maps[0] shose off
    maps[0][0][0] = 0
    maps[1][0][0] = 0

    for h in hole: # (가로, 세로)
        maps[0][h[1]-1][h[0]-1] = -1
        maps[1][h[1]-1][h[0]-1] = -1

    q = deque([[0, 0, 1, 0]]) # x, y, shose, time

    while q:
        cur = q.popleft()
        if cur[0] == m-1 and cur[1] == n-1:
            answer = min(maps[0][cur[0]][cur[1]], maps[1][cur[0]][cur[1]])
            break

        if cur[2] == 1:
            for idx in range(4):
                nx = cur[0] + (dx[idx]*2)
                ny = cur[1] + (dy[idx]*2)
                if nx < 0 or ny < 0 or nx >= m or ny >= n or maps[0][nx][ny] == -1 or maps[0][nx][ny] <= cur[3]+1 or maps[1][nx][ny] <= cur[3]+1:
                    continue
                maps[0][nx][ny] = cur[3]+1
                q.append([nx, ny, 0, cur[3]+1])

        for idx in range(4):
            nx = cur[0] + dx[idx]
            ny = cur[1] + dy[idx]
            if nx < 0 or ny < 0 or nx >= m or ny >= n or maps[cur[2]][nx][ny] == -1 or maps[cur[2]][nx][ny] <= cur[3]+1:
                continue
            maps[cur[2]][nx][ny] = cur[3]+1
            q.append([nx, ny, cur[2], cur[3]+1])

    return answer
```

### sol6
```python
from collections import deque
def solution(n, m, hole):
    answer = -1
    board = [[0 for _ in range(m+1)] for _ in range(n+1)]
    dist = [[[-1, -1] for _ in range(m+1)] for _ in range(n+1)]
    for x, y in hole:
        board[x][y] = 1
    queue = deque()
    queue.append((1, 1, 0))
    dist[1][1][0] = 0

    while len(queue) > 0:
        x, y, cs = queue.popleft()
        for dx, dy in [[-1, 0], [0, 1], [1, 0], [0, -1]]:
            for s in range(2):
                if cs == 1 and s == 1:
                    continue
                nx, ny, ns = x + dx*(s+1), y + dy*(s+1), cs|s
                if nx < 1 or ny < 1 or nx > n or ny > m or board[nx][ny] > 0 or dist[nx][ny][ns] != -1:
                    continue
                queue.append((nx, ny, ns))
                dist[nx][ny][ns] = dist[x][y][cs] + 1

    answer = dist[n][m][1]
    if answer == -1 or (dist[n][m][0] >= 0 and dist[n][m][0] < answer):
        answer = dist[n][m][0]
    return answer
```

## With Java

### sol1

```java
import java.util.*;

class Solution {

    public static int N, M;
    public static boolean[][] visited, jumpVisited;
    public static int[] dx = new int[]{1, 0, -1, 0};
    public static int[] dy = new int[]{0, 1, 0, -1};

    public int solution(int n, int m, int[][] hole) {
        N = n-1;
        M = m-1;

        visited = new boolean[n][m];
        jumpVisited = new boolean[n][m];
        for (int[] h : hole) {
            visited[h[0]-1][h[1]-1] = true;
            jumpVisited[h[0]-1][h[1]-1] = true;
        }

        return bfs();
    }

    public int bfs() {
        Queue<int[]> q = new LinkedList<>();
        q.offer(new int[]{0, 0, 0, 0});
        visited[0][0] = true;
        jumpVisited[0][0] = true;

        while (!q.isEmpty()) {
            int[] poll = q.poll();
            int nowX = poll[0];
            int nowY = poll[1];
            int jumped = poll[2];
            int moving = poll[3];

            if (nowX == N && nowY == M) {
                return moving;
            }

            if (jumped == 0) {
                for (int i = 0; i < 4; i++) {
                    int nextX = nowX + dx[i] * 2;
                    int nextY = nowY + dy[i] * 2;
                    if (nextX > N || nextX < 0 || nextY > M || nextY < 0) continue;
                    if (visited[nextX][nextY] || jumpVisited[nextX][nextY]) continue;

                    jumpVisited[nextX][nextY] = true;
                    q.add(new int[]{nextX, nextY, 1, moving + 1});
                }
            }

            for (int i = 0; i < 4; i++) {
                int nextX = nowX + dx[i];
                int nextY = nowY + dy[i];
                if (nextX > N || nextX < 0 || nextY > M || nextY < 0) continue;

                if (jumped == 0) {
                    if (visited[nextX][nextY]) continue;
                    visited[nextX][nextY] = true;
                    q.add(new int[]{nextX, nextY, jumped, moving + 1});
                } else {
                    if (jumpVisited[nextX][nextY]) continue;
                    jumpVisited[nextX][nextY] = true;
                    q.add(new int[]{nextX, nextY, jumped, moving + 1});
                }
            }
        }

        return -1;
    }
}
```

### sol2

```java

import java.util.Queue;
import java.util.LinkedList;
import java.util.Arrays;

class Solution {
    public static int[] dy = new int[]{-1, 1, 0 , 0};
    public static int[] dx = new int[]{0, 0, -1, 1};

    public boolean check(int[][][] visited, int m, int n, int y, int x){
        return  0 <= y && y < m 
            && 0 <= x && x < n
            && visited[y][x][0] != -1;
    }

    public int[][][] initVisited(int[][] hole, int m, int n){
        int[][][] visited = new int[m][n][2];
        for(int[][] y : visited){
            for(int[] x : y){
                x[0] = Integer.MAX_VALUE;
                x[1] = Integer.MAX_VALUE;
            }
        }

        for(int[] h : hole){
            visited[h[1]-1][h[0]-1][0] = -1;
            visited[h[1]-1][h[0]-1][1] = -1;
        }

        return visited;
    }

    public int solution(int n, int m, int[][] hole) {
        int answer = -1;
        int[][][] visited = initVisited(hole, m, n);
        Queue<int[]> q = new LinkedList();

        int[] cu = new int[]{0, 0, 0}; // y x chance 
        int ny, nx;

        q.offer(cu);
        visited[0][0][0] = 0;
        while(!q.isEmpty()){
            cu = q.poll();

            if(cu[0] == m-1 && cu[1] == n-1)
                continue;

            for(int i = 0; i < 4; i++){
                ny = cu[0] + dy[i];
                nx = cu[1] + dx[i];

                if(check(visited, m, n, ny, nx)
                  && visited[ny][nx][cu[2]] > visited[cu[0]][cu[1]][cu[2]] + 1){
                    visited[ny][nx][cu[2]] = visited[cu[0]][cu[1]][cu[2]] + 1;
                    q.offer(new int[]{ny, nx, cu[2]});
                }

                if(cu[2] == 1) continue;
                ny += dy[i];
                nx += dx[i];

                if(check(visited, m, n, ny, nx)
                  && visited[ny][nx][1] > visited[cu[0]][cu[1]][0] + 1){
                    visited[ny][nx][1] = visited[cu[0]][cu[1]][0] + 1;
                    q.offer(new int[]{ny, nx, 1});
                }
            }
        }

        answer = Math.min(visited[m-1][n-1][0], visited[m-1][n-1][1]);
        return answer == Integer.MAX_VALUE ? -1 : answer;
    }
}
```

### sol3

```java
import java.util.*;
class Solution {
    static int[] dx = {-1,0,1,0};
    static int[] dy = {0,1,0,-1};
    // static int[] jx = {-2,0,2,0,-1,-1,1,1};
    static int[] jx = {-2,0,2,0};
    // static int[] jy = {0,2,0,-2,-1,1,1,-1};
    static int[] jy = {0,2,0,-2};
    static int[][][] visited;
    static int[][] miro;
    public static int bfs(int x, int y, int mCnt, int jChk) {
        visited[jChk][x][y] = 1;
        Queue<int[]> queue = new LinkedList<>();
        queue.add(new int[]{x,y,mCnt,jChk});
        while(!queue.isEmpty()){
            int[] c = queue.remove();
            int cx = c[0];
            int cy = c[1];
            int cmCnt = c[2];
            int cjChk = c[3];
            if (cx == miro.length - 1 && cy == miro[0].length - 1) {
                return cmCnt;
            }
            for (int i = 0; i<4; i++){
                int vx = dx[i] + cx;
                int vy = dy[i] + cy;
                if (vx <= 0 || vy <= 0 || vx >= miro.length || vy >= miro[0].length) {
                    continue;
                }
                if (visited[cjChk][vx][vy] == 0 && miro[vx][vy] != 1) {
                    visited[cjChk][vx][vy] = 1;
                    queue.add(new int[]{vx,vy,cmCnt + 1, cjChk});
                }
            }
            if (cjChk == 0) {
                for (int i = 0; i<4; i++){
                    int vx = jx[i] + cx;
                    int vy = jy[i] + cy;
                    if (vx <= 0 || vy <= 0 || vx >= miro.length || vy >= miro[0].length) {
                        continue;
                    }
                    if (visited[1][vx][vy] == 0 && miro[vx][vy] != 1) {
                        visited[1][vx][vy] = 1;
                        queue.add(new int[]{vx,vy,cmCnt + 1, cjChk + 1});
                    }
                }
            }
        }
        return -1;
    }
    public int solution(int n, int m, int[][] hole) {
        int answer = 0;
        visited = new int[2][n+1][m+1];
        miro = new int[n+1][m+1];
        for (int i = 0; i<hole.length; i++){
            int x = hole[i][0];
            int y = hole[i][1];
            miro[x][y] = 1;
        }
        answer = bfs(1,1,0,0);
        return answer;
    }
}
```

### sol4

```java
import java.util.*;

class Solution {
    boolean[][][] visit;
    boolean[][] board;
    int[] dy = {-1, 0, 1, 0};
    int[] dx = {0, 1, 0, -1};

    public int solution(int n, int m, int[][] hole) {
        int answer = Integer.MAX_VALUE;
        visit = new boolean[n][m][2];
        board = new boolean[n][m];

        for (int[] ints : hole) {
            board[ints[0] - 1][ints[1] - 1] = true;
        }

        Queue<Point> q = new LinkedList<>();
        q.add(new Point(0, 0, 0, 1));
        visit[0][0][1] = true;

        while (!q.isEmpty()) {
            Point p = q.poll();
            if (p.y == n - 1 && p.x == m - 1) {
                answer = p.cnt;
                break;
            }

            for (int i=0; i<4;i ++) {
                int ny = p.y + dy[i];
                int nx = p.x + dx[i];

                if (ny<0 || nx<0 || ny>=n || nx >=m || visit[ny][nx][p.k] || board[ny][nx]) {
                    continue;
                }

                visit[ny][nx][p.k] = true;
                q.add(new Point(ny, nx, p.cnt + 1, p.k));
            }

            if (p.k > 0) {
                for (int i=0; i<4;i ++) {
                    int ny = p.y + dy[i] * 2;
                    int nx = p.x + dx[i] * 2;

                    if (ny<0 || nx<0 || ny>=n || nx >=m || visit[ny][nx][p.k] || board[ny][nx]) {
                        continue;
                    }

                    visit[ny][nx][p.k - 1] = true;
                    q.add(new Point(ny, nx, p.cnt + 1, p.k - 1));
                }    
            }
        }

        if (answer == Integer.MAX_VALUE) {
            answer = -1;
        }

        return answer;
    }
}

class Point {
    int y;
    int x;
    int cnt;
    int k;

    public Point(int y, int x, int cnt, int k) {
        this.y = y;
        this.x = x;
        this.cnt = cnt;
        this.k = k;
    }
}
```

### sol5

```java
import java.util.*;

class Solution {
    int N;
    int M;
    int[] dx = {1,-1,0,0};
    int[] dy = {0,0,1,-1};

    int[] jx ={2,-2,0,0};
    int[] jy ={0,0,-2,2};
    int[][] map;
    boolean[][][] visited;

    int min;

    public int solution(int n, int m, int[][] hole) {
        min = Integer.MAX_VALUE;
        N = n;
        M = m;
        visited = new boolean[n][m][2];
        map = new int[n][m];
        for (int[] h : hole){
            map[h[0] - 1][h[1] - 1] = 1;
        }

        bfs();

        if (min == Integer.MAX_VALUE) {
            return -1;
        }

        return min;
    }

    private void bfs() {
        Queue<int[]> q = new LinkedList<>();
        q.add(new int[]{0,0,0,1});//x, y, count, jumpCount
        visited[0][0][1] = true;

        while(!q.isEmpty()){
            int[] poll = q.poll();
            int x = poll[0];
            int y = poll[1];
            int count = poll[2];
            int jump = poll[3];

            if (x == N - 1 && y == M - 1) {
                min = Math.min(count, min);
                break;
            }

            for (int i = 0; i < 4; i++) {
                int nx = x + dx[i];
                int ny = y + dy[i];

                if(isImpossible(nx,ny)) continue;
                if(visited[nx][ny][jump]) continue;
                if(map[nx][ny] == 1) continue;

                visited[nx][ny][jump] = true;
                q.add(new int[]{nx, ny, count + 1, jump});//jump 안씀
            }

            if(jump > 0){
                for (int i = 0; i < 4; i++) {
                    int nx = x + jx[i];
                    int ny = y + jy[i];

                    if(isImpossible(nx,ny)) continue;
                    if(visited[nx][ny][jump]) continue;
                    if(map[nx][ny] == 1) continue;

                    visited[nx][ny][jump - 1] = true;
                    q.add(new int[]{nx, ny, count + 1, jump - 1});//jump 안씀
                }
            }
        }
    }

    private boolean isImpossible(int nx, int ny) {
        return nx < 0 || ny < 0 || nx >= N || ny >= M;
    }
}
```

### sol6

```java
import java.util.*;
class Solution {
    static int reward[][];
    static int visited[][][];
    static int ans = -1;
    static int dx[] = {0,1,0,-1};
    static int dy[] = {1,0,-1,0};
    static int jx[] = {0,2,0,-2};
    static int jy[] = {2,0,-2,0};
    public static void bfs(){
        visited[0][1][1] = 1;
        Queue<int[]> queue = new LinkedList<>();
        queue.add(new int[]{1,1,0,0});
        while(!queue.isEmpty()){
            int remove[] = queue.remove();
            int cx = remove[0];
            int cy = remove[1];
            int cCnt = remove[2];
            int item = remove[3];
            if (cx == reward.length - 1 && cy == reward[0].length - 1){
                ans = cCnt;
                break;
            }            
            for (int i = 0; i<4; i++) {
                int vx = cx + dx[i];
                int vy = cy + dy[i];
                if (vx <= 0 || vy <= 0 || vx >= reward.length || vy >= reward[0].length){
                    continue;
                }
                if (visited[item][vx][vy] == 0 && reward[vx][vy] == 0) {
                    visited[item][vx][vy] = 1;
                    queue.add(new int[]{vx ,vy ,cCnt+1 , item});
                }
            }   
            if (item == 0) {
                for (int i = 0; i<4; i++) {
                    int vx = cx + jx[i];
                    int vy = cy + jy[i];
                    if (vx <= 0 || vy <= 0 || vx >= reward.length || vy >= reward[0].length){
                        continue;
                    }
                    if (visited[1][vx][vy] == 0 && reward[vx][vy] == 0) {
                        visited[1][vx][vy] = 1;
                        queue.add(new int[]{vx ,vy ,cCnt+1 , 1});
                    }
                }   
            }
        }
    }
    public int solution(int n, int m, int[][] hole) {
        int answer = 0;
        reward = new int[n + 1][m + 1];
        visited = new int[2][n + 1][m + 1];
        for (int i = 0; i<hole.length; i++){
            int x = hole[i][0];
            int y = hole[i][1];
            reward[x][y] = 1;
        }
        bfs();
        answer = ans;
        return answer;
    }
}
```

```java
import java.util.*;

class Solution {
    int[] dx = {0, 1, 0, -1}; // 상 우 하 좌
    int[] dy = {1, 0, -1, 0};
    int[] jumpDx = {0, 2, 0, -2};
    int[] jumpDy = {2, 0, -2, 0};
    int[][] map;
    boolean[][][] visited;

    public int solution(int n, int m, int[][] hole) {
        int answer = 0;
        map = new int[m][n];
        visited = new boolean[m][n][2];

        for(int i=0; i<hole.length; i++) {
            map[hole[i][1]-1][hole[i][0]-1] = 1;
        }

        answer = bfs(n, m, hole);

        return answer;
    }

    public int bfs(int n, int m, int[][] hole) {
        Queue<Point> q = new LinkedList<>();
        q.add(new Point(0, 0, 0, 0));

        while(!q.isEmpty()) {
            Point now = q.poll();

            if(now.y == m-1 && now.x == n-1) return now.cost;

            for(int i=0; i<4; i++) {
                int nx = now.x + dx[i];
                int ny = now.y + dy[i];
                int jumpCnt = now.jumpCnt;

                if(nx >= 0 && ny >= 0 && nx < n && ny < m && map[ny][nx] == 0 && !visited[ny][nx][jumpCnt]) {
                    visited[ny][nx][jumpCnt] = true;
                    q.add(new Point(nx, ny, now.cost + 1, jumpCnt));
                }
            }

            if(now.jumpCnt < 1) {
                for(int i=0; i<4; i++) {
                    int nx = now.x + jumpDx[i];
                    int ny = now.y + jumpDy[i];
                    int jumpCnt = now.jumpCnt + 1;

                    if(nx >= 0 && ny >= 0 && nx < n && ny < m && map[ny][nx] == 0 && !visited[ny][nx][jumpCnt]) {
                        visited[ny][nx][jumpCnt] = true;
                        q.add(new Point(nx, ny, now.cost + 1, jumpCnt));
                    }
                }
            }
        }

        return -1;
    }

    class Point {
        int x;
        int y;
        int cost;
        int jumpCnt;

        Point(int x, int y, int cost, int jumpCnt) {
            this.x = x;
            this.y = y;
            this.cost = cost;
            this.jumpCnt = jumpCnt;
        }
    }

}
```