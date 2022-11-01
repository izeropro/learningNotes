**MyBatis如何实现一对多？**
1.嵌套结果，执行一个SQL语句(类似于mysql的连接查询)
​	使用嵌套结果resultMap方式：在resultMap 里面的 collection 节点配置一对多的类就可以完成
2.分步查询，执行两个select查询语句(类似于mysql的子查询)
​	使用嵌套Select语句方式：在resultMap 里面的 collection 节点配置，另外一个表的查询通过 select 节点配置

**Mybatis输入输出支持的类型？**
​	parameterType、resultType、resultMap

**Mybatis一级缓存二级缓存**

Mybatis底层原理
    https://dandelioncloud.cn/article/details/1481993814998056961/

PageHelper分页原理
    1. PageHelper.startPage(page, size) -> 封装成Page对象放到ThreadLocal<Page>类型的LOCAL_PAGE静态变量中  
    2. 执行查询（Dao方法-mapper接口）执行的时候通过拦截器，获取当前线程中的分页参数，然后拼接SQL语句实现分页查询  
    3. 查询结束后在 finally 语句中清除ThreadLocal中的查询参数  
   
    * PageHelper方法使用了静态的ThreadLocal参数，分页参数和线程是绑定的。内部流程是ThreadLocal中设置了分页参数（pageIndex，pageSize），之后在查询执行的时候，获取当线程中的分页参数，执行查询的时候通过拦截器在sql语句中添加分页参数，之后实现分页查询，查询结束后在 finally 语句中清除ThreadLocal中的查询参数   

