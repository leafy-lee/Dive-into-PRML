# 4 信息论 Information Theory

&emsp;&emsp;机器学习的发展，离不开信息论的推动，两者有千丝万缕的联系。信息论主要研究的是对一个信号包含信息的多少进行量化，最早的应用是在一个含有噪声的信道上，用离散的字母表发送消息，例如通过无线电传输来通信。

&emsp;&emsp;在机器学习中，我们也可以把信息论应用于连续型变量，以及将相关的理论知识用于描述数据和模型的不确定性。在第一篇文章曾提到过将随机噪声看做高斯分布是因为它具有最大的不确定性，而不确定性这个概念就属于信息领域的范畴，我们通常使用 ”熵“ (entropy) 来度量。

&emsp;&emsp;高中化学热力学用熵值描述系统混乱程度，这种混乱程度或无序性就代表了我们对系统的未知或不确定程度，我们很难对系统的下一个状态进行描述。同样地，如果将系统换作机器学习里的学习模型，对未知数据进行准确预测，就是要降低模型对数据特征描述的不确定性；更一般地，最小化误差函数，就是一个熵减的过程，此时误差函数就是熵的一种描述。

&emsp;&emsp;这种不确定性往往反映着我们已掌握的信息量，而正是香农 (Cluade Shannon) 在他的著名论文 ”通信的数学原理“ (A Mathematic Theory of Communication) 提出了 ”信息熵“ 的概念，才解决了信息度量的问题，并且量化出信息的作用。机器学习在信息论领域最关注的也是熵这一概念。

## 4.1 信息熵 information Entropy

&emsp;&emsp;一条信息的信息量与其不确定性有直接的联系，当我们对一件事很不确定或者一无所知时，就需要了解大量信息，反之如果对一件事很确定也就意味着我们已掌握很多相关信息；而对于一件我们掌握了部分信息的事件，比如我们知道一件事情发生的概率很小，但此时有人告知我们这件事情要发生，我们将获得大量信息；如果已知一件事情很有可能发生，此时有人告诉我们这件事发生了，这时我们所获取的信息量相比前者就会小很多，因此可以认为，信息量就是不确定性的大小。

&emsp;&emsp;考虑一个猜小球颜色的例子，盒子里有除颜色外其余都相同的四个小球，有黑白两种颜色，随机抓取一个小球，我们和一群观众猜测该小球颜色。如果我们对盒子里两种颜色的小球数量没任何先验了解，很自然地就会认为抓到小球的颜色为黑和白的概率均为 $\frac{1}{2}$ ；但假如所有人都已知该盒子里有三个黑球和一个白球，我们就会判断该小球为黑色的概率为 $\frac{3}{4}$，倾向于猜测小球为黑色，但如果在答案公示前，有知情人员告诉我们（没告诉其他人）抓到的是白球，我们掌握的信息量就会很大（相当于直接确定）并远远大于其他观众；而如果知情人员告诉我们抓到的是黑球，与我们根据已知信息做出的倾向性预测相同，此时我们虽然掌握的信息量也增加了（从比较确定变成确定），但是并没有前一种情况增加的多，所获得的信息也没有比其他人多很多。需要注意的是，信息量只是描述未知状态下的不确定性，并不和未知的真实结果相关。上述案例中，如果抓到的小球是白色，那大部分观众就会猜错，甚至比完全不知道小球颜色数量时猜错的概率更大，但是掌握的信息仍然是多于一无所知时候的。

&emsp;&emsp;上述案例可以看出，概率可以很清晰的描述信息量。从概率角度度量信息的不确定性，考虑一个随机变量 $x$ ，我们对信息内容的度量依赖概率分布 $p(x)$，然后寻找⼀个函数 $h(x)$，它是概率 $p(x)$ 的单调递增函数，根据我们已知的概率大小表达信息量。$h(x)$ 可以这样定义：对于两个不相关的事件 $a$ 和 $b$，我们观察到两个事件同时发⽣时获得的信息应该等于事件各⾃发⽣时获得的信息之和，即 $h(a,b)=h(a)+h(b)$。两个事件是统计独⽴的，因此 $p(a,b)=p(a)p(b)$。根据这两个关系，很容易构造出 $h(x)$ 与 $p(x)$ 的对数关系

