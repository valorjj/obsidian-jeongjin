

> [source](https://school.programmers.co.kr/learn/courses/15009/lessons/121689)

## sol 1

```java
import java.util.*;
class Solution {
    public int solution(int[] menu, int[] order, int k) {
        int answer = Integer.MIN_VALUE;
        int curTime = 0, completeTime;
        Queue<Integer> queue = new ArrayDeque<>(); // 대기큐, 소요시간을 가짐
        completeTime = curTime+menu[order[0]]; // 첫 번째 완료시간 설정
        for(int idx=1; idx < order.length; idx++) {
	        // 주문이 들어왔다는 것은 k 만큼의 시간이 지난것과 동일하므로 k 를 더한다.
            curTime += k;
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

## sol 2

```java
import java.util.*;

class Solution {
    public int solution(int[] menu, int[] order, int k) {
        int answer = 0;
        int time = 0;
        int outTime = 0;
        Queue<Integer> q = new LinkedList<>();
        
        // 메뉴 제조에 걸리는 시간 outTime
        // 1초씩 흐르는 시간 time
        // time 은 계속 흐르고, 로직이 outTime 과 겹치는 경우에만
        // 실행되어야 하므로
        // while(true) 로 전체를 감싼다.
        while(true){
            // 모든 주문이 완료되고,
            // 시간도 모두 지난경우 while 문 종료
            if(k * order.length < time && q.isEmpty()) break;
            // 음료 제조 완료
            if(outTime > 0 && outTime == time){
                // 대기열에서 해당 주문건 제거
                q.poll();
				
                if(q.isEmpty()){

                }
                // 아직 주문건이 남았습니다.
                else{
                    // 대기열 중 가장 오래된 주문부터 
                    // outTime 에 더합니다.
                    outTime += q.peek();
                }
            }
            // 새로운 손님이 입장한 경우
            if(time % k == 0 && time / k < order.length) {
                // 대기열이 비어있는 경우
                if(q.isEmpty()) {
                    // 제조에 걸리는 시간과 지금까지 지난 시간을 동기화시킨다.
                    outTime = time;
                }
                // 대기열에 메뉴를 추가한다.
                q.add(menu[order[time/k]]);
            }
            // 새로운 손님이 입장했고, 대기열에 처리해야 할 주문이 남아있는 경우
            if(outTime == time && !q.isEmpty()) {
                // 주문시 걸리는 시간에 대기열에 있는 주문건을 더한다.
                outTime += q.peek();
            }
            answer = Math.max(answer, q.size());
            time++;
        }
        return answer;
    }
}
```