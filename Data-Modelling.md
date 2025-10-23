# Comprehensive Data Modeling Interview Questions by Topic

## Fundamentals of Data Modeling

1. What is data modeling and why is it important in data engineering?
2. What are the three types of data models (conceptual, logical, and physical)?
3. What is the difference between a conceptual, logical, and physical data model?
4. What is a table and what are its key components?
5. What is an entity and what is an attribute?
6. What is a primary key and why is it important?
7. What is a foreign key and how does it establish relationships?
8. What is a composite key?
9. What is a surrogate key and when would you use it?
10. What is a natural key vs. a surrogate key?

## Normalization and Denormalization

1. What is normalization and why is it used?
2. Explain the different normal forms (1NF, 2NF, 3NF, BCNF)?
3. What is First Normal Form (1NF)?
4. What is Second Normal Form (2NF)?
5. What is Third Normal Form (3NF)?
6. What is Boyce-Codd Normal Form (BCNF)?
7. What is denormalization and when would you use it?
8. What are the advantages and disadvantages of normalization?
9. What are the advantages and disadvantages of denormalization?
10. How do you decide between normalization and denormalization for a project?

## Entity-Relationship (ER) Modeling

1. What is an Entity-Relationship (ER) model?
2. What are the main components of an ER model?
3. What is an entity in ER modeling?
4. What is a relationship in ER modeling?
5. What are the different types of relationships (one-to-one, one-to-many, many-to-many)?
6. How do you represent a one-to-one relationship?
7. How do you represent a one-to-many relationship?
8. How do you represent a many-to-many relationship?
9. What is cardinality in ER modeling?
10. What is an associative entity or junction table?
11. How do you handle a many-to-many relationship in a database?
12. What is a weak entity and strong entity?
13. How do you use UML in data modeling?

## Dimensional Modeling

1. What is dimensional modeling?
2. What is the difference between OLTP and OLAP systems?
3. What is a fact table?
4. What is a dimension table?
5. What is the difference between a fact table and a dimension table?
6. What is a star schema?
7. What is a snowflake schema?
8. What is the difference between a star schema and a snowflake schema?
9. What is a galaxy schema (fact constellation)?
10. What are measures in a fact table?
11. What are additive, semi-additive, and non-additive measures?
12. What is a degenerate dimension?
13. What is a conformed dimension?
14. What is a junk dimension?
15. What is a role-playing dimension?

## Slowly Changing Dimensions (SCD)

1. What are Slowly Changing Dimensions (SCD)?
2. What is SCD Type 0?
3. What is SCD Type 1 and when would you use it?
4. What is SCD Type 2 and when would you use it?
5. What is SCD Type 3 and when would you use it?
6. How do you implement SCD Type 2 in practice?
7. What are the key columns needed for SCD Type 2?
8. What are the advantages and disadvantages of each SCD type?
9. How do you handle slowly changing dimensions in a fact table?

## Data Warehouse Modeling

1. What is a data warehouse?
2. What is the difference between a database and a data warehouse?
3. What is ETL in the context of data warehousing?
4. What is ELT and how does it differ from ETL?
5. What is a data mart?
6. What is the difference between a data warehouse and a data mart?
7. What is the Kimball methodology?
8. What is the Inmon methodology?
9. What is the difference between Kimball and Inmon approaches?
10. What is a staging area in data warehousing?
11. What is an operational data store (ODS)?

## Data Vault Modeling

1. What is Data Vault modeling?
2. What are the main components of Data Vault (Hub, Link, Satellite)?
3. What is a Hub in Data Vault?
4. What is a Link in Data Vault?
5. What is a Satellite in Data Vault?
6. What are the advantages of Data Vault modeling?
7. When would you choose Data Vault over other modeling techniques?
8. How does Data Vault support historical tracking?

## Advanced Data Modeling Concepts

1. What is data redundancy and how do you minimize it?
2. What is referential integrity?
3. What is a view in a database?
4. What is a materialized view and how does it differ from a regular view?
5. What is partitioning in data modeling?
6. What is sharding and when would you use it?
7. How do you model hierarchical data in a relational database?
8. How do you model time-dependent or historical data?
9. What is a bridge table?
10. What is a data model schema?
11. What is the difference between a database schema and a database instance?
12. What is metadata and why is it important?

