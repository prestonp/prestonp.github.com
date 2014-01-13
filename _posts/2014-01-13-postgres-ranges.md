---
layout: post
title: Calendar scheduling with Postgres date ranges
categories: posts
---

I've been working on writing a small calendar widget for scheduling holidays at Goodybag.com. 
For this particular widget, we want the ability to mark days when our partnered restaurants have 
time off or close up for a holiday. During these _events_, as I call them, our users will be prompted with
a friendly warning that the caterers are unable to fulfill an order. Cool.

To put it all together, I've worked out a solution using the [FullCalendar](http://arshaw.com/fullcalendar/) 
plugin and storing dates in PostgreSQL as `date_range`s. As fortune has it, Postgres 9.2+ supports ranges 
which makes scheduling a whole lot easier! Previously, you would need to store start and end dates 
separately and do all sorts of querying against these two different fields. With range data types, 
there's a lot of neat functionality baked into postgres that simplifies this.

**Data types:**

```
int4range — Range of integer

int8range — Range of bigint

numrange — Range of numeric

tsrange — Range of timestamp without time zone

tstzrange — Range of timestamp with time zone

daterange — Range of date
```

Here's a simple use case for checking if a date takes place during an event.

```sql
CREATE TABLE restaurnat_events (restaurant_id int, during daterange);
INSERT INTO restaurant_events VALUES
    (14, '[2014-01-01, 2014-01-10)');
```

Using the `@>` (contains) syntax

```sql
SELECT restaurant_events.during @> date(now()) as in_range;

# Returns

 in_range 
----------
 f

(1 row)
```

One gotcha for Postgres is that the built in data type ranges are stored and 
returned in the canonical form **[start, end)**, meaning the lower bound is 
inclusive and the upper bound is exclusive. You can insert and update ranges 
with any variation on bounds: 

 * (start, end)
 * (start, end]
 * [start, end]
 * [start, end)
 * empty

Note that ranges like '[3,3)::int4range' will implicitly be converted to `empty`

```sql
SELECT isempty('[3,3)'::int4range);

 isempty 
---------
 t
(1 row)
```

Another useful feature is representing infinity, simply by omitting either of the bounds.
Use `lower_inf` and `upper_inf` functions to check the bounds for infinity.

```sql
SELECT upper_inf('[,)'::int4range);

 upper_inf 
-----------
 t
```

With the FullCalendar plugin, selected ranges are fully inclusive [start, end]. Be sure to
convert postgres' canonical format! 

Read more at http://www.postgresql.org/docs/9.3/static/rangetypes.html
