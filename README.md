Hi!


import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

/*
 * 0, 1, 2 로 이루어진 순열을 만든다
 * 해당 순열에 따라서 0이면 지나가고 1이면 a, 2이면 b를 투여하도록 한다.
 * 전체 순열을 0의 개수가 많음에 따라 정렬한 후 발견한 순간 그만한다.
 */
public class SWEA_2112_보호필름_정연수 {
	static class Select implements Comparable<Select> {
		int[] selected;
		int num;
		
		public Select(int[] arr) {
			num = 0;
			selected = new int[arr.length];
			for (int idx = 0; idx < arr.length; idx++) {
				selected[idx] = arr[idx];
				if (arr[idx] == 0) num++;
			}
		}

		@Override
		public int compareTo(Select o) {
			// TODO Auto-generated method stub
			int tmp = 0;
			for (int idx = 0; idx < o.selected.length; idx++) {
				if (o.selected[idx] == 0) tmp++;
			}
			return tmp - num;
		}
	}
	
	private static BufferedReader br;
	private static StringTokenizer st;
	private static int[] selected;
	private static int height, weight;
	private static int goal;
	private static int[][] film;
	private static int answer;
	private static PriorityQueue<Select> pq;
	
	private static boolean checkGoal(int[][] testFilm) {
		for (int col = 0; col < weight; col++) {
			int pivot = testFilm[0][col];
			int count = 1;
			for (int row = 1; row < height; row++) {
				if (pivot == testFilm[row][col]) {
					count += 1;
				} else {
					pivot = testFilm[row][col];
					count = 1;
				}
				if (count == goal) {
					break;
				}
			}
			if (count < goal) return false;
		}
		return true;
	}
	
	private static int[][] changedFilm() {
		int[][] newFilm = new int[height][weight];
		for (int row = 0; row < height; row++) {
			for (int col = 0; col < weight; col++) {
				if (selected[row] == 1) {
					newFilm[row][col] = 0;
				} else if (selected[row] == 2) {
					newFilm[row][col] = 1;
				} else {
					newFilm[row][col] = film[row][col]; 
				}
			}
		}
		return newFilm;
	}
	
	private static void dfs(int count) {
		if (count == height) {
			pq.add(new Select(selected));
		}
		for (int i = 0; i < 3; i++) {
			selected[count] = i;
			dfs(count + 1);
		}
	}
	
	public static void main(String[] args) throws Exception {
		br = new BufferedReader(new InputStreamReader(System.in));
		st = new StringTokenizer(br.readLine());
		int testCase = Integer.parseInt(st.nextToken());
		for (int test = 1; test <= testCase; test++) {
			st = new StringTokenizer(br.readLine());
			height = Integer.parseInt(st.nextToken());
			weight = Integer.parseInt(st.nextToken());
			goal = Integer.parseInt(st.nextToken());
			answer = Integer.MAX_VALUE;
			selected = new int[height];
			film = new int[height][weight];
			pq = new PriorityQueue<>();
			for (int row = 0; row < height; row++) {
				st = new StringTokenizer(br.readLine());
				for (int col = 0; col < weight; col++) {
					film[row][col] = Integer.parseInt(st.nextToken());
				}
			}
			dfs(0);
			System.out.println("#" + test + " " + answer);
		}
	}
}
