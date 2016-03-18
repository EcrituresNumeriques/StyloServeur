# Authentification flow

Allcaps word are variable that you need to retrieve/provide.

## Already enabled session (id_session, privateKey known)

1. GET /api/user/connect?id_session=ID_SESSION =>
the server creates a new state row in database where id_session = IDSESSION, token = TOKEN (random string), access = false, time = now(), then generate the STATE variable like "IDSESSION-TOKEN"
2. RESPONSE {"error":false, "state":STATE} || {"error":true, "reason":WHYITFAILED}
3. GET /api/user/log?id_session=ID_SESSION&state=STATE&signature=PRIVATEKEY(id_session=ID_SESSION&state=STATE) => 
the server uses the publicKey contained in the session table to decrypt the signature and makes sure the client can sign stuff, if that's the case, access column is switch to true
4. RESPONSE {"error":false, "data",[DATAOFTHEUSER]} || {"error":true, "reason":WHYITFAILED}
5. => do stuff with the API, on every call include the state parameter and the signature parameter which is the query string (minus the signature parametter itself) signed with the private key

## No session enabled but already registered (login, password known)

1. GET /api/user/session?login=LOGIN&password=PASSWORD => The server creates a new user_session row, then creates a new state row in database where id_session = IDSESSION, token = TOKEN (random string), access = false, time = now(), then generates the STATE variable like "IDSESSION-TOKEN" and sends it to the email of the user.
2. RESPONSE {"error":false} || {"error":true, "reason":WHYITFAILED}
3. GET /api/auth?state=CODERECEIVEDBYEMAIL&name=NAMEOFTHESESSION => The server generate a PUBLIC/PRIVATE keys pair, updates the user_session row name and publicKey values, then activates the user_session_state (access = true) 
4. RESPONSE {"error":false,"privateKey":PRIVATEKEY} || {"error":true, "reason":WHYITFAILED}
5. => do stuff with the API, on every call include the state parameter and the signature parameter which is the query string (minus the signature parametter itself) signed with the private key
 

## New account

1. GET /api/user/register?email=EMAIL => the server creates or updates email_confirm row where email = EMAIL, generate a token and sends it by email.
2. RESPONSE {"error":false} || {"error":true, "reason":WHYITFAILED}
3. GET /api/user/validate?email=EMAIL&token=TOKEN => the server validate the code, set failed to true if the attempt is unsuccesful (need to generate a new token), if successful, sets failed to false, creates user/user_session/user_session_state rows
4. RESPONSE {"error":false, "id_session":SESSION, "privateKey" = PRIVATEKEY, "state":STATE} || {"error":true, "reason":WHYITFAILED}
5. GET /api/user/register?email=EMAIL&login=LOGIN&password=PASSWORD&name=NAMEOFTHESESSION&state=STATE&signature=PRIVATEKEY(email=EMAIL&login=LOGIN&password=PASSWORD&name=NAMEOFTHESESSION&state=STATE)
