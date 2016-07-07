**用ScheduledExecutorService取代Timer吧**

Timer有以下三个缺陷：

1. Timer执行任务时只创建一个线程, 如果schedule多个TimeTask任务，多个任务，一个任务处理完成后，才能处理下一个任务。当任务处理时间大于任务间隔时间，就算到达间隔时间，也要等待前一个处理完，才能处理下一个。
2. 任务异常时有缺陷。当抛出未检查异常如RunTimeException时，Timer会停止所有任务运行。
3. 取的绝对时间，系统时间改变，会对执行有影响。

看代码：
<pre>
public class TimerTest {
	public static void main(String []args) {
		Timer timer = new Timer();
		System.out.println(String.format("begin test at %s", System.currentTimeMillis()));
		timer.schedule(new Task1(1), 5000, 8000);
		/*单线程，要等到task(1)执行完，才会执行task(2),间隔时间缺陷*/
		timer.schedule(new Task1(2), 5000, 8000);  
	}
}

class Task1 extends TimerTask {
	int taskNo = 0;
	public Task1(int taskNo) {
		super();
		this.taskNo = taskNo;
	}
	@Override
	public void run() {
		System.out.println(String.format("task %d start at %s", taskNo, System.currentTimeMillis()));
		try {
			Thread.sleep(10000);
			if(taskNo == 2) {
				/*异常缺陷：Timer执行此后，抛出异常，进错结束
				 * ScheduledExecutorService不影响其它任务执行 */
				throw new RuntimeException();  
			}
		} catch (InterruptedException e) {
			// TO DO
		}
	}
}
</pre>

通过ScheduledExecutorService能避免这些缺陷，。代码如下：
<pre>
public class ScheduledThreadPoolTest {
	public static void main(String []args) {
		ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(2);
		System.out.println(String.format("begin test at %s", System.currentTimeMillis()));
		ScheduledFuture<?> sf = scheduledThreadPool.scheduleAtFixedRate(new Task1(1), 5000, 8000, TimeUnit.MILLISECONDS);
		scheduledThreadPool.scheduleAtFixedRate(new Task1(2), 5000, 8000, TimeUnit.MILLISECONDS);
		System.out.println(sf.getDelay(TimeUnit.MILLISECONDS));  // 还有多久被执行
		System.out.println(sf.isDone()); 
		sf.cancel(true);   // 取消任务
	}	
}
</pre>

ScheduledExecutorService： **Task实现Runnable接口即可，不用继承TimeTask， 且返回ScheduledFuture<?>**，可cancel任务，可获取任务还有多久执行等。
