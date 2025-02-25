Hi!

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.util.Arrays;
import java.util.StringTokenizer;

/*
 * 1. 각 선수의 이닝 별로의 결과를 그래프로 입력받습니다.
 * 2. 1번을 제외하고 순열을 만듭니다.
 * 3. 1번을 2에서 만든 순열에 삽입합니다.
 * 4. 이닝이 진행됨에 따라서 시뮬레이션을 합니다.
 * 5. 4는 아웃 카운트가 이닝 * 3에 이르기 까지 수행합니다.
 * 6. 아웃 카운트가 3에 도달하면 이닝을 바꾸어줍니다.
 */
public class BOJ_17281_야구_정연수 {
	private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
	private static StringTokenizer st;
	private static int inning;
	private static int[][] performance;
	private static int[] player = {1, 2, 3, 4, 5, 6, 7, 8};
	
	private static void swap(int left, int right) {
		int tmp = player[left];
		player[left] = player[right];
		player[right] = tmp;
	}
	
	private static boolean hasNext() {
		int head = 0;
		int tail = 7;
		int highestIdx = tail;
		while (highestIdx > 0 && player[highestIdx - 1] >= player[highestIdx]) highestIdx--;
		if (highestIdx == head) return false;
		int targetIdx = tail;
		while (player[highestIdx - 1] >= player[targetIdx]) targetIdx--;
		swap(highestIdx - 1, targetIdx);
		while(highestIdx <= targetIdx) swap(highestIdx++, targetIdx--);
		return true;
	}
	
	public static void main(String[] args) throws Exception {
		st = new StringTokenizer(br.readLine());
		inning = Integer.parseInt(st.nextToken());
		performance = new int[inning][9];
		for (int inningIdx = 0; inningIdx < inning; inningIdx++) {
			st = new StringTokenizer(br.readLine());
			for (int playerIdx = 0; playerIdx < 9; playerIdx++) {
				performance[inningIdx][playerIdx] = Integer.parseInt(st.nextToken());
			}
		}
		
		do {
			System.out.println(Arrays.toString(player));
		} while (hasNext());
	}
}
