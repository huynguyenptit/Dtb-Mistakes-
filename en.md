## What are common database development mistakes made by application developers?

_source https://stackoverflow.com/questions/621884/database-development-mistakes-made-by-application-developers_

**1. Not using appropriate indices**

This is a relatively easy one but still it happens all the time. Foreign keys should have indexes on them. If you're using a field in a 

WHERE you should (probably) have an index on it. Such indexes should often cover multiple columns based on the queries you need to execute.

**2. Not enforcing referential integrity**

Your database may vary here but if your database supports referential integrity--meaning that all foreign keys are guaranteed to point to an entity that exists--you should be using it.

It's quite common to see this failure on MySQL databases. I don't believe MyISAM supports it. InnoDB does. You'll find people who are using MyISAM or those that are using InnoDB but aren't using it anyway.

More here:

- [How important are constraints like NOT NULL and FOREIGN KEY if I’ll always control my database input with php?](https://stackoverflow.com/questions/382309/how-important-are-constraints-like-not-null-and-foreign-key-if-ill-always-contr)
- [Are foreign keys really necessary in a database design?](https://stackoverflow.com/questions/18717/are-foreign-keys-really-necessary-in-a-database-design)
- [Are foreign keys really necessary in a database design?](http://www.diovo.com/2008/08/are-foreign-keys-really-necessary-in-a-database-design/)

**3. Using natural rather than surrogate (technical) primary keys**

Natural keys are keys based on externally meaningful data that is (ostensibly) unique. Common examples are product codes, two-letter state codes (US), social security numbers and so on. Surrogate or technical primary keys are those that have absolutely no meaning outside the system. They are invented purely for identifying the entity and are typically auto-incrementing fields (SQL Server, MySQL, others) or sequences (most notably Oracle).

In my opinion you should **always** use surrogate keys. This issue has come up in these questions:

- [How do you like your primary keys?](https://stackoverflow.com/questions/404040/how-do-you-like-your-primary-keys)
- [What's the best practice for primary keys in tables?](https://stackoverflow.com/questions/337503/whats-the-best-practice-for-primary-keys-in-tables)
- [Which format of primary key would you use in this situation.](https://stackoverflow.com/questions/506164/which-format-of-primary-key-would-you-use-in-this-situation)
- [Surrogate vs. natural/business keys](https://stackoverflow.com/questions/63090/surrogate-vs-natural-business-keys)
- [Should I have a dedicated primary key field?](https://stackoverflow.com/questions/166750/should-i-have-a-dedicated-primary-key-field)

This is a somewhat controversial topic on which you won't get universal agreement. While you may find some people, who think natural keys are in some situations OK, you won't find any criticism of surrogate keys other than being arguably unnecessary. That's quite a small downside if you ask me.

Remember, even [countries can cease to exist](http://en.wikipedia.org/wiki/ISO_3166-1) (for example, Yugoslavia).

**4. Writing queries that require 

DISTINCT to work**

You often see this in ORM-generated queries. Look at the log output from Hibernate and you'll see all the queries begin with:

SELECT DISTINCT ...

This is a bit of a shortcut to ensuring you don't return duplicate rows and thus get duplicate objects. You'll sometimes see people doing this as well. If you see it too much it's a real red flag. Not that 

DISTINCT is bad or doesn't have valid applications. It does (on both counts) but it's not a surrogate or a stopgap for writing correct queries.

From [Why I Hate DISTINCT](http://weblogs.sqlteam.com/markc/archive/2008/11/11/60752.aspx):

> Where things start to go sour in my opinion is when a developer is building substantial query, joining tables together, and all of a sudden he realizes that it **looks** like he is getting duplicate (or even more) rows and his immediate response...his "solution" to this "problem" is to throw on the DISTINCT keyword and **POOF** all his troubles go away.

**5. Favouring aggregation over joins**

Another common mistake by database application developers is to not realize how much more expensive aggregation (ie the 

GROUP BY clause) can be compared to joins.

To give you an idea of how widespread this is, I've written on this topic several times here and been downvoted a lot for it. For example:

From [SQL statement - “join” vs “group by and having”](https://stackoverflow.com/questions/477006/sql-statement-join-vs-group-by-and-having/477013#477013):

> First query:

SELECT userid
FROM userrole
WHERE roleid IN (1, 2, 3)
GROUP by userid
HAVING COUNT(1) = 3

> Query time: 0.312 s

> Second query:

SELECT t1.userid
FROM userrole t1
JOIN userrole t2 ON t1.userid = t2.userid AND t2.roleid = 2
JOIN userrole t3 ON t2.userid = t3.userid AND t3.roleid = 3
AND t1.roleid = 1

> Query time: 0.016 s

> That's right. The join version I proposed is **twenty times faster than the aggregate version.**

**6. Not simplifying complex queries through views**

Not all database vendors support views but for those that do, they can greatly simplify queries if used judiciously. For example, on one project I used a [generic Party model](http://www.tdan.com/view-articles/5014/) for CRM. This is an extremely powerful and flexible modelling technique but can lead to many joins. In this model there were:

- **Party**: people and organisations;
- **Party Role**: things those parties did, for example Employee and Employer;
- **Party Role Relationship**: how those roles related to each other.

Example:

- Ted is a Person, being a subtype of Party;
- Ted has many roles, one of which is Employee;
- Intel is an organisation, being a subtype of a Party;
- Intel has many roles, one of which is Employer;
- Intel employs Ted, meaning there is a relationship between their respective roles.

So there are five tables joined to link Ted to his employer. You assume all employees are Persons (not organisations) and provide this helper view:

CREATE VIEW vw_employee AS
SELECT p.title, p.given_names, p.surname, p.date_of_birth, p2.party_name employer_name
FROM person p
JOIN party py ON py.id = p.id
JOIN party_role child ON p.id = child.party_id
JOIN party_role_relationship prr ON child.id = prr.child_id AND prr.type = 'EMPLOYMENT'
JOIN party_role parent ON parent.id = prr.parent_id = parent.id
JOIN party p2 ON parent.party_id = p2.id

And suddenly you have a very simple view of the data you want but on a highly flexible data model.

**7. Not sanitizing input**

This is a huge one. Now I like PHP but if you don't know what you're doing it's really easy to create sites vulnerable to attack. Nothing sums it up better than the [story of little Bobby Tables](http://xkcd.com/327/).

Data provided by the user by way of URLs, form data **and cookies** should always be treated as hostile and sanitized. Make sure you're getting what you expect.

**8. Not using prepared statements**

Prepared statements are when you compile a query minus the data used in inserts, updates and 

WHERE clauses and then supply that later. For example:

SELECT * FROM users WHERE username = 'bob'

vs

SELECT * FROM users WHERE username = ?

or

SELECT * FROM users WHERE username = :username

depending on your platform.

I've seen databases brought to their knees by doing this. Basically, each time any modern database encounters a new query it has to compile it. If it encounters a query it's seen before, you're giving the database the opportunity to cache the compiled query and the execution plan. By doing the query a lot you're giving the database the opportunity to figure that out and optimize accordingly (for example, by pinning the compiled query in memory).

Using prepared statements will also give you meaningful statistics about how often certain queries are used.

Prepared statements will also better protect you against SQL injection attacks.

