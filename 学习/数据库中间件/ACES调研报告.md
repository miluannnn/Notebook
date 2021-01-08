# ACES调研报告

[TOC]

## 一 资料来源

官方文档：http://doc.tde.jd.local:8001/base_guide/#step_2

cf文档：https://cf.jd.com/pages/viewpage.action?pageId=104955500

ACES技术支持：erp: xnaces

咚咚群: 5306506

## 二 操作步骤

1. 登录平台：使用ERP账号登录ACES-WEB平台
2. 创建业务：创建用于加密/解密的ACES业务
3. 申请授权：根据具体业务需求申请加密/解密授权权限
4. 审批授权：提交授权申请后负责人处理授权审批
5. 获取Token：完成授权审批后获取业务的Token
6. 集成SDK：在SDK中使用Token初始化客户端进行加解密等操作

## 三 实践

### 登录平台

 **ACES-WEB管理平台地址如下：**
     正式环境-北京站：http://aces.jd.com/
     正式环境-印尼站：[http://aces.jd.id](http://aces.jd.id/)
     正式环境-泰国站：[http://aces.jd.co.th](http://aces.jd.co.th/)
     测试环境-北京站：[http://test.aces.jd.com](http://test.aces.jd.com/)

1. 海外站（印尼、泰国）请先点击以下链接申请账号挂靠

   印尼账号挂靠：http://consoleint-prod.idomdm.svc.yn.n.jd.local/self/applyCnInId
   泰国账号挂靠：http://console.asiahrmdm.jd.com/self/applyCnInTh

   注：因海外账号挂靠需由人资部门完成审批后才会同步数据，建议提交挂靠申请后等待30分钟以上再进行登录。部分账号的同步数据可能存在空缺项（比如密码为空），如登录失败，可点击「忘记密码?」完成密码重置后再做尝试。如仍未解决，则请联系可进“人资运维群”咨询（咚咚群号：**5678689**）或 [联系我们](http://doc.tde.jd.local:8001/#acesContacts)

2. 输入ERP账号和密码登录ACES-WEB平台，ACES使用内网单点登录系统进行账号管理，如登录失败，请确认ERP账号或密码是否正确，也可点击「忘记密码?」进行密码重置

###  创建业务

1. 点击左侧导航菜单的「我的业务」，进入“业务列表”

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-2.png)

2. 点击“业务列表”左上角的「创建」，进入“创建”界面填写业务的基本信息，包含业务标识来源（j-one/np.jd.com/特殊）、应用名称/业务唯一关键字、负责人、成员、应用(业务)描述、初始凭证生效区间、初始凭证别名

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-3.png)

- 注1：来源于j-one和np.jd.com的应用业务，请填写正确的“应用名称”
- 注2：来源特殊的业务，请填写由大小写字母、数字和下划线组成的“业务唯一关键字”
- 注3：测试环境无法同步jone和np的信息，请选择“特殊”建立测试业务

3. 点击「下一页」，填写业务的高级信息，包含数据加解密需求（需要对数据进行加密/仅解密其他业务数据）、加密使用场景（加密数据/文件）、密文是否存库（是/否）以及加密数据存储的数据库名和表名（该信息会用于大数据集市抽数，请如实填写）

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-4.png)

4. 点击「保存」，对于“仅解密其他业务数据”的业务将直接创建成功，对于“需要对数据进行加密”的业务将进入创建自授权界面

### 申请授权

创建业务时如果选择了“需要对数据进行加密”选项，保存后会直接弹出创建自授权界面。此外，也可通过以下方式创建授权。授权过期后该业务将无法继续对数据进行加密/解密，此时可以重新申请授权或进行授权续期，进而保证业务的连续运行。

**注：若需要计算BDP索引（即sha256不可逆加密算法），请先根据如下所述申请大数据集市业务的解密授权，并联系业务负责人进行审批**

- 正式环境-北京站：申请「bdp_sha256_Z1mD4mJB」的解密授权
- 正式环境-印尼站：申请「bdp_sha256_grI9yVB5.id」的解密授权
- 正式环境-泰国站：申请「bdp_sha256_ZnMVUEbz.th」的解密授权
- 测试环境-北京站：申请「bdp_sha256_vVqV9Qdk」的解密授权

**方式一：**

1. 点击左侧导航菜单的「我的业务」，进入“业务列表”
2. 点击“业务列表”中操作栏的「查看授权」，进入“授权列表”

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-6.png)

3. 点击“授权列表”左上角的「创建授权」，弹出下拉框选择要创建的授权类型

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-7.png)

4. 选择下拉框的「创建自己的加解密授权」选项，进入“创建自授权”界面，填写授权生效区间（最长可设置为两年）、用途说明

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-5.png)

5. 选择下拉框的「申请其它业务解密授权」选项，进入“申请授权”界面，填写对方业务标识码（即加密数据的业务）、授权生效区间（最长可设置为两年）、用途说明

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-8.png)

6. 点击「提交」，等待双方业务负责人进行审批

**方式二（适用于对方业务标识码关键词已知的场景）：**

1. 点击左侧导航菜单的「我的申请」下的「新申请」选项
2. 在「对方业务识别码」搜索框中输入关键词，按下回车键查看检索信息（支持模糊检索）

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-9.png)

