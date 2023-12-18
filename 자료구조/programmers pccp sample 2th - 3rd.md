

> [문제링크](https://school.programmers.co.kr/learn/courses/15009/lessons/121689)

```java
import java.util.*;
class Solution {
    public int solution(int[] menu, int[] order, int k) {
        int answer = Integer.MIN_VALUE;
        int curTime = 0, completeTime;
        Queue<Integer> queue = new ArrayDeque<>(); // 대기큐, 소요시간을 가짐
        completeTime = curTime+menu[order[0]]; // 첫 번째 완료시간 설정
        for(int idx=1;idx< order.length;idx++) {
            curTime+=k;
            // 주문 받기전에 완료된 내역들 제거하며 완료시간 변경
            // 완료는 됐으나, 대기하고 있는게 없는 경우
            while(completeTime <= curTime && !queue.isEmpty()) {
                completeTime += queue.poll();
            }
            if(completeTime <= curTime) {
                completeTime = curTime;
            }
            queue.offer(menu[order[idx]]);
            int wait = (completeTime > curTime ? 1 : 0) + queue.size();
            answer = Math.max(answer, wait);
        }
        return answer;
    }
}
```