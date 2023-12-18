

> [source](https://school.programmers.co.kr/learn/courses/15009/lessons/121689)

## sol 1

```java
import java.util.*;

class Solution {
    public int solution(int[] menu, int[] order, int k) {
        int answer = Integer.MIN_VALUE;
        int currentTime = 0; // 총 누적 시간
        int completeTime = 0; // 주문을 완료할 때까지 걸리는 시간
        
        Queue<Integer> queue = new ArrayDeque<>();
        completeTime = currentTime + menu[order[0]];
        
        // order 를 기준으로 반복문을 작성합니다.
        
        for(int idx=1; idx< order.length; idx++) {
            // 조건문을 만나기전에 시간의 흐름을 더합니다.
            // order 를 기준으로 했기 때문에 매번 k 만큼이 더해져야 합니다.
            currentTime += k;
    
            // queue 에 주문이 아직 남아 있는 경우와
            // 아무런 주문이 없는 경우 두가지를 생각해봅시다.
            
            // queue 에 처리되지 않은 주문이 남아 있는 경우는
            // 현재 처리 중인 작업이 있다는 뜻 입니다.
            // order 기준으로 idx * k 로 기준이 나누어 떨어지지 않으니
            // 처리시간, 현재시간을 기준으로 분기점을 만들어야 합니다.
            
            // 완료까지 걸리는 시간이 idx * k 로 나타나는 현재시간보다 적은 경우
            // 즉, 현재 시간 이전에 작업이 끝나는 경우
            // 작업 대기열에 아무런 작업이 없다면 바로 다음 작업을 시작하면 됩니다.
            
            // 만약 queue 에 아직 아무리되지 못한 작업이 남아 있다면
            // 해당 작업을 마무리를 지어야 합니다.
            // completeTime 에 queue 에 있는 작업이 걸리는 시간을 더함으로써 
            // 로직을 작성할 수 있습니다.
            
            // 대기열에 작업이 남아있고, 작업완료시간이 현재 시간보다 더 적은경우
            // 계속 작업을 진행해야 합니다.
            // 반대로 대기열에 작업이 존재하지 않거나
            // 작업완료까지 걸리는 시간이 현재시간보다 더 커진 경우
            // 대기열에 다음 작업을 추가해야합니다.
            
            while(completeTime <= currentTime && !queue.isEmpty()) {
                completeTime += queue.poll();
            }
            
            // 만일 대기열에 아무런 작업이 존재하지 않아서 위 while 문을 거치지 않은 경우
            // 혹은 작업해야하는 시간이, 현재 시간보다 더 커진 경우라서 이번 idx 에서는 작업이 불가능한 경우입니다.
            // currentTime 은 반복문 처음에 k 를 더함으로서 변화하고,
            // 작업시간이 더 커져버린 경우는, 다음 idx 에서 while 문에서 처리됩니다.
            // 따라서 예외는 while 문에서 처리하지 못한 상황일 것 입니다.
            
            // 어떤 예외사항이 있을 수 있을까요?
            // 작업이 다 끝나서 대기열은 비어있지만
            // 이전 idx 에서 completeTime 과 currentTime 이 기가막히게 동일해지는 경우가 있을 수 있습니다.
            // 하지만 이것은 로직이 그렇게 직관적이지 않기 때문에 곧바로 떠오르기가 어렵습니다.
            // 이미 방법을 알고 푸는 것이 아니라면 이걸 바로 생각해낼 수 있을까요?
        
            if (completeTime <= currentTime) {
                completeTime = currentTime;
            }
            
            // 새로운 order 는 곧 바로 작업할 수 없고, 대기열로 직행하게 됩니다.
            
            queue.offer(menu[order[idx]]);
            
            // 여기까지 온 경우, queue 에는 반드시 대기중인 작업이 존재합니다.
            // 이 때, 작업까지 걸리는 시간이 idx * k 보다 크다면, 반드시 다음 idx 에 해당하는
            // 고객은 기다려야 합니다. 
        
            int wait = (completeTime > currentTime ? 1 : 0) + queue.size();
            
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

## sol3

> 제일 깔끔한 것 같다.

```java
class Solution {
    public int solution(int[] menu, int[] order, int k) {
        int answer = 0;
        int index = 0; // 작업 인덱스
        int waiting = 0;
        
        // 작업이 종료되는 시간을 배열로 관리한다.
        // queue 대신 배열을 이용한다.
        int[] endTime = new int[order.length];

        // order 기준 반복
        for (int i = 0; i < order.length; i++) {
            System.out.println("for 문 실행");
            System.out.println("index: " + index);
            System.out.println("waiting: " + waiting);
            
            
            System.out.println("####################");
            
            while (index < i && endTime[index] <= k * i) {
                index++;
                waiting--;
            }

            waiting++;
            
            if (i == 0) {
                endTime[i] = menu[order[0]];
            } else {
                endTime[i] = Math.max(k * i, endTime[i - 1]) + menu[order[i]];
            }
            
            System.out.println("endTime: " + endTime[i]);
            
            answer = Math.max(answer, waiting);
        }

        return answer;
    }
}
```

```bash
for 문 실행  
index: 0  
waiting: 0  
####################  
endTime: 12  
for 문 실행  
index: 0  
waiting: 1  
####################  
endTime: 42  
for 문 실행  
index: 0  
waiting: 2  
####################  
endTime: 47  
for 문 실행  
index: 1  
waiting: 2  
####################  
endTime: 59
```

## sol4

```java
public class Solution {
    private static class Order {
        public final int time;
        public final int duration;

        public Order(int time, int duration) {
            this.time = time;
            this.duration = duration;
        }
    }

    public int solution(int[] menu, int[] order, int k) {
        Order[] orders = new Order[order.length];
        for (int i = 0; i < order.length; i++) {
            orders[i] = new Order(k * i, menu[order[i]]);
        }

        int max = 0;

        int time = 0;
        int end = 0;
        for (int start = 0; start < orders.length; start++) {
            Order o = orders[start];
            if (time < o.time) {
                time = o.time;
            }

            time += o.duration;
            while (end < orders.length && orders[end].time < time) {
                end++;
            }
            int count = end - start;
            if (count > max) {
                max = count;
            }
        }
        return max;
    }
}
```