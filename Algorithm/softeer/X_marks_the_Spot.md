```java
import java.io.*;
import java.util.*;

public class Main {

    // 각 문자열 쌍 안에 두 문자열은 길이가 같다.
    // S 에서 글자 x, X 가 등장하는 위치 = P ( 이 위치는 유일.)
    // 이때, 해당 T의 P번째 출력 ( 소문자는 대문자로 변환.)

    // ex) s = Indexing, T : indirect --> S에서 x는 인덱스 4번이니깐, T의 인덱스 4번인 R이 출력.

    // 1 <= N <= 500,000
    // 문자열의 길이는 1,000,000 넘지않음.

    static int N;
    static Indexing[] arr;
    
    
    public static void main(String[] args) throws Exception {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        N = Integer.parseInt(br.readLine());
        // Map<String, String> map = new HashTable<>();
        arr = new Indexing[N];
        
        for (int i=0; i<N; i++) {
            StringTokenizer token = new StringTokenizer(br.readLine());
            String s = token.nextToken();
            String t = token.nextToken();
            // map.put(s, t);
            arr[i] = new Indexing(s, t);
        }

        /////////////////////////////////// 메인로직 ////////////////////////////////////////////

        StringBuffer sb = new StringBuffer();
        for (int i=0; i<N; i++) {
            int index = 0; // x, X 자리 인덱스.
            Indexing indexing = arr[i];
            String[] stS = indexing.s.split("");
            for(int j=0; j<stS.length; j++) {
                
                if (stS[j].equals("x") || stS[j].equals("X")) {
                    index = j; 
                    break;                    
                }
            }

            String[] stT = indexing.t.split("");
            sb.append(stT[index].toUpperCase());          

        }

        System.out.println(sb);

    }


    static class Indexing {
        String s;
        String t;

        public Indexing(String s, String t) {
            this.s = s;
            this.t = t;
        }
    }
}

```