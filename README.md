# Introduction 
**基于 Netty 进行高性能网络通信，基于 Spring 进行对象管理以及动态代理的生成，基于
Zookeeper 实现服务器节点创建与监听，最终实现高性能、轻量级的分布式 RPC 框架。**


# Features
- **支持长连接**
- **支持异步调用**
- **支持心跳检测**
- **支持JSON序列化**
- **接近零配置，基于注解实现调用**
- **基于Zookeeper实现服务注册中心**
- **支持客户端连接动态管理**
- **支持客户端服务监听、发现功能**
- **支持服务端服务注册功能**
- **基于Netty4.X版本实现**

# Quick Start
### 服务端开发
- **在服务端的Service下添加你自己的Service,并加上@Service注解**
	<pre>
	@Service
	public class TestService {
		public void test(User user){
			System.out.println("调用了TestService.test");
		}
	}
	</pre>

- **生成1个服务接口并生成1个实现该接口的类**
	###### 接口如下
	<pre>
	public interface TestRemote {
		public Response testUser(User user);  
	}
	</pre>
	###### 实现类如下，为你的实现类添加@Remote注解，该类是你真正调用服务的地方，你可以生成自己想返回给客户端的任何形式的Response

	<pre> 
	@Remote
	public class TestRemoteImpl implements TestRemote{
		@Resource
		private TestService service;
		public Response testUser(User user){
			service.test(user);
			Response response = ResponseUtil.createSuccessResponse(user);
			return response;
		}
	}	
	</pre>


### 客户端开发
- **在客户端生成一个接口，该接口为你要调用的接口**
	<pre>
	public interface TestRemote {
		public Response testUser(User user);
	}
	</pre>

### 使用
- **在你要调用的地方生成接口形式的属性，为该属性添加@RemoteInvoke注解**
	<pre>
	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration(classes=RemoteInvokeTest.class)
	@ComponentScan("\\")
	public class RemoteInvokeTest {
		@RemoteInvoke
		public static TestRemote userremote;
		public static User user;
		@Test
		public void testSaveUser(){
			User user = new User();
			user.setId(1000);
			user.setName("张三");
			userremote.testUser(user);
		}
	}	
	</pre>
# Overview

![Markdown](https://s1.ax1x.com/2018/07/06/PZK3SP.png)

### 结果
- **一万次调用：5s**
![1w](https://github.com/user-attachments/assets/4c2e8465-fc42-4888-949b-6d8a09367192)

- **十万次调用：27s**
![10w](https://github.com/user-attachments/assets/b073c7b0-6590-443c-94ef-1421af962944)

- **一百万次调用：173s**
![100w](https://github.com/user-attachments/assets/2f064731-4b9c-4845-93bd-455ca1ea121d)



