# Authentification flow

Allcaps word are variable that you need to retrieve/provide.

## Already enabled session (id_session, privateKey known)

1. GET /api/connect?id_session=ID_SESSION =>
the server creates a new state row in database where id_session = IDSESSION, token = TOKEN (random string), access = false, time = now(), then generate the STATE variable like "IDSESSION-TOKEN"
2. RESPONSE {"error":false, "state":STATE} || {"error":true, "reason":WHYITFAILED}
3. GET /api/log?id_session=ID_SESSION&state=STATE&signature=PRIVATEKEY(id_session=ID_SESSION&state=STATE) => 
the server use the publicKey contained in the session table to decrypt the signature and make sure the client can sign stuff, if that's the case, access column is switch to true
4. RESPONSE {"error":false} || {"error":true, "reason":WHYITFAILED}
5. => do stuff with the API, on every call include the state parameter and the signature parameter which is the query string (minus the signature parametter itself) signed with the private key
