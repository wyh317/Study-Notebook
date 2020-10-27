# Morris遍历

经典的二叉树遍历，无论是递归还是非递归。其空间复杂度都是O(h),其中h为二叉树的高度。
而Morris遍历可以做到时间复杂度O(N), 空间复杂度O(1)


## 算法流程
记当前来到的节点引用为cur
* 如果cur没有左孩子，那么cur向右移动：cur = cur.right
* 如果cur有左孩子，那么找到它左子树上最右的节点，记为mostright
    * 如果mostright的右指针right指向空，那么让其指向cur，然后cur向左移动：cur = cur.left
    * 如果mostright的右指针right指向cur, 那么让其指向空，然后cur向右移动：cur = cur.right

举例说明：

    给定二叉树：

              1
            /   \
           2     3
          / \   /  \
         4   5 6    7

    Morris遍历流程：
    1. cur在1：首先cur指向根节点1，当前cur有左孩子，所以找到左子树的最右节点5，5的右指针right现在为空，所以让其指向cur（5的right指向1），然后cur向左移动来到2
    2. cur在2：cur有左孩子，找到其左子树的最右节点4，4的right为空，于是让其指向cur（4的right指向2），然后cur向左移动来到4
    3. cur在4：当前cur没有左孩子，因此向右移动，回到2
    4. cur在2：当前cur左子树的最右节点为4，4的right现在指向cur，于是让它指回空，然后cur向右移动来到5
    5. cur在5：当前cur没有左孩子，因此向右移动，回到1
    6. cur在1：cur左子树的最右节点为5，5的right现在指向cur，于是让它指回空，然后cur向右移动来到3
    7. cur在3：cur左子树的最右节点为6，6的right指向空，于是让它指向cur，然后cur向左移动来到6
    8. cur在5：当前cur没有左孩子，因此向右移动，回到3
    9. cur在3：cur左子树的最右节点为6，6的right指向cur，于是让它指向空，然后cur向右移动来到7
    10. cur在7：当前cur没有左孩子，因此向右移动，发现来到的位置上是空，于是整个遍历结束。

    总结下来：cur分别经过了1 2 4 2 5 1 3 5 3 7




## Morris遍历的本质

在经典递归版的遍历中，只要一个节点不空，遍历会来到这个节点三次：
* 首先来到这个节点
* 遍历完它的左子树后再回到这个节点
* 遍历完它的右子树后再回到这个节点

如果把对这个节点的操作放在第一次来到这个节点的时候，就是先序遍历。  
如果把对这个节点的操作放在第二次来到这个节点的时候，就是中序遍历。  
如果把对这个节点的操作放在第三次来到这个节点的时候，就是后序遍历。  

而在Morris遍历中：
* 如果一个节点有左子树，那么Morris遍历会回到它两次。并且第二次回到这个节点时，其左子树上所有的节点一定都已经遍历完了。Morris遍历根据左子树最右节点mostRight的右指针right指向谁来判断是第几次来到当前这个节点（指向空则是第一次，指向cur节点本身则是第二次）。
* 如果一个节点没有左子树，那么Morris遍历只会回到它一次


## 代码
Morris遍历的先序和中序思想一致，只是对节点操作（以打印为例）的时机不一致。在第一次来到节点时操作为先序，在第二次来到节点时操作为中序。

Morris遍历的中序遍历代码：
```java
public static void morrisIn(TreeNode head) {
    if (head == null) {
        return;
    }
    TreeNode cur = head;
    TreeNode mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        //如果cur有左孩子
        if (mostRight != null) {
            //首先找到cur的左子树的最右节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }
            if (mostRight.right == null) {
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else {
                mostRight.right = null;
            }
        }
        //若该节点有左子树，当把左子树处理完第二次回到这个节点时进行打印行为，即为中序遍历。
        //如果该节点没有左子树，那么只会来到这个节点一次，在这时候打印即可。
        System.out.print(cur.val + " ");
        //如果cur没有左孩子，cur直接向右移动
        cur = cur.right;
    }
    System.out.println();
}
```

Morris遍历的先序遍历代码：
```java
public static void morrisIn(TreeNode head) {
    if (head == null) {
        return;
    }
    TreeNode cur = head;
    TreeNode mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        //如果cur有左孩子
        if (mostRight != null) {
            //首先找到cur的左子树的最右节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }
            //若一个节点有左子树，那么当mosRight的right为空时，为第一次到达这个节点的时候。
			//在此时进行打印行为，即为先序遍历
            if (mostRight.right == null) {
                mostRight.right = cur;
                System.out.print(cur.value + " ");
                cur = cur.left;
                continue;
            } else {
                mostRight.right = null;
            }
        }
        //如果一个节点没有左子树，那么只会来到这个节点一次，在这个时候打印即可。
        else {
            System.out.print(cur.value + " ");
        }
        //如果cur没有左孩子，cur直接向右移动
        cur = cur.right;
    }
    System.out.println();
}
```