---
title: 紧急救援
thumbnail: /gallery/PAT.png
date: 2019-05-16 15:34:25
tags: PAT练习
categories: 刷题
---

作为一个城市的应急救援队伍的负责人，你有一张特殊的全国地图。在地图上显示有多个分散的城市和一些连接城市的快速道路。每个城市的救援队数量和每一条连接两个城市的快速道路长度都标在地图上。当其他城市有紧急求助电话给你的时候，你的任务是带领你的救援队尽快赶往事发地，同时，一路上召集尽可能多的救援队。

<!-- more -->

### 输入格式:

输入第一行给出4个正整数N、M、S、D，其中N（2≤N≤500）是城市的个数，顺便假设城市的编号为0 ~ (N−1)；M是快速道路的条数；S是出发地的城市编号；D是目的地的城市编号。

第二行给出N个正整数，其中第i个数是第i个城市的救援队的数目，数字间以空格分隔。随后的M行中，每行给出一条快速道路的信息，分别是：城市1、城市2、快速道路的长度，中间用空格分开，数字均为整数且不超过500。输入保证救援可行且最优解唯一。

### 输出格式:

第一行输出最短路径的条数和能够召集的最多的救援队数量。第二行输出从S到D的路径中经过的城市编号。数字间以空格分隔，输出结尾不能有多余空格。

### 输入样例:

```
4 5 0 3
20 30 40 10
0 1 1
1 3 2
0 3 3
0 2 2
2 3 2
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 输出样例:

```
2 60
0 1 3
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

 用java实现的，最后一个测试点超时了（用c++重写后AC）。

###  **思路：**

**利用Dijkstra算法求最短路径，由于题目要求：**

> 输出最短路径的条数和能够召集的最多的救援队数量

 **所以增加两个数组：**

pathTeams ： 当前城市集结的救援队伍的数量；

shortPathNum ：源点到当前顶点存在的最短路径数量；

**主要就是增加了存在相同长度最短路径时的处理，代码如下：**

```java
import java.util.Scanner;
import java.util.Stack;

public class EmergenceRescue {
	
	private static int[][] citys;
	private static int N,E,S,D;
	private static int INF = 10000;
	private static int[] teams;
	private static boolean[] visit;
	private static int[] dist;
	private static int[] path;
	private static int[] pathTeams;
	private static int[] shortPathNum;
	
	
	private static void InitCitys(Scanner scan) {
		N = scan.nextInt();E = scan.nextInt();
		S = scan.nextInt();D= scan.nextInt();
		citys = new int[N][N];
		teams = new int[N];
		for(int i = 0;i < N;i++) {
			teams[i] = scan.nextInt();
		}
		for(int i = 0;i < N;i++) {
			for(int j = 0;j < N;j++) {
				if(i == j) citys[i][j] = 0;
				else citys[i][j] = INF;
			}
		}
	}
	public static void CreateCitys(Scanner scan) {
		InitCitys(scan);
		for(int i = 0;i < E;i++) {
			int v = scan.nextInt(),w = scan.nextInt();
			int len = scan.nextInt();
			citys[v][w] = citys[w][v] = len;
		}
		scan.close();
	}
	public static void Dijkstra() {
		visit = new boolean[N];
		dist = new int[N];
		path = new int[N];
		pathTeams = new int[N];
		shortPathNum = new int[N];
		for(int i = 0;i < N;i++) {
			dist[i] = citys[S][i];
			visit[i] = false;
			shortPathNum[i] = 1;
			pathTeams[i] = teams[i] + teams[S];
			path[i] = S;
		}
		visit[S] = true;
		for(int i = 0;i < N;i++) {
			int ans = INF,k = -1;
			for(int j = 0;j < N;j++) {
				if(!visit[j] && dist[j] < ans) {
					ans = dist[j];
					k = j;
				}
			}
			if(ans == INF) break;
			visit[k] = true;
			for(int j = 0;j < N;j++) {
				if(!visit[j] && dist[k] + citys[k][j] <= dist[j]) {
					if(dist[k] + citys[k][j] < dist[j]) {
						dist[j] = dist[k] + citys[k][j];
						path[j] = k;
						pathTeams[j] = pathTeams[k] + teams[j];
						shortPathNum[j] = shortPathNum[k];
					}else {
						shortPathNum[j] += shortPathNum[k];
						if(pathTeams[k] + teams[j] > pathTeams[j] ) {
							pathTeams[j] = pathTeams[k] + teams[j];
							path[j] = k;
						}
					}
				}
			}
		}
	}
	public static void OutputPath() {
		Stack<Integer> pathStack = new Stack<Integer>();
		for(int i = D;i != S;i = path[i]) pathStack.push(i);
		System.out.print(S);
		while(!pathStack.isEmpty()) System.out.printf(" %d",pathStack.pop());
	}
	public static void main(String[] args) {
		Scanner scan = new Scanner(System.in);
		CreateCitys(scan);
		Dijkstra();
		System.out.printf("%d %d\n",shortPathNum[D],pathTeams[D]);
		OutputPath();
	}
}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)