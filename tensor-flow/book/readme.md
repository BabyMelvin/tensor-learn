# <<面向机器智能的TensorFlow实践>>                                       

# 1.Data flow graphs(数据流图)
TensorFlow的计算模型是`有向图`（directed graph） ， 其中`每个节点`（通常以`圆圈`或`方框表示`） 代表了一些`函数`或`计算`， 而边（通常以箭头或线段表示） 代表了`数值`、 `矩阵`或`张量`。

数据流图极为有用的原因如下。

* 首先， 许多常见的机器学习模型， 如神经网络， 本身就是以有向图的形式表示的， 采用数据流图无疑将使机器学习实践者的实现更为自然。 
* 其次， 通过将计算分解为一些小的、 容易微分的环节， TensorFlow能够自动计算
任意节点关于其他对第一个节点的输出产生影响的任意节点的导数（在TensorFlow中称为“Operation”） 。 计算任何节点（尤其是输出节点） 的导数或梯度的能力对于搭建机器学习模型至关重要。
* 最后， 通过计算的分解， 将计算分布在多个CPU、 GPU以及其
他计算设备上更加容易， 即只需将完整的、 较大的数据流图分解为一些较小的计算图， 并让每台计算设备负责一个独立的计算子
图（此外， 还需一定的逻辑对不同设备间的共享信息进行调度） 。

<image src="image/00-01.png"/>

# 2.软件套件
虽然“TensorFlow”主要是指用于构建和训练机器学习模型的API， 但TensorFlow实际上是`一组需配合使用的软件`：
* `TensorFlow`是用于定义机器学习模型、 用数据训练模型， 并将模型导出供后续使用的API。 虽然实际的计算是用C++编写的， 但主要的API均可通过Python访问。 这使得数据科学家和工程师可利用Python中对用户更为友好的环境， 而将实际计算交给高
效的、 经过编译的C++代码。 TensorFlow虽然也提供了一套可执行TensorFlow模型的C++API， 但在本书编写之时它还`具有较大的
局限性`， 因此**对大多数用户都是不推荐的**。

* `TensorBoard`是一个包含在任意标准TensorFlow安装中的图可视化软件。 当用户在TensorFlow中引入某些TensorBoard的特定运
算时， TensorBoard可读取由TensorFlow计算图导出的文件， 并对分析模型的行为提供有价值的参考。 它对概括统计量、 分析训练
过程以及调试TensorFlow代码都极有帮助。 学会尽早并尽可能多地使用TensorBoard会为使用TensorFlow工作增添趣味性， 并带来
更高的生产效率。
* `TensorFlow Serving`是一个可为部署预训练的TensorFlow模型带来便利的软件。 利用内置的TensorFlow函数， 用户可将自己的
模型导出到可由TensorFlow Serving在本地读取的文件中。 之后， 它会启动一个简单的高性能服务器。 该服务器可接收输入数据，
并将之送入预训练的模型， 然后将模型的输出结果返回。 此外， TensorFlow Serving还可以在旧模型和新模型之间无缝切换， 而不
会给最终用户带来任何停机时间。 虽然Serving可能是TensorFlow生态系统中认可度最低的组成， 它却可能是使TensorFlow有别于其
他竞争者的重要因素。 将Serving纳入生产环境可避免用户重新实现自己的模型——他们只需使用TensorFlow导出的文件。`TensorFlow Serving`完全是用C++编写的， 其API也只能通过C++访问。