3. 查找到对方业务后选择「申请授权」操作，进入申请授权界面，填写我的业务标识码（即自己要用于解密的业务）、授权生效区间（最长可设置为两年）、用途说明

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-10.png)

1. 点击「提交」，等待双方业务负责人进行审批

**方式三（适用于对方数据库及表名已知的场景，支持批量申请授权）：**

1. 点击左侧导航菜单的「我的申请」下的「新申请」选项
2. 点击「根据数据库表查询」按钮，在搜索框中输入关键词并在下拉检索结果框中选择数据库/表名，点击「搜索」查看检索信息
3. 点击列表左侧的「添加」按钮将暂存该授权申请（也可点击「取消」按钮移除该授权申请）

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-11.png)

4. 点击搜索框左侧的「已添加」按钮，进入“批量申请授权”界面，填写我的业务标识码（即自己要用于解密的业务）、授权生效区间（最长可设置为两年）、用途说明

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-12.png)

5. 点击「提交」，等待双方业务负责人进行审批

### 审批授权

1. 点击左侧导航菜单的「我的审批」
2. 点击「审批待办」Tab标签，查看待处理的审批信息，点击「通过」或「驳回」进行处理

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-15.png)

### 获取Token

1. 点击左侧导航菜单的「我的业务」，进入“业务列表”
2. 点击“业务列表”中操作栏的「查看Token」，进入“凭证列表”

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-17.png)

3. 在“凭证列表”中点击「获取Token」，可在弹出框中查看并复制Token凭证

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-18.png)

4. 通常在创建业务时会直接创建初始凭证，若想创建新Token凭证，可点击“凭证列表”左上角的「创建」，进入“创建Token”界面，填写生效区间（最长可设置为两年）、凭证别名

![Screenshot](http://doc.tde.jd.local:8001/imgs/web-19.png)

### 集成SDK

在集成SDK之前，请先确认Token所属业务具有已完成审批的有效授权。下文将以Java SDK版本为例，描述开发人员如何集成Java SDK进行加解密。

**若Token来自于ACES的测试环境，请先配置hosts：[10.170.180.106 beta.kms.tde.jd.local] 或 [11.50.60.26 beta.kms.tde.jd.local]**

若需要解密大数据集市的数据，请集成BDP SDK（即将groupId配置为"com.jd.security.bdp"）

1.在Maven的pom.xml文件中配置如下SDK依赖（ACES也提供Java SDK-Spring集成方式、BDP SDK-Spring集成方式和Mybatis插件）

```
<dependencies>
    <dependency>
        <!-- 若需解密大数据集市的数据，groupId请配置为"com.jd.security.bdp" -->
        <groupId>com.jd.security</groupId>
        <artifactId>tdeclient</artifactId>
        <!-- 海外站(印尼/泰国)业务请使用"2.0.4-SNAPSHOT"及以上版本 -->
        <version>2.0.4-SNAPSHOT</version>
    </dependency>
</dependencies>
```

注：
      （1）若需解密大数据集市的数据，groupId请配置为"com.jd.security.bdp"。
      （2）强烈建议指定<version>，避免版本不兼容，具体版本号信息请参阅版本日志。

2.配置Token（从ACES-WEB平台获取）并进行加解密操作，示例代码如下（Git: Java Demo; UDF Demo）

```java
import com.jd.security.tde.Constants;
import com.jd.security.tde.util.Base64;
import com.jd.security.tde.util.TimeMeasurement;
import com.jd.security.tdeclient.TDEClient;
import org.apache.commons.text.RandomStringGenerator;

import java.util.Arrays;
import java.util.Random;

/**
 * TDEClient Demo
 *
 * @author JD Data Security Team
 * @version 1.0
 */

public class TDEDemo {
    public static void main(String[] args) {

        // TDEClient客户端
        TDEClient client = null;

        // 请替换为自己从ACES-WEB平台获取的Token
        String base64_token = "eyJzaWciOiJN...";
        // 环境标识，线上环境设为true，测试环境设为false
        // 测试环境还需配置系统hosts：[10.170.180.106 beta.kms.tde.jd.local] 或 [11.50.60.26 beta.kms.tde.jd.local]
        boolean isProd = false;

        String input = "TDEClient Demo";

        try {
            // #1-初始化TDEClient客户端
            client = TDEClient.getInstance(base64_token, isProd);
            // #2-调用字符串可逆加密API
            String encrypted = client.encryptString(input);
            // #3-校验密文是否可以被解密
            if (client.isDecryptable(encrypted) == CipherStatus.Decryptable) {
                // #4-调用字符串解密API
                String decrypted = client.decryptString(encrypted);
                if (input.equals(decrypted)) {
                    System.out.println("Test case passed.");
                } else {
                    System.err.println("Test case failed.");
                }
            } else {
                System.err.println("Test case failed.");
            }
            // #5-BDP SDK调用不可逆加密API，计算sha256索引
            // 此API需事先申请大数据业务的解密权限，请咚咚联系[xnaces]获取大数据业务标识码并审批
            String index = client.bdpIndex("18846448756");
            if (index.equals("k+NA/ctKU9IbIhZkzESVvDhqHLP3U361eqqju3Yrruw=")) {
                System.out.println("BDP index calculation test case passed.");
            } else {
                System.err.println("BDP index calculation test case failed.");
            }
        } catch (Exception e) {
            System.out.println(e.getLocalizedMessage());
        }
    }
}
```

