## SQL 语句例题及解析

#### Q1：查找最晚入职员工的所有信息

解析：要查找最晚入职的员工，所以按入职日期进行排序，由于只需要查找一人，所以使用 limit 限制结果为 1 条记录。

```sql
select * from employees
order by hire_date desc
limit 1
```

---

#### Q2：查找入职员工时间排名倒数第三的员工所有信息

解析：使用 limit m n 表示从第 m+1 条记录开始取 n 条，因此 limit 2,1 表示取第三条记录。

```sql
select * from employees
order by hire_date desc
limit 2,1
```

---