## Data Modeling for Specific Technologies

1. What are the key considerations for data modeling in Snowflake?
2. How does data modeling differ in NoSQL databases vs relational databases?
3. What is columnar storage and how does it impact data modeling?
4. How do you model data for a data lake?
5. What is the difference between data modeling for RDBMS and cloud data warehouses?
6. How do you approach data modeling in Amazon Redshift?
7. What are distribution keys and sort keys in Redshift?

## Data Modeling Process and Best Practices

1. What are the different phases of a data modeling development cycle?
2. How do you gather requirements from business users for data modeling?
3. How do you start the data modeling process given a set of business requirements?
4. How do you validate your data models?
5. How do you approach the documentation of your data models?
6. What tools do you use for data modeling?
7. How do you maintain data quality and accuracy in your models?
8. How do you handle conflicting business requirements?
9. What is forward engineering in data modeling?
10. What is reverse engineering in data modeling?
11. How do you optimize a data model for performance?
12. How do you approach migrating data from an old system to a new model?

## Scenario-Based and Design Questions

1. Design a data model for an e-commerce platform (orders, customers, products)?
2. Design a data model for a ride-sharing application like Uber?
3. Design a data model for a food delivery application?
4. Design a data model for a social media platform?
5. Design a data model for a library management system?
6. How would you model data for a subscription-based service?
7. How would you model data for tracking user events and analytics?
8. Design a data model to track inventory across multiple warehouses?
9. How would you integrate data from multiple sources in your data model?
10. Describe a challenging data modeling problem you faced and how you resolved it?
11. Describe a situation where you refactored an existing data model?
12. How have you used data modeling to solve a business problem?
13. Provide an example of how you optimized a data model for performance?
14. Describe your process for migrating data models from one database system to another?

## SQL and Query-Related Questions

1. How do you write efficient queries against normalized vs denormalized data?
2. What indexes would you create to optimize query performance?
3. How do you handle NULL values in your data model?
4. What are constraints and what types are commonly used?
5. What is the difference between TRUNCATE, DELETE, and DROP?
6. How do you implement data integrity in your models?

Sources
1. [Top Data Modelling Interview Questions (2025)](https://www.interviewbit.com/data-modelling-interview-questions/)
2. [How I Mastered Data Modeling Interviews](https://www.youtube.com/watch?v=IUK0PmQDrqM)
3. [What Data Modeling Questions Have You Encountered in ...](https://www.reddit.com/r/dataengineering/comments/1ivgg7f/what_data_modeling_questions_have_you_encountered/)
4. [Top 24 Data Modeling Interview Question and Answers for ...](https://www.simplilearn.com/data-modeling-interview-question-and-answers-article)
5. [Interview Questions And Answers For A Data Modeller](https://in.indeed.com/career-advice/interviewing/data-modeller-interview-questions)
6. [5 Data Modelling Interview Questions (With Sample Answers)](https://in.indeed.com/career-advice/interviewing/data-modeling-interview-questions)
7. [100 Data Modelling Interview Questions To Prepare For In ...](https://www.projectpro.io/article/data-modeling-interview-questions-and-answers/597)
8. [90+ Data Science Interview Questions and Answers for 2025](https://www.simplilearn.com/tutorials/data-science-tutorial/data-science-interview-questions)
9. [50 data modeling interview questions (+ answers)](https://www.testgorilla.com/blog/data-modeling-interview-questions/)
10. [The Top 39 Data Engineering Interview Questions and ...](https://www.datacamp.com/blog/top-21-data-engineering-interview-questions-and-answers)
11. [Data Modeling Questions Course](https://www.tryexponent.com/courses/data-modeling-interviews)
12. [Top 90+ Data Engineer Interview Questions and Answers](https://www.netcomlearning.com/blog/data-engineer-interview-questions)
13. [14 Data Engineer Interview Questions and How to Answer ...](https://www.coursera.org/in/articles/data-engineer-interview-questions)
14. [Interview Questions & Answers](https://www.ctanujit.org/uploads/2/5/3/9/25393293/data_engineering_interviews.pdf)
15. [Master Data Engineer Interview Questions: Solve Duplicate ...](https://www.youtube.com/watch?v=YyJEIRqhx.)