Default port: 27017

Connect to a port and specific database

mongo --port <port> <database>

Once connected, find all sites and pretty print them

db.site.find().forEach(printjson);

Find all accounts and print them

db.admin.find().forEach(printjson);

Create a shadow administrator account
