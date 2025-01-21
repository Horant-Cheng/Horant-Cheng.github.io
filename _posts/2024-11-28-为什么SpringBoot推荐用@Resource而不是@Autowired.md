---
title: 为什么SpringBoot推荐用Resource而不是Autowired
date: 2024-11-28 14:30:45 +0800
categories: [SpringBoot, Annotations]
tags: [springboot]     # TAG names should always be lowercase
---

###  主要原因

Spring官方推荐的是构造器注入，而非字段注入（实例见下文）

@Autowired 是Spring框架提供的，故而转为其他IOC框架就不可用了，而@Resource是JSR-250提供的，属于java标准，故而若一定要使用字段注入，那就用@Resource（因为实在太方便了）



### 扩展：字段注入缺点

1. #### 空指针问题

   ```java
   @Autowired
   private MianshiyaService mianshiyaService;
   
   private String mianshiya;
   
   public Mianshiya() {
       this.mianshiya = mianshiyaService.getYa();
   }
   
   ```

   构造方法优先级＞@Autowired，故而会先构造，而此时还没有实例化，故而会报空指针。

2. #### 与IOC容器强耦合

   字段注入使其与Spring强绑定，例如写单元测试时，必须使用Bean注入，不然无法实现单测，导致以为是单测其实是集合测试。

   例如，对于下面代码：

   ```java
   @Service
   public class UserService {
   
       @Autowired
       private UserRepository userRepository;
   
       public User getUserById(Long id) {
           return userRepository.findById(id).orElse(null);
       }
   }
   ```

   此时不难看出UserService依赖 UserRepository 的注入

   编写测试用例：

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest
   public class UserServiceTest {
   
       @Autowired
       private UserService userService;
   
       @MockBean
       private UserRepository userRepository;
   
       @Test
       public void testGetUserById() {
           User mockUser = new User();
           mockUser.setId(1L);
           mockUser.setName("John Doe");
   
           when(userRepository.findById(1L)).thenReturn(Optional.of(mockUser));
   
           User result = userService.getUserById(1L);
           assertEquals("John Doe", result.getName());
       }
   }
   ```

   可以看到进行所谓单元测试时，还是需要依赖于Spring容器进行注入，故而不是单元测试。

   而需要正确实现单元测试则需要以构造器的方式：

   ```java
   @Service
   public class UserService {
   
       private final UserRepository userRepository;
   
       @Autowired
       public UserService(UserRepository userRepository) {
           this.userRepository = userRepository;
       }
   
       public User getUserById(Long id) {
           return userRepository.findById(id).orElse(null);
       }
   }
   ```

   可以看到此时并不依赖于UserRepository的注入

   故而单元测试：

   ```java
   public class UserServiceTest {
   
       private UserService userService;
       private UserRepository userRepository;
   
       @Before
       public void setUp() {
           userRepository = mock(UserRepository.class);
           userService = new UserService(userRepository);
       }
   
       @Test
       public void testGetUserById() {
           User mockUser = new User();
           mockUser.setId(1L);
           mockUser.setName("John Doe");
   
           when(userRepository.findById(1L)).thenReturn(Optional.of(mockUser));
   
           User result = userService.getUserById(1L);
           assertEquals("John Doe", result.getName());
       }
   }
   ```

   可以看出并不需要使用Spring容器。

3. #### 容易违反单一原则

   **单一原则：**单一职责原则指出，一个类应该只有一个引起它变化的原因，即一个类应该只负责一项职责。

   `@Autowired` 是 Spring 框架中用于自动注入依赖的注解。虽然它非常方便，但过度使用可能会导致违反单一职责原则（Single Responsibility Principle, SRP）。单一职责原则指出，一个类应该只有一个引起它变化的原因，即一个类应该只负责一项职责。

   ##### 举例解释

   假设我们有一个 `OrderService` 类，它负责处理订单相关的业务逻辑。最初，这个类可能只依赖于一个 `OrderRepository` 来访问数据库：

   ```java
   @Service
   public class OrderService {
   
       @Autowired
       private OrderRepository orderRepository;
   
       public void processOrder(Order order) {
           // 处理订单逻辑
           orderRepository.save(order);
       }
   }
   ```

   随着时间的推移，业务需求增加，`OrderService` 类可能会逐渐引入更多的依赖项，例如 `PaymentService`、`NotificationService`、`InventoryService` 等：

   ```java
   @Service
   public class OrderService {
   
       @Autowired
       private OrderRepository orderRepository;
   
       @Autowired
       private PaymentService paymentService;
   
       @Autowired
       private NotificationService notificationService;
   
       @Autowired
       private InventoryService inventoryService;
   
       public void processOrder(Order order) {
           // 处理订单逻辑
           paymentService.processPayment(order);
           orderRepository.save(order);
           notificationService.sendNotification(order);
           inventoryService.updateInventory(order);
       }
   }
   ```

   在这个例子中，`OrderService` 类不仅负责处理订单，还负责支付、通知和库存管理。这显然违反了单一职责原则，因为 `OrderService` 类承担了过多的职责。

   ##### 构造器注入的优势

   如果使用构造器注入，当依赖项增多时，构造器会变得臃肿，这会促使开发者反思类的职责是否过多：

   ```java
   @Service
   public class OrderService {
   
       private final OrderRepository orderRepository;
       private final PaymentService paymentService;
       private final NotificationService notificationService;
       private final InventoryService inventoryService;
   
       @Autowired
       public OrderService(OrderRepository orderRepository, PaymentService paymentService,
                           NotificationService notificationService, InventoryService inventoryService) {
           this.orderRepository = orderRepository;
           this.paymentService = paymentService;
           this.notificationService = notificationService;
           this.inventoryService = inventoryService;
       }
   
       public void processOrder(Order order) {
           // 处理订单逻辑
           paymentService.processPayment(order);
           orderRepository.save(order);
           notificationService.sendNotification(order);
           inventoryService.updateInventory(order);
       }
   }
   ```

   通过构造器注入，开发者可以更清楚地看到 `OrderService` 类的依赖项数量，从而更容易意识到类可能承担了过多的职责。这有助于促使开发者将类拆分为更小、更专注的类，每个类只负责一项职责。

   `@Resource` 使用字段注入也会造成和@Autowired一样的问题。