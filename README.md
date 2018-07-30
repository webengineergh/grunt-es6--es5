linux下使用grunt小结（包括es6语法转es5）
一.安装nodejs

建议安装node版本4.xx之上，最好安装偶数版本，因为后期如果要压缩html和js可能会对版本有要求。

nodejs下载地址：https://nodejs.org/dist/

1.下载自己所需版本的node的压缩包(此处以node-v4.4.6-linux-x64.tar.gz为例），到自己指定目录（以mysoftware为例）下解压

cd  /mysoftware(自己的指定目录);

tar xvf node-v4.4.6-linux-x64.tar.gz;

解压后进入到bin目录下确认一下是否存在npm和node文件，如果存在执行下面软连接，如果没有重新解压;

建立软连接，设为全局变量(node的路径可以使用pwd获得）

      ln -s /home/mysoftware/node-v4.4.6-linux-x64/bin/node /usr/local/bin/node

     ln -s /home/mysoftware/node-v4.4.6-linux-x64/bin/npm /usr/local/bin/npm

输入node -v 命令检查是否成功，出现正常版本号证明安装成功。

二.安装grunt(参考上篇博客)

1.管理员权限下输入命令: npm install -g grunt

2.安装成功后输入：grunt -version 检查版本看是否成功

三.使用grunt压缩使用es6语法的项目，需要先将es6转义再压缩，否则会报错，下面附上测试用例以及Gruntfile配置。

<1>Gruntfile文件配置如下：

module.exports = function(grunt) {
    //配置参数
    grunt.initConfig({
        // pkg: grunt.file.readJSON('package.json'),
        watch: {
            js: {
                files:['origin_js/**/*.js'],
                tasks:['default'],
                options: {livereload:false}
            },
            babel:{
                files:'origin_js/**/*.js',
                tasks:['babel']
            }
        },
 
 
        babel: {
            options: {
                sourceMap: false,
                presets: ['env']
 
            },
            dist: {
                files: [{
                    expand:true,
                    cwd:'origin_js/', //origin_js目录下
                    src:['**/*.js'], //所有js文件
                    dest:'babel/'  //输出到此目录下
                }]
            }
        },
        uglify: {
            options: {
                mangle: true, //混淆变量名
                comments: 'false' //false（删除全部注释），some（保留@preserve @license @cc_on等注释）
            },
            my_target: {
                files: [{
                    expand:true,
                    cwd:'babel/', //js目录下
                    src:['**/*.js'], //所有js文件
                    dest:'js/'  //输出到此目录下
                }]
            }
        },
        htmlmin:{
            options:{
                removeComments:false,
                collapseWhitespace:true
            },
            my_target:{
                files:[{
                    expand:true,
                    cwd:'origin_js',
                    src:'**/*.html',
                    dest:'js/'
                }]
            }
        }
    });
 
    //载入uglify插件，压缩js
    grunt.loadNpmTasks('grunt-babel');
    //grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    grunt.loadNpmTasks('grunt-contrib-htmlmin');
    //注册任务
    grunt.registerTask('default', ['babel','uglify','htmlmin']);
    // grunt.registerTask('watcher',['watch']);
}
<2>package.json文件所需插件如下：其中下载安装babel参考官网：https://www.npmjs.com/package/grunt-babel

{
  "name": "appstore",
  "version": "1.0.0",
  "devDependencies": {
    "babel-core":"^6.26.0",
    "babel-preset-env":"^1.6.1",
    "grunt": "^1.0.2",
    "grunt-babel": "^7.0.0",
    "grunt-contrib-htmlmin": "^2.4.0",
    "grunt-contrib-uglify": "^3.3.0"
  }
}

三.效果如下：babel文件夹下是将源文件夹origin_js的代码转换为es5之后的代码，js文件夹下是压缩的babel文件夹下的最终文件
https://img-blog.csdn.net/2018041311361720
