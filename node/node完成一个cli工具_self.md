



![image-20201215231619302](../images/image-20201215231619302.png)

### 为什么前端要学习node

如何实现像nextjs那样的约定路由功能，

vue里面可以实现对路由自动配置



1. 约定一个解释器，使用node解释器来运行，下面可以直接写node代码

```js
#!/usr/bin/env node
```

2. package.json  中bin添加一个值，

```
"bin": {kkb: "./bin/kkb.js"}
```

然后在该项目中执行npm link(相当于在全局安装了这个包)，

然后命令行中执行kkb，会直接直接该项目的./bin/kkb.js文件。

实际上这时候我们就实现了一个最简单的cli工具，命令行输入kkb，执行对应的文件。

```
补充：package.json 中的 bin 字段作用
bin 字段是命令名到本地文件名的映射
```

3. 需要用到的包：commander、download-git-repo ora handlebars figlet clear chalk open watch 。

   1. **node.js 8中util.promisify**：虽然promise已经普及，但是Node.js里仍有大量的依赖回调的异步函数，如果我们每个函数都封装一个Promise，也是很麻烦的。所有Node8就提供了util.promisify()这个方法，方便我们快捷的把原来的异步回调方法改成返回Promise实例的方法。
   2. **figlet : ** 酷炫的文字工具
   3. **clear : ** 等同于在命令行界面运行clear，清屏
   4. **chalk : ** 粉笔， 改变文字的颜色
   5. **download-git-repo : ** 直接从git上下载东西
   6. **ora :** 转啊转
   7. **open** : 自动打开浏览器
   8. **handlebars** : 

   ```js
   // 结合 Await/Async 使用
   const {promisify} = require('util');
   const {fs} = require('fs')
   const stat = promisify(fs.stat)
   async function readStats() {
     try {
       let stats = await stat(dir);
     } catch (err) {
       console.log(err);
     }
   }
   ```

   

4. 定制命令行界面

   1. 需要借助 commander：

      ```js
      #!/usr/bin/env node
      const program = require("commander");
      // 通过-v 查看版本号，一般我们用package.json中自带的版本号
      program.version(require('../package.json').version);
      // 给cli添加init命令
      program
        .command('init <name>') // name是指参数的意思,意指命令行输入的参数
      	.description('init project')
      	.action(name => {
        	console.log('init' + name);
      	})
      or: 
      	.action(require(...)) // 引入的文件里面有export name => {},也就是吧action里面的内容放到一个方法里面。
      program.parse(process.argv);
      
      // 在命令行里运行的命令就是 command('init <name>')这个，name是一个你传入的参数，eg：$ node ./kkg.js init test
      ```

      

5. 克隆一个基础库，一般项目位于github或者gitlab上。

6. 项目克隆好了之后，实现自动安装项目相关依赖。

   1. 需要将子进程的输出流和主进程输出流对接。

   ```js
   // spawn方式是原生就有的，但是此处我们需要改写一下，将子进程的输出流插入到主进程的输出流中，这样子进程的打印才能在控制面板中看到
   const spawn = async (...args) => {
     const { spawn } = require('child_process')
     return new Promise(resolve => {
       const proc = spawn(...args);
       // 插水管
       proc.stdout.pipe(process.stdout) // process描述我们当前所启动的进程
       proc.stderr.pipe(process.stderr)
       proc.on('close', () => {
         resolve();
       })
     })
   }
   
   // 启动项目 克隆的项目执行命令是 npm run serve  cwd： 进入的目录，从你当前文件进入到项目的路径
   await spawn('npm', ['run', 'serve'], {cwd: `./${name}`}) // spawn中的参数其实就是你在shell中输入的内容
   // bash  其实是shell的一种
   ```

7. 自动打开浏览器。

```js
const open = require('open');
open(浏览器url)
```

8. 实现约定路由功能

