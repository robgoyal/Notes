# Privilege Escalation
Remember that there was a mysql user. 

Verifying that there is a MYSQL service listening on port 3306 using [[ss]].


[[mysql]]


There is no passwordless authentication allowed using strapi or developer user so we'll need a password.

The web application must store credentials somewhere if it's using mysql. 

```
strapi@horizontall:~/myapi$ ls       
ls
api    config      favicon.ico   package.json       public
build  extensions  node_modules  package-lock.json  README.md
```

Let's [[grep|search]] for passwords in the config folder.

```
strapi@horizontall:~/myapi$ grep -rni "password" config
grep -rni "password" config/
config/environments/production/database.json:13:        "password": "${process.env.DATABASE_PASSWORD || ''}",
config/environments/development/database.json:12:        "password": "#J!:F9Zt2u"
config/environments/staging/database.json:13:        "password": "${process.env.DATABASE_PASSWORD || ''}",
```

Great! The development folder contains a password that might help us. 

Trying this password out for both strapi and developer to switch into that user on the host did not work. However, we can authenticate to the MySQL server as developer.