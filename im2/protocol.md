Flow protocal
===
Every thing is JSON!
The protocol is constanted of two parts,one is length of the json string,the other is json string.

signalType define:

- 0: heart beat
- 1: login req
- 1 << 4: login res
- 2: start calling session req
- 2 << 4: start calling session res

- 401 call 
- 402 answer
- 403 end session
- 
- 5: logout req
- 5 << 4: logout res



head format:

    "head":{
      "signalType":digitNumber, // predefined type number
      "status":0|-1,// 0:normal,-1:abnormal
      "sessionToken":"32bit sequence"// whether client is login. if this is empty, server need to find password in the body section.
    }

bizType:

- 1024: calling
- 2048: answering
- 4096: end session
- 0: kindly hangup.
- 1: i am busy.
- 2: refuse.


#### Login
Login to Account server

req:

    {
      "head":{
        "signalType":digitNumber, // predefined type number
        "status":0|-1,// 0:normal,-1:abnormal
        "sessionToken":"32bit sequence"// whether client is login. if this is empty, server need to find password in the body section.
      },
      "body":{
        "account":"appleseedez",
        "password":"ffsdfadfasdf" // if sessionToken is not empty string, this should be empty.
      }

    }

res:

    {
      "head":{/*...*/},
      "body":{
         "ssIP":"127.0.0.1",// signal server ip
         "ssPort":3376,// signal server port
         "stunServer":"some stun server", //
         "userInfo":{ /*...*/ }
      }
    }


#### Connet to Signal server

nothing to do

#### client heart beat

req:

    {
      "head":{/*...*/}
      "body":{}
    }

res: no response


#### start calling session

check to see if liuyang is online.

appleseedez => liuyang

req:

    {
      "head":{/*...*/}
      "body":{
        "peerAccount":"liuyang",
        "myAccount":"appleseedez"
      }
    }


res:

    {
      "body":{
        "myAccount":"appleseedez",
        "peerAccount":"liuyang",
        "sessionID":112,
        "relayIP":"10.0.0.1",
        "relayPort":1234
      }
    }



#### calling & answering

appleseedez=>liuyang.

req:

    {
      "head":{
        /*...*/,
        "signalType":4 // this indicate that the req or res is a relay signal. server should send data field to the peerAccount
      },
      "body":{
        "data":{"peerAccount":"appleseedez", "peerSessionID":112, "peerInterIP":"10.0.0.1", "peerInterPort":1234, "peerLocalIP":"10.0.0.2", "peerLocalPort":3425, "myAccount":"liuyang", "mySessionID":113,"bizType":1024},
        "peerAccount":"liuyang",
        "myAccount":"appleseedez"
      }
    }


res:

    {
      "head":{
        /*...*/,
        "signalType":4 // this indicate that the req or res is a relay signal. server should send data field to the peerAccount
      },
      "body":{
        "data":{"peerAccount":"liuyang", "peerSessionID":112, "peerInterIP":"10.0.0.1", "peerInterPort":1234, "peerLocalIP":"10.0.0.2", "peerLocalPort":3425, "myAccount":"liuyang", "mySessionID":113,"bizType":2048},
        "peerAccount":"appleseedez",
        "myAccount":"liuyang"
      }
    }


#### end calling session

appleseedez=>liuyang

req:

    {
      "head":{
        /*...*/,
        "signalType":4 // this indicate that the req or res is a relay signal. server should send data field to the peerAccount
      },
      "body":{
        "data":{"myAccount":"liuyang", "peerAccount":"appleseedez", "bizType":4096, "endMode":1|2|3},
        "peerAccount":"appleseedez",
        "myAccount":"liuyang"
      }
    }


#### logout

req:

    {
      "head":{ /*...*/ },
      "body":{
        "myAccount":"appleseedez"
      }
    }
