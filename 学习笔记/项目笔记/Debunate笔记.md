# 本文是为了快速搭建springboot项目而写

# pom

```java
<properties>
        <java.version>1.8</java.version>
        <guli.version>0.0.1-SNAPSHOT</guli.version>
        <mybatis-plus.version>3.0.5</mybatis-plus.version>
        <velocity.version>2.0</velocity.version>
        <swagger.version>2.7.0</swagger.version>
        <aliyun.oss.version>2.8.3</aliyun.oss.version>
        <jodatime.version>2.10.1</jodatime.version>
        <poi.version>3.17</poi.version>
        <commons-fileupload.version>1.3.1</commons-fileupload.version>
        <commons-io.version>2.6</commons-io.version>
        <httpclient.version>4.5.1</httpclient.version>
        <jwt.version>0.7.0</jwt.version>
        <aliyun-java-sdk-core.version>4.3.3</aliyun-java-sdk-core.version>
        <aliyun-sdk-oss.version>3.1.0</aliyun-sdk-oss.version>
        <aliyun-java-sdk-vod.version>2.15.2</aliyun-java-sdk-vod.version>
        <aliyun-java-vod-upload.version>1.4.11</aliyun-java-vod-upload.version>
        <aliyun-sdk-vod-upload.version>1.4.11</aliyun-sdk-vod-upload.version>
        <fastjson.version>1.2.28</fastjson.version>
        <gson.version>2.8.2</gson.version>
        <json.version>20170516</json.version>
        <commons-dbutils.version>1.7</commons-dbutils.version>
        <canal.client.version>1.1.0</canal.client.version>
        <docker.image.prefix>zx</docker.image.prefix>
        <cloud-alibaba.version>0.2.2.RELEASE</cloud-alibaba.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!--Spring Cloud-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--mybatis-plus 持久层-->
            <dependency>
                <groupId>com.baomidou</groupId>
                <artifactId>mybatis-plus-boot-starter</artifactId>
                <version>${mybatis-plus.version}</version>
            </dependency>

            <!-- velocity 模板引擎, Mybatis Plus 代码生成器需要 -->
            <dependency>
                <groupId>org.apache.velocity</groupId>
                <artifactId>velocity-engine-core</artifactId>
                <version>${velocity.version}</version>
            </dependency>

            <!--swagger-->
            <dependency>
                <groupId>io.springfox</groupId>
                <artifactId>springfox-swagger2</artifactId>
                <version>${swagger.version}</version>
            </dependency>
            <!--swagger ui-->
            <dependency>
                <groupId>io.springfox</groupId>
                <artifactId>springfox-swagger-ui</artifactId>
                <version>${swagger.version}</version>
            </dependency>

            <!--aliyunOSS-->
            <dependency>
                <groupId>com.aliyun.oss</groupId>
                <artifactId>aliyun-sdk-oss</artifactId>
                <version>${aliyun.oss.version}</version>
            </dependency>

            <!--日期时间工具-->
            <dependency>
                <groupId>joda-time</groupId>
                <artifactId>joda-time</artifactId>
                <version>${jodatime.version}</version>
            </dependency>

            <!--xls-->
            <dependency>
                <groupId>org.apache.poi</groupId>
                <artifactId>poi</artifactId>
                <version>${poi.version}</version>
            </dependency>
            <!--xlsx-->
            <dependency>
                <groupId>org.apache.poi</groupId>
                <artifactId>poi-ooxml</artifactId>
                <version>${poi.version}</version>
            </dependency>

            <!--文件上传-->
            <dependency>
                <groupId>commons-fileupload</groupId>
                <artifactId>commons-fileupload</artifactId>
                <version>${commons-fileupload.version}</version>
            </dependency>

            <!--commons-io-->
            <dependency>
                <groupId>commons-io</groupId>
                <artifactId>commons-io</artifactId>
                <version>${commons-io.version}</version>
            </dependency>

            <!--httpclient-->
            <dependency>
                <groupId>org.apache.httpcomponents</groupId>
                <artifactId>httpclient</artifactId>
                <version>${httpclient.version}</version>
            </dependency>

            <dependency>
                <groupId>com.google.code.gson</groupId>
                <artifactId>gson</artifactId>
                <version>${gson.version}</version>
            </dependency>

            <!-- JWT -->
            <dependency>
                <groupId>io.jsonwebtoken</groupId>
                <artifactId>jjwt</artifactId>
                <version>${jwt.version}</version>
            </dependency>

            <!--aliyun-->
            <dependency>
                <groupId>com.aliyun</groupId>
                <artifactId>aliyun-java-sdk-core</artifactId>
                <version>${aliyun-java-sdk-core.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

# swagger

```java
@Configuration
@EnableSwagger2
public class Swagger2Config {
    @Bean
    public Docket webApiConfig(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("webApi")
                .apiInfo(webApiInfo())
                .select()
                //只显示api路径下的页面
                .paths(Predicates.and(PathSelectors.regex("/api/.*")))
                .build();
    }

    @Bean
    public Docket adminApiConfig(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("adminApi")
                .apiInfo(adminApiInfo())
                .select()
                //只显示admin路径下的页面
                .paths(Predicates.and(PathSelectors.regex("/admin/.*")))
                .build();
    }

    private ApiInfo webApiInfo(){
        return new ApiInfoBuilder()
                .title("网站-API文档")
                .description("本文档描述了网站微服务接口定义")
                .version("1.0")
                .contact(new Contact("lovePc", "http://pc.com", "493211102@qq.com"))
                .build();
    }

