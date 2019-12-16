### diff

#### diff 前提策略

策略一：Web UI 中 DOM 节点跨层级的移动操作特别少，可以忽略不计。

策略二：拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。

策略三：对于同一层级的一组子节点，它们可以通过唯一 id 进行区分。

#### 1. tree diff

> 基于策略一，React 对树的算法进行了简洁明了的优化，即对树进行分层比较，两棵树只会对同一层次的节点进行比较

​        React 通过 updateDepth 对 Virtual DOM 树进行***层级控制***，只会对同一个父节点下的所有DOM子节点进行比较。当发现节点已经不存在，则该节点及其子节点会被完全删除掉，不会用于进一步的比较。这样只需要对树进行一次遍历，便能完成整个 DOM 树的比较。

* <font color=#0099fff >优化1：当出现节点跨层级移动时，并不会出现想象中的移动操作，而是以移动父节点为根节点的树被整个重新创建，这是一种影响 React 性能的操作，因此 *React 官方建议不要进行 DOM 节点跨层级的操作*。</font>
* <font color=#0099fff >优化2：在开发组件时，保持稳定的 DOM 结构会有助于性能的提升。例如，可以通过 CSS 隐藏或显示节点，而不是真的移除或添加 DOM 节点 </font>

#### 2. component diff

> 基于策略二，React 是基于组件构建应用的，对于组件间的比较所采取的策略也是简洁高效

​        如果是同一类型的组件，暂时认为此组件不需要被更新，按照原策略继续比较 virtual DOM tree。如果不是，则将该组件判断为 dirty component，从而替换整个组件下的所有子节点，即移除旧组件创建新组件并追加到页面。

* <font color=#0099fff >优化1：对于同一类型的组件，有可能其 Virtual DOM 没有任何变化，如果能够确切的知道这点那可以节省大量的 diff 运算时间，因此 React 允许用户通过 `shouldComponentUpdate()` 来判断该组件是否需要进行 diff</font> 

#### 3. element diff

> 基于策略三，React diff 提供了三种节点操作，**MOVE_EXISTING**（移动）、**INSERT_MARKUP**（插入）和 **REMOVE_NODE**（删除）

**MOVE_EXISTING**，在老集合有新 component 类型，且 element 是可更新的类型，generateComponentChildren 已调用 receiveComponent，这种情况下 prevChild=nextChild，就需要做移动操作，可以复用以前的 DOM 节点</br>

**INSERT_MARKUP**，新的 component 类型不在老集合里， 即是全新的节点，需要对新节点执行插入操作</br>

**REMOVE_NODE**，老 component 类型，在新集合里也有，但对应的 element 不同则不能直接复用和更新，需要执行删除操作，或者老 component 不在新集合里的，也需要执行删除操作</br>

* <font color=#0099fff >优化1：由于位置发生变化，导致需要进行繁杂低效的删除、创建操作，其实只要对这些节点进行位置移动即可，允许开发者对同一层级的同组子节点，添加唯一 `key` 进行区分</font>

