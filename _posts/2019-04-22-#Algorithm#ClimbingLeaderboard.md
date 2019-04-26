---
title: Algorithm - Climbing Leaderboard
tags: [algorithm, hackerrank]
categories: [algorithm]
layout: post
---

* content
{:toc}

[Problem](https://www.hackerrank.com/challenges/climbing-the-leaderboard/problem)

## 问题描述

Alice is playing an arcade game and wants to climb to the top of the leaderboard and wants to track her ranking. The game uses Dense Ranking, so its leaderboard works like this:

- The player with the highest score is ranked number 1 on the leaderboard.
- Players who have equal scores receive the same ranking number, and the next player(s) receive the immediately following ranking number.

For example, the four players on the leaderboard have high scores of 100, 90, 90 and 80. Those players will have ranks 1, 2, 2, and 3 respectively. If Alice's scores 70, 80 and 105, her rankings after each game are 4, 3, and 1.

Complete the climbingLeaderboard function in the editor below. It should return an integer array where each element `res[j]` represents Alice's rank after the jth game.

## 解题思路

因为相同的得分排名相同，而且不会因为得分相同导致后面的排名顺延，所以可以考虑先将初始得分数组和Alice得分数组中的相同的分数去掉。

因为初始得分是递减的，而Alice的得分是递增的，所以可以设置两个指针来比较这两个数组中的元素，其中pscore指向score数组中的第一个元素，移动方向为从前往后；palice指向alice数组中的最后一个元素，移动方向为从后往前。算法步骤为：

（0）将初始得分的数组记为`scores`，将Alice得分的数组记为`alice`；`pscore = 0; palice = alice.length - 1;`

（1）从`pscore`指向的元素开始往后找，找到第一个比`alice[palice]`小或者与其相等的元素，将其位置（`pscore + 1`，因为第一名下标为0）记录为`alice[palice]`的最终排名；如果找不到这样的一个元素，表示`scores`中所有元素都比`alice[palice]`大，因此`alice[palice]`的排名为`scores.length + 1`；

（2）如果`palice == alice.length-1`，查找完毕，执行（3）；否则`palice ++`，回到（1）。

（3）遍历初始的未去重alice数组，根据上面记录下的每一个分数的排名来得到数组中各个分数的排名，返回排名数组。

## 算法复杂度

去重部分复杂度为*O(n)*，查找部分复杂度为*O(m+n)*。

## 代码

```java
import java.io.*;
import java.math.*;
import java.security.*;
import java.text.*;
import java.util.*;
import java.util.concurrent.*;
import java.util.regex.*;

public class Solution {

    static int[] deleteDuplicate(int[] scores){
        int[] newScores = new int[scores.length];
        int newLength = 0;
        for(int i=0;i<scores.length;i++){
            if(i==0){
                newScores[i] = scores[i];
                newLength ++;
            }else{
                if(scores[i]!=scores[i-1]){
                    newScores[newLength] = scores[i];
                    newLength ++;
                }
            }
        }
        return Arrays.copyOf(newScores,newLength);
    }
    
    // Complete the climbingLeaderboard function below.
    static int[] climbingLeaderboard(int[] scores, int[] alice) {
        int[] dscore = deleteDuplicate(scores);
        int[] dalice = deleteDuplicate(alice);
        Map<Integer,Integer> map = new HashMap<>();
        int pa = dalice.length-1;
        int ps = 0;
        while(pa>=0){
            if(dalice[pa]>=dscore[ps]){
                map.put(dalice[pa],ps + 1);
                pa --;
            }else if(ps == dscore.length-1){
                map.put(dalice[pa],dscore.length+1);
                pa --;
            }else{
                ps ++;
            }
        }

        int[] rank = new int[alice.length];
        for(int i=0;i<alice.length;i++){
            rank[i] = map.get(alice[i]);
        }

        return rank;
    }

    private static final Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) throws IOException {
        BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(System.getenv("OUTPUT_PATH")));

        int scoresCount = scanner.nextInt();
        scanner.skip("(\r\n|[\n\r\u2028\u2029\u0085])?");

        int[] scores = new int[scoresCount];

        String[] scoresItems = scanner.nextLine().split(" ");
        scanner.skip("(\r\n|[\n\r\u2028\u2029\u0085])?");

        for (int i = 0; i < scoresCount; i++) {
            int scoresItem = Integer.parseInt(scoresItems[i]);
            scores[i] = scoresItem;
        }

        int aliceCount = scanner.nextInt();
        scanner.skip("(\r\n|[\n\r\u2028\u2029\u0085])?");

        int[] alice = new int[aliceCount];

        String[] aliceItems = scanner.nextLine().split(" ");
        scanner.skip("(\r\n|[\n\r\u2028\u2029\u0085])?");

        for (int i = 0; i < aliceCount; i++) {
            int aliceItem = Integer.parseInt(aliceItems[i]);
            alice[i] = aliceItem;
        }

        int[] result = climbingLeaderboard(scores, alice);

        for (int i = 0; i < result.length; i++) {
            bufferedWriter.write(String.valueOf(result[i]));

            if (i != result.length - 1) {
                bufferedWriter.write("\n");
            }
        }

        bufferedWriter.newLine();

        bufferedWriter.close();

        scanner.close();
    }
}

```