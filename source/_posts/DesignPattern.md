# Design Pattern

## Responsebility Chain

> 责任链模式

  Avoid coupling the sender of a request to its receiver by giving more than one object a chance to handle the request.Chain the receiving objects and pass the request along the chain until an object handles it.

### 1.优点

- 请求与处理分开，两者解耦，提高系统灵活性。

### 2.缺点

- 性能问题，每个请求都是链头到链尾。
- 调试不方便。

> 注意事项：链中节点数量需要控制，避免出现超长链的情况，一般做法是在Handler中设置一个最大节点数量，在setNext方法中判断是否已经超出其阈值，超出则不允许该链建立，避免无意识地破坏系统性能。

### 3.实现

- 类图

   ```mermaid
  classDiagram
  class Chain
   <<interface>> Chain
    Chain <|.. HandlerChain
    Chain : doHandle(Request request,Response response) void
    Chain : getCurrentLocation() int
    Chain : resetCurrentLocation() int 
    Chain : getChainSize() int
    Class HandlerChain
    HandlerChain : -List~Handler~ handlers
    HandlerChain : -int size
    HandlerChain : -int currentLocation
    class Handler
    <<interface>> Handler
    Handler : doHandler(Request request,Response response,Chain chain) void
    Handler <|.. IgnorableHandler
    Handler <|.. InterruptHandler
    class IgnorableHandler
    <<abstract>> IgnorableHandler
    IgnorableHandler : ignoreHandler(Request request)* boolean
    IgnorableHandler : doIgnoreHandler(Request request, Response response)* void
    class InterruptHandler
    <<abstract>> InterruptHandler
    InterruptHandler : interrupt(Request request)* boolean
    InterruptHandler : doInterrupting(Request request,Response response)* void
    class AbstractStarterHandler
    <<abstract>> AbstractStarterHandler
    AbstractStarterHandler : startHandler(Request request) Response
    AbstractStarterHandler : initHandler()* List~Handler~
  ```

  - Chain

     定义链接口和方法

     ```java
     public interface HandleChain {
     
         void doHandle(ValidRequest request, ValidResponse response);
     
         int getCurrentLocation();
     
         void resetCurrentLocation();
     
         int getChainSize();
     }
    ```

     依据业务实现链接口

     ```java
    public class ValidateHandleChain implements HandleChain {
    
        private List<Handler> handlers;
        
        private final int size;
        
        private int currentLocation = 0;
        
        public ValidateHandleChain(List<Handler> handlers) {
            this.handlers = handlers;
            this.size = handlers.size();
        }
        
        //处理当前Handler,并将当前位置标识往后移动
        @Override
        public void doHandle(ValidRequest request,ValidResponse response) {
            if (currentLocation == size) {
                resetCurrentLocation();
                return;
            }
            currentLocation++;
            Handler nextHandler = handlers.get(currentLocation - 1);
            nextHandler.doHandler(request,response,this);
        }
        
        //获取当前位置信息
        @Override
        public int getCurrentLocation() {
            return this.currentLocation;
        }
        
        //重置当前位置信息
        @Override
        public void resetCurrentLocation() {
            this.currentLocation = 0;
        }
        
        //过去当前链长度
        @Override
        public int getChainSize() {
            return this.size;
        }
    
        }
     ```

