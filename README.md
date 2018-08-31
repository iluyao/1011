# 1011
Cope QHearting.



import java.util.Arrays;
import java.util.Collections;
import java.util.Scanner;

public class Main {   
    static int N = 0, result = 0; // N 是短木棍数量，result 是所求长木棍长度
    // 使用 used 的二进制位标记第 n 根端木棍是否已经被使用
    // all 为所有木棍都被使用时的值
    static long used = 0, all = 0;
    static Integer[] sticks = null; // 保存每根木棍的长度

    // cur 表示当前正在尝试第 cur 根短木棍
    // len 表示当前所拼长木棍已完成的长度
    static boolean dfs(int cur, int len) {
        if (len == result) { // 当前所拼长木棍长度已达目标结果
            if (used == all) { // 所有短木棍已经被使用
                return true;
            }

            // 尝试拼下一根长木棍
            cur = len = 0;
        }

        int prevStick = -1;
        for (; cur < N; cur++) {
            int stick = sticks[cur];
            if (
                (used & (1L << cur)) != 0 || // 这截短木棍曾经使用过
                stick > result - len || // 这截短木棍的长度大于剩余所需长度
                prevStick == stick // 这截短木棍和前一截一样长，没必要再次尝试
            ) {
                continue;
            }

            used ^= 1L << cur; // 标记第 cur 木棍已经被使用
            if (dfs(cur + 1, len + stick)) {
                return true;
            }
            used ^= 1L << cur; // 发现使用第 cur 木棍无法成功，去掉标记，尝试其它木棍

            // 拼接一根新的长木棍时，无法使用这截木棍拼成一根完整木棍，
            // 不必继续换木棍尝试，因为这截木棍早晚会用到
            if (len == 0) {
                return false;
            }

            prevStick = stick;
        }

        return false;
    }

    public static void main(String[] args) {
        Scanner cin = new Scanner(System.in);

        while ((N = cin.nextInt()) != 0) {
            int maxLen = 0, sumLen = 0;
            sticks = new Integer[N];

            for (int i = 0; i < N; i++) {
                sticks[i] = cin.nextInt();

                sumLen += sticks[i];
                if (sticks[i] > maxLen) {
                    maxLen = sticks[i];
                }
            }

            // 先将木棍从大到小排序，这样在尝试换木棍过程中不用回头找了
            Arrays.sort(sticks, Collections.reverseOrder());
            for (result = maxLen; ; result++) {
                // 最终长木棍的长度一定是 sumLen 的因数
                if (sumLen % result != 0) {
                    continue;
                }

                used = 0;
                // N 为 64 时直接使用 -1，因为 1L << 64 的结果是 1
                all = N < 64 ? (1L << N) - 1 : -1;

                if (dfs(0, 0)) {
                    System.out.println(result);
                    break;
                }
            }
        }

        cin.close();
    }
}
