# basic-auth
## Inatallation
```javascript
  npm i base-64
  npm i bcrypt
```

## require data
```javascript
const base64 = require('base-64');
const @@@theModel=require("../modul/auth_model/reguster_model")
const bcrypt = require('bcrypt');
```
## implimentation 
```javascript
module.exports=async (req,res,next)=>{
    //----------------------------------DECODED THE CODE ---------------------------------------------//
        //req.headers.authorization=Basic bGl0aDoxMjM
        //THE CODE COME FROM HEADER ENCODE YOU NEED TO DECODED


        let basicHeaderParts = req.headers.authorization.split(' ');  // ['Basic', 'KDHKJAHKJDSHJKASHJK']
        let encodedString = basicHeaderParts.pop();  // KDHKJAHKJDSHJKASHJK
        let decodedString = base64.decode(encodedString); // "USERNAME:PASSWORD"
        let [email, password] = decodedString.split(':'); //[USERNAME,PASSWORD]


        try{
            //CHECK THE EMAIL IS CORRECT RO NOT
            const user = await @@@theModel.findOne({ where: { email: email } });
            //CHECK IF PASSWORD FROM HEADER MATCH WITH  PASSWORD FROM DATABASE
            const valid = await bcrypt.compare(password, user.password);
            if(valid){
            //IF THE EMAIL AND PASSWORD ARE CORRECT GO TO NEXT STEP
            //passing the information in request 
            req.basicSignInUser=user
            next()
            }
            else{
                throw new Error('EMAIL OR PASSWORD ARE WRONG');
            }
        }
        catch (error) { 
            res.status(403);
            res.send("Invalid Signin");
    
        }
}
```

# barear auth
## installation 
```javascript 
npm i dotenv
npm i jsonwebtoken
```


## reaquird data

```javascript
require('dotenv').config();
const jwt = require('jsonwebtoken');
const @@theModel=require("../modul/auth_model/reguster_model")
```

```javascript
module.exports = async (req, res, next) => {
  try {
    if(!req.headers.authorization){
      return res.send("You are not authorized  please set the barear ")
    }
    //CHECK IF THE HEADER CONTAIN AUTHRIZATION 
    if (req.headers.authorization) {
    //GET THE JWT TOKEN FROM HERE( Bearer eyJhbGciXVCJ9.eyJlbWFpbCI6ImxpY1NTI3NTk4MH0.0) CODE WILL ACCEPT ASS STRING
    var token = req.headers.authorization.split(' ')[1];
    //GET THE SIGNIN ELEMT {USERNAME,SECRETE,EXPIRATION}{ email: 'lith', iat: 1655275080, exp: 1655275980 }
    const parsedToken = jwt.verify(token, process.env.ACCESS_TOKEN_SECRET)
    //SEARCH  IN DATABASE AND RETURN THE USER HAVE THE SAME EMAIL
    const user =await @@@theModel.findOne({where:{email:parsedToken.email}})
    req.baererUser=user
    next()

    }
  } catch (e) {
    res.status(403);
    res.send("Invalid Signin");

  }
}
```