$$h(x)=-\log_2p(x)\tag1$$
其中，负号确保了信息的非负特性。低概率事件 $x$ 对应于⾼的信息量，正如前面案例在已知小球颜色数量的情况下抓到白球的小概率事件包含着更丰富的信息。对数的底的选择是任意的。如果遵循信息论的普遍传统，就会使⽤ 2 作为对数的底，此时，$h(x)$ 的单位是⽐特 (bit, binary digit)。大家对计算机中的 bit 相信一定不陌生，它和内存相关，而计算机内存正好表示所存储的信息量，关于这个信息量的描述，有一个更通俗的例子。

&emsp;&emsp;两个人 A 和 B 一起玩一个猜数字游戏，A 随机想一个 32 以内的整数（不包含 0），然后 B 来猜。游戏规定 A 可以回答 B 的问题，比如 B 猜的数字是否比 A 所想的数字要大，我们想想 B 最少提问多少次后，就可以准确猜出 A 所想的数字。B 只需要先问 ”是否大于 16“，如果 A 回答不是，就再问 "是否大于 8"，如果 A 回答是，再问 "是否大于 12"，依次类推，只需要提问 5 次就一定可以准确知道是哪个数字。很容易看出这与二进制是一致的，提问顺序就是依次从高位到低位确定二进制每一位。也就是说，在计算机里储存一个 32 以内（不包含 32）的整数，只需要 5 比特的内存；而如果是描述一个在 $[0,31]$ 上均匀分布的整数这样一个事件的话，它所包含的信息量也是 5 比特。当然，计算机存储的就是信息，而这样一个有 32 种可能情况的事件包含 5 比特信息，恰好也是对数关系，我们再次将概率分布和信息量用同一种表示方式联系起来。

&emsp;&emsp;但是如果 A 在想出这个整数时并不是在 $[1,32]$ 均匀采样，假设 B 已知 A 有 $\frac{2}{3}$ 的可能想出 $[1,16]$ 上的数字，此时他第一次就询问 "是否大于 8"，如果 A 的数字确实是在 $[1,16]$ 上，那么 B 一共只需提问 4 次就可以知道，但是如果该数字在 $[17,32]$ 上，B 需要提问 6 次才能得知。乍看之下，好像信息量也没减少，但如果结合已知的概率分布计算信息量期望，这个事件所包含的信息量应该是 $\frac{2}{3}\times 4+\frac{1}{3}\times 6=\frac{14}{3} \mathrm {bit}<5\mathrm {bit}$，可见我们所掌握的先验知识相比均匀采样的情况多了 $\frac{1}{3}\mathrm{bit}$ 的信息量。另外也发现，虽然对于不均匀分布，信息量貌似会降低，但如果 A 采用高斯分布采样，B 仍然也只能像第一种情况那样从 16 问起，一定程度也说明高斯分布的不确定性较高。当然，这个例子最终还是为了说明，描述信息量需要考虑概率分布的期望。

&emsp;&emsp;现在假设⼀个发送者想传输⼀个随机的离散变量 x 给接收者，离散变量概率分布为 p(x) 。此时传输的平均信息量通通过公式 (1) 关于 p(x) 的期望得到，期望值为

$$H[x]=-\sum_xp(x)\log_2p(x)\tag2$$
这个量被叫做随机变量 $x$ 的熵 (entropy)，需要注意，$\lim_{p\rightarrow \infty}p\log_2 p=0$。

&emsp;&emsp;有了熵这个概念，我们就尝试把它用在更贴近我们实际生活的事情上，比如说，我们想知道一篇 500 字的作文信息熵是多少，首先我们知道，这篇作文是由一些常用汉字组成的，如果常用汉字有 7000 个，且每一个汉字出现概率相同，那么根据公式 (2) 信息熵为 $\frac{1}{7000}\times\log_2 \frac{1}{7000}\times500\approx6500\mathrm {bit}$。但实际上每一个汉字的使用频率是不相等的，这就相当于引入先验知识，这种情况这篇文章包含的信息小于 $6500\mathrm {bit}$。到这我们又可以发现，引入先验知识后信息熵总会下降，而公式 (2) 取最大值的时候恰好也是每一个结果都等概率出现的时候。因为不借助任何先验知识，我们总会等概率的猜测每一种结果出现的可能性，这个时候就是不确定性最高的。有使用过机器学习或深度学习做分类的同学应该会发现，在我们对模型参数做了初始化后不进行学习直接分类，对于一个十分类的问题，起始准确率总在 $10\%$ 左右，这个时候也没进行学习，意味着我们没有获得任何信息。但是这并不意味着均匀分布的不确定性就最大，后面在高斯分布一节中，我们会证明在均值和方差一定时，高斯分布拥有最大熵。

