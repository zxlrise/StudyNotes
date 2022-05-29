# SQL练习

## SQL必知必会

### [SQL1  从 Customers 表中检索所有的 ID](https://www.nowcoder.com/practice/009199576d094b56807a8368058841ee?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)  

**思路**

简单查询

**sql语句**           

```sql
select cust_id from Customers
```

###  [SQL2  检索并列出已订购产品的清单](https://www.nowcoder.com/practice/9e4741b77f4244149a069883bc0d23be?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)          

**思路**

使用`distinct`简单去重

**sql语句**

```sql
select distinct prod_id from OrderItems
```

### [SQL3  检索所有列](https://www.nowcoder.com/practice/cf0e3919ba8e4fa2ba19ea09df7fb756?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

查询全表 

**sql语句**

```sql
select * from Customers;
```

### [SQL4  检索顾客名称并且排序](https://www.nowcoder.com/practice/6cfabb1b49554c4c8d8f9977bf6a3a5d?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

排序

**sql语句**

```sql
select cust_name from Customers order by cust_name desc;
```

### [SQL5 对顾客ID和日期排序](https://www.nowcoder.com/practice/fa4eb4880d124a4ead7a9b025fe75b70?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

先按顾客 ID 对结果进行排序，再按订单日期倒序排列

**sql语句**

```sql
select cust_id, order_num
from Orders
order by cust_id, order_date desc
```

### [SQL6 按照数量和价格排序](https://www.nowcoder.com/practice/bd05a6684e534bd1bf2d9ebbda475333?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

按数量由多到少、价格由高到低排序。 

**sql语句**

```sql
select quantity,item_price
from OrderItems
order by quantity desc,item_price desc
```

### [SQL7 检查SQL语](https://www.nowcoder.com/practice/ba2d42708239429e870fa80db81c07da?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

order by

**sql语句**

```sql
select vend_name
from Vendors
order by vend_name desc
```

### [SQL8  返回固定价格的产品](https://www.nowcoder.com/practice/9949bfb933614abe8bd2bc26c129843e?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

从 Products 表中检索产品 ID（prod_id）和产品名称（prod_name），只返回价格为 9.49 美元的产品。

**sql语句**

```sql
select prod_id,prod_name
from Products
where prod_price=9.49;
```

### [SQL9 返回更高价格的产品](https://www.nowcoder.com/practice/f6153be7485448cdb444279dcc105cb8?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路** 

**sql语句**

```sql
select prod_id,prod_name
from Products
where prod_price>=9;
```

### [SQL10 返回产品并且按照价格排序](https://www.nowcoder.com/practice/560c94bf434e4e77911982e2d7ca0abb?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路** 

**sql语句**

```sql
select prod_name, prod_price
from Products
where prod_price >= 3 and prod_price <= 6
order by prod_price;
```

### [SQL11 返回更多的产品](https://www.nowcoder.com/practice/dc91b7d2de3c4603a55995e83210f605?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

**sql语句**

```sql
select distinct order_num
from OrderItems
where quantity >= 100;
```

### [SQL12 检索供应商名称](https://www.nowcoder.com/practice/c4d520ed6a264ad3900eff95e4195d59?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

**sql语句**

```sql
select vend_name
from Vendors
where vend_country="USA" and vend_state="CA"
```

### [SQL13 检索并列出已订购产品的清单](https://www.nowcoder.com/practice/674d99a46a96494d8267ae4d162ed459?tpId=298&tags=&title=&difficulty=0&judgeStatus=0&rp=0&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

数量满足大于等于100，且满足prod_id 是"BR01"，“BR02”，“BR03"中的任意一个。 

**sql语句**

```sql
select order_num,prod_id,quantity
from OrderItems
where quantity >= 100 and prod_id in ('BR01','BR02','BR03');
```

### [SQL14 返回所有价格在 3美元到 6美元之间的产品的名称和价格](https://www.nowcoder.com/practice/e4268b4e044e4b94875c238098d98cf8?tpId=298&tqId=2368041&ru=/exam/oj&qru=/ta/sql-teach-yourself/question-ranking&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%25E7%25AF%2587%26topicId%3D298)

**思路**

**sql语句**

```sql
select prod_name, prod_price
from Products
where prod_price >= 3 and prod_price <= 6
order by prod_price asc;
```

