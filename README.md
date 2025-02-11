Hi!


package startcamp;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

/*
 * 우선 전체 맵을 순회 하며 각각의 섬에 1, 2, 3, 4 를 기입한 새로운 지도를 만든다.(visit 겸)
 * 각각의 섬에서 사방으로 뻗어서 닿는 곳의 섬 번호와 최솟값을 구한다.
 * 위의 값으로 각각의 섬에서 갈 수 있는 섬들의 최소값을 기입한 그래프 작성
 * (못 가는 곳은 -1)
 * 백트래킹으로 모든 곳 방문하는데 parent 배열로 사이클을 만들지 않는 경로만을 추가한다.
 * (경로 압축도 해야함)
 * 이때 중복으로 세는 경우가 있는데 이거는 좀 더 고민
 */
public class Main {
	private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	private static int N, M;
	private static int[][] map;
	private static int[][] island;
	private static int[][] direction;
	private static List<int[]> setArr;
	private static int answer = Integer.MAX_VALUE;
	private static int islandNum;
	private static int[] dy = {-1, 1, 0, 0};
	private static int[] dx = {0, 0, -1, 1};
	
	public static void checkIsland(int islandNum, int y, int x) {
		ArrayDeque<int[]> locationQ = new ArrayDeque<>();
		int[] location = {y, x};
		locationQ.add(location);
		while(!locationQ.isEmpty()) {
			int[] tmp = locationQ.poll();
			if (island[tmp[0]][tmp[1]] != 0 || map[tmp[0]][tmp[1]] == 0) continue;
			island[tmp[0]][tmp[1]] = islandNum;
			for (int i = 0; i < 4; i++) {
				int nxtY = tmp[0] + dy[i];
				int nxtX = tmp[1] + dx[i];
				if (nxtY < 0 || N <= nxtY || nxtX < 0 || M <= nxtX) continue;
				int[] nxtLocation = {nxtY, nxtX};
				locationQ.add(nxtLocation);
			}
		}
	}
	
	public static int visitIsland() {
		int islandNum = 0;
		for (int col = 0; col < N; col++) {
			for (int row = 0; row < M; row++) {
				if (map[col][row] == 0 || island[col][row] != 0) continue;
				islandNum++;
				checkIsland(islandNum, col, row);
			}
		}
		return islandNum;
	}
	
	public static void initDirection(int islandNum) {
		for (int i = 1; i <= islandNum; i++) {
			for (int j = 1; j <= islandNum; j++) {
				direction[i][j] = Integer.MAX_VALUE;
			}
		}
	}
	
	public static void richBridge(int y, int x) {
		for (int i = 0; i < 4; i++) {
			int from = island[y][x];
			int curY = y;
			int curX = x;
			int bridgeLength = 0;
			while (true) {
				bridgeLength++;
				int nxtY = curY + dy[i];
				int nxtX = curX + dx[i];
				// 범위 밖이거나 섬 안이다.
				if (nxtY < 0 || N <= nxtY || nxtX < 0 || M <= nxtX || island[nxtY][nxtX] == from) {
					break;
				}
				
				// 다른 섬 도착
				if (island[nxtY][nxtX] != 0) {
					if (bridgeLength <= 2) break;
					int to = island[nxtY][nxtX];
					direction[from][to] = Math.min(direction[from][to], bridgeLength - 1);
					direction[to][from] = Math.min(direction[to][from], bridgeLength - 1);
				}
				curY = nxtY;
				curX = nxtX;
			}
		}
	}
	
	/*
	 * island를 돌면서 사방으로 뻗어서 direction 완성
	 */
	public static void countDirection() {
		for (int i = 0; i < N; i++) {
			for (int j = 0; j < M; j++) {
				if (island[i][j] == 0) continue;
				richBridge(i, j);
			}
		}
	}
	
	private static int getParent(int idx, int[] parent) {
		if (parent[idx] == idx) return idx;
		return parent[idx] = getParent(parent[idx], parent);
	}
	
	private static boolean check(int[] parent) {
		for (int i = 2; i <= islandNum; i++) {
			if (getParent(1, parent) != getParent(i, parent)) {
				return false;
			}
		}		
		return true; 
	}
	
	private static void getMinDirection(int start, int cnt, int totalDirection, int[] parent) {
		if (cnt == islandNum - 1 && check(parent)) {
			answer = Math.min(answer, totalDirection);
			return;
		}
		for (int i = start; i < setArr.size(); i++) {
			int[] tmp = setArr.get(i);
			if (direction[tmp[0]][tmp[1]] == Integer.MAX_VALUE) continue;
			System.out.println(tmp[0] + " " + tmp[1]);
			int a = getParent(tmp[0], parent);
			int b = getParent(tmp[1], parent);
			if (a == b) continue;
			parent[tmp[1]] = a;
			getMinDirection(start + 1, cnt + 1, totalDirection + direction[tmp[0]][tmp[1]], parent);
			parent[tmp[1]] = b;
		}
	}

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());

		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		map = new int[N][M];
		island = new int[N][M];
		for (int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			for (int j = 0; j < M; j++) {
				map[i][j] = Integer.parseInt(st.nextToken());
			}
		}
		
		islandNum = visitIsland();
		direction = new int[islandNum + 1][islandNum + 1];
		initDirection(islandNum);
		countDirection();

		setArr = new ArrayList<>();
		for (int i = 1; i <= islandNum; i++) {
			for (int j = i + 1; j <= islandNum; j++) {
				int[] set = {i, j};
				setArr.add(set);
			}
		}
		
		int[] parent = new int[islandNum + 1];
		for (int i = 1; i <= islandNum; i++) {
			parent[i] = i;
		}
		getMinDirection(0, 0, 0, parent);
		
		for (int i = 1; i <= islandNum; i++) {
			System.out.println(Arrays.toString(direction[i]));
		}
		
		if (answer == Integer.MAX_VALUE) {
			System.out.println(-1);
		} else {
			System.out.println(answer);			
		}
	}
}