&emsp;&emsp;如果我们把上述例子的的作文输入到计算机，同样也需要 $6500\mathrm {bit}$ 内存，但是我们已知这篇作文实际信息熵是小于这个值的，这里就造成了信息的冗余 (redundancy)，这在信息编码里是不可或缺的。值得一提的是，所有语言里，汉语的冗余度是相对较小的，同一段文字翻译成中英两种版本，中文总是要简洁一些。

&emsp;&emsp;到此，回到我们开头第一句所提的，信息和不确定性的联系，实际上信息就是消除不确定性的，英语里信息和情报都属于同一个单词 information，在战争中，谍战或情报战意义非常重大，有时候 1 比特的信息就抵得过千军万马。第二次世界大战的欧洲战场，纳粹德国曾一度兵临莫斯科城下，苏联兵力不继，斯大林在欧洲已无兵可派，虽然在东线西伯利亚有 60 万苏联红军驻军，但为了防止日本入侵不敢轻易调离，因为斯大林不知道日军将北上进攻苏联，还是南下向美国开战。这时传奇间谍佐尔格向斯大林发送一则我们看来只有 1 比特的情报，“日军将南下”，于是斯大林大胆地从东线抽调了 26 个精良师增援西线，莫斯科保卫战一举扭转战局，改变了二战进程。我们将第三次科技革命后的时代称为信息时代，现在我们的生活也趋向于信息化，现在很多不同形式的竞争实际上就是一种信息战，掌握更多的信息或情报，也意味着更主动的话语权。

## 4.2 熵的物理意义 Entropy in Physics

&emsp;&emsp;这一节我们讨论熵的物理意义。熵的概念最早起源于热⼒学，用于描述系统的⽆序或混乱程度，然后统计力学对熵进行了量化，可以这样理解熵的含义：考虑⼀个集合，包含 $N$ 个完全相同的物体，这些物体要被分到若⼲个箱⼦中，使得第 $i$ 个箱⼦有 $n_i$ 个物体。考虑把物体分配到箱⼦中不同⽅案的数量。有 $N$ 种⽅式选择第⼀个物体，有 $N-1$ 种⽅式选择第⼆个物体，依次类推，总共有 $N!$ 种⽅式把 $N$ 个物体分配到箱⼦中。然⽽，我们不想区分每个箱⼦内物体的重新排列。在第 $i$ 个箱⼦中，有 $n_i!$ 种⽅式对物体重新排序，因此把 $N$ 个物体分配到箱⼦中的总⽅案数量为

$$W=\frac{N!}{\prod_{i}n_i!}\tag3$$

&emsp;&emsp;这被称为乘数 (multiplicity)。熵被定义为通过适当的参数放缩后的对数乘数，即

$$H=\frac{1}{N}\ln W=\frac{1}{N}\ln N!-\frac{1}{N}\sum_{i}\ln n_i!\tag4$$

&emsp;&emsp;现在考虑极限 $N\rightarrow\infty$，并且保持⽐值 $\frac{n_i}{N}$ 固定，使⽤ Stirling 估计

$$\ln N!\simeq N\ln N-N\tag5$$

可以得到
$$H=-\lim_{N\rightarrow\infty}\sum_{i}\left ( \frac{n_i}{N} \right )\ln \left ( \frac{n_i}{N} \right )=-\sum_i p_i\ln p_i\tag6$$
其中用到 $\sum_in_i=N$，这⾥，$p_i=\lim_{N\rightarrow\infty}\left ( \frac{n_i}{N} \right )$ 是⼀个物体被分配到第 $i$ 个箱⼦的概率，箱⼦中物体的具体分配⽅案被称为微观状态 (microstate)，箱子中物体数的分布占比 $\frac{n_i}{N}$ 被称为宏观状态 (macrostate)。

&emsp;&emsp;可以把箱⼦表述成离散随机变量 $X$ 的状态 $x_i$，其中 $P(X=x_i)=p_i$。这样，随机变量 $X$ 的熵为

$$H[p]=-\sum_{i}p(x_i)\ln p(x_i)\tag7$$

