---
layout: photo
title: SpringBoot 发送邮件和附件
tags:
- SpringBoot
- Email
categories: 
- SpringBoot
- Email
description: 使用Spring的JavaMailSender和JavaMailSenderImpl 实现邮件的发送含附件
---

> Spring Boot邮件服务实现

<!-- more -->

## 基础知识

### 什么是SMTP？

SMTP全称为Simple Mail Transfer Protocol（简单邮件传输协议），它是一组用于从源地址到目的地址传输邮件的规范，通过它来控制邮件的中转方式。SMTP认证要求必须提供账号和密码才能登陆服务器，其设计目的在于避免用户受到垃圾邮件的侵扰。

### 什么是IMAP？

IMAP全称为Internet Message Access Protocol（互联网邮件访问协议），IMAP允许从邮件服务器上获取邮件的信息、下载邮件等。IMAP与POP类似，都是一种邮件获取协议。

### 什么是POP3？

POP3全称为Post Office Protocol 3（邮局协议），POP3支持客户端远程管理服务器端的邮件。POP3常用于“离线”邮件处理，即允许客户端下载服务器邮件，然后服务器上的邮件将会被删除。目前很多POP3的邮件服务器只提供下载邮件功能，服务器本身并不删除邮件，这种属于改进版的POP3协议。

### IMAP和POP3协议有什么不同呢？

两者最大的区别在于，IMAP允许双向通信，即在客户端的操作会反馈到服务器上，例如在客户端收取邮件、标记已读等操作，服务器会跟着同步这些操作。而对于POP协议虽然也允许客户端下载服务器邮件，但是在客户端的操作并不会同步到服务器上面的，例如在客户端收取或标记已读邮件，服务器不会同步这些操作。

## 进阶知识

### 什么是JavaMailSender和JavaMailSenderImpl？

JavaMailSender和JavaMailSenderImpl 是Spring官方提供的集成邮件服务的接口和实现类，以简单高效的设计著称，目前是Java后端发送邮件和集成邮件服务的主流工具。

### 如何通过JavaMailSenderImpl发送邮件？

非常简单，直接在业务类注入JavaMailSenderImpl并调用send方法发送邮件。其中简单邮件可以通过SimpleMailMessage来发送邮件，而复杂的邮件（例如添加附件）可以借助MimeMessageHelper来构建MimeMessage发送邮件。例如：

```java
 @Autowired
    private JavaMailSenderImpl mailSender;

    public void sendMail() throws MessagingException {
        //简单邮件
        SimpleMailMessage simpleMailMessage = new SimpleMailMessage();
        simpleMailMessage.setFrom("admin@163.com");
        simpleMailMessage.setTo("wang@qq.com");
        simpleMailMessage.setSubject("Happy New Year");
        simpleMailMessage.setText("新年快乐！");
        mailSender.send(simpleMailMessage);

        //复杂邮件
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        MimeMessageHelper messageHelper = new MimeMessageHelper(mimeMessage);
        messageHelper.setFrom("admin@163.com");
        messageHelper.setTo("wang@qq.com");
        messageHelper.setSubject("Happy New Year");
        messageHelper.setText("新年快乐！");
        messageHelper.addInline("doge.gif", new File("xx/xx/doge.gif"));
        messageHelper.addAttachment("work.docx", new File("xx/xx/work.docx"));
        mailSender.send(mimeMessage);
    }
```

### 为什么JavaMailSenderImpl 能够开箱即用 ？

所谓开箱即用其实就是基于官方内置的自动配置，翻看源码可知晓邮件自动配置类(MailSenderPropertiesConfiguration) 为上下文提供了邮件服务实例(JavaMailSenderImpl)。具体源码如下：

```java
@Configuration
@ConditionalOnProperty(prefix = "spring.mail", name = "host")
class MailSenderPropertiesConfiguration {
    private final MailProperties properties;
    MailSenderPropertiesConfiguration(MailProperties properties) {
        this.properties = properties;
    }
    @Bean
    @ConditionalOnMissingBean
    public JavaMailSenderImpl mailSender() {
        JavaMailSenderImpl sender = new JavaMailSenderImpl();
        applyProperties(sender);
        return sender;
    }
```

其中MailProperties是关于邮件服务器的配置信息，具体源码如下：

