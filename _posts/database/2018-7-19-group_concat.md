---
layout: post
category : SQL
tagline: group_concat
tags : [GROUP_CONCAT,MySQL]
---
{% include JB/setup %}

### group_concat可以把多行内容合并成一列

```
select a.*, concat("[", group_concat(b.name separater ','), "]") from table_a a
left join table_b b on a.id = b.a_id
```

### 注意点

- 有和外表关联时，多条记录也会变成只有一条，这时要注意sql里group by a.id或者其他主表的唯一值
- 当和多张外表关联时，因为笛卡尔积会产生多条重复记录，可以在group_concat里使用distinct去重

```
group_concat(distinct b.name separater ','),
group_concat(distinct c.other_name separater ',')
```