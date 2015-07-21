## 对象的克隆——原型模式（二）  

3 完整解决方案  

Sunny 公司开发人员决定使用原型模式来实现工作周报的快速创建，快速创建工作周报结构图如图7-3所示：

![快速创建工作周报结构图](images/1333464523_7039.gif)   

在图中，WeeklyLog 充当具体原型类，Object 类充当抽象原型类，clone() 方法为原型方法。WeeklyLog 类的代码如下所示：  

```
//工作周报WeeklyLog：具体原型类，考虑到代码的可读性和易理解性，只列出部分与模式相关的核心代码
class WeeklyLog implements Cloneable
{
       private  String name;
       private  String date;
       private  String content;
       public  void setName(String name) {
              this.name  = name;
       }
       public  void setDate(String date) {
              this.date  = date;
       }
       public  void setContent(String content) {
              this.content  = content;
       }
       public  String getName() {
              return  (this.name);
       }
       public  String getDate() {
              return  (this.date);
       }
       public  String getContent() {
              return  (this.content);
       }
     //克隆方法clone()，此处使用Java语言提供的克隆机制
       public WeeklyLog clone()
       {
              Object obj = null;
              try
              {
                     obj = super.clone();
                     return (WeeklyLog)obj;     
              }
              catch(CloneNotSupportedException e)
              {
                     System.out.println("不支持复制！");
                     return null;
              }
       }
}
```

编写如下客户端测试代码：

```
class Client
{
       public  static void main(String args[])
       {
              WeeklyLog log_previous = new WeeklyLog();  //创建原型对象
              log_previous.setName("张无忌");
              log_previous.setDate("第12周");
              log_previous.setContent("这周工作很忙，每天加班！");
             
              System.out.println("****周报****");
              System.out.println("周次：" +  log_previous.getDate());
              System.out.println("姓名：" +  log_previous.getName());
              System.out.println("内容：" +  log_previous.getContent());
              System.out.println("--------------------------------");
             
              WeeklyLog  log_new;
              log_new  = log_previous.clone(); //调用克隆方法创建克隆对象
              log_new.setDate("第13周");
              System.out.println("****周报****");
              System.out.println("周次：" + log_new.getDate());
              System.out.println("姓名：" + log_new.getName());
              System.out.println("内容：" + log_new.getContent());
       }
}
```

编译并运行程序，输出结果如下：  

```
****周报****
周次：第12周
姓名：张无忌
内容：这周工作很忙，每天加班！
--------------------------------
****周报****
周次：第13周
姓名：张无忌
内容：这周工作很忙，每天加班！
```

通过已创建的工作周报可以快速创建新的周报，然后再根据需要修改周报，无须再从头开始创建。原型模式为工作流系统中任务单的快速生成提供了一种解决方案。  

**思考**  
如果在 Client 类的 main() 函数中增加如下几条语句：  
System.out.println(log_previous == log_new);  
System.out.println(log_previous.getDate() == log_new.getDate());  
System.out.println(log_previous.getName() == log_new.getName());  
System.out.println(log_previous.getContent() == log_new.getContent());  
预测这些语句的输出结果。