> 作者：赵威

## 1. KMP算法《部分匹配表》

```java
对应暴力算法(简单但效率极低)
    
应用场景-字符串匹配问题
字符串匹配问题：：
(1) 有一个字符串 str1= ""字节跳动欢迎你 字节跳动欢迎你"" ，和一个子串 str2="字节跳动"
(2) 现在要判断 str1 是否含有 str2, 如果存在，就返回第一次出现的位置, 如果没有，则返回-1
(3) 若原问字符串长度为 m,模式字符串长度为 n，则用暴力匹配算法其时间复杂度为 O(m*n)
```

```java
暴力匹配算法
如果用暴力匹配的思路，并假设现在str1匹配到 i 位置，子串str2匹配到 j 位置，则有:
(1) 如果当前字符匹配成功（即str1[i] == str2[j]），则i++，j++ ，继续匹配下一个字符
(2) 如果失配（即str1[i]! = str2[j]），令i = i - (j - 1)，j = 0。相当于每次匹配失败时，i 回溯，j 被置为0。
(3)用暴力方法解决的话就会有大量的回溯，每次只移动一位，若是不匹配，移动 到下一位接着判断，浪费了大量的时间。(不    可行!)
(4) 暴力匹配算法实现
    public class BaoLi {
	public static void main(String[] args) {
		// 测试暴力匹配算法
		String str1="欢迎你";
		String str2="跳动欢迎你---";
		int index=baoli(str1, str2);
		System.out.println("index="+index);

	}
    //暴力匹配
	public static int baoli(String str1, String str2) {
		char[] s1 = str1.toCharArray();
		char[] s2 = str1.toCharArray();
		int s1Lenght = s1.length;
		int s2Lenght = s2.length;
		int i = 0;// 指向S1
		int j = 0;// 指向S2
		while (i < s1Lenght && j < s2Lenght) {// 保证检索时不越界
			if (s1[i] == s2[j]) {// 匹配成功
				i++;
				j++;
			} else {
				// 匹配失败
				i = i - (j - 1);
				j = 0;
			}

		}
		
		//判断是否匹配成功
		if(j == s2Lenght) {
            //如原文字符串 "abcde",模式匹配字符串"cd"
            //当找到时,说明j索引已经到了模式匹配字符串的最后即此时 j=1
            //而此时原文字符串的索引也会停止即i此时为3
            //因为模式匹配字符串是原文字符串的子串，同时由上述可得 i-j=3-1=2
            //正好是子串在原文字符串中出现的第一个位置(前提是字串和原文字符串中的字符连续匹配上)
			return i-j;
		}else {
			return -1;
		}
		
	}
}
```

```java
KMP算法介绍 
    
(1) KMP是一个解决模式串在文本串是否出现过，如果出现过，最早出现的位置的经典 算法
(2) Knuth-Morris-Pratt 字符串查找算法，简称为 “KMP 算法”，常用于在一个文本串S内查找一个模式串P 的出现位置，     这个算法由 Donald Knuth、Vaughan Pratt、James H. Morris三人于1977年联合发表故取这3人的姓氏命名此算法.
(3) KMP方法算法就利用之前判断过信息，通过一个next 数组，保存模式串中前后最长公共子序列的长度，每次回溯时，通过     next 数组找到，前面匹配过的位置，省去了大量的计算时间
(4) 其时间复杂度是线性阶的，相比暴力匹配算法时间复杂度大大降低 O(m+n)
```

```java
KMP算法最佳应用-字符串匹配问题
字符串匹配问题：： 
(1) 有一个字符串 str1= "BBC ABCDAB ABCDABCDABDE"，和一个子串 str2="ABCDABD "
(2) 现在要判断 str1 是否含有 str2, 如果存在，就返回第一次出现的位置, 如果没有，则返回-1
(3) 要求：使用KMP算法完成判断，不能使用简单的暴力匹配算法.
```



```java
KMP算法代码实现:

package com.bytedance.kmp;

import java.util.Arrays;

public class KMPAlgorithn {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String str1 = "BBC ABCDAB ABCDABCDABDE";
		String str2 = "ABCDABD";
		int[] next = kmpNext("ABCDABD");
		System.out.println("next=" + Arrays.toString(next));
		int index = kmpSearch(str1, str2, next);
		System.out.println("index=" + index);

	}

	// 搜索算法
	/**
	 * 
	 * @param str1 源字符串
	 * @param str2 字串
	 * @param next 字串对应的部分匹配表
	 * @return -1表示没匹配到，找到就反会第一个匹配的位置
	 */
	public static int kmpSearch(String str1, String str2, int[] next) {
		// 一str1为标准遍历
		for (int i = 0, j = 0; i < str1.length(); i++) {

			// 处理dest.charAt(i) ！= dest.charAt(j),调整j
			while (j > 0 && str1.charAt(i) != str2.charAt(j)) {
				j = next[j - 1];
			}

			if (str1.charAt(i) == str2.charAt(j)) {
				j++;
			}
			// 说明找到
			if (j == str2.length()) {
				return i - j + 1;
			}
		}
		return -1;
	}

	// 获取自字串的部分匹配值表
	public static int[] kmpNext(String dest) {
		// 创建一个数组保存部分匹配值
		int[] next = new int[dest.length()];
		// 如果字符串的长度为1
		// 其部分匹配值为0
		next[0] = 0;
		//
		for (int i = 1, j = 0; i < dest.length(); i++) {
			// 当dest.charAt(i) == dest.charAt(j)这个条件不成立时需要从next[j-1]获取新的j
			// 直到发现dest.charAt(i) == dest.charAt(j)成立才退出
			while (j > 0 && dest.charAt(i) != dest.charAt(j)) {
				j = next[j - 1];
			}
			// 当下面的条件成立时，部分匹配值就是1
			if (dest.charAt(i) == dest.charAt(j)) {
				j++;
			}
			next[i] = j;
		}
		return next;
	}
}
```

## 2. 回溯算法 (八皇后问题)

```java
问题概述：//国际象棋中的皇后，可以横向、纵向、斜向移动。如何在一个8X8的棋盘上放置8个皇后，使得任意两个皇后都不在同一条横线、竖线、斜线方向上(**一共有92种解法**)
    
下图的绿色格子是一个皇后在棋盘上的“封锁范围”，其他皇后不得放置在这些格子：
```

![image-20200227200620217](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200227200620217.png)

```java
下图的绿色格子是两个皇后在棋盘上的“封锁范围”，其他皇后不得放置在这些格子：
```

![image-20200227201726483](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200227201726483.png)

```java
八皇后问题代码演示:

package com.atguigu.recursion;
public class Queue8 {
	//定义一个max表示共有多少个皇后
	int max = 8;
	//定义数组array, 保存皇后放置位置的结果,比如 arr = {0 , 4, 7, 5, 2, 6, 1, 3} 
	int[] array = new int[max];
	static int count = 0;//统计有多少种解法
	static int judgeCount = 0;//统计皇后之间的冲突次数
	public static void main(String[] args) {
		//测试 ,8皇后
		Queue8 queue8 = new Queue8();
		queue8.check(0);//从0开始放
		System.out.printf("一共有%d解法", count);
		System.out.printf("一共判断冲突的次数%d次", judgeCount); // 1.5w
		
	}
	//定义一个方法，放置第n个皇后
	//特别注意： check 是 每一次递归时，进入到check中都有  for(int i = 0; i < max; i++)，因此会有回溯
	private void check(int n) {
		if(n == max) {  //n=8时代表所有皇后已经放好，得出了一种解法
			print();
			return;
		}
		
		//如果没有则依次放入皇后，并判断是否冲突
		for(int i = 0; i < max; i++) {
			//先把当前这个皇后 n , 放到该行的第1列(如放第2个皇后就是放在第二行的第一列先，数组表示就是                   array[..,0,..,])
			array[n] = i;
			//判断当放置第n个皇后到i列时，是否冲突
			if(judge(n)) { // 表示不冲突
				//接着放n+1个皇后,即开始递归
				check(n+1); //  
			}
			//如果冲突，就继续执行 array[n] = i; 即将第n个皇后，放置在本行得 后移的一个位置for(int i = 0;               i < max; i++) i++了
		}
	}
	
	//查看当放置第n个皇后, 应该检测该皇后是否和前面已摆放的皇后冲突
	//n 表示第n个皇后
	private boolean judge(int n) {
		judgeCount++;
		for(int i = 0; i < n; i++) {//每放一个皇后，就将当前皇后和已经放好的皇后作比较，是否冲突
			// 说明
			//1. array[i] == array[n]  表示判断 第n个皇后是否和前面的n-1个皇后在同一列
			//2. Math.abs(n-i) == Math.abs(array[n] - array[i]) 表示判断第n个皇后是否和第i皇后是否在同一                   斜线
			// n = 1  放置第 2列 1 n = 1 array[1] = 1
			// Math.abs(1-0) == 1  Math.abs(array[n] - array[i]) = Math.abs(1-0) = 1
			//3. 判断是否在同一行, 没有必要，n 每次都在递增
			if(array[i] == array[n] || Math.abs(n-i) == Math.abs(array[n] - array[i]) ) {
				return false;
			}
		}
		//说明不冲突
		return true;
	}
	
	//定义方法,将皇后摆放的位置输出(只有摆放正确,才能调用此方法进行输出)
	private void print() {
		count++;
		for (int i = 0; i < array.length; i++) {
			System.out.print(array[i] + " ");
		}
		//打印一个结果就换行输出
		System.out.println();
	}
} 

运行结果:
0 4 7 5 2 6 1 3 
0 5 7 2 6 3 1 4 
0 6 3 5 7 1 4 2 
0 6 4 7 1 3 5 2 
1 3 5 7 2 0 6 4 
1 4 6 0 2 7 5 3 
1 4 6 3 0 7 5 2 
1 5 0 6 3 7 2 4 
1 5 7 2 0 3 6 4 
1 6 2 5 7 4 0 3 
1 6 4 7 0 3 5 2 
1 7 5 0 2 4 6 3 
2 0 6 4 7 1 3 5 
2 4 1 7 0 6 3 5 
2 4 1 7 5 3 6 0 
2 4 6 0 3 1 7 5 
2 4 7 3 0 6 1 5 
2 5 1 4 7 0 6 3 
2 5 1 6 0 3 7 4 
2 5 1 6 4 0 7 3 
2 5 3 0 7 4 6 1 
2 5 3 1 7 4 6 0 
2 5 7 0 3 6 4 1 
2 5 7 0 4 6 1 3 
2 5 7 1 3 0 6 4 
2 6 1 7 4 0 3 5 
2 6 1 7 5 3 0 4 
2 7 3 6 0 5 1 4 
3 0 4 7 1 6 2 5 
3 0 4 7 5 2 6 1 
3 1 4 7 5 0 2 6 
3 1 6 2 5 7 0 4 
3 1 6 2 5 7 4 0 
3 1 6 4 0 7 5 2 
3 1 7 4 6 0 2 5 
3 1 7 5 0 2 4 6 
3 5 0 4 1 7 2 6 
3 5 7 1 6 0 2 4 
3 5 7 2 0 6 4 1 
3 6 0 7 4 1 5 2 
3 6 2 7 1 4 0 5 
3 6 4 1 5 0 2 7 
3 6 4 2 0 5 7 1 
3 7 0 2 5 1 6 4 
3 7 0 4 6 1 5 2 
3 7 4 2 0 6 1 5 
4 0 3 5 7 1 6 2 
4 0 7 3 1 6 2 5 
4 0 7 5 2 6 1 3 
4 1 3 5 7 2 0 6 
4 1 3 6 2 7 5 0 
4 1 5 0 6 3 7 2 
4 1 7 0 3 6 2 5 
4 2 0 5 7 1 3 6 
4 2 0 6 1 7 5 3 
4 2 7 3 6 0 5 1 
4 6 0 2 7 5 3 1 
4 6 0 3 1 7 5 2 
4 6 1 3 7 0 2 5 
4 6 1 5 2 0 3 7 
4 6 1 5 2 0 7 3 
4 6 3 0 2 7 5 1 
4 7 3 0 2 5 1 6 
4 7 3 0 6 1 5 2 
5 0 4 1 7 2 6 3 
5 1 6 0 2 4 7 3 
5 1 6 0 3 7 4 2 
5 2 0 6 4 7 1 3 
5 2 0 7 3 1 6 4 
5 2 0 7 4 1 3 6 
5 2 4 6 0 3 1 7 
5 2 4 7 0 3 1 6 
5 2 6 1 3 7 0 4 
5 2 6 1 7 4 0 3 
5 2 6 3 0 7 1 4 
5 3 0 4 7 1 6 2 
5 3 1 7 4 6 0 2 
5 3 6 0 2 4 1 7 
5 3 6 0 7 1 4 2 
5 7 1 3 0 6 4 2 
6 0 2 7 5 3 1 4 
6 1 3 0 7 4 2 5 
6 1 5 2 0 3 7 4 
6 2 0 5 7 4 1 3 
6 2 7 1 4 0 5 3 
6 3 1 4 7 0 2 5 
6 3 1 7 5 0 2 4 
6 4 2 0 5 7 1 3 
7 1 3 0 6 4 2 5 
7 1 4 2 0 6 3 5 
7 2 0 5 1 4 6 3 
7 3 0 2 5 1 6 4 
一共有92解法      一共判断冲突的次数15720次
```

## 3. 马踏棋盘算法(回溯算法)

```java
使用深度优化遍历算法(DFS)+贪心算法优化:
```

![image-20200316172354452](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200316172354452.png)

![image-20200316172706681](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200316172706681.png)

```java
(1) 马踏棋盘算法也被称为骑士周游问题
(2) 将马随机放在国际象棋的8×8棋盘Board[0～ 7][0～7]的某个方格中，马按走棋规则( 马走日字)
    进行移动。要求每个方格只进入一次，走 遍棋盘上全部64个方格
```

```java
(1) 马踏棋盘问题(骑士周游问题) 实际上是图的深度优先搜索(DFS)的应用。
(2)如果使用回溯（就是深度优先搜索）来解决，假如马儿踏了53个点，如图：走到了第53 个，坐标（1,0
），发现已经走到尽头，没办法，那就只能回退，查看其他的路径，就在棋盘上不停的回溯
(3) 分析第一种方式的问题，并使用贪心算法（ greedyalgorithm）进行优化。解决马踏棋盘问题
```

![image-20200316180414378](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200316180414378.png)

```java
package com.bytedance.horse;

import java.awt.Point;
import java.util.ArrayList;

public class HorseChessborad {
	private static int x;// 棋盘的列数
	private static int y;// 棋盘的行数
	// 创建数组标记棋盘的各个位置是否被访问过
	private static boolean visited[];
	// 标记棋盘的所有位置是否都被访问过
	private static boolean finished;// 如果为true表示访问过

	public static void main(String[] args) {
		// 测试马踏棋盘
		x = 6;
		y = 6;
		int row = 2;// 从第一行开始
		int colum = 4;// 从第一列开始
		// 创建棋盘
		int[][] chessborad = new int[x][y];
		visited = new boolean[x * y];
		// 测试耗时
		long start = System.currentTimeMillis();
		traverslChessbprad(chessborad, row - 1, colum - 1, 1);
		long end = System.currentTimeMillis();
		System.out.println("共耗时=" + (end - start) + "毫秒");
		// 输出棋盘的最后情况
		for (int[] rows : chessborad) {// 取出每一行
			for (int colums : rows) {// 取出每一行的每一个元素
				System.out.print(colums + "\t");
			}
			System.out.println();
		}
	}

	/***
	 * 完成马踏棋盘算法
	 * 
	 * @param chessborad 棋盘
	 * @param row        马当前位置的行从0开始记
	 * @param colum      马当前位的列从0开始记
	 * @param step       第几步，初始位置就是第一步
	 */
	public static void traverslChessbprad(int[][] chessborad, int row, int colum, int step) {
		chessborad[row][colum] = step;
		// 标记该位置已经被访问
		visited[row * x + colum] = true;
		// 获取当前位置可以走的位置的集合
		ArrayList<Point> ps = next(new Point(colum, row));
		// 遍历集合
		while (!ps.isEmpty()) {
			// 取出一个下一个可以走的位置
			Point p = ps.remove(0);
			// 判断该取出的店是否被访问过
			if (!visited[p.y * x + p.x]) {// 说明还没有被访问过
				// 递归
				traverslChessbprad(chessborad, p.y, p.x, step + 1);
			}
		}

		// while循环结束需要判断马是否完成了任务
		if (step < x * y && !finished) {// 条件成立说明没有完成任务
			chessborad[row][colum] = 0;
			visited[row * x + colum] = false;
		} else {
			finished = true;
		}
	}

	/**
	 * 功能：根据当前位置（Point对象）计算马还可以走哪些位置，将还能走的位置放入ArrayList集合中
	 * 
	 * @param curPoint 当前马的位置
	 * @return
	 */
	public static ArrayList<Point> next(Point curPoint) {
		// 创建ArrayList
		ArrayList<Point> ps = new ArrayList<Point>();
		// 创建一个point
		Point p1 = new Point();
		// 向左移动两列，向上移动一行得到一个交点5这个5就是马可以走的位置，将5这个点放入集合即可
		if ((p1.x = curPoint.x - 2) >= 0 && (p1.y = curPoint.y - 1) >= 0) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 6 这个位置
		if ((p1.x = curPoint.x - 1) >= 0 && (p1.y = curPoint.y - 2) >= 0) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 7 这个位置
		if ((p1.x = curPoint.x + 1) < x && (p1.y = curPoint.y - 2) >= 0) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 0 这个位置
		if ((p1.x = curPoint.x + 2) < x && (p1.y = curPoint.y - 1) >= 0) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 1 这个位置
		if ((p1.x = curPoint.x + 2) < x && (p1.y = curPoint.y + 1) < y) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 2 这个位置
		if ((p1.x = curPoint.x + 1) < x && (p1.y = curPoint.y + 2) < y) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 3 这个位置
		if ((p1.x = curPoint.x - 1) >= 0 && (p1.y = curPoint.y + 2) < y) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 4 这个位置
		if ((p1.x = curPoint.x - 2) >= 0 && (p1.y = curPoint.y + 1) < y) {
			ps.add(new Point(p1));
		}
		return ps;
	}

}

```

**使用贪心算法进行优化**

```java
package com.bytedance.horse;

import java.awt.Point;
import java.util.ArrayList;
import java.util.Comparator;

public class HorseChessborad {
	private static int x;// 棋盘的列数
	private static int y;// 棋盘的行数
	// 创建数组标记棋盘的各个位置是否被访问过
	private static boolean visited[];
	// 标记棋盘的所有位置是否都被访问过
	private static boolean finished;// 如果为true表示访问过

	public static void main(String[] args) {
		// 测试马踏棋盘
		x = 8;
		y = 8;
		int row = 1;// 从第一行开始
		int colum = 1;// 从第一列开始
		// 创建棋盘
		int[][] chessborad = new int[x][y];
		visited = new boolean[x * y];
		// 测试耗时
		long start = System.currentTimeMillis();
		traverslChessbprad(chessborad, row - 1, colum - 1, 1);
		long end = System.currentTimeMillis();
		System.out.println("共耗时=" + (end - start) + "毫秒");
		// 输出棋盘的最后情况
		for (int[] rows : chessborad) {// 取出每一行
			for (int colums : rows) {// 取出每一行的每一个元素
				System.out.print(colums + "\t");
			}
			System.out.println();
		}
	}

	/***
	 * 完成马踏棋盘算法
	 * 
	 * @param chessborad 棋盘
	 * @param row        马当前位置的行从0开始记
	 * @param colum      马当前位的列从0开始记
	 * @param step       第几步，初始位置就是第一步
	 */
	public static void traverslChessbprad(int[][] chessborad, int row, int colum, int step) {
		chessborad[row][colum] = step;
		// 标记该位置已经被访问
		visited[row * x + colum] = true;
		// 获取当前位置可以走的位置的集合
		ArrayList<Point> ps = next(new Point(colum, row));
		// 对ps进行排序，排序规则就是对ps的所有的Point对象的下一步的位置的数目进行非递减排序
		sort(ps);
		// 遍历集合
		while (!ps.isEmpty()) {
			// 取出一个下一个可以走的位置
			Point p = ps.remove(0);
			// 判断该取出的店是否被访问过
			if (!visited[p.y * x + p.x]) {// 说明还没有被访问过
				// 递归
				traverslChessbprad(chessborad, p.y, p.x, step + 1);
			}
		}

		// while循环结束需要判断马是否完成了任务
		if (step < x * y && !finished) {// 条件成立说明没有完成任务
			chessborad[row][colum] = 0;
			visited[row * x + colum] = false;
		} else {
			finished = true;
		}
	}

	/**
	 * 功能：根据当前位置（Point对象）计算马还可以走哪些位置，将还能走的位置放入ArrayList集合中
	 * 
	 * @param curPoint 当前马的位置
	 * @return
	 */
	public static ArrayList<Point> next(Point curPoint) {
		// 创建ArrayList
		ArrayList<Point> ps = new ArrayList<Point>();
		// 创建一个point
		Point p1 = new Point();
		// 向左移动两列，向上移动一行得到一个交点5这个5就是马可以走的位置，将5这个点放入集合即可
		if ((p1.x = curPoint.x - 2) >= 0 && (p1.y = curPoint.y - 1) >= 0) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 6 这个位置
		if ((p1.x = curPoint.x - 1) >= 0 && (p1.y = curPoint.y - 2) >= 0) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 7 这个位置
		if ((p1.x = curPoint.x + 1) < x && (p1.y = curPoint.y - 2) >= 0) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 0 这个位置
		if ((p1.x = curPoint.x + 2) < x && (p1.y = curPoint.y - 1) >= 0) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 1 这个位置
		if ((p1.x = curPoint.x + 2) < x && (p1.y = curPoint.y + 1) < y) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 2 这个位置
		if ((p1.x = curPoint.x + 1) < x && (p1.y = curPoint.y + 2) < y) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 3 这个位置
		if ((p1.x = curPoint.x - 1) >= 0 && (p1.y = curPoint.y + 2) < y) {
			ps.add(new Point(p1));
		}
		// 判断马儿可以走 4 这个位置
		if ((p1.x = curPoint.x - 2) >= 0 && (p1.y = curPoint.y + 1) < y) {
			ps.add(new Point(p1));
		}
		return ps;
	}

	// 根据当前这个一步的所有的下一步的选择位置，进行·非递减排序减少回溯
	public static void sort(ArrayList<Point> ps) {
		ps.sort(new Comparator<Point>() {
			@Override
			public int compare(Point o1, Point o2) {
				// 获取o1的下一步的所有的位置个数
				int count1 = next(o1).size();
				// 获取o2的下一步的所有位置个数
				int count2 = next(o2).size();
				if (count1 < count2) {
					return -1;
				} else if (count1 == count2) {
					return 0;
				} else {
					return 1;
				}
			}

		});
	}

}

```

## 4. 汉诺塔问题 (分治算法)

```java
分治算法介绍 
(1)分治法是一种很重要的算法。字面上的解释是“分而治之”，就是把一个复杂的问题分 成两个或更多的相同或相似的子问      题，再把子问题分成更小的子问题…… 直到最后子问题可以简单的直接求解，原问题的解即子问题的解的合并。这个技巧是    很 多高效算法的基础，如排序算法(快速排序，归并排序)，傅立叶变换(快速傅立叶变换)……
(2) 分治算法可以求解的一些经典问题 
    • 二分搜索 • 大整数乘法 • 棋盘覆盖 
    • 合并排序 • 快速排序 • 线性时间选择 
    • 最接近点对问题 • 循环赛日程表 • 汉诺塔
    
 //最难点是分的过程！！！！！！
```

```java
分治法在每一层递归上都有三个步骤： 
(1) 分解：将原问题分解为若干个规模较小，相互独立，与原问题形式相同的子问题 
(2) 解决：若子问题规模较小而容易被解决则直接解，否则递归地解各个子问题 
(3) 合并：将各个子问题的解合并为原问题的解
```

```java
分治(Divide-and-Conquer(P))算法设计模式如下:

if |P|≤n0
then return(ADHOC(P))
//将P分解为较小的子问题 P1 ,P2 ,…,Pk 
for i← 1 to k
do yi← Divide-and-Conquer(pi) 递归解决pi
T ← MERGE(y1,y2.....yk) 合并子问题
return (T)
    
其中|P|表示问题P的规模；n0为一阈值，表示当问题P的规模不超过n0 时，问题已容易直接解出，不必再继续分解。ADHOC(P) 是该分治法中的基本子算法，用于直接解小规模的问题P因此，当P的规模不超过 n0时直接用算法ADHOC(P)求解。算法MERGE(y1,y2,…,yk) 是该分治法中的合并子算法，用于将P的子问题P1 ,P2 ,…,Pk的相应的解y1,y2,…,yk合并为P的解。

```

```java
汉诺塔思路分析:

如果是有一个盘， A->C 如果有 n >= 2 情况，总是可以看做是两个盘 1.最下边的盘 2. 上面的盘
(1) 先把最上面的盘 A->B 
(2) 把最下边的盘 A->C 
(3) 把B塔的所有盘 从 B->C
 //(1) (2) (3)就是小规莫范围内的递归
```

```java
package com.bytaedance.dac;

public class Hanoitower {

	public static void main(String[] args) {
		// 测试
		hanoiTower(5, 'A', 'B', 'C');

	}

	// 分治算法汉诺塔
	/**
	 * @param num 盘子的数量
	 * @param a   a柱子
	 * @param b   b柱子
	 * @param c   c柱子
	 */
	public static void hanoiTower(int num, char a, char b, char c) {
		// 如果只有一个盘
		if (num == 1) {
			System.out.println("第1个盘从" + a + "->" + c);
		} else {
			// 大于1个盘总是看成2个盘
			// 1.把最上面的所有盘移动到B
			hanoiTower(num - 1, a, c, b);
			// 2.把最下面的盘从A移到C
			System.out.println("第" + num + "个盘从" + a + "->" + c);
			// 3.把B塔的盘移动到C
			hanoiTower(num - 1, b, a, c);
		}
	}
}
```

## 5.数据结构

```java
数据结构分2种: 线性结构 非线性结构
    线性结构：
    1.线性结构为常用数据结构，特点是数据元素之间存在一对一线性关系(如数组)
    2.线性结构有两种不同的存储结构，即顺序存储结构和链式存储结构。顺序存储的线性表为顺序表，顺序表中的存储元素      是连续的(指的是分配的内存地址)
    3.链式存储(如链表)的线性表称为链表，链表中的存储元素不一定是连续的，元素节点中存放数据元素以及相邻元素的地址信息
    4.线性结构常见的有：数组 队列 链表和栈
    非线性结构：二维数组 多为数组 广义表 树结构 图结构
```

## 6. 稀疏数组和队列

```java
当一个数组中大部分元素为０，或者为同一个值的数组时，可以使用稀疏数组来保存 该数组。
稀疏数组的处理方法是: 
(1)记录数组一共有几行几列，有多少个不同的值 
(2)把具有不同值的元素的行列及值记录在一个小规模的数组中(稀疏数组)，从而缩小程序的规 模
```

如图：1-1

![image-20200219192423583](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200219192423583.png)

```java
上图是将6*7的数组转换为9*3的新数组：
     6 7 8代表6行7列有8个有效数字
    [1] 0 3 22: 6*7的数组的数组第一行有效数字为22和15,这个就代表有效数字22所处的位置为第0行的第3列
                而15就是处在第0行的第6列,同理右表的其他表示与上述原理一样.
```

应用实例：1-2

![image-20200219192821228](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200219192821228.png)

```java
二维数组转稀疏数组的思路：
1.遍历原始二维数组,得到有效数据的个数sum
2.根据sum建立稀疏数组sparseAtt int[sum+1][3]
3.将二维数组的有效数据存入到稀疏数组
稀疏数组转原始数组的思路：
1.先读取稀疏数组的第一行,根据第一行的数据创建原始的二维数组比如原始的 ChArr=int [11][11]
2.再读取稀疏数组后几行的数据,并赋值给原始二维数组.
    
代码实现：
    
package com.bytedance.sparsearray;
/*
 * 数据结构之稀疏数组
 */
public class SparseArray {
	public static void main(String[] args) {
		//1.创建一个原始得二维数组11*11(0:表示无棋子 1:表示黑子  2:表示白色棋子)
		int chessArr1[][]=new int[11][11];
		chessArr1[1][2]=1;
	    chessArr1[2][3]=2;
	    System.out.println("原始的二维数组:");
	    //利用增强型for循环输出原始二维数组
	    for(int[] row:chessArr1) {//二维数组遍历先遍历每一行存入一维数组
	    	for(int data:row) {//遍历一维数组即可得相应元素
	    		System.out.printf("%d\t",data);
	    	}
	    	System.out.println();
	    }
	    //将二维数组转为稀疏数组
	    //1.遍历二维数组得到非0数据的个数
	    int count=0;
	    for(int i=0;i<chessArr1.length;i++) {
	    	for(int j=0;j<chessArr1.length;j++) {
	    		if(chessArr1[i][j]!=0) {
	    			count++;
	    		}
	    	}
	    }
	    System.out.println("有效数据个数:"+count+"个");
	    //创建对应的稀疏数组
	    int sparseArr[][]=new int[count+1][3];
	    //给稀疏数组赋值
	    sparseArr[0][0]=11;
	    sparseArr[0][1]=11;
	    sparseArr[0][2]=count;//(相当于value)
	    //遍历二维数组将非0值存放到稀疏数组中
	    int cont=0;//用于记录是第几个非0数据
	    for(int i=0;i<chessArr1.length;i++) {
	    	for(int j=0;j<chessArr1.length;j++) {
	    		if(chessArr1[i][j]!=0) {
	    			cont++;
	    			sparseArr[cont][0]=i;
	    		    sparseArr[cont][1]=j;
	    		    sparseArr[cont][2]=chessArr1[i][j];
	    		}
	    	}
	    }
	    //输出稀疏数组的形式
	    System.out.println("稀疏数组：");
	    for(int i=0;i<sparseArr.length;i++) {
	    	System.out.printf("%d\t%d\t%d\t\n",sparseArr[i][0],sparseArr[i][1],sparseArr[i][2]);
	    }
	    System.out.println();
	    //将稀疏数组恢复为二维数组
	    /*
	     * 1.先读取稀疏数组的第一行,根据第一行的数据创建原始的二维数组比如原始的 ChArr=int [11][11]
         * 2.再读取稀疏数组后几行的数据,并赋值给原始二维数组.
	     */
	    int chessArr2[][]=new int[sparseArr[0][0]][sparseArr[0][1]];
	    //遍历稀疏数组得其他行从第二行开始,赋值给原始二维数组
	    for(int i=1;i<sparseArr.length;i++) {
	    	chessArr2[sparseArr[i][0]][sparseArr[i][1]]=sparseArr[i][2];
	    }
	    //输出恢复后的二维数组
	    System.out.println("恢复后的二维数组：");
	    for(int[] row:chessArr2) {//二维数组遍历先遍历每一行存入一维数组
	    	for(int data:row) {//遍历一维数组即可得相应元素
	    		System.out.printf("%d\t",data);
	    	}
	    	System.out.println();
	  }
	}
}

队列：队列是一个有序列表，可用数组或者链表实现
     原则：先入先出即先存入的数据先取出,后存入的数据后取出原理如图1-3(数组模拟)
     队列本身是一个有序列表,如果使用数组的结构存储队列的数据,队列的声明数组为图1-3MaxSize为队列的最大容量
     由于队列的输出与输入为从前后两端来处理,因此需要2个变量front rear 分别记录队列前后端的下标front 会随着数      据输出而改变 rear 会随着数据输入而改变.
```

图1-3

![image-20200220123125342](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200220123125342.png)

```java
front指向队列的头部 rear指向队列的尾部 初始化均为-1 向队列存入数据是从尾部存,取数据是从队列头部开始取

将数据存入队列称为addQueue addQueue处理的步骤分析思路：
 1.将尾指针向后移即 rear+1 条件是 front==rear 意为此时队列数组为空,可以存入数据
 2.如果 rarr指针小于MaxSize-1 时则可以将数据存入rear所指向的数组元素，当 raer==MaxSize-1 时代表数组已经存满    数据,则不能继续存放数据.
    
代码实现队列：
//注意：
    
此代码模拟的数组数列有问题,当取出数据后front头部指针会后移,但是取出数据后空余的位置则不能填入数据,即就算当把数组的全部数据取出以后数组为空,也不能再填入数据,此数组不能复用.原因是没有进行取余,利用环形队列可以解决这个问题,同时也是对数组模拟队列的优化版.

公式: (尾部-头部+表长)%表长 进行了取余运算得到的结果就是队列中有效数据的个数
    
package com.bytedance.queue;
import java.util.Scanner;
/*
 * 数组模拟队列
 */
public class ArrayQueue01 {
  public static void main(String[] args) {
     //测试 创建队列
	 ArrayQueue arrayQueue=new ArrayQueue(3) ;
     char key=' ';//接受用户输入的数据
     //创建扫描器
     Scanner src=new Scanner(System.in);
     //设置变量 为死循环
     boolean flg=true;
     //输出一个菜单
     while(flg) {
    	 System.out.println("s(show):显示队列！");
    	 System.out.println("e(exit):退出程序");
    	 System.out.println("a(add):添加数据到队列");
    	 System.out.println("g(get):从队列取出数据");
    	 System.out.println("h(head):查看队列头的数据");
		key = src.next().charAt(0);//接受一个字符串
    	 switch(key) {
    	 case 's':
    		 arrayQueue.showQueue();
    		 break;
    	 case 'a':
    		 System.out.println("请输入一个数");
    		 int value =src.nextInt();
    		 arrayQueue.addQueue(value);
    		 break;
    	 case 'g':
    		 try {
    			int res= arrayQueue.getQueue();
    			System.out.printf("取出的数据是%d\n",res);
    		 }catch(Exception e) {
    			 System.out.println(e.getMessage());
    		 }
    		 break;
    	 case 'h'://查看队列头数据
    		 try {
     			int res= arrayQueue.headQueue();
     			System.out.printf("输出队列头的数据是%d\n",res);
     		 }catch(Exception e) {
     			 System.out.println(e.getMessage());
     		 }
    		 break;
    	 case 'e'://退出
    		 src.close();
    		 flg=false;
    		 break;
    		 default:
    			 break;
    	 }
     }
    System.out.println("程序已退出！"); 
 }

}
//使用数组模拟队列--->编写一个ArrayQueue01类
class ArrayQueue{
	//数组最大容量
	private int maxSize;
	//指向队列头部
	private int front;
	//指向队列尾部
	private int rear;
	//存放数据的数组
	private int[] array;
	//创建队列的构造器
	public ArrayQueue(int arrayMaxSize ) {
		maxSize=arrayMaxSize;
		//初始化数组
		array=new int[maxSize];
		front=-1;//指向队列头部(队列头的前一个位置即-1)
		rear=-1;//指向队列尾部，直接指向队列的最后一个数据
	}
	//判断队列是否满
	public boolean isFull() {
		return rear==maxSize-1;
	}
	//判断队列是否为空
	public boolean isEmpty() {
		return front==rear;
	}
	//添加数据到队列
	public void addQueue(int n) {
		//判断队列是否满	
		if(isFull()) {
			System.out.println("队列已满,不能添加数据");
			return;
		}
		//rear后移
		rear++;
		array[rear]=n;
	}
	//获取队列数据进行出队列
	public int getQueue() {
		//判断队列是否为空
		if(isEmpty()){
		//如果是空的就抛出一个异常
	    throw new RuntimeException("队列为空，无法取数据！");
		}
		//不为空取数据
		front++;
		return array[front];
	}
	//显示队列所有数据
	public void showQueue() {
		//遍历队列 判断队列是否为空
		if(isEmpty()) {
			System.out.println("队列为空,没有数据！");
			return;
		}
		for(int i=0;i<array.length;i++) {
			System.out.printf("array[%d]=%d\n",i,array[i]);
		}
	}
	//显示队列头部
	public int headQueue() {
		if(isEmpty()) {
			throw new RuntimeException("队列为空，没有数据！");
		}
		return array[front+1];
	}
} 
```

```java'
数组模拟环形队列：(对以上代码进行优化)
```

![image-20200220214530875](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200220214530875.png)

```java
思路分析:
 1.front 变量含义进行调整:front 指向队列的第一个元素,即array[front]就是队列的第一个元素,front初始值为0
 2.rear 变量含义也许进行调整:raer指向队列的最后一个元素的后一个位置,为的是预留一个空间做约定,rear初始值为0
 3.队列满时满足的条件:(rear+1)%MaxSize==front
   //当rear1指向倒数第二个位置时进行加1然后和MaxSize取模运算如果等于front说明队列已满，最后位置的元素是动态变      化的，当存入数据动作不变时，而一直进行取数据时，很有可能rear就会指向队列的头部位置即Queue
 4.队列为空时满足:rear==front
 5.队列中有效数字个数:(rear-front+MaxSize)%MaxSize (拿数试能试出来)
```

```java
环形队列代码实现：
 /*
 * 数组模拟环形队列
 */
public class ArrayQueue02 {
   public static void main(String[] args) {
		 //测试 创建队列
		ArrayQueue arrayQueue=new ArrayQueue(6) ;//队列最大容量仍是5
	     char key=' ';//接受用户输入的数据
	     //创建扫描器
	     Scanner src=new Scanner(System.in);
	     //设置变量 为死循环
	     boolean flg=true;
	     //输出一个菜单
	     while(flg) {
	    	 System.out.println("s(show):显示队列！");
	    	 System.out.println("e(exit):退出程序");
	    	 System.out.println("a(add):添加数据到队列");
	    	 System.out.println("g(get):从队列取出数据");
	    	 System.out.println("h(head):查看队列头的数据");
			key = src.next().charAt(0);//接受一个字符串
	    	 switch(key) {
	    	 case 's':
	    		 arrayQueue.showQueue();
	    		 break;
	    	 case 'a':
	    		 System.out.println("请输入一个数");
	    		 int value =src.nextInt();
	    		 arrayQueue.addQueue(value);
	    		 break;
	    	 case 'g':
	    		 try {
	    			int res= arrayQueue.getQueue();
	    			System.out.printf("取出的数据是%d\n",res);
	    		 }catch(Exception e) {
	    			 System.out.println(e.getMessage());
	    		 }
	    		 break;
	    	 case 'h'://查看队列头数据
	    		 try {
	     			int res= arrayQueue.headQueue();
	     			System.out.printf("输出队列头的数据是%d\n",res);
	     		 }catch(Exception e) {
	     			 System.out.println(e.getMessage());
	     		 }
	    		 break;
	    	 case 'e'://退出
	    		 src.close();
	    		 flg=false;
	    		 break;
	    		 default:
	    			 break;
	    	 }
	     }
	    System.out.println("程序已退出！"); 
	}
}
//使用数组模拟队列--->编写一个ArrayQueue01类
class ArrayQueue {
	//数组最大容量
	private int maxSize;
	//front 变量含义进行调整:front 指向队列的第一个元素,即array[front]就是队列的第一个元素,front初始值为0
	private int front;
	//rear 变量含义也许进行调整:rear指向队列的最后一个元素的后一个位置,为的是预留一个空间做约定,rear初始值为0
	private int rear;
	//存放数据的数组
	private int[] array;
	//创建队列的构造器
	public ArrayQueue(int arrayMaxSize ) {
		maxSize=arrayMaxSize;
		//初始化数组
		array=new int[maxSize];
		front=0;//指向队列头部
		rear=-0;//指向队列尾部，直接指向队列的最后一个数据
	}
	//判断队列是否满
	public boolean isFull() {
		return (rear+1)%maxSize==front;
	}
	//判断队列是否为空
	public boolean isEmpty() {
		return front==rear;
	}
	//添加数据到队列
	public void addQueue(int n) {
		//判断队列是否满	
		if(isFull()) {
			System.out.println("队列已满,不能添加数据");
			return;
		}
		//直接将数据放入数组
		array[rear]=n;
		//rear后移 需取模防止数组越界
		rear=(rear+1)%maxSize;
	}
	//获取队列数据进行出队列
	public int getQueue() {
		//判断队列是否为空
		if(isEmpty()){
		//如果是空的就抛出一个异常
	    throw new RuntimeException("队列为空，无法取数据！");
		}
		//不为空取数据
		/*
		 * front是指向队列的第一个元素的
		 * 1.将front对应的值保留到一个临时变量value
		 * 2.将front后移，此时需进行取模运算,如果直接进行front++会数组越界,当front++到maxSize时
		 *   还会后移就造成数组越界的问题
		 * 3.将临时保存得value返回即可
		 */
		int value=array[front];
		front=(front+1)%maxSize;
		return value;
	}
	//显示队列所有数据
	public void showQueue() {
		//遍历队列 判断队列是否为空
		if(isEmpty()) {
			System.out.println("队列为空,没有数据！");
			return;
		}
		/*分析：从front开始进行遍历,遍历多少元素
		 * 
		 */
		
		for(int i=front;i<front+size();i++) {
			System.out.printf("array[%d]=%d\n",i%maxSize,array[i%maxSize]);
		}
	}
	//求出当前队列得有效数据个数已进行过上面的显示队列元素,否则显示队列元素得代码则无法执行
	public int size() {
		return (rear-front+maxSize)%maxSize;
	}
	//显示队列头部
	public int headQueue() {
		if(isEmpty()) {
			throw new RuntimeException("队列为空，没有数据！");
		}
		return array[front];
	}
} 
```

## 7. 链表之单向链表

### 1.链表

```java
链表是有序列表,在内存中的存储如下图：(实际存储的图)
    data域代表存储的数据 next域代表指向下一个节点的地址
链表是以节点的方式来存储,必须包含以上2域,表的各个节点不一定是连续存储的,链表带有头节点的链表和没有带头节点的链表,是否带有头节点根据实际需求决定！
```

![image-20200221211029564](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200221211029564.png)

```java
单链表(带头结点) 逻辑结构示意图如下:
此图是逻辑上的实现如a1的next指针指向a2的头,但实际上不一定指向a2,这只是一个逻辑上的实现原理 ^ 表示next为空
```

![image-20200221211058129](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200221211058129.png)

### 2.单链表应用实例

```java
使用带head的单向链表实现 水浒传108将的排名管理
    1.完成对英雄人物的增删改查
    2.第一种方法:添加英雄时直接添加到链表尾部,不用考虑顺序
    3.第二种方法:添加英雄时将英雄根据英雄排名添加到对应位置(若有这个排名,添加失效,给出相应提示！)
```

### 3.第一种方法的代码实现

```java
package com.bytedance.linkedlist;
/*
 * 此写法没有限制顺序添加
 */
public class SingleLinkedList {
  public static void  main(String[] args) {
	  //测试
	  //1.创建节点
	  HeroNode hero1=new HeroNode(1,"宋江","及时雨");
	  HeroNode hero2=new HeroNode(2,"卢俊义","玉麒麟");
	  HeroNode hero3=new HeroNode(3,"吴用","智多星");
	  HeroNode hero4=new HeroNode(4,"林冲","豹子头");
	 //创建链表将上述元素加入链表
	  SingleLinkedListDemo  singleLinkedList=new  SingleLinkedListDemo();
	  singleLinkedList.add(hero1);
	  singleLinkedList.add(hero2);
	  singleLinkedList.add(hero3);
	  singleLinkedList.add(hero4);
	  
	  singleLinkedList.list();
  }
}
//定义SingleLinkedListDemo单链表，管理英雄
class SingleLinkedListDemo{
	//初始化一个头节点,头节点不能动,不存放具体数据
	private HeroNode head=new HeroNode(0,"","");
	//添加节点到链表
    //分析：不考虑顺序时找到当前链表的最后节点,将最后这个节点得next指向新的节点
	public void add(HeroNode heroNode) {
	//由于head节点不能动,故需要定义一个辅助指针temp
	HeroNode temp=head;
	//遍历链表,找到最后的节点
	while(true) {
	//当temp.next==null	时已经到了节点的最后
	 if(temp.next==null) {
		 break;
	 }
	 //若没有找到最后得节点temp就后移
	 temp=temp.next;
	}
	//当退出while循环时temp就指向了链表的最后 此时将链表连接即可
	temp.next=heroNode;
 }
	//显示链表(遍历) 通过辅助指针进行遍历
	public void list() {
    //判断链表是否为空
	 if(head.next==null) {
		 System.out.println("链表为空！");
		 return;
	 }
	 //因为头节点不能动,定义一个辅助指针进行遍历
	 HeroNode temp=head.next;
	 while(true) {
		 //判断是否到链表的最后
		 if(temp==null) {
			 break;
		 }
		 //输出节点信息
		 System.out.println(temp);
		 //将temp后移(不后移会造成死循环)
		 temp=temp.next;
	  }
	}
}
//定义一个HeroNode,每个HeroNode对象就是一个节点
class HeroNode{
	public int no;//编号
	public String name;//名字
	public String nickname;//昵称
	public HeroNode next;//指向下一个节点
	//构造器
	public HeroNode(int no, String name, String nickname) {
		this.no = no;
		this.name = name;
		this.nickname = nickname;
	}
	//显示方便,重写toString
	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + ", nickname=" + nickname + "]";
	}
}

运行结果：
     HeroNode [no=1, name=宋江, nickname=及时雨]
     HeroNode [no=2, name=卢俊义, nickname=玉麒麟]
     HeroNode [no=3, name=吴用, nickname=智多星]
     HeroNode [no=4, name=林冲, nickname=豹子头]  
```

### 4.第二种方法的代码实现

```java
思路分析：
1. 通过辅助指针temp经遍历找到新添加节点的位置
2. 新的节点.next=temp.next
3. 将temp.next=新的节点
```

```java
package com.bytedance.linkedlist;
/*
 * 此写法限制顺序添加
 */
public class SingleLinkedList {
  public static void  main(String[] args) {
	  //测试
	  //1.创建节点
	  HeroNode hero1=new HeroNode(1,"宋江","及时雨");
	  HeroNode hero2=new HeroNode(2,"卢俊义","玉麒麟");
	  HeroNode hero3=new HeroNode(3,"吴用","智多星");
	  HeroNode hero4=new HeroNode(4,"林冲","豹子头");
	 //按照编号顺序插入
	  SingleLinkedListDemo  singleLinkedList=new  SingleLinkedListDemo();
	  singleLinkedList.addByOder(hero1);
	  singleLinkedList.addByOder(hero2);
	  singleLinkedList.addByOder(hero3);
	  singleLinkedList.addByOder(hero4);
	  singleLinkedList.addByOder(hero4);
      singleLinkedList.list();
  }
}
//定义SingleLinkedListDemo单链表，管理英雄
class SingleLinkedListDemo{
	//初始化一个头节点,头节点不能动,不存放具体数据
	private HeroNode head=new HeroNode(0,"","");
	//添加节点到链表
    //分析：不考虑顺序时找到当前链表的最后节点,将最后这个节点得next指向新的节点
	public void add(HeroNode heroNode) {
	//由于head节点不能动,故需要定义一个辅助指针temp
	HeroNode temp=head;
	//遍历链表,找到最后的节点
	while(true) {
	//当temp.next==null	时已经到了节点的最后
	 if(temp.next==null) {
		 break;
	 }
	 //若没有找到最后得节点temp就后移
	 temp=temp.next;
	}
	//当退出while循环时temp就指向了链表的最后 此时将链表连接即可
	temp.next=heroNode;
 }
	//第二种方式添加方式,根据英雄排名将英雄插到指定位置
	public void addByOder(HeroNode heroNode) {
	//因头节点不能动,故通过辅助指针temp找到添加的位置	
    //链表是单链表的原因,所以找的temp是位于添加位置的前一个节点,否则无法加入数据
		HeroNode temp=head;
		//flag标志添加的编号是否存在,默认是false
		boolean flag=false;
		while(true) {
			//说明temp已在链表的最后
			if(temp.next==null) {
				break;//此时无论如何都要break
			}
			//说明位置已经找到,即temp的后面插入即可
			if(temp.next.no>heroNode.no) {
				break;
			}else if(temp.next.no==heroNode.no) {
			       //说明希望添加的编号已经存在
			        flag=true;//说明编号存在
			        break;
			}
			//以上条件均不成立的时候temp继续向后移,遍历当链表
			temp=temp.next;
		}
		//判断flag的值
		if(flag) {
			System.out.printf("计划插入的英雄的编号已经存在,不能加入了！\n",heroNode.no);
		}else {
			//插入到链表中 temp后面
			heroNode.next=temp.next;
			temp.next=heroNode;
		}
	}
	//显示链表(遍历) 通过辅助指针进行遍历
	public void list() {
    //判断链表是否为空
	 if(head.next==null) {
		 System.out.println("链表为空！");
		 return;
	 }
	 //因为头节点不能动,定义一个辅助指针进行遍历
	 HeroNode temp=head.next;
	 while(true) {
		 //判断是否到链表的最后
		 if(temp==null) {
			 break;
		 }
		 //输出节点信息
		 System.out.println(temp);
		 //将temp后移(不后移会造成死循环)
		 temp=temp.next;
	  }
	}
}
//定义一个HeroNode,每个HeroNode对象就是一个节点
class HeroNode{
	public int no;//编号
	public String name;//名字
	public String nickname;//昵称
	public HeroNode next;//指向下一个节点
	//构造器
	public HeroNode(int no, String name, String nickname) {
		this.no = no;
		this.name = name;
		this.nickname = nickname;
	}
	//显示方便,重写toString
	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + ", nickname=" + nickname + "]";
	}
}

运行结果：
     计划插入的英雄的编号已经存在,不能加入了！
     HeroNode [no=1, name=宋江, nickname=及时雨]
     HeroNode [no=2, name=卢俊义, nickname=玉麒麟]
     HeroNode [no=3, name=吴用, nickname=智多星]
     HeroNode [no=4, name=林冲, nickname=豹子头]  
```

### 5. 单链表节点的修改

```java
1. 编号不变,名字和昵称可以改变
   代码实现：
  ===================================================================================================
    修改节点部分的低代码：
    //修改节点的信息
	//说明：根据newHeroNode的编号修改名称和昵称但是编号不能修改
	public void update(HeroNode newHeroNode) {
		//判断链表是否为空
		if(head.next==null) {
			 System.out.println("链表为空！");
			 return;
		 }
		//找到需要修改的节点,定义一个辅助指针temp
		HeroNode temp=head.next;
		//代表是否找到该节点
		boolean flag=false;
		while(true) {
			if(temp==null) {
				//已经遍历链表结束到了链表的最后
				break;
			}
			if(temp.no==newHeroNode.no) {
				//表示找到
				flag=true;
				break;
            }
			temp=temp.next;
		}
		//根据flag判断是否找到需要修改的节点
		if(flag) { 
			temp.name = newHeroNode.name; 
		    temp.nickname = newHeroNode.nickname;
		} else { 
			//没有找到
			System.out.printf("没有找到 编号 %d 的节点，不能修改\n", newHeroNode.no);
		}
	} 
=====================================================================================================
 完整代码：
 思路(1) 先找到该节点,通过遍历
     (2) temp.name = newHeroNode.name; 
         temp.nickname= newHeroNode.nickname   
         //修改节点可直接找到待修改信息的节点,无需找其前一个节点或者后一个节点.
 package com.bytedance.linkedlist;
/*
 * 此写法限制顺序添加
 */
public class SingleLinkedList {
  public static void  main(String[] args) {
	  //测试
	  //1.创建节点
	  HeroNode hero1=new HeroNode(1,"宋江","及时雨");
	  HeroNode hero2=new HeroNode(2,"卢俊义","玉麒麟");
	  HeroNode hero3=new HeroNode(3,"吴用","智多星");
	  HeroNode hero4=new HeroNode(4,"林冲","豹子头");
	 //按照编号顺序插入
	  SingleLinkedListDemo  singleLinkedList=new  SingleLinkedListDemo();
	  singleLinkedList.addByOder(hero1);
	  singleLinkedList.addByOder(hero2);
	  singleLinkedList.addByOder(hero3);
	  singleLinkedList.addByOder(hero4);
	  singleLinkedList.list();
	  //修改节点信息
	  HeroNode newHeroNode = new HeroNode(1, "小宋", "老阴比");
	  singleLinkedList.update(newHeroNode);
	  System.out.println("修改后的链表:"); 
	  singleLinkedList.list();
	 
  }
}
//定义SingleLinkedListDemo单链表，管理英雄
class SingleLinkedListDemo{
	//初始化一个头节点,头节点不能动,不存放具体数据
	private HeroNode head=new HeroNode(0,"","");
	//添加节点到链表
    //分析：不考虑顺序时找到当前链表的最后节点,将最后这个节点得next指向新的节点
	public void add(HeroNode heroNode) {
	//由于head节点不能动,故需要定义一个辅助指针temp
	HeroNode temp=head;
	//遍历链表,找到最后的节点
	while(true) {
	//当temp.next==null	时已经到了节点的最后
	 if(temp.next==null) {
		 break;
	 }
	 //若没有找到最后得节点temp就后移
	 temp=temp.next;
	}
	//当退出while循环时temp就指向了链表的最后 此时将链表连接即可
	temp.next=heroNode;
 }
	//第二种方式添加方式,根据英雄排名将英雄插到指定位置
	public void addByOder(HeroNode heroNode) {
	//因头节点不能动,故通过辅助指针temp找到添加的位置	
    //链表是单链表的原因,所以找的temp是位于添加位置的前一个节点,否则无法加入数据
		HeroNode temp=head;
		//flag标志添加的编号是否存在,默认是false
		boolean flag=false;
		while(true) {
			//说明temp已在链表的最后
			if(temp.next==null) {
				break;//此时无论如何都要break
			}
			//说明位置已经找到,即temp的后面插入即可
			if(temp.next.no>heroNode.no) {
				break;
			}else if(temp.next.no==heroNode.no) {
			       //说明希望添加的编号已经存在
			        flag=true;//说明编号存在
			        break;
			}
			//以上条件均不成立的时候temp继续向后移,遍历当链表
			temp=temp.next;
		}
		//判断flag的值
		if(flag) {
			System.out.printf("计划插入的英雄的编号已经存在,不能加入了！\n",heroNode.no);
		}else {
			//插入到链表中 temp后面
			heroNode.next=temp.next;
			temp.next=heroNode;
		}
	}
	//修改节点的信息
		//说明：根据newHeroNode的编号修改名称和昵称但是编号不能修改
		public void update(HeroNode newHeroNode) {
			//判断链表是否为空
			if(head.next==null) {
				 System.out.println("链表为空！");
				 return;
			 }
			//找到需要修改的节点,定义一个辅助指针temp
			HeroNode temp=head.next;
			//代表是否找到该节点
			boolean flag=false;
			while(true) {
				if(temp==null) {
					//已经遍历链表结束到了链表的最后
					break;
				}
				if(temp.no==newHeroNode.no) {
					//表示找到
					flag=true;
					break;
				}
				temp=temp.next;
			}
			//根据flag判断是否找到需要修改的节点
			if(flag) { 
				temp.name = newHeroNode.name; 
			    temp.nickname = newHeroNode.nickname;
			} else { 
				//没有找到
				System.out.printf("没有找到 编号 %d 的节点，不能修改\n", newHeroNode.no);
			}
			
		}
	//显示链表(遍历) 通过辅助指针进行遍历
	public void list() {
    //判断链表是否为空
	 if(head.next==null) {
		 System.out.println("链表为空！");
		 return;
	 }
	 //因为头节点不能动,定义一个辅助指针进行遍历
	 HeroNode temp=head.next;
	 while(true) {
		 //判断是否到链表的最后
		 if(temp==null) {
			 break;
		 }
		 //输出节点信息
		 System.out.println(temp);
		 //将temp后移(不后移会造成死循环)
		 temp=temp.next;
	  }
	}
}
//定义一个HeroNode,每个HeroNode对象就是一个节点
class HeroNode{
	public int no;//编号
	public String name;//名字
	public String nickname;//昵称
	public HeroNode next;//指向下一个节点
	//构造器
	public HeroNode(int no, String name, String nickname) {
		this.no = no;
		this.name = name;
		this.nickname = nickname;
	}
	//显示方便,重写toString
	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + ", nickname=" + nickname + "]";
	}
}
```

### 6. 单链表节点的删除

删除节点图示如下图：

![image-20200222175614557](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200222175614557.png)

```java
被删除的节点将不会有其它指向了,将会被JAVA的垃圾回收机制回收.
删除节点代码实例：(//将代码写在修改节点信息代码的后面即可//)
=====================================================================================================
//删除节点信息：
		//定义一个temp找到待删除节点的前一个节点,将temp.next和待删除节点.no 作比较
		public void del(int no) {
			//辅助节点
			HeroNode temp=head;
			//是否找到待删除节点的前一个节点
			boolean flag=false;
			while(true) {
				if(temp.next==null) {
					//已经遍历链表结束到了链表的最后
					break;
				}
				if(temp.next.no==no) {
					//说明找到待删除节点的前一个节点temp
					flag=true;
					break;
				}
				//temp向后移,实现遍历
				temp=temp.next;
			}
			//判断flag
			if(flag) {//找到
				temp.next=temp.next.next;
			}else{
				System.out.printf("没有找到待删除的节点!%d\n",no);
			}
		}
=====================================================================================================
```

### 7. 单链表的面试题-(腾讯，百度，新浪)

```java
1. 求单链表中有效节点的个数
2. 查找单链表中的倒数第k个节点
3. 单链表的反转
4. 从头到尾打印单链表->[要求方式1:反向遍历 要求方式2: Stack栈]
5. 合并两个有序的链表,合并之后的链表依然有序
```

```java
1. 求单链表中有效节点的个数
   代码实现：
  /*
 * 此写法没有限制顺序添加
 */
public class SingleLinkedList {
  public static void  main(String[] args) {
	  //测试
	  //1.创建节点
	  HeroNode hero1=new HeroNode(1,"宋江","及时雨");
	  HeroNode hero2=new HeroNode(2,"卢俊义","玉麒麟");
	  HeroNode hero3=new HeroNode(3,"吴用","智多星");
	  HeroNode hero4=new HeroNode(4,"林冲","豹子头");
	 //按照编号顺序插入
	  SingleLinkedListDemo  singleLinkedList=new  SingleLinkedListDemo();
	  singleLinkedList.addByOder(hero1);
	  singleLinkedList.addByOder(hero2);
	  singleLinkedList.addByOder(hero3);
	  singleLinkedList.addByOder(hero4);
	  System.out.println("链表信息");
	  singleLinkedList.list();
	  singleLinkedList.del(1);
	  //测试单链表有效节点的个数代码
	  System.out.println(getLength(singleLinkedList.getHead()));//3
	 
  }
  //定义方法：获取单链表的节点个数,若单链表带有头节点则不统计头节点
  public static int getLength(HeroNode head) {
	  if(head.next==null) {//空链表
		  return 0;
	  }
	  //定义length存储个数
	  int length=0;
	  HeroNode cur=head.next;//定义辅助指针
	  while(cur!=null) {
		  length++;
		  cur=cur.next;//遍历
	  }
	  return length;
  }
 }  
 
class SingleLinkedListDemo{
	//初始化一个头节点,头节点不能动,不存放具体数据
	private HeroNode head=new HeroNode(0,"","");
	//返回头节点
	public HeroNode getHead() {
		return head;
	}
=====================================================================================================
    
2. 查找单链表中的倒数第k个节点
   思路分析：
         (1)定义方法接收head节点同时接受index节点
         (2)index表示倒数第index个节点
         (3)先把链表遍历一遍,得到链表长度length
         (4)然后从链表的第一个开始遍历(length-index)个即可
         (5)找到了就返回该节点找不到返回null
  
   代码实现：
   public class SingleLinkedList {
  public static void  main(String[] args) {
	  //测试
	  //1.创建节点
	  HeroNode hero1=new HeroNode(1,"宋江","及时雨");
	  HeroNode hero2=new HeroNode(2,"卢俊义","玉麒麟");
	  HeroNode hero3=new HeroNode(3,"吴用","智多星");
	  HeroNode hero4=new HeroNode(4,"林冲","豹子头");
	 //按照编号顺序插入
	  SingleLinkedListDemo  singleLinkedList=new  SingleLinkedListDemo();
	  singleLinkedList.addByOder(hero1);
	  singleLinkedList.addByOder(hero2);
	  singleLinkedList.addByOder(hero3);
	  singleLinkedList.addByOder(hero4);
	  System.out.println("链表信息");
	  singleLinkedList.list();
	  singleLinkedList.del(1);
	  //测试单链表有效节点的个数代码
	  System.out.println(getLength(singleLinkedList.getHead()));//3
	  //测试查找单链表中的倒数第k个节点
	  HeroNode result= findLastIndexdNode(singleLinkedList.getHead(),1);
	  System.out.println("result:"+result);
      运行结果：
         // HeroNode hero4=new HeroNode(4,"林冲","豹子头");//
	 
  }
  public static HeroNode findLastIndexdNode(HeroNode head, int index) {
	  //判断链表是否为空
	  if(head.next==null) {
		  return null;
	  }
	  //遍历得到链表的长度即节点个数
	  int size=getLength(head);
	  //遍历到size-index 就是倒数第k个节点
	  //做index校验
	  if(index<=0||index>size) {
		  return null;
	  }
	  //定义辅助指针temp先指向第一个有效节点
	  HeroNode temp=head.next;
	  //for循环定位到倒数的index
	  for(int i=0;i<size-index;i++) {
	  //假设有效节点为3个找倒数第一个节点只需temp在第一个有效节点往后移2步
	  //3-1=2当i=0时在第一个节点i<2 temp后移一位 i++此时temp到了第二个有效节点
	  //此时这个节点的下一个节点就是要找的倒数节点了只需让temp指向它即可,表达式temp=temp.next
		  temp=temp.next;
	  }
	  return temp;
  }
=====================================================================================================  
```

### 8.单链表的反转(***有难度***)

![image-20200222214305244](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200222214305244.png)

![image-20200222222019080](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200222222019080.png)

```java
代码实现：
public class SingleLinkedList {
  public static void  main(String[] args) {
	  //测试
	  //1.创建节点
	  HeroNode hero1=new HeroNode(1,"宋江","及时雨");
	  HeroNode hero2=new HeroNode(2,"卢俊义","玉麒麟");
	  HeroNode hero3=new HeroNode(3,"吴用","智多星");
	  HeroNode hero4=new HeroNode(4,"林冲","豹子头");
	 //按照编号顺序插入
	  SingleLinkedListDemo  singleLinkedList=new  SingleLinkedListDemo();
	  singleLinkedList.addByOder(hero1);
	  singleLinkedList.addByOder(hero2);
	  singleLinkedList.addByOder(hero3);
	  singleLinkedList.addByOder(hero4);
	 //测试反转
	  System.out.println("没有反转之前:");
	  singleLinkedList.list();
	 //反转
	  reverseList( singleLinkedList.getHead());
	  System.out.println("反转之后的新链表:");
	  singleLinkedList.list();
	  
	  //singleLinkedList.list();
	  //singleLinkedList.del(1);
	  //测试单链表有效节点的个数代码
	  //System.out.println(getLength(singleLinkedList.getHead()));//3
	  //测试查找单链表中的倒数第k个节点
	  // HeroNode result= findLastIndexdNode(singleLinkedList.getHead(),1);
	  //System.out.println("result:"+result);
	 
  }
  //定义方法将链表反转
  public static void reverseList(HeroNode head) {//给我一个节点
	  //进行判断：若当前链表为空或仅有一个节点，则没有必要进行反转，直接返回即可
	  //当前链表为空head.next==null 仅有一个节点head.next.next==null
	  if(head.next==null||head.next.next==null) {
		  return ;
	  }
	  //定义辅助指针temp3用来遍历链表
	  HeroNode temp3=head.next;
	  //定义一个变量  tnext 用来指向temp3指向节点的下一个节点，是保护数据完整性的作用
	  //如果不设这个变量会造成链表断裂因为此链表是单向链表
	  HeroNode tnext=null;
	  //设立新链表的头部节点
	  HeroNode reverseHead=new  HeroNode(0,"","");
	  //难点出现：遍历原始链表的每一个节点，将其数据取出，然后将取出的数据放在新链表的最前端即新链表的第一个节         点
	  while(temp3!=null) {
		  //将当前得下一个节点保存起来，原因上面注释部分有
		  tnext=temp3.next;
		  //将temp3的下一个节点指向新链表的最前端
		  temp3.next=reverseHead.next;
		  //将temp3连接到新链表
		  reverseHead.next=temp3;
		  //temp3向后移继续取元素
		  temp3=tnext;
	  }
	  //将原始链表得head.next即第一个节点指向新链表得第一个有效节点(reverseHead.next)即可
	  head.next=reverseHead.next;
  } 
```

### 9. 逆序打印单链表

```java
[要求方式1:反向遍历 要求方式2: Stack栈]
```

![image-20200223152304490](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200223152304490.png)

```java
代码演示：
    mport java.util.Stack;
/*
 * 此写法没有限制顺序添加
 */
public class SingleLinkedList {
  public static void  main(String[] args) {
	  //测试
	  //1.创建节点
	  HeroNode hero1=new HeroNode(1,"马云","马爸爸");
	  HeroNode hero2=new HeroNode(2,"马化腾","吸血鬼");
	  HeroNode hero3=new HeroNode(3,"丁磊","丁三石");
	  HeroNode hero4=new HeroNode(4,"刘强东","不知妻美");
	 //按照编号顺序插入
	  SingleLinkedListDemo  singleLinkedList=new  SingleLinkedListDemo();
	  singleLinkedList.addByOder(hero1);
	  singleLinkedList.addByOder(hero2);
	  singleLinkedList.addByOder(hero3);
	  singleLinkedList.addByOder(hero4);
	  System.out.println("原始链表:");
	  singleLinkedList.list();
	  //测试逆序打印链表
	  System.out.println("逆序输出后的链表:");
	  reversPrint(singleLinkedList.getHead());
	 
  }
  //利用栈逆序打印链表
  public static void reversPrint(HeroNode head) {
	  //空链表无法打印
	  if(head.next==null) {
		  return;
	  }
	  //创建栈,将节点压入栈中
	  Stack<HeroNode> stack=new Stack<HeroNode>();
	  HeroNode temp3=head.next;
	  //将节点压入栈
	  while(temp3!=null) {
		  stack.push(temp3);//压进
		  //temp3后移继续压下一个节点
		  temp3=temp3.next;
	  }
	  //将栈中的节点进行打印
	  while(stack.size()>0) {
		  System.out.println(stack.pop());
	  }
  }
```

### 10. 单项链表知识点完整代码

```java
package com.bytedance.linkedlist;
import java.util.Stack;
/*
 * 此写法没有限制顺序添加
 */
public class SingleLinkedList {
  public static void  main(String[] args) {
	  //测试
	  //1.创建节点
	  HeroNode hero1=new HeroNode(1,"马云","马爸爸");
	  HeroNode hero2=new HeroNode(2,"马化腾","吸血鬼");
	  HeroNode hero3=new HeroNode(3,"丁磊","丁三石");
	  HeroNode hero4=new HeroNode(4,"刘强东","不知妻美");
	 //按照编号顺序插入
	  SingleLinkedListDemo  singleLinkedList=new  SingleLinkedListDemo();
	  singleLinkedList.addByOder(hero1);
	  singleLinkedList.addByOder(hero2);
	  singleLinkedList.addByOder(hero3);
	  singleLinkedList.addByOder(hero4);
	 //测试反转
	  System.out.println("原始链表:");
	  singleLinkedList.list();
	  //逆序打印测试
	  System.out.println("逆序输出后的链表:");
	  reversPrint(singleLinkedList.getHead());
	  //反转
	  //reverseList( singleLinkedList.getHead());
	  //System.out.println("反转之后的新链表:");
	  //singleLinkedList.list();
	  
	  //singleLinkedList.list();
	  //singleLinkedList.del(1);
	  //测试单链表有效节点的个数代码
	  //System.out.println(getLength(singleLinkedList.getHead()));//3
	  //测试查找单链表中的倒数第k个节点
	  // HeroNode result= findLastIndexdNode(singleLinkedList.getHead(),1);
	  //System.out.println("result:"+result);
  }
  //利用栈逆序打印链表
  public static void reversPrint(HeroNode head) {
	  //空链表无法打印
	  if(head.next==null) {
		  return;
	  }
	  //创建栈,将节点压入栈中
	  Stack<HeroNode> stack=new Stack<HeroNode>();
	  HeroNode temp3=head.next;
	  //将节点压入栈
	  while(temp3!=null) {
		  stack.push(temp3);//压进
		  //temp3后移继续压下一个节点
		  temp3=temp3.next;
	  }
	  //将栈中的节点进行打印
	  while(stack.size()>0) {
		  System.out.println(stack.pop());
	  }
  }
  //定义方法将链表反转
  public static void reverseList(HeroNode head) {//给我一个节点
	  //进行判断：若当前链表为空或仅有一个节点，则没有必要进行反转，直接返回即可
	  //当前链表为空head.next==null 仅有一个节点head.next.next==null
	  if(head.next==null||head.next.next==null) {
		  return ;
	  }
	  //定义辅助指针temp3用来遍历链表
	  HeroNode temp3=head.next;
	  //定义一个变量  tnext 用来指向temp3指向节点的下一个节点，是保护数据完整性的作用
	  //如果不设这个变量会造成链表断裂因为此链表是单向链表
	  HeroNode tnext=null;
	  //设立新链表的头部节点
	  HeroNode reverseHead=new  HeroNode(0,"","");
	  //难点出现：遍历原始链表的每一个节点，将其数据取出，然后将取出的数据放在新链表的最前端即新链表的第一个节点
	  while(temp3!=null) {
		  //将当前得下一个节点保存起来，原因上面注释部分有
		  tnext=temp3.next;
		  //将temp3的下一个节点指向新链表的最前端
		  temp3.next=reverseHead.next;
		  //将temp3连接到新链表
		  reverseHead.next=temp3;
		  //temp3向后移继续取元素
		  temp3=tnext;
	  }
	  //将原始链表得head.next即第一个节点指向新链表得第一个有效节点(reverseHead.next)即可
	  head.next=reverseHead.next;
  }
  
  //查找单链表中的倒数第k个节点
  /*
   * 定义方法接收head节点同时接受index节点
   * index表示倒数第index个节点
   * 先把链表遍历一遍,得到链表长度length
   * 然后从链表的第一个开始遍历(length-index)个即可
   * 找到了就返回该节点找不到返回null
   */
  public static HeroNode findLastIndexdNode(HeroNode head, int index) {
	  //判断链表是否为空
	  if(head.next==null) {
		  return null;
	  }
	  //遍历得到链表的长度即节点个数
	  int size=getLength(head);
	  //遍历到size-index 就是倒数第k个节点
	  //做index校验
	  if(index<=0||index>size) {
		  return null;
	  }
	  //定义辅助指针temp先指向第一个有效节点
	  HeroNode temp=head.next;
	  //for循环定位到倒数的index
	  for(int i=0;i<size-index;i++) {
	  //假设有效节点为3个找倒数第一个节点只需temp在第一个有效节点往后移2步
	  //3-1=2当i=0时在第一个节点i<2 temp后移一位 i++此时temp到了第二个有效节点
	  //此时这个节点的下一个节点就是要找的倒数节点了只需让temp指向它即可,表达式temp=temp.next
		  temp=temp.next;
	  }
	  return temp;
  }
  //定义方法：获取单链表的节点个数,若单链表带有头节点则不统计头节点
  public static int getLength(HeroNode head) {
	  if(head.next==null) {//空链表
		  return 0;
	  }
	  //定义length存储个数
	  int length=0;
	  HeroNode cur=head.next;//定义辅助指针
	  while(cur!=null) {
		  length++;
		  cur=cur.next;//遍历
	  }
	  return length;
  }
  
}
//定义SingleLinkedListDemo单链表，管理英雄
class SingleLinkedListDemo{
	//初始化一个头节点,头节点不能动,不存放具体数据
	private HeroNode head=new HeroNode(0,"","");
	//返回头节点
	public HeroNode getHead() {
		return head;
	}
	//添加节点到链表
    //分析：不考虑顺序时找到当前链表的最后节点,将最后这个节点得next指向新的节点
	public void add(HeroNode heroNode) {
	//由于head节点不能动,故需要定义一个辅助指针temp
	HeroNode temp=head;
	//遍历链表,找到最后的节点
	while(true) {
	//当temp.next==null	时已经到了节点的最后
	 if(temp.next==null) {
		 break;
	 }
	 //若没有找到最后得节点temp就后移
	 temp=temp.next;
	}
	//当退出while循环时temp就指向了链表的最后 此时将链表连接即可
	temp.next=heroNode;
 }
	//第二种方式添加方式,根据英雄排名将英雄插到指定位置
	public void addByOder(HeroNode heroNode) {
	//因头节点不能动,故通过辅助指针temp找到添加的位置	
    //链表是单链表的原因,所以找的temp是位于添加位置的前一个节点,否则无法加入数据
		HeroNode temp=head;
		//flag标志添加的编号是否存在,默认是false
		boolean flag=false;
		while(true) {
			//说明temp已在链表的最后
			if(temp.next==null) {
				break;//此时无论如何都要break
			}
			//说明位置已经找到,即temp的后面插入即可
			if(temp.next.no>heroNode.no) {
				break;
			}else if(temp.next.no==heroNode.no) {
			       //说明希望添加的编号已经存在
			        flag=true;//说明编号存在
			        break;
			}
			//以上条件均不成立的时候temp继续向后移,遍历当链表
			temp=temp.next;
		}
		//判断flag的值
		if(flag) {
			System.out.printf("计划插入的英雄的编号已经存在,不能加入了！\n",heroNode.no);
		}else {
			//插入到链表中 temp后面
			heroNode.next=temp.next;
			temp.next=heroNode;
		}
	}
	//修改节点的信息
		//说明：根据newHeroNode的编号修改名称和昵称但是编号不能修改
		public void update(HeroNode newHeroNode) {
			//判断链表是否为空
			if(head.next==null) {
				 System.out.println("链表为空！");
				 return;
			 }
			//找到需要修改的节点,定义一个辅助指针temp
			HeroNode temp=head.next;
			//代表是否找到该节点
			boolean flag=false;
			while(true) {
				if(temp==null) {
					//已经遍历链表结束到了链表的最后
					break;
				}
				if(temp.no==newHeroNode.no) {
					//表示找到
					flag=true;
					break;
				}
				temp=temp.next;
			}
			//根据flag判断是否找到需要修改的节点
			if(flag) { 
				temp.name = newHeroNode.name; 
			    temp.nickname = newHeroNode.nickname;
			} else { 
				//没有找到
				System.out.printf("没有找到 编号 %d 的节点，不能修改\n", newHeroNode.no);
			}
			
		}
		//删除节点信息：
		//定义一个temp找到待删除节点的前一个节点,将temp.next和待删除节点.no 作比较
		public void del(int no) {
			//辅助节点
			HeroNode temp=head;
			//是否找到待删除节点的前一个节点
			boolean flag=false;
			while(true) {
				if(temp.next==null) {
					//已经遍历链表结束到了链表的最后
					break;
				}
				if(temp.next.no==no) {
					//说明找到待删除节点的前一个节点temp
					flag=true;
					break;
				}
				//temp向后移,实现遍历
				temp=temp.next;
			}
			//判断flag
			if(flag) {//找到
				temp.next=temp.next.next;
			}else{
				System.out.printf("没有找到待删除的节点!%d\n",no);
			}
		}
		
	//显示链表(遍历) 通过辅助指针进行遍历
	public void list() {
    //判断链表是否为空
	 if(head.next==null) {
		 System.out.println("链表为空！");
		 return;
	 }
	 //因为头节点不能动,定义一个辅助指针进行遍历
	 HeroNode temp=head.next;
	 while(true) {
		 //判断是否到链表的最后
		 if(temp==null) {
			 break;
		 }
		 //输出节点信息
		 System.out.println(temp);
		 //将temp后移(不后移会造成死循环)
		 temp=temp.next;
	  }
	}
}
//定义一个HeroNode,每个HeroNode对象就是一个节点
class HeroNode{
	public int no;//编号
	public String name;//名字
	public String nickname;//昵称
	public HeroNode next;//指向下一个节点
	//构造器
	public HeroNode(int no, String name, String nickname) {
		this.no = no;
		this.name = name;
		this.nickname = nickname;
	}
	//显示方便,重写toString
	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + ", nickname=" + nickname + "]";
	}
}
```

## 8. 双向链表

```java
使用带 head 头的双向链表实现 –人物排行榜
管理单向链表的缺点分析: 
    (1) 单向链表，查找的方向只能是一个方向，而双向链表可以向前或者向后查找。 
    (2) 单向链表不能自我删除，需要靠辅助节点 ，而双向链表，则可以自我删除，所以前面我们单链表删除
        时节点，总是找到 temp,temp 是待删除节点的前一个节点(**细节**). 
    (3) 分析双向链表如何完成遍历，添加，修改和删除的思路
 
双向链表的删除和添加节点(此图添加节点是按照加到链表的最后)如下图,修改节点信息和单链表一样:
```

![image-20200223165649997](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200223165649997.png)

```java
代码实例：
package com.bytedance.linkedlist;
public class DoubleLinkedListDome {
public static void main(String[] args) {
		//创建人物信息
		HeroNode2 hero1=new HeroNode2(1,"乔丹","篮球之神");
		HeroNode2 hero2=new HeroNode2(2,"科比","黑曼巴");
		HeroNode2 hero3=new HeroNode2(3,"奥尼尔","大鲨鱼");
		HeroNode2 hero4=new HeroNode2(4,"罗斯","风城玫瑰");
		//创建双向链表
		DoublLinkedList doublLinkedList=new DoublLinkedList();
		//链表加入信息
		doublLinkedList.add(hero1);
		doublLinkedList.add(hero2);
		doublLinkedList.add(hero3);
		doublLinkedList.add(hero4);
		System.out.println("双向链表信息：");
		doublLinkedList.list();
		//修改节点信息
		HeroNode2 newheroNode=new HeroNode2(1,"乔丹","飞人");
		doublLinkedList.update(newheroNode);
		System.out.println("修改信息后的双向链表：");
		doublLinkedList.list();
		//删除节点
		doublLinkedList.del(4);
		System.out.println("删除信息后的双向链表：");
		doublLinkedList.list();
	}
}
//创建双向链表
class DoublLinkedList{
	//初始化一个头节点,头节点不能动,不存放具体数据
	private HeroNode2 head=new HeroNode2(0,"","");
	//返回头节点
	public HeroNode2 getHead() {
			return head;
	}
	   //遍历双向链表的方法
	   //显示链表(遍历) 通过辅助指针进行遍历
		public void list() {
	    //判断链表是否为空
		 if(head.next==null) {
			 System.out.println("链表为空！");
			 return;
		 }
		 //因为头节点不能动,定义一个辅助指针进行遍历
		 HeroNode2 temp=head.next;
		 while(true) {
			 //判断是否到链表的最后
			 if(temp==null) {
				 break;
			 }
			 //输出节点信息
			 System.out.println(temp);
			 //将temp后移(不后移会造成死循环)
			 temp=temp.next;
		  }
		}
		public void add(HeroNode2 heroNode) {
			//由于head节点不能动,故需要定义一个辅助指针temp
			HeroNode2 temp=head;
			//遍历链表,找到最后的节点
			while(true) {
			//当temp.next==null	时已经到了节点的最后
			 if(temp.next==null) {
				 break;
			 }
			 //若没有找到最后得节点temp就后移
			 temp=temp.next;
			}
			//当退出while循环时temp就指向了链表的最后 此时将链表连接即可
			//完成双向链表
			temp.next=heroNode;
			heroNode.pre=temp;
		 }
		//修改双向链表
		//修改节点的信息
		//说明：根据newHeroNode的编号修改名称和昵称但是编号不能修改
	    public void update(HeroNode2 newHeroNode) {
		 //判断链表是否为空
		 if(head.next==null) {
			 System.out.println("链表为空！");
			 return;
		}
		//找到需要修改的节点,定义一个辅助指针temp
		HeroNode2 temp=head.next;
	   //代表是否找到该节点
		boolean flag=false;
		while(true) {
			if(temp==null) {
			//已经遍历链表结束到了链表的最后
			break;
			}
			if(temp.no==newHeroNode.no) {
			  //表示找到
				flag=true;
				break;
			 }
			temp=temp.next;
			}
			//根据flag判断是否找到需要修改的节点
			if(flag) {
				temp.name = newHeroNode.name; 
				 temp.nickname = newHeroNode.nickname;
			 } else { 
					//没有找到
					System.out.printf("没有找到 编号 %d 的节点，不能修改\n", newHeroNode.no);
					}
		}
	  //双向链表删除节点的方法：
	  //直接找到待删除节点，进行自己删除
	  		public void del(int no) {
	  			//判断当前链表是否为空
	  			if(head.next==null) {
	  				System.out.println("当前链表为空,无法删除！");
	  				return;
	  			}
	  			//辅助节点
	  			HeroNode2 temp=head.next;
	  			//是否找到待删除节点的前一个节点
	  			boolean flag=false;
	  			while(true) {
	  				if(temp==null) {
	  					//已经遍历链表结束到了链表的最后
	  					break;
	  				}
	  				if(temp.no==no) {
	  					//说明找到待删除节点的前一个节点temp
	  					flag=true;
	  					break;
	  				}
	  				//temp向后移,实现遍历
	  				temp=temp.next;
	  			}
	  			//判断flag
	  			if(flag) {//找到,进行删除
	  				temp.pre.next=temp.next;
	  				//如果删除的是最后一个节点下面的代码就不用执行，因为最后一个节点已经是链表最后了
	  				//后面是空的,一旦执行下面的代码的话会出现空指针异常错误
	  				if(temp.next!=null) {
	  				temp.next.pre=temp.pre;
	  				}
	  			}else{
	  				System.out.printf("没有找到待删除的节点!%d\n",no);
	  			}
	  		}
	}
//定义节点HeroNode2
class HeroNode2{
	public int no;//编号
	public String name;//名字
	public String nickname;//昵称
	public HeroNode2 next;//指向下一个节点
	public HeroNode2 pre;//指向前一个节点
	//构造器
	public HeroNode2(int no, String name, String nickname) {
		this.no = no;
		this.name = name;
		this.nickname = nickname;
	}
	//显示方便,重写toString
	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + ", nickname=" + nickname + "]";
	}	
}
```

## 9. 单向环形链表与约瑟夫问题

```java
约瑟夫问题：
    Josephu(约瑟夫、约瑟夫环) 问题 Josephu 问题为：设编号为 1，2，… n 的 n 个人围坐一圈，约定编号为 k（1<=k<=n）的人从 1 开始报数,数到m 的那个人出列,它的下一位又从 1 开始报数,数到m的那个人又出列,依次类推,直到所有人出列为止,由 此产生一个出队编号的序列。
提示: 用一个不带头结点的循环链表来处理 Josephu 问题: 先构成一个有 n 个结点的单循环链表,然后由 k 结点起从 1 开始计数,计到m时,对应结点从链表中删除,然后再从被删除结点的下一个结点又从 1 开始计数,直到最后一个结点从链表中删除算法结束.
```

![image-20200223193046788](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200223193046788.png)

```java
创建单向环形链表与遍历示意图 如下:
```

![image-20200223200302992](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200223200302992.png)

```java
单项环形链表创建与遍历代码实现:

package com.bytedance.linkedlist;
public class Josepfu {
   public static void main(String[] args) {
	//测试构建和遍历	
	  CircleSingleLinkedList  circleSingleLinkedList=new CircleSingleLinkedList();
	  circleSingleLinkedList.addBoy(5);//创建5个节点
	  circleSingleLinkedList.showBoy();
   }
}
//创建环形单向链表
class CircleSingleLinkedList{
	//创建一个frist节点,这是第一个开始节点
	private Boy first=null;
	//添加子节点，构成环形链表
	public void addBoy(int numbers) {
	//int numbers 传入要创建节点的个数,进行数字合法校验
	if(numbers<1) {	
		System.out.println("numbers输入不合法！");
	    return;
	}
	//定义辅助指针，起到帮助创建环形链表的作用
	Boy temp3=null;
	//for循环创建环形链表
	for(int i=1;i<=numbers;i++) {
	//根据编号创建子节点
		Boy boy=new Boy(i);
	//若是第一个节点
        if(i==1) {
        	first=boy;
        	//构成环
        	first.setNext(first);
        	//使temp3指向第一个节点,因first不能动
        	temp3=first;
        }else {
        	temp3.setNext(boy);
        	boy.setNext(first);
        	temp3=boy;
        }
	 }
	}
	//遍历当前环形链表
	public void showBoy(){
		//判断链表是否为空
		if(first==null) {
			System.out.println("链表为空！");
			return;
		}
		//由于first无法动,定义辅助指针temp帮助遍历
		Boy temp=first;
		while(true) {
			System.out.printf("节点编号%d\n",temp.getNo());
			//已经到链表最后,遍历结束
			if(temp.getNext()==first) {
				break;
			}
			//没结束指针则后移
			temp=temp.getNext();
		}
	}
}
//创建一个Boy类表示一个节点
class Boy{
	private int no;//编号
	private Boy next;//指向下一个节点,默认为null
	public Boy(int no) {
		this.no = no;
	}
	public int getNo() {
		return no;
	}
	public void setNo(int no) {
		this.no = no;
	}
	public Boy getNext() {
		return next;
	}
	public void setNext(Boy next) {
		this.next = next;
	}	
}
```

### 1. 约瑟夫出圈队列问题

![image-20200224121913108](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200224121913108.png)

```java
代码实现:
// first.next=first temp.next=first中的temp.next=first指的是temp的下一个域指向新的first而不是temp自己移动
package com.bytedance.linkedlist;
public class Josepfu {
   public static void main(String[] args) {
	  //测试构建和遍历	
	  CircleSingleLinkedList  circleSingleLinkedList=new CircleSingleLinkedList();
	  circleSingleLinkedList.addBoy(5);//创建5个节点
	  circleSingleLinkedList.showBoy();
	  //测试节点出圈
	  System.out.println("节点出圈顺序:");
	  circleSingleLinkedList.countBoy(1, 2, 5);
   }
}
//创建环形单向链表
class CircleSingleLinkedList{
	//创建一个frist节点,这是第一个开始节点
	private Boy first=null;
	//添加子节点，构成环形链表
	public void addBoy(int numbers) {
	//int numbers 传入要创建节点的个数,进行数字合法校验
	if(numbers<1) {	
		System.out.println("numbers输入不合法！");
	    return;
	}
	//定义辅助指针，起到帮助创建环形链表的作用
	Boy temp3=null;
	//for循环创建环形链表
	for(int i=1;i<=numbers;i++) {
	//根据编号创建子节点
		Boy boy=new Boy(i);
	//若是第一个节点
        if(i==1) {
        	first=boy;
        	//构成环
        	first.setNext(first);
        	//使temp3指向第一个节点,因first不能动
        	temp3=first;
        }else {
        	temp3.setNext(boy);
        	boy.setNext(first);
        	temp3=boy;
        }
	 }
	}
	//遍历当前环形链表
	public void showBoy(){
		//判断链表是否为空
		if(first==null) {
			System.out.println("链表为空！");
			return;
		}
		//由于first无法动,定义辅助指针temp帮助遍历
		Boy temp=first;
		while(true) {
			System.out.printf("节点编号%d\n",temp.getNo());
			//已经到链表最后,遍历结束
			if(temp.getNext()==first) {
				break;
			}
			//没结束指针则后移
			temp=temp.getNext();
		}
	}
	//根据用户输入计算节点出圈顺序
	/*startNo从第几个节点开始
	 *countNum表示数几下
	 *nums表示最初有几个节点在环形链表中
	 */
	public void countBoy(int startNo,int countNum,int nums) {
		//做数据校验
		if(first==null||startNo<1||startNo>nums) {
			System.out.println("输入数据有误！请重新输入");
			return;
		}
		//创建辅助指针完成节点出圈
		Boy helper=first;
		//helper开始时指向环形链表的最后一个节点
		while(true) {
			if(helper.getNext()==first) {
				//表名已经指向了最后一个节点
				break;
			}
			//helper后移  
			helper=helper.getNext();
		}
		//节点报数之前first helper 移动k-1次
		for(int i=0;i<startNo-1;i++) {
			first=first.getNext();
			helper=helper.getNext();
		}
		//节点报数时 first helper同时移动 m-1次,循环直到链表仅有只一个元素为止
		while(true) {
			//此时链表中只剩一个节点
			if(helper==first) {
				break;
			}
			//使first helper同时移动 countNum-1次，出圈
			for(int j=0;j<countNum-1;j++) {
				first=first.getNext();
				helper=helper.getNext();
			}
			//此时first指向的节点就是要出列的节点
			System.out.printf("出圈节点编号为%d\n",first.getNo());
			//此时将first指向的节点出圈
			first=first.getNext();
			//helper.setNext(first);指的是helper的下一个域指向新的first而不是helper移动
			helper.setNext(first);
		}
		System.out.printf("最后圈中节点编号为%d\n",first.getNo());
	}
}
//创建一个Boy类表示一个节点
class Boy{
	private int no;//编号
	private Boy next;//指向下一个节点,默认为null
	public Boy(int no) {
		this.no = no;
	}
	public int getNo() {
		return no;
	}
	public void setNo(int no) {
		this.no = no;
	}
	public Boy getNext() {
		return next;
	}
	public void setNext(Boy next) {
		this.next = next;
	}	
}
```

## 10. 栈

```java
栈的英文为(stack)
(2) 栈是一个先入后出(FILO-First In Last Out)的有序列表。
(3) 栈(stack)是限制线性表中元素的插入和删除只能在线性表的同一端进行的一种特殊线性表。允许插入和删除的 一端，为变化的一端，称为栈顶(Top)，另一端为固定的一端，称为栈底(Bottom)。
(4) 根据栈的定义可知，最先放入栈中元素在栈底，最后放入的元素在栈顶，而删除元素刚好相反，最后放入的元 素最先删除，最先放入的元素最后删除
(5) 图解方式说明出栈(pop)和入栈(push)的概念
```

![image-20200224180709390](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200224180709390.png)

### 1. 栈的应用场景

```java
(1) 子程序的调用：在跳往子程序前,会先将下个指令的地址存到堆栈中,直到子程序执行完后再将地址取出,以回到原来的       程序中.
(2) 处理递归调用: 和子程序的调用类似,只是除了储存下一个指令的地址外,也将参数、区域变量等数据存入堆 栈中。
(3) 表达式的转换[中缀表达式转后缀表达式]与求值(实际解决). (面试经典) 
(4) 二叉树的遍历. 
(5) 图形的深度优先(depth 一 first)搜索法.
```

### 2.数组模拟栈(stack)的使用

```java
(1) 用数组模拟栈的使用,由于栈是一种有序列表,当然可以使用数组的结构来储存栈的数据内容,下面我们就用数组模拟栈的出     栈,入栈等操作。
(2) 实现思路分析:
    1. 定义变量 top 表示栈顶初始化为 -1
    2. 向栈中加入数据,top向后移 top++ ,将元素存入top指向的位置即可 stack[top]=数据;
    3. 栈中取数据,从最后一个取,此时取数据让top向前移指向下一个元素,原来的那个元素用一个变量保存即可完成数据出        栈 int value=stack[top]; top--; return;
```

```java
数组模拟栈代码实现:

package stack;
import java.util.Scanner;
public class ArrayStackDemo {

	public static void main(String[] args) {
		//测试数组模拟栈
		ArrayStack stack=new ArrayStack(4);
		String key="";
		//控制是否退出菜单
		boolean loop=true;
		Scanner src=new Scanner(System.in);
		while(loop) {
			System.out.println("show:表示显示栈");
			System.out.println("exit:退出程序");
			System.out.println("push:表示向栈加入数据");
			System.out.println("pop:表示从栈取出数据");
			System.out.println("请输入选择:");
			key=src.next();
			switch(key) {
			case "show":
				stack.list();
				break;
			case "push":
				System.out.println("请输入数据！");
				int value=src.nextInt();
				stack.push(value);
				break;
			case "pop":
                try {//取数据时可能发生异常
					int val=stack.pop();
					System.out.println("取出的数据:"+val);
				}catch(Exception e) {
					System.out.println(e.getMessage());
				}
				break;
			case "exit":
				//关闭流《防止泄露资源浪费
				src.close();
				loop=false;
				break;
			}
		}
		System.out.println("程序以退出！");
	}
}
//定义类表示栈结构
class  ArrayStack{
	//栈的大小
	private int maxSize;
	//定义数组，存放数据
	private int[] stack;
	//定义栈顶
	private int top=-1;
	//构造器
	public  ArrayStack(int maxSize) {
		this.maxSize=maxSize;
		//初始化数组
		stack=new int[maxSize];
	}
	//判断栈的情况
	public boolean isFull() {
		//此时栈满
		return top== maxSize-1;
	}
	public boolean isEmpty() {
		//此时栈空
		return top==-1;
	}
	//入栈
	public void push(int value) {
		//判断栈是否满
		if(isFull()) {
			System.out.println("栈已满,不能存放数据！");
			return;
		}
		//否则可以添加数据
		top++;
		stack[top]=value;
	}
	//出栈
	public int pop() {
		//判断是否为空
		if(isEmpty()) {
			//抛异常
			throw new RuntimeException("栈为空,没有数据!");
		}
		//从最后一个数据开取
		int value=stack[top];
		top--;
		return value;
	}
	//遍历栈,从栈顶开始遍历
	public void list() {
	//判断栈是否为空
		if(isEmpty()) {
			System.out.println("栈为空,没有数据!");
			return;
		}
		for(int i=top;i>=0;i--) {
			System.out.printf("stack[%d]=%d\n",i,stack[i]);
		}
	}
}
```

### 3. 栈实现综合计算器

```java]
例: 请输入一个表达式
    计算式: (6*5*8+3-5*6-3) 点击计算直接出结果
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\未命名文件.png)

```java
代码实现：
    
 package stack;

 public class Cakulatatot0{

	public static void main(String[] args) {
		 //完成表达式运算
		 //如 3+6*3-2=19 这个表达式
	     String espression="3+6*3-2";
	     //创建 数栈和符号栈
	     ArrayStack numStack =new ArrayStack(10);
	     ArrayStack operStack =new ArrayStack(10);
	     //定义相关变量 定义扫描索引
	     int index=0;
		 int num1=0;
		 int num2=0;
		 //存储操作符
		 int oper=0;
		 //存储结果
		 int res=0;
		 //将扫描的char保存
		 char ch= ' ';
		 //拼接多位数
		 String KeepNum="";
		 //while 循环espression表达式
		 while(true) {
	     //开始扫描表达式的每个字符
	     //从第一个开始取，每次取一个，取时是字符串利用charAt转为字符
		ch=	espression.substring(index, index+1).charAt(0);
	     //若取出的元素是数字或操作符就进行相应处理即可
		 if( operStack.isOper(ch)) {//如果是操作符
			    //判断当前符号栈是否为空
				if(!operStack.isEmpty()) {
					//符号栈有操作符，进行比较，若当前操作符优先级小于或等于栈中的操作符,就进行取数运算操作
				    if( operStack.priority(ch)<= operStack.priority( operStack.peek())){//和当前栈顶操作符比较
				    //从数栈取2个数进行运算 
					num1= numStack.pop();
					num2= numStack.pop();
					//从符号栈取操作符
					oper= operStack.pop();
					//进行运算
					res= numStack.cal(num1, num2, oper);
					//运算结果入栈
					numStack.push(res); 
					//将当前操作符加入符号栈
					operStack.push(ch);
				    } else {
				    	//符号栈为空直接入栈
					     operStack.push(ch);
				    }
				  }else {//符号栈为空直接入栈
					     operStack.push(ch);
				   }
			  }else {
				//如果是数直接入数栈，但是不能直接push(ch) 此时ch代表的是字符不是int型数字根据ASCII应该 ch-48
					numStack.push(ch-48); 
			  }
		     //使index+1,并判断是否已扫描到表达式的最后
		     index++;
		     if(index>=espression.length()) {
		    	 //扫描结束
		    	 break;
		     }
		     //表达式扫描完毕，顺序从数栈和符号栈取出数据和操作符进行运算
		     while(true) {
		    	//当符号栈为空时运算结束，数栈中只有一个数字即为结果
		    	 if(operStack.isEmpty()) {
		    		//运算结束 
		    		 break;
		    	 }
		    	 num1=numStack.pop();
				 num2=numStack.pop();
				 oper=operStack.pop();
				 res=numStack.cal(num1, num2, oper);
				 numStack.push(res);//入栈
		     }
		    System.out.printf("表达式%s=%d",espression,numStack.pop());
	}

}
//创建栈
	class ArrayStack {
	// 栈的大小
	private int maxSize;
	// 定义数组，存放数据
	private int[] stack;
	// 定义栈顶
	private int top = -1;

	// 构造器
	public ArrayStack(int maxSize) {
		this.maxSize = maxSize;
		// 初始化数组
		stack = new int[this.maxSize];
	}

	// 返回当前栈顶的值，但不进行出栈操作
	public int peek() {
		return stack[top];
	}

	// 判断栈的情况
	public boolean isFull() {
		// 此时栈满
		return top == maxSize - 1;
	}

	public boolean isEmpty() {
		// 此时栈空
		return top == -1;
	}

	// 入栈
	public void push(int value) {
		// 判断栈是否满
		if (isFull()) {
			System.out.println("栈已满,不能存放数据！");
			return;
		}
		// 否则可以添加数据
		top++;
		stack[top] = value;
	}

	// 出栈
	public int pop() {
		// 判断是否为空
		if (isEmpty()) {
			// 抛异常
			throw new RuntimeException("栈为空,没有数据!");
		}
		// 从最后一个数据开取
		int value = stack[top];
		top--;
		return value;
	}

	// 遍历栈,从栈顶开始遍历
	public void list() {
		// 判断栈是否为空
		if (isEmpty()) {
			System.out.println("栈为空,没有数据!");
			return;
		}
		for (int i = top; i >= 0; i--) {
			System.out.printf("stack[%d]=%d\n", i, stack[i]);
		}
	}

	// 返回运算符的优先级,优先级大小由我们自己设定,优先级使用数字表示
	// 数字越大优先级越高
	public int priority(int oper) {
		// 传入的操作符为* 或者 / 优先级就定位 1 自己设定的
		if (oper == '*' || oper == '/') {
			return 1;
		} else if (oper == '+' || oper == '-') {
			return 0;
		} else {
			// 如果传入的操作符不是+ - * / 中的其中一个就返回-1
			return -1;
		}
	}

	// 判断是否是操作符
	public boolean isOper(char val) {
		return val == '+' || val == '-' || val == '*' || val == '/';
	}

	// 进行运算方法
	public int cal(int num1, int num2, int oper) {
		// 存放运算的结果
		int res = 0;
		switch (oper) {
		case '+':
			res = num1 + num2;
			break;
		case '-':
			res = num2 - num1;// 后弹出的数据减去先弹出的数据
			break;
		case '*':
			res = num1 * num2;
			break;
		case '/':
			res = num2/num1;// 后弹出的数据除先弹出的数据
			break;
		default:
			break;
		}
		return res;
	}
  }
}
```

### 4. 多位数表达式情况代码实现

```java
package com.bytedance.stack;

import java.util.Scanner;

public class Calculator {

	public static void main(String[] args) {
		Scanner src=new Scanner(System.in);
		System.out.println("请输入一个表达式:");
		String expression = src.next(); 
		//创建两个栈，数栈，一个符号栈
		ArrayStack2 numStack = new ArrayStack2(10);
		ArrayStack2 operStack = new ArrayStack2(10);
		//定义需要的相关变量
		int index = 0;//用于扫描
		int num1 = 0; 
		int num2 = 0;
		int oper = 0;
		int res = 0;
		char ch = ' '; //将每次扫描得到char保存到ch
		String keepNum = ""; //用于拼接 多位数
		//开始while循环的扫描expression
		while(true) {
			//依次得到expression 的每一个字符
			ch = expression.substring(index, index+1).charAt(0);
			//判断ch是什么，然后做相应的处理
			if(operStack.isOper(ch)) {//如果是运算符
				//判断当前的符号栈是否为空
				if(!operStack.isEmpty()) {
					//如果符号栈有操作符，就进行比较,如果当前的操作符的优先级小于或者等于栈中的操作符,就需要从数栈中pop出两个数,
					//在从符号栈中pop出一个符号，进行运算，将得到结果，入数栈，然后将当前的操作符入符号栈
					if(operStack.priority(ch) <= operStack.priority(operStack.peek())) {
						num1 = numStack.pop();
						num2 = numStack.pop();
						oper = operStack.pop();
						res = numStack.cal(num1, num2, oper);
						//把运算的结果如数栈
						numStack.push(res);
						//然后将当前的操作符入符号栈
						operStack.push(ch);
					} else {
						//如果当前的操作符的优先级大于栈中的操作符， 就直接入符号栈.
						operStack.push(ch);
					}
				}else {
					//如果为空直接入符号栈..
					operStack.push(ch); // 1 + 3
				}
			} else { //如果是数，则直接入数栈
				
				//numStack.push(ch - 48); //? "1+3" '1' => 1
				//如果是数直接入数栈，但是不能直接push(ch) 此时ch代表的是字符不是int型数字根据ASCII应该 ch-48
				/*
				 *当处理的数为多位数时入60+3*。。当index扫秒时不能扫到数字就直接入栈，而是要把这个数字扫完即扫完整个
				 *60再入栈，此时需要一个变量进行字符串拼接
				 */
				    //处理多位数拼接
				
				//处理多位数
				keepNum += ch;
				
				//如果ch已经是expression的最后一位，就直接入栈
				if (index == expression.length() - 1) {
					numStack.push(Integer.parseInt(keepNum));
				}else{
				
					//判断下一个字符是不是数字，如果是数字，就继续扫描，如果是运算符，则入栈
					//注意是看后一位，不是index++
					if (operStack.isOper(expression.substring(index+1,index+2).charAt(0))) {
						//如果后一位是运算符，则入栈 keepNum = "1" 或者 "123"
						numStack.push(Integer.parseInt(keepNum));
						//重要的!!!!!!, keepNum清空
						keepNum = "";
						
					}
				}
			}
			//让index + 1, 并判断是否扫描到expression最后.
			index++;
			if (index >= expression.length()) {
				break;
			}
		}
		
		//当表达式扫描完毕，就顺序的从 数栈和符号栈中pop出相应的数和符号，并运行.
		while(true) {
			//如果符号栈为空，则计算到最后的结果, 数栈中只有一个数字【结果】
			if(operStack.isEmpty()) {
				break;
			}
			num1 = numStack.pop();
			num2 = numStack.pop();
			oper = operStack.pop();
			res = numStack.cal(num1, num2, oper);
			numStack.push(res);//入栈
		}
		//将数栈的最后数，pop出，就是结果
		int res2 = numStack.pop();
		System.out.printf("表达式 %s = %d", expression, res2);
	}

}

//先创建一个栈,直接使用前面创建好
//定义一个 ArrayStack2 表示栈, 需要扩展功能
class ArrayStack2 {
	private int maxSize; // 栈的大小
	private int[] stack; // 数组，数组模拟栈，数据就放在该数组
	private int top = -1;// top表示栈顶，初始化为-1
	
	//构造器
	public ArrayStack2(int maxSize) {
		this.maxSize = maxSize;
		stack = new int[this.maxSize];
	}
	
	//增加一个方法，可以返回当前栈顶的值, 但是不是真正的pop
	public int peek() {
		return stack[top];
	}
	
	//栈满
	public boolean isFull() {
		return top == maxSize - 1;
	}
	//栈空
	public boolean isEmpty() {
		return top == -1;
	}
	//入栈-push
	public void push(int value) {
		//先判断栈是否满
		if(isFull()) {
			System.out.println("栈满");
			return;
		}
		top++;
		stack[top] = value;
	}
	//出栈-pop, 将栈顶的数据返回
	public int pop() {
		//先判断栈是否空
		if(isEmpty()) {
			//抛出异常
			throw new RuntimeException("栈空，没有数据~");
		}
		int value = stack[top];
		top--;
		return value;
	}
	//显示栈的情况[遍历栈]， 遍历时，需要从栈顶开始显示数据
	public void list() {
		if(isEmpty()) {
			System.out.println("栈空，没有数据~~");
			return;
		}
		//需要从栈顶开始显示数据
		for(int i = top; i >= 0 ; i--) {
			System.out.printf("stack[%d]=%d\n", i, stack[i]);
		}
	}
	//返回运算符的优先级，优先级是程序员来确定, 优先级使用数字表示
	//数字越大，则优先级就越高.
	public int priority(int oper) {
		if(oper == '*' || oper == '/'){
			return 1;
		} else if (oper == '+' || oper == '-') {
			return 0;
		} else {
			return -1; // 假定目前的表达式只有 +, - , * , /
		}
	}
	//判断是不是一个运算符
	public boolean isOper(char val) {
		return val == '+' || val == '-' || val == '*' || val == '/';
	}
	//计算方法
	public int cal(int num1, int num2, int oper) {
		int res = 0; // res 用于存放计算的结果
		switch (oper) {
		case '+':
			res = num1 + num2;
			break;
		case '-':
			res = num2 - num1;// 注意顺序
			break;
		case '*':
			res = num1 * num2;
			break;
		case '/':
			res = num2 / num1;
			break;
		default:
			break;
		}
		return res;
	}
}
```

### 5. 前缀 中缀 后缀（逆波兰表达式）表达式规则

```java
前缀表达式 (波兰表达式): 前缀表达式的运算符位于操作数之前 例如: (3+4)*5-6 其前缀表达式为: - * +3456
中缀表达式: 前面写的代码就是中缀表达式
    
//前缀表达式的计算机求值:
从右至左扫描表达式，遇到数字时，将数字压入堆栈，遇到运算符时，弹出栈顶的两个 数，用运算符对它们做相应的计算(栈顶元素和次顶元素)，并将结果入栈；重复上述过程直到表达式最左端，最后运算得出的值即为 表达式的结果

例如: (3+4)×5-6 对应的前缀表达式就是 - × + 3 4 5 6 , 针对前缀表达式求值步骤如下:
(1) 从右至左扫描，将6、5、4、3压入堆栈 2) 遇到+运算符，因此弹出3和4（3为栈顶元素，4为次顶元素）,计算出3+4的    值,得7,再将7入栈
(3) 接下来是×运算符,因此弹出7和5,计算出7×5=35，将35入栈 4) 最后是-运算符,计算出35-6的值,即29,由此得出最     终结果
    
//后缀表达式 
 (1) 后缀表达式又称逆波兰表达式,与前缀表达式相似，只是运算符位于操作数之后 
 (2) 中举例说明： (3+4)×5-6 对应的后缀表达式就是 3 4 + 5 × 6 – 3) 
    
    再比如:
    正常的表达式                         逆波兰表达式 
      a+b							    a b +
      a+(b-c)							a b c - +
      a+(b-c)*d a+d*(b-c)                 a b c – d * + a d b c - * +
//后缀表达式的计算机求值
从左至右
扫描表达式，遇到数字时，将数字压入堆栈，遇到运算符时，弹出栈顶的两个数，用运 算符对它们做相应的计算(次顶元素 和栈顶元素)，并将结果入栈；重复上述过程直到表达式最右端，最后运算得出的值即为 表达式的结果
例如: (3+4)×5-6 对应的后缀表达式就是 3 4 + 5 × 6 - , 针对后缀表达式求值步骤如下:
(1) 从左至右扫描，将3和4压入堆栈； 
(2) 遇到+运算符，因此弹出4和3（4为栈顶元素，3为次顶元素），计算出3+4的值，得     7，再将7 入栈；
(3) 将5入栈；
(4) 接下来是×运算符，因此弹出5和7，计算出7×5=35，将35入栈； 5) 将6入栈； 6) 最后是-运算符，计算出35-6的值，即     29，由此得出最终结果

```

### 6. 逆波兰计算器(后缀表达式计算器)

```java
完成一个逆波兰计算器，完成如下任务:
(1) 输入一个逆波兰表达式(后缀表达式)，使用栈(Stack), 计算其结果 
(2)支持小括号和多位数整数
```

```java
代码实现：
//计算表达式: (3+6) *5-6  
//计算表达式: 4*5-8+60+8/2
package com.atguigu.stack;
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class PolandNotation {
    public static void main(String[] args) {
		String suffixExpression="3 6 + 5 * 6 -";//39
		String suffixExpression="4 5 * 8 - 60 + 8 2 / +";//76
		List<String> list = getListString(suffixExpression);
		int result = calculate(list);
		System.out.println("计算的结果是=" + result);
		
	}
	
	//将逆波兰表达式， 依次将数据和运算符 放入到 ArrayList中
	public static List<String> getListString(String suffixExpression) {
		//将 suffixExpression 分割,使用空格分隔
		String[] split = suffixExpression.split(" ");
		List<String> list = new ArrayList<String>();
		for(String ele: split) {
			list.add(ele);//将分割的每一个元素加入到集合中
		}
		return list;
		
	}
	public static int calculate(List<String> ls) {
		// 创建栈
		Stack<String> stack = new Stack<String>();
		// 遍历 ls
		for (String item : ls) {
			// 使用正则表达式来取出数
			if (item.matches("\\d+")) { // 匹配的是多位数
				// 如果是数字就直接直接入栈
				stack.push(item);
			} else {
				// 如果是字符就取出出两个数，并运算， 再入栈
				int num2 = Integer.parseInt(stack.pop());
				int num1 = Integer.parseInt(stack.pop());
				int result = 0;//存放运算结果
				if (item.equals("+")) {
					result = num1 + num2;
				} else if (item.equals("-")) {
					result = num1 - num2;
				} else if (item.equals("*")) {
					result = num1 * num2;
				} else if (item.equals("/")) {
					result = num1 / num2;
				} else {
					throw new RuntimeException("运算符有误");
				}
				//把result转换成字符串 入栈
				stack.push("" + result);
			}
		}
		//最后留在stack中的数据是运算结果，这个结果转换成数字进行返回
		return Integer.parseInt(stack.pop());
	}
}
```

### 7. 中缀转后缀表达式(逆波兰表达式)

```java
具体步骤如下: 
(1) 初始化两个栈：运算符栈 s1 和储存中间结果的栈 s2；
(2) 从左至右扫描中缀表达式；
(3) 遇到操作数时，将其压 s2； 
(4) 遇到运算符时，比较其与 s1 栈顶运算符的优先级：
1.如果 s1 为空，或栈顶运算符为左括号“(”，则直接将此运算符入栈； 
2.否则，若优先级比栈顶运算符的高，也将运算符压入 s1； 
3.否则，将 s1 栈顶的运算符弹出并压入到 s2 中，再次转到(4.1)与 s1 中新的栈顶运算符相比较；
(5) 遇到括号时： 
   (1) 如果是左括号“(”，则直接压入 s1 
   (2) 如果是右括号“)”，则依次弹出 s1 栈顶的运算符，并压入 s2，直到遇到左括号为止，此时将这一对括号丢弃 
       a. 重复步骤 2 至 5，直到表达式的最右边 
       b.将 s1 中剩余的运算符依次弹出并压入 s2 
       c.依次弹出 s2 中的元素并输出，结果的逆序即为中缀表达式对应的后缀表达式
```

```java
代码实现：
    
package com.atguigu.stack;
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;
/*
 * 完成 1+((2+3)*4-5)=16---->1 2 3 + * + 5 - 的计算
 */
public class PolandNotation {

	public static void main(String[] args) {
		//第一步：给定一个表达式
		//String expression="1+((2+3)*4)-5";
		String expression="3+((6*6)-10+60/30)*8+40";//235
		List<String> infixExpressionList=toInfixExpressionList(expression);
		System.out.println("中缀表达式对应的List:"+infixExpressionList);//[1, +, (, (, 2, +, 3, ), *, 4, ), -, 5]/
		List<String> suffixExpressionList=parseSuffixExpressionList(infixExpressionList);
		System.out.println("后缀表达式对应的List:"+suffixExpressionList);//[1, 2, 3, +, 4, *, +, 5, -]
        System.out.println("expression表达式的结果是:"+calculate(suffixExpressionList));//16
	}
	/*
	 * 第三步:将第二步得到的中缀表达式对应的list[1, +, (, (, 2, +, 3, ), *, 4, -, 5]
                   转换为逆波兰表达式对应的list[1,2,3,+,4,*,+,5,-]
	 */
	public static List<String> parseSuffixExpressionList(List<String> ls){
		//初始化1个栈,符号栈，和一个用于存放中间结果的List集合
		Stack<String> s1=new Stack<String>();
		List<String> s2=new ArrayList<String>();
		//遍历ls
		for(String item:ls) {
			//若是数字直接入s2
			if(item.matches("\\d+")){
				s2.add(item);
			}else if(item.equals("(")) {
				//若是小括号直接入s1符号栈
				s1.push(item);
			}else if(item.equals(")")) {
				//若是右括号
				while(!s1.peek().equals("(")) {//s1栈顶元素不是"("就继续循环
					//s1的内容弹出加入到s1中
					s2.add(s1.pop());
				}
				s1.pop();//将"("弹出s1栈,即消除小括号
			}else {
				//当item的运算符优先级小于等于s1栈顶运算符优先级,将 s1 栈顶的运算符弹出并压入到 s2 中，再次转到(4.1)与 s1 中新的栈顶运算符相比较
				//此时定义一个比较优先级大小的方法2.0
				while(s1.size()!=0 && Operation.getValue(s1.peek())>=Operation.getValue(item)) {
					s2.add(s1.pop());
				}
				//将当前item加入栈中
				s1.push(item);
			}
		}
		//将s1中剩余的运算符依次加入s2中
		while(s1.size()!=0) {
			s2.add(s1.pop());
		}
		//s2是list集合其中的元素是按顺序存放的，故此时对应的,list就是逆波兰表达式
		return s2;
	}
	//第二步：编写方法将中缀表达式转成相应的ArrayList
	public static List<String> toInfixExpressionList(String s){
		//定义一个list存放中缀表达式对应的内容
		List<String> ls=new ArrayList<String>();
		//定义指针遍历中缀表达式字符串
		int i=0;
		//定义对多为数的拼接变量
		String str;
		//定义ch用于每遍历到一个字符就将此字符存放到ch
		char ch;
		do {
			//如果ch不是数字，则将ch加入ls中
			if((ch=s.charAt(i))<48||(ch=s.charAt(i))>57) {
				ls.add(""+ch);
				//i后移继续遍历
				i++;
			}else {
				//若是数字，则需要考虑是否为多位数
				//此时需将str置空
				str="";
				while(i<s.length() && (ch=s.charAt(i))>=48 && (ch=s.charAt(i))<=57) {
					str+=ch;//进行拼接
					i++;
				}
				ls.add(str);
			}
		}while(i<s.length());
		return ls;
	}
	//将逆波兰表达式， 依次将数据和运算符 放入到 ArrayList中
	public static List<String> getListString(String suffixExpression) {
		//将 suffixExpression 分割,使用空格分隔
		String[] split = suffixExpression.split(" ");
		List<String> list = new ArrayList<String>();
		for(String ele: split) {
			list.add(ele);//将分割的每一个元素加入到集合中
		}
		return list;
	}
	public static int calculate(List<String> ls) {
		// 创建栈
		Stack<String> stack = new Stack<String>();
		// 遍历 ls
		for (String item : ls) {
			// 使用正则表达式来取出数
			if (item.matches("\\d+")) { // 匹配的是多位数
				// 如果是数字就直接直接入栈
				stack.push(item);
			} else {
				// 如果是字符就取出出两个数，并运算， 再入栈
				int num2 = Integer.parseInt(stack.pop());
				int num1 = Integer.parseInt(stack.pop());
				int result = 0;//存放运算结果
				if (item.equals("+")) {
					result = num1 + num2;
				} else if (item.equals("-")) {
					result = num1 - num2;
				} else if (item.equals("*")) {
					result = num1 * num2;
				} else if (item.equals("/")) {
					result = num1 / num2;
				} else {
					throw new RuntimeException("运算符有误");
				}
				//把result转换成字符串 入栈
				stack.push("" + result);
			}
		}
		//最后留在stack中的数据是运算结果，这个结果转换成数字进行返回
		return Integer.parseInt(stack.pop());
	}
}
//定义优先级大小的类，比较优先级大小的方法2.0
class Operation{
	private static int ADD=1;//加法运算符优先级
	private static int SUB=1;//减法运算符优先级
	private static int MUL=2;//乘法运算符优先级
	private static int DIV=2;//除法运算符优先级
	//比较优先级大小的方法2.0，返回对应数字即可
	public static int getValue(String operation) {//传进一个运算符
		int res=0;//存放结果
		switch(operation) {
		case "+":
			res=ADD;
			break;
		case "-":
			res=SUB;
			break;
		case "*":
			res=MUL;
			break;
		case "/":
			res=DIV;
			break;
	    default:
	    	System.out.println("不存在该运算符！");
	    	break;
		}
		return res;
	}
}
```

```java
考虑效数等表达式格式的逆波兰计算器完整代码：
    
package com.atguigu.reversepolishcal;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Stack;
import java.util.regex.Pattern;

public class ReversePolishMultiCalc {
    // 匹配 + - * / ( ) 运算符
    static final String SYMBOL = "\\+|-|\\*|/|\\(|\\)";
    static final String LEFT = "(";
    static final String RIGHT = ")";
    static final String ADD = "+";
    static final String MINUS= "-";
    static final String TIMES = "*";
    static final String DIVISION = "/";

    // 加減 + -
    static final int LEVEL_01 = 1;
    // 乘除 * /
    static final int LEVEL_02 = 2;
   // 括号
    static final int LEVEL_HIGH = Integer.MAX_VALUE;
    static Stack<String> stack = new Stack<>();
    static List<String> data = Collections.synchronizedList(new ArrayList<String>());
   //去除所有空白符
    public static String replaceAllBlank(String s ){
        // \\s+ 匹配任何空白字符，包括空格、制表符、换页符等等, 等价于[ \f\n\r\t\v]
        return s.replaceAll("\\s+","");
    }
   // 判断是不是数字 int double long float
    public static boolean isNumber(String s){
        Pattern pattern = Pattern.compile("^[-\\+]?[.\\d]*$");
        return pattern.matcher(s).matches();
    }
     // 判断是不是运算符
    public static boolean isSymbol(String s){
        return s.matches(SYMBOL);
    }
    //匹配运算等级
    public static int calcLevel(String s){
        if("+".equals(s) || "-".equals(s)){
            return LEVEL_01;
        } else if("*".equals(s) || "/".equals(s)){
            return LEVEL_02;
        }
        return LEVEL_HIGH;
    }
     //匹配 
    public static List<String> doMatch (String s) throws Exception{
        if(s == null || "".equals(s.trim())) throw new RuntimeException("data is empty");
        if(!isNumber(s.charAt(0)+"")) throw new RuntimeException("data illeagle,start not with a number");

        s = replaceAllBlank(s);

        String each;
        int start = 0;

        for (int i = 0; i < s.length(); i++) {
            if(isSymbol(s.charAt(i)+"")){
                each = s.charAt(i)+"";
        //栈为空，(操作符，或者 操作符优先级大于栈顶优先级 && 操作符优先级不是( )的优先级 及是 ) 不能直接入栈
                if(stack.isEmpty() || LEFT.equals(each)
                        || ((calcLevel(each) > calcLevel(stack.peek())) && calcLevel(each) < LEVEL_HIGH)){
                    stack.push(each);
                }else if( !stack.isEmpty() && calcLevel(each) <= calcLevel(stack.peek())){
                //栈非空，操作符优先级小于等于栈顶优先级时出栈入列，直到栈为空，或者遇到了(，最后操作符入栈
                    while (!stack.isEmpty() && calcLevel(each) <= calcLevel(stack.peek()) ){
                        if(calcLevel(stack.peek()) == LEVEL_HIGH){
                            break;
                        }
                        data.add(stack.pop());
                    }
                    stack.push(each);
                }else if(RIGHT.equals(each)){
                    // ) 操作符，依次出栈入列直到空栈或者遇到了第一个)操作符，此时)出栈
                    while (!stack.isEmpty() && LEVEL_HIGH >= calcLevel(stack.peek())){
                        if(LEVEL_HIGH == calcLevel(stack.peek())){
                            stack.pop();
                            break;
                        }
                        data.add(stack.pop());
                    }
                }
                start = i ;    //前一个运算符的位置
            }else if( i == s.length()-1 || isSymbol(s.charAt(i+1)+"") ){
                each = start == 0 ? s.substring(start,i+1) : s.substring(start+1,i+1);
                if(isNumber(each)) {
                    data.add(each);
                    continue;
                }
                throw new RuntimeException("data not match number");
            }
        }
  //如果栈里还有元素，此时元素需要依次出栈入列，可以想象栈里剩下栈顶为/，栈底为+，应该依次出栈入列，可以直接翻     转整个stack 添加到队列
        Collections.reverse(stack);
        data.addAll(new ArrayList<>(stack));
        System.out.println(data);
        return data;
    }
     // 算出结果
    public static Double doCalc(List<String> list){
        Double d = 0d;
        if(list == null || list.isEmpty()){
            return null;
        }
        if (list.size() == 1){
            System.out.println(list);
            d = Double.valueOf(list.get(0));
            return d;
        }
        ArrayList<String> list1 = new ArrayList<>();
        for (int i = 0; i < list.size(); i++) {
            list1.add(list.get(i));
            if(isSymbol(list.get(i))){
                Double d1 = doTheMath(list.get(i - 2), list.get(i - 1), list.get(i));
                list1.remove(i);
                list1.remove(i-1);
                list1.set(i-2,d1+"");
                list1.addAll(list.subList(i+1,list.size()));
                break;
            }
        }
        doCalc(list1);
        return d;
    }
     //运算
    public static Double doTheMath(String s1,String s2,String symbol){
        Double result ;
        switch (symbol){
            case ADD : result = Double.valueOf(s1) + Double.valueOf(s2); break;
            case MINUS : result = Double.valueOf(s1) - Double.valueOf(s2); break;
            case TIMES : result = Double.valueOf(s1) * Double.valueOf(s2); break;
            case DIVISION : result = Double.valueOf(s1) / Double.valueOf(s2); break;
            default : result = null;
        }
        return result;
    }
    public static void main(String[] args) {
        //String math = "9+(3-1)*3+10/2";
        String math = "12.8 + (2 - 3.55)*4+10/5.0";
        try {
            doCalc(doMatch(math));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 11. 递归(回溯)

```java
概念：递归就是方法自己调用自己,每次调用时传入不同的变量.递归有助于编程者解决复杂的问题,同时可以让代码变得简洁.
递归调用机制 ：
(1) 打印问题     (2) 阶乘问题     
```

![image-20200227152800140](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200227152800140.png)

```java
(1) 打印问题代码演示:
    public class RecursionTest {

	public static void main(String[] args) {      public static void main(String[] args) { 
		//通过打印问题，回顾递归调用机制					//通过打印问题，回顾递归调用机制
		test(4)									          test(4)
		
	}										  }
	//打印问题. 									//打印问题. 
	public static void test(int n) {				public static void test(int n) {
		if (n > 2) {									if (n > 2) {
			test(n - 1);									test(n - 1);
		} 												} else{
		System.out.println("n=" + n);						System.out.println("n=" + n);
	}													 }
}											}
运行结果是：n=2 										运行结果是：n=2 
           n=3										//只有n=2的原因是当n=2时n不大于此时才会执行else下
           n=4									    //的语句
```

```java
(2) 阶乘问题代码演示：
    public class RecursionTest {
    public static void main(String[] args) {
		int result = factorial(3);
		System.out.println("res=" + result);
	}
	//阶乘问题
	public static int factorial(int n) {
		if (n == 1) { 
			return 1;
		} else {
			return factorial(n - 1) * n; // 1 * 2 * 3
		}
	}
}
```

### 1. 递归需要遵循的规则

```java
(1) 执行一个方法时，就创建一个新的受保护的独立空间(栈空间) 
(2) 方法的局部变量是独立的，不会相互影响, 比如 n 变量 
(3) 如果方法中使用的是引用类型变量(比如数组)，就会共享该引用类型的数据. 
(4) 递归必须向退出递归的条件逼近，否则就是无限递归,出现 StackOverflowError(栈溢出),递归死:) 
(5) 当一个方法执行完毕，或者遇到 return，就会返回，遵守谁调用，就将结果返回给谁，同时当方法执行完毕或 者返回       时，该方法也就执行完毕
    //如上图来解释，当n=2后这个栈就执行完毕了，将返回或者跳转到调用它的那个栈即n=3那个栈.
```

### 2. 迷宫问题

```java
说明:
(1) 小球得到的路径，和程序员 设置的找路策略有关即：找 路的上下左右的顺序相关
(2) 再得到小球路径时，可以先 使用(下右上左)，再改成(上 右下左)，看看路径是不是有变化
(3) 测试回溯现象 
    
//思考: 如何求出最短路径
   //思路：找到最短路径需定义合理的策略,使用递归算法
```

```java
迷宫(labyrinth)问题2种策略代码演示:
public class MiGong {

	public static void main(String[] args) {
		// 先创建一个二维数组，模拟迷宫, 地图
		int[][] map = new int[8][7];//8行7列的数组
		// 使用1 表示墙，上下全部置为1：即上下边界的元素全是1，最上面的一行就是第0行，最下面的一行是第7行
		//此时只需遍历上述两行元素的纵下标即可，一共是7列
		for (int i = 0; i < 7; i++) {
			map[0][i] = 1;//第0行所有列置为1
			map[7][i] = 1;//第7行所有列置为1
		}

		// 左右全部置为1
		//将第0列和第6列设为墙，此时只有航标在变
		for (int i = 0; i < 8; i++) {
			map[i][0] = 1;
			map[i][6] = 1;
		}
		//设置挡板, 1 表示
		map[3][1] = 1;
		map[3][2] = 1;
		/*
		 * 将以下设成挡板后，只有2个格子可以走，当开始从第一个格子开始走像右走，不通，然后向下走
		 * 可以走通到了第二个格子，第二个各自向右走还是不通，就向下走，但下面还是挡板也走不通此时形成了死路
		 * 就会在第二个格子这标为3代表走过，根据栈的规则，此时这个格子会回到调用它的那个格子即第一个格子，此             时又将
		 * 第一个格子表为了3，由此1 2两个格子被标为死路，这个步骤用了回溯规则，注意此处采用的是策略1.0 。
		 */
		//map[1][2] = 1;//设成挡板
		//map[2][2] = 1;//设成挡板
		
		// 输出地图
		System.out.println("地图的情况");
		for (int i = 0; i < 8; i++) {
			for (int j = 0; j < 7; j++) {
				System.out.print(map[i][j] + " ");
			}
			System.out.println();
		}
		
		//使用递归回溯给小球找路
		//setWay(map, 1, 1);
		setWay2(map, 1, 1);
		
		//输出新的地图, 小球走过，并标识过的递归
		System.out.println("小球走过，并标识过的 地图的情况");
		for (int i = 0; i < 8; i++) {
			for (int j = 0; j < 7; j++) {
				System.out.print(map[i][j] + " ");
			}
			System.out.println();
		}
		
	}
	
	//使用递归回溯来给小球找路  
	//说明
	//1. map 表示地图
	//2. i,j 表示从地图的哪个位置开始出发 (1,1)
	//3. 如果小球能到 map[6][5] 位置，则说明通路找到.
	//4. 约定： 当map[i][j] 为 0 时表示该点没有走过 当为 1时 表示墙  ； 2 表示通路可以走 ； 3 表示该点已经          走过，但是走不通
	//5. 在走迷宫时，需要确定一个策略1.0(方法) 下->右->上->左 , 如果该点走不通，再回溯
	/**
	 * 
	 * map 表示地图
	 *  i 从哪个位置开始找
	 *  j 
	 *  如果找到通路，就返回true, 否则返回false
	 */
	public static boolean setWay(int[][] map, int i, int j) {
		// 第一种策略：下->右->上->左 
		if(map[6][5] == 2) { // 通路已经找到ok
			return true;
		} else {
			if(map[i][j] == 0) { //如果当前这个点还没有走过
				//按照策略 下->右->上->左  走
				map[i][j] = 2; // 假定该点是可以走通.
				if(setWay(map, i+1, j)) {//向下走,即让行下标+1
					return true;
				} else if (setWay(map, i, j+1)) { //向右走，即行下表不变，列下标+1
					return true;
				} else if (setWay(map, i-1, j)) { //向上即列下表不变，行下标-1
					return true;
				} else if (setWay(map, i, j-1)){ // 向左走，即行下标不变，列下标-1
					return true;
				} else {
					//说明该点是走不通，是死路
					map[i][j] = 3;
					return false;
				}
			} else { // 如果map[i][j] != 0 , map可能是 1， 2， 3
				return false;
			}
		}
	}
	
	// 第二种策略：上->右->下->左
	public static boolean setWay2(int[][] map, int i, int j) {
		if(map[6][5] == 2) { // 通路已经找到ok
			return true;
		} else {
			if(map[i][j] == 0) { //如果当前这个点还没有走过
				//按照策略 上->右->下->左
				map[i][j] = 2; // 假定该点是可以走通.
				if(setWay2(map, i-1, j)) {//向上走，行标-1，列标不变
					return true;
				} else if (setWay2(map, i, j+1)) { //向右走，列标不变，行标+1
					return true;
				} else if (setWay2(map, i+1, j)) { //向下，行标+1，列标不变
					return true;
				} else if (setWay2(map, i, j-1)){ // 向左走，即行下标不变，列下标-1
					return true;
				} else {
					//说明该点是走不通，是死路
					map[i][j] = 3;
					return false;
				}
			} else { // 如果map[i][j] != 0 , 可能是 1， 2， 3
				return false;
			}
		}
	}
}
```

## 12. 排序算法部分

```java
排序也称排序算法(Sort Algorithm)，排序是将一组数据，依指定的顺序进行排列的过程
排序的分类： 
    (1) 内部排序: 指将需要处理的所有数据都加载到内部存储器(内存)中进行排序. 
    (2) 外部排序法： 数据量过大，无法全部加载到内存中，需要借助外部存储(文件等)进行排序.
    (3) 常见排序算法分类如下图:
```

![image-20200227230617665](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200227230617665.png)

### 1. 算法的时间复杂度

```java
度量一个程序(算法)执行时间的两种方法
(1) 事后统计的方法 这种方法可行,但是有两个问题：
    一是要想对设计的算法的运行性能进行评测，需要实际运行该程序 ；二是所得时间的统计量依赖于计算机的硬件、软件等     环境因素, 这种方式，要在同一台计算机的相同状态下运行，才能比较那个算法速度更快.
(2) 事前估算的方法: 
    通过分析某个算法的时间复杂度来判断哪个算法更优.
```

### 2.  时间复杂度之时间频度

```java
时间频度:
一个算法花费的时间与算法中语句的执行次数成正比例，哪个算法中语句执 行次数多，它花费时间就多。
一个算法中的语句执行次数称为语句频度或时间频度。记为T(n)
```

```java
时间频度举例演示：
   计算1-100所有数字之和：
    第一种算法：                                      第一种算法：
    int a=0;									  a=(1+n)*n/2(等差数列公式)
    int n=100;									此算法的时间频度是: T(n)=1
    for(int i=1;i<n;i++){                           无论n的数有多大,程序只执行这一次,所以频度为1
        a+=i;
    }
此算法的时间频度是 T(n)+1 
n代表程序的规模，当for循环到n=100时，还需i++
再判断依次i<n，这个就是多出来的那次即+1
```

![image-20200228121015077](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200228121015077.png)

```java
结论: // 对于时间频度常数项可忽略
(1) 2n+20 和 2n 随着n 变大，执行曲线无限接近, 20可以忽略 
(2) 3n+10 和 3n 随着n 变大，执行曲线无限接近, 10可以忽略
```

![image-20200228123140108](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200228123140108.png)

```java
结论: //忽略低次项
(1) 2n^2+3n+10 和 2n^2 随着n 变大, 执行曲线无限接近, 可以忽略 3n+10 
(2) n^2+5n+20 和 n^2 随着n 变大,执行曲线无限接近, 可以忽略 5n+20
```

![image-20200228124436334](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200228124436334.png)

```java
结论: // 忽略系数
  (1) 随着n值变大，5n^2+7n 和 3n^2 + 2n ，执行曲线重合, 说明 这种情况下, 5和3 可以忽略。
  (2) 而n^3+5n 和 6n^3+4n ，执行曲线分离，说明多少次方式关键
```

### 3. 时间复杂度计算计算

```java
时间复杂度
(1) 一般情况下，算法中的基本操作语句的重复执行次数是问题规模n 的某个函数，用T(n)表示，若有某个辅助函数f(n)，使     得当n 趋近于无穷大时，T(n) / f(n) 的极限值为不等于零的常数，则称f(n)是T(n) 的同数量级函数。记作 T(n)=Ｏ(     f(n) )，称Ｏ( f(n) ) 为算法的渐进时间复杂度，简称时间复杂度。//T(n)=n+1  f(n)=n 则T(n)/f(n)----->1
(2) T(n) 不同，但时间复杂度可能相同。 如：T(n)=n²+7n+6 与 T(n)=3n²+2n+2 它们的T(n) 不同，但时间复杂度相同，     都为O(n²)。 //因为低次项，常数项，系数均可忽略
(3) 计算时间复杂度的方法：
    • 用常数1代替运行时间中的所有加法常数// T(n)=n²+7n+6 => T(n)=n²+7n+1 
    • 修改后的运行次数函数中，只保留最高阶项 //T(n)=n²+7n+1 => T(n) = n² 
    • 去除最高阶项的系数 //T(n) = n² => T(n) = n² => O(n²)
```

```java
1. 常数阶O(1) 2. 对数阶O(log2n) 3. 线性阶O(n) 4. 线性对数阶O(nlog2n) 
5. 平方阶O(n^2)(双层嵌套循环) 6. 立方阶O(n^3)(3层嵌套循环) 7. k次方阶O(n^k)(k曾嵌套循环) 8. 指数阶O(2^n)
```

![image-20200228125225990](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200228125225990.png)

```java
• 常见的算法时间复杂度由小到大依次为：Ο(1)＜Ο(log2n)＜Ο(n)＜Ο(nlog2n)＜Ο(n^2)＜Ο( n^3)＜ Ο(n^k) ＜Ο(2^n)<o(n!) ，随着问题规模n,的不断增大，上述时间复杂度不断增大，算法的执行效率越低 
• 从图中可见，我们应该尽可能避免使用指数阶的算法
```

```java
1.常数阶O(1)
 无论代码执行了多少行，只要是没有循环等复杂结构，那这个代码的时间复杂度就都是O(1)
 int i=1;
 int  j=2;
 ++i;
 j++;
 int m=i+j;
上面的代码执行时,消耗时间并不随着某个变量的增长我而增长,即无论这类代码多长，几百万行也无所谓均可用o(1)表示其时间复杂度
    
2.对数阶O(log2n)
  int i=1;
  while(i<n){
      i*=2;
  }
再while循环中,每次都将 i 乘以 2，乘完之后，i 距离 n 就越来越近了。假设循环x次之后，i 就大于 2 了，此时这个循环就退出了，也就是说 2 的 x 次方等于 n，那么 x = log2n也就是说当循环 log2n 次以后，这个代码就结束了。因此这个代码的时间复杂度为：O(log2n) . O(log2n) 的这个 2 时间上是根据代码变化的，i = i * 3 ，则是 O(log3n) .
//如 n=1024 则log2n=10,则结果就是o(10)即为复杂度
    
3.线性阶O(n)
  for(int i=1;i<=n;i++){
      j=i;
      j++;
  }
for循环里面的代码会执行n遍，因此它消耗的时间是随着n 的变化而变化的因此这类代码都可以用O(n)来表示它的时间复杂度
    
4.线性对数阶O(nlog2n)
  for(int m=1;m<n;m++){
      i=1;
      while(i<n){
          i*=2;
      }
  }
说明：线性对数阶O(nlogN) 将时间复杂度为O(logn)的代码循环N 遍的话，那么它的时间复杂度就是 n * O(logN)，也就是了O(nlogN)
    
5. 平方阶O(n^2)(双层嵌套循环)
   for(x=1;i<=n;i++){
       for(i=1;i<=n;i++){
           j=i;
           j++;
       }
   }
说明：平方阶O(n²) ,如果把 O(n) 的代码再嵌套循环一遍，它的时间复杂度就是 O(n²)，这段代码其实就是嵌套了2层n 循环，它的时间复杂度就是 O(n*n)，即 O(n²) 如果将其中一层循环的n改成m ，那它的时间复杂度就变成了 O(m*n)
```

### 4. 平均时间复杂度和最坏时间复杂度

![image-20200228151414465](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200228151414465.png)

```java
(1) 平均时间复杂度是指所有可能的输入实例均以等概率出现的情况下,该算法的运行时间.
(2) 最坏情况下的时间复杂度称最坏时间复杂度。一般讨论的时间复杂度均是最坏情况下的时间复杂度. 这样做的原因是：       最坏情况下的时间复杂度是算法在任何输入实例运行时间的界限，这就保证了算法的运行时间不会比最坏情况更长.
```

### 5. 算法的空间复杂度

```java
(1) 类似于时间复杂度的讨论，一个算法的空间复杂度(Space Complexity) 定义为该算法所耗费的存储空间，它也是问题规     模n的函数。
(2) 空间复杂度(Space Complexity)
    是对一个算法在运行过程中临时占用存储空间大小的量度。有的算法需要占用的临时工作单元数与解决问题的规模n有       关，它随着n的增大而增大，当 n 较大时，将占用较多的存储单元，例如快速排序和归并排序算法就属于这种情况
(3) 在做算法分析时，主要讨论的是时间复杂度
   从用户使用体验上看，更看重的程序执行的速度。一些缓存产品(Redis, memcache)和算法(基数排序)本质就是用空间换    时间.
```

### 6. 冒泡排序算法

```java
冒泡排序（Bubble Sorting）的基本思想是：通过对待 排序序列从前向后（从下标较小的元素开始）,依次比较 相邻元素的值，若发现逆序(违反设定的从小到大或者从大到小)则交换，使值较大 的元素逐渐从前移向后部，就象水底下的气泡一样逐渐 向上冒。
    
因为排序的过程中，各元素不断接近自己的位置，如果一趟比较下 来没有进行过交换，就说明序列有序，因此要在排序过程中设置 一个标志flag判断元素是否进行过交换。从而减少不必要的比较。( 这里说的优化，可以在冒泡排序写好后，在进行)
 
冒泡排序的规律总结:
(1) 一共进行 数组的大小 -1 次 大的循环 
(2)每一趟排序的次数在逐渐的减少 
(3) //如果我们发现在某趟排序中，没有发生一次交换， 可以提前结束冒泡排序。这个就是优化
```

```java
冒泡排序代码演示:
1.0版本演示冒泡排序的细节流程

import java.util.Arrays;
public class MaoPao {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
        int array[]= {3,9,-1,10,-2};//此数组大小为5需要4次循环
        //第一趟排序，将最大的数排在最后
        //先定义临时变量
        int temp=0;
        for(int j=0;j<array.length-1;j++) {
        	//如果前面的数大于后面的数则两个数的位置进行交换 j=1
        	if(array[j]>array[j+1]) {
        		temp=array[j];
        		array[j]=array[j+1];
        		array[j+1]=temp;
        	}
        }
        System.out.println("第一趟排序排序后的数组:"+Arrays.toString(array));//[3, -1, 9, -2, 10]
       //第二趟排序，把第二大的数排在倒数第二个位置
        for(int j=0;j<array.length-2;j++) {
        	//如果前面的数大于后面的数则两个数的位置进行交换   j=2
        	if(array[j]>array[j+1]) {
        		temp=array[j];
        		array[j]=array[j+1];
        		array[j+1]=temp;
        	}
        }
        System.out.println("第二趟排序排序后的数组:"+Arrays.toString(array));//[-1, 3, -2, 9, 10]
      //第三趟排序，把第三大的数排在倒数第三个位置
        for(int j=0;j<array.length-3;j++) {
        	//如果前面的数大于后面的数则两个数的位置进行交换   j=3
        	if(array[j]>array[j+1]) {
        		temp=array[j];
        		array[j]=array[j+1];
        		array[j+1]=temp;
        	}
        }
        System.out.println("第三趟排序排序后的数组:"+Arrays.toString(array));//[-1, -2, 3, 9, 10]
        //第四趟排序，把第四大的数排在倒数第四位置
        for(int j=0;j<array.length-4;j++) {
        	//如果前面的数大于后面的数则两个数的位置进行交换  j=4
        	if(array[j]>array[j+1]) {
        		temp=array[j];
        		array[j]=array[j+1];
        		array[j+1]=temp;
        	}
        }
        System.out.println("第三趟排序排序后的数组:"+Arrays.toString(array));//[-2, -1, 3, 9, 10]
	}
}
=====================================================================================================
1.1版本将以上代码进行整合:
// length-1-i减的这个i就是上述代码减的那个j
import java.util.Arrays;

public class MaoPao {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
        int array[]= {3,9,-1,10,-2};
        //第一趟排序，将最大的数排在最后
        //先定义临时变量
        int temp=0;
        for(int i=0;i<array.length-1;i++) {
        	 for(int j=0;j<array.length-1-i;j++) {
             	//如果前面的数大于后面的数则两个数的位置进行交换
             	if(array[j]>array[j+1]) {
             		temp=array[j];
             		array[j]=array[j+1];
             		array[j+1]=temp;
             	}
             }
        	 System.out.println("第"+(i+1)+"趟排序排序后的数组:"+Arrays.toString(array));
        } 
	}
}
运行结果：
        第1趟排序排序后的数组:[3, -1, 9, -2, 10]
        第2趟排序排序后的数组:[-1, 3, -2, 9, 10]
        第3趟排序排序后的数组:[-1, -2, 3, 9, 10]
        第4趟排序排序后的数组:[-2, -1, 3, 9, 10]

=====================================================================================================
2.0版本冒泡排序优化(即鸡尾酒算法！！！)：
//如果发现在某趟排序中，没有发生一次交换， 可以提前结束冒泡排序。这个就是优化  

import java.util.Arrays;

public class MaoPao {
    public static void sort(int [] array) {
    	//第一趟排序，将最大的数排在最后
        //先定义临时变量
        int temp=0;
        //判断是否进行过数据交换
        boolean flag=false;
        for(int i=0;i<array.length-1;i++) {
        	 for(int j=0;j<array.length-1-i;j++) {
             	//如果前面的数大于后面的数则两个数的位置进行交换
             	if(array[j]>array[j+1]) {
             		flag=true;//标识此处已经进行过交换
             		temp=array[j];
             		array[j]=array[j+1];
             		array[j+1]=temp;
             	}
             }
        	 if(!flag) {//如果在某趟排序过程中一次，一次也没交换位置，就退出重新下一趟排序
        		 break;
        	 }else {
        		 flag=false;//如果发生了交换，则应该重置flag，用于继续判断
        	 }
        }
    }
	public static void main(String[] args) {
		// TODO Auto-generated method stub
        int array[]= {3,9,-1,10,68};
        System.out.println("排序前的数组:");
        System.out.println(Arrays.toString(array));
        sort(array);
        System.out.println("排序后的数组:");
        System.out.println(Arrays.toString(array));
	}
}

运行结果:
排序前的数组:
[3, 9, -1, 10, 68]
排序后的数组:
[-1, 3, 9, 10, 68]


====================================================================================================
测试冒泡排序的运行效率：
    
package com.bytedance.maopao;

import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

public class MaoPao {
    public static void sort(int [] array) {
    	//第一趟排序，将最大的数排在最后
        //先定义临时变量
        int temp=0;
        //判断是否进行过数据交换
        boolean flag=false;
        for(int i=0;i<array.length-1;i++) {
        	 for(int j=0;j<array.length-1-i;j++) {
             	//如果前面的数大于后面的数则两个数的位置进行交换
             	if(array[j]>array[j+1]) {
             		flag=true;//标识此处已经进行过交换
             		temp=array[j];
             		array[j]=array[j+1];
             		array[j+1]=temp;
             	}
             }
        	 if(!flag) {//如果在某趟排序过程中一次，一次也没交换位置，就退出重新下一趟排序
        		 break;
        	 }else {
        		 flag=false;//如果发生了交换，则应该重置flag，用于继续判断
        	 }
        }
    }
	public static void main(String[] args) {
		// 定义大小为100000的数组
        int array[]= new int [100000];
        //随机向数组存入数据
        for(int i=0;i<array.length;i++) {
        	array[i]=(int)(Math.random()*100000);
        }
        //定义时间以及格式
        Date dat=new  Date();
        SimpleDateFormat simple=new	 SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        //将时间格式转换为字符串
        String da=simple.format(dat);
        System.out.println("排序前当前时间:"+da);
        sort(array);
        Date dat2=new  Date();
        String da2=simple.format(dat2);
        System.out.println("排序后当前时间:"+da2);
        运行结果：//时间17秒左右
	}
}
```

### 7. 选择排序算法

```java
选择式排序也属于内部排序法，是从欲排序的数据中，按指定的规则选出某一元素，再依规定交换位置后达到排序的目的。 

选择排序思想:
第一次从 arr[0]~arr[n-1]中选取最小值，与 arr[0]交换，第二次从 arr[1]~arr[n-1]中选取最小值，与 arr[1]交换，第三次从 arr[2]~arr[n-1]中选取最小值，与 arr[2] 交换，…，第 i 次从 arr[i-1]~arr[n-1]中选取最小值，与arr[i-1]交换，…, 第 n-1 次从 arr[n-2]~arr[n-1]中选取最小值， 与 arr[n-2]交换，总共通过 n-1 次，得到一个按排序码从小到大排列的有序序列.
```

![image-20200228202150224](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200228202150224.png)

```java
1.选择排序一共有数组大小(size)-1轮排序
2.每轮排序，即是新的一次循环
3.假定当前这个数是最小的数,然后和后面的每个数进行比较,如果发现有比当前数小的数则重新确定最小数,同时得到其下标
4.遍历到数组最后时,即可得到本轮最小数和其下标
```

```java
1.0 版本选择排序的细节流程:

import java.util.Arrays;

public class SelectSort {
	//选择排序
	public static void selectSort(int[] array){
	  //第一轮:假定数组的第一个元素是最小值
	  //假定最小元素下标为0即假定第一个元素是最小值
	  int minIndex=0;//第一个元素下标为0
	  int minValue=array[0];//数组的第一个值
	  for(int j=1;j<array.length;j++) {//j=1：此时第一个已经假定最小，所以应从其后一个元素开始找
		  if(minValue>array[j]) {//如果假定的最小值大于当前值，就做交换
			  minValue=array[j];
			  minIndex=j;//新的最小值下标
		  }
	  }
	 /*此时进行交换
	  * 例如[3,2,1,0]假设开始3是最小值，比较其他数发现0比3小则交换位置，3的下标是0，0的下标是3
	  * 即开始array[0]=3 array[3]=0,此时新的最小值是0用array[minIndex]表示，意思等同于array[3]
	  * 然后将原来设定的最小值即array[0]=3放在当前值array[3]的位置上，最后再将得到的这个最小值放在开始设定的         最小值的
	  * 位置上array[0]=minValue，此时就完成了交换的操作
	  * 
	  * if(minIndex!=0) :需要这步的原因如果{1，2，3，5}这是原始数组，则第一轮就无法执行了，因为1就是最小的           值，不需要交换操作
	  * 所以当得到的最小值下标和原始设定的最小值下标冲突时，就不用交换。
	  */
	  if(minIndex!=0) {
		  array[minIndex]=array[0];
		  array[0]=minValue;
	  }
	  System.out.println("第一轮排序后的数组:"+Arrays.toString(array));//[0, 2, 1, 3]
	  
	  //第二轮：步骤和第一轮一样，只是现在从数组的第2个元素开始比较
	   minIndex=1;
	   minValue=array[1];//数组的第二个值
	  for(int j=2;j<array.length;j++) {//j=2：此时第2个假定最小，所以应从其后一个元素开始找
		  if(minValue>array[j]) {//如果假定的最小值大于当前值，就做交换
			  minValue=array[j];//重置最小值
			  minIndex=j;//新的最小值下标
		  }
	  }
	  //此处和第一轮操作一样
	  if( minIndex!=1) {
	  array[minIndex]=array[1];
	  array[1]=minValue;
	  }
	  System.out.println("第二轮排序后的数组:"+Arrays.toString(array));//[0, 1, 2, 3]
	  
	 
	 //第三轮：步骤和前轮一样，只是现在从数组的第3个元素开始比较
	   minIndex=2;
	   minValue=array[2];//数组的第二个值
	  for(int j=3;j<array.length;j++) {//j=3：此时第3个假定最小，所以应从其后一个元素开始找
		  if(minValue>array[j]) {//如果假定的最小值大于当前值，就做交换
			  minValue=array[j];//重置最小值
			  minIndex=j;//新的最小值下标
		  }
	  }
	  //此处和第一轮操作一样
	  if( minIndex!=2) {
	  array[minIndex]=array[2];
	  array[2]=minValue;
	  }
	  System.out.println("第三轮排序后的数组:"+Arrays.toString(array));//[0, 1, 2, 3]
	  
	}

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int[] array= {3,2,1,0};
		System.out.println("原始数组:"+Arrays.toString(array));
		selectSort(array);
	}
}
=====================================================================================================
2.0 版本将上面代码整合成完整的选择排序:

public class SelectSort {
	//选择排序
	public static void selectSort(int[] array){
	for(int i=0;i<array.length-1;i++) {
	  int minIndex=i;
	  int minValue=array[i];//数组的第一个值
	  for(int j=i+1;j<array.length;j++) {//j=1：此时第一个已经假定最小，所以应从其后一个元素开始找
		  if(minValue<array[j]) {//如果假定的最小值大于当前值，就做交换
			  minValue=array[j];
			  minIndex=j;//新的最小值下标
		  }
	  }
	  if(minIndex!=i) {
		  array[minIndex]=array[i];
		  array[i]=minValue;
	  }
	  System.out.println("第"+(i+1)+"轮排序后的数组:"+Arrays.toString(array));
	} 
	  
	}

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int[] array= {3,2,1,0,10,80,34,68,78,8};
		System.out.println("原始数组:"+Arrays.toString(array));
		selectSort(array);
	}

}
=====================================================================================================
测试选择排序的运行效率：
    
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

public class SelectSort {
	//选择排序
	public static void selectSort(int[] array){
	for(int i=0;i<array.length-1;i++) {
	  int minIndex=i;
	  int minValue=array[i];//数组的第一个值
	  for(int j=i+1;j<array.length;j++) {//j=1：此时第一个已经假定最小，所以应从其后一个元素开始找
		  if(minValue<array[j]) {//如果假定的最小值大于当前值，就做交换
			  minValue=array[j];
			  minIndex=j;//新的最小值下标
		  }
	  }
	  if(minIndex!=i) {
		  array[minIndex]=array[i];
		  array[i]=minValue;
	  }
	  //System.out.println("第"+(i+1)+"轮排序后的数组:"+Arrays.toString(array));
	} 
	  
	}

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		
		// 定义大小为100000的数组
        int array[]= new int [100000];
        //随机向数组存入数据
        for(int i=0;i<array.length;i++) {
        	array[i]=(int)(Math.random()*100000);
        }
        //定义时间以及格式
        Date dat=new  Date();
        SimpleDateFormat simple=new	 SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        //将时间格式转换为字符串
        String da=simple.format(dat);
        System.out.println("排序前当前时间:"+da);
        selectSort(array);
        Date dat2=new  Date();
        String da2=simple.format(dat2);
        System.out.println("排序后当前时间:"+da2);
        运行结果：//时间2秒左右
	}
}
```

### 8. 插入排序算法

```java
插入式排序属于内部排序法，是对于欲排序的元素以插入的方式找寻该元素的 适当位置，以达到排序的目的.
插入排序法思想:
插入排序（Insertion Sorting）的基本思想是：把n 个待排序的元素看成为一个有序表和一个无序表， 开始时有序表中只包含一个元素，无序表中包含有n-1个元素 ，排序过程中每次从无序表中取出第一个元素，把它的排序码依次与有序表元素的排序码进行比较，将它插入到有序表中的适当位置，使之成 为新的有序表.
    
//插入排序存在的问题：当待插入的数是较小的时候，后移次数增多很多，影响效率
```

![image-20200229001535473](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200229001535473.png)

```java
以第一轮插入做演示后续几轮与第一轮操作流程是一样的：
先假设一个待排序数组方演示:  int[] array= {30,23,29,25,};
```

![image-20200229134419776](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200229134419776.png)

```java
1.0版本插入排序的具体实现步骤:
package com.bytedance.maopao;

import java.util.Arrays;

//共需array.length-1轮
public class InsertSort {
    //插入排序
	public static void insertSort(int[] array) {
		//第一轮:将60插入到有序列表
		//定义待插入数据
		int insertValue=array[1];
		//待插入数的前一个数的下标
		int insertIndex=0;
		while(insertIndex >=0 && insertValue<array[insertIndex]) {
			array[insertIndex+1]=array[insertIndex];
			insertIndex--;
		}
		array[insertIndex+1]=insertValue;
		System.out.println("第一轮插入后结果:"+Arrays.toString(array));
		
		//第二轮：
		 insertValue=array[2];
		//
		 insertIndex=1;
		while(insertIndex >=0 && insertValue<array[insertIndex]) {
			array[insertIndex+1]=array[insertIndex];
			insertIndex--;
		}
		array[insertIndex+1]=insertValue;
		System.out.println("第2轮插入后结果:"+Arrays.toString(array));
		
		//第3轮：
		 insertValue=array[3];
		//
		 insertIndex=2;
		while(insertIndex >=0 && insertValue<array[insertIndex]) {
			array[insertIndex+1]=array[insertIndex];
			insertIndex--;
		}
		array[insertIndex+1]=insertValue;
		System.out.println("第3轮插入后结果:"+Arrays.toString(array));
		
	}
	public static void main(String[] args) {
		// TODO Auto-generated method stub
      int[] array= {30,23,29,25,};
      insertSort(array);
	}

}
//运行结果:
   第1轮插入后结果:[23, 30, 29, 25]
   第2轮插入后结果:[23, 29, 30, 25]
   第3轮插入后结果:[23, 25, 29, 30]

```

```java
2.0版本选择排序(对以上代码整合)
    
import java.util.Arrays;

//共需array.length-1轮
public class InsertSort {
    //插入排序
	public static void insertSort(int[] array) {
		
		for(int i=1;i<array.length;i++) {
		int insertValue=array[i];
		//待插入数的前一个数的下标
		int insertIndex=i-1;
		while(insertIndex >=0 && insertValue<array[insertIndex]) {
			array[insertIndex+1]=array[insertIndex];
			insertIndex--;
		}
		array[insertIndex+1]=insertValue;
		System.out.println("第"+(i+1)+"轮插入后结果:"+Arrays.toString(array));
		
		}
		
	}
	public static void main(String[] args) {
		// TODO Auto-generated method stub
      int[] array= {30,23,29,25,};
      insertSort(array);
	}
}
//运行结果:
   第1轮插入后结果:[23, 30, 29, 25]
   第2轮插入后结果:[23, 29, 30, 25]
   第3轮插入后结果:[23, 25, 29, 30]
```

```java
测试插入排序的运行效率：
    
package com.bytedance.maopao;

import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

//共需array.length-1轮
public class InsertSort {
    //插入排序
	public static void insertSort(int[] array) {
		
		for(int i=1;i<array.length;i++) {
		int insertValue=array[i];
		//待插入数的前一个数的下标
		int insertIndex=i-1;
		while(insertIndex >=0 && insertValue<array[insertIndex]) {
			array[insertIndex+1]=array[insertIndex];
			insertIndex--;
		}
		array[insertIndex+1]=insertValue;
		
		}
		
	}
	public static void main(String[] args) {
		// TODO Auto-generated method stub
       // 定义大小为100000的数组
      int array[]= new int [100000];
      //随机向数组存入数据
      for(int i=0;i<array.length;i++) {
      	array[i]=(int)(Math.random()*100000);
      }
      //定义时间以及格式
      Date dat=new  Date();
      SimpleDateFormat simple=new	 SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
      //将时间格式转换为字符串
      String da=simple.format(dat);
      System.out.println("排序前当前时间:"+da);
      insertSort(array);
      Date dat2=new  Date();
      String da2=simple.format(dat2);
      System.out.println("排序后当前时间:"+da2);//运行结果：3秒左右
	}
}
```

### 9. 希尔排序

```java
希尔排序(Shell) 也是一种插入排序 ，它是简单插入排序经过改进之后的一个更高效的版本，也称为缩小增量排序 。
    
希尔排序法基本思想:
希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序 ；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1 时，整个文件恰被分成一组，算法便终止
```

插图的位置保留！！！！！

```java
1.0版本希尔排序之交换法：
   
//Shell排序之交换式算法(速度相对较低)
public class ShellSort {
	public static void shellSort(int[] array) {
		int temp=0;
		//第一轮：将数组分成array.length/2组，以定好的数组为例就是分成了5组
		for(int i=5;i<array.length;i++) {//先将素组分为5组
			for(int j=i-5;j>=0;j-=5) {//遍历每一组的元素，每组有2个元素,每组元素之间步长是5
				if(array[j]>array[j+5]) {
					temp=array[j];
					array[j]=array[j+5];
					array[j+5]=temp;
				}
			}
		}
		System.out.println("第1轮排序后数组:"+Arrays.toString(array));
		//第二轮：将第一轮的5组基础上再分成2组
		for(int i=2;i<array.length;i++) {
			for(int j=i-2;j>=0;j-=2) {
				if(array[j]>array[j+2]) {
					temp=array[j];
					array[j]=array[j+2];
					array[j+2]=temp;
				}
			}
		}
		System.out.println("第2轮排序后数组:"+Arrays.toString(array));
		
		//第3轮：将第2轮的2组基础上再分成1组
				for(int i=1;i<array.length;i++) {
					for(int j=i-1;j>=0;j-=1) {
						if(array[j]>array[j+1]) {
							temp=array[j];
							array[j]=array[j+1];
							array[j+1]=temp;
						}
					}
				}
				System.out.println("第3轮排序后数组:"+Arrays.toString(array));
	}
	public static void main(String[] args) {
		// TODO Auto-generated method stub
        int[] array= {8,9,1,7,2,3,5,4,6,0};//共10个数据
        shellSort(array);
	}
}
```

```java
2.0版本希尔排序之交换法：
    
import java.util.Arrays;

//Shell排序之交换式算法(速度相对较低)
public class ShellSort {
	 
	public static void shellSort(int[] array) {
		int temp=0;
        // int count=0;
	 for(int flag=array.length/2;flag>0;flag/=2) {
		for(int i=flag;i<array.length;i++) {//先将素组分为5组
			for(int j=i-flag;j>=0;j-=flag) {//遍历每一组的元素，每组有2个元素
				if(array[j]>array[j+flag]) {
					temp=array[j];
					array[j]=array[j+flag];
					array[j+flag]=temp;
				}
			}
		}
         //System.out.println("第"+(++count)+"轮排序后数组:"+Arrays.toString(array));
	 }
	}
	public static void main(String[] args) {
		// TODO Auto-generated method stub
        int[] array= {8,9,1,7,2,3,5,4,6,0};//共10个数据
        System.out.println("原始数组:"+Arrays.toString(array));
        shellSort(array);
        System.out.println("排序后的数组:"+Arrays.toString(array));
	}
}
```

```java
希尔排序之交换算法的运行效率测试:

import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

//Shell排序之交换式算法(速度相对较低)
public class ShellSort {
	 
	public static void shellSort(int[] array) {
		int temp=0;
	 for(int flag=array.length/2;flag>0;flag/=2) {
		for(int i=flag;i<array.length;i++) {//先将素组分为5组
			for(int j=i-flag;j>=0;j-=flag) {//遍历每一组的元素，每组有2个元素
				if(array[j]>array[j+flag]) {
					temp=array[j];
					array[j]=array[j+flag];
					array[j+flag]=temp;
				}
			}
		}
	 }
	}
	public static void main(String[] args) {
		// TODO Auto-generated method stub
        // 定义大小为100000的数组
        int array[]= new int [100000];
        //随机向数组存入数据
        for(int i=0;i<array.length;i++) {
        	array[i]=(int)(Math.random()*100000);
        }
        //定义时间以及格式
        Date dat=new  Date();
        SimpleDateFormat simple=new	 SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        //将时间格式转换为字符串
        String da=simple.format(dat);
        System.out.println("排序前当前时间:"+da);
        shellSort(array);
        Date dat2=new  Date();
        String da2=simple.format(dat2);
        System.out.println("排序后当前时间:"+da2);//13秒左右
        //if(array[j]>array[j+flag])这行代码是运行效率较低的原因
	}
}
```

```java
3.0版本 希尔排序之位移动算法:
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

//Shell排序之位移式算法(速度相对较低)
public class ShellSort {
	
	public static void shellSort(int[] array) {
		//定义flag增量进行分组，并逐渐缩小只增量
		for(int flag=array.length/2;flag>0;flag/=2) {
			//从第flag
			for(int i=flag;i<array.length;i++) {
				int j=i;
				int temp=array[j];
				if(array[j]<array[j-flag]) {
					while(j-flag>=0 && temp<array[j-flag]) {
						//
						array[j]=array[j-flag];
						j-=flag;
					}
					//退出while循环即找到了插入的位置
					array[j]=temp;
				}
			}
		}
	
	}
	public static void main(String[] args) {		
		int[] array= {8,9,1,7,2,3,5,4,6,0};//共10个数据
	    System.out.println("原始数组:"+Arrays.toString(array));
        shellSort(array);
	    System.out.println("排序后的数组:"+Arrays.toString(array));
	}
}
```

```java
希尔排序之位移动算法运行效率：
    import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

//Shell排序之位移式算法(速度相对较低)
public class ShellSort {
	
	public static void shellSort(int[] array) {
		//定义flag增量进行分组，并逐渐缩小只增量
		for(int flag=array.length/2;flag>0;flag/=2) {
			//从第flag
			for(int i=flag;i<array.length;i++) {
				int j=i;
				int temp=array[j];
				if(array[j]<array[j-flag]) {
					while(j-flag>=0 && temp<array[j-flag]) {
						//
						array[j]=array[j-flag];
						j-=flag;
					}
					//退出while循环即找到了插入的位置
					array[j]=temp;
				}
			}
		}
	
	}
	public static void main(String[] args) {		
		int array[]= new int [80000];
		//随机向数组存入数据
		for(int i=0;i<array.length;i++) {
			array[i]=(int)(Math.random()*80000);
		}
		//定义时间以及格式
		Date dat=new  Date();
		SimpleDateFormat simple=new	 SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		//将时间格式转换为字符串
		String da=simple.format(dat);
		System.out.println("排序前当前时间:"+da);
		shellSort(array);
		Date dat2=new  Date();
		String da2=simple.format(dat2);
		//System.out.println("排序后当前时间:"+da2);//1~3秒左右
	}
} 
```

### 10. 快速排序算法

```java
快速排序（Quicksort）是对冒泡排序的一种改进。
基本思想 是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进 行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\快速排序思路原理图.jpg)

```java
快速排序法应用实例: 
要求: 对 [-9,78,0,23,-567,70] 进行从小到大的 排序，要求使用快速排序法。【测试8w和800w】 
说明[验证分析]:
1.如果取消左右递归，结果是 -9 -567 0 23 78 70 
2.如果取消右递归,结果是 -567 -9 0 23 78 70 
3.如果取消左递归,结果是 -9 -567 0 23 70 78
```

```java
package com.bytedance.quicsort;
import java.util.Arrays;
/**
 * @author 快速排序算法
 * @create 2020-04-22 13:07
 */
public class QuicSort {
    public static void main(String[] args) {
        int[] array = {-9, 78, 0, 23, -567, 70};
        quickSort(array, 0, array.length - 1);
        System.out.println(Arrays.toString(array));

    }

    public static void quickSort(int[] array, int left, int right) {
        int l = left;//左索引
        int r = right;//右索引
        //中间基准值
        int pivot = array[(left + right)] / 2;
        //临时变量
        int temp = 0;
        //循环的目的是让比中间基准值小的放到其左边，比基准值大的放在其右边
        while (l < r) {
            //在中间基准值的左边一直找，直到找到比基准值大的或等于才推出
            while (array[l] < pivot) {
                l++;
            }
            //在中间基准值的右边一直找，直到找到比基准值小的或等于才推出
            while (array[r] > pivot) {
                r--;
            }
            //说明中间值的左右两边已经按照左边全部是小于等于中间值
            //右边是全部大于等于中间值的
            if (l >= r) {
                break;
            }
            //交换
            temp = array[l];
            array[l] = array[r];
            array[r] = temp;
            //如果交换完后发现array[l]==pivot,r向前移一位
            if (array[l] == pivot) {
                r--;
            }
            //如果交换完后发现array[r]==pivot,l向后移一位
            if (array[r] == pivot) {
                l++;
            }
        }
        //System.out.println("第一轮结果:" + Arrays.toString(array));

        //如果l==r，必须使l++,r-- 否则会出现栈溢出问题
        if (l == r) {
            l++;
            r--;
        }
        //向左递归
        if (left < r) {
            quickSort(array, left, r);
        }
        //向右递归
        if (right > l) {
            quickSort(array, l, right);
        }
    }
}
```

### 11. 归并排序算法

```java
归并排序（MERGE-SORT）是利用归并 的思想实现的排序方法，该算法采用经典的分治（divide-and-conquer ）策略（分治法将问题分(divide)成一些小的问题然后递归求解，而治(conquer) 的阶段则将分的阶段得到的各答案"修补"在一起，即分而治之)。//核心就是分治
```

![image-20200301151926500](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200301151926500.png)

```java
上图所示一共归并了7次，数组大小为8，即规律是：归并次数 count=arrray.length-1;
```

![image-20200301184348218](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200301184348218.png)

```java
归并排序算法演示:

import java.util.Arrays;

//归并排序算法
public class MergeSort {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
        int[] array= {8,4,5,7,1,3,6,2};
        int temp[]=new int[array.length];//这个临时数组占用了额外空间,这是归并排序的特点
        System.out.println("排序前的数组:"+Arrays.toString(array));
        mergeSort(array,0,array.length-1,temp);
        System.out.println("排序后的数组:"+Arrays.toString(array));
        
	}
	//分解与合并
	public static void mergeSort(int[] array,int left,int right,int[] temp) {
			if(left < right) {
				int mid = (left + right) / 2; //中间索引
				//向左递归分解：,左边分解后的最右边的边界索引就是未分解之前数组的中间位置
				mergeSort(array, left, mid, temp);
				//向右递归分解:右边分解后的最左边的边界索引就是未分解之前数组的中间位置+1个就是
				mergeSort(array, mid + 1, right, temp);
				//合并:调用写好的合并方法
				merge(array, left, mid, right, temp);
				
			}
		}
	//1.定义合并数组的方法
	/* array[] 原始数组
	 * left 数组左边有序序列的初始索引
	 * right 右边索引
	 * mid 中间索引，用于定位数组中间位置
	 * temp[] 临时数组用于存放数据，最后将临时数组元素拷贝到原始数组即完成排序
	 * 
	 */
	public static void merge(int[] array,int left,int mid,int right,int[] temp) {
		int i=left;//i:数组左边有序序列的初始索引
		int j=mid+1;//j:右边有序序列的初始索索引
		int t=0;//temp[]数组的索引，每次将前面的数组取出来数据放入temp数组，可根据temp索引来来放
		
		//a.将左右两面的有序序列的数据拷贝到temp数组，终止条件是左右两边有一边处理结束
		while(i <= mid && j <= right) {
	    //如果左边有序序列的当前元素小于右边有序序列的当前元素 ，就将左边有序序列的这个当前元素拷贝到temp中
			if(array[i] <= array[j]) {   //***将此处的小于号改成大于号就是从大到小排序***
				temp[t]=array[i];//将当前元素拷贝到数组
				t++;//当前元素存储完后，为下一次存储做准备，需后移
				i++;//同时 i 也后移指向左边有序序列的下一个元素
			}else {
				//表示右边有序序列的元素比左边有序序列的元素大，将右边的这个元素加到temp数组中
			   temp[t]=array[j];
			   t++;//当前元素存储完后，为下一次存储做准备，需后移
			   j++;//同时 j 也后移指向右边有序序列的下一个元素
			}
		}
		//b.把有数据剩余的一边的数据按顺序添入temp数组
		while(i <= mid) {
			//表示左边序列有元素剩余，将左边剩余元素拷贝到数组
			temp[t]=array[i];
			t++;
			i++;
		}
		while(j <= right) {
			//表示右边序列有元素剩余，将右边剩余元素拷贝到数组
			temp[t]=array[j];
			t++;
			j++;
		}
		//c.将temp数组的元素考入到原始数组array
		/*
		 * 一共考了7次
		 * 第一次是4 和8这组索引时0和1，tempLeft=0，right=1
		 * 第二次是5和7索引是2和3,tempLeft=2，right=3
		 * 第三次是以上两个合并，最左索引tempLeft=0，最右索引是right=3
		 * 最后后一次是最左索引tempLeft=0，最右索引是right=7
		 */
		t=0;
		int tempLeft=left;
		while(tempLeft<=right) {
			array[tempLeft]=temp[t];
			t++;
			tempLeft++;
		}
	}
}
//运行结果：
排序前的数组:[8, 4, 5, 7, 1, 3, 6, 2]
排序后的数组:[1, 2, 3, 4, 5, 6, 7, 8]
//测试运行效率代码省略，时间大约1s,和快排差不多
```

### 12. 基数排序算法（堆排序）

```java
(1) 基数排序（radix sort）属于“分配式排序”（distribution sort），又称“桶子法”（bucket sort）或bin sort ，     顾名思义，它是通过键值的各个位的值，将要排序的元素分配 至某些“桶”(数组)中，达到排序的作用
(2) 基数排序法是属于稳定性的排序，基数排序法的是效率高的稳定性排序法 
(3) 基数排序(Radix Sort)是桶排序的扩展 
(4) 基数排序是1887年赫尔曼·何乐礼发明的。它是这样实现的：将整数按位数切割成不同的数字，然后按 每个位数分别比       较。
    
基数排序基本思想:
(1) 将所有待比较数值统一为同样的数位长度，数位较短的数前面补零。然后， 从最低位开始，依次进行一次排序。这样从最     低位排序一直到最高位排序完 成以后, 数列就变成一个有序序列。  
 
// 进行的轮数与数组中最大的那个数的位数有关，三位数就进行3轮，5位数就进行5轮........
```





```java
基数排序的说明: 
(1) 基数排序是对传统桶排序的扩展，速度很快. 
(2) 基数排序是经典的空间换时间的方式，占用内存很大, 当对海量数据排序时，容易造成 OutOfMemoryError 。 
(3) 基数排序时稳定的。[注:
    假定在待排序的记录序列中，存在多个具有相同的关键字的记录，若经过排序，这些记录的相对 次序保持不变，即在原序     列中，r[i]=r[j]，且r[i]在r[j]之前，而在排序后的序列中，r[i]仍在r[j]之前， 则称这种排序算法是稳定的；否则     称为不稳定的]
(4) 有负数的数组，我们不用基数排序来进行排序(可能造成数组越界), 如果要支持负数(把负数转换成整数，最后在进行反转      ).
    参考: https://code.i-harness.com/zh-CN/q/e98fa9
```

```java
1.0 版本基数排序算法演示:

package com.bytedance.maopao;
import java.util.Arrays;

public class RadixSort {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int[] array={53,3,542,748,14,214};
		radixSort(array);
	}
    //定义基数排序方法
	public static void  radixSort(int[] array) {
		//第一轮：对每个元素的最低位进行处理(个位)
		//定义一个二维数组，表示10个数，每个桶就是一个一维数组
		//防止数据溢出，将每个一维数组大小定为 array.length,用空间换时间的算法
		int[][] bucket=new int[10][array.length];
		//定义一维数组记录每个桶中放入数据的个数
		int [] bucketElementCounts=new int[10];
		
		for(int j=0;j<array.length;j++) {
			//取出每个元素的个位
			int digitOfElement=array[j]%10;
			//将个位数字放入对应的桶中
			bucket[digitOfElement][bucketElementCounts[digitOfElement]]=array[j];
			bucketElementCounts[digitOfElement]++;
			
		}
		//按照桶的顺序(一维数组的下标将数据依次取出，放入原来的数组)
		int index=0;
		//遍历每一个桶，将桶中的数据放入原数组
		for(int k=0;k<bucketElementCounts.length;k++) {
			//如果桶中有数据才放入原数组
			if(bucketElementCounts[k]!=0) {
				//遍历这个桶中的元素
				for(int l=0;l<bucketElementCounts[k];l++ ) {
					//取元素放入原数组
					array[index++]= bucket[k][l];
				}
			}
			//第一轮结束后需将bucketElementCounts[k]置为0，否则会出问题(最细节处！！！！！)
			bucketElementCounts[k]=0;
		}
		System.out.println("第1轮的排序结果:"+Arrays.toString(array));
	//============================================================================
	  //第二轮排序：取十位上的数
		for(int j=0;j<array.length;j++) {
			//取出每个元素的个位
			int digitOfElement=array[j]/10%10;
			//将个位数字放入对应的桶中
			bucket[digitOfElement][bucketElementCounts[digitOfElement]]=array[j];
			bucketElementCounts[digitOfElement]++;
			
		}
		//按照桶的顺序(一维数组的下标将数据依次取出，放入原来的数组)
		 index=0;
		//遍历每一个桶，将桶中的数据放入原数组
		for(int k=0;k<bucketElementCounts.length;k++) {
			//如果桶中有数据才放入原数组
			if(bucketElementCounts[k]!=0) {
				//遍历这个桶中的元素
				for(int l=0;l<bucketElementCounts[k];l++ ) {
					//取元素放入原数组
					array[index++]= bucket[k][l];
				}
			}
			bucketElementCounts[k]=0;
		}
		System.out.println("第2轮的排序结果:"+Arrays.toString(array));
	//=====================================================================
	//第3轮排序：取出百位上的数
		for(int j=0;j<array.length;j++) {
			//取出每个元素的个位
			int digitOfElement=array[j]/100;
			//将个位数字放入对应的桶中
			bucket[digitOfElement][bucketElementCounts[digitOfElement]]=array[j];
			bucketElementCounts[digitOfElement]++;
			
		}
		//按照桶的顺序(一维数组的下标将数据依次取出，放入原来的数组)
		 index=0;
		//遍历每一个桶，将桶中的数据放入原数组
		for(int k=0;k<bucketElementCounts.length;k++) {
			//如果桶中有数据才放入原数组
			if(bucketElementCounts[k]!=0) {
				//遍历这个桶中的元素
				for(int l=0;l<bucketElementCounts[k];l++ ) {
					//取元素放入原数组
					array[index++]= bucket[k][l];
				}
			}
			bucketElementCounts[k]=0;
		}
		System.out.println("第3轮的排序结果:"+Arrays.toString(array));
	}
}

```

```java
2.0 版本基数排序算法演示(将1.0版本进行整合):

package com.bytedance.maopao;

import java.util.Arrays;

public class RadixSort {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int[] array={53,3,542,748,14,214};
		radixSort(array);
	}
    //定义基数排序方法
	public static void radixSort(int[] array) {
		// 得到数组中最大数的位数
		int max = array[0];// 假设第一个数是最大值，用其他值和第一个数比大小，直到找到最大数
		for (int i = 1; i < array.length; i++) {
			if (array[i] > max) {
				max = array[i];
			}
		}
		// 得到最大数的位数
		int maxLength = (max + "").length();
		// 定义一个二维数组，表示10个数，每个桶就是一个一维数组
		// 防止数据溢出，将每个一维数组大小定为 array.length,用空间换时间的算法
		int[][] bucket = new int[10][array.length];
		// 定义一维数组记录每个桶中放入数据的个数
		int[] bucketElementCounts = new int[10];
		for (int i = 0,n=1; i < maxLength; i++,n=n*10) {
			//针对轮数
			for (int j = 0; j < array.length; j++) {
				// 取出每个元素的个位
				int digitOfElement = array[j] /n% 10; //取个位时是%10相当于/n% 10此时n=1;
				// 将个位数字放入对应的桶中             //取十位上的数是/10% 10，取百位是/100
				bucket[digitOfElement][bucketElementCounts[digitOfElement]] = array[j];
				bucketElementCounts[digitOfElement]++;

			}
			// 按照桶的顺序(一维数组的下标将数据依次取出，放入原来的数组)
			int index = 0;
			// 遍历每一个桶，将桶中的数据放入原数组
			for (int k = 0; k < bucketElementCounts.length; k++) {
				// 如果桶中有数据才放入原数组
				if (bucketElementCounts[k] != 0) {
					// 遍历这个桶中的元素
					for (int l = 0; l < bucketElementCounts[k]; l++) {
						// 取元素放入原数组
						array[index++] = bucket[k][l];
					}
				}
				// 第i轮结束后需将bucketElementCounts[k]置为0，否则会出问题(最细节处！！！！！)
				bucketElementCounts[k] = 0;
			}
			System.out.println("第"+(i+1)+"轮的排序结果:" + Arrays.toString(array));
		}

	}
}
//运行结果：
第1轮的排序结果:[542, 53, 3, 14, 214, 748]
第2轮的排序结果:[3, 14, 214, 542, 748, 53]
第3轮的排序结果:[3, 14, 53, 214, 542, 748]
// 运行效率特别快不到1s 注意：当数据量特别大的时候，特别消耗内存，需注意
```

### 13. 排序算法的时间复杂度对比

![image-20200302105123335](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200302105123335.png)

```java
(1) 稳定：如果a原本在b前面，而a=b ，排序之后a仍然在b的前面；
(2) 不稳定：如果a原本在b的前面，而a= b，排序之后a可能会出现在b 的后面；
(3) 内排序 ：所有排序操作都在内存中完成； 
(4) 外排序:由于数据太大，因此把数据放在磁 盘中，而排序通过磁盘和内存的数据 传输才能进行；
5) 时间复杂度： 一个算法执行所耗费的时间。 
(6) 空间复杂度 ：运行完一个程序所需内存的大小。
(7) n: 数据规模 8) k: “桶”的个数
(9) In-place: 不占用额外内存 10) Out-place: 占用额外内存
    
//选择，冒泡，插入是一个级别，当数据量特别大的时候，时间复杂度越高
//Shell,归并，快排,堆排序是一个级别
//基数排序当数据量特大的时候时间复杂度越小，运行越快，但是消耗内存，当n小的时候基数排序的效率可能没有Shell那个级别的排序高.
```

## 13. 查找算法部分

```java
(1) 顺序(线性)查找 (2) 二分查找/折半查找 (3) 插值查找 (4) 斐波那契查找
```

### 1. 线性查找算法

```java
有一个数列： 
{1,8, 10, 89, 1000, 1234 } ，判断数列中是否包含此名称【 顺序查找】 要求:
如果找到了，就提示找到，并给出下 标值。
    
思路：
如果查找到全部符合条件的值 
```

```java
代码演示:
package search;

public class SeqSearch {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		//无序数组
		int array[]= {1,9,11,-1,34,89};
		int index=seqSearch(array, 11);
		if(index==-1) {
			System.out.println("没有找到该值！");
		}else {
			System.out.println("找到的元素对应下标为:"+index);
		}

	}
	//实现的线性查找是找到一个满足条件就返回
	public static int seqSearch(int[] array,int value) {
		//线性查找是逐个比对，发现有相同的值时返回下标
		for(int i=0;i<array.length;i++) {
			if(array[i]==value) {
				return i;
			}
		}
		return -1;
	}
}
```

### 2. 二分查找算法

```java
二分查找： 
请对一个有序数组进行二分查找 {1,8, 10, 89, 1000, 1234} ，输入一个数看看该数组是否存在此数，并且求出下标，如果没有就提示" 没有这个数"。
```

插图位置！！！！！

```java
二分查找递归算法：
import java.util.ArrayList;
import java.util.List;

//使用二分查找必须是有序的
public class BinarySearch {

	public static void main(String[] args) {
		int array[] = { 1, 8, 10, 89, 1000, 1000, 1000 };
		ArrayList<Integer> index = binarySearch(array, 0, array.length - 1, 89);
		System.out.println(index);
	}

	// 二分查找算法
	/*
	 * array 待查找的 数组 left 数组左边索引 right 数组右边索引 findValue 需要查找的值
	 * 如果没有找到需要查找的值，就返回-1，代表没查到
	 */
	public static ArrayList<Integer> binarySearch(int[] array, int left, int right, int findValue) {
		// 结束递归的条件
		if (left > right) {
			return new ArrayList<Integer>();
		}
		// 定义中间下标
		int mid = left + (right - left) / 2;
		int midValue = array[mid];
		if (findValue > midValue) {// 找的值大于中间值，则向右递归
			return binarySearch(array, mid + 1, right, findValue);
		} else if (findValue < midValue) {// 找的值小于中间值，则向左递归
			return binarySearch(array, left, mid - 1, findValue);
		} else {
			/*
			 * 查找数组其中元素相同的所有下标: 定义集合用于存储找到相同元素的下标 当找到了要查找的数，现向其左边扫面，向左扫描的开始位置就是这个值的前一位:mid-1
			 * 找到了相同值就将其下标存入集合，如果没找到继续向左扫描，当向左扫描到数组最左边还是没找到就退出循环
			 * 此时将向左扫描得到的相同元素的下标存入集合，然后再将最开始找到的那个Mid也存入集合
			 * 然后从最开始找到的那个mid向右扫描，向右扫描的开始位置是mid+1,原理和向左一样，中止条件是temp>array.length-1
			 * 此时已经扫描到数组最右边的边界
			 */
			ArrayList<Integer> resIndexlist = new ArrayList<Integer>();
			// 向左扫描的起始位置
			int temp = mid - 1;
			while (true) {
				if (temp < 0 || array[temp] != findValue) {
					break;
				}
				// 将找到的元素下标放进集合
				resIndexlist.add(temp);
				temp--;// 继续向左扫描
			}
			resIndexlist.add(mid);// 将最开始找到的mid即作为标准的那个mid加入集合
			// 向右扫描的起始位置
			temp = mid + 1;
			while (true) {
				if (temp > array.length - 1 || array[temp] != findValue) {
					break;
				}
				resIndexlist.add(temp);
				temp++;// 向右继续扫描
			}
			return resIndexlist;
		}
	}
}

```

```java
二分查找之旋转数组:
```

### 3. 插值查找算法

```java
(1) 插值查找算法类似于二分查找，不同的是插值查找每次从自适应mid处开始查找
(2) 将折半查找中的求mid 索引的公式 , low 表示左边索引left, high表示右边索引right. key 就是前面的         findVal
```

![image-20200302172759625](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200302172759625.png)

```java
(3) int mid = low + (high - low) * (key - arr[low]) / (arr[high] - arr[low]) ;/*插值索引*/ 
    对应前面的代码公式：
    int mid = left + (right – left) * (findVal – arr[left]) / (arr[right] – arr[left])
```

```java
利用上面公式举例：
给定一个数组，找出给定的值：array={1,2,3..........100}
1.找出 1 的位置
  int mid=0+(99-0)*(1-1)/(100-1)=0即1的位置是0
2. 找出100的位置
  int mid=0+(99-0)*(100-1)/(100-1)=99即100的位置是在99
```

```java
package search;

import java.util.Arrays;

public class InsertSearch {

	public static void main(String[] args) {
		int[] array = new int[100];
		for (int i = 0; i < 100; i++) {
			array[i] = i + 1;
		}
		int index = insertValue(array, 0, array.length - 1, 60);
		System.out.println("得到下标为:" + index);
	}

	// 定义插值查找算法方法
	/*
	 * start 左边索引 end 右边索引 插值算法要求算法有序
	 */
	public static int insertValue(int[] array, int start, int end, int findValue) {
//		int count=0;
//		count++;
//		System.out.println("查找次数："+count);
		// 没找到的情况:findValue < array[0] || findValue > array[array.length - 1]防止数组越界
		if (start > end || findValue < array[0] || findValue > array[array.length - 1]) {
			return -1;
		}
		// 定位mid
		int mid = start + (end - start) * (findValue - array[start]) / (array[end] - array[start]);
		int midValue = array[mid];
		if (findValue > midValue) {// 要查找的值比定位到的大，向右递归
			return insertValue(array, mid + 1, end, findValue);
		} else if (findValue < midValue) {// 向左递归
			return insertValue(array, start, mid - 1, findValue);
		} else {
			return mid;
		}
	}
}

```

```java
插值查找注意事项：
(1) 对于数据量较大，关键字分布比较均匀的查找表来说，采用插值查找, 速度较快.
(2) 关键字分布不均匀(指数据的跳跃性很大)的情况下，该方法不一定比折半查找要好
```

### 4. 斐波那契查找算法(黄金分割法)

```java
斐波那契(黄金分割法)查找基本介绍: 
(1) 黄金分割点是指把一条线段
    分割为两部分，使其中一部分与全长之比等于另一部分与这部 分之比。取其前三位数字的近似值是0.618 。由于按此比     s例设计的造型十分美丽，因此称为黄金分割 ，也称为中外比
(2) 斐波那契数列 {1, 1, 2, 3, 5, 8, 13, 21, 34, 55 } 发现斐波那契数列的两个相邻数 的比例，无限接近 黄金分割     值0.618
```

```java
斐波那契(黄金分割法)查找算法
斐波那契(黄金分割法)原理: 
   斐波那契查找原理与前两种相似，仅仅 改变了中间结点（mid）的位置，mid不 再是中间或插值得到，而是位于黄金分 割 点附近，即mid=low+F(k-1)-1 （F代表斐波那契数列），如下图所示对F(k-1)-1的理解： 
       (1) 由斐波那契数列 F[k]=F[k-1]+F[k-2] 的性质，可以得到 （F[k]-1）=（F[k-1]-1）+（F[k-2]-1）+1 。该            式说明：只要顺序表的长度为F[k]-1，则可以将该表分成长度为F[k-1]-1和F[k-2]-1 的两段，即如上图所示。            从而中间位置为mid=low+F(k-1)-1
       (2) 类似的，每一子段也可以用相同的方式分割 
       (3) 但顺序表长度n不一定刚好等于F[k]-1，所以需要将原来的顺序表长度n增加至F[k]-1。这里的k 值只要能使得              F[k]-1恰好大于或等于n即可，由以下代码得到,顺序表长度增加后，新增的位置（从n +1到F[k]-1位置），都赋            为n位置的值即可。
           while(n>fib(k)-1)
```

```java
package com.atguigu.search;

import java.util.Arrays;

public class FibonacciSearch {

	public static int maxSize = 20;
	public static void main(String[] args) {
		int [] arr = {1,8, 10, 89, 1000, 1234};
		
		System.out.println("index=" + fibSearch(arr, 1234));// 0
		
	}

	//因为后面我们mid=low+F(k-1)-1，需要使用到斐波那契数列，因此我们需要先获取到一个斐波那契数列
	//非递归方法得到一个斐波那契数列
	public static int[] fib() {
		int[] f = new int[maxSize];
		f[0] = 1;
		f[1] = 1;
		for (int i = 2; i < maxSize; i++) {
			f[i] = f[i - 1] + f[i - 2];
		}
		return f;
	}
	
	//编写斐波那契查找算法
	//使用非递归的方式编写算法
	/**
	 * 
	 * @param a  数组
	 * @param key 我们需要查找的关键码(值)
	 * @return 返回对应的下标，如果没有-1
	 */
	public static int fibSearch(int[] a, int key) {
		int low = 0;
		int high = a.length - 1;
		int k = 0; //表示斐波那契分割数值的下标
		int mid = 0; //存放mid值
		int f[] = fib(); //获取到斐波那契数列
		//获取到斐波那契分割数值的下标
		while(high > f[k] - 1) {
			k++;
		}
		//因为 f[k] 值 可能大于 a 的 长度，因此我们需要使用Arrays类，构造一个新的数组，并指向temp[]
		//不足的部分会使用0填充
		int[] temp = Arrays.copyOf(a, f[k]);
		//实际上需求使用a数组最后的数填充 temp
		//举例:
		//temp = {1,8, 10, 89, 1000, 1234, 0, 0}  => {1,8, 10, 89, 1000, 1234, 1234, 1234,}
		for(int i = high + 1; i < temp.length; i++) {
			temp[i] = a[high];
		}
		
		// 使用while来循环处理，找到我们的数 key
		while (low <= high) { // 只要这个条件满足，就可以找
			mid = low + f[k - 1] - 1;
			if(key < temp[mid]) { //我们应该继续向数组的前面查找(左边)
				high = mid - 1;
				//为甚是 k--
				//说明
				//1. 全部元素 = 前面的元素 + 后边元素
				//2. f[k] = f[k-1] + f[k-2]
				//因为 前面有 f[k-1]个元素,所以可以继续拆分 f[k-1] = f[k-2] + f[k-3]
				//即 在 f[k-1] 的前面继续查找 k--
				//即下次循环 mid = f[k-1-1]-1
				k--;
			} else if ( key > temp[mid]) { // 我们应该继续向数组的后面查找(右边)
				low = mid + 1;
				//为什么是k -=2
				//说明
				//1. 全部元素 = 前面的元素 + 后边元素
				//2. f[k] = f[k-1] + f[k-2]
				//3. 因为后面我们有f[k-2] 所以可以继续拆分 f[k-1] = f[k-3] + f[k-4]
				//4. 即在f[k-2] 的前面进行查找 k -=2
				//5. 即下次循环 mid = f[k - 1 - 2] - 1
				k -= 2;
			} else { //找到
				//需要确定，返回的是哪个下标
				if(mid <= high) {
					return mid;
				} else {
					return high;
				}
			}
		}
		return -1;
	}
}

```

## 15. 哈希表（数据结构）

```java
哈希表的基本介绍 散列表（Hash table，也叫哈希表），是根据关键码值( Key value)(如 id)而直接进行访问的数据结构 。也就是说，它通过把关键码值映射 到表中一个位置来访问记录，以加快 查找的速度。这个映射函数叫做 散列函数，存放记录的数组叫做 散列表。
```

![image-20200303200456356](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200303200456356.png)

插图的位置!!!!!!

```JAVA
package com.bytedance.hashtable;

import java.util.Scanner;

public class HashTable {

	public static void main(String[] args) {
		// 创建哈希表
		HashTab hashtable = new HashTab(7);
		// 测试
		String key = "";
		Scanner src = new Scanner(System.in);
		while (true) {
			System.out.println("add：添加雇员");
			System.out.println("list：显示雇员");
			System.out.println("find：查找雇员");
			System.out.println("exit：退出系统");
			key = src.next();
			switch (key) {
			case "add":
				System.out.println("输入id");
				int id = src.nextInt();
				System.out.println("输入名字");
				String name = src.next();
				// 创建雇员
				Emp emp = new Emp(id, name);
				hashtable.add(emp);
				break;
			case "list":
				hashtable.list();
				break;
			case "find":
				System.out.println("请输入要查找的id");
				id = src.nextInt();
				hashtable.findEmpById(id);
				break;
			case "exit":
				src.close();
				System.exit(0);
				break;
			default:
				break;
			}
		}
	}

}

//创建哈希表，管理多条来链表
class HashTab {
	private int size;// 共有几条链表
	// 数组里面存放的元素是链表
	private EmpLinkedList[] empLinkedListArray;

	// 构造器
	public HashTab(int size) {
		this.size = size;
		// 初始化
		empLinkedListArray = new EmpLinkedList[size];
		// 分别初始化每个链表!!!!!!!,细节，没有这步会空指针异常！！！！！
		for (int i = 0; i < size; i++) {
			empLinkedListArray[i] = new EmpLinkedList();
		}
	}

	// 添加雇员
	public void add(Emp emp) {
		// 根据id得到该员工应加入哪条链表
		int empLinkedListNo = hashFun(emp.id);
		// 将emp加入到对应链表的对应位置
		empLinkedListArray[empLinkedListNo].add(emp);
	}

	// 遍历所有链表即遍历哈希表
	public void list() {
		for (int i = 0; i < size; i++) {
			empLinkedListArray[i].list(i);
		}
	}

	// 根据输入的id查找雇员
	public void findEmpById(int id) {
		// 利用散列函数确定是那条链表
		int empLinkedListNo = hashFun(id);
		Emp emp = empLinkedListArray[empLinkedListNo].findEmpById(id);
		if (emp != null) {
			System.out.printf("在第%条链表中找到雇员id=%d\n", empLinkedListNo + 1, id);
		} else {
			System.out.println("哈希表中没有找到此雇员！");
		}
	}

	// 使用取模法编写散列表
	public int hashFun(int id) {
		return id % size;
	}
}

//创建雇员
class Emp {
	public int id;
	public String name;
	public Emp next;// 指向下一个雇员,默认为空

	public Emp(int id, String name) {
		this.id = id;
		this.name = name;
	}
}

//创建链表
class EmpLinkedList {
	// 头指针指向第一个雇员
	private Emp head;// 默认为空
	// 添加雇员到链表
	// 假定添加雇员时id是自增长的,id分配是从小到大,因此将雇员加到链表的最后

	public void add(Emp emp) {
		// 如果是添加当前链表的第一个雇员
		if (head == null) {// 表示添加到第一个
			head = emp;
			return;
		}
		// 如果不是添加第一个雇员，需指针定位到链表最后
		Emp curEmp = head;
		while (true) {
			if (curEmp.next == null) {// 说明到链表最后
				break;
			}
			curEmp = curEmp.next;// 后移，直到链表最后
		}
		// 退出时直接将emp加到链表最后
		curEmp.next = emp;
	}

	// 遍历链表雇员信息
	public void list(int no) {
		// 判断链表是否为空
		if (head == null) {
			System.out.println("第" + (no + 1) + "链表为空！");
			return;
		}
		System.out.print("第" + (no + 1) + "链表信息为：");
		Emp curEmp = head;// 辅助指针
		while (true) {
			System.out.printf("=> id=%d name=%s\t", curEmp.id, curEmp.name);
			if (curEmp.next == null) {// curEmp.next已经是最后节点
				break;
			}
			curEmp = curEmp.next;// 后移遍历
		}
		System.out.println();
	}

	// 根据雇员id找雇员就返回emp,没找到返回-1
	// 找到
	public Emp findEmpById(int id) {
		// 判断当前链表是否为空
		if (head == null) {
			System.out.println("链表为空，找不到呀！");
			return null;
		}
		// 辅助指针
		Emp curEmp = head;
		while (true) {
			if (curEmp.id == id) {// 代表找到
				break;
			}
			// 退出
			if (curEmp.next == null) {
				// 说明没找到
				curEmp = null;
				break;
			}
			curEmp = curEmp.next;// 继续判断下一个id
		}
		return curEmp;
	}
}
```

## 16. 树结构

```java
二叉树
(1) 数组存储方式的分析
优点：通过下标方式访问元素，速度快。对于有序数组 ，还可使用二分查找提高检索速度。 缺点：如果要检索具体某个值，或者插入值(按一定顺序)会整体移动，效率较低 [示意图 ]
```

![image-20200304123301230](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200304123301230.png)

```java
(2) 链式存储方式的分析 优点：在一定程度上对数组存储方式有优化(
比如：插入一个数值节点，只需要将插入节点，链接到链表中即可， 删除效率也很好)。
缺点：在进行检索时，效率仍然较低，比如(检索某个值，需要从头节点开始遍历) 
```

```java
(3) 树存储方式的分析 能提高数据存储，读取的效率, 比如利用 二叉排序树(Binary Sort Tree) ，既可以保证数据的检索速度，同时也可以保证数据的插入，删除，修改的速度。如下图：

(4)例如电脑中的文件夹结构就是树结构：主文件夹就是树结构的根，主文件夹中子文件夹就是树节点或者子树

```

<img src="C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200304131436030.png" alt="image-20200304131436030" style="zoom:80%;" />

## 17.二叉树的概念和常用术语

```java
1. 节点 2.根节点 3.父节点 4.子节点  5. 叶子节点 (没有子节点的节点,也是度为0的节点) 6.节点的权(节点值) 
7. 路径(从root节点找到该节点的路线) 8. 层 9.子树 10. 树的高度(最大层数) 11. 森林 :多颗子树构成森林
12.节点度(degree):子树的个数 如下图A节点的度就是2即B C两个子树 B节点的度也是2，而F的度为0没有子树
13.树的度:所有节点度中的最大值 如下图树的度就是2
14.非叶子节点:即度不为0的节点
15.节点的深度:从根节点到当前节点的唯一路径上的节点总数   如E节点的深度就是3 路径A->B->E 经过3个节点
16.节点的高度: 从当前节点到最远叶子节点的路径上的节点总数  如B节点的高度就是3即B->H,深度是2
17.树的深度:所有节点深度中的最大值 如下图树的深度就是4
18.往往树的高度等于树的深度(即也可理解为树的层数)
19.有序树:树中的任意节点的子节点之间有顺序关系(无序树则相反，也称自由树,二叉树是严格的有序树)
```

![image-20200304133410619](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\二叉树图.jpg)

```java
二叉树的概念
(1) 树有很多种，每个节点最多只能有两个子节点(即节点的度必须是小于等于2)的一种形式称为二叉树。
(2) 二叉树的子节点分为左节点和右节点。
(3) 如果该二叉树的所有叶子节点都在最后一层，并且结点总数= 2^n -1 , n 为层数,则我们称为满二叉树。
    满二叉树的节点的度都要么为0，要么为2，满叉树一定是真二叉树,真二叉树不一定是满二叉树
(4) 如果该二叉树的所有叶子节点都在最后一层或者倒数第二层，而且最后一层的叶子节点在左边连续，倒数第二层的叶子节     点在右边连续,称为完全二叉树。完全二叉树从上至下从左至右完全排满会得到一颗满二叉树,完全二叉树根节点到倒数第     二层是满二叉树,满二叉树也是一颗完全二叉树，而完全二叉树不一定是满二叉树.
    完全二叉树的性质:
    1.度为1的节点只有左子树             
    2.度为1的节点要么为1个,要么为0个
    3.同样节点数量的二叉树,完全二叉树的高度是最小的
    4.假设二叉树的高度为h(h>=1),则至少有2^h-1个节点,最多有(2^h)-1个节点(满二叉树)
    5.节点总数为n 得到以下结论
      2^h-1<=n<2^h
      h-1<=log2^n<h
      n一定是整数,但取对数后的结果不一定是整数,得到的可能为小数如得到的结果是 3.5 则要满足前面的不等式
      n只能取3,即2<=3<4,而不是n取4,否则不等式无法成立,这是计算机中的向下取整(floor)即砍掉小数部分,保留
      整数部分(向上取整为ceiling,即整数部分加1,若效数部分为0则直接保留整数部分即可)
      最终树的高度计算公式: h=(floor)log2^n+1
     6.一颗有n个节点的完全二叉树(n>0),从上到下,从左到有从1开始编号,对任意第i个节点
          如果 i=1,它是根节点
          如果 i>1 它的父节点编号为 floor(i/2)
          如果 2i<=n 它的左子节点编号为 2i
          如果 2i>n 它无左子节点
          如果 2i+1<=n 它的右子节点编号为 2i+1
          如果 2i+1>n 它无右子节点
       一颗有n个节点的完全二叉树(n>0),从上到下,从左到有从0开始编号,对任意第i个节点
          如果 i=0,它是根节点
          如果 i>0 它的父节点编号为 floor(i-1/2)
          如果 2i+1<=n-1 它的左子节点编号为 2i+1
          如果 2i+1>n 它无左子节点
          如果 2i+2<=n-1 它的右子节点编号为 2i+2
          
(5) 二叉树的左子树和右子树是有顺序的，如果左右子树调换了位置则是两颗不同的二叉树，需要严格遵守
(6) 即使某个节点只有一颗子树，也要严格区分左子树与右子树
(7) 非空二叉树的第i层最多有2^n-1个节点(n>=1)  n=1时结果等于1即是根节点,n=2时结果等于2即得到根节点的左右子节点
(8) 在高度为h的二叉树上最多有 (2^h)-1个节点(h>=1) 如下图满二叉树最多节点为 2^3-1=7 个节点
(9) 对于任意一颗非空二叉树,若叶子节点个数为 n0 ,度为2的节点个数为 n2,则有 n0=n2+1
    推导过程:
      假设度为1的节点个数为 n1,那么就有二叉树的节点总数为 n=n0+n1+n2
      从边的角度思考-度为0的节点没有边,度为1的节点有一条边,度为2的节点有2条边,故总变数 T=n1+2*n2
      从节点的角度思考-每个节点的顶部均有一条边,除了根节点,故总边数 T=n-1
      又因为 n=n0+n1+n2 所以有 T=n-1=n0+n1+n2-1 推出 T=n1+2*n2=n0+n1+n2-1 最后退出->n0=n2+1
(10) 真二叉树:所有节点的度要么为0,要么为2,没有度为1的节点
```

![image-20200304135401103](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\完全二叉树与满二叉树.jpg)

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\真二叉树.jpg)

**二叉树面试题**

```java
一颗完全二叉树一共有786个节点,问一共有多少个叶子节点?
   
解析: 假设叶子节点个数为 n0 ,度为1的节点个数为 n1,度为2的节点个数为 n2
     总节点个数 n=n0+n1+n2,同时 n0=1+n2,所以 n=2n0+n1-1 
     完全二叉树节点度为1的个数要么1个,要么0个 
     所以当 n1=1时,n=2n0,n必然是偶数 叶子节点个数n0=n/2 非叶子节点个数 n/2 
     当n1为0时 n=2n0-1,n必是奇数 叶子节点个数 n0=(n+1)/2,非叶子节点个数 n1+n2=(n-1)/2
      
     通用公式:n0=(floor)(n-1)>>1即(floor)(n-1)/2
     推到过程:假设无论 n 是奇数还是偶数都按照 n0=(n+1)/2 这个公式算,奇数本身就符合,但是偶数会多加了个1,算出来
     的结果是小数,计算机默认是向下取整，即还是算的是n/2,所以无论 n 是偶数还是奇数都只是一个公式即可运算
      
     非叶子节点个数通用公式:n1+n2=(floor)(n/2)
    
```



### 1.二叉树的前 ，中，后序遍历

```java
前序遍历: 先输出父节点，再遍历左子树和右子树 
中序遍历: 先遍历左子树，再输出父节点，再遍历右子树 
后序遍历: 先遍历左子树，再遍历右子树，最后输出父节点 
//小结: 看输出父节点的顺序，就确定是前序，中序还

思路图解如下图:
```

![image-20200306130905531](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200306130905531.png)

```java
前中后序遍历代码实现:

package com.bytedance.tree;
public class BinaryTreeDemo {

	public static void main(String[] args) {
		// 测试,先创建二叉树
		BinaryTree binaryThree = new BinaryTree();
		// 创建节点
		HeroNode root = new HeroNode(1, "乔丹");
		HeroNode node2 = new HeroNode(2, "科比");
		HeroNode node3 = new HeroNode(3, "詹姆斯");
		HeroNode node4 = new HeroNode(4, "罗斯");
		HeroNode node5 = new HeroNode(5, "韦德");
		// 根节点root，左节点第一个是node2,右节点第一个是node3,node3的右节点为node4
		root.setLeft(node2);
		root.setRight(node3);
		node3.setRight(node4);
		// 给节点3加一个左节点
		node3.setLeft(node5);
		// 将根节点加入到二叉树
		binaryThree.setRoot(root);
		// 测试前序遍历
		System.out.println("前序遍历的结果:");// 正常结果是1-2-3-5-4
		binaryThree.preOrder();
		// 测试中序遍历
		System.out.println("中序遍历的结果:");// 正常结果是2-1-5-3-4
		binaryThree.infixOrder();
		// 测试后序遍历
		System.out.println("后序遍历的结果:");// 正常结果是2-5-4-3-1
		binaryThree.postOrder();
	}

}

//编写二叉树
class BinaryTree {
	// 著重要的根节点
	private HeroNode root;

	public void setRoot(HeroNode root) {
		this.root = root;
	}

	// 二叉树调用遍历方法
	// 调用前序遍历
	public void preOrder() {
		if (this.root != null) {
			this.root.preOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}

	// 调用中序遍历方法
	public void infixOrder() {
		if (this.root != null) {
			this.root.infixOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}

	// 调用后序遍历方法
	public void postOrder() {
		if (this.root != null) {
			this.root.postOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}
}

//创建节点
class HeroNode {
	private int no;// 编号
	private String name;// 名字
	private HeroNode left;// 指向二叉树左边的索引，默认为空
	private HeroNode right;// 指向二叉树右边的索引，默认为空

	public HeroNode(int no, String name) {
		this.no = no;
		this.name = name;
	}

	public int getNo() {
		return no;
	}

	public void setNo(int no) {
		this.no = no;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public HeroNode getLeft() {
		return left;
	}

	public void setLeft(HeroNode left) {
		this.left = left;
	}

	public HeroNode getRight() {
		return right;
	}

	public void setRight(HeroNode right) {
		this.right = right;
	}

	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + "]";
	}

	// 节点的前序遍历方法
	public void preOrder() {
		System.out.println(this);// 输出父节点
		// 递归左子树前序遍历
		if (this.left != null) {
			this.left.preOrder();
		}
		// 递归向右子树遍历
		if (this.right != null) {
			this.right.preOrder();
		}
	}

	// 中序遍历
	public void infixOrder() {
		// 递归左子树中序遍历
		if (this.left != null) {
			this.left.infixOrder();
		}
		System.out.println(this);
		// 递归向右子树遍历
		if (this.right != null) {
			this.right.infixOrder();
		}
	}

	// 后序遍历
	public void postOrder() {
		if (this.left != null) {
			this.left.postOrder();
		}
		if (this.right != null) {
			this.right.postOrder();
		}
		System.out.println(this);
	}
}
```

### 2.二叉树的前中后序查找

```java
二叉树-查找指定节点 要求: 
(1) 请编写前序查找，中序查找和后序查找的方法。 
(2) 并分别使用三种查找方式，查找 heroNO = 5 的节点 
(3) 并分析各种查找方式，分别比较了多少次
```

![image-20200306143836563](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200306143836563.png)

```java
前中后序查找代码实现:

package com.bytedance.tree;
public class BinaryTreeDemo {

	public static void main(String[] args) {
		// 测试,先创建二叉树
		BinaryTree binaryThree = new BinaryTree();
		// 创建节点
		HeroNode root = new HeroNode(1, "乔丹");
		HeroNode node2 = new HeroNode(2, "科比");
		HeroNode node3 = new HeroNode(3, "詹姆斯");
		HeroNode node4 = new HeroNode(4, "罗斯");
		HeroNode node5 = new HeroNode(5, "韦德");
		// 根节点root，左节点第一个是node2,右节点第一个是node3,node3的右节点为node4
		root.setLeft(node2);
		root.setRight(node3);
		node3.setRight(node4);
		// 给节点3加一个左节点
		node3.setLeft(node5);
		// 将根节点加入到二叉树
		binaryThree.setRoot(root);
		// 测试前序查找
		System.out.println("前序查找结果：");
		HeroNode resNode = binaryThree.perOrderSearch(5);
		if (resNode != null) {
			System.out.printf("信息为 no=%d name=%s", resNode.getNo(), resNode.getName());
		} else {
			System.out.printf("没有找到no=%d的成员", 5);
		}
		// 测试中序查找
		System.out.println("中序查找结果：");
		HeroNode resNode1 = binaryThree.perOrderSearch(5);
		if (resNode != null) {
			System.out.printf("信息为 no=%d name=%s", resNode.getNo(), resNode.getName());
		} else {
			System.out.printf("没有找到no=%d的成员", 5);
		}
	}

}

//编写二叉树
class BinaryTree {
	// 最重要的根节点
	private HeroNode root;

	public void setRoot(HeroNode root) {
		this.root = root;
	}

	// 二叉树调用遍历方法
	// 调用前序遍历
	public void preOrder() {
		if (this.root != null) {
			this.root.preOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}

	// 调用中序遍历方法
	public void infixOrder() {
		if (this.root != null) {
			this.root.infixOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}

	// 调用后序遍历方法
	public void postOrder() {
		if (this.root != null) {
			this.root.postOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}

	// 调用前序查找
	public HeroNode perOrderSearch(int no) {
		if (root != null) {
			return root.perOrderSearch(no);
		} else {
			return null;
		}
	}

	// 调用中序查找
	public HeroNode infixOrdereSearch(int no) {
		if (root != null) {
			return root.infixOrdereSearch(no);
		} else {
			return null;
		}
	}

	// 调用后续查找
	public HeroNode postOrderSearch(int no) {
		if (root != null) {
			return root.postOrderSearch(no);
		} else {
			return null;
		}
	}
}

//创建节点
class HeroNode {
	private int no;// 编号
	private String name;// 名字
	private HeroNode left;// 指向二叉树左边的索引，默认为空
	private HeroNode right;// 指向二叉树右边的索引，默认为空

	public HeroNode(int no, String name) {
		this.no = no;
		this.name = name;
	}

	public int getNo() {
		return no;
	}

	public void setNo(int no) {
		this.no = no;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public HeroNode getLeft() {
		return left;
	}

	public void setLeft(HeroNode left) {
		this.left = left;
	}

	public HeroNode getRight() {
		return right;
	}

	public void setRight(HeroNode right) {
		this.right = right;
	}

	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + "]";
	}

	// 节点的前序遍历方法
	public void preOrder() {
		System.out.println(this);// 输出父节点
		// 递归左子树前序遍历
		if (this.left != null) {
			this.left.preOrder();
		}
		// 递归向右子树遍历
		if (this.right != null) {
			this.right.preOrder();
		}
	}

	// 中序遍历
	public void infixOrder() {
		// 递归左子树中序遍历
		if (this.left != null) {
			this.left.infixOrder();
		}
		System.out.println(this);
		// 递归向右子树遍历
		if (this.right != null) {
			this.right.infixOrder();
		}
	}

	// 后序遍历
	public void postOrder() {
		if (this.left != null) {
			this.left.postOrder();
		}
		if (this.right != null) {
			this.right.postOrder();
		}
		System.out.println(this);
	}

	// 前序遍历查找:找到就返回
	public HeroNode perOrderSearch(int no) {
		// 比较当前节点是否为待查找的节点
		if (this.no == no) {
			return this;
		}
		// 不是，就判断当前节点的左子节点是否为空
		// 左递归前序查找，找到节点就返回
		HeroNode resNode = null;// 用于存储结果节点
		if (this.left != null) {
			// 不为空继续向左进行前序查找
			resNode = this.left.perOrderSearch(no);
		}
		// 说明在左子树找到了
		if (resNode != null) {
			return resNode;
		}
		// 判断当前节点的右子节点是否为空,不为空就继续向右递归前序查找
		if (this.right != null) {
			resNode = this.right.perOrderSearch(no);
		}
		return resNode;
	}

	// 中序遍历查找
	public HeroNode infixOrdereSearch(int no) {
		// 先判断当前节点的左子节点
		HeroNode resNode = null;
		if (this.left != null) {
			resNode = this.left.infixOrdereSearch(no);
		}
		// 如果resNode不为空就返回，但是虽然返回但也有可能没有找到
		if (resNode != null) {
			return resNode;
		}
		// 和当前节点比较，如果是就返回
		if (this.no == no) {
			return this;
		}
		// 向右查找.找到了就返回，没找到就返回null
		if (this.right != null) {
			resNode = this.right.infixOrdereSearch(no);
		}
		return resNode;
	}

	// 后序遍历查找
	public HeroNode postOrderSearch(int no) {
		// 判断当前节点的左子节点是否为空,不为空则向左递归查找
		HeroNode resNode = null;
		if (this.left != null) {
			resNode = this.left.postOrderSearch(no);
		}
		// 说明在左子树
		if (resNode != null) {
			return resNode;
		}
		// 左子树买找到，则向右子树递归查找,需判断右子节点是否为空
		if (this.right != null) {
			resNode = this.right.postOrderSearch(no);
		}
		if (resNode != null) {
			return resNode;
		}
		// 左右子树都没有找到，就比较当前节点是不是
		if (this.no == no) {
			return this;
		}
		return resNode;
	}
}
```

### 3. 二叉树删除节点

```java
二叉树-删除节点 要求 
(1) 如果删除的节点是叶子节点，则删除该节点 
(2) 如果删除的节点是非叶子节点，则删除该子树. 
(3) 测试，删除掉 5号叶子节点 和 3号子树.
```

![image-20200306210629820](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200306210629820.png)

```java
删除节点代码实现:

package com.bytedance.tree;
public class BinaryTreeDemo {

	public static void main(String[] args) {
		// 测试,先创建二叉树
		BinaryTree binaryThree = new BinaryTree();
		// 创建节点
		HeroNode root = new HeroNode(1, "乔丹");
		HeroNode node2 = new HeroNode(2, "科比");
		HeroNode node3 = new HeroNode(3, "詹姆斯");
		HeroNode node4 = new HeroNode(4, "罗斯");
		HeroNode node5 = new HeroNode(5, "韦德");
		// 根节点root，左节点第一个是node2,右节点第一个是node3,node3的右节点为node4
		root.setLeft(node2);
		root.setRight(node3);
		node3.setRight(node4);
		// 给节点3加一个左节点
		node3.setLeft(node5);
		// 将根节点加入到二叉树
		binaryThree.setRoot(root);
		// 测试删除节点
		System.out.println("删除前前序遍历结果:");
		binaryThree.preOrder();
		System.out.println("删除后序遍历结果:");
		binaryThree.delNode(5);
		binaryThree.preOrder();
	}

}

//编写二叉树
class BinaryTree {
	// 最重要的根节点
	private HeroNode root;

	public void setRoot(HeroNode root) {
		this.root = root;
	}

	// 删除节点
	public void delNode(int no) {
		// 如果数只有一个根节点root没有其他节点,需立即判断root是否是待删除结点
		if (root != null) {
			if (root.getNo() == no) {
				root = null;
			} else {
				// 递归删除
				root.delNode(no);
			}
		} else {
			System.out.println("次数为空，不能执行删除操作！");
		}
	}

	// 二叉树调用遍历方法
	// 调用前序遍历
	public void preOrder() {
		if (this.root != null) {
			this.root.preOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}

	// 调用中序遍历方法
	public void infixOrder() {
		if (this.root != null) {
			this.root.infixOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}

	// 调用后序遍历方法
	public void postOrder() {
		if (this.root != null) {
			this.root.postOrder();
		} else {
			System.out.println("当前二叉树为空，无法遍历！");
		}
	}

	// 调用前序查找
	public HeroNode perOrderSearch(int no) {
		if (root != null) {
			return root.perOrderSearch(no);
		} else {
			return null;
		}
	}

	// 调用中序查找
	public HeroNode infixOrdereSearch(int no) {
		if (root != null) {
			return root.infixOrdereSearch(no);
		} else {
			return null;
		}
	}

	// 调用后续查找
	public HeroNode postOrderSearch(int no) {
		if (root != null) {
			return root.postOrderSearch(no);
		} else {
			return null;
		}
	}
}

//创建节点
class HeroNode {
	private int no;// 编号
	private String name;// 名字
	private HeroNode left;// 指向二叉树左边的索引，默认为空
	private HeroNode right;// 指向二叉树右边的索引，默认为空

	public HeroNode(int no, String name) {
		this.no = no;
		this.name = name;
	}

	public int getNo() {
		return no;
	}

	public void setNo(int no) {
		this.no = no;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public HeroNode getLeft() {
		return left;
	}

	public void setLeft(HeroNode left) {
		this.left = left;
	}

	public HeroNode getRight() {
		return right;
	}

	public void setRight(HeroNode right) {
		this.right = right;
	}

	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + "]";
	}

	// 递归删除节点
	public void delNode(int no) {
		// 若当前节点的左子节点不为空，并且左子节点就是待删除节点，只需将当前节点左节点置空即可即 this.left=null;并且返回结束递归
		if (this.left != null && this.left.no == no) {
			this.left = null;
			return;
		}
		// 若当前节点的右子节点不为空，并且右子节点就是要删除的节点，只需将当前节点右节点置空即可即 this.right=null;并且返回结束递归
		if (this.right != null && this.right.no == no) {
			this.right = null;
			return;
		}
		// 继续向左子树进行以上递归删除
		if (this.left != null) {
			this.left.delNode(no);
		}
		// 继续向右子树进行以上递归删除
		if (this.right != null) {
			this.right.delNode(no);
		}
	}

	// 节点的前序遍历方法
	public void preOrder() {
		System.out.println(this);// 输出父节点
		// 递归左子树前序遍历
		if (this.left != null) {
			this.left.preOrder();
		}
		// 递归向右子树遍历
		if (this.right != null) {
			this.right.preOrder();
		}
	}

	// 中序遍历
	public void infixOrder() {
		// 递归左子树中序遍历
		if (this.left != null) {
			this.left.infixOrder();
		}
		System.out.println(this);
		// 递归向右子树遍历
		if (this.right != null) {
			this.right.infixOrder();
		}
	}

	// 后序遍历
	public void postOrder() {
		if (this.left != null) {
			this.left.postOrder();
		}
		if (this.right != null) {
			this.right.postOrder();
		}
		System.out.println(this);
	}

	// 前序遍历查找:找到就返回
	public HeroNode perOrderSearch(int no) {
		// 比较当前节点是否为待查找的节点
		if (this.no == no) {
			return this;
		}
		// 不是，就判断当前节点的左子节点是否为空
		// 左递归前序查找，找到节点就返回
		HeroNode resNode = null;// 用于存储结果节点
		if (this.left != null) {
			// 不为空继续向左进行前序查找
			resNode = this.left.perOrderSearch(no);
		}
		// 说明在左子树找到了
		if (resNode != null) {
			return resNode;
		}
		// 判断当前节点的右子节点是否为空,不为空就继续向右递归前序查找
		if (this.right != null) {
			resNode = this.right.perOrderSearch(no);
		}
		return resNode;
	}

	// 中序遍历查找
	public HeroNode infixOrdereSearch(int no) {
		// 先判断当前节点的左子节点
		HeroNode resNode = null;
		if (this.left != null) {
			resNode = this.left.infixOrdereSearch(no);
		}
		// 如果resNode不为空就返回，但是虽然返回但也有可能没有找到
		if (resNode != null) {
			return resNode;
		}
		// 和当前节点比较，如果是就返回
		if (this.no == no) {
			return this;
		}
		// 向右查找.找到了就返回，没找到就返回null
		if (this.right != null) {
			resNode = this.right.infixOrdereSearch(no);
		}
		return resNode;
	}

	// 后序遍历查找
	public HeroNode postOrderSearch(int no) {
		// 判断当前节点的左子节点是否为空,不为空则向左递归查找
		HeroNode resNode = null;
		if (this.left != null) {
			resNode = this.left.postOrderSearch(no);
		}
		// 说明在左子树
		if (resNode != null) {
			return resNode;
		}
		// 左子树买找到，则向右子树递归查找,需判断右子节点是否为空
		if (this.right != null) {
			resNode = this.right.postOrderSearch(no);
		}
		if (resNode != null) {
			return resNode;
		}
		// 左右子树都没有找到，就比较当前节点是不是
		if (this.no == no) {
			return this;
		}
		return resNode;
	}
}
```

### 4. 层序遍历

```java
1.需要计算二叉树高度或判断是否为完全二叉树时，可使用层序遍历。
2.访问顺序：从上到下，从左到右依次访问节点。
 遍历思路：
 0.使用队列存储节点。
 1.将根节点入队列。
 2.将队列头节点A出队列，进行访问。
 3.将节点A的左子节点入队列。
 4.将节点A的右子节点入队列。
 5.循环执行2-4步骤。
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\层序遍历二叉树.jpg)

```java

```



## 18. 顺序存储二叉树

```java
基本说明:
从数据存储来看，数组存储方式和树 的存储方式可以相互转换，即数组可 以转换成树，树也可以转换成数组， 如图1-1。
要求: 
(1) 1-1图的二叉树的结点，要求以数组 的方式来存放 arry : [1, 2, 3, 4, 5, 6, 7]
(2) 要求在遍历数组 arry时，仍然可以以 前序遍历，中序遍历和后序遍历的 方式完成结点的遍历
```

![image-20200306213051157](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200306213051157.png)

```java
顺序存储二叉树的特点:
(1) 顺序二叉树通常只考虑完全二叉树 
(2) 第n个元素的左子节点为 2 * n + 1 //得到的结果是对应数组的下标，如求第一个元素结果就是2*1+1=3，对应结果为4
(3) 第n个元素的右子节点为 2 * n + 2 //得到的结果是对应数组的下标
(4) 第n个元素的父节点为 (n-1) / 2//得到的结果是对应数组的下标
(5) n : 表示二叉树中的第几个元素(按0开始编号 如图1-1所示)//和数组保持一致
```

```java
顺序存储二叉树代码实现:

package com.bytedance.tree;
//以数组的方式存储二叉树
public class ArrayBinaryTreeDemo {

	public static void main(String[] args) {

		int[] array = { 1, 2, 3, 4, 5, 6, 7 };
		// 创建ArrayBinaryTree 对象
		ArrayBinaryTree arrayBinaryTree = new ArrayBinaryTree(array);
		arrayBinaryTree.perOrder();// 1-2-4-5-3-6-7
	}

}

//编写ArrayBinaryTree，实现顺序存储二叉树遍历
class ArrayBinaryTree {
	// 存储二叉树节点的数组
	private int[] array;

	public ArrayBinaryTree(int[] array) {
		this.array = array;
	}

	// 进行 perOrder(int index)的方法重载
	public void perOrder() {
		this.perOrder(0);
	}

	// 编写完成顺序存储二叉树的前序遍历方法
	public void perOrder(int index) {// index数组的下标
		// 如果数组为空或者array.length
		if (array == null || array.length == 0) {
			System.out.println("数组为空,无法进行二叉树的前序遍历");
		}
		// 否则就输出当前元素
		System.out.println(array[index]);
		// 向左递归遍历
		if ((index * 2 + 1) < array.length) {// 防止下标越界
			perOrder(index * 2 + 1);
		}
		// 向右递归遍历
		if ((index * 2 + 2) < array.length) {
			perOrder(index * 2 + 2);
		}
	}
}
```

### 1. 线索化二叉树

![image-20200307151442067](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200307151442067.png)

```java
线索二叉树基本介绍
(1) n个结点的二叉链表中含有n+1 【公式 2n-(n-1)=n+1】 个空指针域。利用二叉链表中的空指针域，存放指向该结点在       某种遍历次序下的前驱和后继结点的指针（这种附加的指针称为"线索"）
(2) 这种加上了线索的二叉链表称为线索链表，相应的二叉树称为线索二叉树( Threaded BinaryTree)。根据线索性质的不     同，线索二叉树可分为 前序线索二叉树、中序线索二叉树和后序线索二叉树三种
(3) 一个结点的前一个结点，称为前驱结点
(4) 一个结点的后一个结点，称为后继结点
```

![image-20200307153642479](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200307153642479.png)

```java
说明:当线索化二叉树后，Node节点的 属性 left 和 right ，有如下情况: 
(1) left 指向的是左子树，也可能是指向的前驱节点. 比如 ① 节点 left 指向的左子树, 而 ⑩ 节点的 left 指向的就是     前驱节点.
(2) right指向的是右子树，也可能是指向后继节点，比如 ① 节点right 指向的是右子树，而⑩ 节点的 right 指向的是后继     节点.
```

```java
线索化二叉树代码实现:

package com.bytedance.tree;
import java.util.concurrent.SynchronousQueue;

public class ThreadedBinaryTreeDemo {

	public static void main(String[] args) {
		// 测试一把中序线索二叉树的功能
		HeroNode root = new HeroNode(1, "小6");
		HeroNode node2 = new HeroNode(3, "小7");
		HeroNode node3 = new HeroNode(6, "小8");
		HeroNode node4 = new HeroNode(8, "小9");
		HeroNode node5 = new HeroNode(10, "菜鸡");
		HeroNode node6 = new HeroNode(14, "King");

		// 二叉树，后面我们要递归创建, 现在简单处理使用手动创建
		root.setLeft(node2);
		root.setRight(node3);
		node2.setLeft(node4);
		node2.setRight(node5);
		node3.setLeft(node6);

		// 测试中序线索化
		ThreadedBinaryTree threadedBinaryTree = new ThreadedBinaryTree();
		threadedBinaryTree.setRoot(root);
		threadedBinaryTree.threadedNodes();

		// 测试: 以10号节点测试
		HeroNode leftNode = node5.getLeft();
		HeroNode rightNode = node5.getRight();
		System.out.println("10号结点的前驱结点是 =" + leftNode); // 3
		System.out.println("10号结点的后继结点是=" + rightNode); // 1

		// 当线索化二叉树后，能在使用原来的遍历方法
		// threadedBinaryTree.infixOrder();
		System.out.println("使用线索化的方式遍历 线索化二叉树:");
		threadedBinaryTree.threadedList(); // 8, 3, 10, 1, 14, 6

	}

}

//定义ThreadedBinaryTree 实现了线索化功能的二叉树
class ThreadedBinaryTree {
	private HeroNode root;

	// 为了实现线索化，需要创建要给指向当前结点的前驱结点的指针
	// 在递归进行线索化时，pre 总是保留前一个结点
	private HeroNode pre = null;

	public void setRoot(HeroNode root) {
		this.root = root;
	}

	// 重载一把threadedNodes方法
	public void threadedNodes() {
		this.threadedNodes(root);
	}

	// 遍历线索化二叉树的方法
	public void threadedList() {
		// 定义一个变量，存储当前遍历的结点，从root开始
		HeroNode node = root;
		while (node != null) {
			// 循环的找到leftType == 1的结点，第一个找到就是8结点
			// 后面随着遍历而变化,因为当leftType==1时，说明该结点是按照线索化
			// 处理后的有效结点
			while (node.getLeftType() == 0) {
				node = node.getLeft();
			}

			// 打印当前这个结点
			System.out.println(node);
			// 如果当前结点的右指针指向的是后继结点,就一直输出
			while (node.getRightType() == 1) {
				// 获取到当前结点的后继结点
				node = node.getRight();
				System.out.println(node);
			}
			// 替换这个遍历的结点
			node = node.getRight();

		}
	}

	// 编写对二叉树进行中序线索化的方法
	/**
	 * 
	 * @param node 就是当前需要线索化的结点
	 */
	public void threadedNodes(HeroNode node) {

		// 如果node==null, 不能线索化
		if (node == null) {
			return;
		}

		// (一)先线索化左子树
		threadedNodes(node.getLeft());
		// (二)线索化当前结点[有难度]
		// 处理当前结点的前驱结点
		// 以8结点来理解
		// 8结点的.left = null , 8结点的.leftType = 1
		if (node.getLeft() == null) {
			// 让当前结点的左指针指向前驱结点
			node.setLeft(pre);
			// 修改当前结点的左指针的类型,指向前驱结点
			node.setLeftType(1);
		}

		// 处理后继结点
		if (pre != null && pre.getRight() == null) {
			// 让前驱结点的右指针指向当前结点
			pre.setRight(node);
			// 修改前驱结点的右指针类型
			pre.setRightType(1);
		}
		// !!! 每处理一个结点后，让当前结点是下一个结点的前驱结点
		pre = node;

		// (三)在线索化右子树
		threadedNodes(node.getRight());

	}

	// 删除结点
	public void delNode(int no) {
		if (root != null) {
			// 如果只有一个root结点, 这里立即判断root是不是就是要删除结点
			if (root.getNo() == no) {
				root = null;
			} else {
				// 递归删除
				root.delNode(no);
			}
		} else {
			System.out.println("空树，不能删除~");
		}
	}

	// 前序遍历
	public void preOrder() {
		if (this.root != null) {
			this.root.preOrder();
		} else {
			System.out.println("二叉树为空，无法遍历");
		}
	}

	// 中序遍历
	public void infixOrder() {
		if (this.root != null) {
			this.root.infixOrder();
		} else {
			System.out.println("二叉树为空，无法遍历");
		}
	}

	// 后序遍历
	public void postOrder() {
		if (this.root != null) {
			this.root.postOrder();
		} else {
			System.out.println("二叉树为空，无法遍历");
		}
	}

	// 前序遍历
	public HeroNode preOrderSearch(int no) {
		if (root != null) {
			return root.preOrderSearch(no);
		} else {
			return null;
		}
	}

	// 中序遍历
	public HeroNode infixOrderSearch(int no) {
		if (root != null) {
			return root.infixOrderSearch(no);
		} else {
			return null;
		}
	}

	// 后序遍历
	public HeroNode postOrderSearch(int no) {
		if (root != null) {
			return this.root.postOrderSearch(no);
		} else {
			return null;
		}
	}
}

//先创建HeroNode 结点
class HeroNode {
	private int no;
	private String name;
	private HeroNode left; // 默认null
	private HeroNode right; // 默认null
	// 说明
	// 1. 如果leftType == 0 表示指向的是左子树, 如果 1 则表示指向前驱结点
	// 2. 如果rightType == 0 表示指向是右子树, 如果 1表示指向后继结点
	private int leftType;
	private int rightType;

	public int getLeftType() {
		return leftType;
	}

	public void setLeftType(int leftType) {
		this.leftType = leftType;
	}

	public int getRightType() {
		return rightType;
	}

	public void setRightType(int rightType) {
		this.rightType = rightType;
	}

	public HeroNode(int no, String name) {
		this.no = no;
		this.name = name;
	}

	public int getNo() {
		return no;
	}

	public void setNo(int no) {
		this.no = no;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public HeroNode getLeft() {
		return left;
	}

	public void setLeft(HeroNode left) {
		this.left = left;
	}

	public HeroNode getRight() {
		return right;
	}

	public void setRight(HeroNode right) {
		this.right = right;
	}

	@Override
	public String toString() {
		return "HeroNode [no=" + no + ", name=" + name + "]";
	}

	// 递归删除结点
	// 1.如果删除的节点是叶子节点，则删除该节点
	// 2.如果删除的节点是非叶子节点，则删除该子树
	public void delNode(int no) {

		// 思路
		/*
		 * 1. 因为我们的二叉树是单向的，所以我们是判断当前结点的子结点是否需要删除结点，而不能去判断当前这个结点是不是需要删除结点. 2.
		 * 如果当前结点的左子结点不为空，并且左子结点 就是要删除结点，就将this.left = null; 并且就返回(结束递归删除) 3.
		 * 如果当前结点的右子结点不为空，并且右子结点 就是要删除结点，就将this.right= null ;并且就返回(结束递归删除) 4.
		 * 如果第2和第3步没有删除结点，那么我们就需要向左子树进行递归删除 5. 如果第4步也没有删除结点，则应当向右子树进行递归删除.
		 * 
		 */
		// 2. 如果当前结点的左子结点不为空，并且左子结点 就是要删除结点，就将this.left = null; 并且就返回(结束递归删除)
		if (this.left != null && this.left.no == no) {
			this.left = null;
			return;
		}
		// 3.如果当前结点的右子结点不为空，并且右子结点 就是要删除结点，就将this.right= null ;并且就返回(结束递归删除)
		if (this.right != null && this.right.no == no) {
			this.right = null;
			return;
		}
		// 4.我们就需要向左子树进行递归删除
		if (this.left != null) {
			this.left.delNode(no);
		}
		// 5.则应当向右子树进行递归删除
		if (this.right != null) {
			this.right.delNode(no);
		}
	}

	// 编写前序遍历的方法
	public void preOrder() {
		System.out.println(this); // 先输出父结点
		// 递归向左子树前序遍历
		if (this.left != null) {
			this.left.preOrder();
		}
		// 递归向右子树前序遍历
		if (this.right != null) {
			this.right.preOrder();
		}
	}

	// 中序遍历
	public void infixOrder() {

		// 递归向左子树中序遍历
		if (this.left != null) {
			this.left.infixOrder();
		}
		// 输出父结点
		System.out.println(this);
		// 递归向右子树中序遍历
		if (this.right != null) {
			this.right.infixOrder();
		}
	}

	// 后序遍历
	public void postOrder() {
		if (this.left != null) {
			this.left.postOrder();
		}
		if (this.right != null) {
			this.right.postOrder();
		}
		System.out.println(this);
	}

	// 前序遍历查找
	/**
	 * 
	 * @param no 查找no
	 * @return 如果找到就返回该Node ,如果没有找到返回 null
	 */
	public HeroNode preOrderSearch(int no) {
		System.out.println("进入前序遍历");
		// 比较当前结点是不是
		if (this.no == no) {
			return this;
		}
		// 1.则判断当前结点的左子节点是否为空，如果不为空，则递归前序查找
		// 2.如果左递归前序查找，找到结点，则返回
		HeroNode resNode = null;
		if (this.left != null) {
			resNode = this.left.preOrderSearch(no);
		}
		if (resNode != null) {// 说明我们左子树找到
			return resNode;
		}
		// 1.左递归前序查找，找到结点，则返回，否继续判断，
		// 2.当前的结点的右子节点是否为空，如果不空，则继续向右递归前序查找
		if (this.right != null) {
			resNode = this.right.preOrderSearch(no);
		}
		return resNode;
	}

	// 中序遍历查找
	public HeroNode infixOrderSearch(int no) {
		// 判断当前结点的左子节点是否为空，如果不为空，则递归中序查找
		HeroNode resNode = null;
		if (this.left != null) {
			resNode = this.left.infixOrderSearch(no);
		}
		if (resNode != null) {
			return resNode;
		}
		System.out.println("进入中序查找");
		// 如果找到，则返回，如果没有找到，就和当前结点比较，如果是则返回当前结点
		if (this.no == no) {
			return this;
		}
		// 否则继续进行右递归的中序查找
		if (this.right != null) {
			resNode = this.right.infixOrderSearch(no);
		}
		return resNode;

	}

	// 后序遍历查找
	public HeroNode postOrderSearch(int no) {

		// 判断当前结点的左子节点是否为空，如果不为空，则递归后序查找
		HeroNode resNode = null;
		if (this.left != null) {
			resNode = this.left.postOrderSearch(no);
		}
		if (resNode != null) {// 说明在左子树找到
			return resNode;
		}

		// 如果左子树没有找到，则向右子树递归进行后序遍历查找
		if (this.right != null) {
			resNode = this.right.postOrderSearch(no);
		}
		if (resNode != null) {
			return resNode;
		}
		System.out.println("进入后序查找");
		// 如果左右子树都没有找到，就比较当前结点是不是
		if (this.no == no) {
			return this;
		}
		return resNode;
	}

}
```

## 19. 堆排序

```java
(1) 堆排序是利用堆这种数据结构而设计的一种排序算法，堆排序是一种选择排序，它的最坏，最好，平均时间复 杂度均为       O(nlogn)，它也是不稳定排序。
(2) 堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆, 注意 : 没有 要求结点     的左孩子的值和右孩子的值的大小关系。
(3) 每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆
```

![image-20200308124729107](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308124729107.png)

![image-20200308125427662](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308125427662.png)

```java
堆排序的思想:

 (1) 将待排序序列构造成一个大顶堆 
 (2) 此时，整个序列的最大值就是堆顶的根节点。 
 (3) 将其与末尾元素进行交换，此时末尾就为最大值。 
 (4) 然后将剩余 n-1 个元素重新构造成一个堆，这样会得到 n 个元素的次小值。如此反复执行，便能得到一个有序 序列       
```

![image-20200308153534451](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308153534451.png)

![image-20200308154035354](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308154035354.png)

![image-20200308154317342](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308154317342.png)

![image-20200308154818311](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308154818311.png)

![image-20200308155220120](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308155220120.png)

![image-20200308155550314](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308155550314.png)

![image-20200308155906067](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308155906067.png)

```java
简单总结堆排序的基本思路： 
 (1).将无序序列构建成一个堆，根据升序降序需求选择大顶堆或小顶堆; 
 (2).将堆顶元素与末尾元素交换，将最大元素"沉"到数组末端; 
 (3).重新调整结构，使其满足堆定义，然后继续交换堆顶元素与当前末尾元素，反复执行调整+交换步骤，
        直到整个序列有序
```

![image-20200308145610860](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308145610860.png)

```java
堆排序代码实现:
//运行效率测试特别快，时间复杂度为线性对数阶: n(logn)
package com.bytedance.tree;

import java.util.Arrays;
public class HeapSort {

	public static void main(String[] args) {
		// 数组进行升序排序
		int[] array = { 4, 6, 8, 5, 9 };
		heapSort(array);// 最终数组为=[4, 5, 6, 8, 9](猜测正确)
	}

	// 定义堆排序的方法
	public static void heapSort(int[] array) {
		int temp = 0;
		System.out.println("堆排序!");
		// 分步完成
//		adjustHeap(array, 1, array.length);
//		System.out.println("第一次" + Arrays.toString(array));// 正常结果为4-9-8-5-6
//		adjustHeap(array, 0, array.length);
//		System.out.println("第二次" + Arrays.toString(array));// 正常结果为9-6-8-5-4
		// 一次性完成最终代码
		// 将无序序列构成一个堆，根据大顶堆规则
		for (int i = array.length / 2 - 1; i >= 0; i--) {
			adjustHeap(array, i, array.length);
		}
		//
		for (int j = array.length - 1; j > 0; j--) {
			// 交换动作
			temp = array[j];
			array[j] = array[0];
			array[0] = temp;
			adjustHeap(array, 0, j);
		}
		System.out.println("最终数组为=" + Arrays.toString(array));// 猜测结果：4-5-6-8-9
	}

	// 将数组(二叉树)调整为一个大顶堆
	/*
	 * 此方法的功能: 将以i对应的非叶子节点调整为大顶堆 array 待调整的数组 i 非叶子节点在数组中的索引 length
	 * 表示对多少个元素进行调整，因为在逐步调整过程中length是减少的
	 */
	public static void adjustHeap(int array[], int i, int length) {
		// 先取出当前元素的值保存起来
		int temp = array[i];
		/*
		 * k是i的左子节点,k+1是i的右子节点
		 */
		for (int k = 2 * i + 1; k < length; k = 2 * k + 1) {
			if (k + 1 < length && array[k] < array[k + 1]) {// 左节点的值小于右子节点的值
				k++;// if里的条件满足后，使k指向i的右子节点
			}
			if (array[k] > temp) {
				array[i] = array[k];
				i = k;// 让找到的这个k代替i的身份(非叶子节点)，继续循环操作
			} else {
				break;
			}
		}
		// 当以上for循环结束时已经完成了将以i为父节点的这棵树的最大值放在了以i为父节点的这棵树的堆顶(这棵树是局部的)
		// 将temp的值放到调整后的位置
		array[i] = temp;
	}
}
```

## 20. 赫夫曼树

### 1. 赫夫曼树的图解以及创建

```java
基本介绍
(1) 给定 n 个权值作为 n 个叶子结点，构造一棵二叉树，若该树的带权路径长度(wpl)达到最小，称这样的二叉树为 最优二     叉树，也称为哈夫曼树(Huffman Tree), 还有的书翻译为霍夫曼树。 
(2) 赫夫曼树是带权路径长度最短的树，权值较大的结点离根较近
    
赫夫曼树几个重要概念和举例说明 
(1) 路径和路径长度：在一棵树中，从一个结点往下可以达到的孩子或孙子结点之间的通路，称为路径。通路中分支的数目称     为路径长度。若规定根结点的层数为 1，则从根结点到第 L 层结点的路径长度为 L-1 
(2) 结点的权及带权路径长度：若将树中结点赋给一个有着某种含义的数值，则这个数值称为该结点的权。结
    点的带权路径长度为：从根结点到该结点之间的路径长度与该结点的权的乘积 
(3) 树的带权路径长度：树的带权路径长度规定为所有叶子结点的带权路径长度之和，记为WPL(weighted path
    length) ,权值越大的结点离根结点越近的二叉树才是最优二叉树。 
(4) WPL 最小的就是赫夫曼树
```

![image-20200308181625182](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200308181625182.png)

```java
赫夫曼树创建思路图解:
原始数组={13, 7, 8, 3, 29, 6, 1}
(1) 从小到大进行排序, 将每一个数据，每个数据都是一个节点 ， 每个节点可以看成是一颗最简单的二叉树 
(2) 取出根节点权值最小的两颗二叉树 
(3) 组成一颗新的二叉树, 该新的二叉树的根节点的权值是前面两颗二叉树根节点权值的和
(4) 再将这颗新的二叉树，以根节点的权值大小 再次排序， 不断重复 1-2-3-4 的步骤，直到数列中，所有的数 据都被处       理，就得到一颗赫夫曼树,如下图所示
```

![赫夫曼树创建图解](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\赫夫曼树.png)

*赫夫曼树代码实现*:

```java
package huffmantree;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class HuffmanTree {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int[] array = { 13, 7, 8, 3, 29, 6, 1 };
		Node root = creatHuffmanTree(array);
		// 测试
		perOrder(root);

	}

	// 调用前序遍历的方法
	public static void perOrder(Node root) {
		if (root != null) {
			root.perOrder();
		} else {
			System.out.println("此赫夫曼树为空，无法遍历！");
		}
	}

	// 创建赫夫曼树
	// 创建成赫夫曼树数组，返回的是赫夫曼树的根节点
	public static Node creatHuffmanTree(int[] array) {
		// 将数组的元素取出构建成Node,放入到ArrayList中
		List<Node> nodes = new ArrayList<Node>();
		// 遍历数组取出元素
		for (int value : array) {
			nodes.add(new Node(value));
		}
		// 处理的过程为循环的
		// 当不断的进行remove操作时最后只会剩下一个节点,只要剩余节点大于一个就可以一直进行循环
		while (nodes.size() > 1) {
			// 进行从小到大排序
			Collections.sort(nodes);
			// System.out.println(nodes);

			// 取出根节点权值最小的2个二叉树
			// 取出权值最小的节点(每个节点就是最简单的二叉树)
			Node leftNode = nodes.get(0);
			// 取出权值第二小的节点
			Node rightNode = nodes.get(1);
			// 将这两个节点构成新的二叉树;parent新二叉树父节点
			Node parent = new Node(leftNode.value + rightNode.value);
			parent.left = leftNode;
			parent.right = rightNode;
			// 从ArrayList中删除处理过的二叉树
			nodes.remove(leftNode);
			nodes.remove(rightNode);
			// 将parent节点加入ArrayList集合中
			nodes.add(parent);
			Collections.sort(nodes);
		}
		// 最后返回赫夫曼树的根节点即可
		return nodes.get(0);
	}

}

//创建节点类
//使Node支持Collections集合排序.让Node实现Comparable接口
class Node implements Comparable<Node> {
	int value;// 节点的权值
	Node left;// 指向左子节点
	Node right;// 指向右子节点
	// 前序遍历赫夫曼树

	public void perOrder() {
		System.out.println(this);
		if (this.left != null) {
			this.left.perOrder();
		}
		if (this.right != null) {
			this.right.perOrder();
		}
	}

	public Node(int value) {
		this.value = value;
	}

	@Override
	public String toString() {
		return "Node [value=" + value + "]";
	}

	@Override
	public int compareTo(Node o) {
		// TODO Auto-generated method stub
		// 从小到大排序,从大到小排就在前面加个-号：-(this.value-o.value)
		return this.value - o.value;// 对权值进行比较
	}

}

运行结果:
Node [value=67]
Node [value=29]
Node [value=38]
Node [value=15]
Node [value=7]
Node [value=8]
Node [value=23]
Node [value=10]
Node [value=4]
Node [value=1]
Node [value=3]
Node [value=6]
Node [value=13]
```

### 2. 赫夫曼编码

```java
基本介绍
(1) 赫夫曼编码也翻译为 哈夫曼编码(Huffman Coding)，又称霍夫曼编码，是一种编码方式, 属于一种程序算法 
(2) 赫夫曼编码是赫哈夫曼树在电讯通信中的经典的应用之一。
(3) 赫夫曼编码广泛地用于数据文件压缩。其压缩率通常在 20%～90%之间 
(4) 赫夫曼码是可变字长编码(VLC)的一种,Huffman 于 1952 年提出一种编码方法，称之为最佳编码
```

```java
原理剖析 
通信领域中信息的处理方式 1-定长编码(按照原始数据编码的方式)
  
• i like like like java do you like a java// 共40个字符(包括空格)
    
• 105 32 108 105 107 101 32 108 105 107 101 32 108 105 107 101 32 106 97 118 97 32 100 111 32 121 111 117 32 108 105 107 101 32 97 32 106 97 118 97 //对应Ascii码
    
• 01101001 00100000 01101100 01101001 01101011 01100101 00100000 01101100 01101001 01101011 01100101 00100000 01101100 01101001 01101011 01100101 00100000 01101010 01100001 01110110 01100001 00100000 01100100 01101111 00100000 01111001 01101111 01110101 00100000 01101100 01101001 01101011 01100101 00100000 01100001 00100000 01101010 01100001 01110110 01100001 // 对应的二进制
• 按照二进制来传递信息，总的长度是 359 (包括空格)
    

通信领域中信息的处理方式2-变长编码 
• i like like like java do you like a java// 共40个字符(包括空格)
• d:1 y:1 u:1 j:2 v:2 o:2 l:4 k:4 e:4 i:5 a:5 :9 // 各个字符对应的个数 • 0= , 1=a, 10=i, 11=e, 100=k,    101=l, 110=o, 111=v, 1000=j, 1001=u, 1010=y, 1011=d
说明：按照各个字符出现的次数进行编码，原则是出现次数越多的，则编码越小，比如 空格出现了9 次， 编码为0 ,其它依次类推.
• 按照上面给各个字符规定的编码，则我们在传输 "i like like like java do you like a java" 数据时，编码就是     10010110100...
• 字符的编码都不能是其他字符编码的前缀，符合此要求的编码叫做前缀编码， 即不能匹配到重复的编码(.)
```

```java
通信领域中信息的处理方式3-赫夫曼编码 
• i like like like java do you like a java // 共40个字符(包括空格)
• d:1 y:1 u:1 j:2 v:2 o:2 l:4 k:4 e:4 i:5 a:5 :9 // 各个字符对应的个数 
• 按照上面字符出现的次数构建一颗赫   夫曼树, 次数作为权值.(图后)
```

```java
创建步骤如下：

传输的 字符串 
(1) i like like like java do you like a java 
(2) d:1 y:1 u:1 j:2 v:2 o:2 l:4 k:4 e:4 i:5 a:5 :9 // 各个字符对应的个数 
(3) 按照上面字符出现的次数构建一颗赫夫曼树, 次数作为权值 步骤：
构成赫夫曼树的步骤： 
(1) 从小到大进行排序, 将每一个数据，每个数据都是一个节点 ， 每个节点可以看成是一颗最简单的二叉树 
(2) 取出根节点权值最小的两颗二叉树 
(3) 组成一颗新的二叉树, 该新的二叉树的根节点的权值是前面两颗二叉树根节点权值的和 
(4) 再将这颗新的二叉树，以根节点的权值大小 再次排序， 不断重复 1-2-3-4 的步骤，直到数列中，所有的数据都被处       理， 就得到一颗赫夫曼树
```

![赫夫曼编码图解](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200309130150190.png)

```java
(4) 根据赫夫曼树,给各个字符,规定编码 (前缀编码), 向左的路径为 0 向右的路径为 1,编码 如下: 
o: 1000 u: 10010 d: 100110 y: 100111 i: 101 a : 110
k: 1110 l: 001 空格: 01
   //解决了多义性的问题，任意一个字母的编号不再是其他字符的前缀，很牛逼的样子!!! 
(5) 按照上面的赫夫曼编码，我们的"i like like like java do you like a java" 字符串对应的编码为 (注 意这里我     们使用的无损压缩)                  10101001101111011110100110111101111010011011110111101000011000011100110011110000110    01111000100100100110111101111011100100001100001110 
   通过赫夫曼编码处理 长度为 133 (通过保存为txt文本可测试为133)

(6） 长度为 ： 133 说明: 原来长度是 359 , 压缩了 (359-133) / 359 = 62.9%
     此编码满足前缀编码,即字符的编码都不能是其他字符编码的前缀.不会造成匹配的多义性 赫夫曼编码是无损处理方案
```

```java
注意事项 
//这个赫夫曼树根据排序方法不同，也可能不太一样，这样对应的赫夫曼编码也不完全一样，但是wpl 是
//一样的，都是最小的, 最后生成的赫夫曼编码的长度是一样，比如: 如果让每次生成的新的二叉树总是排在权 值相同的二叉树的最后一个，则生成的二叉树为:
```

![image-20200309175159975](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200309175159975.png)

### 3. 数据压缩——创建赫夫曼树

```java
将给出的一段文本,比如 "i like like like java do you like a java" ， 
根据前面的的赫夫曼编码原理，对其进行数据压缩处理 ，形式如 
" 101010011011110111101001101111011110100110111101111010000110000111001        1001111000011001111000100100100110111101111011100100001100001110 "
步骤1：根据赫夫曼编码压缩数据的原理，需要创建 "i like like like java do you like a java" 对应的赫夫曼树.
```

```java
思路:
(1)创建Node节点存放字符,字符的权值即字符的个数,left和right,形式为: Node{data(存放字符),weight(权值),left      right}

(2)得到 i like like like java do you like a java 对应的字符数组:byte[] array
    
(3)编写方法,将构建赫夫曼树的节点加到 Lis t集合中,得到的形式为:
   [Node[data=97,weight=5],Node[data=32,wight=9,.....Node[data=...,wight=...] 97是a 32是空格
   这个形式完美体现了: d:1 y:1 u:1 j:2 v:2 o:2 l:4 k:4 e:4 i:5 a:5 :9
                           
(4)最后通过 List 创建赫夫曼树
```

*代码实现*

```java
package com.bytedance.huffmancode;

import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class HuffmanCode {

	public static void main(String[] args) {
		// 构建字符数组
		String content = "i like like like java do you like a java";
		byte[] contentBytes = content.getBytes();
		System.out.println(contentBytes.length);// 长度测试为40个字符
		List<Node> nodes = getNodes(contentBytes);
		// System.out.println(nodes);
		// 测试创建的二叉树
		System.out.println("赫夫曼树:");
		Node root = creatHuffmanTree(nodes);
		System.out.println("前序遍历：");
		root.preOrder();
	}

	// 前序遍历的方法
	private static void perOrder(Node root) {
		if (root != null) {
			root.preOrder();
		} else {
			System.out.println("赫夫曼树为空，无法遍历！");
		}
	}

	 //编写list集合存放Node
	// 次方法接受字符数组，返回的是List 形式为:[Node[data=97,weight=5],......]
	private static List<Node> getNodes(byte[] bytes) {
		// 创建ArrayList
		ArrayList<Node> nodes = new ArrayList<Node>();
		// 存储每个byte出现的次数，利用map[Key，Value]处理
		Map<Byte, Integer> counts = new HashMap<>();
		for (byte b : bytes) {
			// 尝试拿到字符的权值,此时有两种
			Integer count = counts.get(b);
			if (count == null) {// 说明一次没存放，Map里没有这个字符
				// 将这个字符放进map,第一次接受
				counts.put(b, 1);
			} else {
				// 否则代表之前已经存放了，此时再加入只需count+1即可
				counts.put(b, count + 1);
			}
		}
		// 以上for循环结束后已经完成了对字符出现的次数的统计

		// 将每一个键值对转成一个Node对象，并将其加入到nodes集合
		// 遍历Map集合
		for (Map.Entry<Byte, Integer> entry : counts.entrySet()) {
			nodes.add(new Node(entry.getKey(), entry.getValue()));
		}
		return nodes;
	}

	// 通过List创建对应的赫夫曼树
	private static Node creatHuffmanTree(List<Node> nodes) {
		while (nodes.size() > 1) {
			// 排序,从小到大
			Collections.sort(nodes);
			// 取出第一颗最小的二叉树
			Node leftNode = nodes.get(0);
			//// 取出第二颗最小的二叉树
			Node rightNode = nodes.get(1);
			// 创建一颗新的二叉树.其根节点没有data仅有权值
			Node parent = new Node(null, leftNode.weight + rightNode.weight);
			parent.left = leftNode;
			parent.right = rightNode;
			// 将处理过的两颗二叉树从nodes中移除
			nodes.remove(leftNode);
			nodes.remove(rightNode);
			// 将新的二叉树加入nodes
			nodes.add(parent);
		}
		// 返回根节点
		return nodes.get(0);
	}
}

//1.创建Node
class Node implements Comparable<Node> {
	Byte data;// 存放数据如a，a的编码为97等等其他字符同理
	int weight;// 权值即具体字符的个数,比如a出现了5次权值就是5
	Node left;
	Node right;

	// 构造器
	public Node(Byte data, int weight) {
		this.data = data;
		this.weight = weight;
	}

	@Override
	public int compareTo(Node o) {
		// 从小到大拍
		return this.weight - o.weight;
	}

	// 重写toString方法
	@Override
	public String toString() {
		return "Node [data=" + data + ", weight=" + weight + "]";
	}

	// 前序遍历
	public void preOrder() {
		System.out.println(this);
		if (this.left != null) {
			this.left.preOrder();
		}
		if (this.right != null) {
			this.right.preOrder();
		}
	}

}
```

```java
上述代码已经生成了赫夫曼树, 下面继续完成任务 
(1) 生成赫夫曼树对应的赫夫曼编码 , 如下表:
=01 a=100 d=11000 u=11001 e=1110 v=11011 i=101 y=11010 j=0010 k=1111 l=000 o=0011
(2) 使用赫夫曼编码来生成赫夫曼编码数据 ,即按照上面的赫夫曼编码，将"i like like like java do you like a    java" 字符串生成对应的编码数据, 形式如下. 101010001011111111001000101111111100100010111111110010010100110111 0001110000011011101000111100101000101111111100110001001010011011100
```

*赫夫曼编码表代码实现*

```java
package com.bytedance.huffmancode;

import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class HuffmanCode {

	public static void main(String[] args) {
		// 构建字符数组
		String content = "i like like like java do you like a java";
		byte[] contentBytes = content.getBytes();
		System.out.println(contentBytes.length);// 长度测试为40个字符
		List<Node> nodes = getNodes(contentBytes);
		// System.out.println(nodes);
		// 测试创建的二叉树
		System.out.println("赫夫曼树:");
		Node root = creatHuffmanTree(nodes);
		System.out.println("前序遍历：");
		root.preOrder();
		// 测试是否生成赫夫曼编码
		Map<Byte, String> huffmanCode = getCodes(root);
		// 从根节点开始所以起开始左右应该为空
		System.out.println("生成的赫夫曼编码表:" + huffmanCode);
	}
	//生成的赫夫曼树对应的赫夫曼编码
	//思路分析：
	//1. 将赫夫曼编码表存放在map中<Byie,String>形式如a=97对应编码为100...
	static Map<Byte, String> huffmanCodes = new HashMap<Byte, String>();
	// 2.在生成赫夫曼编码表时需要拼接路径，故定义StringBuilder存储某个叶子节点的路径
	static StringBuilder stringBuilder = new StringBuilder();
	
	//为了调用方便可以重载getCodes方法
	private static Map<Byte, String> getCodes(Node root) {
		if (root == null) {
			return null;
		} else {
			// 处理左子树
			getCodes(root.left, "0", stringBuilder);
			// 处理右子树
			getCodes(root.right, "1", stringBuilder);
			return huffmanCodes;
		}
	}

	/*
	 * 功能:将传入的Node节点的所有叶子节点的赫夫曼编码得到并存入到 huffmanCodes中 Node 传入的节点,默认从根节点开始传 code
	 * 路径，左子节点就是0，右子节点是1 stringBuilder 拼接路径
	 */
	private static void getCodes(Node node, String code, StringBuilder stringBuilder) {
		StringBuilder stringBuilder2 = new StringBuilder(stringBuilder);
		// 将传入的code加入到tringBuilder2
		stringBuilder2.append(code);
		// 如果node为空1就不处理
		if (node != null) {
			// 判断当前节点是叶子节点还是非叶子节点
			if (node.data == null) {// 说明当前节点为非叶子节点
				// 递归处理
				// 向左递归:0表示向左的路径
				getCodes(node.left, "0", stringBuilder2);
				// 向右递归:1表示向右的路径
				getCodes(node.right, "1", stringBuilder2);
			} else {
				// 说明是叶子节点,表示找到了某个叶子结点的最后了
				huffmanCodes.put(node.data, stringBuilder2.toString());// stringBuilder2.toString()转换成字符串
			}
		}
	}
	// 前序遍历的方法
	private static void perOrder(Node root) {
		if (root != null) {
			root.preOrder();
		} else {
			System.out.println("赫夫曼树为空，无法遍历！");
		}
	}

	 //编写list集合存放Node
	// 次方法接受字符数组，返回的是List 形式为:[Node[data=97,weight=5],......]
	private static List<Node> getNodes(byte[] bytes) {
		// 创建ArrayList
		ArrayList<Node> nodes = new ArrayList<Node>();
		// 存储每个byte出现的次数，利用map[Key，Value]处理
		Map<Byte, Integer> counts = new HashMap<>();
		for (byte b : bytes) {
			// 尝试拿到字符的权值,此时有两种
			Integer count = counts.get(b);
			if (count == null) {// 说明一次没存放，Map里没有这个字符
				// 将这个字符放进map,第一次接受
				counts.put(b, 1);
			} else {
				// 否则代表之前已经存放了，此时再加入只需count+1即可
				counts.put(b, count + 1);
			}
		}
		// 以上for循环结束后已经完成了对字符出现的次数的统计

		// 将每一个键值对转成一个Node对象，并将其加入到nodes集合
		// 遍历Map集合
		for (Map.Entry<Byte, Integer> entry : counts.entrySet()) {
			nodes.add(new Node(entry.getKey(), entry.getValue()));
		}
		return nodes;
	}

	// 通过List创建对应的赫夫曼树
	private static Node creatHuffmanTree(List<Node> nodes) {
		while (nodes.size() > 1) {
			// 排序,从小到大
			Collections.sort(nodes);
			// 取出第一颗最小的二叉树
			Node leftNode = nodes.get(0);
			//// 取出第二颗最小的二叉树
			Node rightNode = nodes.get(1);
			// 创建一颗新的二叉树.其根节点没有data仅有权值
			Node parent = new Node(null, leftNode.weight + rightNode.weight);
			parent.left = leftNode;
			parent.right = rightNode;
			// 将处理过的两颗二叉树从nodes中移除
			nodes.remove(leftNode);
			nodes.remove(rightNode);
			// 将新的二叉树加入nodes
			nodes.add(parent);
		}
		// 返回根节点
		return nodes.get(0);
	}
}

//1.创建Node
class Node implements Comparable<Node> {
	Byte data;// 存放数据如a，a的编码为97等等其他字符同理
	int weight;// 权值即具体字符的个数,比如a出现了5次权值就是5
	Node left;
	Node right;

	// 构造器
	public Node(Byte data, int weight) {
		this.data = data;
		this.weight = weight;
	}

	@Override
	public int compareTo(Node o) {
		// 从小到大拍
		return this.weight - o.weight;
	}

	// 重写toString方法
	@Override
	public String toString() {
		return "Node [data=" + data + ", weight=" + weight + "]";
	}

	// 前序遍历
	public void preOrder() {
		System.out.println(this);
		if (this.left != null) {
			this.left.preOrder();
		}
		if (this.right != null) {
			this.right.preOrder();
		}
	}

}
```

*赫夫曼编码字节数组代码实现*

```java
package com.bytedance.huffmancode;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class HuffmanCode {

	public static void main(String[] args) {
		// 构建字符数组
		String content = "i like like like java do you like a java";
		byte[] contentBytes = content.getBytes();
		System.out.println(contentBytes.length);// 长度测试为40个字符
		List<Node> nodes = getNodes(contentBytes);
		// System.out.println(nodes);
		// 测试创建的二叉树
		System.out.println("赫夫曼树:");
		Node root = creatHuffmanTree(nodes);
		System.out.println("前序遍历：");
		root.preOrder();
		// 测试是否生成赫夫曼编码
		Map<Byte, String> huffmanCode = getCodes(root);
		// 从根节点开始所以起开始左右应该为空
		System.out.println("生成的赫夫曼编码表:" + huffmanCode);

		// zip(contentBytes);

		// 测试
		byte[] huffmanCodeBytes = zip(contentBytes, huffmanCodes);
		System.out.println("huffmanCodes=" + Arrays.toString(huffmanCodeBytes));
		// 结果为[-88, -65, -56, -65, -56, -65, -55, 77, -57, 6, -24, -14, -117, -4, -60,
		// -90, 28]一共17个相比原来的40进行了压缩,压缩率大约为57.5%
	}

	// 定义方法，将一个字符串对应的byte数组通过赫夫曼编码表返回赫夫曼编码压缩后的字节数组
	// byte[] bytes:原始字符串对应的数组
	// Map<Byte,String> huffmanCodes：生成的赫夫曼编码map
	// byte[] 返回赫夫曼编码处理后的byte数组
	// byte[] bytes:byte[] contentBytes = content.getBytes()"i like like like java
	// do you like a java";;
	// huffmanCodes:就是一大堆0101110那些东西,将这些0101的子符8个一组转为一个byte放入到huffmanCodes中
	// 而这8位转成的byte其实是一个补码，需要将其转换成其不补码，再转换成原码，转换成原码后才是这个字节数组中真正存储存储的byte

	private static byte[] zip(byte[] bytes, Map<Byte, String> huffmanCodes) {
		// 利用huffmanCodes将bytes转成赫夫曼编码对应的字符串
		StringBuilder stringBuilder = new StringBuilder();
		// 遍历bytes数组、
		for (byte b : bytes) {
			stringBuilder.append(huffmanCodes.get(b));
		}

		// System.out.println("测试stringBuilder的结果:"+stringBuilder.toString());

		// 将一大堆那个字符串转换位byte数组
		// 先统计返回一大堆字符串的长度
		int len;
		if (stringBuilder.length() % 8 == 0) {
			len = stringBuilder.length() / 8;
		} else {
			len = stringBuilder.length() / 8 + 1;
		}
		// 创建存储压缩后的byte数组
		byte[] huffmanCodeBytes = new byte[len];
		// 定义截取的第几个byte
		int index = 0;
		// 遍历stringBuilder，取8位8位的放入By数组
		for (int i = 0; i < stringBuilder.length(); i += 8) {// 每8位对应一个byte
			String strByte;
			// 每循环依次就截取8位放入huffmanCodeBytes数组中
			if ((i + 8) > stringBuilder.length()) {// 防止数组越界即不够8位就从当前字符截取到最后即可
				strByte = stringBuilder.substring(i);
			} else {
				strByte = stringBuilder.substring(i, i + 8);
			}
			// 将strByte转化成一个byte放入huffmanCodeBytes数组中
			huffmanCodeBytes[index] = (byte) Integer.parseInt(strByte, 2);
			index++;
		}
		return huffmanCodeBytes;
	}

	// 生成的赫夫曼树对应的赫夫曼编码
	// 思路分析：
	// 1. 将赫夫曼编码表存放在map中<Byie,String>形式如a=97对应编码为100...
	static Map<Byte, String> huffmanCodes = new HashMap<Byte, String>();
	// 2.在生成赫夫曼编码表时需要拼接路径，故定义StringBuilder存储某个叶子节点的路径
	static StringBuilder stringBuilder = new StringBuilder();

	// 为了调用方便可以重载getCodes方法
	private static Map<Byte, String> getCodes(Node root) {
		if (root == null) {
			return null;
		} else {
			// 处理左子树
			getCodes(root.left, "0", stringBuilder);
			// 处理右子树
			getCodes(root.right, "1", stringBuilder);
			return huffmanCodes;
		}
	}

	/*
	 * 功能:将传入的Node节点的所有叶子节点的赫夫曼编码得到并存入到 huffmanCodes中 Node 传入的节点,默认从根节点开始传 code
	 * 路径，左子节点就是0，右子节点是1 stringBuilder 拼接路径
	 */
	private static void getCodes(Node node, String code, StringBuilder stringBuilder) {
		StringBuilder stringBuilder2 = new StringBuilder(stringBuilder);
		// 将传入的code加入到tringBuilder2
		stringBuilder2.append(code);
		// 如果node为空1就不处理
		if (node != null) {
			// 判断当前节点是叶子节点还是非叶子节点
			if (node.data == null) {// 说明当前节点为非叶子节点
				// 递归处理
				// 向左递归:0表示向左的路径
				getCodes(node.left, "0", stringBuilder2);
				// 向右递归:1表示向右的路径
				getCodes(node.right, "1", stringBuilder2);
			} else {
				// 说明是叶子节点,表示找到了某个叶子结点的最后了
				huffmanCodes.put(node.data, stringBuilder2.toString());// stringBuilder2.toString()转换成字符串
			}
		}
	}

	// 前序遍历的方法
	private static void perOrder(Node root) {
		if (root != null) {
			root.preOrder();
		} else {
			System.out.println("赫夫曼树为空，无法遍历！");
		}
	}

	// 编写list集合存放Node
	// 次方法接受字符数组，返回的是List 形式为:[Node[data=97,weight=5],......]
	private static List<Node> getNodes(byte[] bytes) {
		// 创建ArrayList
		ArrayList<Node> nodes = new ArrayList<Node>();
		// 存储每个byte出现的次数，利用map[Key，Value]处理
		Map<Byte, Integer> counts = new HashMap<>();
		for (byte b : bytes) {
			// 尝试拿到字符的权值,此时有两种
			Integer count = counts.get(b);
			if (count == null) {// 说明一次没存放，Map里没有这个字符
				// 将这个字符放进map,第一次接受
				counts.put(b, 1);
			} else {
				// 否则代表之前已经存放了，此时再加入只需count+1即可
				counts.put(b, count + 1);
			}
		}
		// 以上for循环结束后已经完成了对字符出现的次数的统计

		// 将每一个键值对转成一个Node对象，并将其加入到nodes集合
		// 遍历Map集合
		for (Map.Entry<Byte, Integer> entry : counts.entrySet()) {
			nodes.add(new Node(entry.getKey(), entry.getValue()));
		}
		return nodes;
	}

	// 通过List创建对应的赫夫曼树
	private static Node creatHuffmanTree(List<Node> nodes) {
		while (nodes.size() > 1) {
			// 排序,从小到大
			Collections.sort(nodes);
			// 取出第一颗最小的二叉树
			Node leftNode = nodes.get(0);
			//// 取出第二颗最小的二叉树
			Node rightNode = nodes.get(1);
			// 创建一颗新的二叉树.其根节点没有data仅有权值
			Node parent = new Node(null, leftNode.weight + rightNode.weight);
			parent.left = leftNode;
			parent.right = rightNode;
			// 将处理过的两颗二叉树从nodes中移除
			nodes.remove(leftNode);
			nodes.remove(rightNode);
			// 将新的二叉树加入nodes
			nodes.add(parent);
		}
		// 返回根节点
		return nodes.get(0);
	}
}

//1.创建Node
class Node implements Comparable<Node> {
	Byte data;// 存放数据如a，a的编码为97等等其他字符同理
	int weight;// 权值即具体字符的个数,比如a出现了5次权值就是5
	Node left;
	Node right;

	// 构造器
	public Node(Byte data, int weight) {
		this.data = data;
		this.weight = weight;
	}

	@Override
	public int compareTo(Node o) {
		// 从小到大拍
		return this.weight - o.weight;
	}

	// 重写toString方法
	@Override
	public String toString() {
		return "Node [data=" + data + ", weight=" + weight + "]";
	}

	// 前序遍历
	public void preOrder() {
		System.out.println(this);
		if (this.left != null) {
			this.left.preOrder();
		}
		if (this.right != null) {
			this.right.preOrder();
		}
	}

}
```

### 4. 赫夫曼编码字节数组的封装

```java
package com.bytedance.huffmancode;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class HuffmanCode {

	public static void main(String[] args) {
		// 构建字符数组
		String content = "i like like like java do you like a java";
		byte[] contentBytes = content.getBytes();
		byte[] huffmanCodeBytes = huffmanZip(contentBytes);
		System.out.println("压缩后的结果:" + Arrays.toString(huffmanCodeBytes) + "压缩后数组的长度:" + huffmanCodeBytes.length);
	}

	// 定义方法将上面的代码进行封装,便于调用
	// bytes原始字符串对应的字节数组
	// 返回的是压缩后的数组
	private static byte[] huffmanZip(byte[] bytes) {
		// 将传入的字节数组转为nodes放入list中
		List<Node> nodes = getNodes(bytes);
		// 创建赫夫曼树
		Node root = creatHuffmanTree(nodes);
		// 根据赫夫曼树生成对应的赫夫曼编码
		Map<Byte, String> huffmanCode = getCodes(root);
		// 根据赫夫曼编码对原始数组进行压缩
		byte[] huffmanCodeBytes = zip(bytes, huffmanCodes);
		return huffmanCodeBytes;
	}

	// 定义方法，将一个字符串对应的byte数组通过赫夫曼编码表返回赫夫曼编码压缩后的字节数组
	// byte[] bytes:原始字符串对应的数组
	// Map<Byte,String> huffmanCodes：生成的赫夫曼编码map
	// byte[] 返回赫夫曼编码处理后的byte数组
	// byte[] bytes:byte[] contentBytes = content.getBytes()"i like like like java
	// do you like a java";;
	// huffmanCodes:就是一大堆0101110那些东西,将这些0101的子符8个一组转为一个byte放入到huffmanCodes中
	// 而这8位转成的byte其实是一个补码，需要将其转换成其不补码，再转换成原码，转换成原码后才是这个字节数组中真正存储存储的byte

	private static byte[] zip(byte[] bytes, Map<Byte, String> huffmanCodes) {
		// 利用huffmanCodes将bytes转成赫夫曼编码对应的字符串
		StringBuilder stringBuilder = new StringBuilder();
		// 遍历bytes数组、
		for (byte b : bytes) {
			stringBuilder.append(huffmanCodes.get(b));
		}

		// System.out.println("测试stringBuilder的结果:"+stringBuilder.toString());

		// 将一大堆那个字符串转换位byte数组
		// 先统计返回一大堆字符串的长度
		int len;
		if (stringBuilder.length() % 8 == 0) {
			len = stringBuilder.length() / 8;
		} else {
			len = stringBuilder.length() / 8 + 1;
		}
		// 创建存储压缩后的byte数组
		byte[] huffmanCodeBytes = new byte[len];
		// 定义截取的第几个byte
		int index = 0;
		// 遍历stringBuilder，取8位8位的放入By数组
		for (int i = 0; i < stringBuilder.length(); i += 8) {// 每8位对应一个byte
			String strByte;
			// 每循环依次就截取8位放入huffmanCodeBytes数组中
			if ((i + 8) > stringBuilder.length()) {// 防止数组越界即不够8位就从当前字符截取到最后即可
				strByte = stringBuilder.substring(i);
			} else {
				strByte = stringBuilder.substring(i, i + 8);
			}
			// 将strByte转化成一个byte放入huffmanCodeBytes数组中
			huffmanCodeBytes[index] = (byte) Integer.parseInt(strByte, 2);
			index++;
		}
		return huffmanCodeBytes;
	}

	// 生成的赫夫曼树对应的赫夫曼编码
	// 思路分析：
	// 1. 将赫夫曼编码表存放在map中<Byie,String>形式如a=97对应编码为100...
	static Map<Byte, String> huffmanCodes = new HashMap<Byte, String>();
	// 2.在生成赫夫曼编码表时需要拼接路径，故定义StringBuilder存储某个叶子节点的路径
	static StringBuilder stringBuilder = new StringBuilder();

	// 为了调用方便可以重载getCodes方法
	private static Map<Byte, String> getCodes(Node root) {
		if (root == null) {
			return null;
		} else {
			// 处理左子树
			getCodes(root.left, "0", stringBuilder);
			// 处理右子树
			getCodes(root.right, "1", stringBuilder);
			return huffmanCodes;
		}
	}

	/*
	 * 功能:将传入的Node节点的所有叶子节点的赫夫曼编码得到并存入到 huffmanCodes中 Node 传入的节点,默认从根节点开始传 code
	 * 路径，左子节点就是0，右子节点是1 stringBuilder 拼接路径
	 */
	private static void getCodes(Node node, String code, StringBuilder stringBuilder) {
		StringBuilder stringBuilder2 = new StringBuilder(stringBuilder);
		// 将传入的code加入到tringBuilder2
		stringBuilder2.append(code);
		// 如果node为空1就不处理
		if (node != null) {
			// 判断当前节点是叶子节点还是非叶子节点
			if (node.data == null) {// 说明当前节点为非叶子节点
				// 递归处理
				// 向左递归:0表示向左的路径
				getCodes(node.left, "0", stringBuilder2);
				// 向右递归:1表示向右的路径
				getCodes(node.right, "1", stringBuilder2);
			} else {
				// 说明是叶子节点,表示找到了某个叶子结点的最后了
				huffmanCodes.put(node.data, stringBuilder2.toString());// stringBuilder2.toString()转换成字符串
			}
		}
	}

	// 前序遍历的方法
	private static void perOrder(Node root) {
		if (root != null) {
			root.preOrder();
		} else {
			System.out.println("赫夫曼树为空，无法遍历！");
		}
	}

	// 编写list集合存放Node
	// 次方法接受字符数组，返回的是List 形式为:[Node[data=97,weight=5],......]
	private static List<Node> getNodes(byte[] bytes) {
		// 创建ArrayList
		ArrayList<Node> nodes = new ArrayList<Node>();
		// 存储每个byte出现的次数，利用map[Key，Value]处理
		Map<Byte, Integer> counts = new HashMap<>();
		for (byte b : bytes) {
			// 尝试拿到字符的权值,此时有两种
			Integer count = counts.get(b);
			if (count == null) {// 说明一次没存放，Map里没有这个字符
				// 将这个字符放进map,第一次接受
				counts.put(b, 1);
			} else {
				// 否则代表之前已经存放了，此时再加入只需count+1即可
				counts.put(b, count + 1);
			}
		}
		// 以上for循环结束后已经完成了对字符出现的次数的统计

		// 将每一个键值对转成一个Node对象，并将其加入到nodes集合
		// 遍历Map集合
		for (Map.Entry<Byte, Integer> entry : counts.entrySet()) {
			nodes.add(new Node(entry.getKey(), entry.getValue()));
		}
		return nodes;
	}

	// 通过List创建对应的赫夫曼树
	private static Node creatHuffmanTree(List<Node> nodes) {
		while (nodes.size() > 1) {
			// 排序,从小到大
			Collections.sort(nodes);
			// 取出第一颗最小的二叉树
			Node leftNode = nodes.get(0);
			//// 取出第二颗最小的二叉树
			Node rightNode = nodes.get(1);
			// 创建一颗新的二叉树.其根节点没有data仅有权值
			Node parent = new Node(null, leftNode.weight + rightNode.weight);
			parent.left = leftNode;
			parent.right = rightNode;
			// 将处理过的两颗二叉树从nodes中移除
			nodes.remove(leftNode);
			nodes.remove(rightNode);
			// 将新的二叉树加入nodes
			nodes.add(parent);
		}
		// 返回根节点
		return nodes.get(0);
	}
}

//1.创建Node
class Node implements Comparable<Node> {
	Byte data;// 存放数据如a，a的编码为97等等其他字符同理
	int weight;// 权值即具体字符的个数,比如a出现了5次权值就是5
	Node left;
	Node right;

	// 构造器
	public Node(Byte data, int weight) {
		this.data = data;
		this.weight = weight;
	}

	@Override
	public int compareTo(Node o) {
		// 从小到大拍
		return this.weight - o.weight;
	}

	// 重写toString方法
	@Override
	public String toString() {
		return "Node [data=" + data + ", weight=" + weight + "]";
	}

	// 前序遍历
	public void preOrder() {
		System.out.println(this);
		if (this.left != null) {
			this.left.preOrder();
		}
		if (this.right != null) {
			this.right.preOrder();
		}
	}

}
```

### 5. 数据解压——字节转二进制字符串

```java
数据解压(使用赫夫曼编码解码)
使用赫夫曼编码来解码数据,具体要求是 
(1) 前面得到了赫夫曼编码和对应的编码 byte[] , 
    即:[-88, -65, -56, -65, -56, -65, -55, 77 , -57, 6, -24, -14, -117, -4, -60, -90, 28]
(2) 现在要求使用赫夫曼编码,进行解码,又重新得到原来的字符串"i like like like java do you like a java"
```

*数据解压代码实现*

```java
package com.bytedance.huffmancode;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class HuffmanCode {

	public static void main(String[] args) {
		// 构建字符数组
		String content = "i like like like java do you like a java";
		byte[] contentBytes = content.getBytes();
		byte[] huffmanCodeBytes = huffmanZip(contentBytes);
		System.out.println("压缩后的结果:" + Arrays.toString(huffmanCodeBytes) + "压缩后数组的长度:" + huffmanCodeBytes.length);
		byte[] sourceBytes=decode( huffmanCodes,huffmanCodeBytes );
		System.out.println("解压得到原来的字符串:"+new String(sourceBytes));
	}

	// 完成数据解压
	
	//真正的解码方法
	/**
	 * @param huffmanCodes 赫夫曼编码表
	 * @param huffmanBytes 压缩得到的字节数组
	 * @return 返回原来字符串对应的数组
	 */
	private static byte[] decode(Map<Byte, String> huffmanCodes, byte[] huffmanBytes) {
		// 得到huffmanCodes对应的二进制的字符串即一大堆0101那个东西,进行拼接
		StringBuilder stringBuilder = new StringBuilder();
		// 将byte数组转换成二进制字符串
		for (int i = 0; i < huffmanBytes.length; i++) {
			byte b = huffmanBytes[i];
			// 判断是不是最后一个字节
			boolean flag = (i == huffmanBytes.length - 1);// length-1为数组的最后一位，这位不用进行补位
			// 不是最后一位就加入到tringBuilde中进行拼接
			stringBuilder.append(byteToBitString(!flag, b));
		}

		// 得到的二进制字符串按照指定的赫夫曼编码进行解码
		// 因为是反向查找原来a->100 现在 100->a
		Map<String, Byte> map = new HashMap<String, Byte>();
		for (Map.Entry<Byte, String> entry : huffmanCodes.entrySet()) {
			// 进行反向放
			map.put(entry.getValue(), entry.getKey());
		}

		// 创建集合存放byte
		List<Byte> list = new ArrayList<>();
		// 遍历得到的stringBuilder
		for (int i = 0; i < stringBuilder.length();) {
			// 计数器
			int count = 1;
			boolean flag = true;
			// b用于存储遍历得到的字节
			Byte b = null;

			while (flag) {
				/**
				 * stringBuilder形如:101001110....
				 * 如当i=0时count=1此时截取到得是1，意思就是让i不动,count动,直到找到指定匹配的字符，这个过程就是递增取key
				 */
				String key = stringBuilder.substring(i, i + count);
				b = map.get(key);// 和map里的字符进行对比
				if (b == null) {// 为空说明没找到
					count++;
				} else {
					// 匹配到了，就退出循环
					flag = false;
				}
			}
			// 将得到的b存入list中
			list.add(b);
			// 此时直接将i移动到count找到匹配字符的那个位置，假设找到了10，此时直接让i跳到10后面的那个位置
			i += count;
		}

		// for循还结束后就已经得到了所有的"i like .....java"的单个字符串
		// 将list中的数据存入一个byte数组并返回
		byte[] b = new byte[list.size()];
		for (int i = 0; i < b.length; i++) {
			b[i] = list.get(i);
		}
		return b;
	}

	/*
	 * 第一步：将压缩后得到的字节数组重新转换成赫夫曼编码对应的二进制字符串即"1001100....
	 * 第二步:将生成的二进制字符串按照赫夫曼编码转化成原来的字符串即"i like .....java"
	 */
	// 该方法是将一个byte转成一个二进制的字符串(如传进了一个Byte为-88就将其转化为对应的二进制)
	// flag表示知否需要补高位，true表示补，false表示不补
	private static String byteToBitString(boolean flag, byte b) {
		// 使用变量保存传入的b
		int temp = b;// 将b转成了byte
		// 如果是整数需要补高位
		// 可能出现某个字节的位数本身不需要取8位，所以先进行判断，需要补高位的就补，不需要的就直接返回即可
		if (flag) {
			// 按位与256->1 0000 000
			// 1->0000 0001
			// 1和256按位与->1 0000 0001
			temp |= 256;
		}
		// 返回的是temp对应的二进制的补码
		String str = Integer.toBinaryString(temp);
		if (flag) {
			return str.substring(str.length() - 8);// 倒数取8位
		} else {
			return str;
		}

	}

	// 定义方法将上面的代码进行封装,便于调用
	// bytes原始字符串对应的字节数组
	// 返回的是压缩后的数组
	private static byte[] huffmanZip(byte[] bytes) {
		// 将传入的字节数组转为nodes放入list中
		List<Node> nodes = getNodes(bytes);
		// 创建赫夫曼树
		Node root = creatHuffmanTree(nodes);
		// 根据赫夫曼树生成对应的赫夫曼编码
		Map<Byte, String> huffmanCode = getCodes(root);
		// 根据赫夫曼编码对原始数组进行压缩
		byte[] huffmanCodeBytes = zip(bytes, huffmanCodes);
		return huffmanCodeBytes;
	}

	// 定义方法，将一个字符串对应的byte数组通过赫夫曼编码表返回赫夫曼编码压缩后的字节数组
	// byte[] bytes:原始字符串对应的数组
	// Map<Byte,String> huffmanCodes：生成的赫夫曼编码map
	// byte[] 返回赫夫曼编码处理后的byte数组
	// byte[] bytes:byte[] contentBytes = content.getBytes()"i like like like java
	// do you like a java";;
	// huffmanCodes:就是一大堆0101110那些东西,将这些0101的子符8个一组转为一个byte放入到huffmanCodes中
	// 而这8位转成的byte其实是一个补码，需要将其转换成其不补码，再转换成原码，转换成原码后才是这个字节数组中真正存储存储的byte

	private static byte[] zip(byte[] bytes, Map<Byte, String> huffmanCodes) {
		// 利用huffmanCodes将bytes转成赫夫曼编码对应的字符串
		StringBuilder stringBuilder = new StringBuilder();
		// 遍历bytes数组、
		for (byte b : bytes) {
			stringBuilder.append(huffmanCodes.get(b));
		}

		//System.out.println("测试stringBuilder的结果:"+stringBuilder.toString());

		// 将一大堆那个字符串转换位byte数组
		// 先统计返回一大堆字符串的长度
		int len;
		if (stringBuilder.length() % 8 == 0) {
			len = stringBuilder.length() / 8;
		} else {
			len = stringBuilder.length() / 8 + 1;
		}
		// 创建存储压缩后的byte数组
		byte[] huffmanCodeBytes = new byte[len];
		// 定义截取的第几个byte
		int index = 0;
		// 遍历stringBuilder，取8位8位的放入By数组
		for (int i = 0; i < stringBuilder.length(); i += 8) {// 每8位对应一个byte
			String strByte;
			// 每循环依次就截取8位放入huffmanCodeBytes数组中
			if ((i + 8) > stringBuilder.length()) {// 防止数组越界即不够8位就从当前字符截取到最后即可
				strByte = stringBuilder.substring(i);
			} else {
				strByte = stringBuilder.substring(i, i + 8);
			}
			// 将strByte转化成一个byte放入huffmanCodeBytes数组中
			huffmanCodeBytes[index] = (byte) Integer.parseInt(strByte, 2);
			index++;
		}
		return huffmanCodeBytes;
	}

	// 生成的赫夫曼树对应的赫夫曼编码
	// 思路分析：
	// 1. 将赫夫曼编码表存放在map中<Byie,String>形式如a=97对应编码为100...
	static Map<Byte, String> huffmanCodes = new HashMap<Byte, String>();
	// 2.在生成赫夫曼编码表时需要拼接路径，故定义StringBuilder存储某个叶子节点的路径
	static StringBuilder stringBuilder = new StringBuilder();

	// 为了调用方便可以重载getCodes方法
	private static Map<Byte, String> getCodes(Node root) {
		if (root == null) {
			return null;
		} else {
			// 处理左子树
			getCodes(root.left, "0", stringBuilder);
			// 处理右子树
			getCodes(root.right, "1", stringBuilder);
			return huffmanCodes;
		}
	}

	/*
	 * 功能:将传入的Node节点的所有叶子节点的赫夫曼编码得到并存入到 huffmanCodes中 Node 传入的节点,默认从根节点开始传 code
	 * 路径，左子节点就是0，右子节点是1 stringBuilder 拼接路径
	 */
	private static void getCodes(Node node, String code, StringBuilder stringBuilder) {
		StringBuilder stringBuilder2 = new StringBuilder(stringBuilder);
		// 将传入的code加入到tringBuilder2
		stringBuilder2.append(code);
		// 如果node为空1就不处理
		if (node != null) {
			// 判断当前节点是叶子节点还是非叶子节点
			if (node.data == null) {// 说明当前节点为非叶子节点
				// 递归处理
				// 向左递归:0表示向左的路径
				getCodes(node.left, "0", stringBuilder2);
				// 向右递归:1表示向右的路径
				getCodes(node.right, "1", stringBuilder2);
			} else {
				// 说明是叶子节点,表示找到了某个叶子结点的最后了
				huffmanCodes.put(node.data, stringBuilder2.toString());// stringBuilder2.toString()转换成字符串
			}
		}
	}

	// 前序遍历的方法
	private static void perOrder(Node root) {
		if (root != null) {
			root.preOrder();
		} else {
			System.out.println("赫夫曼树为空，无法遍历！");
		}
	}

	// 编写list集合存放Node
	// 此方法接受字符数组，返回的是List 形式为:[Node[data=97,weight=5],......]
	private static List<Node> getNodes(byte[] bytes) {
		// 创建ArrayList
		ArrayList<Node> nodes = new ArrayList<Node>();
		// 存储每个byte出现的次数，利用map[Key，Value]处理
		Map<Byte, Integer> counts = new HashMap<>();
		for (byte b : bytes) {
			// 尝试拿到字符的权值,此时有两种
			Integer count = counts.get(b);
			if (count == null) {// 说明一次没存放，Map里没有这个字符
				// 将这个字符放进map,第一次接受
				counts.put(b, 1);
			} else {
				// 否则代表之前已经存放了，此时再加入只需count+1即可
				counts.put(b, count + 1);
			}
		}
		// 以上for循环结束后已经完成了对字符出现的次数的统计

		// 将每一个键值对转成一个Node对象，并将其加入到nodes集合
		// 遍历Map集合
		for (Map.Entry<Byte, Integer> entry : counts.entrySet()) {
			nodes.add(new Node(entry.getKey(), entry.getValue()));
		}
		return nodes;
	}

	// 通过List创建对应的赫夫曼树
	private static Node creatHuffmanTree(List<Node> nodes) {
		while (nodes.size() > 1) {
			// 排序,从小到大
			Collections.sort(nodes);
			// 取出第一颗最小的二叉树
			Node leftNode = nodes.get(0);
			//// 取出第二颗最小的二叉树
			Node rightNode = nodes.get(1);
			// 创建一颗新的二叉树.其根节点没有data仅有权值
			Node parent = new Node(null, leftNode.weight + rightNode.weight);
			parent.left = leftNode;
			parent.right = rightNode;
			// 将处理过的两颗二叉树从nodes中移除
			nodes.remove(leftNode);
			nodes.remove(rightNode);
			// 将新的二叉树加入nodes
			nodes.add(parent);
		}
		// 返回根节点
		return nodes.get(0);
	}
}

//1.创建Node
class Node implements Comparable<Node> {
	Byte data;// 存放数据如a，a的编码为97等等其他字符同理
	int weight;// 权值即具体字符的个数,比如a出现了5次权值就是5
	Node left;
	Node right;

	// 构造器
	public Node(Byte data, int weight) {
		this.data = data;
		this.weight = weight;
	}

	@Override
	public int compareTo(Node o) {
		// 从小到大拍
		return this.weight - o.weight;
	}

	// 重写toString方法
	@Override
	public String toString() {
		return "Node [data=" + data + ", weight=" + weight + "]";
	}

	// 前序遍历
	public void preOrder() {
		System.out.println(this);
		if (this.left != null) {
			this.left.preOrder();
		}
		if (this.right != null) {
			this.right.preOrder();
		}
	}

}
```



### 6. 文件压缩与解压

```java
具体要求：给定一个图片文件，要求对其进行无损压缩, 看看压缩效果如何。 
思路：读取文件-> 得到赫夫曼编码表 -> 完成压缩
    
具体要求：将前面压缩的文件，重新恢复成原来的文件。 
思路：读取压缩文件(数据和赫夫曼编码表)-> 完成解压(文件恢复)
```

```java
package com.bytedance.huffmancode;

import java.io.*;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class HuffmanCode {

	public static void main(String[] args) {
		// 测试压缩文件
		// String srcFile="d://cr.png";
		// String dstFile="d://cry.zip";
		// 调用方法
		// zipFile(srcFile,dstFile);
		// System.out.println("恭喜压缩文件成功！");
		// 测试解压文件
		String zipFile = "d://cry.zip";
		String dstFile = "d://cr3.png";
		onZipFile(zipFile, dstFile);
		System.out.println("恭喜解压文件成功！");
	}

	// 文件解压方法
	/**
	 * @param zipFile 准备解压的文件
	 * @param dstFile 将文件解压到的路径
	 */
	public static void onZipFile(String zipFile, String dstFile) {
		// 定义文件的输入流
		InputStream is = null;
		// 与输入流关联的对象输入流
		ObjectInputStream ois = null;
		// 文件输出流
		OutputStream os = null;
		try {
			// 创建文件输入流
			is = new FileInputStream(zipFile);
			// 创建和is关联的对象输入流
			ois = new ObjectInputStream(is);
			// 读取byte数组(huffmanBytes)
			byte[] huffmanBytes = (byte[]) ois.readObject();
			// 读取赫夫曼编码表
			Map<Byte, String> huffmancodes = (Map<Byte, String>) ois.readObject();
			// 解码:得到原始的bytes数组
			byte[] bytes = decode(huffmanCodes, huffmanBytes);
			// bytes写入到目标文件
			os = new FileOutputStream(dstFile);
			// 写出数据到文件中
			os.write(bytes);
		} catch (Exception e) {
			System.out.println(e.getMessage());
		} finally {
			try {
				os.close();
				ois.close();
				is.close();
			} catch (Exception e) {
				System.out.println(e.getMessage());
			}
		}
	}

	// 文件压缩的方法
	/**
	 * @param srcFile 传入的希望压缩的文件的完整路径
	 * @param dstFile 将压缩后的文件放入那个文件下
	 */
	public static void zipFile(String srcFile, String dstFile) {
		// 创建输出流
		OutputStream os = null;
		ObjectOutputStream oos = null;
		// 创建文件的输入流
		FileInputStream is = null;
		try {
			is = new FileInputStream(srcFile);
			// 创建一个和源文件大小一样的byte数组
			byte[] b = new byte[is.available()];
			// 读取源文件
			is.read(b);
			// 直接进行文件压缩
			byte[] huffmanBytes = huffmanZip(b);
			// 创建文件输出流,存放压缩文件
			os = new FileOutputStream(dstFile);
			// 创建一个和文件输出流关联的objectOutputStream
			oos = new ObjectOutputStream(os);
			// 将赫夫曼编码后的字节数组写入压缩文件
			oos.writeObject(huffmanBytes);
			// 可以直接以对象流的方式写入赫夫曼编码，方便恢复文件,必须将赫夫曼编码写入压缩文件，否则恢复不了
			oos.writeObject(huffmanCodes);
		} catch (Exception e) {
			System.out.println(e.getMessage());
		} finally {
			try {
				is.close();
				oos.close();
				os.close();
			} catch (Exception e) {
				System.out.println(e.getMessage());
			}
		}
	}
	// 完成数据解压

	// 真正的解码方法
	/**
	 * @param huffmanCodes 赫夫曼编码表
	 * @param huffmanBytes 压缩得到的字节数组
	 * @return 返回原来字符串对应的数组
	 */
	private static byte[] decode(Map<Byte, String> huffmanCodes, byte[] huffmanBytes) {
		// 得到huffmanCodes对应的二进制的字符串即一大堆0101那个东西,进行拼接
		StringBuilder stringBuilder = new StringBuilder();
		// 将byte数组转换成二进制字符串
		for (int i = 0; i < huffmanBytes.length; i++) {
			byte b = huffmanBytes[i];
			// 判断是不是最后一个字节
			boolean flag = (i == huffmanBytes.length - 1);// length-1为数组的最后一位，这位不用进行补位
			// 不是最后一位就加入到tringBuilde中进行拼接
			stringBuilder.append(byteToBitString(!flag, b));
		}

		// 得到的二进制字符串按照指定的赫夫曼编码进行解码
		// 因为是反向查找原来a->100 现在 100->a
		Map<String, Byte> map = new HashMap<String, Byte>();
		for (Map.Entry<Byte, String> entry : huffmanCodes.entrySet()) {
			// 进行反向放
			map.put(entry.getValue(), entry.getKey());
		}

		// 创建集合存放byte
		List<Byte> list = new ArrayList<>();
		// 遍历得到的stringBuilder
		for (int i = 0; i < stringBuilder.length();) {
			// 计数器
			int count = 1;
			boolean flag = true;
			// b用于存储遍历得到的字节
			Byte b = null;

			while (flag) {
				/**
				 * stringBuilder形如:101001110....
				 * 如当i=0时count=1此时截取到得是1，意思就是让i不动,count动,直到找到指定匹配的字符，这个过程就是递增取key
				 */
				String key = stringBuilder.substring(i, i + count);
				b = map.get(key);// 和map里的字符进行对比
				if (b == null) {// 为空说明没找到
					count++;
				} else {
					// 匹配到了，就退出循环
					flag = false;
				}
			}
			// 将得到的b存入list中
			list.add(b);
			// 此时直接将i移动到count找到匹配字符的那个位置，假设找到了10，此时直接让i跳到10后面的那个位置
			i += count;
		}

		// for循还结束后就已经得到了所有的"i like .....java"的单个字符串
		// 将list中的数据存入一个byte数组并返回
		byte[] b = new byte[list.size()];
		for (int i = 0; i < b.length; i++) {
			b[i] = list.get(i);
		}
		return b;
	}

	/*
	 * 第一步：将压缩后得到的字节数组重新转换成赫夫曼编码对应的二进制字符串即"1001100....
	 * 第二步:将生成的二进制字符串按照赫夫曼编码转化成原来的字符串即"i like .....java"
	 */
	// 该方法是将一个byte转成一个二进制的字符串(如传进了一个Byte为-88就将其转化为对应的二进制)
	// flag表示知否需要补高位，true表示补，false表示不补
	private static String byteToBitString(boolean flag, byte b) {
		// 使用变量保存传入的b
		int temp = b;// 将b转成了byte
		// 如果是整数需要补高位
		// 可能出现某个字节的位数本身不需要取8位，所以先进行判断，需要补高位的就补，不需要的就直接返回即可
		if (flag) {
			// 按位与256->1 0000 000
			// 1->0000 0001
			// 1和256按位与->1 0000 0001
			temp |= 256;
		}
		// 返回的是temp对应的二进制的补码
		String str = Integer.toBinaryString(temp);
		if (flag) {
			return str.substring(str.length() - 8);// 倒数取8位
		} else {
			return str;
		}

	}

	// 定义方法将上面的代码进行封装,便于调用
	// bytes原始字符串对应的字节数组
	// 返回的是压缩后的数组
	private static byte[] huffmanZip(byte[] bytes) {
		// 将传入的字节数组转为nodes放入list中
		List<Node> nodes = getNodes(bytes);
		// 创建赫夫曼树
		Node root = creatHuffmanTree(nodes);
		// 根据赫夫曼树生成对应的赫夫曼编码
		Map<Byte, String> huffmanCode = getCodes(root);
		// 根据赫夫曼编码对原始数组进行压缩
		byte[] huffmanCodeBytes = zip(bytes, huffmanCodes);
		return huffmanCodeBytes;
	}

	// 定义方法，将一个字符串对应的byte数组通过赫夫曼编码表返回赫夫曼编码压缩后的字节数组
	// byte[] bytes:原始字符串对应的数组
	// Map<Byte,String> huffmanCodes：生成的赫夫曼编码map
	// byte[] 返回赫夫曼编码处理后的byte数组
	// byte[] bytes:byte[] contentBytes = content.getBytes()"i like like like java
	// do you like a java";;
	// huffmanCodes:就是一大堆0101110那些东西,将这些0101的子符8个一组转为一个byte放入到huffmanCodes中
	// 而这8位转成的byte其实是一个补码，需要将其转换成其不补码，再转换成原码，转换成原码后才是这个字节数组中真正存储存储的byte

	private static byte[] zip(byte[] bytes, Map<Byte, String> huffmanCodes) {
		// 利用huffmanCodes将bytes转成赫夫曼编码对应的字符串
		StringBuilder stringBuilder = new StringBuilder();
		// 遍历bytes数组、
		for (byte b : bytes) {
			stringBuilder.append(huffmanCodes.get(b));
		}

		// System.out.println("测试stringBuilder的结果:"+stringBuilder.toString());

		// 将一大堆那个字符串转换位byte数组
		// 先统计返回一大堆字符串的长度
		int len;
		if (stringBuilder.length() % 8 == 0) {
			len = stringBuilder.length() / 8;
		} else {
			len = stringBuilder.length() / 8 + 1;
		}
		// 创建存储压缩后的byte数组
		byte[] huffmanCodeBytes = new byte[len];
		// 定义截取的第几个byte
		int index = 0;
		// 遍历stringBuilder，取8位8位的放入By数组
		for (int i = 0; i < stringBuilder.length(); i += 8) {// 每8位对应一个byte
			String strByte;
			// 每循环依次就截取8位放入huffmanCodeBytes数组中
			if ((i + 8) > stringBuilder.length()) {// 防止数组越界即不够8位就从当前字符截取到最后即可
				strByte = stringBuilder.substring(i);
			} else {
				strByte = stringBuilder.substring(i, i + 8);
			}
			// 将strByte转化成一个byte放入huffmanCodeBytes数组中
			huffmanCodeBytes[index] = (byte) Integer.parseInt(strByte, 2);
			index++;
		}
		return huffmanCodeBytes;
	}

	// 生成的赫夫曼树对应的赫夫曼编码
	// 思路分析：
	// 1. 将赫夫曼编码表存放在map中<Byie,String>形式如a=97对应编码为100...
	static Map<Byte, String> huffmanCodes = new HashMap<Byte, String>();
	// 2.在生成赫夫曼编码表时需要拼接路径，故定义StringBuilder存储某个叶子节点的路径
	static StringBuilder stringBuilder = new StringBuilder();

	// 为了调用方便可以重载getCodes方法
	private static Map<Byte, String> getCodes(Node root) {
		if (root == null) {
			return null;
		} else {
			// 处理左子树
			getCodes(root.left, "0", stringBuilder);
			// 处理右子树
			getCodes(root.right, "1", stringBuilder);
			return huffmanCodes;
		}
	}

	/*
	 * 功能:将传入的Node节点的所有叶子节点的赫夫曼编码得到并存入到 huffmanCodes中 Node 传入的节点,默认从根节点开始传 code
	 * 路径，左子节点就是0，右子节点是1 stringBuilder 拼接路径
	 */
	private static void getCodes(Node node, String code, StringBuilder stringBuilder) {
		StringBuilder stringBuilder2 = new StringBuilder(stringBuilder);
		// 将传入的code加入到tringBuilder2
		stringBuilder2.append(code);
		// 如果node为空1就不处理
		if (node != null) {
			// 判断当前节点是叶子节点还是非叶子节点
			if (node.data == null) {// 说明当前节点为非叶子节点
				// 递归处理
				// 向左递归:0表示向左的路径
				getCodes(node.left, "0", stringBuilder2);
				// 向右递归:1表示向右的路径
				getCodes(node.right, "1", stringBuilder2);
			} else {
				// 说明是叶子节点,表示找到了某个叶子结点的最后了
				huffmanCodes.put(node.data, stringBuilder2.toString());// stringBuilder2.toString()转换成字符串
			}
		}
	}

	// 前序遍历的方法
	private static void perOrder(Node root) {
		if (root != null) {
			root.preOrder();
		} else {
			System.out.println("赫夫曼树为空，无法遍历！");
		}
	}

	// 编写list集合存放Node
	// 次方法接受字符数组，返回的是List 形式为:[Node[data=97,weight=5],......]
	private static List<Node> getNodes(byte[] bytes) {
		// 创建ArrayList
		ArrayList<Node> nodes = new ArrayList<Node>();
		// 存储每个byte出现的次数，利用map[Key，Value]处理
		Map<Byte, Integer> counts = new HashMap<>();
		for (byte b : bytes) {
			// 尝试拿到字符的权值,此时有两种
			Integer count = counts.get(b);
			if (count == null) {// 说明一次没存放，Map里没有这个字符
				// 将这个字符放进map,第一次接受
				counts.put(b, 1);
			} else {
				// 否则代表之前已经存放了，此时再加入只需count+1即可
				counts.put(b, count + 1);
			}
		}
		// 以上for循环结束后已经完成了对字符出现的次数的统计

		// 将每一个键值对转成一个Node对象，并将其加入到nodes集合
		// 遍历Map集合
		for (Map.Entry<Byte, Integer> entry : counts.entrySet()) {
			nodes.add(new Node(entry.getKey(), entry.getValue()));
		}
		return nodes;
	}

	// 通过List创建对应的赫夫曼树
	private static Node creatHuffmanTree(List<Node> nodes) {
		while (nodes.size() > 1) {
			// 排序,从小到大
			Collections.sort(nodes);
			// 取出第一颗最小的二叉树
			Node leftNode = nodes.get(0);
			//// 取出第二颗最小的二叉树
			Node rightNode = nodes.get(1);
			// 创建一颗新的二叉树.其根节点没有data仅有权值
			Node parent = new Node(null, leftNode.weight + rightNode.weight);
			parent.left = leftNode;
			parent.right = rightNode;
			// 将处理过的两颗二叉树从nodes中移除
			nodes.remove(leftNode);
			nodes.remove(rightNode);
			// 将新的二叉树加入nodes
			nodes.add(parent);
		}
		// 返回根节点
		return nodes.get(0);
	}
}

//1.创建Node
class Node implements Comparable<Node> {
	Byte data;// 存放数据如a，a的编码为97等等其他字符同理
	int weight;// 权值即具体字符的个数,比如a出现了5次权值就是5
	Node left;
	Node right;

	// 构造器
	public Node(Byte data, int weight) {
		this.data = data;
		this.weight = weight;
	}

	@Override
	public int compareTo(Node o) {
		// 从小到大拍
		return this.weight - o.weight;
	}

	// 重写toString方法
	@Override
	public String toString() {
		return "Node [data=" + data + ", weight=" + weight + "]";
	}

	// 前序遍历
	public void preOrder() {
		System.out.println(this);
		if (this.left != null) {
			this.left.preOrder();
		}
		if (this.right != null) {
			this.right.preOrder();
		}
	}

}
```

### 7. 赫夫曼编码压缩文件注意事项

```java
(1)如果文件本身就是经过压缩处理的，那么使用赫夫曼编码再压缩效率不会有明显 变化, 比如视频,ppt 等等文件 
    [举例压一个 .ppt]
(2) 赫夫曼编码是按字节来处理的，因此可以处理所有的文件( 二进制文件、文本文件) [举例压一个.xml文件]
(3) 如果一个文件中的内容，重复的数据不多，压缩效果也不会很明显.
```

## 21. 二叉排序树（BST)

```java
先看一个需求 
给你一个数列 (7, 3, 10, 12, 5, 1, 9)，要求能够高效的完成对数据的查询和添加   
解决方案分析
使用数组 
(1) 数组未排序， 优点：直接在数组尾添加，速度快。 缺点：查找速度慢. 
(2) 数组排序，优点：可以使用二分查找，查找速度快，缺点：为了保证数组有序 ，在添加新数据时，找到插入位置后
    后面的数据需整体移动，速度慢使用链式存储-链表不管链表是否有序，查找速度都慢，添加数据速度比数组快，不需要数     据整体 移动。使用二叉排序树 
```

```java
二叉排序树(也称二叉搜索树)介绍 
二叉排序树：BST: (Binary Sort(Search) Tree),
对于二叉排序树的任何一个非叶子节点，要求左子节点的值比当前节点的值小，右子节点的值比当前节点的值大。 特别说明：如果有相同的值，可以将该节点放在左子节点或右子节点//如下图
1.其左右子树也是一颗二叉排序树
2.二叉搜索树可以大大的提高搜索数据的效率
3.二叉搜索树存储的元素必须具备可比较性. 如可存储 int double类型数据,若是自定义类型则必须指定比较方式,如需要在   树中存储Preson类,preson有年龄,身高等属性,如果按年龄比较则要将年龄较小的放在树的左子树,大的放在右子树

4. 二叉搜索树的接口设计
   int size()-元素的数量             void add(E element)-添加元素
   boolean isEmpty()-是否为空        void remove(E element)-删除元素
   void clear()-清空所有元素         boolean contains(E element)-是否存在某元素即搜索
   //注意:现在使用的二叉树是没有索引概念的,在树中索引用不上
```

![image-20200310223551736](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200310223551736.png)

### 1. BST的创建和遍历

```java
一个数组创建成对应的二叉排序树，并使用中序遍历二叉排序树，比如: 数组为 Array(7, 3, 10, 12, 5, 1, 9)
```

*BST创建和遍历代码实现*

```java
package com.bytedance.binarysorttree;

public class BinarySortTreeDemo {

	public static void main(String[] args) {
		// 测试
		int[] array = { 7, 3, 10, 12, 5, 1, 9 };
		BinarySortTree binarySortTree = new BinarySortTree();
		// 循环的添加节点到BST
		for (int i = 0; i < array.length; i++) {
			binarySortTree.add(new Node(array[i]));
		}
		// 中序遍历BST
		System.out.println("中序遍历BST:");
		binarySortTree.infixOrder();
	}

}

//创建BST
class BinarySortTree {
	private Node root;// 根节点

	public void add(Node node) {
		if (root == null) {
			root = node;// root为空传入的node直接指向root
		} else {
			root.add(node);
		}
	}

	// 中序遍历
	public void infixOrder() {
		if (root != null) {
			root.infixOeder();
		} else {
			System.out.println("BST为空无法遍历!");
		}
	}
}

//创建节点
class Node {
	int value;
	Node left;
	Node right;

	public Node(int value) {
		this.value = value;
	}

	@Override
	public String toString() {
		return "Node [value=" + value + "]";
	}

	// 添加节点的方法
	// 递归添加节点
	public void add(Node node) {
		if (node == null) {
			return;
		}
		// 判断传入的节点和当前子树的根节点的值作比较
		if (node.value < this.value) {
			// 若当前节点的左子节为空就将传入的节点挂在此节点左边
			if (this.left == null) {
				this.left = node;
			} else {// 不为空就继续递归向左子树加入节点
				this.left.add(node);
			}
		} else {
			// 添加的结点的值大于当前节点的值，即子树的根节点
			if (this.right == null) {
				this.right = node;
			} else {
				// 递归向右子树添加节点
				this.right.add(node);
			}
		}
	}

	// 中序遍历方法
	public void infixOeder() {
		if (this.left != null) {
			this.left.infixOeder();
		}
		System.out.println(this);
		if (this.right != null) {
			this.right.infixOeder();
		}
	}

    
//运行结果: 正好是升序的
Node [value=1]
Node [value=3]
Node [value=5]
Node [value=7]
Node [value=9]
Node [value=10]
Node [value=12]

 
```

**非递实现节点的添加**

```java
package com.byedance.tree;
/**
 * @author 二叉搜索树
 * @create 2020-04-23 11:37
 */
public class BinarySearchTree<E> {
    private int size;//元素数量
    private Node<E> root;//根节点

    public int size() {
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public void clear() {

    }

    public void add(E element) {
        elementNotNullCheck(element);
        //添加第一个节点
        if (root == null) {
            //添加的第一个节点就是根节点
            root = new Node<>(element, null);
            size++;
            return;
        }
        //如果能到这说明添加的节点是子节点
        Node<E> node = root;
        Node<E> parentNode = root;//默认情况下根节点是父节点
        int cmp = 0;
        while (node != null) {
            cmp = compare(element, node.element);
            //在比较之前将父节点保存
            parentNode = node;
            if (cmp > 0) {
                node = node.right;
            } else if (cmp < 0) {
                node = node.left;
            } else {
                //相等
                return;
            }
        }
        //插到父节点的位置
        Node<E> newNode = new Node<>(element, parentNode);
        if (cmp > 0) {
            parentNode.right = newNode;
        } else {
            parentNode.left = newNode;
        }
        size++;
    }

    public void remove(E element) {

    }

    public boolean contains(E element) {
        return false;
    }

    /**
     * 返回值等于0代表e1和e2相等,返回值大于代表e1大于e2,返回值小于0代表e1小于e2
     */
    private int compare(E e1, E e2) {
        return 0;
    }

    //判断是否为空
    private void elementNotNullCheck(E element) {
        if (element == null) {
            //非法参数异常
            throw new IllegalArgumentException("element must not be null!");
        }
    }

    //内部维护一个节点类
    private static class Node<E> {
        E element;//元素
        Node<E> left;//左子节点
        Node<E> right;//右子节点
        Node<E> parent;//父节点

        public Node(E element, Node<E> parent) {
            this.element = element;
            this.parent = parent;
        }
    }
}
```

### 2. BST的删除

```java
二叉排序树的删除情况比较复杂，有下面三种情况需要考虑
(1) 删除叶子节点 (比如：2, 5, 9, 12) 
(2) 删除只有一颗子树的节点 (比如：1) 
(3) 删除有两颗子树的节点. (比如：7, 3，10 )
//如下图
```

![image-20200310231109998](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200310231109998.png)

```java
对删除结点的各种情况的思路分析:
第一种情况: 
删除叶子节点 (比如：2, 5, 9, 12) 
思路 
(1) 需求先去找到要删除的结点 targetNode 
(2) 找到 targetNode 的 父结点 parent 
(3) 确定 targetNode 是 parent 的左子结点 还是右子结点 
(4) 根据前面的情况来对应删除 左子结点 parent.left = null 右子结点 parent.right = null;


第二种情况: 删除只有一颗子树的节点 比如 1 思路 
(1) 需求先去找到要删除的结点 targetNode 
(2) 找到 targetNode 的 父结点 parent 
(3) 确定 targetNode 的子结点是左子结点还是右子结点 
(4) targetNode 是 parent 的左子结点还是右子结点 
(5) 如果 targetNode 有左子结点 
    5.1 如果 targetNode 是 parent 的左子结点parent.left = targetNode.left;
    5.2 如果 targetNode 是 parent 的右子结点 parent.right = targetNode.left; 
(6) 如果 targetNode 有右子结点 
    6.1 如果 targetNode 是 parent 的左子结点 parent.left = targetNode.right; 
    6.2 如果 targetNode 是 parent 的右子结点 parent.right = targetNode.right
  
        
情况三 ： 删除有两颗子树的节点. (比如：7, 3，10 ) 思路 
(1) 需求先去找到要删除的结点 targetNode 
(2) 找到 targetNode 的 父结点 parent 
(3) 从 targetNode 的右子树找到最小的结点 //若从左子树找就找最大的节点
(4) 用一个临时变量，将最小结点的值保存 temp = 12 
(5) 删除该最小结点 
(6) targetNode.value = temp
```

*BST的节点删除代码实现*

```java
package com.bytedance.binarysorttree;

public class BinarySortTreeDemo {

	public static void main(String[] args) {
		// 测试
		int[] array = { 7, 3, 10, 12, 5, 1, 9, 2 };
		BinarySortTree binarySortTree = new BinarySortTree();
		// 循环的添加节点到BST
		for (int i = 0; i < array.length; i++) {
			binarySortTree.add(new Node(array[i]));
		}
		// 中序遍历BST
		System.out.println("中序遍历BST:");
		binarySortTree.infixOrder();

		// 测试删除叶子节点
		binarySortTree.delNode(2);
		System.out.println("删除节点后:");
		binarySortTree.infixOrder();

		// 测试只有一颗子树的节点(删1)
		binarySortTree.delNode(1);
		System.out.println("删除后的结果:");
		binarySortTree.infixOrder();

		// 测试删除两颗子树的节点
		binarySortTree.delNode(10);
		System.out.println("删除后的结果:");
		binarySortTree.infixOrder();
	}

}

//创建BST
class BinarySortTree {
	private Node root;// 根节点

	// 查找带删除的节点
	public Node search(int value) {
		if (root == null) {
			return null;
		} else {
			return root.search(value);
		}
	}

	// 查找父节点
	public Node searchParent(int value) {
		if (root == null) {
			return null;
		} else {
			return root.searchParent(value);
		}
	}

	// 定义方法
	/**
	 * 
	 * @param node 传入的节点作为BST的根节点
	 * @return 返回以node为BST根节点的最小节点的值
	 */
	public int delRightTreeMin(Node node) {
		Node target = node;
		// 循环查找左子节点
		while (target.left != null) {
			target = target.left;
		}
		// target指向了最小节点,将其删除
		delNode(target.value);
		return target.value;
	}

	// 删除节点
	public void delNode(int value) {
		if (root == null) {
			return;
		} else {
			// 找到待删除的节点targetNode
			Node targetNode = search(value);
			if (targetNode == null) {
				return;// 如果没找到就返回
			}
			// 如果targetNode没有父节点，即可能删除的就是整颗树就是根节点，或者只有一个节点的树
			if (root.left == null && root.right == null) {
				root = null;// 直接指控即可
				return;
			}
			// 查找targetNode的父节点
			Node parent = searchParent(value);
			// 如果待删除的节点是叶子节点
			if (targetNode.left == null && targetNode.right == null) {
				// 判断targetNode是父节点的左子节点还是右子节点
				if (parent.left != null && parent.left.value == value) {
					// 说明是左子节点
					parent.left = null;
				} else if (parent.right != null && parent.right.value == value) {
					// 说明是右子节点
					parent.right = null;
				}
			} else if (targetNode.left != null && targetNode.right != null) {
				// 说明tagetNode是有2个子树的节点
				int minValue = delRightTreeMin(targetNode.right);
				targetNode.value = minValue;

			} else {
				// 删除仅有一颗子树的节点
				// 如果待删除的节点有左子节点
				if (parent != null) {//加这个判断的原因是当删除到只剩2个节点的时候删除在进行删除的话可能删除的是这两个节点形成的二叉树的root节点，若想删除root只需使root指向其节点即可如root=targetNode.left
					if (targetNode.left != null) {
						if (parent.left.value == value) {
							parent.left = targetNode.left;
						} else {
							// targetNode是parent的右子节点
							parent.right = targetNode.left;
						}
					} else {
						root = targetNode.left;
					}
				} else {
					// 待删除的节点有右子节点
					// targetNode是parent的左子节点
					if (parent != null) {
						if (parent.left.value == value){
							parent.left = targetNode.right;
						}else {
							// targetNode是parent的右子节点
							parent.right = targetNode.right;
						}
					  } else {
						root = targetNode.right;
					}
				}
			}
		}

	}
	public void add(Node node) {
		if (root == null) {
			root = node;// root为空传入的node直接指向root
		} else {
			root.add(node);
		}
	}

	// 中序遍历
	public void infixOrder() {
		if (root != null) {
			root.infixOeder();
		} else {
			System.out.println("BST为空无法遍历!");
		}
	}
}

//创建节点
class Node {
	int value;
	Node left;
	Node right;

	public Node(int value) {
		this.value = value;
	}

	// 查找要删除的节点
	/**
	 * 
	 * @param value 待删除节点的值
	 * @return 找到就返回该节点，否则返回空
	 */
	public Node search(int value) {
		if (value == this.value) {// 找到该节点
			return this;
		} else if (value < this.value) {
			// 查找的值小于当前节点则向左子树递归查找
			// 如果左子节点为空则不能找了
			if (this.left == null) {
				return null;
			}
			return this.left.search(value);
		} else {
			// 查找的值不小于当前节点则向右子树递归查找
			if (this.right == null) {
				return null;
			}
			return this.right.search(value);
		}
	}

	// 查找待删除节点的父节点
	/**
	 * @param value 要找的结点的值
	 * @return 返回的是待删除节点的父节点，没有就返回null
	 */
	public Node searchParent(int value) {
		// 表示找到了待删除结点的父节点
		if ((this.left != null && this.left.value == value) || (this.right != null && this.right.value == value)) {
			return this;
		} else {
			// 若查找的值小于当前节点的值，并且当前节点的左子节点不为空
			if (value < this.value && this.left != null) {
				// 向左子树递归查找
				return this.left.searchParent(value);
			} else if (value >= this.value && this.right != null) {
				// 向右子树递归查找
				return this.right.searchParent(value);
			} else {
				// 没有找到父节点
				return null;
			}
		}
	}

	@Override
	public String toString() {
		return "Node [value=" + value + "]";
	}

	// 添加节点的方法
	// 递归添加节点
	public void add(Node node) {
		if (node == null) {
			return;
		}
		// 判断传入的节点和当前子树的根节点的值作比较
		if (node.value < this.value) {
			// 若当前节点的左子节为空就将传入的节点挂在此节点左边
			if (this.left == null) {
				this.left = node;
			} else {// 不为空就继续递归向左子树加入节点
				this.left.add(node);
			}
		} else {
			// 添加的结点的值大于当前节点的值，即子树的根节点
			if (this.right == null) {
				this.right = node;
			} else {
				// 递归向右子树添加节点
				this.right.add(node);
			}
		}
	}

	// 中序遍历方法
	public void infixOeder() {
		if (this.left != null) {
			this.left.infixOeder();
		}
		System.out.println(this);
		if (this.right != null) {
			this.right.infixOeder();
		}
	}
}
```

## 22. 平衡二叉树(AVL)

![image-20200311123750912](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200311123750912.png)

```java
案例(说明二叉排序树可能的问题) 
给定一个数列{1,2,3,4,5,6}，要求创建一颗二叉排序树(BST), 分析问题所在.
1 2 3 4 5 6 上面BST 存在的问题分析:
(1) 左子树全部为空，从形式上看，更像一个单链表. 
(2) 插入速度没有影响 
(3) 查询速度明显降低(因为需要依次比较), 不能发挥BST 的优势，因为每次还需要比较左子树，其查询速度比单链表还慢
(4) 解决方案-平衡二叉树(AVL)
```

![image-20200311124259893](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200311124259893.png)

```java
平衡二叉树 基本介绍:
(1) 平衡二叉树也叫平衡二叉搜索树（Self-balancing binary search tree）又被称为 AVL树， 可以保证查询效率高。
(2) 具有以下特点：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1 ，并且左右两个子树都是一棵平衡二叉树。     平衡二叉树的常用实现方法有 红黑树、AVL(一种算法)、替罪羊树、Treap、伸展树等。

(3)：
    如上图所示第一颗树的左子树比右子树高，左子树的高度为1，而整棵树的高度是2
    第三个图：左子树比右子树高，左子树的高度为2，整棵树的高度是3
```

### 1. AVL树左旋

```java
应用案例-单旋转(左旋转) 
要求: 给你一个数列，创建出对应的平衡二叉树.数列 {4,3,6,5,7,8}
```

![AVL树](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\AVL树.png)

### 2. AVL树右旋

```java
应用案例-单旋转(右旋转) 
要求: 给你一个数列，创建出对应的平衡二叉树.数列 {10,12, 8, 9, 7, 6}
```

![AVL右旋](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\AVL右旋2.png)

### 3.AVL树双旋

```java
应用案例-双旋转
前面的两个数列，进行单旋转(即一次旋转)就可以将非平衡二叉树转成平衡二叉树, 但是在某些情况下，单旋转不能完成平衡二叉树的转换。
比如数列 int[] arr = { 10, 11, 7, 6, 8, 9 }; 
运行原来的代码可以看到，并没有转成 AVL树. int[] arr = {2,1,6,5,7,3}; // 运行原来的代码可以看到，并没有转成 AVL树 
```

![image-20200311192228501](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200311192228501.png)

```java
上图符合右旋转的规则，但是右旋过后发现仍然不是AVL树，观察原BST的图发现，原BST的左节点的那颗左子树即以节点7为根节点的二叉树的右子树是高于左子树的，此时需要对以7为根节点的这颗子二叉树进行左旋，旋转过后重新形成的整颗大BST进行右旋，最终会得到一个AVL树
```

![image-20200311200106687](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200311200106687.png)

![image-20200311200444248](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200311200444248.png)

*AVL树旋转综合代码*

```java
package com.bytedance.avl;

public class AVLTreeDemo {
	public static void main(String[] args) {
		int[] arr = {4,3,6,5,7,8};
		//int[] arr = { 10, 12, 8, 9, 7, 6 };
		//int[] arr = { 10, 11, 7, 6, 8, 9 };
		// 创建一个 AVLTree对象
		AVLTree avlTree = new AVLTree();
		// 添加结点
		for (int i = 0; i < arr.length; i++) {
			avlTree.add(new Node(arr[i]));
		}

		// 遍历
		System.out.println("中序遍历");
		avlTree.infixOrder();

		System.out.println("平衡处理后~~");
		System.out.println("树的高度=" + avlTree.getRoot().height()); // 3
		System.out.println("树的左子树高度=" + avlTree.getRoot().leftHeight()); // 2
		System.out.println("树的右子树高度=" + avlTree.getRoot().rightHeight()); // 2
		System.out.println("当前的根结点=" + avlTree.getRoot());// 8

	}

}

// 创建AVLTree
class AVLTree {
	private Node root;

	public Node getRoot() {
		return root;
	}

	// 查找要删除的结点
	public Node search(int value) {
		if (root == null) {
			return null;
		} else {
			return root.search(value);
		}
	}

	// 查找父结点
	public Node searchParent(int value) {
		if (root == null) {
			return null;
		} else {
			return root.searchParent(value);
		}
	}

	// 编写方法:
	// 1. 返回的 以node 为根结点的二叉排序树的最小结点的值
	// 2. 删除node 为根结点的二叉排序树的最小结点
	/**
	 * 
	 * @param node 传入的结点(当做二叉排序树的根结点)
	 * @return 返回的 以node 为根结点的二叉排序树的最小结点的值
	 */
	public int delRightTreeMin(Node node) {
		Node target = node;
		// 循环的查找左子节点，就会找到最小值
		while (target.left != null) {
			target = target.left;
		}
		// 这时 target就指向了最小结点
		// 删除最小结点
		delNode(target.value);
		return target.value;
	}

	// 删除结点
	public void delNode(int value) {
		if (root == null) {
			return;
		} else {
			// 1.需求先去找到要删除的结点 targetNode
			Node targetNode = search(value);
			// 如果没有找到要删除的结点
			if (targetNode == null) {
				return;
			}
			// 如果我们发现当前这颗二叉排序树只有一个结点
			if (root.left == null && root.right == null) {
				root = null;
				return;
			}

			// 去找到targetNode的父结点
			Node parent = searchParent(value);
			// 如果要删除的结点是叶子结点
			if (targetNode.left == null && targetNode.right == null) {
				// 判断targetNode 是父结点的左子结点，还是右子结点
				if (parent.left != null && parent.left.value == value) { // 是左子结点
					parent.left = null;
				} else if (parent.right != null && parent.right.value == value) {// 是由子结点
					parent.right = null;
				}
			} else if (targetNode.left != null && targetNode.right != null) { // 删除有两颗子树的节点
				int minVal = delRightTreeMin(targetNode.right);
				targetNode.value = minVal;

			} else { // 删除只有一颗子树的结点
				// 如果要删除的结点有左子结点
				if (targetNode.left != null) {
					if (parent != null) {
						// 如果 targetNode 是 parent 的左子结点
						if (parent.left.value == value) {
							parent.left = targetNode.left;
						} else { // targetNode 是 parent 的右子结点
							parent.right = targetNode.left;
						}
					} else {
						root = targetNode.left;
					}
				} else { // 如果要删除的结点有右子结点
					if (parent != null) {
						// 如果 targetNode 是 parent 的左子结点
						if (parent.left.value == value) {
							parent.left = targetNode.right;
						} else { // 如果 targetNode 是 parent 的右子结点
							parent.right = targetNode.right;
						}
					} else {
						root = targetNode.right;
					}
				}

			}

		}
	}

	// 添加结点的方法
	public void add(Node node) {
		if (root == null) {
			root = node;// 如果root为空则直接让root指向node
		} else {
			root.add(node);
		}
	}

	// 中序遍历
	public void infixOrder() {
		if (root != null) {
			root.infixOrder();
		} else {
			System.out.println("二叉排序树为空，不能遍历");
		}
	}
}

// 创建Node结点
class Node {
	int value;
	Node left;
	Node right;

	public Node(int value) {

		this.value = value;
	}

	// 返回左子树的高度
	public int leftHeight() {
		if (left == null) {
			return 0;
		}
		return left.height();
	}

	// 返回右子树的高度
	public int rightHeight() {
		if (right == null) {
			return 0;
		}
		return right.height();
	}

	// 返回 以该结点为根结点的树的高度
	public int height() {
		//树的高度是左子树和右子树的两者间的高度的最大值加1
		return Math.max(left == null ? 0 : left.height(), right == null ? 0 : right.height()) + 1;
	}

	// 左旋转方法
	private void leftRotate() {

		// 创建新的节点,以当前根结点的值创建的
		Node newNode = new Node(value);
		// 把新的节点的左子树设置成当前节点的左子树
		newNode.left = left;
		// 把新的节点的右子树设置成带你过去节点的右子树的左子树
		newNode.right = right.left;
		// 把当前节点的值替换成右子节点的值
		value = right.value;
		// 把当前节点的右子树设置成当前节点右子树的右子树
		right = right.right;
		// 把当前节点的左子树(左子结点)设置成新的节点
		left = newNode;

	}

	// 右旋转
	private void rightRotate() {
		Node newNode = new Node(value);
		newNode.right = right;
		newNode.left = left.right;
		value = left.value;
		left = left.left;
		right = newNode;
	}

	// 查找要删除的结点
	/**
	 * 
	 * @param value 希望删除的结点的值
	 * @return 如果找到返回该结点，否则返回null
	 */
	public Node search(int value) {
		if (value == this.value) { // 找到就是该结点
			return this;
		} else if (value < this.value) {// 如果查找的值小于当前结点，向左子树递归查找
			// 如果左子结点为空
			if (this.left == null) {
				return null;
			}
			return this.left.search(value);
		} else { // 如果查找的值不小于当前结点，向右子树递归查找
			if (this.right == null) {
				return null;
			}
			return this.right.search(value);
		}

	}

	// 查找要删除结点的父结点
	/**
	 * 
	 * @param value 要找到的结点的值
	 * @return 返回的是要删除的结点的父结点，如果没有就返回null
	 */
	public Node searchParent(int value) {
		// 如果当前结点就是要删除的结点的父结点，就返回
		if ((this.left != null && this.left.value == value) || (this.right != null && this.right.value == value)) {
			return this;
		} else {
			// 如果查找的值小于当前结点的值, 并且当前结点的左子结点不为空
			if (value < this.value && this.left != null) {
				return this.left.searchParent(value); // 向左子树递归查找
			} else if (value >= this.value && this.right != null) {
				return this.right.searchParent(value); // 向右子树递归查找
			} else {
				return null; // 没有找到父结点
			}
		}

	}

	@Override
	public String toString() {
		return "Node [value=" + value + "]";
	}

	// 添加结点的方法
	// 递归的形式添加结点，注意需要满足二叉排序树的要求
	public void add(Node node) {
		if (node == null) {
			return;
		}

		// 判断传入的结点的值，和当前子树的根结点的值关系
		if (node.value < this.value) {
			// 如果当前结点左子结点为null
			if (this.left == null) {
				this.left = node;
			} else {
				// 递归的向左子树添加
				this.left.add(node);
			}
		} else { // 添加的结点的值大于 当前结点的值
			if (this.right == null) {
				this.right = node;
			} else {
				// 递归的向右子树添加
				this.right.add(node);
			}

		}

		// 当添加完一个结点后,如果: (右子树的高度-左子树的高度) > 1  则左旋转
		if (rightHeight() - leftHeight() > 1) {
			// 如果它的右子树的左子树的高度大于它的右子树的右子树的高度
			if (right != null && right.leftHeight() > right.rightHeight()) {
				// 先对右子结点进行右旋转
				right.rightRotate();
				// 然后在对当前结点进行左旋转
				leftRotate(); // 左旋转..
			} else {
				// 直接进行左旋转即可
				leftRotate();
			}
			return; // 必须要!!条件满足了就return 否则会继续执行下面的代码无线做，毫无意义
		}

		// 当添加完一个结点后,如果 (左子树的高度 - 右子树的高度) > 1,右旋转
		if (leftHeight() - rightHeight() > 1) {
			// 如果它的左子树的右子树高度大于它的左子树的左子树高度
			if (left != null && left.rightHeight() > left.leftHeight()) {
				// 先对当前结点的左结点(左子树)->左旋转
				left.leftRotate();
				// 再对当前结点进行右旋转
				rightRotate();
			} else {
				// 直接进行右旋转即可
				rightRotate();
			}
		}
	}

	// 中序遍历
	public void infixOrder() {
		if (this.left != null) {
			this.left.infixOrder();
		}
		System.out.println(this);
		if (this.right != null) {
			this.right.infixOrder();
		}
	}

}
```

## 23. 多路查找树（多叉树）

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\二叉树的缺点.png)

```java
上图是对二叉树(虽然二叉树的操作效率高)出现的问题分析：二叉树只有左右2个节点，每个节点只能存一个数据项，一旦数据量太大，就会造成树的高度过大
```

```java
多叉树：
(1) 在二叉树中，每个节点有数据项，最多有两个子节点。如果允许每个节点可以有更多的数据项和更多的子节点， 就是多叉   树（multiway tree）
(2)  2-3 树，2-3-4 树就是多叉树，多叉树通过重新组织节点，减少树的高度，能对二叉树进行优化//如下图
```

![image-20200312130758942](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200312130758942.png)

### 1. 2-3树（B树的一种）

```java
2-3树(3阶B树)基本介绍 
2-3树是最简单的B树结构, 具有如下特点:
(1) 2-3树的所有叶子节点都在同一层.//(只要是B树都满足这个条件) 
(2) 有两个子节点的节点叫二节点，二节点要么没有子节点，要么有两个子节点. 
(3) 有三个子节点的节点叫三节点，三节点要么没有子节点，要么有三个子节点. 
(4) 2-3树是由二节点和三节点构成的树
```

![2-3树](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\2-3树.png)

### 2. B树和B+树

```java
一.B树的说明:(平衡多路搜索树) //英文 balanced Tree 非常平衡的树结构
(1) B树的阶：节点的最多子节点个数。比如2-3树的阶是3，2-3-4树的阶是4
(2) B树的搜索，从根结点开始，对结点内的关键字（有序）序列进行二分查找，如果命中则结束，否则进入查询关键字所属       范围的儿子结点；重复，直到所对应的儿子指针为空，或已经是叶子结点
(3) 关键字集合分布在整颗树中, 即叶子节点和非叶子节点都存放数据.
(4) 搜索有可能在非叶子结点结束 
(5) 其搜索性能等价于在关键字全集内做一次二分查找
(6) B树特点：
    a.一个节点可以存储超过2个元素，可以拥有超过2个子节点。
    b.拥有二叉树的一些性质。
    c.平衡，每个节点的所有子树高度一致。
    d.比较矮。
 二. m阶B树的性质（m >= 2）
    假设一个节点存储的元素个数为x
     1.根节点个数：1 <= x <= m-1，三阶B树根节点数大于等于1并且小于等于2。
     2.非根节点：(m / 2)(向上取整) - 1 <= x <= m - 1
       // ⌈m/2⌉  -1<=x<=m-1 
       //向上取整, 运算称为 Ceiling，用数学符号 ⌈ ⌉  （上有起止，开口向下）表示,。
       //向下取整, 运算称为 Floor，用数学符号 ⌊ ⌋ （下有起止，开口向上）表示。
     3.如果有子节点，子节点个数 y = x + 1
       a.如果此节点是根节点,则其子节点的个数范围：2 <= y <= m
       b.如果此节点是非根节点,则其子节点的个数范围：(m / 2)(向上取整) <= y <= m  
       比如 m=3,2<=y<=3,因此可以称为 (2,3) 树,2-3树
       比如 m=4,2<=y<=4,因此可以称为 (2,4) 树,2-3-4树
       比如 m=5,3<=y<=5,因此可以称为 (3,5) 树
       比如 m=6,3<=y<=6,因此可以称为 (3,6) 树
       比如 m=7,4<=y<=7,因此可以称为 (4,7) 树
       数据库中的B树是200~300阶,即一个节点存储200~300个数据
 三. B树 VS 二叉搜索树
    1.将二叉搜索树的节点合并，可以成为B树。
    2.多代（父和子）节点合并，可以获得一个超级节点（类似3阶B树中的18和33节点，23和30节点）。
      2代合并的超级节点，最多拥有4个子节点（至少是4阶B树）。
       //如18 12 13合并后的超级节点有4个子节点,18 12 合并后的超级节点有3个子节点
      3代合并的超级节点，最多拥有8个子节点（至少是8阶B树）。
      n代合并的超级节点，最多拥有2^n个子节点（至少是2^n阶B树）
    3.m阶B树,最多需要log2^m代合并
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\B树.png)

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\二叉树-3阶B树.jpg)

**B树添加元素**（新添加的元素必定是添加到 `叶子节点`）

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\B树添加元素.jpg)

**添加元素上溢解决方法**

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\B树添加元素导致上溢 .jpg)

**删除元素以及下溢解决方法**

```java
1.假如需要删除的元素在叶子节点中，那么直接删除即可
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\B树删除元素.jpg)

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\B树删除元素下溢解决.jpg)

```java
B+树的说明: 
(1) B+树的搜索与B树也基本相同，区别是B+ 树只有达到叶子结点才命中（B
    树可以在非叶子结点命中），其性能也等价于在关键字全集做一次二分查找
(2) 所有关键字都出现在叶子结点的链表中 （即数据只能在叶子节点【也叫稠密索引】 ），且链表中的关键字(数据)恰好是     有序的。
(3) 不可能在非叶子结点命中 
(4) 非叶子结点相当于是叶子结点的索引（稀疏索引） ，叶子结点相当于是存储（关键字）数据的数据层
(5) 更适合文件索引系统 
(6) B树和B+树各有自己的应用场景，不能说B+树 完全比B树好，反之亦然.
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\B加树.png)

```java
算法为求解规模为nxxxxxxxxxx 为什么要有图(1) 之前的线性表和树: 线性表(如单链表)局限于一个直接前驱和一个直接后继的关系(2) 树也只能有一个直接前驱也就是父节点 (3) 当需要表示多对多的关系时，就用到了图java
```

```java
图是一种数据结构，其中结点可以具有零个或多个相邻元素。两个结点之间的连接 称为边。 结点也可以称为顶点。如图：
```

![image-20200313114517131](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200313114517131.png)

```java
图的常用概念
(1) 顶点(vertex) (2) 边(edge) (3) 路径 (4) 无向图如下图
```

![image-20200313114926945](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200313114926945.png)

```java
(1) 有向图 (2) 带权图 如下图所示
```

![image-20200313115304036](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200313115304036.png)

###  3. 红黑树

****

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\红黑树.jpg)

```java
一.红黑树也是一种自平衡的二叉搜索树，也曾叫做平衡二叉B树。//红黑树是真二叉树
红黑树必须满足以下5条性质：

1.节点是RED或者BLACK 
2.根节点是BLACK
3.叶子节点（外部节点，空节点）都是BLACK
4.RED节点的子节点都是BLACK
  a.RED节点的parent都是BLACK
  b.从根节点到叶子节点的所有路径上不能有2个连续的RED节点
5.从任意节点到叶子节点的所有路径都包含相同数目的BLACK节点。
6.在添加和删除节点之后，让树依然满足以上5条性质，就可以保证平衡。

二.红黑树的等价变化
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\红黑树性质.jpg)

```java
如果上图(红黑树VS2-3-4树)最底层的BLACK节点不存在，那么整颗B树只有一个节点，而且是超级节点
红黑树节点间的关系:
父节点（parent） 55是38和80的父节点，38是25和46的父节点。
兄弟节点（sibling）25和46是兄弟节点，76和88是兄弟节点。
叔父节点（parent的兄弟节点）25和46的叔父节点是80。
祖父节点（parent的父节点）25的祖父节点是55。
```

```java

```



## 24. 数据结构——图

```java
图的表示方式有两种：二维数组表示（邻接矩阵）；链表表示（邻接表）
```

```java
邻接矩阵是表示图形中顶点之间相邻关系的矩阵，对于n 个顶点的图而言，矩阵是的row和col表示的是1....n个点。如下图:
无向图一共5个节点转换为5*5的矩阵，，矩阵中的行和列之间的元素关系为0或1，0表示两个元素之间不能直接联通，1表示可以直接联通！！！
```

![image-20200313115828841](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200313115828841.png)

```java
邻接表:
(1) 邻接矩阵需要为每个顶点都分配n个边的空间，其实有很多边都是不存在, 会造成空间的一定损失.
(2) 邻接表的实现只关心存在的边，不关心不存在的边。因此没有空间浪费，邻接 表由数组+链表组成
    
说明: 
(1) 标号为0的结点的相关联的结点为 1 2 3 4 即可以直接互联的点
(2) 标号为1的结点的相关联结点为0 4 ，
(3) 标号为2的结点相关联的结点为 0 4 5
```

![image-20200313121416843](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200313121416843.png)

### 1. 图的创建代码实现

```java
  A B C D E  
A 0 1 1 0 0
B 1 0 1 1 1           
C 1 1 0 0 0 
D 0 1 0 0 0 
E 0 1 0 0 0

思路分析 (1) 存储顶点String 使用 ArrayList容器 (2) 保存矩阵使用二维数组 int[][] edges 
//说明  1 表示能够直接连接  0 表示不能直接连接
```

```java
package com.bytedance.graph;

import java.util.ArrayList;
import java.util.Arrays;;

public class Graph {
	// 存储顶点利用ArrayList结合
	private ArrayList<String> vertexList;
	// 使用二维数组保存邻接矩阵
	private int[][] edges;
	// 表示边的个数
	private int numOfEdges;

	public static void main(String[] args) {
		// 测试图是否正确
		int n = 5;// 节点个数
		// 得到节点的值
		String Vertex[] = { "A", "B", "C", "D", "E" };
		// 创建图对象
		Graph graph = new Graph(n);
		// 循环添加顶点
		for (String vertex : Vertex) {
			graph.insertVertex(vertex);
		}

		// 添加边
		// A-B A-C 直接相连 B-C B-D B-E 是互联的
		graph.insertEdge(0, 1, 1);// A-B
		graph.insertEdge(0, 2, 1);// A-C
		graph.insertEdge(1, 2, 1);// B-C
		graph.insertEdge(1, 3, 1);// B-D
		graph.insertEdge(1, 4, 1);// B-E
		// 显示创建好的图
		graph.showGraph();
	}

	// 构造器
	public Graph(int n) {// 传入顶点
		// 初始化矩阵与ArrayList
		edges = new int[n][n];
		vertexList = new ArrayList<String>(n);
		// 边默认初始化为0
		numOfEdges = 0;
	}

	// 常用方法
	// 返回节点个数
	public int getNumOfVertex() {
		return vertexList.size();
	}

	// 显示图对应的矩阵
	public void showGraph() {
		for (int[] link : edges) {
			System.out.println(Arrays.toString(link));
		}
	}

	// 返回边的个数
	public int getNumOfEdges() {
		return numOfEdges;
	}

	// 返回节点i(i为下标)对应的数据
	public String getValueByIndex(int i) {
		return vertexList.get(i);
	}

	// 返回 v1 v2的权值
	public int getWeight(int v1, int v2) {
		return edges[v1][v2];
	}

	// 插入顶点方法
	public void insertVertex(String vertex) {
		vertexList.add(vertex);
	}

	// 添加边
	/**
	 * @param v1     下标，表示第几个顶点，如第一个顶点A下标0
	 * @param v2     另一个顶点的下标
	 * @param weight 表示两个顶点之间的关系，如直连通的话weight就是1不通就是0
	 */
	public void insertEdge(int v1, int v2, int weight) {
		edges[v1][v2] = weight;
		edges[v2][v1] = weight;
		numOfEdges++;
	}
}


运行结果:
[0, 1, 1, 0, 0]
[1, 0, 1, 1, 1]
[1, 1, 0, 0, 0]
[0, 1, 0, 0, 0]
[0, 1, 0, 0, 0]

```

### 2. 图的深度优先(DFS)算法

```java
图的深度优先遍历介绍:
所谓图的遍历，即是对结点的访问。一个图有那么多个结点，如何遍历这些结点，需要特定策略，一般有两种访问策略: 
(1)深度优先遍历 (2)广度优先遍历
    
深度优先遍历基本思想
图的深度优先搜索(Depth First Search) 。 
(1)深度优先遍历，从初始访问结点出发，初始访问结点可能有多个邻接结点，深度优先遍历的策略就是首先访问第一个邻接    结点，然后再以这个被访问的邻接 结点作为初始结点，访问它的第一个邻接结点，可以这样理解：每次都在访问完当前结    点后首先访问 当前结点的第一个邻接结点。
(2)这样的访问策略是优先往纵向挖掘深入，而不是对一个结点的所有邻接结点进行横向访问。
(3) 显然，深度优先搜索是一个递归的过程
    
    
深度优先遍历算法步骤 
(1) 访问初始结点v，并标记结点v为已访问。 
(2) 查找结点v的第一个邻接结点w。 
(3) 若w存在，则继续执行4，如果w不存在，则回到第1步，将从v的下一个结点继续。
(4) 若w未被访问，对w进行深度优先遍历递归（即把w当做另一个v，然后进行步骤123）。
(5) 查找结点v的w邻接结点的下一个邻接结点，转到步骤3。
```

![image-20200313165735434](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200313165735434.png)

```java
package com.bytedance.graph;

import java.util.ArrayList;
import java.util.Arrays;;

public class Graph {
	// 存储顶点利用ArrayList结合
	private ArrayList<String> vertexList;
	// 使用二维数组保存邻接矩阵
	private int[][] edges;
	// 表示边的个数
	private int numOfEdges;
	// 定义数组boolean[]记录某个顶点是否被访问过
	private boolean[] isVisited;

	public static void main(String[] args) {
		// 测试图是否正确
		int n = 5;// 节点个数
		// 得到节点的值
		String Vertex[] = { "A", "B", "C", "D", "E" };
		// 创建图对象
		Graph graph = new Graph(n);
		// 循环添加顶点
		for (String vertex : Vertex) {
			graph.insertVertex(vertex);
		}

		// 添加边
		// A-B A-C 直接相连 B-C B-D B-E 是互联的
		graph.insertEdge(0, 1, 1);// A-B
		graph.insertEdge(0, 2, 1);// A-C
		graph.insertEdge(1, 2, 1);// B-C
		graph.insertEdge(1, 3, 1);// B-D
		graph.insertEdge(1, 4, 1);// B-E
		// 显示创建好的图
		graph.showGraph();

		// 测试深度遍历
		System.out.println("深度遍历结果:");
		graph.dfs();
	}

	// 构造器
	public Graph(int n) {// 传入顶点
		// 初始化矩阵与ArrayList
		edges = new int[n][n];
		vertexList = new ArrayList<String>(n);
		// 边默认初始化为0
		numOfEdges = 0;
		isVisited = new boolean[5];
	}

	// 得到第一个邻接节点的下标
	/**
	 * 
	 * @param index
	 * @return 若存在就返回对应的下标，否则就返回-1
	 */
	public int getFirstNeighbor(int index) {// 传入一个节点下标才能得到该节点的下一个邻接节点的下标
		for (int j = 0; j < vertexList.size(); j++) {
			if (edges[index][j] > 0) {
				return j;
			}
		}
		return -1;
	}

	// 根据前一个邻接节点的下标获取下一个邻接节点的下标
	public int getNextNeighbor(int v1, int v2) {
		for (int j = v2 + 1; j < vertexList.size(); j++) {
			if (edges[v1][j] > 0) {
				return j;
			}
		}
		return -1;
	}

	// 深度优先遍历
	private void dfs(boolean[] isVisited, int i) {
		// 开始进来访问了一个节点，将其输出
		System.out.print(getValueByIndex(i) + "->");
		// 将该节点设为已访问过
		isVisited[i] = true;
		// 将以i为下标的节点继续访问下一个邻接节点
		int w = getFirstNeighbor(i);
		while (w != -1) {// 说明有邻接节点还需判断是否被访问过
			if (!isVisited[w]) {
				dfs(isVisited, w);
			}
			// 如果w节点已经被访问过,则访问w的下一个邻接节点
			w = getNextNeighbor(i, w);
		}
	}

	// 对dfs进行重载,遍历所有节点，进行深度遍历
	public void dfs() {
		// 遍历所有节点进行dfs
		for (int i = 0; i < getNumOfVertex(); i++) {
			if (!isVisited[i]) {
				dfs(isVisited, i);
			}
		}
	}

	// 常用方法
	// 返回节点个数
	public int getNumOfVertex() {
		return vertexList.size();
	}

	// 显示图对应的矩阵
	public void showGraph() {
		for (int[] link : edges) {
			System.out.println(Arrays.toString(link));
		}
	}

	// 返回边的个数
	public int getNumOfEdges() {
		return numOfEdges;
	}

	// 返回节点i(i为下标)对应的数据
	public String getValueByIndex(int i) {
		return vertexList.get(i);
	}

	// 返回 v1 v2的权值
	public int getWeight(int v1, int v2) {
		return edges[v1][v2];
	}

	// 插入顶点方法
	public void insertVertex(String vertex) {
		vertexList.add(vertex);
	}

	// 添加边
	/**
	 * @param v1     下标，表示第几个顶点，如第一个顶点A下标0
	 * @param v2     另一个顶点的下标
	 * @param weight 表示两个顶点之间的关系，如直连通的话weight就是1不通就是0
	 */
	public void insertEdge(int v1, int v2, int weight) {
		edges[v1][v2] = weight;
		edges[v2][v1] = weight;
		numOfEdges++;
	}
}
```

### 3. 图的广度优先(BFS)算法

```java
广度优先遍历基本思想 
图的广度优先搜索(Broad First Search) 。
类似于一个分层搜索的过程，广度优先遍历需要使用一个队列以保持访问过的结点的顺序， 以便按这个顺序来访问这些结点的邻接结点
    
    
广度优先遍历算法步骤 
(1) 访问初始结点v并标记结点v为已访问。 
(2) 结点v入队列 
(3) 当队列非空时，继续执行，否则算法结束(针对的是加完的v)。 
(4) 出队列，取得队头结点u。 
(5) 查找结点u的第一个邻接结点w。 
(6) 若结点u的邻接结点w不存在，则转到步骤3；否则循环执行以下三个步骤： 
    6.1 若结点w尚未被访问，则访问结点w并标记为已访问。 
    6.2 结点w入队列 
    6.3 查找结点u的继w邻接结点后的下一个邻接结点w，转到步骤6。
```

```java
package com.bytedance.graph;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;;

public class Graph {
	// 存储顶点利用ArrayList结合
	private ArrayList<String> vertexList;
	// 使用二维数组保存邻接矩阵
	private int[][] edges;
	// 表示边的个数
	private int numOfEdges;
	// 定义数组boolean[]记录某个顶点是否被访问过
	private boolean[] isVisited;

	public static void main(String[] args) {
		// 测试图是否正确
		int n = 5;// 节点个数
		// 得到节点的值
		String Vertex[] = { "A", "B", "C", "D", "E" };
		// 创建图对象
		Graph graph = new Graph(n);
		// 循环添加顶点
		for (String vertex : Vertex) {
			graph.insertVertex(vertex);
		}

		// 添加边
		// A-B A-C 直接相连 B-C B-D B-E 是互联的
		graph.insertEdge(0, 1, 1);// A-B
		graph.insertEdge(0, 2, 1);// A-C
		graph.insertEdge(1, 2, 1);// B-C
		graph.insertEdge(1, 3, 1);// B-D
		graph.insertEdge(1, 4, 1);// B-E
		// 显示创建好的图
		graph.showGraph();

		// 测试深度优先遍历
		System.out.println("深度遍历结果:");
		graph.dfs();
		System.out.println();
		// 测试广度优先
		System.out.println("广度优先遍历结果:");
		graph.bfs();
	}

	// 构造器
	public Graph(int n) {// 传入顶点
		// 初始化矩阵与ArrayList
		edges = new int[n][n];
		vertexList = new ArrayList<String>(n);
		// 边默认初始化为0
		numOfEdges = 0;
	}

	// 得到第一个邻接节点的下标
	/**
	 * 
	 * @param index
	 * @return 若存在就返回对应的下标，否则就返回-1
	 */
	public int getFirstNeighbor(int index) {// 传入一个节点下标才能得到该节点的下一个邻接节点的下标
		for (int j = 0; j < vertexList.size(); j++) {
			if (edges[index][j] > 0) {
				return j;
			}
		}
		return -1;
	}

	// 根据前一个邻接节点的下标获取下一个邻接节点的下标
	public int getNextNeighbor(int v1, int v2) {
		for (int j = v2 + 1; j < vertexList.size(); j++) {
			if (edges[v1][j] > 0) {
				return j;
			}
		}
		return -1;
	}

	// 深度优先遍历
	private void dfs(boolean[] isVisited, int i) {
		// 开始进来访问了一个节点，将其输出
		System.out.print(getValueByIndex(i) + "->");
		// 将该节点设为已访问过
		isVisited[i] = true;
		// 将以i为下标的节点继续访问下一个邻接节点
		int w = getFirstNeighbor(i);
		while (w != -1) {// 说明有邻接节点还需判断是否被访问过
			if (!isVisited[w]) {
				dfs(isVisited, w);
			}
			// 如果w节点已经被访问过,则访问w的下一个邻接节点
			w = getNextNeighbor(i, w);
		}
	}

	// 对dfs进行重载,遍历所有节点，进行深度遍历
	public void dfs() {
		isVisited = new boolean[vertexList.size()];
		// 遍历所有节点进行dfs
		for (int i = 0; i < getNumOfVertex(); i++) {
			if (!isVisited[i]) {
				dfs(isVisited, i);
			}
		}
	}

	// 广度优先遍历方法BFS
	/**
	 * @param isVisted 是否被访问过
	 * @param i        对哪个顶点进行广度优先遍历
	 */
	private void bfs(boolean[] isVisted, int i) {
		// 表示队列的头节点的下标
		int u;
		// 邻接节点
		int w;
		// 队列，记录界节点访问的顺序
		LinkedList queue = new LinkedList();
		// 访问节点
		System.out.print(getValueByIndex(i) + "->");
		// 标记为以访问
		isVisited[i] = true;
		// 将节点加入队列
		queue.addLast(i);

		while (!queue.isEmpty()) {// 队列非空就加
			// 取出队列的头节点下标
			u = (Integer) queue.removeFirst();
			// 得到第一个邻接节点的下标,看看是否能获取到
			w = getFirstNeighbor(u);
			// 说明找到了。可以连
			while (w != -1) {
				// 是否访问过
				if (!isVisited[w]) {
					System.out.print(getValueByIndex(w) + "->");
					// 标记为以访问
					isVisited[w] = true;
					// 入队列
					queue.addLast(w);
				}
				// 以u为前驱节点找w后面的下一个节点即意思为比如开始
				// 是从A找的找到了C，这个C已经访问过了，接着找C的下一个邻接节点D，通过A找D
				w = getNextNeighbor(u, w);// 体现广度优先的地方就在这
			}
		}
	}

	// 遍历所有节点均进行广度优先搜索
	public void bfs() {
		isVisited = new boolean[vertexList.size()];
		for (int i = 0; i < getNumOfVertex(); i++) {
			if (!isVisited[i]) {
				bfs(isVisited, i);
			}
		}
	}

	// 常用方法
	// 返回节点个数
	public int getNumOfVertex() {
		return vertexList.size();
	}

	// 显示图对应的矩阵
	public void showGraph() {
		for (int[] link : edges) {
			System.out.println(Arrays.toString(link));
		}
	}

	// 返回边的个数
	public int getNumOfEdges() {
		return numOfEdges;
	}

	// 返回节点i(i为下标)对应的数据
	public String getValueByIndex(int i) {
		return vertexList.get(i);
	}

	// 返回 v1 v2的权值
	public int getWeight(int v1, int v2) {
		return edges[v1][v2];
	}

	// 插入顶点方法
	public void insertVertex(String vertex) {
		vertexList.add(vertex);
	}

	// 添加边
	/**
	 * @param v1     下标，表示第几个顶点，如第一个顶点A下标0
	 * @param v2     另一个顶点的下标
	 * @param weight 表示两个顶点之间的关系，如直连通的话weight就是1不通就是0
	 */
	public void insertEdge(int v1, int v2, int weight) {
		edges[v1][v2] = weight;
		edges[v2][v1] = weight;
		numOfEdges++;
	}
}

广度优先遍历结果:
A->B->C->D->E->
	
```

## 25. 二分查找之非递归算法

```java
(1) 二分查找法只适用于从有序的数列中进行查找(比如数字和字母等) ，将数列排序后再进行查找
(2) 二分查找法的运行时间为对数时间O(㏒₂n) ，即查找到需要的目标位置最多 只需要㏒₂n步，假设从[0,99]的队列(100个     数，即n=100)中寻到目标数30 ，则需要查找步数为㏒₂100 , 即最多需要查找7次( 2^6 < 100 < 2^7)
```

```java
二分查找算法(非递归)代码实现: 数组 {1,3, 8, 10, 11, 67, 100}, 
```

```java
public class BinarySearchNoRecur {

	public static void main(String[] args) {
		// 测试
		int[] array = { 1, 3, 3, 8, 10, 11, 67, 100 };
		int index = binarySearch(array, 3);
		System.out.println(index);
	}

	// 二分查找非递归算法
	/**
	 * @param array  带查找数组
	 * @param target 待查找的数据
	 * @return 返回下标
	 */
	public static int binarySearch(int[] array, int target) {
		int left = 0;
		int right = array.length - 1;
		while (left <= right) {// 可以继续查找
			int mid = left + (right - left) / 2;
			if (array[mid] == target) {
				return mid;
			} else if (array[mid] > target) {
				right = mid - 1;// 向左面查找,将最右面的索引调整位置
			} else {
				left = mid + 1;// 向右查找
			}
		}
		return -1;
	}

}
```

## 26. 动态规划算法

```java
应用场景-背包问题
背包问题：有一个背包，容量为 4 磅,现有如下物品
(1) 要求达到的目标为装入的背包的总价值最大,并且重量不超出(01背包问题)    (2) 要求装入的物品不能重复
```

![image-20200313222317112](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200313222317112.png)

```java
动态规划算法介绍 
(1) 动态规划(Dynamic Programming)算法的核心思想是：将大问题划分为小问题进行解决从而一步步获取最优解的处理算法
(2)动态规划算法与分治算法类似，其基本思想也是将待求解问题分解成若干个子 问题，先求解子问题，然后从这些子问题的    解得到原问题的解。
(3) 与分治法不同的是，
    适合于用动态规划求解的问题，经分解得到子问题往往不是互相独立的。 ( 即下一个子阶段的求解是建立在上一个子阶段     的解的基础上，进行进一步的求 解 )
(4) 动态规划可以通过填表的方式来逐步推进，得到最优解
```

```java
背包问题主要是指一个给定容量的背包、若干具有一定价值和重量的物品，如何选 择物品放入背包使物品的价值最大。其中又分01背包和完全背包(完全背包指的是： 每种物品都有无限件可用) 这里的问题属于01背包，即每个物品最多放一个。而无限背包可以转化为01背包。
    
算法的主要思想，利用动态规划来解决。每次遍历到的第i个物品，根据w[i]和v[i] 来确定是否需要将该物品放入背包中。即对于给定的n个物品，设v[i]、w[i]分别为第i 个物品的价值和重量，C为背包的容量。再令v[i][j]表示在前i 个物品中能够装入容量为j的背包中的最大价值。则我们有下面的结果：
(1) v[i][0]=v[0][j]=0; //表示填入表第一行和第一列是0 
(2) 当w[i]> j 时：v[i][j]=v[i-1][j] // 当准备加入新增的商品的容量大于当前背包的容量时，就直接使用上一个单元     格的装入策略 
(3) 当j>=w[i]时： v[i][j]=max{v[i-1][j], v[i]+v[i-1][j-w[i]]}
    //j 表示待加入背包物品的重量，w[i]表示已经加入背包物品的重量
    //当准备加入的新增的商品的容量小于等于当前背包的容量,(说明可以尝试装一下) 
    //装入的方式: 
     v[i-1][j]： 就是上一个单元格的装入的最大值 
     v[i] : 表示当前商品的价值 v[i-1][j-w[i]] ：装入i-1商品，到剩余空间j-w[i]的最大值 //不一定装i-1个商品
     当j>=w[i]时： v[i][j]=max{v[i-1][j], v[i]+v[i-1][j-w[i]]} :
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\背包问题.png)

```java
package com.bytedance.dynamic;

public class KnapsackProblem {

	public static void main(String[] args) {
		// 物品重量
		int[] w = { 1, 4, 3 };
		// 物品价值
		int[] val = { 1500, 3000, 2000 };
		// 表示背包的容量
		int m = 4;
		// 物品的数量
		int n = val.length;
		// 定义二维数组记录物品放入的情况
		int[][] path = new int[n + 1][m + 1];
		// 定义二维数组
		// v[i][j] 表示在前i个物品中能够装入容量为j的背包中的最大值
		int[][] v = new int[n + 1][m + 1];
		// 初始化第一行和第一列
		for (int i = 0; i < v.length; i++) {
			v[i][0] = 0;// 将第一列设为0
		}
		for (int i = 0; i < v[0].length; i++) {
			v[0][i] = 0;// 将第一行设为0
		}

		// 进行动态规划处理
		for (int i = 1; i < v.length; i++) {// 跳过第一行
			for (int j = 1; j < v[0].length; j++) {// 跳过第一列
				if (w[i - 1] > j) {// 原来公式是从零开始的此处的W减去一即可
					v[i][j] = v[i - 1][j];
				} else {
					// 因为i是从1开始的所以下面的公式被改动
					// v[i][j] = Math.max(v[i - 1][j], val[i - 1] + v[i - 1][j - w[i - 1]]);

					// 记录物品存入背包的情况不能简单的利用公式，需要if-else进行处理
					if (v[i - 1][j] < val[i - 1] + v[i - 1][j - w[i - 1]]) {
						v[i][j] = val[i - 1] + v[i - 1][j - w[i - 1]];
						// 记录到path
						path[i][j] = 1;
					} else {
						v[i][j] = v[i - 1][j];
					}

				}
			}
		}
		for (int i = 0; i < v.length; i++) {
			for (int j = 0; j < v[i].length; j++) {
				System.out.print(v[i][j] + "" + "\t");
			}
			System.out.println();
		}
		// 输出放入的物品
		System.out.println("=================");
		int i = path.length - 1;// 行的最大下标
		int j = path[0].length - 1;// 列得最大下标
		while (i > 0 && j > 0) {// 从最后往前遍历
			if (path[i][j] == 1) {
				System.out.printf("第%d个物品放入到背包\n", i);
				j -= w[i - 1];// 放好的物品重量是i,背包剩余的容量就是w[i-1]
			}
			i--;// 找到另一个了，就继续往前找
		}

	}

}

运行结果:
0	0	0	0	0	
0	1500	1500	1500	1500	
0	1500	1500	1500	3000	
0	1500	1500	2000	3500	
=================
第3个物品放入到背包
第1个物品放入到背包

```

## 27.贪心算法(集合覆盖问题)

```java
贪心算法介绍 
(1) 贪婪算法(贪心算法)
    是指在对问题进行求解时，在每一步选择中都采取最好或者最优(即最有利) 的选择，从而希望能够导致结果是最好或者最     优的算法
(2) 贪婪算法所得到的结果不一定(考虑成本)是最优的结果(有时候会是最优解) ，但是都是相对近似(接近)最优解的结果
```

```java
贪心算法最佳应用-集合覆盖
(1) 假设存在如下表的需要付费的广播台，以及广播台信号可以覆盖的地区。 如何选择最少的广播台，让所有的地区都可以接     收到信号
```

![image-20200314182726909](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200314182726909.png)

```JAVA
(2) 思路分析: 如何找出覆盖所有地区的广播台的集合，使用穷举法实现, 列出每个可能的广播台的集合，这被称为幂      集。假设总的有n 个广播台，则广播台的组合总共有2ⁿ -1 个,假设每秒可以计算10个子集//当n的数量级特别大的时候-1可   忽略
```

![image-20200314183253952](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200314183253952.png)

```java
使用贪婪算法，则可以得到非常接近的解，并且效率高。选择策略上，因为需要覆 盖全部地区的最小集合: 
(1) 遍历所有的广播电台, 找到一个覆盖了最多未覆盖的地区的电台( 此电台可能包含一些已覆盖的地区，但没有关系）
    //举例：其他电台覆盖了大连，北京，上海，南京，等地区，而这个电台覆盖了其他更多这些电台没覆盖的地区             //而这个电台有可能覆盖了北京或上海地区等地区
(2) 将这个电台加入到一个集合中(比如ArrayList), 想办法把该电台覆盖的地区在下次比较时去掉。
(3) 重复第1步直到覆盖了全部的地区
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\贪心算法.png)

```java
package com.bytedance.greedy;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;

public class BreedyAlgorithm {

	public static void main(String[] args) {
		// 创建电台放入到哈希
		HashMap<String, HashSet<String>> broadcasts = new HashMap<String, HashSet<String>>();
		// 将电台放入 broadcasts
		HashSet<String> hashset1 = new HashSet<String>();
		hashset1.add("北京");
		hashset1.add("上海");
		hashset1.add("天津");

		HashSet<String> hashset2 = new HashSet<String>();
		hashset2.add("广州");
		hashset2.add("上海");
		hashset2.add("深圳");

		HashSet<String> hashset3 = new HashSet<String>();
		hashset3.add("成都");
		hashset3.add("上海");
		hashset3.add("杭州");

		HashSet<String> hashset4 = new HashSet<String>();
		hashset4.add("上海");
		hashset4.add("天津");

		HashSet<String> hashset5 = new HashSet<String>();
		hashset5.add("杭州");
		hashset5.add("大连");

		// 加入到Map
		broadcasts.put("k1", hashset1);
		broadcasts.put("k2", hashset2);
		broadcasts.put("k3", hashset3);
		broadcasts.put("k4", hashset4);
		broadcasts.put("k5", hashset5);

		// 存放所有城市
		HashSet<String> allCtiy = new HashSet<String>();
		allCtiy.add("北京");
		allCtiy.add("上海");
		allCtiy.add("天津");
		allCtiy.add("广州");
		allCtiy.add("深圳");
		allCtiy.add("成都");
		allCtiy.add("杭州");
		allCtiy.add("大连");

		// 创建ArrayList存放选择的集合
		ArrayList<String> select = new ArrayList<String>();

		// 定义临时集合存放遍历过程中电台覆盖的地区和当前还没有覆盖地区的交集
		HashSet<String> tempSet = new HashSet<String>();

		// 定义变量保存在一次遍历过程中能够覆盖最大未覆盖的地区的电台的K
		// 如果这个变量不为空则加入select
		String maxKey = null;
		while (allCtiy.size() != 0) {// 不为零表示还没有覆盖所有地区
			// 每进行一次循环就置空maxKey
			maxKey = null;
			// 遍历broadcasts
			// 取出对应电台的K
			for (String key : broadcasts.keySet()) {
				// 每进行一次for就清空一次
				tempSet.clear();
				// 当前这个K所能覆盖的地区
				HashSet<String> ctiy = broadcasts.get(key);
				tempSet.addAll(ctiy);
				// 求交集,将交集赋给tempSet
				tempSet.retainAll(allCtiy);
				// 如果这个集合包含的未覆盖地区的数量比maxKey指向的集合地区还多，需要重置maxKey
				if (tempSet.size() > 0 && (maxKey == null || tempSet.size() > broadcasts.get(maxKey).size())) {
					maxKey = key;
				}
			}
			// 如果 maxKey 不为空则将maxKey加入select集合
			if (maxKey != null) {
				select.add(maxKey);
				// 将masKey指向的电台所覆盖的地区从城市集合中删掉
				allCtiy.removeAll(broadcasts.get(maxKey));
			}
		}
		System.out.println("得到的选择结果=" + select);
	}

}
```

## 28. 普利姆(Prim)算法——最小生成树问题

```java
最小生成树
修路问题本质就是就是最小生成树问题，最小生成树(Minimum Cost Spanning Tree)，简称MST。 
(1) 给定一个带权的无向连通图,如何选取一棵生成树,使树上所有 边上权的总和为最小,这叫最小生成树
(2) N个顶点，一定有N-1条边 
(3) 包含全部顶点 
(4) N-1条边都在图中 
(5) 求最小生成树的算法主要是普里姆 算法和克鲁斯卡尔算法
```

![image-20200314215143566](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200314215143566.png)

```java
普里姆算法介绍
(1) 普利姆(Prim)算法求最小生成树，也就是在包含n个顶点的连通图中，找出只有( n-1)条边包含所有n个顶点的连通子图，     也就是所谓的极小连通子图
(2) 普利姆的算法如下:
(1) 设G=(V,E)是连通网，T=(U,D)是最小生成树，V,U是顶点集合，E,D是边的集合 (2) 若从顶点u开始构造最小生成树，则      从集合V中取出顶点u放入集合U中，标记顶点v的 visited[u]=1
(3) 若集合U中顶点ui与集合V-U中的顶点vj
    之间存在边，则寻找这些边中权值最小的边，但不能构成回路，将顶点vj加入集合U 中，将边（ui,vj）加入集合D中，标     记visited[vj]=1
(4) 重复步骤②，直到U与V相等，即所有顶点都被标记为访问过，此时D中有n-1条边
```

```java
普里姆算法——修路问题

(1) 有胜利乡有 7 个村庄(A, B, C, D, E, F, G) ，现在需要修路把 7 个村庄连通
(2) 各个村庄的距离用边线表示(权) ，比如 A – B 距离 5 公里 3) 问：如何修路保证各个村庄都能连通，并且总的修建公     路总里程最短
```

![image-20200314231159855](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200314231159855.png)

```java
import java.util.Arrays;

public class PrimAlgorithm {

	public static void main(String[] args) {
		// 测试图是否创建成功
		char[] data = new char[] { 'A', 'B', 'C', 'D', 'E', 'F', 'G' };
		int verxs = data.length;
		// 邻接矩阵,10000表示不连通
		int[][] weight = new int[][] { 
            { 10000, 5, 7, 10000, 10000, 10000, 2 },
            { 5, 10000, 10000, 9,10000, 10000, 3 },
		   { 7, 10000, 10000, 10000, 8, 10000, 10000 }, 
            { 10000, 9, 10000, 10000, 10000, 4,10000 },
		   { 10000, 10000, 8, 10000, 10000, 5, 4 }, 
            { 10000, 10000, 10000, 4, 5, 10000, 6 },
		   { 2, 3, 10000, 10000, 4, 6, 10000 }, };

		// 创建图对象
		MGraph graph = new MGraph(verxs);
		// 创建最小生成树对象
		MinTree minTree = new MinTree();
		minTree.creatGraph(graph, verxs, data, weight);
		// 输出
		minTree.showGraph(graph);
		System.out.println("==============");
		// 测试Prim算法
		minTree.prim(graph, 0);
	}

}

// 创建最小生成树
class MinTree {
	// 创建图的邻接矩阵
	/**
	 * @param graph  图对象
	 * @param verxs  图对应的顶点的个数
	 * @param data   图的顶点的值
	 * @param weight 图的邻接矩阵
	 */
	public void creatGraph(MGraph graph, int verxs, char data[], int[][] weight) {
		//
		int i, j;
		// 遍历顶点
		for (i = 0; i < verxs; i++) {
			// 将顶点的值传给图里面的顶点
			graph.data[i] = data[i];
			for (j = 0; j < verxs; j++) {
				// 初始化邻接矩阵
				graph.weight[i][j] = weight[i][j];
			}
		}
	}

	// 显示图的邻接矩阵
	public void showGraph(MGraph graph) {
		for (int[] link : graph.weight) {
			System.out.println(Arrays.toString(link));
		}
	}

	// 核心算法，得到最小生成树
	// graph()传入图 int v(从图的第几个顶点开始生成)
	public void prim(MGraph graph, int v) {

		// 标记顶点是否被访问过,
		int visited[] = new int[graph.verxs];
		// 将当前节点标记为以访问
		visited[v] = 1;
		// h1 h2记录两个顶点的下标
		int h1 = -1;
		int h2 = -2;
		// 初始化成一个大的数,后续遍历过程中，遇到小的值就将其替换
		int minWeight = 10000;
		// 生成边的条数为定点数减1条即graph.verxs-1
		for (int k = 1; k < graph.verxs; k++) {
			// 双层for循环的作用就是确定每次生成的子图，哪两个节点间的距离最近
			for (int i = 0; i < graph.verxs; i++) {// 记录已经访问过的节点
				for (int j = 0; j < graph.verxs; j++) {// 记录未访问过的节点
					if (visited[i] == 1 && visited[j] == 0 && graph.weight[i][j] < minWeight) {
						// 替换(寻找已经访问过的节点与为访问节点间的权值最小的边)
						minWeight = graph.weight[i][j];
						h1 = i;
						h2 = j;
					}
				}
			}
			// 当退出双层for循环后说明已经找到了权值最小的一条边
			System.out.println("边<" + graph.data[h1] + "," + graph.data[h2] + "> 权值:" + minWeight);
			// 将找到的节点标记为以访问
			visited[h2] = 1;
			// 重置minWeight
			minWeight = 10000;
		}
	}
}

// 创建图
class MGraph {
	// 图的节点个数
	int verxs;
	// 保存节点数据
	char[] data;
	// 邻接矩阵存放边
	int[][] weight;

	public MGraph(int verxs) {
		this.verxs = verxs;
		data = new char[verxs];
		weight = new int[verxs][verxs];
	}
}

测试结果:
//无论从哪个节点开始，但最终的最小生成树都是一样的，本代码大话就是25
边<A,G> 权值:2
边<G,B> 权值:3
边<G,E> 权值:4
边<E,F> 权值:5
边<F,D> 权值:4
边<A,C> 权值:7
```

## 29. 克鲁斯卡尔算法(Kruaskal)——最小生成树问题

```java
克鲁斯卡尔算法介绍
(1) 克鲁斯卡尔(Kruskal)算法，是用来求加权连通图的最小生成树的算法。 
(2) 基本思想：按照权值从小到大的顺序选择n-1条边，并保证这n-1 条边不构成回路
(3) 具体做法：首先构造一个只含n
    个顶点的森林，然后依权值从小到大从连通网中选择边加入到森林中，并使森 林中不产生回路，直至森林变成一棵树为止
```

```java
克鲁斯卡尔算法图解说明 
以城市公交站问题来图解说明 克鲁斯卡尔算法的原理和步骤：
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\克鲁斯卡尔.png)

```java
克鲁斯卡尔算法图解
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\克鲁斯卡尔算法.png)

```java
克鲁斯卡尔算法分析
    
根据前面介绍的克鲁斯卡尔算法的基本思想和做法，克鲁斯卡尔算法重点需要解决的以下两个问题： 
问题一 对图的所有边按照权值大小进行排序。 
问题二 将边添加到最小生成树中时，怎么样判断是否形成了回路。
问题一很好解决，采用排序算法进行排序即可。
问题二，处理方式是：记录顶点在"最小生成树"中的终点，顶点的终点是"在最小生成树中与它连通的最大顶点"。 然后每次需要将一条边添加到最小生存树时，判断该边的两个顶点的终点是否重合
```

```java
如何判断是否构成回路 -举例说明 (如图 )
```

![image-20200315150639206](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200315150639206.png)

```JAVA
关于终点的说明： 
(1) 就是将所有顶点按照从小到大的顺序排列好之后；某个顶点的终点就是"与它连通的最大顶点"。
(2) 因此，接下来，虽然<C,E>是权值最小的边。但是C和 E 的终点都是 F，即它们的终点相同，因此，将<C,E> 加入最小生     成树的话，会形成回路。这就是判断回路的方式。也就是说，我们加入的边的两个顶点不能都指向同一个终点，否则将构     成回路。
```

```java
克鲁斯卡尔最佳实践-公交站问题 
    
(1) 有北京有新增 7 个站点(A, B, C, D, E, F, G) ，现在需要修路把 7 个站点连通 
(2) 各个站点的距离用边线表示(权) ，比如 A – B 距离 12 公里 3) 
问：如何修路保证各个站点都能连通，并且总的修建公路总里程最短?
```

```java
package com.bytadance.kruskal;
import java.util.Arrays;

public class KrusKalCase {
    
	// 边的个数
	private int edgeNum;
	// 顶点数组
	private char[] vertexs;
	// 邻接矩阵
	private int[][] matrix;
	// 两个顶点不能联通
	private static final int INF = Integer.MIN_VALUE;

	public static void main(String[] args) {
		//测试邻接矩阵
		char[] vertexs= {'A','B','C','D','E','F','G'};
		int[][] matrix= {
				/*A*//*B*//*C*//*D*//*E*//*F*//*G*/
				/*A*/ { 0, 12, INF, INF, INF, 16, 14},
				/*B*/ { 12, 0, 10, INF, INF, 7, INF}, 
				/*C*/ { INF, 10, 0, 3, 5, 6, INF}, 
				/*D*/ { INF, INF, 3, 0, 4, INF, INF},
				/*E*/ { INF, INF, 5, 4, 0, 2, 8},
				/*F*/ { 16, 7, 6, INF, 2, 0, 9}, 
				/*G*/ { 14, INF, INF, INF, 8, 9, 0}
		};
		
		KrusKalCase krusKalCase = new KrusKalCase(vertexs, matrix);
		krusKalCase.print();

		// 测试边的权值排序
		// EData[] edges = krusKalCase.getEdges();
		// System.out.println("未排序=" + Arrays.toString(edges));
		// krusKalCase.sortEdges(edges);
		// System.out.println("排序后=" + Arrays.toString(edges));
		krusKalCase.kruskal();
	}

	// 构造器
	public KrusKalCase(char[] vertexs, int[][] matrix) {
		// 初始化顶点数和边的个数
		int vlen = vertexs.length;
		// 初始化顶点
		this.vertexs = new char[vlen];
		for (int i = 0; i < vertexs.length; i++) {
			this.vertexs[i] = vertexs[i];
		}

		// 初始化边
		this.matrix = new int[vlen][vlen];
		for (int i = 0; i < vlen; i++) {
			for (int j = 0; j < vlen; j++) {
				this.matrix[i][j] = matrix[i][j];
			}
		}

		// 统计边
		for (int i = 0; i < vlen; i++) {
			for (int j = i + 1; j < vlen; j++) {// j=i+1将自己和自己连的去掉
				if (this.matrix[i][j] != INF) {// 说明这条边有效
					edgeNum++;
				}
			}
		}
	}

	// 克鲁斯卡尔算法
	public void kruskal() {
		int index = 0;// 表示最后结果数组的索引
		int[] ends = new int[edgeNum];// 保存已有最小生成树中的每个顶点在最小生成树中的终点
		// 创建结果数组保存最终的最小生成树
		EData[] rets = new EData[edgeNum];
		// 获取图中所边的集合
		EData[] edges = getEdges();
		System.out.println("获取图的边的集合=" + Arrays.toString(edges) + "共" + edges.length);
		// 按照边的权值大小进行排序(从小到大)
		sortEdges(edges);
		// 遍历edges数组， 将边添加到最小生成树中时，判断准备加入的边是否形成了回路，没构成回路就加入
		for (int i = 0; i < edgeNum; i++) {
			// 获取到第i条边的第一个顶点
			int p1 = getPosition(edges[i].start);
			// 获取第i条边的第2个顶点
			int p2 = getPosition(edges[i].end);
			// 获取p1这个顶点在已有的最小生成树中对应的终点
			int m = getEnd(ends, p1);
			// 获取p2这个顶点在已有的最小生成树中对应的终点
			int n = getEnd(ends, p2);
			// 是否构成回路
			if (m != n) {
				// 没构成回路
				ends[m] = n;// 设置m在最小生成树中的终点
				rets[index++] = edges[i];// 一条边加入rets数组
			}

		}

		// 统计并打印最小生成树
		System.out.println("最小生成树为:");
		for (int i = 0; i < index; i++) {
			System.out.println(rets[i]);
		}

	}

	// 打邻接矩阵
	public void print() {
		System.out.println("邻接矩阵为:\n");
		for (int i = 0; i < vertexs.length; i++) {
			for (int j = 0; j < vertexs.length; j++) {
				System.out.printf("%12d\t", matrix[i][j]);
			}
			System.out.println();
		}
	}

	// 对边的权值进行排序处理
	// EData[]边的集合
	private void sortEdges(EData[] edges) {
		for (int i = 0; i < edges.length - 1; i++) {
			for (int j = 0; j < edges.length - 1 - i; j++) {
				if (edges[j].weight > edges[j + 1].weight) {
					// 进行交换
					EData temp = edges[j];
					edges[j] = edges[j + 1];
					edges[j + 1] = temp;
				}
			}
		}
	}

	/**
	 * 
	 * @param ch 顶点的值
	 * @return 返回顶点对应的下标，找不到就返回-1
	 */
	private int getPosition(char ch) {
		for (int i = 0; i < vertexs.length; i++) {
			if (vertexs[i] == ch) {
				return i;
			}
		}
		// 找不到
		return -1;
	}

	/**
	 * 获取途中的边放入EData[]数组中，后面遍历该数组 通过matrix邻接矩阵获取 EData[]形式 [['A','B',12]...]
	 * 
	 * @return
	 */
	private EData[] getEdges() {
		int index = 0;
		EData[] edges = new EData[edgeNum];
		for (int i = 0; i < vertexs.length; i++) {
			for (int j = i + 1; j < vertexs.length; j++) {// j=j+1表示跳过自己不需要比较
				if (matrix[i][j] != INF) {
					edges[index++] = new EData(vertexs[i], vertexs[j], matrix[i][j]);
				}
			}
		}
		return edges;
	}

	//
	/**
	 * 功能：获取下标为i的顶点的终点,即判断2个点的终点是不是同一个终点
	 * 
	 * @param ends 记录了各个顶点的终点,这个数组是在遍历过程中，动态形成的
	 * @param i    传入顶点对应的下标
	 * @return 返回下标为i的这个顶点对应的终点的下标
	 */
	private int getEnd(int[] ends, int i) {
		while (ends[i] != 0) {
			i = ends[i];
		}
		return i;
	}
}

//创建边类,表示对象实际为一条边
class EData {
	char start;// 边的起点
	char end;// 边的终点(两个点之间的连线)
	int weight;// 边的权值
	// 构造器

	public EData(char start, char end, int weight) {
		this.start = start;
		this.end = end;
		this.weight = weight;
	}

	@Override
	public String toString() {
		return "EData [<" + start + "," + end + ",>=" + weight + "]";
	}

}

最小生成树为:
EData [<E,F,>=2]
EData [<C,D,>=3]
EData [<D,E,>=4]
EData [<B,F,>=7]
EData [<E,G,>=8]
EData [<A,B,>=12]
```

## 30. 迪杰斯特拉算法（Dijkstra）——最短路径问题

```java
Dijkstra应用实例:

(1) 战争时期，胜利乡有 7 个村庄(A, B, C, D, E, F, G) ，现在有六个邮差，从G点出发，需要分别把邮件分别送到 A,     B, C , D, E, F 六个村庄
(2) 各个村庄的距离用边线表示(权) ，比如 A – B 距离 5 公里 
(3) 问：如何计算出G村庄到 其它各个村庄的最短距离? 
(4) 如果从其它点出发到各个点的最短距离又是多少?//如下图
```

![image-20200315184118912](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200315184118912.png)

```java
迪杰斯特拉(Dijkstra)算法介绍
迪杰斯特拉(Dijkstra)算法是典型最短路径算法 ，用于计算一个结点到其他结点的最短路径。 它的主要特点是以起始点为中心向 外层层扩展(广度优先搜索思想)，直到扩展到终点为止
```

```java
迪杰斯特拉(Dijkstra)算法过程
设置出发顶点为v，顶点集合V{v1,v2,vi...}，v到V中各顶点的距离构成距离集合Dis， Dis{d1,d2,di...}，Dis集合记录着v到图中各顶点的距离(到自身可以看作0，v到vi 距离对应为di) 
(1) 从Dis中选择值最小的di并移出Dis集合，同时移出V集合中对应的顶点vi，此时的 v到vi即为最短路径
(2) 更新Dis集合，更新规则为：比较v到V集合中顶点的距离值，与v通过vi到V 集合中顶点的距离值，保留值较小的一个(同      时也应该更新顶点的前驱节点为vi ，表明是通过vi到达的)
(3) 重复执行两步骤，直到最短路径顶点为目标顶点即可结束
```



```java
package com.bytedance.Dijkstra;

import java.util.Arrays;

public class DijkstraAlgorithm {

	public static void main(String[] args) {
		// 初始化顶点数组
		char[] vertex = { 'A', 'B', 'C', 'D', 'E', 'F', 'G' };
		// 邻接矩阵
		int[][] matrix = new int[vertex.length][vertex.length];
		// 定义一个最大数表示不可连接
		final int N = 65535;
		matrix[0] = new int[]{N,5,7,N,N,N,2}; 
		matrix[1] = new int[]{5,N,N,9,N,N,3}; 
		matrix[2] = new int[]{7,N,N,N,8,N,N}; 
		matrix[3] = new int[]{N,9,N,N,N,4,N}; 
		matrix[4] = new int[]{N,N,8,N,N,5,4}; 
		matrix[5] = new int[]{N,N,N,4,5,N,6}; 
		matrix[6] = new int[]{2,3,N,N,4,6,N};
		//创建图对象
		Graph graph = new Graph(vertex, matrix);
		// 测试图的邻接矩阵
		graph.showGragh();
		//测试迪杰斯特拉算法
		graph.dsj(6);
		graph.showDijkstra();
	}

}


//创建图
class Graph {
	// 顶点数组
	private char[] vertex;
	// 邻接矩阵
	private int[][] matrix;
	//已经访问的顶点的集合
	private VisitedVertex vv;

	// 构造器
	public Graph(char[] vertex, int[][] matrix) {
		this.vertex = vertex;
		this.matrix = matrix;
	}
	
	//显示结果
	public void showDijkstra() {
		vv.show();
	}

	// 显示图
	public void showGragh() {
		for (int[] link : matrix) {
			System.out.println(Arrays.toString(link));
		}
	}
	
	//迪杰斯特拉算法
	/**
	 * 
	 * @param index 出发顶点对应的下标
	 */
	public void dsj(int index) {
		vv = new VisitedVertex(vertex.length, index);
		// 更新index下标顶点到周围顶点的距离和前驱顶点
		update(index);

		for (int j = 1; j < vertex.length; j++) {
			// 选择并返回新的访问顶点
			index = vv.updateArray();
			// 更新index下标顶点到周围顶点的距离和前驱顶点
			update(index);
		}

	}
	
	//更新index下标顶点到周围顶点的距离和周围顶点点的前驱顶点，同时 
	private void update(int index) {
		int len=0;
		//根据遍历邻接矩阵的matrix[index]这行
		for(int j=0;j<matrix[index].length;j++) {
			//出发顶点到index顶点的距离加上index顶点到j顶点的距离即
			//getDis(index)是出发顶点是G，假设到了A将这段距离保存起来，然后A下面还有个顶点假设为B，A到B
			//的距离就是matrix[index][j] 两端距离加起来就是B到G的总距离也就是目前所遍历到的最短路径
			len=vv.getDis(index)+matrix[index][j];
			// 如果j顶点没有被访问过，并且len小于出发顶点到j顶点的距离，则需要更新
			if(!vv.in(j)&&len<vv.getDis(j)) {
				//更新j顶点前驱为index这个顶点
				vv.updataPre(j, index);
				//更新出发顶点到j这个顶点的距离
				vv.updataDis(j, len);
			}
		}
	}
}

//最重要的类
//以访问顶点集合
class VisitedVertex {
	// 记录各个顶点是否访问过1表示访问过，0表示未访问过,此过程是动态更新的
	private int[] already_arr;
	// 每一个下标对应的值为前一个顶点的下标，也是动态更新的
	private int[] pre_visited;
	// 记录出发顶点到其他所有顶点的距离，例如从G点出发，就会记录G顶点到其他顶点的距离，也是动态更新的，得到的最短距离会存入到DIS
	private int[] dis;
	
	//构造器
	/**
	 * 
	 * @param length 顶点的个数
	 * @param index 出发顶点对应的下标
	 */
	public VisitedVertex(int length, int index) {
		this.already_arr = new int[length];
		this.pre_visited = new int[length];
		this.dis = new int[length];
		// 初始化dis数组,将出发顶点与到他顶点的访问距离都是65535
		Arrays.fill(dis, 65535);
		this.already_arr[index] = 1;// 设置出发顶点被访问过
		this.dis[index] = 0;// 设置出发顶点到出发顶点的访问距离为0
	}

	/**
	 * 功能：判断index对应的顶点是否被访问过
	 * 
	 * @param index
	 * @return 如果访问过就返回true没被访问过就返回false
	 */
	public boolean in(int index) {
		return already_arr[index] == 1;// 等于1代表访问过了就返回true，否则就false
	}

	/**
	 * 功能：更新出发顶点到index对应大结点的距离
	 * 
	 * @param index 需要更新的顶点的下标
	 * @param len   需要更新为多大的距离
	 */
	public void updataDis(int index, int len) {
		dis[index] = len;
	}

	/**
	 * 功能：更新pre顶点的前驱为index的这个顶点
	 * 
	 * @param pre
	 * @param index
	 */
	public void updataPre(int pre, int index) {
		pre_visited[pre] = index;
	}

	/**
	 * 功能：返回出发顶点到index对应的顶点的距离
	 * 
	 * @param index
	 */
	public int getDis(int index) {
		return dis[index];
	}

	// 继续更新并返回新的访问顶点，例如G作为出发点完成后，就将A作为新的访问顶点，而不是出发点，出发点始终为G不能变
	public int updateArray() {
		int min = 65535;
		int index = 0;
		for (int i = 0; i < already_arr.length; i++) {
			if (already_arr[i] == 0 && dis[i] < min) {
				min = dis[i];
				index = i;
			}
		}
		// 更新index顶点被访问过
		already_arr[index] = 1;
		return index;
	}
	
	// 显示最后的结果
	// 将3个数组的情况输出
	public void show() {
		System.out.println("=============================");
		// 输出already_arr
		for (int i : already_arr) {
			System.out.print(i + "\t");
		}
		System.out.println();
		// 输出pre_visited
		for (int i : pre_visited) {
			System.out.print(i + "\t");
		}
		System.out.println();
		// 输出dis
		for (int i : dis) {
			System.out.print(i + "\t");
		}
		System.out.println();

		// 为了显示可读性高做一些动作
		char[] vertex = { 'A', 'B', 'C', 'D', 'E', 'F', 'G' };
		int count = 0;
		for (int i : dis) {
			if (i != 65535) {
				System.out.print(vertex[count] + "(" + i + ")");
			} else {
				System.out.println("INF");
			}
			count++;
		}
		System.out.println();
	}

}
```

## 31. 弗洛伊德(Floyd)算法——最短路径问题

```java
弗洛伊德(Floyd)算法介绍//时间复杂度较高
(1) 和Dijkstra 算法一样，弗洛伊德(Floyd)算法也是一种用于寻找给定的加权图中顶点间最短路径的算法。该算法 名称以     创始人之一、1978 年图灵奖获得者、斯坦福大学计算机科学系教授罗伯特·弗洛伊德命名
(2) 弗洛伊德算法(Floyd)计算图中各个顶点之间的最短路径 
(3) 迪杰斯特拉算法用于计算图中某一个顶点到其他顶点的最短路径。 
(4) 弗洛伊德算法 VS 迪杰斯特拉算法：迪杰斯特拉算法通过选定的被访问顶点，求出从出发访问顶点到其他顶点 的最短路     径；弗洛伊德算法中每一个顶点都是出发访问点，所以需要将每一个顶点看做被访问顶点，求出从每 一个顶点到其他顶点     的最短路径。
```

![image-20200316144408915](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200316144408915.png)

```java
弗洛伊德算法的步骤：
第一轮循环中，以 A(下标为：0)作为中间顶点【即把 A作为中间顶点的所有情况都进行遍历, 就会得到更新距离表和前驱关系】,距离表和前驱关系更新为：
```

![image-20200316144555698](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200316144555698.png)

```java
分析如下：
(1) 以A顶点作为中间顶点是，B->A->C 的距离由 N->9，同理 C 到 B；C->A->G 的距离由N->12，同理G到 C 
(2) 更换中间顶点，循环执行操作，直到所有顶点都作为中间顶点更新后，计算结束
```

![image-20200316144713970](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\image-20200316144713970.png)

```java
package com.bytedance.floyd;
import java.util.Arrays;
public class FloydAlgorithm {

	public static void main(String[] args) {
		// 测试图是否创建成功
		char[] vertex = { 'A', 'B', 'C', 'D', 'E', 'F', 'G' };
		// 将最原始的邻接矩阵创建好
		int[][] matrix = new int[vertex.length][vertex.length];
		// 定义较大值表示两者无法联通
		final int N = 65535;
		matrix[0] = new int[] { 0, 5, 7, N, N, N, 2 };
		matrix[1] = new int[] { 5, 0, N, 9, N, N, 3 };
		matrix[2] = new int[] { 7, N, 0, N, 8, N, N };
		matrix[3] = new int[] { N, 9, N, 0, N, 4, N };
		matrix[4] = new int[] { N, N, 8, N, 0, 5, 4 };
		matrix[5] = new int[] { N, N, N, 4, 5, 0, 6 };
		matrix[6] = new int[] { 2, 3, N, N, 4, 6, 0 };
		// 创建图对象
		Graph graph = new Graph(vertex.length, matrix, vertex);
		// 调用弗洛伊德算法
		graph.flody();
		graph.show();
	}

}

//创建图
class Graph {
	private char[] vertex;// 存放顶点数组
	private int[][] dis;// 从各个顶点出发到其他顶点的距离，最后的结果也保存在这个数组
	private int[][] pre;// 保存到达目标顶点的前驱顶点

	// 构造器
	/**
	 * 
	 * @param length 大小
	 * @param matrix 邻接矩阵
	 * @param vertex 顶点数组
	 */
	public Graph(int length, int[][] matrix, char[] vertex) {
		this.vertex = vertex;
		this.dis = matrix;
		this.pre = new int[length][length];
		// 对Pre数组进行初始化，存放的是前驱节点的下标
		for (int i = 0; i < length; i++) {
			Arrays.fill(pre[i], i);
		}

	}

	// 显示pre数组和dis数组
	public void show() {

		// 易于显示阅读，对其进行优化
		char[] vertex = { 'A', 'B', 'C', 'D', 'E', 'F', 'G' };

		for (int k = 0; k < dis.length; k++) {
			// 先将pre数组的一行数据
			for (int i = 0; i < dis.length; i++) {
				System.out.print(vertex[pre[k][i]] + "\t");
			}
			System.out.println();
			// 输出dis数组的一行数据
			for (int i = 0; i < dis.length; i++) {
				System.out.print("(" + vertex[k] + "到" + vertex[i] + "的最短路径" + dis[k][i] + ")" + "\t");
			}
			System.out.println();
			System.out.println();
		}
	}

	// 弗洛伊德核心算法
	public void flody() {
		int len;// 保存距离
		// 对中间顶点遍历，k为中间顶点的下标
		for (int k = 0; k < dis.length; k++) {
			// 从i顶点出发
			for (int i = 0; i < dis.length; i++) {
				// 从i出发经过k到达j
				for (int j = 0; j < dis.length; j++) {
					len = dis[i][k] + dis[k][j];// 求出i顶点出发经过k中间顶点到达j这个顶点的距离
					if (len < dis[i][j]) {// 算出的len小于直连的距离就进行置换
						dis[i][j] = len;// 跟新距离
						// 更新前驱顶点
						pre[i][j] = pre[k][j];
					}
				}
			}
		}
	}
}
```

## 32. 跳表(SkipList)

****

```java

一个有序链表搜索、添加、删除的平均时间复杂度是多少？O(n)
能否利用二分搜索优化有序列表,将搜索 添加 删除的平均时间复杂度降低至 ——> O(logn)?
链表没有像数组那样的高效随机访问 (O(1)时间复杂度)
那有没有其它办法让有序链表搜索 添加 删除的平均时间复杂度降低至 ——> O(logn)
方案:使用跳表 
```

```java
跳表又称跳跃表,在有序链表的基础上增加了跳跃的功能,设计初衷是为了取代平衡树 (如红黑树这种平衡二叉搜索树)。
Redis中的SortedSet LeveID 中的MenTable都用到了跳跃表,Redis,LeveID是Key-Value数据库 
    
对比平衡树:
①跳表的实现和维护更加简单
②跳表的搜索 删除 添加的平均时间复杂度是 ——>O(logn) 
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\跳表图示.png)

```java
跳表的每个节点存储的是 Key-Value ,如上图中的数字就是所在节点的 Key,黑色点就是 Key 对应的值
```

### 32.1 跳表的搜索

****

```java
1. 从顶层链表的首开始,从左往右搜索,直至找到一个大于或等于目标的函数,或当前者到达当前层链表的尾部
2. 如果该元素等于目标元素,则表明该元素已经被找到
3. 如果元素大于目标元素或已经到达链表的尾部,则退回到当前层的前一个元素,然后转入下一层进行搜索
//从图可以看出从最上面开始找是最快的，因为最上面的链路经过的节点最少，固然是最快的
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\跳表图示 (2).png)

### 32.2 跳表的添加,删除,以及跳表的层数

****

```java
添加的细节: 随机决定新添加元素的层数
删除的细节: 删除一个元素后,整个跳表的层数可能会降低
```

**跳表的层数**

```java
1. 跳表是按层序构造的,底层是一个普通的有序链表,高层相当于低层的快速通道
2. 在第i层中的元素是按某个固定的概率 p (通常为 1/2 或 1/4),出现在第 i+1 层中,产生越高的层数,概率越低
   a.元素层数恰好等于 1 的概率为 1 – p
   b.元素层数大于等于 2 的概率为 p，而元素层数恰好等于 2 的概率为 p * (1 – p)
   c.元素层数大于等于3的概率为 p^2,而元素层数恰好等于3的概率为 p^2 * (1-p)
   d.元素层数大于等于4的概率为 p^3,而元素层数恰好等于4的概率为 p^3 * (1-p)
    .....
   n. 一个元素的平均层数是 1/(1-p)
```

![](C:\Users\86136\AppData\Roaming\Typora\typora-user-images\跳表公式 (1).png)

```java
当 p=1/2 时,每个元素所包含的平均指针数量是 2
当 p=1/4 时,每个元素所包含的平均指针数量是 1.33
```

### 32.3 跳表的复杂度分析

****

```java
每一层的元素数量:
√ 第 1 层链表固定有 n 个元素
√ 第 2 层链表固定有 n*p 个元素
√ 第 3 层链表固定有 n*p^2 个元素
√ 第 k 层链表固定有 n*p^k 个元素
√ ......

//其他:
 最高层的层数是 log 1/p n,平均有 1/p 元素
 在搜索时,每一层链表的预期查找步数最多是 1/p,所以总的查找步数是 -(log p n/p)时间复杂度是 O(log n)

```