```java
@ConfigurationProperties(prefix = "spring.mail")
public class MailProperties {
    private static final Charset DEFAULT_CHARSET = StandardCharsets.UTF_8;
    private String host;
    private Integer port;
    private String username;
    private String password;
    private String protocol = "smtp";
    private Charset defaultEncoding = DEFAULT_CHARSET;
    private Map<String, String> properties = new HashMap<>();
}
```

## 使用教程

### 一、开启邮件服务

登陆网易邮箱163，在设置中打开并勾选POP3/SMTP/IMAP服务，然后会得到一个授权码，这个邮箱和授权码将用作登陆认证。

![163邮箱认证](https://huangzhanguo.github.io/assets/img/springbootmail/微信图片_20191102133537.jpg)

### 二、配置邮件服务

pom引入web、thymeleaf 和spring-boot-starter-mail等相关依赖。例如：

```pom
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
        </dependency>
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>webjars-locator-core</artifactId>
        </dependency>
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
            <version>3.3.1</version>
        </dependency>
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>bootstrap</artifactId>
            <version>3.3.7</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

根据前面提到的配置项(MailProperties)填写相关配置信息，其中spring.mail.username 表示连接邮件服务器时认证的登陆账号，可以是普通的手机号或者登陆账号，并非一定是邮箱，为了解决这个问题，推荐大家在spring.mail. properties.from填写邮件发信人即真实邮箱。

然后在application.yml添加如下配置：

```
server:
  port: 8888
spring:
  application:
    name: Spring-Boot-Send-Mail
  mail:
    host: smtp.163.com #SMTP服务器地址
    username: xxx@163.com  #登陆账号
    password: password #登陆密码（或授权码）
    properties:
      from: xxx@163.com #邮件发信人（即真实邮箱）
  thymeleaf:
    cache: false
    prefix: classpath:/templates/
  servlet:
    multipart:
      max-file-size: 10MB #限制单个文件大小
      max-request-size: 50MB #限制请求总量
```

透过前面的进阶知识，我们知道在发送邮件前，需要先构建 SimpleMailMessage或 MimeMessage 邮件信息类来填写邮件标题、邮件内容等信息，最后提交给JavaMailSenderImpl发送邮件，这样看起来没什么问题，也能实现既定目标，但在实际使用中会出现大量零散和重复的代码，还不便于保存邮件到数据库。

那么优雅的发送邮件应该是如何的呢？应该屏蔽掉这些构建信息和发送邮件的细节，不管是简单还是复杂邮件，都可以通过统一的API来发送邮件。例如：mailService.send(mailVo) 。

例如通过邮件信息类(MailVo) 来保存发送邮件时的邮件主题、邮件内容等信息 ：

```
package com.hehe.vo;

public class MailVo {
    private String id;//邮件id
    private String from;//邮件发送人
    private String to;//邮件接收人（多个邮箱则用逗号","隔开）
    private String subject;//邮件主题
    private String text;//邮件内容
    private Date sentDate;//发送时间
    private String cc;//抄送（多个邮箱则用逗号","隔开）
    private String bcc;//密送（多个邮箱则用逗号","隔开）
    private String status;//状态
    private String error;//报错信息
    @JsonIgnore
    private MultipartFile[] multipartFiles;//邮件附件
   //省略GET&SET方法
}
```

## 三、发送邮件和附件

> =========== 接下来正式介绍发送邮件的最核心逻辑 前方高能 =============

除了发送邮件之外，还包括检测邮件和保存邮件等操作，例如：

- 检测邮件 checkMail(); 首先校验邮件收信人、邮件主题和邮件内容这些必填项，若为空则拒绝发送。

  前后端分离可使用@Valid，BindingResult对参数进行检验

- 发送邮件 sendMimeMail(); 其次通过MimeMessageHelper来解析MailVo并构建MimeMessage传输邮件。
- 保存邮件 sendMimeMail(); 最后将邮件保存到数据库，便于统计和追查邮件问题。

本案例邮件业务类 MailService 的具体源码如下：

```java
package com.hehe.service;

/**
 * 邮件业务类 MailService
 */
@Service
public class MailService {

    private Logger logger = LoggerFactory.getLogger(getClass());//提供日志类

    @Autowired
    private JavaMailSenderImpl mailSender;//注入邮件工具类


    /**
     * 发送邮件
     */
    public MailVo sendMail(MailVo mailVo) {
        try {
            checkMail(mailVo); //1.检测邮件
            sendMimeMail(mailVo); //2.发送邮件
            return saveMail(mailVo); //3.保存邮件
        } catch (Exception e) {
            logger.error("发送邮件失败:", e);//打印错误信息
            mailVo.setStatus("fail");
            mailVo.setError(e.getMessage());
            return mailVo;
        }

    }

    //检测邮件信息类
    private void checkMail(MailVo mailVo) {
        if (StringUtils.isEmpty(mailVo.getTo())) {
            throw new RuntimeException("邮件收信人不能为空");
        }
        if (StringUtils.isEmpty(mailVo.getSubject())) {
            throw new RuntimeException("邮件主题不能为空");
        }
        if (StringUtils.isEmpty(mailVo.getText())) {
            throw new RuntimeException("邮件内容不能为空");
        }
    }

    //构建复杂邮件信息类
    private void sendMimeMail(MailVo mailVo) {
        try {
            MimeMessageHelper messageHelper = new MimeMessageHelper(mailSender.createMimeMessage(), true);//true表示支持复杂类型
            mailVo.setFrom(getMailSendFrom());//邮件发信人从配置项读取
            messageHelper.setFrom(mailVo.getFrom());//邮件发信人
            messageHelper.setTo(mailVo.getTo().split(","));//邮件收信人
            messageHelper.setSubject(mailVo.getSubject());//邮件主题
            messageHelper.setText(mailVo.getText());//邮件内容
            if (!StringUtils.isEmpty(mailVo.getCc())) {//抄送
                messageHelper.setCc(mailVo.getCc().split(","));
            }
            if (!StringUtils.isEmpty(mailVo.getBcc())) {//密送
                messageHelper.setCc(mailVo.getBcc().split(","));
            }
            if (mailVo.getMultipartFiles() != null) {//添加邮件附件
                for (MultipartFile multipartFile : mailVo.getMultipartFiles()) {
                    messageHelper.addAttachment(multipartFile.getOriginalFilename(), multipartFile);
                }
            }
            if (StringUtils.isEmpty(mailVo.getSentDate())) {//发送时间
                mailVo.setSentDate(new Date());
                messageHelper.setSentDate(mailVo.getSentDate());
            }
            mailSender.send(messageHelper.getMimeMessage());//正式发送邮件
            mailVo.setStatus("ok");
            logger.info("发送邮件成功：{}->{}", mailVo.getFrom(), mailVo.getTo());
        } catch (Exception e) {
            throw new RuntimeException(e);//发送失败
        }
    }

    //保存邮件
    private MailVo saveMail(MailVo mailVo) {
        //将邮件保存到数据库..
        return mailVo;
    }

    //获取邮件发信人
    public String getMailSendFrom() {
        return mailSender.getJavaMailProperties().getProperty("from");
    }
}
```

> 搞定了发送邮件最核心的业务逻辑，接下来咱们写一个简单页面用来发送邮件。

首先写好跟页面交互的控制器 MailController，具体源码如下：

```java
@RestController
public class MailController {
    @Autowired
    private MailService mailService;

    /**
     * 发送邮件的主界面
     */
    @GetMapping("/")
    public ModelAndView index() {
        ModelAndView mv = new ModelAndView("sendMail");//打开发送邮件的页面
        mv.addObject("from", mailService.getMailSendFrom());//邮件发信人
        return mv;
    }
    /**
     * 发送邮件
     */
    @PostMapping("/mail/send")
    public MailVo sendMail(MailVo mailVo, MultipartFile[] files) {
        mailVo.setMultipartFiles(files);
        return mailService.sendMail(mailVo);//发送邮件和附件
    }
}
```

然后在/resources/views/mail目录新建sendMail.html，具体源码如下：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

<head>
    <meta charset="UTF-8"/>
    <title>发送邮件</title>
    <link th:href="@{/webjars/bootstrap/css/bootstrap.min.css}" rel="stylesheet" type="text/css"/>
    <script th:src="@{/webjars/jquery/jquery.min.js}"></script>
    <script th:href="@{/webjars/bootstrap/js/bootstrap.min.js}"></script>

</head>

<body>
<div class="col-md-6" style="margin:20px;padding:20px;border: #E0E0E0 1px solid;">
    <marquee behavior="alternate" onfinish="alert(12)" id="mq"
             onMouseOut="this.start();$('#egg').text('嗯 真听话！');"
             onMouseOver="this.stop();$('#egg').text('有本事放开我呀！');">
        <h5 id="egg">祝大家新年快乐！</h5><img id="doge" src="http://pics.sc.chinaz.com/Files/pic/faces/3709/7.gif" alt="">
    </marquee>

    <form class="form-horizontal" id="mailForm">
        <div class="form-group">
            <label class="col-md-2 control-label">邮件发信人:</label>
            <div class="col-md-6">
                <input class="form-control" id="from" name="from" th:value="${from}" readonly="readonly">
            </div>
        </div>
        <div class="form-group">
            <label class="col-md-2 control-label">邮件收信人:</label>
            <div class="col-md-6">
                <input class="form-control" id="to" name="to" title="多个邮箱使用,隔开">
            </div>
        </div>
        <div class="form-group">
            <label class="col-md-2 control-label">邮件主题:</label>
            <div class="col-md-6">
                <input class="form-control" id="subject" name="subject">
            </div>
        </div>
        <div class="form-group">
            <label class="col-md-2 control-label">邮件内容:</label>
            <div class="col-md-6">
                <textarea class="form-control" id="text" name="text" rows="5"></textarea>
            </div>
        </div>
        <div class="form-group">
            <label class="col-md-2 control-label">邮件附件:</label>
            <div class="col-md-6">
                <input class="form-control" id="files" name="files" type="file" multiple="multiple">
            </div>
        </div>
        <div class="form-group">
            <label class="col-md-2 control-label">邮件操作:</label>
            <div class="col-md-3">
                <a class="form-control btn btn-primary" onclick="sendMail()">发送邮件</a>
            </div>
            <div class="col-md-3">
                <a class="form-control btn btn-default" onclick="clearForm()">清空</a>
            </div>
        </div>
    </form>

    <script th:inline="javascript">
        var appCtx = [[${#request.getContextPath()}]];

        function sendMail() {

            var formData = new FormData($('#mailForm')[0]);
            $.ajax({
                url: appCtx + '/mail/send',
                type: "POST",
                data: formData,
                contentType: false,
                processData: false,
                success: function (result) {
                    alert(result.status === 'ok' ? "发送成功！" : "你被Doge嘲讽了：" + result.error);
                },
                error: function () {
                    alert("发送失败！");
                }
            });
        }

        function clearForm() {
            $('#mailForm')[0].reset();
        }

        setInterval(function () {
            var total = $('#mq').width();
            var width = $('#doge').width();
            var left = $('#doge').offset().left;
            if (left <= width / 2 + 20) {
                $('#doge').css('transform', 'rotateY(180deg)')
            }
            if (left >= total - width / 2 - 40) {
                $('#doge').css('transform', 'rotateY(-360deg)')
            }
        });
    </script>
</div>
</body>
</html>
```

## 四、测试发送邮件

如果是初学者，建议大家先下载源码，修改配置后运行工程，成功后再自己重新写一遍代码，这样有助于加深记忆。

启动工程并访问：http://localhost:8888 然后可以看到发送邮件的主界面如下：

![](https://huangzhanguo.github.io/blog_img/springbootmail/微信截图_20191102111759.png)

![](https://huangzhanguo.github.io/blog_img/springbootmail/微信截图_20191102111900.png)

至此发送邮件代码全部完成，欢迎大家下载并关注Github 源码。

## 五、常见失败编码

如果企业定制了邮件服务器，自然会记录邮件日志，根据错误编码存储日志有利于日常维护。

例如这些由网易邮箱提供的错误编码标识：

### 421

- 421 HL:REP 该IP发送行为异常，存在接收者大量不存在情况，被临时禁止连接。请检查是否有用户发送病毒或者垃圾邮件，并核对发送列表有效性；
- 421 HL:ICC 该IP同时并发连接数过大，超过了网易的限制，被临时禁止连接。请检查是否有用户发送病毒或者垃圾邮件，并降低IP并发连接数量；
- 421 HL:IFC 该IP短期内发送了大量信件，超过了网易的限制，被临时禁止连接。请检查是否有用户发送病毒或者垃圾邮件，并降低发送频率；
- 421 HL:MEP 该IP发送行为异常，存在大量伪造发送域域名行为，被临时禁止连接。请检查是否有用户发送病毒或者垃圾邮件，并使用真实有效的域名发送；

### 450

- 450 MI:CEL 发送方出现过多的错误指令。请检查发信程序；
- 450 MI:DMC 当前连接发送的邮件数量超出限制。请减少每次连接中投递的邮件数量；
- 450 MI:CCL 发送方发送超出正常的指令数量。请检查发信程序；
- 450 RP:DRC 当前连接发送的收件人数量超出限制。请控制每次连接投递的邮件数量；
- 450 RP:CCL 发送方发送超出正常的指令数量。请检查发信程序；
- 450 DT:RBL 发信IP位于一个或多个RBL里。请参考http://www.rbls.org/关于RBL的相关信息；
- 450 WM:BLI 该IP不在网易允许的发送地址列表里；
- 450 WM:BLU 此用户不在网易允许的发信用户列表里；

### 451

- 451 DT:SPM ,please try again 邮件正文带有垃圾邮件特征或发送环境缺乏规范性，被临时拒收。请保持邮件队列，两分钟后重投邮件。需调整邮件内容或优化发送环境；
- 451 Requested mail action not taken: too much fail authentication 登录失败次数过多，被临时禁止登录。请检查密码与帐号验证设置；
- 451 RP:CEL 发送方出现过多的错误指令。请检查发信程序；
- 451 MI:DMC 当前连接发送的邮件数量超出限制。请控制每次连接中投递的邮件数量；
- 451 MI:SFQ 发信人在15分钟内的发信数量超过限制，请控制发信频率；
- 451 RP:QRC 发信方短期内累计的收件人数量超过限制，该发件人被临时禁止发信。请降低该用户发信频率；
- 451 Requested action aborted: local error in processing 系统暂时出现故障，请稍后再次尝试发送；

### 500

- 500 Error: bad syntaxU 发送的smtp命令语法有误；
- 550 MI:NHD HELO命令不允许为空；
- 550 MI:IMF 发信人电子邮件地址不合规范。请参考http://www.rfc-editor.org/关于电子邮件规范的定义；
- 550 MI:SPF 发信IP未被发送域的SPF许可。请参考http://www.openspf.org/关于SPF规范的定义；
- 550 MI:DMA 该邮件未被发信域的DMARC许可。请参考http://dmarc.org/关于DMARC规范的定义；
- 550 MI:STC 发件人当天的连接数量超出了限定数量，当天不再接受该发件人的邮件。请控制连接次数；
- 550 RP:FRL 网易邮箱不开放匿名转发（Open relay）；
- 550 RP:RCL 群发收件人数量超过了限额，请减少每封邮件的收件人数量；
- 550 RP:TRC 发件人当天内累计的收件人数量超过限制，当天不再接受该发件人的邮件。请降低该用户发信频率；
- 550 DT:SPM 邮件正文带有很多垃圾邮件特征或发送环境缺乏规范性。需调整邮件内容或优化发送环境；
- 550 Invalid User 请求的用户不存在；
- 550 User in blacklist 该用户不被允许给网易用户发信；
- 550 User suspended 请求的用户处于禁用或者冻结状态；
- 550 Requested mail action not taken: too much recipient 群发数量超过了限额；

### 552

- 552 Illegal Attachment 不允许发送该类型的附件，包括以.uu .pif .scr .mim .hqx .bhx .cmd .vbs .bat .com .vbe .vb .js .wsh等结尾的附件；
- 552 Requested mail action aborted: exceeded mailsize limit 发送的信件大小超过了网易邮箱允许接收的最大限制；

### 553

- 553 Requested action not taken: NULL sender is not allowed 不允许发件人为空，请使用真实发件人发送；
- 553 Requested action not taken: Local user only SMTP类型的机器只允许发信人是本站用户；
- 553 Requested action not taken: no smtp MX only MX类型的机器不允许发信人是本站用户；
- 553 authentication is required SMTP需要身份验证，请检查客户端设置；

### 554

- 554 DT:SPM 发送的邮件内容包含了未被许可的信息，或被系统识别为垃圾邮件。请检查是否有用户发送病毒或者垃圾邮件；
- 554 DT:SUM 信封发件人和信头发件人不匹配；
- 554 IP is rejected, smtp auth error limit exceed 该IP验证失败次数过多，被临时禁止连接。请检查验证信息设置；
- 554 HL:IHU 发信IP因发送垃圾邮件或存在异常的连接行为，被暂时挂起。请检测发信IP在历史上的发信情况和发信程序是否存在异常；
- 554 HL:IPB 该IP不在网易允许的发送地址列表里；
- 554 MI:STC 发件人当天内累计邮件数量超过限制，当天不再接受该发件人的投信。请降低发信频率；
- 554 MI:SPB 此用户不在网易允许的发信用户列表里；
- 554 IP in blacklist 该IP不在网易允许的发送地址列表里。