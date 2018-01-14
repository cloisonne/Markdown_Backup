---
title: 关于二叉树的算法题
categories:
  - Codes
tags:
  - 算法
  - Java
permalink: binary-tree
date: 2017-12-18 20:31:28
---

<h2 id="intro">前言</h2>关于二叉树的算法题，用java写，包括剑指offer和网上各种。


<!-- more -->

---------------

### 二叉树的下一个节点

给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

``` java
/*
public class TreeLinkNode {
    int val;
    TreeLinkNode left = null;
    TreeLinkNode right = null;
    TreeLinkNode next = null;

    TreeLinkNode(int val) {
        this.val = val;
    }
}
*/
public class Solution {
    public TreeLinkNode GetNext(TreeLinkNode pNode)
    {
        TreeLinkNode curNode = null;
		// 右孩子存在
        if(pNode.right!=null){
            curNode = pNode.right;
            while(curNode.left!=null)curNode=curNode.left;
            return curNode;
        }
		//右孩子不存在 p是左孩子 找父节点
        if(pNode.next==null)return null;
        if(pNode==pNode.next.left){
            return pNode.next;
        }
		//右孩子不存在 p是右孩子 追溯父节点 找是左孩子的那个的父节点
        curNode=pNode.next;
        while(curNode.next!=null){
            if(curNode==curNode.next.left)
                return curNode.next;
        	curNode=curNode.next;
        }
        return null;
        
    }
}
```

### 对称的二叉树

请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

``` java
/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    boolean isSymmetrical(TreeNode pRoot)
    {
        return isSymmetrical(pRoot, pRoot);
    }
    boolean isSymmetrical(TreeNode pRoot1,TreeNode pRoot2)
    {
        if(pRoot1==null&&pRoot2==null)return true;
        else if(pRoot1==null||pRoot2==null)return false;
        else if(pRoot1.val!=pRoot2.val)return false;
        return isSymmetrical(pRoot1.left,pRoot2.right)&&isSymmetrical(pRoot2.left,pRoot1.right);
    }
}

```


### 按之字形顺序打印二叉树

请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

**注意**： 偶数行reverse方法效率太低了。 

``` java
import java.util.ArrayList;
import java.util.Stack;

/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
		int layer =1;
        Stack<TreeNode> s1 = new Stack<TreeNode>(); //存奇数层
        s1.push(pRoot);
        Stack<TreeNode> s2 = new Stack<TreeNode>();//存偶数层
        ArrayList<ArrayList<Integer>> list = new ArrayList<ArrayList<Integer>>();
        
        while(!s1.empty()||!s2.empty()){
            if(layer%2!=0){
                ArrayList<Integer> temp = new ArrayList<Integer>();
                while(!s1.empty()){
                    TreeNode node = s1.pop();
                    if(node!=null){
                        temp.add(node.val);
                        System.out.print(node.val+' ');
                        s2.push(node.left);
                        s2.push(node.right);
                    }
                }
                if(!temp.isEmpty()){
                    list.add(temp);
                    layer++;
                    System.out.println();
                }
            }
            else{
                ArrayList<Integer> temp = new ArrayList<Integer>();
                while(!s2.empty()){
                    TreeNode node = s2.pop();
                    if(node!=null){
                        temp.add(node.val);
                        System.out.print(node.val+' ');
                        s1.push(node.right);
                        s1.push(node.left);
                    }
                }
                if(!temp.isEmpty()){
                    list.add(temp);
                    layer++;
                    System.out.println();
                }
            }
        }
        return list;
    }

}
```

### 把二叉树打印成多行

从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。 

``` java
import java.util.ArrayList;
import java.util.*;


/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
    	ArrayList<ArrayList<Integer>> list = new ArrayList<ArrayList<Integer>>();
        if(pRoot==null)return list;
        Queue<TreeNode> layer = new LinkedList<TreeNode>();
        ArrayList<Integer> temp = new ArrayList<Integer>();
        layer.add(pRoot);
        int start=0,end=1;
        while(!layer.isEmpty()){
            TreeNode cur = layer.remove();
            temp.add(cur.val);
            start++;
            if(cur.left!=null)layer.add(cur.left);
            if(cur.right!=null)layer.add(cur.right);
            if(start==end){
                end=layer.size();
                start=0;
                list.add(temp);
                temp=new ArrayList<Integer>();
            }
        }
        return list;
    }
    
}
```

### 序列化二叉树

请实现两个函数，分别用来序列化和反序列化二叉树

``` java

/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    String Serialize(TreeNode root) {
        if(root==null)return "";
        StringBuilder res = new StringBuilder();
        Serialize2(root,res);
        return res.toString();
  }
    void Serialize2(TreeNode root,StringBuilder res){
        if(root==null){
            res.append("#,");
            return ;
        }
        res.append(root.val);
        res.append(',');
        Serialize2(root.left,res);
        Serialize2(root.right,res);
    }
    
    int index=-1;
    
    TreeNode Deserialize(String str) {
        if(str.length()==0)return null;
        String[] strs = str.split(",");
        return Deserialize2(strs);
  }

    TreeNode Deserialize2(String[] strs){
        index++;
        if(!strs[index].equals("#")){
            TreeNode root = new TreeNode(0);
            root.val=Integer.parseInt(strs[index]);
            root.left=Deserialize2(strs);
            root.right=Deserialize2(strs);
            return root;
        }
        return null;
    }
}
```

### 二叉搜索树的第k个结点

给定一颗二叉搜索树，请找出其中的第k大的结点。例如， 5 / \ 3 7 /\ /\ 2 4 6 8 中，按结点数值大小顺序第三个结点的值为4。

``` java
/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    int index=0;
    TreeNode KthNode(TreeNode root, int k)
    {
        if(root!=null&&k>=1){
            TreeNode node = KthNode(root.left,k);
            if(node!=null)return node;
            index++;
            if(index==k)return root;
            node= KthNode(root.right,k);
            if(node!=null)return node;
        }
        return null;
    }
}
```