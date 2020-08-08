---
layout: post
title:  "Mybatis调用Oracle存储过程"
date:   2020-08-08 13:48:09 +0800
categories: 后端
---
框架： spring boot + mybatis + oracle 数据库

报错：
the error occurred while setting parameters

或者
Malformed SQL92 string at position x
 
检查

如果确认在mybatis 的mapper.xml 文件中配置正确的话，那可以试着把<![CDATA[ ]]>，加上即可。
最终mapper 文件内容如下（parameterType = map 这属性即为 java.util.map 存储过程和函数一般都以map方式进行传输，
使用 update 是因为内容涉及DML，如果没有数据变更事务使用select 方式也可以）

例如这里我调用的是 函数：

<update id="saveUser" parameterType="map" statementType="CALLABLE">
<![CDATA[
{ #{res,mode=OUT,jdbcType = VARCHAR} = call sync_pgk.func_sync_user(
#{userId,mode=IN,jdbcType = DECIMAL},
#{enabledFlag ,mode=IN,jdbcType = VARCHAR},
#{masterOrg,mode=IN,jdbcType = VARCHAR} )
}
]]>
</update>

如果是调用的存储过程，无需设置返回值

<update id="saveUser" parameterType="map" statementType="CALLABLE">
<![CDATA[
{ call sync_pgk.func_sync_user(
#{userId,mode=IN,jdbcType = DECIMAL},
#{enabledFlag ,mode=IN,jdbcType = VARCHAR},
#{masterOrg,mode=IN,jdbcType = VARCHAR} )
}
]]>
</update>

DAO文件内方法

void saveUser(Map<String,Object> param);
 
调用后，如果需要res 返回值， 使用param.get("res") 即可。
