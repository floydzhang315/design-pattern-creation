## 复杂对象的组装与创建——建造者模式（二）  

**3 完整解决方案**  

Sunny 公司开发人员决定使用建造者模式来实现游戏角色的创建，其基本结构如图所示：  

![游戏角色创建结构图](images/1333541212_6038.gif) 

在图中，ActorController 充当指挥者，ActorBuilder 充当抽象建造者，HeroBuilder、AngelBuilder 和 DevilBuilder 充当具体建造者，Actor 充当复杂产品。完整代码如下所示：

```

//Actor角色类：复杂产品，考虑到代码的可读性，只列出部分成员属性，且成员属性的类型均为String，真实情况下，有些成员属性的类型需自定义
class Actor
{
       private  String type; //角色类型
       private  String sex; //性别
       private  String face; //脸型
       private  String costume; //服装
       private  String hairstyle; //发型
      
       public  void setType(String type) {
              this.type  = type;
       }
       public  void setSex(String sex) {
              this.sex  = sex;
       }
       public  void setFace(String face) {
              this.face  = face;
       }
       public  void setCostume(String costume) {
              this.costume  = costume;
       }
       public  void setHairstyle(String hairstyle) {
              this.hairstyle  = hairstyle;
       }
       public  String getType() {
              return  (this.type);
       }
       public  String getSex() {
              return  (this.sex);
       }
       public  String getFace() {
              return  (this.face);
       }
       public  String getCostume() {
              return  (this.costume);
       }
       public  String getHairstyle() {
              return  (this.hairstyle);
       }
}
 
//角色建造器：抽象建造者
abstract class ActorBuilder
{
       protected  Actor actor = new Actor();
      
       public  abstract void buildType();
       public  abstract void buildSex();
       public  abstract void buildFace();
       public  abstract void buildCostume();
       public  abstract void buildHairstyle();
 
    //工厂方法，返回一个完整的游戏角色对象
       public Actor createActor()
       {
              return actor;
       }
}
 
//英雄角色建造器：具体建造者
class HeroBuilder extends ActorBuilder
{
       public  void buildType()
       {
              actor.setType("英雄");
       }
       public  void buildSex()
       {
              actor.setSex("男");
       }
       public  void buildFace()
       {
              actor.setFace("英俊");
       }
       public  void buildCostume()
       {
              actor.setCostume("盔甲");
       }
       public  void buildHairstyle()
       {
              actor.setHairstyle("飘逸");
       }    
}
 
//天使角色建造器：具体建造者
class AngelBuilder extends ActorBuilder
{
       public  void buildType()
       {
              actor.setType("天使");
       }
       public  void buildSex()
       {
              actor.setSex("女");
       }
       public  void buildFace()
       {
              actor.setFace("漂亮");
       }
       public  void buildCostume()
       {
              actor.setCostume("白裙");
       }
       public  void buildHairstyle()
       {
              actor.setHairstyle("披肩长发");
       }    
}
 
//恶魔角色建造器：具体建造者
class DevilBuilder extends ActorBuilder
{
       public  void buildType()
       {
              actor.setType("恶魔");
       }
       public  void buildSex()
       {
              actor.setSex("妖");
       }
       public  void buildFace()
       {
              actor.setFace("丑陋");
       }
       public  void buildCostume()
       {
              actor.setCostume("黑衣");
       }
       public  void buildHairstyle()
       {
              actor.setHairstyle("光头");
       }    
}
```

指挥者类 ActorController 定义了 construct() 方法，该方法拥有一个抽象建造者 ActorBuilder 类型的参数，在该方法内部实现了游戏角色对象的逐步构建，代码如下所示：  

```
/游戏角色创建控制器：指挥者
class ActorController
{
    //逐步构建复杂产品对象
       public Actor construct(ActorBuilder ab)
       {
              Actor actor;
              ab.buildType();
              ab.buildSex();
              ab.buildFace();
              ab.buildCostume();
              ab.buildHairstyle();
              actor=ab.createActor();
              return actor;
       }
}
```

为了提高系统的灵活性和可扩展性，我们将具体建造者类的类名存储在配置文件中，并通过工具类 XMLUtil 来读取配置文件并反射生成对象，XMLUtil 类的代码如下所示：  

```
import javax.xml.parsers.*;
import org.w3c.dom.*;
import org.xml.sax.SAXException;
import java.io.*;
class XMLUtil
{
//该方法用于从XML配置文件中提取具体类类名，并返回一个实例对象
       public  static Object getBean()
       {
              try
              {
                     //创建文档对象
                     DocumentBuilderFactory  dFactory = DocumentBuilderFactory.newInstance();
                     DocumentBuilder  builder = dFactory.newDocumentBuilder();
                     Document  doc;                                                
                     doc  = builder.parse(new File("config.xml"));
             
                     //获取包含类名的文本节点
                     NodeList  nl = doc.getElementsByTagName("className");
            Node  classNode=nl.item(0).getFirstChild();
            String  cName=classNode.getNodeValue();
           
            //通过类名生成实例对象并将其返回
            Class c=Class.forName(cName);
                 Object obj=c.newInstance();
            return obj;
         }  
         catch(Exception e)
         {
              e.printStackTrace();
              return null;
          }
       }
}
```

配置文件 config.xml 中存储了具体建造者类的类名，代码如下所示：  

```
<?xml version="1.0"?>
<config>
       <className>AngelBuilder</className>
</config>   
```

编写如下客户端测试代码：

```
class Client
{
       public  static void main(String args[])
       {
              ActorBuilder ab; //针对抽象建造者编程
              ab =  (ActorBuilder)XMLUtil.getBean(); //反射生成具体建造者对象
 
         ActorController ac = new  ActorController();
              Actor actor;
              actor = ac.construct(ab); //通过指挥者创建完整的建造者对象
 
              String  type = actor.getType();
              System.out.println(type  + "的外观：");
              System.out.println("性别：" + actor.getSex());
              System.out.println("面容：" + actor.getFace());
              System.out.println("服装：" + actor.getCostume());
              System.out.println("发型：" + actor.getHairstyle());
       }
}
```

编译并运行程序，输出结果如下：

```
天使的外观：
性别：女
面容：漂亮
服装：白裙
发型：披肩长发
```

在建造者模式中，客户端只需实例化指挥者类，指挥者类针对抽象建造者编程，客户端根据需要传入具体的建造者类型，指挥者将指导具体建造者一步一步构造一个完整的产品（逐步调用具体建造者的 buildX() 方法），相同的构造过程可以创建完全不同的产品。在游戏角色实例中，如果需要更换角色，只需要修改配置文件，更换具体角色建造者类即可；如果需要增加新角色，可以增加一个新的具体角色建造者类作为抽象角色建造者的子类，再修改配置文件即可，原有代码无须修改，完全符合“开闭原则”。