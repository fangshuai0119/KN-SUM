# Java数据结构与算法

## 一、数据结构与算法概述

### 1.1 线性结构

1. 线性结构作为最常用的数据结构，其特点是**数据元素之间存在一对一**的线性关系
2. 线性结构有两种不同的存储结构，即**顺序存储结构（数组）**和**链式存储结构（链表）**。顺序存储的线性表称为顺序表，顺序表中的存储元素是连续的。
3. 链式存储的线性表称为链表，链表中的**存储元素不一定是连续的**，元素节点中存放数据元素以及相邻元素的地址信息。
4. 线性结构常见的有：**数组、队列、链表和栈**。

### 1.2 非线性结构

非线性结构包括：二维数组、多维数组、广义表、树结构、图结构

## 二、稀疏数组与队列

### 2.1 稀疏数组

#### 2.1.1 需要解决的问题

![image-20210511223643492](Java数据结构与算法.assets/image-20210511223643492.png)

#### 2.1.2 基本介绍

>当一个数组中大部分元素为0，或者为同一个值的数组时，可以使用稀疏数组来保存该数组。
>
>稀疏数组的处理方法：
>
>1. 记录数组一共有几行几列，有多少个不同的值
>2. 把具有不同值的元素的行列及值记录在一个小规模的数组中，从而缩小程序的规模。

#### 2.1.4 案例

![image-20210511224050479](Java数据结构与算法.assets/image-20210511224050479.png)

#### 2.1.4 思路分析

![image-20210511225559356](Java数据结构与算法.assets/image-20210511225559356.png)

#### 2.1.5 代码实现

```java
package com.fs.sparse.array;

/**
 * 稀疏数组
 * @author fangshuai
 * @version 1.0 2021/05/11
 */
public class SparseArray {

    public static void main(String[] args) {
        // 创建一个原始的二维数组 11 * 11
        // 0: 表示没有棋子  1: 表示黑子  2: 表示蓝子
        int chessArr1[][] = new int[11][11];
        chessArr1[1][2] = 1;
        chessArr1[2][3] = 2;

        // 输出原始的二维数组
        System.out.println("原始的二维数组");
        printArray(chessArr1);

        // 将二维数组转化为稀疏数组
        // 获取二维数组有值的个数
        int sum = getSum(chessArr1);
        int[][] sparseArr = new int[sum + 1][3];
        sparseArr[0][0] = chessArr1.length;
        sparseArr[0][1] = chessArr1[0].length;
        sparseArr[0][2] = sum;
        int row = 1;
        for (int i = 0; i < chessArr1.length; i++) {
            for (int j = 0; j < chessArr1[i].length; j++) {
                if (chessArr1[i][j] > 0) {
                    sparseArr[row][0] = i;
                    sparseArr[row][1] = j;
                    sparseArr[row][2] = chessArr1[i][j];
                    row += 1;
                }
            }
        }
        System.out.println("转换稀疏数组完成");
        printArray(sparseArr);

        // 将稀疏数组转为二维数组
        int[][] rawArr = new int[sparseArr[0][0]][sparseArr[0][1]];
        for (int i = 1; i < sparseArr.length; i++) {
            rawArr[sparseArr[i][0]][sparseArr[i][1]] = sparseArr[i][2];
        }
        System.out.println("还原二维数组完成");
        printArray(rawArr);
    }

    private static int getSum(int[][] arr) {
        int sum = 0;
        for (int i = 0; i < arr.length; i++) {
            for (int j = 0; j < arr[i].length; j++) {
                if (arr[i][j] > 0) {
                    sum += 1;
                }
            }
        }
        return sum;
    }

    private static void printArray(int[][] arr) {
        for (int i = 0; i < arr.length; i++) {
            for (int j = 0; j < arr[i].length; j++) {
                System.out.printf("%d\t", arr[i][j]);
            }
            System.out.println();
        }
    }
}

```

### 2.2 队列

#### 2.2.1 队列介绍

> - 队列是一个有序列表，可以用数组或是链表来实现
> - 遵循先入先出的原则。即：先存入队列的数据，要先取出。后存入的要后取出

#### 2.2.2 模拟数组队列 - 代码实现

