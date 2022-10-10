---

title: 实现上传文件到OSS

date: 2022-10-9 

description: "OSS是阿里的存储服务"

categories: 

- 开发

tags: 

- java
- springboot
- 文件上传

---

# OSS实现文件上传

`oss`就是阿里的一个分布式存储服务。

> 分布式就是一份文件多个地方保存，防止文件损坏或丢失

<!--more-->

## Springboot整合OSS

### 1、创建一个阿里云账户

### 2、开通OSS对象存储服务

### 3、创建Bucket

### 4、创建阿里访问密钥

### 5、导入java相关依赖

> 上传的方式是以IO流的形式传输的

```xml
    <dependency>
            <groupId>com.aliyun.oss</groupId>
            <artifactId>aliyun-sdk-oss</artifactId>
            <version>3.15.0</version>
        </dependency>
```



### 6、上传文件

```java
package com.lzj.service;

import com.aliyun.oss.ClientException;
import com.aliyun.oss.OSS;
import com.aliyun.oss.OSSClientBuilder;
import com.aliyun.oss.OSSException;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

/**
 * @author ：mmzs
 * @date ：Created in 2022/9/21 9:51
 * @description：oss测试
 * @modified By：
 * @version: $
 */
@Service
public class OssService {
    public Map<String,Object> upload(MultipartFile multipartFile){
        Map<String,Object> photoMap=new HashMap<>();
        // Endpoint以华东1（杭州）为例，其它Region请按实际情况填写。
        String endpoint = "oss-cn-hangzhou.aliyuncs.com";
        // 阿里云账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM用户进行API访问或日常运维，请登录RAM控制台创建RAM用户。
        String accessKeyId = "LTAI5t6sJxv93p8qfrhR7c5T";
        String accessKeySecret = "Cw3vcg0xl1Yp4toHNvslLPD7m2gOqS";
        // 填写Bucket名称，例如examplebucket。
        String bucketName = "lzj-bucket";
        // 填写Object完整路径，例如exampledir/exampleobject.txt。Object完整路径中不能包含Bucket名称。
//        String objectName = "blog/test.txt";

        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

        try {
            InputStream inputStream = multipartFile.getInputStream();
            //获取文件名称
            String originalFilename = multipartFile.getOriginalFilename();
//            获取文件名后缀
            String substring = originalFilename.substring(originalFilename.lastIndexOf("."));
            //uuid组合
            String uuid= UUID.randomUUID().toString();
//            新文件名称
            String newFileName=uuid+substring;
//            生成日期
            SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy/MM/dd");
            String format = simpleDateFormat.format(new Date());
//            生成路径
            String fileUrl=format+"/"+newFileName;
//            上传到oss服务中去
            ossClient.putObject(bucketName,fileUrl,inputStream);
            photoMap.put("url","https://"+bucketName+"."+endpoint+"/"+fileUrl);
            System.out.println("https://"+bucketName+"."+endpoint+"/"+fileUrl);

            return photoMap;




//            String content = "Hello OSS";
        } catch (Exception oe) {
            System.out.println(oe);
            photoMap.put("error","上传失败");
            return photoMap;
        } finally {
            if (ossClient != null) {
                ossClient.shutdown();
            }
        }
    }

}

```

