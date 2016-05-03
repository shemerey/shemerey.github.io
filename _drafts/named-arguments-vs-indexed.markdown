---
layout: "post"
title: "Named arguments vs Indexed"
date: "2016-05-03 13:41"
---

There is two ways of using arguments both has pros and cons, I want to emphasize
that there is no "silver bluet" and you don't have to choose one or another.  You
have to know both and use both whenever it appropriate.

## Indexed arguments

Some application, languages, and even syntax contractions force you to use index
version of arguments, they all well known and don't worth to mention, but what
is really interesting that some times we can use it in unexpected places.

For instance in Postgres, each of the arguments you specify in a `select` has a
1-based index and you can use these indexes in the `order by` as well as `group
by` statements.

Instead of writing

```sql
select id, updated_at from posts order by updated_at;
```

you can write

```sql
select id, updated_at from posts order by 2;
```

If you want to group by a table's `type` and then order by the counts from
highest to lowest, you can do the following

```sql
select type, count(*) from transaction group by 1 order by 2 desc;
```