```java
package com.fs.queue;

import java.util.Scanner;

/**
 * @author fangshuai
 * @version 1.0 2021/05/12
 */
public class ArrayQueueDemo {

//    public static void main(String[] args) {
//        ArrayQueue arrayQueue = new ArrayQueue(4);
//        arrayQueue.addQueue(4);
//        arrayQueue.addQueue(5);
//        arrayQueue.addQueue(6);
//        arrayQueue.addQueue(7);
//        arrayQueue.show();
//        System.out.println("从队列取数据: " + arrayQueue.getQueue());
//        System.out.println("从队列取数据: " + arrayQueue.getQueue());
//        System.out.println("从队列取数据: " + arrayQueue.getQueue());
//        arrayQueue.show();
//        arrayQueue.showHeadQueue();
//        System.out.println("从队列取数据: " + arrayQueue.getQueue());
//        arrayQueue.showHeadQueue();
//        arrayQueue.show();
//    }

    public static void main(String[] args) {
        ArrayQueue arrayQueue = new ArrayQueue(4);
        // 接收用户输入
        char key = ' ';
        Scanner scanner = new Scanner(System.in);
        boolean loop = true;
        while (loop) {
            System.out.println("s(show): 显示队列");
            System.out.println("e(exit): 退出程序");
            System.out.println("a(add): 添加数据到队列");
            System.out.println("g(get): 从队列取出数据");
            System.out.println("h(head): 显示队列头的数据");
            key = scanner.next().charAt(0);
            switch (key) {
                case 's':
                    arrayQueue.show();
                    break;
                case 'a':
                    System.out.println("请输入一个数");
                    int value = scanner.nextInt();
                    arrayQueue.addQueue(value);
                    break;
                case 'e':
                    scanner.close();
                    loop = false;
                case 'g':
                    try {
                        int head = arrayQueue.getQueue();
                        System.out.println("取出的数据：" + head);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'h':
                    try {
                        System.out.println("当前队列头数据: " + arrayQueue.showHeadQueue());
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                default:
                    break;
            }
        }
    }

}

/**
 * 使用数组模拟队列 - 编写一个ArrayQueue类
 */
class ArrayQueue {

    /**
     * 表示数组的最大容量
     */
    private int maxSize;

    /**
     * 队列头
     */
    private int front;

    /**
     * 队列尾
     */
    private int rear;

    /**
     * 该数据用于存储数据，模拟队列
     */
    private int[] arr;

    public ArrayQueue(int maxSize) {
        this.maxSize = maxSize;
        this.arr = new int[maxSize];
        // 指向队列头部前一个位置
        this.front = -1;
        // 指向队列尾部的具体数据
        this.rear = -1;
    }

    /**
     * 判断队列是否已满
     * @return
     */
    public boolean isFull() {
        return this.rear == this.maxSize - 1;
    }

    /**
     * 判断队列是否为空
     * @return
     */
    public boolean isEmpty() {
        return this.rear == this.front;
    }

    /**
     * 添加数据
     * @param n
     */
    public void addQueue(int n) {
        if (isFull()) {
            System.out.println("队列满，不能加入数据");
            return;
        }
        this.rear += 1;
        this.arr[this.rear] = n;
    }

    /**
     * 数据出队
     */
    public int getQueue() {
        if (isEmpty()) {
            System.out.println("队列为空，不能获取数据");
            throw new RuntimeException("队列为空，不能获取数据");
        }
        this.front += 1;
        return this.arr[this.front];
    }

    /**
     * 显示
     */
    public void show() {
        if (isEmpty()) {
            System.out.println("队列为空，没有数据");
            return;
        }
        for (int i = 0; i < this.arr.length; i++) {
            System.out.printf("arr[%d]=%d\n", i, this.arr[i]);
        }
    }

    /**
     * 显示头部数据
     */
    public int showHeadQueue() {
        if (isEmpty()) {
            System.out.println("队列为空，没有数据");
            throw new RuntimeException("队列为空，不能获取数据");
        }
        System.out.println("当前头部数据:" + this.arr[this.front + 1]);
        return this.arr[this.front + 1];
    }
}
```

- 问题分析并优化
  1. 目前数组使用一次就不能再次使用，数组没有达到复用
  2. 改进：将这个数组使用算法，改进成一个环形的数组，采用取模（%）方式完成

#### 2.2.3 数组模拟环形队列

