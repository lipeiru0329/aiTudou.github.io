---
title: blog1
date: 2020-06-19 09:38:52
tags:
---

## 随手一记 -- 1 MyBatis版本升级回顾及原理分析

---

这个系列就是会记录一下平时的看到的一些问题，有时候会带一些分析，也为了大家互相学习。那么话不多说，我们开始。

---

- 问题：inf-bom版本从1.3.9.6升级至1.4.2.1的时候 类型转换的时候，系统产生了强转错误
- 解析：当升级之后，我们发现MyBatics升级了两个版本。
![](https://mmbiz.qpic.cn/mmbiz_png/hEx03cFgUsX64of1Bwpy9eUT1pGhWjicNY0jZb1UzasVfrb9KqehCrmIXIUEX9VSq46zSPFVjhBqfSgtPn92slA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们发现在3.2.3 及之前的版本之中，会忽略XML中的parameterType这个属性，并且使用真实的变量类型进行值的处理。也就是说即使你在XML 里面的type 写的是错的，MyBatics也会自动帮你修改。
```
int updateResponse(@Param("id")long id, @Param("response")String response, @Param("updateTime")LocalDateTime updateTime);
```
XML:
```
<update id="updateResponse" parameterType="java.lang.String">
UPDATE invoice_log
  SET response = #{response}, update_time = #{updateTime}
WHERE id = #{id}
</update>
```

但是在3.2.4及之后的版本里面，MyBatics 将会用你的XML的type，所以说如果你的type写的不对，就会发生转换错误。

>Release Note:

>An special remark about this feature. Previous versions ignored the "parameterType" attribute and used the actual parameter to calculate bindings. This version builds the binding information during startup and the "parameterType" attribute is used if present (though it is still optional), so in case you had a wrong value for it you will have to change it

