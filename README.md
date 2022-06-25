# basic-auth
## Inatallation
```javascript
  npm i base-64
  npm i bcrypt
```

## require data
```javascript
const base64 = require('base-64');
const $$DEFINE MODEL USE$$=require("../modul/auth_model/reguster_model")
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
            const user = await $$DEFINE MODEL USE$$.findOne({ where: { email: email } });
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
