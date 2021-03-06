Reco repo
==========

A Recommendation engine written in database stored procedure



Install instructions
--------------------
Unzip into a web app folder

In mysql, run the installScript.sql to create the database, tables and stored procs
Use the commented lines at the bottom of installScript.sql to load the data from 3 csv files in \scripts

Point apache virtual host to htdocs which should contain getRec.php, index.php and the css

Run by loading the index.php page and entering an email and date and hit the button


Approach
---------
I progressively developed the sql and analyzed performance. After analyzing the files and problem statement I decided to
 use a database and stored procedure and then created tables and scripts to load the data. Once loaded, I could query the data to understand it better. I then worked on the query to retrieve recommendations, improving over iterations. 
 Its certainly not perfect and I believe the query can be improved given more time.

I believe loading the data into database tables and using the power of the database to query the data provides the best 
solution for this problem because:
 - a database provides a good foundation for future growth as more data is added and changed
 - a database provides tools and means to analyze fetch algorithm performance (e.g. explain and profiling)
 - a database allows for easily adding other criteria such as a users personal preferences to include 
    in providing recommendations in the future

I decided to implement the query as a stored procedure to provide best performance and maintainability. It can easily support multiple clients and centralizes the logic. The fetch query can be improved without requiring changes to server code (or other clients).
 
I used a simple php script implemented as a Rest api for serving recommendations. I tried to make as Rest like as possible, with more time there are frameworks to make the urls restful. Please see comments in script.
I created a simple HTML web app to show how to easily use. Excuse the basic layout.
I tested on IE8 and Chrome.



Data Analysis
-------------
I found that supersize@subrational.com attendeded 1 event which was dated 2011-11-10. Not 
 the Nov 1st date in the Instructions. So if you look for an event in the past Nov 1st you won't find
 any for supersize@subrational.com.

Analyzing the data I found: user supersize@subrational.com (id: d421fd74-b2bf-4a15-9984-7954233f13e6) went to 1 
event (id: fc361414-c435-4a85-8be2-d03512c39973). That event has an event date of Nov 10 (after Nov 1st)
25 other users also attended the same event (id: fc361414-c435-4a85-8be2-d03512c39973)
	select * from attendees where event_id = 'fc361414-c435-4a85-8be2-d03512c39973'

Here's a sampling of the user ids who also attended the same event and whom we use for recommendations. 
Examining the data I double checked that events they attended were coming up in recommendations.

user id:                              email                         events attended
0551f79f-41f2-481e-b8dc-7cbd6498832f  pediceled@thinkling.com also  attended just that same event but no others
5c55c7ed-f428-4a3c-9bf9-1647dfe214fa  fundamental@cumaphytic.com    attended just that same event but no others
422e3ae6-9cc5-4567-aa8f-22e1fc0d2a92  tough@melene.com              attended that and 2 other events (these are recommended): 
                               9e4492e0-df36-44a6-8421-226bb9cb2db3, 59d4bbac-9adc-4220-ad2d-7e50f2c529f6
43190f11-fcf4-4474-8325-9f6612a840ae                                attended that and 2 other events (these are recommended): 
                               77bf8ad5-bc46-45b9-84b1-dedec442464b b13a8239-264b-4fe2-a117-5fcd17ca5262
ff928b75-8ba9-43ab-9664-6984601d3fd6                                 attended that and 1 other event (these are recommended):
                               a1bf91fa-c666-47a3-a2a6-f15139be1d5b
21bfa79a-d9a5-4398-8064-d059ca5dbedf                                 attended that and 3 other events (these are recommended):
                               add8eb70-489c-466c-95a8-95250215b255 1fea3554-0600-40ff-93d1-388c91afd3b2 1fea3554-0600-40ff-93d1-388c91afd3b2

							   
However of the 29 events recommended, only the last 4 have dates on or after 2011-11-11, per this query:
select * from events where event_id IN ('048a89c9-85c2-447e-9563-5ee5f65f2873',
'1fea3554-0600-40ff-93d1-388c91afd3b2',
'3cbdeacb-fd16-4526-9c8b-eb349f648253',
'3f84bdef-fc94-4c41-a05b-3f3ee4501d70',
'42d51615-55f0-42e4-b96d-3d45d7041b00',
'4bf4c581-db73-41b5-a143-d3b2783a9858',
'5242c995-ed90-48db-80b7-91a6ad9c434b',
'59d4bbac-9adc-4220-ad2d-7e50f2c529f6',
'6256b83d-2495-4944-af14-8aa671323ae5',
'6a1c4f08-815c-424b-bbb7-afc4e2b4a6b1',
'77bf8ad5-bc46-45b9-84b1-dedec442464b',
'954df908-a42e-413e-9bf3-f543e7dac931',
'96c129dc-295a-4336-a0d8-fa5d82b127b6',
'9bd09d26-45bd-4466-85c5-35d093ba76a1',
'9e4492e0-df36-44a6-8421-226bb9cb2db3',
'9e994661-376f-4b0a-bbc5-544d9a17dcec',
'9ef66565-53b4-402f-8f99-2726211e761d',
'a1bf91fa-c666-47a3-a2a6-f15139be1d5b',
'ab25deaf-a17b-418a-9acf-3459ee611c6b',
'add8eb70-489c-466c-95a8-95250215b255',
'b13a8239-264b-4fe2-a117-5fcd17ca5262',
'b143de6a-046f-48e6-858c-949d862e19b3',
'bfbe1006-1538-408d-a1bb-907e9bb455a6',
'c93d554b-5f61-449d-bd9d-462799f93f64',
'cea27f5b-fbdb-4929-9424-d4dafb31c647',
'cfac2a82-f391-4a75-acf9-cf664389bf58',
'db2a4919-faed-4ed6-8682-6e3e8874cf71',
'f2939966-4225-4f83-a3ff-bf82cba18baa',
'fc361414-c435-4a85-8be2-d03512c39973')
						   
							   
