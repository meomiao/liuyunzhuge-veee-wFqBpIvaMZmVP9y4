
书接上回，我们继续来聊聊图的遍历与实现。


![](https://img2024.cnblogs.com/blog/386841/202411/386841-20241104004140970-1224371762.png)


# ***01***、遍历


在图的基本功能中有个很重要的功能——遍历，遍历顾名思义就是把图上所有的点都访问一遍，具体来说就是从一个连通的图中某一个点出发，沿着边访问所有的点，并且每个点只能访问一遍。


下面我们介绍两种常见的遍历方式：深度优先遍历（DFS）和广度优先遍历（BFS）。


## 1、深度优先遍历


如果我们把边当作路，深度优先遍历就是路一直往下走，直到没路了再返回走其他路。其实优点像树的先序遍历从根节点沿着子节点一直向下直到叶子节点再调头。


下面我们梳理一下深度优先遍历大致分为以下几个步骤：


（1）从图中任意一个点A出发，并访问点；


（2）找出点A的第一个未被访问的邻接点，并访问该点；


（3）以该点为新的点，重复步骤（2），直至新的邻接点没有未被访问的邻接点；


（4）返回前一个点并依次访问前一个点为未被访问的其他邻接点，并访问该点；


（5）重复步骤（3）和（4），直至所有点都被访问过；


![](https://img2024.cnblogs.com/blog/386841/202411/386841-20241104004148071-761195456.jpg)


如上图演示了从点A出发进行深度优先遍历过程，其中红色虚线表示前进路线，蓝色虚线表示回退路线。最后输出：A\-\>B\-\>E\-\>F\-\>C\-\>G\-\>D。


## 2、广度优先遍历


如果说深度优先遍历是找到一条路一直走到底，那么广度优先遍历就是先把所有的路走一步再说。其实优点像树的层次遍历从根节点出发先遍历其子节点然后再遍历其孙子节点直至遍历完所有节点。


下面我们梳理一下广度优先遍历大致分为以下几个步骤：


（1）从图中任意一点A出发，并访问点A；


（2）依次访问点A所有未被访问的邻接点，访问完邻接点后，然后按邻接点顺序把邻接点作为新的出发执行步骤（1）；


（3）重复步骤（1）和（2）直至所有点都被访问到。


![](https://img2024.cnblogs.com/blog/386841/202411/386841-20241104004155261-1557567463.jpg)


如上图演示了从点A出发进行广度优先遍历过程，其中红色虚线表示前进路线。最后输出：A\-\>B\-\>C\-\>D\-\>E\-\>F\-\>G。


# ***02***、实现（邻接矩阵）


下面我们就以邻接矩阵的存储方式实现一个无向图。


## 1、定义


根据图的定义，我们需要定义点集合、边集合两个私有变量用于存储核心数据，为了操作访问我们再定义点数量和边数量两个私有变量，代码如下：



```
//点集合
private T[] _vertexArray { get; set; }
//边集合
private int[,] _edgeArray { get; set; }
//点数量
private int _vertexCount;
//边数量
private int _edgeCount { get; set; }

```

## 2、初始化 Init


此方法主要是初始化上面定义的私有变量，同时确定点集合大小，具体代码如下：



```
//初始化
public MyselfGraphArray Init(int length)
{
    //初始化指定长度点集合
    _vertexArray = new T[length];
    //初始化指定长度边集合
    _edgeArray = new int[length, length];
    //初始化点数量
    _vertexCount = 0;
    //初始化边数量
    _edgeCount = 0;
    return this;
}

```

## 3、获取点数量 VertexCount


我们可以通过点数量私有变量快速获取图的点数量，代码如下：



```
//返回点数量
public int VertexCount
{
    get
    {
        return _vertexCount;
    }
}

```

## 4、获取边数量 EdgeCount


我们可以通过边数量私有变量快速获取图的点数量，代码如下：



```
//返回边数量
public int EdgeCount
{
    get
    {
        return _edgeCount;
    }
}

```

## 5、获取点索引 GetVertexIndex


该方法是通过点元素获取其索引值，具体代码如下：



```
//返回指定点元素的索引   
public int GetVertexIndex(T vertex)
{
    if (vertex == null)
    {
        return -1;
    }
    //根据值查找索引
    return Array.IndexOf(_vertexArray, vertex);
}

```

## 6、获取点元素 GetVertexByIndex


该方法通过点索引获取点元素，具体代码如下：



```
//返回指定点索引的元素
public T GetVertexByIndex(int index)
{
    //如果索引非法则报错
    if (index < 0 || index > _vertexArray.Length - 1)
    {
        throw new InvalidOperationException("索引错误");
    }
    return _vertexArray[index];
}

```

## 7、插入点 InsertVertex


插入点元素时，我们需要先通过点元素获取其索引，如果索引已存在或者点集合已经满了则直接返回，否则添加点元素同时更新点数量，具体代码如下：



```
//插入点
public void InsertVertex(T vertex)
{
    //获取点索引
    var index = GetVertexIndex(vertex);
    //如果索引大于-1说明点已存在，则直接返回
    if (index > -1)
    {
        return;
    }
    //如果点集合已满，则直接返回
    if (_vertexCount == _vertexArray.Length)
    {
        return;
    }
    //添加点元素，并且更新点数量
    _vertexArray[_vertexCount++] = vertex;
}

```

## 8、插入边 InsertEdge


插入边时可以同时指定边的权值。我们首先需要把两个点元素转换为点索引，同时验证索引，验证不通过则直接返回。否则开始添加边，因为无向图的特性，所以需要添加两点索引相反的边。同时更新边数量，具体代码如下：



```
//插入边
public void InsertEdge(T vertex1, T vertex2, int weight)
{
    //根据点元素获取点索引
    var vertexIndex1 = GetVertexIndex(vertex1);
    //如果索引等于-1说明点不存在，则直接返回
    if (vertexIndex1 == -1)
    {
        return;
    }
    //根据点元素获取点索引
    var vertexIndex2 = GetVertexIndex(vertex2);
    //如果索引等于-1说明点不存在，则直接返回
    if (vertexIndex2 == -1)
    {
        return;
    }
    //更新两点关系，即边信息
    _edgeArray[vertexIndex1, vertexIndex2] = weight;
    //用于无向图，对于有向图则删除此句子
    _edgeArray[vertexIndex2, vertexIndex1] = weight;
    //更新边数量
    _edgeCount++;
}

```

## 9、获取边权值 GetWeight


该方法可以获取边的权值，权值可以根据需要在插入边方法中设置，需要对输入的点进行验证，如果点不存在则报错，具体代码如下：



```
//返回两点之间边的权值
public int GetWeight(T vertex1, T vertex2)
{
    //根据点元素获取点索引
    var vertexIndex1 = GetVertexIndex(vertex1);
    //如果索引等于-1说明点不存在
    if (vertexIndex1 == -1)
    {
        //如果未找到点则报错
        throw new KeyNotFoundException($"点不存在");
    }
    //根据点元素获取点索引
    var vertexIndex2 = GetVertexIndex(vertex2);
    //如果索引等于-1说明点不存在
    if (vertexIndex2 == -1)
    {
        //如果未找到点则报错
        throw new KeyNotFoundException($"点不存在");
    }
    return _edgeArray[vertexIndex1, vertexIndex2];
}

```

## 10、深度优先遍历 DFS


深度优先遍历正常有两种实现方法，一种是使用递归调用，一种是使用栈结构实现，下面我们使用递归的方式来实现。


因为我们需要保证每个点只会被访问一次，因此需要定义一个数组用来记录元素已经被访问过。我们这里是以无向图为例，因为无向图的对称性，索引我们选用一维数组即可满足记录被访问元素，而如果是有向图我们则需要使用二维数组记录被访问元素。


具体代码如下：



```
//深度优先遍历
public void DFS(T startVertex)
{
    //根据点元素获取点索引
    var startVertexIndex = GetVertexIndex(startVertex);
    //如果索引等于-1说明点不存在
    if (startVertexIndex == -1)
    {
        //如果未找到点则报错
        throw new KeyNotFoundException($"点不存在");
    }
    //定义已访问标记数组
    //因为无向图对称特性因此一维数组即可
    //如果是有向图则需要定义二维数组
    var visited = new bool[_vertexCount];
    DFSUtil(startVertexIndex, visited);
    Console.WriteLine();
}
//深度优先遍历
private void DFSUtil(int index, bool[] visited)
{
    //标记当前元素已访问过
    visited[index] = true;
    //打印点
    Console.Write(_vertexArray[index] + " ");
    //遍历查找与当前元素相邻的元素
    for (var i = 0; i < _vertexCount; i++)
    {
        //如果是相邻的元素，并且元素未被访问过
        if (_edgeArray[index, i] == 1 && !visited[i])
        {
            //则递归调用自身方法
            DFSUtil(i, visited);
        }
    }
}

```

## 11、广度优先遍历 BFS


广度优先遍历可以借助队列来实现。首先把起始点添加入队列，然后把点出队列，同时把该点的所有邻接点添加入队列，循环往复，一直到把所有元素处理完为止。



```
//广度优先遍历
public void BFS(T startVertex)
{
    //根据点元素获取点索引
    var startVertexIndex = GetVertexIndex(startVertex);
    //如果索引等于-1说明点不存在
    if (startVertexIndex == -1)
    {
        //如果未找到点则报错
        throw new KeyNotFoundException($"点不存在");
    }
    //定义已访问标记数组
    //因为无向图对称特性因此一维数组即可
    //如果是有向图则需要定义二维数组
    var visited = new bool[_vertexCount];
    //使用队列实现广度优先遍历
    var queue = new Queue<int>();
    //将起点入队
    queue.Enqueue(startVertexIndex);
    //标记起点为已访问
    visited[startVertexIndex] = true;
    //遍历队列
    while (queue.Count > 0)
    {
        //出队点
        var vertexIndex = queue.Dequeue();
        //打印点
        Console.Write(_vertexArray[vertexIndex] + " ");
        //遍历查找与当前元素相邻的元素
        for (var i = 0; i < _vertexCount; i++)
        {
            //如果是相邻的元素，并且元素未被访问过
            if (_edgeArray[vertexIndex, i] == 1 && !visited[i])
            {
                //则将相邻元素索引入队
                queue.Enqueue(i);
                //并标记为已访问
                visited[i] = true;
            }
        }
    }
    Console.WriteLine();
}

```

***注***：测试方法代码以及示例源码都已经上传至代码库，有兴趣的可以看看。[https://gitee.com/hugogoos/Planner](https://github.com)


 本博客参考[MeoMiao 萌喵加速](https://biqumo.org)。转载请注明出处！
