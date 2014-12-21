---
layout: post
title: "一些关于Java异常处理的概念"
date: 2014-12-18 00:38:45 +0800
comments: true
categories: 
---
### 一： 基本概念
**“Java中的基本概念是：结构不佳的代码不能运行.” **—— 《Java编程思想》

“异常”这个词有“对此感到意外”的意思，异常处理需要处理的部分也正是没有按照预期想法运行的代码。

但是出现错误的地方不一定知道如何处理错误，当前环境中也许没有足够的信息来解决这个“意外”，于是将错误提交到高级别的环境中，从而做出正确的决定。这就是抛出异常。

我们常用 try..catch 块来抛出和处理异常

    try{
		// Code that might generate exceptions
	}catch {
	   // Handle exceptions
	}
根本目的是为了将完成任务的代码和错误检查的代码相互分离开，同时可以处理异常。

说道异常处理，理论上有两种基本模型，**终止模型&恢复模型。**

Java支持**终止模型**，这种模型中，当错误发生的时，假设错误十分关键，以至程序无法返回到异常发生的地方继续执行，一旦异常被抛出，就表明错误无法挽回，不能回来继续执行代码。

**恢复模型**则认为异常处理程序的工作是修正错误，然后重新尝试try块的代码，并认为下一次能成功。如果希望java实现类似恢复的行为，那么再遇见错误的时候不能抛出异常，而是调用方法修正错误，或将try放在while循环中不断执行，直到结果满意。

但是恢复模型不实用，因为恢复模性的处理程序需要知道异常抛出的地点，对于异常会从很多地方抛出的大型程序而言，很难实现和维护。

那么有办法改良从而使恢复模型变得实用吗？

当然有，把try放进while循环里时设置一个static类型的计数器，使循环在放弃以前能尝试一定的次数，这将使程序的健壮性提高很多。

我们经常在异常处理代码看到下面这个东东：

	catch(Exception e) {
		e.printStackTrace();
	}
printStackTrace() 方法将打印“从方法调用处直到异常抛出处”的方法调用序列，也就是栈轨迹。从这里可以看到异常是如何被一层层抛出，都经过了哪些方法。

以上异常都是在try..catch抛出，称为“被检查的异常”，编译时期强制检查。除此之外，还有一种“不受检查的异常”，在代码运行时自动检查，称为RuntimeException。

比如NullPointerException，如果在java代码中对每一个对象都检查是否为空，开销未免太大，Java虚拟机便帮我们做了这样一个检查，运行时发现为空的对象时，由Java虚拟机自动抛出异常。

那么问题来了，如果只抛出没有人捕获这个异常的话会发生什么？

答案是：如果RuntimeException没有被捕获而直达main()，那么在程序退出前将调用异常的printStackTrace()方法讲堆栈信息打印出来。

异常处理的最后，finally处理那些不管发生什么都一定要被执行的代码。

### 二：编写简单的自定义异常
在我们的代码中，如何使用异常呢。

首先需要了解，Exception是java中所有异常类的基类，继承自Throwable。

如果我们需要抛出的异常类能够承载一些信息，那么就需要自定义异常类，并在恰当的时候抛出，而非每次都抛出Exception类。

如果可以用自定义异常解决问题，那么Exception类还有用吗？
答案是非常有用，看下面的例子。

	public void dataAccessCode(){
    	Connection conn = null;
	    try{
    	    conn = getConnection();
	    }catch(SQLException ex){
    	    ex.printStacktrace();
    	    // Handle the exception
    	}catch(Exception e) {
    		e.printStacktrace();
    	} finally{
        	DBUtil.closeConnection(conn);
    	}
    }
假设try中的代码抛出很多异常，异常处理时首先catch住SQLException，就会优先处理这个异常。

异常处理过程跟switch-case很相似，不同的是，每当catch住一个异常后就会执行该处的异常处理代码，之后异常处理过程结束。而switch-case中需要在每一个case后添加break语句，防止遍历case语句。

每个异常处理块只会在抛出的异常跟catch中的异常匹配时执行，如果catch中的所有异常都不匹配，而代码又确实抛出了异常，此时catch到Exception的代码就可以工作了。

所以通常在异常处理的最后加上对Exception的catch，防止漏掉某些异常。

那么如何编写自定义异常类呢，下面的例子是最常见的一种做法。

	public class AccountNotFoundException extends Exception {
		public AccountNotFoundException() {
			super();
		}
		public AccountNotFoundException(String message) {
			super(message);
		}
	}
为自定义异常类添加构造方法，抛出异常类时候添加message，使异常承载更多的信息。

	throw new AccountNotFoundException("User");
	// In different place
	throw new AccountNotFoundException("Customer");
上面这两个异常被抛出，在异常的catch可以清楚的知道是哪种Account找不到。

如果为异常类添加私有变量和getter方法，如下：

	public class AccountNotFoundException extends Exception {
		private String message;
		public AccountNotFoundException() {
			super();
		}
		public AccountNotFoundException(String message) {
			super(message);
		}
		public String getMessage(){
			return this.message;
		}
	}
那么对于不同地方抛出的异常，catch部分就可以根据不同的message而分别处理，如下：

	try{
		//throw AccountNotFoundException
	} catch(AccountNotFoundException e) {
		if(e.getMessage.equals("User"))
			// do something for User
		else if(e.getMessage.equals("Customer"))
			// do something for Customer
		else
			// do something normal
	}
不管是自定义异常类，还是处理异常的代码，在写代码的时候think twice，可以帮我们解决很多代码中的问题，并优化代码结构。

对于异常处理，多了解一点，受益良多。

