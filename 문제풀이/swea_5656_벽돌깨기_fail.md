### swea_5656_벽돌깨기

#### 50개 테스트케이스 중 40개 맞음

- **중복 순열**을 이용해서 벽돌 깨기 순서 정함  
- **BFS**를 이용하여 주위 벽돌 깸


```
package P0414;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class swea_5656_벽돌깨기 {
	static int T,N,X,Y,map[][],rockCnt,min;
	static ArrayList<int[]> droplist;
	static int[] select;
	static int[] dy= {-1,1,0,0};
	static int[] dx= {0,0,-1,1};
	static BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
	static StringTokenizer st;
	static StringBuffer sb=new StringBuffer();
	
	public static void main(String[] args) throws NumberFormatException, IOException {
		T=Integer.parseInt(br.readLine());
		
		for (int t = 1; t <= T; t++) {
		
			st=new StringTokenizer(br.readLine());
			N=Integer.parseInt(st.nextToken());
			X=Integer.parseInt(st.nextToken());
			Y=Integer.parseInt(st.nextToken());
			rockCnt=0;
			map=new int[Y][X];
			min=Integer.MAX_VALUE;
			
			droplist=new ArrayList<int[]>();
			select=new int[N];
			
			for (int i = 0; i < Y; i++) {
				st=new StringTokenizer(br.readLine());
				for (int j = 0; j < X; j++) {
					map[i][j]=Integer.parseInt(st.nextToken());
					if(map[i][j]!=0) rockCnt++;
				}
			}
			
			sb.append("#").append(t).append(" ").append(process()).append("\n");
			
		}
		
		System.out.print(sb.toString());
		
	}

	private static int process() {
		
		//1. 구슬이 떨어지는 곳의 순서 중복 순열
		perm(0); 
		
		//2.구슬 떨어뜨리기
		for (int i = 0; i < droplist.size(); i++) { 
			drop(i);
		}
		
		return min;
	}
	
	private static void drop(int droplistIdx) {
		
		int order[]=droplist.get(droplistIdx); //떨어지는 곳의 순서
		
		//2-1.맵&벽돌개수 복사
		int[][] tmpMap=new int[Y][X];
		for (int i = 0; i < Y; i++) {
			for (int j = 0; j < X; j++) {
				tmpMap[i][j]=map[i][j];
			}
		}
		int tmpRockCnt=rockCnt;
		
		//2-2.벽돌 제거
		for (int i = 0; i < N; i++) {
			
			//a.떨어지는위치 구하기
			int dropY=-1;		//떨어지는 Y 위치
			int dropX=order[i]; //떨어지는 X 위치
			for (int j = 0; j < Y; j++) {
				if(tmpMap[j][dropX]!=0) {dropY=j; break;} 
			}
			if(dropY==-1) continue; //해당 줄에 구슬 없음

			int val=tmpMap[dropY][dropX];
			
			
			//b.떨어트리기
			Queue<int[]> q=new LinkedList<int[]>();
			boolean[][] v=new boolean[Y][X];
			
			q.add(new int[] {dropY,dropX,val});
			v[dropY][dropX]=true;
			
			while(!q.isEmpty()) {
				int[] tmp=q.poll();
				int y=tmp[0];
				int x=tmp[1];
				int range=tmp[2];
				
				tmpMap[y][x]=0;
				tmpRockCnt--;
				
				for (int r = 1; r < range; r++) {
					for (int d = 0; d < 4; d++) {
						int ny=y+dy[d]*r;
						int nx=x+dx[d]*r;
						
						if(ny>=0 && nx>=0 && ny<Y && nx<X && tmpMap[ny][nx]!=0 && !v[ny][nx]) {
							q.add(new int[] {ny,nx,tmpMap[ny][nx]});
							v[ny][nx]=true;
						}
					}
				}
			}
				
			//c.벽돌 밑으로 떨어지기
			for (int x = 0; x < X; x++) {
				int cnt=0;
				for (int y = Y-1; y >= 1; y--) {
					if(tmpMap[y][x]==0) cnt++;
					if(cnt>0 && tmpMap[y][x]!=0) {
						tmpMap[y+cnt][x]=tmpMap[y][x];
						tmpMap[y][x]=0;
					}
				}
			}
		}
		
		min=Math.min(min, tmpRockCnt);
	}

	private static void perm(int cnt) {
		
		if(cnt==N) {
			int[] tmp=new int[N];
			for (int i = 0; i < N; i++) {
				tmp[i]=select[i];
			}
			
			droplist.add(tmp);
			return;
		}
		
		for (int i = 0; i < X; i++) {
			select[cnt]=i;
			perm(cnt+1);
		}
	}
	
}

```
