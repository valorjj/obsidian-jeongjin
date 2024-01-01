[문제](https://school.programmers.co.kr/learn/courses/19344/lessons/242260)

## solution 1

```java
import java.util.*;
class Solution {

    public int toSeconds(int h, int m, int s) {
        return s + 60 * (m + 60 * h);
    }

    public double minute(int i) {
        return 60.0 * i + 60.0 * (i % 60) / 59;
    }

    public double hour(int i) {
        return 60.0 * i + 60.0 * (i % 720) / (120 * 6 - 1);
    }

    public int solution(int s, int e) {
        Set<Double> set = new HashSet<>();
        for (int i = 0; i < 24 * 60; i++) {
            set.add(minute(i));
            set.add(hour(i));
        }
        int answer = 0;
        for (double v : set)
            if (v >= s && v <= e)
                answer++;
        return answer;
    }

    public int solution(int h1, int m1, int s1, int h2, int m2, int s2) {
        return solution(toSeconds(h1, m1, s1), toSeconds(h2, m2, s2));
    }
}
```

## solution 2

```java
class Solution {
    public int solution(int h1, int m1, int s1, int h2, int m2, int s2) {
        int answer = 0;
        int H = h1 * 3600 + m1 * 60 + s1;
        float preH = 0f, curH = 0f;
        int M = m1 * 60 + s1;
        float preM = 0f, curM = 0f;
        int S = s1;
        int preS = 0, curS = 0;
        int count = (h2 - h1) * 3600 + (m2 - m1) * 60 + s2 - s1;

        preH = H / 720f % 60;
        preM = M / 60f % 60;
        preS = S;
        if(H == S || M == S) answer++;
        while(count > 0) {
            S++;
            M++;
            H++;
            curS = S;
            curM = M / 60f % 60;
            curH = H / 720f % 60;
            if(preS < preM && curM <= curS) answer++;
            if(preS < preH && curH <= curS) if(curM != curH) answer++;
            if(S == 60) {
                S = 0;
                if(curM == 60) {
                    M = 0;
                    if(curH == 60) H = 0;
                }
            }
            preS = S;
            preM = M / 60f % 60;
            preH = H / 720f % 60;
            count--;
        }

        return answer;
    }
}
```

## solution 3

```java
class Solution {

    public int solution(int h1, int m1, int s1, int h2, int m2, int s2) {
        int result = calc(h2, m2, s2) - calc(h1, m1, s1);

        if (5 * (h2 % 12 + m2 / 60.0 + s2 / 3600.0) == s2 || m2 + s2 / 60.0 == s2) {
            result++;
        }

        return result;
    }

    public int calc(int h, int m, int s) {
        if (h >= 12) {
            return calc2(11, 59, 59) + calc2(h - 12, m, s);
        } else {
            return calc2(h, m, s);
        }
    }

    public int calc2(int h, int m, int s) {
        if (h == 0 && m == 0 && s == 0) {
            return 0;
        }

        int ret = h * 59 + h * 60 + m * 2 - 1;

        if (5 * (h + m / 60.0 + s / 3600.0) < s) {
            ret += 1;
        }

        if (m + s / 60.0 < s) {
            ret += 1;
        }

        return ret;
    }
}
```