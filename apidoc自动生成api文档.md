
# MCU 接口文档生成工具(apidoc) 使用说明

Create By JayceMei

## apidoc

1. 下载

    全局安装
    `npm install apidoc -g`

    在项目中，我直接放到了项目的node_modules 中， 调用方式 Windows 下使用 apidoc.cmd, Linux 使用apidoc.


2. 配置

    在根目录下，创建apidoc.json
        
        "name": "apidoc-mcu",
        "version": "1.0.0",   // 文档版本
        "description": "MCU ApiDoc",
        "title": "MCU RESTful 接口文档",
        "url" : "https://localhost",
        "template": {
            "withCompare": true,
            "withGenerator": true
        }
    

    apidoc.json 中指定了一些最后生成的api 文档的格式及部分配置， 详情参见 http://apidocjs.com/#configuration 

3. 使用

    通过在代码中使用 apidoc 固定的一些注释格式， 通过该工具可以将这些注释自动生成静态的HTML页面。

    如： 
    
        /**
        * @api {post} /post/admin/add MCU账户添加
        * @apiDescription MCU账户添加
        * @apiName add
        * @apiGroup admin
        * @apiParam {json} data {"adminid":"mjx","adminname":"mjx",    "roleid":"4","password":"mjx123456","tel":"","email":""}
        * @apiSuccess {number} result 1
        * @apiSuccess {string} msg "success"
        * @apiSuccessExample {json} Success-Response:
        *  {
        *      result : 1,
        *      msg : "success"
        *  }
        * @apiError {number} result 0 / 500100（用户名已存在）
        * @apiError {string} msg "error msg"
        * 
        * @apiSampleRequest http://localhost/post/admin/add
        * @apiVersion 1.0.0
        */
    
    在命令行中输入 `.\apidoc.cmd -i .\routes\ -o .\public\apidoc\` , 在.\public\apidoc\ 目录下 自动生成api文档的静态HTML文件

    `-i --input <input>`  输入源目录

    `-o --output <output>` 输出目录 
    

    生成的静态HTML，可以直接通过文件路径访问 file:///E:/Project/MCU/WEB/Trunk/public/apidoc/index.html 。
    
    但是在我们项目中，由于通过express指定了静态资源的访问映射 `app.use(express.static(__dirname + '/public'));
` 是故当服务启动后可以通过 http://localhost/apidoc/index.html# 方式直接访问。



4. 版本更迭

    当出现接口文档版本更新时， 可通过叠加注释的方式进行更新

        /**
        * @api {post} /post/admin/add MCU账户添加
        * @apiDescription MCU账户添加
        * @apiName add
        * @apiGroup admin
        * @apiParam {json} data {"adminid":"mjx","adminname":"mjx","roleid":"4","password":"mjx123456","tel":"","email":""}
        * @apiSuccess {number} result 1
        * @apiSuccess {string} msg "success"
        * @apiSuccessExample {json} Success-Response:
        *  {
        *      result : 1,
        *      msg : "success"
        *  }
        * 
        * @apiError {number} result 0 / 500100（用户名已存在）
        * @apiError {string} msg "error msg"
        * @apiSampleRequest http://localhost/post/admin/add
        * @apiVersion 1.0.0
        * 
        */
        /** 
        * 
        * @api {post} /post/admin/add MCU账户添加 第二版 测试
        * @apiDescription MCU账户添加
        * @apiName add
        * @apiGroup admin
        * @apiParam {json} data {"adminid":"mjx","adminname":"mjx","roleid":"4","password":"mjx123456","tel":"","email":""}
        * @apiSuccess {number} result 1
        * @apiSuccess {string} msg "success"
        * @apiSuccessExample {json} Success-Response:
        *  {
        *      result : 1,
        *      msg : "success"
        *  }
        * 
        * @apiError {number} result 0 / 500100（用户名已存在）
        * @apiError {string} msg "error msg"
        * 
        * @apiSampleRequest http://localhost/post/admin/add
        * @apiVersion 1.0.1
        */

    通过改变 @apiVersion 的值， 来实现某个api 的版本更新。


