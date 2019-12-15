title: Mybatis-JDBCType与javaType映射关系
tags:
  - Mybatis
  - java
categories:
  - 技术
thumbnail: '/images/thumbnails/mybatis.jpg'
cc: true
comments: true
date: 2017-07-13 18:31:00
original : false

---

留备便查

 JDBCType  | JavaType
 -------- | ---------
 CHAR | String
 VARCHAR | String
 LONGVARCHAR | String
 NUMERIC | java.math.BigDecimal
 DECIMAL | java.math.BigDecimal
 BIT | boolean
 BOOLEAN | boolean
 TINYINT | byte
 SMALLINT | short
 INTEGER | int
 BIGINT | long
 REAL | float
 FLOAT | double
 DOUBLE | double
 BINARY | byte[]
 VARBINARY | byte[]
 LONGVARBINARY | byte[]
 DATE | java.sql.Date
 TIME | java.sql.Time
 TIMESTAMP | java.sql.Timestamp
 DATETIME | java.sql.Timestamp
 CLOB | Clob
 BLOB | Blob
 DATALINK | java.net.URL
