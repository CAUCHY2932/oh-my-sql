# mysql的50道练习题

自学MySQL一段时间了，仅仅是看书，总有一种“纸上得来终觉浅”的感觉，所以花了几天时间，把这50道经典题目，从头到尾做了一遍，实际上是做了3遍，第一遍完全自己做，第二遍对答案，当然对答案的过程参考了很多网友的分享(具体链接见文末)，第三遍就是现在，所以以下代码运行基本没问题。

MySQL：Mac版-8.0.14

参考链接：https://zhuanlan.zhihu.com/p/57317210

## 建表语句

### student

```mysql
create table Student(S varchar(10),Sname varchar(10),Sage datetime,Ssex varchar(10)); 
insert into Student values('01' , '赵雷' , '1990-01-01' , '男'); 
insert into Student values('02' , '钱电' , '1990-12-21' , '男'); 
insert into Student values('03' , '孙风' , '1990-05-20' , '男'); 
insert into Student values('04' , '李云' , '1990-08-06' , '男'); 
insert into Student values('05' , '周梅' , '1991-12-01' , '女'); 
insert into Student values('06' , '吴兰' , '1992-03-01' , '女'); 
insert into Student values('07' , '郑竹' , '1989-07-01' , '女'); 
insert into Student values('08' , '王菊' , '1990-01-20' , '女'); 
```

### Course

```mysql
create table Course(C varchar(10),Cname varchar(10),T varchar(10)); 
insert into Course values('01' , '语文' , '02'); 
insert into Course values('02' , '数学' , '01'); 
insert into Course values('03' , '英语' , '03'); 
```

### Teacher

```mysql
create table Teacher(T varchar(10),Tname varchar(10)); 
insert into Teacher values('01' , '张三'); 
insert into Teacher values('02' , '李四'); 
insert into Teacher values('03' , '王五'); 
```

### Scores

```mysql
create table SC(S varchar(10),C varchar(10),score decimal(18,1)); 
insert into SC values('01' , '01' , 80); 
insert into SC values('01' , '02' , 90); 
insert into SC values('01' , '03' , 99); 
insert into SC values('02' , '01' , 70); 
insert into SC values('02' , '02' , 60); 
insert into SC values('02' , '03' , 80); 
insert into SC values('03' , '01' , 80); 
insert into SC values('03' , '02' , 80); 
insert into SC values('03' , '03' , 80); 
insert into SC values('04' , '01' , 50); 
insert into SC values('04' , '02' , 30); 
insert into SC values('04' , '03' , 20); 
insert into SC values('05' , '01' , 76); 
insert into SC values('05' , '02' , 87); 
insert into SC values('06' , '01' , 31); 
insert into SC values('06' , '03' , 34); 
insert into SC values('07' , '02' , 89); 
insert into SC values('07' , '03' , 98); 
```

### 查看数据

```mysql
select * from Student; 
select * from Course; 
select * from Teacher; 
select * from SC; 
```

## 题目

### 1. 查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数

思路：1）某学生同时学了 01 和 02 课程2）某学生只学习了01课程，02 成绩为null

```mysql
1.子查询
select t1.S, s.Sname, t1.c1,t2.c2 from 
	(select S, score as c1 from SC where C = '01')t1 
left join 
	(select S, score as c2 from SC where C = '02') t2 
on t1.S=t2.S
join
Student s
on t1.S=s.S
where t1.c1 > t2.c2 or t2.c2 is null; 
2.自联结
select distinct sc2.S, s.Sname, sc2.C, sc2.score as c1, sc3.score as c2 from SC sc1 
join SC sc2 on sc1.S=sc2.S and sc2.C='01'
left join SC sc3 on sc1.S = sc3.S and sc3.C='02'
join Student s
on s.S=sc2.S
where sc2.score > sc3.score or sc3.score is null;
```

#### 1.1.查询同时选修01和02课程的情况

```mysql
#和上一题的思路类似
select c1.S,s.Sname,c1.C cor1,c1.score sco1,c2.C cor2,c2.score sco2 
from 
	(select * from SC where C=01) c1,
	(select * from SC where C = 02) c2,
	Student s
where c1.S= c2.S and s.S = c1.S;
#或者
select distinct sc2.S,s.Sname,sc2.C,sc2.score,sc3.C,sc3.score 
from SC sc1 
join SC sc2 on sc1.S = sc2.S and sc2.C = 01
join SC sc3 on sc2.S = sc3.S and sc3.C = 02
join Student s on sc3.S = s.S;
```