- Handler

     Handler接口定义

     ```java
     public interface Handler {
     
         void doHandler(ValidRequest request, ValidResponse response,HandleChain chain);
       
     }
     ```

  - interrupt

       定义可中断的Handler。

     ```java
    public abstract class InterruptHandler implements Handler {
       
        public abstract boolean interrupt(ValidRequest request);
       
        @Override
        public final void doHandler(ValidRequest request, ValidResponse response, HandleChain chain) {
            if (interrupt(request)) {
                doInterrupting(request, response);
                return;
            }
            chain.doHandle(request, response);
        }
       
        public abstract void doInterrupting(ValidRequest request, ValidResponse response);
    }
    ```

       > doHandler方法final修饰，子类不可重写，interrupt()方法用于判断当前节点是否满足中断条件,doInterrupting()处理中断时的逻辑。

       ​  缺乏数据不可继续的校验Handler

     ```java
       public class UserIdValidateHandler extends InterruptHandler{

           @Override
           public boolean interrupt(ValidRequest request) {
               return Objects.isNull(request.getUserId());
           }
       
           @Override
           public void doInterrupting(ValidRequest request, ValidResponse response) {
               response.setPassable(false);
               List<String> overMessage = response.getOverMessage();
               overMessage.add("用户id为空");
               response.setOverMessage(overMessage);
           }
       }
     ```

  - ignorable

       定义可跳过的Handler

     ```java
    public abstract class IgnorableHandler implements Handler {
       
        public abstract boolean ignoreHandler(ValidRequest request);
       
        @Override
        public final void doHandler(ValidRequest request, ValidResponse response, HandleChain chain) {
            if (ignoreHandler(request)) {
                log.info("跳过校验,第「{}」条",chain.getCurrentLocation());
            } else {
                doIgnoreHandler(request, response);
            }
            chain.doHandle(request,response);
        }
       
        public abstract void doIgnoreHandler(ValidRequest request, ValidResponse response);
    }
    ```

     > doHandler()方法final修饰，子类不可重写，ignoreHandler()判断是否可以跳过，doIgnoreHandler()不可跳过时需要处理的逻辑

     ​  有前置条件的校验Handler

     ```java
     public class CarTypeControlHandler extends IgnorableHandler {
     
         @Override
         public boolean ignoreHandler(ValidRequest request) {
             return Objects.equals(request.getCarTypeControl(), CarTypeEnum.NOLIMIT.getCode().toString());//无限制则跳过
         }
     
     
         @Override
         public void doIgnoreHandler(ValidRequest request, ValidResponse response) {
             String allowedCarType = request.getAllowedCarType();
             String[] split = carTypeControl.split(",");
             Integer useCarType = request.getUseCarType();
             if (!Objects.equals(allowedCarType,useCarType)) {
                 response.setPassable(false);
                 List<String> overMessage = response.getOverMessage();
                 overMessage.add("车型管控不符合规则");
                 response.setOverMessage(overMessage);
             }
         }
     }
     ```

  - normal

     > 该类型Handler直接实现Handler接口，此处用作最后一个节点，做最终的校验。

- Starter

    责任链启动器.

     ```java
     public abstract class AbstractStarterHandler {
     
       //启动责任链
         public final ValidResponse startHandler(ValidRequest request) {
             ValidResponse response = new ValidResponse();
             List<Handler> handlers = initHandler();
             HandleChain handleChain = new ValidateHandleChain(handlers);
             try {
                 handleChain.doHandle(request, response);
             } finally {
                 handleChain.resetCurrentLocation();
             }
             return response;
         }
       //初始化
         public abstract List<Handler> initHandler();
     }
     ```

     > 子类实现抽象方法initHandler()实现责任链初始化。子类实例调用startHandler()启动责任链。

## Strategy

> 策略模式

Define a family of algorithms,encapsulate each one,and make them interchangeable.

### 1.Strategy优点

- 算法可以自由切换
- 避免使用多重条件判断
- 扩展性良好

### 2.Strategy缺点

- 策略类数量增多
- 所有策略类都需要对外暴露

### 3.Strategy应用场景

- 多个类只有在算法或行为上稍有不同的场景。
- 算法需要自由切换的场景。
- 需要屏蔽算法规则的场景。

> 注意事项：系统中策略家族的具体策略数量超过四个，则需要考虑使用混合模式，解决策略类膨胀和对外暴露的问题，否则后续维护会很困难。

### 实现

### 参考资料

- 《设计模式之禅》
