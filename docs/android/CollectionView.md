[Home](../../README.md)

# Android

## CollectionView
CollectionView 指类似布局按一定规则顺序显示的布局组。

#### ListView
ListView 通过设置 BaseAdapter 可以很简便地显示一个列表。老旧控件，可以视为被废弃，用 RecyclerView 来替代。
其中 BaseAdapter 可以通过以下方法来优化：
1. 复用 contentView，省去 `ListView.getView()` 中 `LayoutInflater.inflate()` 操作。
2. 缓存 View，省去多余的 `View.findViewById()` 操作。可以使用 ViewHolder 来保持 View 的引用。
3. 根据具体情况进行分页加载。
4. 对列表中的图片进行缓存。
5. 在快速滑动时暂停图片的加载。
6. 避免重复设置 Listener 或设置过多 Listener。
7. 设置 `Adapter.hasStableIds()` 返回 `true`，避免在 `notifyDataSetChanged()` 时重新绘制过多布局。

#### GridView
类似 ListView，子控件通过网格的方式来排序。老旧控件，可以视为被废弃，用 RecyclerView 来替代。

#### FlexboxLayout
带有换行功能的 LinearLayout。

#### RecyclerView
RecyclerView 通过设置 RecyclerView.Adapter 后也可以显示一个列表。列表的类型、排序方式、高度等都可以灵活配置。
- **LayoutManager**
RecyclerView 通过设置不同的 LayoutManager 来决定子控件的排布方式。若未设置 LayoutManager，即使设置了 Adapter 也不会显示子控件。系统自带了三种 LayoutManager：LinearLayoutManager、GridLayoutManager、StaggeredGridLayoutManager 满足了大部分需求。
实现自定义的 LayoutManager 需要实现一个抽象方法：`generateDefaultLayoutParams()` 用于为每个子控件生成 LayoutParams。并且需要复写 `onLayoutChildren()` 方法来为每个子控件设置具体位置，相当于 `ViewGroup.onLayout()` 方法。
`onLayoutChildren()` 在以下情况会被调用，需要注意适配：
    1. 在 RecyclerView 初始化时，会被调用两次。
    2. 在调用 `RecyclerView.Adapter.notifyDataSetChanged()` 时，会被调用。
    3. 在调用 `RecyclerView.setAdapter()` 替换 Adapter 时, 会被调用。
    4. 在 RecyclerView 执行动画时，会被调用。

    千万不要在该方法中处理所有的子控件。而是根据具体策略处理部分子控件（可见或即将可见）。
    需要复写 `canScrollVertically()` 或 `canScrollHorizontally()` 才能实现滑动，并在 `scrollVerticallyBy()` 或者 `scrollHorizontallyBy()` 中进行滑动位置计算。
- **RecyclerView.Adapter**
继承 RecyclerView.Adapter 需要实现三个抽象方法：
`getItemCount()` 方法返回了列表（数据）的长度。
`onCreateViewHolder(ViewGroup parent, int viewType)` 方法用于创建一个 RecyclerView.ViewHolder，用于缓存不同的 contentView，并可以配合 `getItemViewType(int position)` 来获取不同的 viewType 生成不同的 ViewHolder。在 inflate 布局的时候需要传入 parent 对象，便于适应于 RecyclerView 的布局。
`onBindViewHolder(RecyclerHolder holder, int position)` 方法用于将数据显示（绑定）至 ViewHolder 中。
BaseRecyclerViewAdapterHelper（简称 BRVAH）是一个对 RecyclerView.Adapter 进行封装与扩展的库，实现了 Header、Footer、EmptyView、分组 Header、Expandable、上拉加载更多、下拉刷新等功能。
- **ItemDecoration**
继承 ItemDecoration 需要实现两个方法：
`getItemOffsets()` 方法返回了每个子控件的偏移量，`onDraw()` 方法用于绘制，并且可以实现 `onDrawOver()` 方法绘制覆盖于顶层的内容。
- **ItemAnimator**
可以通过继承 SimpleItemAnimator 来实现简单的自定义动画。

[Home](../../README.md)