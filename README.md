# Apache-Hive-Practicals
Documentation which displays several Hive queries

HIVE – CASE – STUDY
ANALYSIS FOR CLICK STREAM DATA
We are now dealing with a data set where web sites of several countries in the world are included. Our Primary task is to store this data on Hadoop and Compute it.
Environment Setup:
1. If you’re using Cloudera or HDP sand box, it easy to get this data set, if you’re using any cloud or third party lab environment push your dataset by using putty or WinSCP.
2. Push your data set to HDFS by using: hdfs dfs –put dataset.
3. As it is a JSON file, download the hive SerDe from:  http://files.cloudera.com/samples/hive-serdes-1.0-SNAPSHOT.jar  and make sure you move the file to hdfs
4. In hive add your SerDe by a command
  Add jar hive-serdes-1.0-SNAPSHOT.jar;
5. Once you’re done with these prior steps you are ready to play with your data set in Hive.

LET’S CREATE A DATABASE AND LOAD OUR DATA IN HIVE TABLES
1. Create a Hive table for our JSON data format.
ADD JAR <localpath where hive json serde jar is located>
CREATE TABLE clickstr (a string, c string, nk int, tz string, gr string, g string, h string,l string, hh string, r string, u string, t int, hc int, cy string, al string) ROW FORMAT SERDE 'com.cloudera.hive.serde.JSONSerDe';

 
2. Load your Json data into the table you created.
LOAD DATA LOCAL INPATH '/home/user/UsaGovData.txt' OVERWRITE INTO TABLE clickstr;
 
3. Perform Dynamic partition on the above table for query tuning and load the data into it.
 
4. Now Enable Dynamic partition and load the data into it.
hive> SET hive.exec.dynamic.partition=true
hive> set hive.exec.dynamic.partition.mode=nonstrict
hive> INSERT OVERWRITE TABLE clickstrpart PARTITION(c) SELECT a,nk,g,h,l,hh,r,u,t,gr,cy,tz,hc,al,c FROM clickstr;
 
LET’S PERFORM QUERIES:
PLEASE TAKE A LOOK INTO THE JSON SCHEMA VIZ THE COLUMNS WE HAVE:
{
"a": USER_AGENT,
"c": COUNTRY_CODE, # 2-character iso code
"nk": KNOWN_USER, # 1 or 0. 0=this is the first time we've seen this browser
"g": GLOBAL_BITLY_HASH,
"h": ENCODING_USER_BITLY_HASH,
"l": ENCODING_USER_LOGIN,
"hh": SHORT_URL_CNAME,
"r": REFERRING_URL,
"u": LONG_URL,
"t": TIMESTAMP,
"gr": GEO_REGION,
"ll": [LATITUDE, LONGITUDE],
"cy": GEO_CITY_NAME,
"tz": TIMEZONE # in http://en.wikipedia.org/wiki/Zoneinfo format
"hc": TIMESTAMP OF TIME HASH WAS CREATED,
"al": ACCEPT_LANGUAGE http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4
}

1. Write a hive query to find the agents based on the country code.
Ans: select count (a) as no_of_agents, c from clickstrpart GROUP BY c; 
 

2. Write a query to find out the partitions.
Ans: show partition clickstrpart;  

3. Write a query to find the users as per ENCODING_USER_LOGIN.
Ans: select count(*) from clickstrpart;  

4. Write a Query to find the users as per their language.
Ans: select count(a) from clickstrpart where al = "en-us";

5. Write a query to find the users as per their time zone 
Ans: select count(a) from clickstrpart where tz = '/America\/Sao_paulo';

6. Write a query to group by the first view and latest view of web sites done by the user.
Ans: select count(a) as no_of_agents , c,nk from clickstrpart GROUP BY c,nk; 

7. Write a query to find out 10 popular sites in each country by considering LONG_URL ‘u’.
Ans: SELECT u,count(*) as cnt from clickstrpart GROUP BY u ORDER BY cnt DESC LIMIT 10; 

8.  Write a query to find out 10 popular sites in each country by considering both LONG_URL ‘u’ and Country code ‘c’.
Ans: SELECT c,u,count(*) as cnt from clickstrpart GROUP BY c,u ORDER BY cnt DESC LIMIT 10;