    private ApiInfo adminApiInfo(){
        return new ApiInfoBuilder()
                .title("后台管理系统-API文档")
                .description("本文档描述了后台管理系统微服务接口定义")
                .version("1.0")
                .contact(new Contact("lovePc", "http:/pc.com", "49321112@qq.com"))
                .build();
    }
}
```

## MD5

```java
public final class MD5 {

    public static String encrypt(String strSrc) {
        try {
            char hexChars[] = { '0', '1', '2', '3', '4', '5', '6', '7', '8',
                    '9', 'a', 'b', 'c', 'd', 'e', 'f' };
            byte[] bytes = strSrc.getBytes();
            MessageDigest md = MessageDigest.getInstance("MD5");
            md.update(bytes);
            bytes = md.digest();
            int j = bytes.length;
            char[] chars = new char[j * 2];
            int k = 0;
            for (int i = 0; i < bytes.length; i++) {
                byte b = bytes[i];
                chars[k++] = hexChars[b >>> 4 & 0xf];
                chars[k++] = hexChars[b & 0xf];
            }
            return new String(chars);
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
            throw new RuntimeException("MD5加密出错！！+" + e);
        }
    }


}
```

# 统一异常处理

```java
/**
 * 全局异常来
 * */
@ControllerAdvice
public class GlobalExpectionHandler {

    @ExceptionHandler(Exception.class)
    @ResponseBody
    public Result erro(Exception e){
        e.printStackTrace();
        return Result.fail();
    }   
}
```

# JWT单点登录流程

> * 后端部分基本流程
>   
>   - 编写两个controller
>   
>   - 一个负责生成token传给前端
>   
>   - 另一个负责获取前端中header中的token转化成用户信息
> 
> * 前端部分基本流程
>   
>   * 将后端产生的token放入cookie中
>   
>   * 配置拦截器，将cookie中的token放入header中
>   
>   * 调用后端接口获取到headr中的信息

## 后端代码实现

> ### controller类
> 
> * #### 生成token的controller
>   
>   ```java
>       /**
>        * 使用token进行登陆
>        * */
>       @PostMapping("loginByToken")
>       public ResultBack loginByToken(@RequestBody LoginTable loginTable){
>          //发送token值给前端
>           String token =  loginService.loginByToken(loginTable);
>          return ResultBack.ok().data("token",token);
>       }
>   ```
> 
> * #### 通过token获取用户信息的controller类
>   
>   ```java
>       /**
>        * 获取头信息中的token 返回用户信息
>        * */
>       @GetMapping("getByToken")
>       public ResultBack getByToken(HttpServletRequest request){
>           //根据request的对象头获取头信息 返回用户id
>           String id = Jwt.getMemberIdByJwtToken(request);
>           //根据id获取用户信息
>           LoginTable loginTable = loginService.getById(id);
>           Map<String, Object> map = new HashMap<>();
>           map.put("userInfo",loginTable);
>           return ResultBack.ok().data(map);
>       }
>   ```

> * ### service接口类
> 
> ```java
>  String loginByToken(LoginTable loginTable);
> ```

> #### serviceImpl接口实现类
> 
> ```java
>  @Override
>     public String loginByToken(LoginTable loginData) {
>         //获取数据
>         Integer uid = loginData.getUid();
>         String password = loginData.getPassword();
>         //构建条件
>         QueryWrapper<LoginTable> query = new QueryWrapper<>();
>         query.eq("uid", uid).eq("password", MD5.encrypt(password));
>         //获取数据
>         LoginTable loginTable = baseMapper.selectOne(query);
>         return Jwt.getJwtToken(String.valueOf(loginTable.getId()), loginTable.getUsername());
>     }
> ```

## 前端代码实现

> * ### 配置拦截器
>   
>   ```js
>   //请求拦截器
>   service.interceptors.request.use(
>       //config配置对象
>       config => {
>           //判断cookie中是否包含名称是cookiname的数据
>           if(cookie.get('cookieName')){
>               //把cookie里面的值放到header里面
>               config.headers['token'] = cookie.get('cookieName');
>           }
>           return config;
>       },
>       error => {
>           console.log(error);
>           return Promise.reject();
>       }
>   );
>   ```
> 
> * ### 调用后端接口获取token值放入cookie中
>   
>   ```js
>       //jwt登录
>       function loginByToken() {
>         userInfo.loginByToken(param).then((res) => {
>           //获取token值
>           let token = res.data.token;
>           //放入cookie中 【第一个参数 是名字，第二个参数是信息，第三个参数是作用域】
>           cookie.set("cookieName", token, { domain: "localhost" });
>           //根据头信息中的token查询信息
>           //再将查询的信息放到cookie中
>           getUserInfoByToken();
>           router.push("/loginIndex")
>         });
>       }
>       //根据token查询信息的方法
>       function getUserInfoByToken() {
>         userInfo.getUserInfoByToken().then((res) => {
>           let data = JSON.stringify(res.data.userInfo);
>           cookie.set("userInfo", data, { domain:'localhost'})
>         });
>       }
>   ```
> 
> * ### 调用后端接口将header中的token转化成用户信息
>   
>   ```js
>   
>   ```

# 如何动态绑定变量发送请求

> 通过 v-bind:src

# TODO 点击提交照片获取创建时间