&emsp;&emsp;如果分布 $p(x_i)$ 不是等概率分布，特别是，有几个值的概率比较高的话，熵就会相对较低。当 $p_i=1$ 且所有的 $p_{j\ne i}=0$ 时，熵取得最⼩值 0。可以证明，当所有 $p(x_i)$ 都相等且为 $p(x_i)=\frac{1}{M}$ 时，熵取得最⼤值，其中，$M$ 是状态 $x_i$ 的总数，此时对应熵值为 $H=\ln M$。这个结果可以应用 Jensen 不等式 (Jensen's inequality) 一步得出结果。下面我们介绍 Jensen 不等式。

&emsp;&emsp;Jensen 不等式的应用对象是凸函数 (convex function) 。一个函数 $f(x)$ 在 $x=a$ 到 $x=b$ 之间任意点 $x$ 都可以写成 $\lambda a$+(1-\lambda)b 的形式，其中 $0\leq \lambda \leq 1$，那么函数图像 $(a,f(a))$ 和 $(b,f(b))$ 之间的连线上对应的点就可以写成 $\lambda f(a)+(1-\lambda)f(b)$，而函数 $f(x)$ 上的对应值为 $f(\lambda a+(1-\lambda)b)$。如果 $f(x)$ 为凸函数，就具有性质

$$f(\lambda a+(1-\lambda)b)\leq\lambda f(a)+(1-\lambda)f(b)\tag8$$

&emsp;&emsp;这也等价于要求函数的⼆阶导数处处为正，特别地，如果仅在 $\lambda=0$ 和 $\lambda =1$ 处取等号，就称函数 $f(x)$ 为严格凸函数。反之，如果函数具有相反的性质，我们就称其为凹函数 (concave function)。对于凸函数 $f(x)$ 有

$$f\left (\sum_{i=1}^M\lambda_ix_i\right )\leq\sum_{i=1}^M\lambda_if(x_i)\tag9$$

&emsp;&emsp;我们可以使用数学归纳法来证明。假设 $M=1$ ，根据公式 (8) 很容易证明，然后令 $M=M+1$，证明

$$\begin{align} f\left (\sum_{i=1}^{M+1}\lambda_ix_i \right)&=f\left (\lambda_{M+1}x_{M+1}+\sum_{i=1}^M\lambda_ix_i\right )\\ &=f\left (\lambda_{M+1}x_{M+1}+(1-\lambda_{M+1})\sum_{i=1}^M\frac{\lambda_i}{1-\lambda_{M+1}}x_i\right )\\ &\leq\lambda_{M+1}f(x_{M+1})+(1-\lambda_{M+1})\sum_{i=1}^M\frac{\lambda_i}{1-\lambda_{M+1}}f(x_i)\\ &= \sum_{i=1}^{M+1}\lambda_if(x_i) \end{align}\tag{10}$$
其中从第二步到第三步就应用了公式 (8)。因为已知 $M=1$ 的情况成立，应用公式 (10) 很容易得出公式 (9)。

&emsp;&emsp;如果我们把 $\lambda_i$ 看成取值为 $\left \{ x_i \right \}$ 的离散变量 $x$ 的概率分布，那么公式 (10) 就可写成

$$f(\mathbb E[x])\leq\mathbb E[f(x)]\tag{11}$$

&emsp;&emsp;对于连续变量，Jensen 不等式为

$$f\left ( \int xp(x)\mathrm dx \right )\leq \int f(x)p(x)\mathrm dx\tag{12}$$

&emsp;&emsp;再回到公式 (7)，此时的 $f(x)$ 为 $\ln (x)$ ，同时 $\ln (x)$ 为凹函数，我们只需要将公式 (9) 反过来使用即可，即

$$\begin{align} H(x)=-\sum_{i=1}^Mp(x_i)\ln p(x_i)&=\sum_{i=1}^M p(x_i)\ln \frac{1}{p(x_i)}\\ &\leq\ln \left ( \sum_{i=1}^M p(x_i)\frac{1}{p(x_i)}\right )\\ &=\ln M \end{align}\tag{13}$$

&emsp;&emsp;其中又根据
$$\ln \left ( \sum_{i=1}^M p(x_i)\frac{1}{p(x_i)}\right )=\ln \left ( \sum_{i=1}^M p(x_i)\right )+\ln \left ( \sum_{i=1}^M \frac{1}{p(x_i)}\right )=\ln \left ( \sum_{i=1}^M \frac{1}{p(x_i)}\right )\tag{14}$$
可以得出 $H=\ln M$ 时 $p(x_i)=\frac{1}{M}$。

&emsp;&emsp;我们可以把熵的定义扩展到连续变量 $x$ 的概率分布 $p(x)$，将 $x$ 切分成宽度为 $\Delta$ 的箱⼦，然后假设 $p(x)$ 是连续的。根据均值定理 (mean value theorem)，对于每个这样的箱⼦，⼀定存在⼀个值 $x_i$ 使得

$$\int_{i\Delta}^{(i+1)\Delta}p(x)\mathrm dx=p(x_i)\Delta\tag{15}$$

&emsp;&emsp;只要 $x$ 落在第 $i$ 个箱⼦中，我们就把 $x$ 赋值为 $x_i$，观察到值 $x_i$ 的概率为 $p(x_i)\Delta$，即我们使用概率 $p(x_i)\Delta$ 代表了落在区间 $[{i\Delta},{(i+1)\Delta}]$ 上的概率，这就变成了离散的分布，这种情形下熵的形式为

$$H_\Delta=-\sum_{i}p(x_i)\Delta\ln (p(x_i)\Delta)=-\sum_{i}p(x_i)\Delta\ln p(x_i)-\ln \Delta\tag{16}$$

&emsp;&emsp;推导时使⽤了 $\sum_{i}p(x_i)\Delta=1$，现在省略公式 (16) 第二项，然后考虑极限 $\Delta\rightarrow0$，公式 (16) 就变成 $-\int p(x)\ln p(x)\mathrm d x$，这被称为微分熵 (differential entropy)。同时被忽略的项 $-\ln \Delta$ 正是熵的离散形式与连续形式的差，在 $\Delta\rightarrow\infty$ 时会发散，这就反映出将熵的离散形式转换为连续形式需要大量比特位，这与我们在计算机里使用浮点数存储连续型变量需要大量内存的现象一致。

&emsp;&emsp;微分熵最大化时对应 $p(x)$ 为高斯分布，在第九章会有详细证明。

&emsp;&emsp;假设有⼀个联合概率分布 $p(x,y)$。我们从这个概率分布中抽取了⼀对 $x$ 和 $y$。如果 $x$ 的值已知，那么需要确定对应 $y$ 值所需的附加信息就是 $-\ln p(y|x)$。因此，⽤来确定 $y$ 值的平均 附加信息可以写成

$$H[y|x]=-\int\int p(x,y)\ln p(y|x)\mathrm dy \mathrm dx\tag{17}$$
这被称为给定 $x$ 的情况下，$y$ 的条件熵 (conditional entropy)。条件熵满⾜下⾯的关系

$$H[x,y]=H[y|x]+H[x]\tag{18}$$
其中，$H[x,y]$ 是 $p(x,y)$ 的微分熵，$H[x]$ 是边缘分布 $p(x)$ 的微分熵。因此，描述 $x$ 和 $y$ 所需的信息是描述 $x$ ⾃⼰所需的信息，再$加上给定 $x$ 的情况下确定 $y$ 所需的额外信息。

## 4.3 相对熵 Relative Entropy

&emsp;&emsp;接下来介绍相对熵 (relative entropy)，相对熵最早用在信号处理上，如果两个随机信号的相对熵越小，说明两个信号越接近，否则信号差异越大。如果用来处理某一事件的信息，相对熵用来衡量两个概率分布函数的相似性，相对熵越大，两个随机分布的差异越大，反之越小。考虑某个未知的分布 $p(x)$，以及使⽤某个分布 $q(x)$ 对它进⾏近似。如果我们使⽤ $q(x)$ 表示信息，由于不是真实分布，我们就需要⼀些附加的信息，这部分信息就是用来度量两个分布的平均信息差，也被称为分布 $p(x)$ 和 $q(x)$ 之间的相对熵或 Kullback-Leibler 散度（KL 散度），为

$$\mathrm {KL}(p||q)=-\int p(x)\ln q(x)\mathrm d x-\left ( -\int p(x)\ln p(x)\mathrm d x \right )=-\int p(x)\ln\frac{ q(x)}{p(x)}\mathrm d x\tag{19}$$

&emsp;&emsp;KL 散度满足 $\mathrm {KL}(p(x)||q(x))\geq0$，当且仅当 $p(x)=q(x)$ 时等号成⽴。使用 Jensen 不等式即可证明

$$\mathrm {KL}(p||q)=-\int p(x)\ln\frac{ q(x)}{p(x)}\mathrm d x\geq-\ln \int q(x)\mathrm d x\tag{20}$$

&emsp;&emsp;再结合归一化 $\int q(x)\mathrm d x=1$，可得 $\mathrm {KL}(p(x)||q(x))\geq0$。实际上，$-\ln x$ 是严格凸函数，因此只有 $q(x)=p(x)$ 对于所有 $x$ 都成⽴时，等号才成⽴，可以看出，如果我们使用了不同与真实概率分布的概率密度估计，那我们一定会增加两个分布 KL 散度的额外信息量，这在通信应用中就不可避免的造成了编码效率损失。

&emsp;&emsp;另外，相对熵是不对称的，也就是 $\mathrm {KL}(p||q)\ne\mathrm {KL}(q||p)$，从定义式中就可以看出。有时不是很方便，为了使其对称，Jensen 和 Shannon 提出一种新的计算方式

$$\mathrm{JS}(p||q)=\frac{1}{2}[\mathrm {KL}(p||q)+\mathrm {KL}(q||p)]\tag{21}$$

&emsp;&emsp;假设数据通过未知分布 $p(x)$ ⽣成，对 $p(x)$ 建模可以使⽤⼀些参数分布 $q(x|\theta)$ 来近似这个分布。$q(x|\theta)$ 由可调节的参数 $\theta$ 控制（例如⼀个⾼斯分布）。⼀种确定 $\theta$ 的⽅式是最⼩化 $p(x)$ 和 $q(x|\theta)$ 关于 $\theta$ 的 KL 散度。虽然我们不知道 $p(x)$，但是，可以从观察到的服从分布 $p(x)$ 的有限训练数据 $\left \{ x_n\right \}$ 通过这些数据的有限加和近似 $p(x)$ 的期望，即

$$\mathrm {KL}(p||q)\simeq \frac{1}{N}\sum_{n=1}^N\left \{ -\ln q(x_n|\theta) +\ln p(x_n)\right \}\tag{22}$$

&emsp;&emsp;第⼀项是使⽤训练集估计的分布 $q(x|\theta)$ 下 $\theta$ 的负对数似然函数，第二项与 $\theta$ 无关，所以最⼩化 KL 散度等价于最⼤化 $\theta$ 似然函数。后面在第十章贝叶斯网络中将会见到一种引入贝叶斯定理，使用一个参数分布 $q(\theta|x)$ 去逼近 $\theta$ 的先验分布 $p(\theta)$ 的方法，这种方法对于抑制网络过拟合，提高模型泛化能力有重要作用。

## 4.4 互信息 Mutual Information

&emsp;&emsp;现在考虑由联合概率分布 $p(x,y)$ 给出的两个随机变量 $x$ 和 $y$ 组成的数据集，如果两个变量是相互独⽴的，那么联合分布可以分解为边缘分布的乘积 $p(x,y)=p(x)p(y)$。如果变量不完全独⽴，我们也可以通过考察联合概率分布与边缘概率分布乘积之间的 KL 散度来判断它们是否近似于相互独⽴。此时，KL 散度为

$$I[x,y]=\mathrm {KL}(p(x,y)||p(x)p(y))=-\int\int p(x,y)\ln \left ( \frac{p(x)p(y)}{p(x,y)} \right )\mathrm d x\mathrm dy\tag{23}$$

&emsp;&emsp;这被称为 $x$ 和 $y$ 的互信息 (mutual information)，就是已知变量 $x$ 的前提下，$y$ 的信息熵与条件熵的差异。在 $p(x,y)=p(y|x)p(x)$ 时，互信息和条件熵之间的关系为

$$\begin{align} I[x,y]&=-\int\int p(x,y)\ln p(x)\mathrm d x\mathrm dy-\int\int p(x,y)\ln p(y)\mathrm d x\mathrm dy+\int\int p(x,y)\ln p(x,y)\mathrm d x\mathrm dy\\ &=-\int\int p(x,y)\ln p(y)\mathrm d x\mathrm dy+\int\int p(x,y)\ln p(y|x)\mathrm d x\mathrm dy\\ &=H[y]-H[y|x]\\ \end{align}\tag{24}$$

&emsp;&emsp;可以看出，所谓两个事件的相关性度量，就是在了解其中一个变量 $x$ 的前提下，对降低另一个变量 $y$ 的不确定性所提供的信息量。根据贝叶斯定理，我们可以把 $p(y)$ 看成 $y$ 的先验概率分布，把 $p(y|x)$ 看成观察到新数据 $x$ 之后的后验概率分布。因此互信息表示⼀个新的观测数据 $x$ 造成的随机变量 $y$ 的不确定性的减⼩。

