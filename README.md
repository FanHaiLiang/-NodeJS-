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
         process.nextTick(callback);[^1]:不会等待上第一个函数执行完毕在执行回调函数,而是立即执行
       }

      doSomething(function onEnd(){
         compute();
      })
      ```
# 常用工具 util util是一个Node.js核心模块,提供常用函数的集合,用于弥补核心JavaScript的功能过于精简的不足
### util.inherits(constructor,superConstructor)是一个实现对象间原型继承的函数.
### util.inspect(obj,[showHidden],[depth],[colors])是一个将任意对象转换为字符串的方法,通常用于调试和错误输出.
* showHidden 是一个 可选参数,如果值为true,将会输出更多隐藏信息.
* depth 表示最大递归的层数,如果对象很复杂,你可以指定层数以控制输出信息的多少。如果不指定 depth ,默认会递归2层,指定为 null 表示将不限递归层数完整遍历对象
* 如果color值为true,输出格式将会以ANSI颜色编码,通常用于在终端显示更漂亮的效果.
# events Node.js本身架构就是事件式的,而他提供了唯一的接口,所以称Node.js事件编程的基石.events模块不仅用于用户代码与Node.js下层事件循环的交互,还几乎被所有的模块依赖.
### 事件驱动 events模块只提供了一个对象:events.EventEmitter.EventEmitter的核心就是事件发射与事件监听器功能的封装.
##### EventEmitter常用的API
* EventEmitter.on(event,listrner)为指定事件注册一个监听,接受一个字符串event和一个回调函数listener.
* EventEmitter.emit(event,[arg1],[arg2],[...])发射event事件,传递若干可选参数到事件监听器的参数表.
* EventEmitter.once(event,listener)为指定事件注册一个单次监听器,即监听器最多会触发一次,触发后立刻解除该接听器.
* EventEmitter.removeListener(event, listener) 移除指定事件的某个监听器, listener 必须是该事件已经注册过的监听器.
* EventEmitter.removeAllListeners([event]) 移除所有事件的所有监听器,如果指定 event ,则移除指定事件的所有监听器
# error事件
* EventEmitter定义了一个特殊的事件error,它包含了"错误"的语义,我们再遇到异常的时候通常会发射error事件.当error被发射时,Eventmitter规定如果没有响应的监听器,Node.js会把它当作异常,退出程序并打印调用栈.我们一般要为会发射error事件的对象设置监听器,避免遇到错误后整个程序崩溃.
# 文件系统fs
* fs 模块是文件操作的封装,它提供了文件的读取,写入,更名,删除,遍历目录,连接POSIX文件系统操作.
* fs.readFile(filename,[encoding],[callback(err,data)])是最简单的读取文件的函数. 没有返回值
* fs.readFileSync(filename,[encoding])是fs.readFile同步的版本 有返回值
* fs.open(path,flags,[mode],[callback(err,fd)]),path:文件路径 flags:打开方式 mode 用于创建文件时指定权限 fd回调函数传递一个文件爱呢描述符.
* fs.read(fd,buffer,offset,lengt,position,[callback(err,bytesRead,buffer)])
   * fs.read的功能是从指定的文件描述父fd中读取数据并写入buffer指向的缓冲区对象.
   * offser是buffer的写入偏移量.
   * length是要从文件中读取的字节数.
   * position是文件读取的起始万位置,如果position的值为null,则会从当前文件指针的位置读取.
   * 回调函数传递bytesRead和buffer,分别表示读取的字节数和缓冲区对象.

* fs 模块函数表
* 功能                    异步函数                                                                              同步函数
* 打开文件                 fs.open(path,flags, [mode], [callback(err,fd)])                             fs.openSync(path,flags, [mode])
* 关闭文件                 fs.close(fd, [callback(err)])                                                      fs.closeSync(fd)
* 读取文件(文件描述符)       fs.read(fd,buffer,offset,length,position,[callback(err, bytesRead, buffer)])        fs.readSync(fd, buffer, offset,length, position)
* 写入文件(文件描述符)       fs.write(fd,buffer,offset,length,position,[callback(err, bytesWritten, buffer)])    fs.writeSync(fd, buffer, offset,length, position)
* 读取文件内容              fs.readFile(filename,[encoding],[callback(err, data)])                              fs.readFileSync(filename,[encoding]
* 写入文件内容              fs.writeFile(filename,[callback(err)])                                              fs.writeFileSync(filename, data,[encoding])
* 删除文件                 fs.unlink(path,[callback[err]])                                                     fs.inlinkSunc(path)
* 创建目录 fs.mkdir(path, [mode], [callback(err)]) fs.mkdirSync(path, [mode])
* 删除目录 fs.rmdir(path, [callback(err)]) fs.rmdirSync(path)
* 读取目录 fs.readdir(path, [callback(err, files)]) fs.readdirSync(path)
* 获取真实路径              fs.realpath(path, [callback(err,resolvedPath)])                                     fs.realpathSync(path)
* 更名                    fs.rename(path1, path2, [callback(err)])                                            fs.renameSync(path1, path2)
* 截断                    fs.truncate(fd, len, [callback(err)])                                               fs.truncateSync(fd, len)
* 更改所有权               fs.chown(path, uid, gid, [callback(err)])                                           fs.chownSync(path, uid, gid)
* 更改所有权(文件描述符)     fs.fchown(fd, uid, gid, [callback(err)])                                             fs.fchownSync(fd, uid, gid)
* 更改所有权(不解析符号链接)  fs.lchown(path, uid, gid, [callback(err)])                                          fs.lchownSync(path, uid, gid)
* 更改权限                 fs.chmod(path, mode, [callback(err)])                                               fs.chmodSync(path, mode)
* 更改权限(文件描述符)      fs.fchmod(fd, mode, [callback(err)])                                                fs.fchmodSync(fd, mode)
* 更改权限(不解析符号链接)   fs.lchmod(path, mode, [callback(err)])                                              fs.lchmodSync(path, mode)
* 获取文件信息              fs.stat(path, [callback(err, stats)])                                               fs.statSync(path)
* 获取文件信息(文件描述符)    fs.fstat(fd, [callback(err, stats)])                                                fs.fstatSync(fd)
* 获取文件信息(不解析符号链接) fs.lstat(path, [callback(err, stats)])                                              fs.lstatSync(path)
* 创建硬链接                fs.link(srcpath, dstpath, [callback(err)])                                           fs.linkSync(srcpath, dstpath)
* 创建符号链接              fs.symlink(linkdata, path, [type],[callback(err)])                                  fs.symlinkSync(linkdata, path,[type])
* 读取链接                  fs.readlink(path, [callback(err,linkString)])                                       fs.readlinkSync(path)
* 修改文件时间戳             fs.utimes(path, atime, mtime, [callback(err)])                                      fs.utimesSync(path, atime, mtime)
* 修改 文件时间 戳(文件描述符) fs.futimes(fd, atime, mtime, [callback(err)])                                       fs.futimesSync(fd, atime, mtime)
# HTTP服务器与客户端
  * Node.js标准库提供了http模块，其中封装了一个高效的HTTP服务器和一个简易的HTTP客户端．
# HTTP服务器
   * http.server事件
      * http.server是一个基于事件的HTTP服务器，所有的请求都被封装为独立的事件，开发者只需要对它的事件编写响应函数即可实现HTTP服务器的所有功能．
      * 继承自EventEmitter,提供以下几个事件．
         * request:当客户端请求到来时，该事件被触发，提供两个参数req和res，分贝是http.ServerRequest和http.ServerResponse的实例，表示响应和请求信息．
         * connection:当TCP连接建立时，该事件被触发，提供一个参数socket,为net.Socket的实例．connection事件的粒度大于request.
         * close：当服务器关闭时，该事件辈出发．注意不是再用户连接断开时．
