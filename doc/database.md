# Database

## user

 1. id_user (unique, primary) INT 
 2. name VARCHAR 64
 3. login (unique) VARCHAR 64
 4. email (unique) VARCHAR 128
 5. password VARCHAR 86
 6. salt VARCHAR 16

## user_session

1. id_session (unique, primary) INT
2. id_user INT
3. publicKey BLOB
4. name VARCHAR (128)

## user_session_state

1. id_state (unique, primary) INT
2. token VARCHAR 128
3. access BOOL
4. id_session INT
5. time TIMESTAMP
6. update TIMESTAMP
7. ip VARCHAR (IPv4, IPv6)
8. userAgent TEXT
9. failed BOOL

## email_confirm

1. email VARCHAR 128
2. token  VARCHAR 128
3. failed BOOL

## article

1. id_article (unique, primary) INT
2. time TIMESTAMP
3. update TIMESTAMP
4. file VARCHAR (/files/path/to/file.xml)
5. permission VARCHAR ( exclusive | private | collaborative | open )

## article_permission

1. id_permission (unique, primary) INT
2. id_article INT
3. access VARCHAR ( see | comment | suggest | enrich | edit | owner )
