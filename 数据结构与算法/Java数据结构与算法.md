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



