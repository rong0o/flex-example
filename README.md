>摘要：之前对于flex只是简单的理解与记忆，没有深入思考各个属性存在的理由与价值，因此每次隔段时间不用就忘了。
这次用自己的理解深入思考了下flex布局的各个属性配置，发现了许多以前忽视的细节，总结如下。下面均以主轴为水平轴的情况为例来理解flex。

### 一、对于容器盒子，可设置属性
- flex-direction
- flex-wrap
- flex-flow
- justify-content
- align-items
- align-content

对于父盒子，无非就是几种设置：
1) 内部的元素是否以flex布局展示 —— display:flex 
2) 内部的元素是横向排列还是纵向排列 —— flex-direction,即决定了主轴和交叉轴 
3) 元素的对齐方式： 
    - 主轴上：justify-content  
    - 交叉轴上：align-item、align-content （只有一根主轴时，即只有一行时无效）
    - 对齐方式主要是：flex-start, flex-end, center，只有中间有空隙：space-between(space：空隙)，周围都有空隙：space-around
4) 交叉轴大小自动填满容器，比如纵轴，希望height是自动填满的，而不是参差不齐的，这时就可以利用stretch将元素在交叉轴方向填满容器【前提是item高度未设置具体值,并且容器有高度】，所以这个对齐属性值也只在交叉轴的两个对齐配置align-item、align-content上有。在主轴上的size则要利用下面的item本身的flex属性设置来实现

### 二、对于子元素item，可设置属性
- order
- flex-grow
- flex-shrink
- flex-basis
- flex
- align-self

对于子元素item 无非就是几种设置：
1) item 的排序——order，显然这个每个item索引值不一样，所以必须放在item上设置
2) 元素以flex布局展示时，width为100%的默认设置会失效，此时的默认width将由item 内容撑开，此时衍生出两个概念：
    - 剩余空间，除去item的width或flex-basis后的剩余空间 <br/>
    - flex-basis，就可以理解为设置flex item 的width,但是优先级高于width, 默认值是auto,此时就是width值<br/>

    因此剩余空间如何分配，就可以转化为如何设置item在主轴上的大小（即水平排列时的宽度），并且这种设置不是固定值，剩余空间大小是由视窗大小决定的，所以这种设置也可以看成百分比设置，只是省去了我们计算百分比的繁琐过程。<br/>

    flex的灵活在于，他可以随意设置哪些item 不自适应，哪些item 自适应，即哪些固定宽度，哪些参与剩余空间的分配。<br/>

    显然每个item情况不一样，所以在主轴占的大小，因此放大缩小是基于item来设置的衍生项：flex-grow, flex-shrink
    - flex-grow: <br/>
    -- 0 ,默认值，不参与分配，宽度为固定值或以内容撑开的值<br/>
    -- \>0, 分得几份剩余空间，比如为1时，就是将剩余空间等分后，拿一份。计算时会把所有item 的 flex-grow加起来得到n, 假设剩余空间为p,则这个设置为1的item分得的空间为(p/n)*1
    - flex-shrink: 1,默认值，默认空间不足时会缩小，这是因为flex布局默认不换行（nowrap）,所以这里需要设置为默认可以缩小<br/>
    -- 0，不缩小<br/>
    -- \>0, 与放大原理一样<br/>
3) 交叉轴上单独的对齐方式——align-item
当某个元素希望单独对齐时，可以针对这个item设置align-self,不设置时，他是默认继承父元素的align-item配置的.<br/>
值得注意的是，这个只针对item在交叉轴上的对齐。<br/>
在主轴上的单个对齐可以参考：缺点就是会导致元素内部的文本也会靠右对齐，需要的话在内部重置<br/>
```
    flex: 1;
    text-align: right;
```
4) 配置简写——flex:  none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ] 
    - 默认值：0 1 auto，与各单项默认值一致，只缩不放
    - flex:none  ——  0 0 auto，不缩放
    - flex:auto —— 1 1 auto，均正常缩放
    - flex: n >0 —— n 1 0%,
    - flex: 其他 —— 用的较少，用时再查阅手册吧，主要记住各子项的设置规则就好<br/>

    >flex:1与flex:auto最重要的是flex-basis，当flex:1,时，flex-basis：0%，所以item的主轴上的大小设置全部重置为0，所有item均不会纳入剩余空间的计算，此时剩余空间的大小即为整个视窗大小，就可以实现视窗空间等分。

### 总结：
1) 显然，flex有主轴和交叉轴的区分，是因为主轴上的表现更加灵活，所以需要根据实际情况与需求来设置主轴
2) flex-wrap,默认值为nowrap，即不换行,所以当主轴item体积超过视窗大小时，width和flex-basis都将失效,item会被自动缩小，除非设置了flex-shrink,若都设置了flex-shrink不缩小，则超出时会出现滚动条。<br/>
所以优先级 flex-shrink > flex-basis > width