#### 1.2查询存在" 01 "课程但可能不存在" 02 "课程的情况

```mysql
select c1.S,c1.C cor1,c1.score sco1,c2.C cor2,c2.score scor2 from 
(select * from SC where C = 01)c1 
left join
(select * from SC where C = 02)c2
on  c1.S=c2.S ;
#或者
select distinct sc2.S, s.Sname,sc2.C,sc2.score,sc3.C,sc3.score 
from SC sc1 
join SC sc2 on sc1.S = sc2.S and sc2.C = 01
left join SC sc3 on sc3.S = sc2.S and sc3.C = 02
join Student s on sc1.S = s.S
```

#### 1.3查询不存在" 01 "课程但存在" 02 "课程的情况

```mysql
#not in 
select * from SC where S not in
	(select S from SC where C = 01)  and C =02  ; 
```

### 2.查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩

```mysql
select t.S,s.Sname, t.avg from 
(select S,avg(score) avg from SC 
group by S)t,
Student s
where t.S=s.S and t.avg >= 60;
#或者
select s.S,s.Sname, avg(sc.score) as avg from Student s,SC
where s.S=SC.S
group  by s.S,s.Sname
having avg >=60
```

### 3.查询SC表存在成绩的学生信息

```mysql
#选了课程，但是成绩为null的情况
select s.S,s.Sname,Ssex,sc.C,score from Student s,SC sc
where s.S = sc.S and (sc.S,sc.C) in 
	(select S,C from SC where score is not null);
#或者
select s.S,s.Sname,Ssex,sc.C,score from Student s,SC sc
where s.S = sc.S and score is not null
```

### 4.查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null )

```mysql
#需要考虑一门也没有选的学生的学生信息，选课但是没成绩的学生的信息
select s.S,s.Sname,Ssex,sc.C,score from
Student s 
left join SC sc
on s.S = sc.S 
where score is null
```

### 5.查询「李」姓老师的数量

```mysql
select count(1) from Teacher 
where Tname like '李%'; 
```

### 6.查询学过「张三」老师授课的同学的信息

### 7.查询没有学全所有课程的同学的信息

### 8.查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息

### 9.查询和" 01 "号的同学学习的课程完全相同的其他同学的信息

### 10. 查询没学过"张三"老师讲授的任一门课程的学生姓名



### 11. 查询有两门课程及其以上不及格同学的学号，姓名及其平均成绩

### 12. 检索" 01 "课程分数小于 60，按分数降序排列的学生信息



### 13. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩?

### 14. 查询各科成绩最高分、最低分和平均分：





### 15.按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺?

### 15.1 按各科成绩进行排序，并显示排名， Score 重复时合并名次

### 16. 查询学生的总成绩，并进行排名，总分重复时保留名次空缺

### 16.1 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺

### 17. 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比

### 18. 查询各科成绩前三名的记录

### 19. 查询每门课程被选修的学生数

### 20. 查询出只选修两门课程的学生学号和姓名

### 21. 查询男生、女生人数

### 22. 查询名字中含有「风」字的学生信息



### 23.查询同名同性学生名单，并统计同名人数

### 24. 查询 1990 年出生的学生名单

### 25. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

### 26. 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩

### 27. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数 （三表联结）

### 28. 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）

### 29. 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数

### 30. 查询不及格的课程

### 31. 查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名

### 32. 求每门课程的学生人数



### 33. 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

### 34. 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩



### 35. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 (同一个学生的 ？）

### 36. 查询每门功成绩最好的前两名

### 37. 统计每门课程的学生选修人数（超过 5 人的课程才统计）。

### 38. 检索至少选修两门课程的学生学号

### 39. 查询选修了全部课程的学生信息(可以参考之前）

### 40. 查询各学生的年龄，只按年份来算

### 41. 按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一（周岁）

```mysql
select S,Sage,timestampdiff(year,Sage,curdate()) from Student; 
```

### 42. 查询本周过生日的学生?

```mysql
select * from Student where week(Sage) = week(now()); 
```

### 43. 查询下周过生日的学生?

```mysql
select * from Student where week(Sage) = week(now())+1; 
select * from Student where week(Sage) = week(date_add(now(),interval 1 week)) 
```

### 44. 查询本月过生日的学生

```mysql
select * from Student where month(Sage) = month(now()); 
```

### 45. 查询下月过生日的学生

```mysql
select * from Student where Month(Sage) = month(now())+1; 
select * from Student where Month(Sage) =month(date_add(now(), interval 1 Month)); 
```

