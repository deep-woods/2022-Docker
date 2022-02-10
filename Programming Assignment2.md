Compare a traditional database, with an analytical database, and a NoSQL database

Compare three examples; each should be drawn from one of the following areas that we discussed:
Databases (a traditional database, an analytical database, NoSQL database)
Statistics Packages (such as SPSS, SAS, R, MiniTab, and MATLAB)
API (including WEKA, Orange, Statistica, and Hadoop)

---

## Traditional database vs analytical database vs NoSQL

Traditional database (SQL): the relational database, the traditional type of databases stores data in table. The structure of relational databases is quite intuitive as always takes the form of data. Each data point (row or record) is stored with a unique identifier called `key`. Columns represent attributes for record entries. When creating a table, the programmer can assign the datatype for each attribute and whether it can be null (empty value) or not. Due to this structrual rigidness, traditional relational databases would require more space for storage, inefficient, hard to maintain and optimise (Oracle, n.d.).

Analytical database: also known as OLAP (Online Analytical Processing), analytical database specialises in big data managmeent for business applications and business intelligence (omni sci, n.d.). Examples of analytical databases include market data, transcational data, sensor data, natural language data, process data, and machine data.

NoSQL database (Not Only SQL): the most remarkable difference that distinguishes NoSQL from traditional SQL is is flexible schema form. NoSQL databases do not implement tables like SQL does not require fixed form for data entries (although it can). Therefore, it is easier to develop and feature high functionality and performance (AWS, n.d.). Types of NoSQL databases include key-value pairs, documents, graphs, in-memory and searches.

<br>

## DataBase, Statistics Packages and API

Database:

- MySQL: MySQL is one of the most popular relational database software along with Oracle and MariaDB. As wide as its popularity, it is highly compatible with other technologies and architectures across the IT landscape. For instance, data can be migrated from MySQL to MariaDB or Oracle and other on-premise or cloud-based storage systems.

- NoSQL: the most trending NoSQL software include MongoDB, DynamoDB, Cassandra, and Couchbase. MongoDB is 'a source-available cross-platform document-oriented database program (Wikipedia, n.d.)'. It supports field, range query, and regular expression searches. Written in C++, JavaScript and Python, it also supports the use of user-defined JavaScript funcions for queries.

Statistics Package: R

- R is a programming language for statistical computing software. First developed by Ross Ihaka and Robert Gentleman, it is a popular language in data mining, statistics and data analysis.

API: Hadoop

- Hadoop is an open-source framework used for big data storage and processing. The core of Hadoop allows for clustering multiple computing machines to distribute big data for safer storage and higher processing velocity and performance. As Hadoop generates (by default) 3 duplicates of datablocks (called `datanodes`) and distribute them across a corresponding number of machines, it is more fault tolerant against unexpected disasters.

<br>

### References

- Oracle (n.d.) What is a Relational Database (RDBMS)? https://www.oracle.com/database/what-is-a-relational-database/
- Omni Sci (n.d.) Analytical Database Definition https://www.omnisci.com/technical-glossary/analytical-database
- AWS (n.d.) What is NoSQL? https://aws.amazon.com/nosql/
- Wikipedia (n.d.) MongoDB https://en.wikipedia.org/wiki/MongoDB
- Wikipedia (n.d.) R (programming language) https://en.wikipedia.org/wiki/R_(programming_language)
- AWS (n.d.) What is Hadoop? https://aws.amazon.com/emr/details/hadoop/what-is-hadoop/
