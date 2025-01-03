## 23331147苏长皓

## 7.22


```cpp
// 边结点的结构定义
struct ArcNode {
    int adjvex;           // 该弧指向的顶点位置
    ArcNode *nextarc;     // 指向下一条弧的指针
};

// 顶点的结构定义
struct VNode {
    VertexType data;      // 顶点信息
    ArcNode *firstarc;    // 指向第一条依附该顶点的弧
};

// 图的结构定义
struct ALGraph {
    VNode vertices[MAX_VERTEX_NUM];  // 顶点数组
    int vexnum, arcnum;              // 图的顶点数和弧数
};

bool visited[MAX_VERTEX_NUM];    // 访问标记数组

// 基于DFS的路径查找算法
bool ExistPath(ALGraph G, int vi, int vj) {
    // 初始化访问标记数组
    for(int i = 0; i < G.vexnum; i++) {
        visited[i] = false;
    }
    return DFS(G, vi, vj);
}

// 深度优先搜索函数
bool DFS(ALGraph G, int start, int end) {
    if(start == end) return true;    // 找到目标顶点
    
    visited[start] = true;    // 标记当前顶点已访问
    
    // 遍历当前顶点的所有邻接点
    for(ArcNode *p = G.vertices[start].firstarc; p != NULL; p = p->nextarc) {
        int w = p->adjvex;
        if(!visited[w] && DFS(G, w, end)) {
            return true;    // 找到路径则返回true
        }
    }
    return false;    // 未找到路径
}
```

算法思路：通过深度优先搜索，从起点vi开始递归地访问每个邻接顶点。在访问过程中使用visited数组标记已访问顶点，避免重复访问。当到达终点vj或搜索完所有可能路径时返回结果。算法的时间复杂度为O(V + E)，空间复杂度为O(V)，其中V为顶点数，E为边数。

## 7.23


算法要求：判断以邻接表方式存储的有向图中是否存在由顶点vi到顶点vj的路径(i≠j)，使用广度优先搜索策略实现。

```cpp
// 边结点的结构定义
struct ArcNode {
    int adjvex;           // 该弧指向的顶点位置
    ArcNode *nextarc;     // 指向下一条弧的指针
};

// 顶点的结构定义
struct VNode {
    VertexType data;      // 顶点信息
    ArcNode *firstarc;    // 指向第一条依附该顶点的弧
};

// 图的结构定义
struct ALGraph {
    VNode vertices[MAX_VERTEX_NUM];  // 顶点数组
    int vexnum, arcnum;              // 图的顶点数和弧数
};

bool visited[MAX_VERTEX_NUM];    // 访问标记数组

// 基于BFS的路径查找算法
bool ExistPath(ALGraph G, int vi, int vj) {
    int queue[MAX_VERTEX_NUM];    // 顶点队列
    int front = 0, rear = 0;      // 队列头尾指针
    
    // 初始化访问标记数组
    for(int i = 0; i < G.vexnum; i++) {
        visited[i] = false;
    }
    
    // 起点入队并标记已访问
    queue[rear++] = vi;
    visited[vi] = true;
    
    // 当队列非空时循环
    while(front < rear) {
        int v = queue[front++];    // 取出队首顶点
        
        // 找到终点，返回true
        if(v == vj) return true;
        
        // 遍历该顶点的所有邻接点
        for(ArcNode *p = G.vertices[v].firstarc; p != NULL; p = p->nextarc) {
            int w = p->adjvex;
            // 若该邻接点未被访问，则入队并标记已访问
            if(!visited[w]) {
                queue[rear++] = w;
                visited[w] = true;
            }
        }
    }
    return false;    // 搜索完毕未找到路径
}
```

算法思路：通过广度优先搜索实现路径查找。使用队列存储待访问的顶点，从起点vi开始，将其邻接点逐层加入队列。每访问一个顶点时，检查是否为终点vj。使用visited数组标记已访问顶点，避免重复访问。当找到终点或队列为空时返回结果。算法的时间复杂度为O(V + E)，空间复杂度为O(V)，其中V为顶点数，E为边数。

与深度优先搜索相比，广度优先搜索按层次访问顶点，能保证找到的是最短路径（以边数计）。但在空间使用上可能需要更多，因为要同时保存同一层的所有顶点。

## 7.29

一条长度为k的简单路径需要恰好包含k条边，并且不重复经过任何顶点。我们可以使用深度优先搜索的思想，在搜索过程中记录已经走过的路径长度。当路径长度达到k并且到达目标顶点时，说明找到了符合要求的路径。

```cpp
// 邻接表的基本结构定义
struct ArcNode {
    int adjvex;           // 该弧指向的顶点位置
    ArcNode *nextarc;     // 指向下一条弧的指针
};

struct VNode {
    VertexType data;      // 顶点信息
    ArcNode *firstarc;    // 指向第一条依附该顶点的弧
};

struct ALGraph {
    VNode vertices[MAX_VERTEX_NUM];  // 顶点数组
    int vexnum, arcnum;              // 图的顶点数和弧数
};

bool visited[MAX_VERTEX_NUM];    // 访问标记数组

// 搜索长度为k的路径
bool FindPath(ALGraph G, int v1, int v2, int k) {
    // 初始化访问标记数组
    for(int i = 0; i < G.vexnum; i++) {
        visited[i] = false;
    }
    return DFS(G, v1, v2, k, 0);
}

// depth表示当前路径长度
bool DFS(ALGraph G, int start, int end, int k, int depth) {
    // 当前路径长度等于k时，检查是否到达终点
    if(depth == k) {
        return start == end;
    }
    
    // 如果当前路径长度已超过k，返回false
    if(depth > k) return false;
    
    visited[start] = true;    // 标记当前顶点已访问
    
    // 遍历所有邻接点
    for(ArcNode *p = G.vertices[start].firstarc; p != NULL; p = p->nextarc) {
        int w = p->adjvex;
        if(!visited[w]) {
            // 继续搜索，路径长度加1
            if(DFS(G, w, end, k, depth + 1)) {
                return true;
            }
        }
    }
    
    visited[start] = false;    // 回溯时取消标记
    return false;
}
```

算法思路说明：
1. 使用深度优先搜索策略，但增加了对路径长度的跟踪
2. 通过depth参数记录当前路径包含的边数
3. 在搜索过程中使用visited数组确保不重复访问顶点
4. 需要注意的是在回溯时要取消顶点的访问标记，因为同一个顶点可能出现在不同的简单路径中
5. 当路径长度达到k时，检查是否到达目标顶点
6. 如果路径长度超过k，则当前路径不满足要求，返回false继续搜索其他可能的路径

算法的时间复杂度为O(V^k)，其中V为顶点数，因为在最坏情况下需要检查所有可能的k长度路径。空间复杂度为O(V)，主要用于递归调用栈和访问标记数组。