对前面的数组模拟队列的优化，充分利用数组。因此将数组看做是一个环形的。（通过**取模的方式来实现**）

**分析说明：**

1. 尾索引的下一个为头索引时表示队列满，即将队列容量空出一个作为约定，这个在做判断队列满的时候需要注意(rear + 1) % maxSize == front (满)
2. rear == front (空)
3. 分析示意图

![image-20210727223540090](Java数据结构与算法.assets/image-20210727223540090.png)

##### 2.2.3.1 数组模拟环形队列-代码实现

```java
package com.fs.queue;

import java.util.Scanner;

/**
 * @author fangshuai
 * @version 1.0 2021/07/27
 */
public class CircleArrayQueueDemo {

    public static void main(String[] args) {
        CircleArray circleArray = new CircleArray(4);
        // 接收用户输入
        char key = ' ';
        Scanner scanner = new Scanner(System.in);
        boolean loop = true;
        while (loop) {
            System.out.println("s(show): 显示队列");
            System.out.println("e(exit): 退出程序");
            System.out.println("a(add): 添加数据到队列");
            System.out.println("g(get): 从队列取出数据");
            System.out.println("h(head): 显示队列头的数据");
            key = scanner.next().charAt(0);
            switch (key) {
                case 's':
                    circleArray.show();
                    break;
                case 'a':
                    System.out.println("请输入一个数");
                    int value = scanner.nextInt();
                    circleArray.addQueue(value);
                    break;
                case 'e':
                    scanner.close();
                    loop = false;
                case 'g':
                    try {
                        int head = circleArray.getQueue();
                        System.out.println("取出的数据：" + head);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'h':
                    try {
                        System.out.println("当前队列头数据: " + circleArray.headQueue());
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                default:
                    break;
            }
        }
    }

}

class CircleArray {

    /**
     * 表示数组的最大容量
     */
    private int maxSize;

    /**
     * front 变量的含义做一个调整：front就指向队列的第一个元素，也就是说arr[front]就是队列的第一个元素, front 的初始值为0
     */
    private int front;

    /**
     * rear变量的含义做一个调整：rear指向队列的最后一个元素的后一个位置。因为希望空出一个空间作为约定，rear 的初始值为0
     */
    private int rear;

    /**
     * 该数据用于存放数据，模拟队列
     */
    private int[] arr;

    public CircleArray(int arrayMaxSize) {
        this.maxSize = arrayMaxSize;
        this.arr = new int[this.maxSize];
    }

    /**
     * 判断队列是否已满
     * @return
     */
    public boolean isFull() {
        return (rear + 1) % maxSize == front;
    }

    /**
     * 判断队列是否为空
     * @return
     */
    public boolean isEmpty() {
        return rear == front;
    }

    /**
     * 添加数据到队列
     * @param n
     */
    public void addQueue(int n) {
        // 判断队列是否满
        if (isFull()) {
            System.out.println("队列满, 不能加入数据");
            return;
        }
        // 直接将数据加入
        arr[rear] = n;
        // 将rear 后移，必须考虑rear 取模
        rear = (rear + 1) % maxSize;
    }

    /**
     * 取数据
     * @return
     */
    public int getQueue() {
        // 判断队列是否为空
        if (isEmpty()) {
            throw new RuntimeException("队列空, 不能取数据");
        }
        // 这里需要分析出front是指向队列的第一个元素
        // 1. 先把front对应的值保留到一个临时变量
        // 2. 将front 后移
        // 3. 将临时保存的变量返回
        int n = arr[front];
        front = (front + 1) % maxSize;
        return n;
    }

    /**
     * 显示队列所有数据
     */
    public void show() {
        if (isEmpty()) {
            System.out.println("队列空的， 没有数据");
            return;
        }
        // 思路：从front 开始遍历, 遍历多少个元素
        for (int i = front; i < front + getSize(); i++) {
            System.out.printf("arr[%d]=%d\n", i % maxSize, arr[i % maxSize]);
        }
    }

    /**
     * 获取队列元素个数
     * @return
     */
    public int getSize() {
        return (rear + maxSize - front) % maxSize;
    }

    /**
     * 获取队列头数据，不取出
     * @return
     */
    public int headQueue() {
        return arr[front];
    }
}

```

