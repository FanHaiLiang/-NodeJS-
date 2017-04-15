# -NodeJS-

# Node.js最大的特点就是异步I/O(或者非阻塞I/O)与事件紧密结合的编程模式.
# 什么是阻塞:
   线程在执行中如果遇到磁盘读写或网络通信(统称为I/O操作),通常要耗费较长的时间,
   这时操作系统会剥夺这个线程的CPU控制权,使其暂停执行,同时将资源让给其他的工作线程,
   这种线程调度方式成为阻塞.当I/O操作完毕时,操作系统将这个线程的阻塞状态解除,
   恢复其对CPU的控制权,令其继续执行.这种I/O模式就是通常的同步式I/o或阻塞式I/O

# 异步式I/O或非阻塞式I/O
  当线程遇到I/O操作时,不会以阻塞的方式等待I/O操作的完成或数据的返回,而只是将I/O请求发送给操作
  系统.继续执行下一条语句.当系统完成I/O操作时,以事件的形式通过执行I/O操作的线程,
  线程会在特定时候处理这个事件.为了处理异步I/O,线程必须有时间循环,不断的检查有没有未处理的事件,
  一次予以出路.

# 为什么Node.js使用了单线程,非阻塞的事件编程模式
  阻塞模式下,一个线程只能处理一项任务,要想提高吞吐量必须通过多线程.而非阻塞模式下,
  一个线程永远再执行计算操作,这个线程所使用的CUP核心利用率永远是100%,I/O以事件的方式通知.
  在阻塞模式下,多线程往往能提高系统吞吐量,因为一个线程阻塞时还有其他线程再工作,
  多线程可以让CPU资源不被阻塞中的线程浪费.而在非阻塞模式下,线程不会被I/O阻塞,永远再利用CPU.
  多线程带来的好处仅仅是在多核CPU的情况下利用更多的核,而Nodejs的单线程也能来带同样的好处.

# exports是模块公开的接口
# require用于从外部获取一个模块的接口
# npm是Node.js官方提供的包管理工具,它已经成了Node.js包的标准发布平台,用于Node.js包的发布,传播
  ,依赖控制.Npm提供了命令工具,使你可以方便地下载,安装,升级,删除包,也可以让你作为你开发者发布并维护包.

# 创建全局链接
  npm提供了一个有趣的命令npm link,它的功能是在本地包和全局包之间创建符号链接.
  使用全局安装模式安装的包不能直接通过require使用,但通过npm link命令可以打破这一限制.
  我们可以再node_modules子目录中发现一个指向安装到全局的包的符号链接.
  npm link express
  ./node_modules/express -> /usr/local/lib/node_modules/express
  通过这种方法我们就可以把全局包当本地包来使用了
# 命令调试
  node debug 文件名 将会启动调试工具进入调试状态(百度Node.js调试命令)
# 远程调试 使用以下两个语句之一即可打开调试服务器
  默认端口5858 --debug=1234 指定端口号为1234
  node --debug[=port] script.js
  node --debug-brk[=port] script.js
# 全局变量
### process是一个全局变量,global对象的属性.它用于描述当前No的.js进程状态的对象,提供了一个与操作系统的简单接口.
      process.argv是命令行参数数组,第一个元素是node,第二个元素是脚本文件名,从第三个元素开始每个元素是一个运行参数.
      ```js
      console.log(process.argv);
      ```
### process.stdou是标准输出流,通常我们使用conlose.log()向标准流输出打印字符,而process.stdout.write()函数提供了更底层的接口.
### process.stdin是标准输入流,初始时它被暂停的,要想从标准流输入读取数据,你必须恢复流,并手动编写流的事件响应函数.
      ```js
      process.stdin.resume()
      process.stdin.on('data',function(data){
         process.stdout.write('read from console:' + data.toString())
      });
      ```
### process.nextTick(callback)的功能是为事件循环设置一项任务,Node.js会在下次事件循环响应时调用callback.
      ```js
      function doSomething(args,callback){
         sometingComplicated(args);
         callback();
      }
      ```

