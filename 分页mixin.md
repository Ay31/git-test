# 分页mixin

1. created
   1. initTableProps 初始化表格属性
      1. 首先是根据`tableprops.column.***`确定表格每一列的列宽，如果该列column属性中没有显示表明width，就在column对象中添加默认值宽度`column.minWidth`属性
      2. 读取`this.tableProps.operator`对象，如果没有明确宽度，手动添加`operatorProps`至`tableProps`对象中

2. 函数
   1. `handleFormSearch()`搜索
      
      1. 使用`requestData(this.getPaginationInfo())`请求数据，请求参数由`getPaginationInfo()`提供
         1. `getPaginationInfo`默认返回页数和条数
         2. `requestData` 首先获取分页参数（条数&页数），然后根据当前页面（因为是mixin）的`this.paginationInfo`的配置，获取请求方法`reqMethod`，再获取`list属性`中的`url, method, reqMethod: subReqMethod, reqName, onSuccess, additions`
         3. `requestData`还有一步是获取当前表单的查询条件，调用`getSearchFormModel`,首先`Object.key`遍历`this.searchForm.model`，然后将其中的空项格式化为undefined或null，最后返回该model至requestData函数
         4. `requestData` 将以上数据合并成一个data对象，包含model，分页info，请求addtion
         5. 如果存在`subReqMethod`，将reqMethod更换
         6. 存在reqMthod，则调用reqMethod并传入reqName和4中的data对象作为参数，如果没有提供reqMethod，则直接调用原生httpRequest，参数使用2中获取的url, method与4的data
         7. 请求成功后回调函数执行`loaded()`函数
            1. `loaded`，根据返回数据，解构出`records, current, size, total`
            2. 定义data，先判断当前组件有没有`this.mapDatas`（先处理数据），有则执行`mapDatas(recoreds)`如果没有data则直接等于解构出的`recoreds`
            3. this.tableProps.info = {
            pageIndex: current,
            pageSize: size,
             };
             this.tableProps.total = total; 初始化表格分页属性
         4. 执行onSuccess.call(this,res)

2. `handlePageChange`查询分页
   1. ​      this.requestData(this.tableProps.info);  传入当前页面分页配置
