### Challenge 02
This obfuscated code is funny, as it creates a function that returns an array of strings used trough the entire code, along with a function to access the function mentioned before. We can just try to deobfuscate this with a tool, but for the purpose of the course I am going to take the longer route as if there wasn't no way to deobfuscate this code

By debugging, we get that the first two methods called are
Math.length
and Math.floor
This is used for the first check, meaning that the length for the flag string is 15 (Math.floor(Math.pi\*15))

```js
checkFlag = () => {
        const _0x2b4129 = _0x97be0e;
        let _0x517135 = flagInput[_0x2b4129(0x14f)];
        if (_0x517135[_0x2b4129(0x152)] !== Math[_0x2b4129(0x151)](0x5 * Math['PI'])) return showWrongFlag(), !0x1;
        let _0x4dd674 = convertAsciiToHex(_0x517135['substring'](0x0, 0x3));
        return _0x2b4129(0x141) !== _0x4dd674 || '6f' !== _0x517135[_0x2b4129(0x142)](0x4)[_0x2b4129(0x14a)](0x10)[_0x2b4129(0x14e)]() || '42' !== _0x517135[_0x2b4129(0x142)](0x5)[_0x2b4129(0x14a)](0x10)['toLowerCase']() || '66' !== _0x517135['charCodeAt'](0x6)[_0x2b4129(0x14a)](0x10)[_0x2b4129(0x14e)]() || '55' !== _0x517135['charCodeAt'](0x7)['toString'](0x10)['toLowerCase']() || '73' !== _0x517135[_0x2b4129(0x142)](0x8)[_0x2b4129(0x14a)](0x10)[_0x2b4129(0x14e)]() || '43' !== _0x517135['charCodeAt'](0x9)[_0x2b4129(0x14a)](0x10)[_0x2b4129(0x14e)]() || '61' !== _0x517135[_0x2b4129(0x142)](0xa)['toString'](0x10)[_0x2b4129(0x14e)]() || '54' !== _0x517135['charCodeAt'](0xb)[_0x2b4129(0x14a)](0x10)['toLowerCase']() || '65' !== _0x517135['charCodeAt'](0xc)[_0x2b4129(0x14a)](0x10)[_0x2b4129(0x14e)]() || '44' !== _0x517135[_0x2b4129(0x142)](0xd)[_0x2b4129(0x14a)](0x10)[_0x2b4129(0x14e)]() || '{' !== _0x517135[_0x2b4129(0x156)](0x3) || '}' !== _0x517135['charAt'](0xe) ? (showWrongFlag(), !0x1) : (correctFlag[_0x2b4129(0x143)]['remove'](_0x2b4129(0x159)), correctFlag[_0x2b4129(0x143)][_0x2b4129(0x158)]('flag_status'), !0x0);
},

```

By looking at the check flag function, we can see that we only have values for chars from 3 to 15, The convert ascii to hex function is done for chars 0 to 3

```js
function _0x5adf() {
    const _0x1e4eb8 = ['getElementById', '51283gPAojS', 'toString', '1625988lCIcSy', 'addEventListener', 'wrong_flag', 'toLowerCase', 'value', 'check_flag', 'floor', 'length', '20CRVEaS', 'flag', '14RaEIwJ', 'charAt', 'flag_status', 'add', 'd-none', '1860414gQizHQ', '166460rJHWOs', '1340253frtXka', 'click', '617768', 'charCodeAt', 'classList', '3288736xEiwDM', 'split', '699kBZaqv', '6996PssMOr'];
    _0x5adf = function() {
        return _0x1e4eb8;
    };
    return _0x5adf();
}
```

So we can easily deduct which is the first 3 letters from the fact that:
Is a 6 digit hex value
So first three are awh - 617768

Rest of the flag is checked by comparing each individual character of the provided input to a hex value of an ascii character, so we can just retrieve the rest of the flag by converting the hex value to ascii

Finally, flag is 
awh{redacted}


### Challenge 03
After dealing with the challenge 02, this was a breeze

We can just try to deobfuscate this with a tool, but for the purpose of the course I am going to take the lounger route as if there wasn't no way to deobfuscate this code

We have many ways of dealing with this, I Choosed the most straightforward, although we could have also tried to manually decrypt the article, given that we had the encryption key, the library used , the encrypted article and the algorithm used

First of all, we have a really ugly one line obfuscated code, so we prettyfy it
```js

var _0x55e95c = [115, 117, 98, 115, 99, 114, 105, 98, 101, 114];
var _0x55e95e = _0x13f5;

function _0x13f5(_0xcf02c4, _0x388f20) {
var _0x1fea74 = _0x1fea();
return _0x13f5 = function(_0x13f576, _0x267ca0) {
    _0x13f576 = _0x13f576 - 0xe5;
    var _0x10237b = _0x1fea74[_0x13f576];
    return _0x10237b;
}, _0x13f5(_0xcf02c4, _0x388f20);
}(function(_0x243803, _0x330b8d) {
var _0x3ad0d1 = _0x13f5,
    _0x342599 = _0x243803();
while (!![]) {
    try {
        var _0x4bc267 = -parseInt(_0x3ad0d1(0xfc)) / 0x1 * (parseInt(_0x3ad0d1(0xed)) / 0x2) + parseInt(_0x3ad0d1(0xf4)) / 0x3 * (parseInt(_0x3ad0d1(0xe8)) / 0x4) + -parseInt(_0x3ad0d1(0xeb)) / 0x5 + parseInt(_0x3ad0d1(0xef)) / 0x6 + parseInt(_0x3ad0d1(0xf6)) / 0x7 + parseInt(_0x3ad0d1(0xfe)) / 0x8 + -parseInt(_0x3ad0d1(0xfa)) / 0x9 * (parseInt(_0x3ad0d1(0xf1)) / 0xa);
        if (_0x4bc267 === _0x330b8d) break;
        else _0x342599['push'](_0x342599['shift']());
    } catch (_0x3423e0) {
        _0x342599['push'](_0x342599['shift']());
    }
}
}(_0x1fea, 0x84953));
var secretKey = String.fromCharCode.apply(null, _0x55e95c),
encryptedArticle = _0x55e95e(0xe9);
if (localStorage[_0x55e95e(0xfb)](_0x55e95e(0xe5)) === 'subscriber') try {
var decryptedBytes = CryptoJS[_0x55e95e(0xe7)][_0x55e95e(0xea)](encryptedArticle, secretKey),
    decryptedArticle = decryptedBytes[_0x55e95e(0xf5)](CryptoJS[_0x55e95e(0xec)]['Utf8']);
decryptedArticle && (document[_0x55e95e(0xf2)](_0x55e95e(0xf0))[_0x55e95e(0xf9)] = decryptedArticle, document[_0x55e95e(0xf2)](_0x55e95e(0xf0))[_0x55e95e(0xe6)]['display'] = _0x55e95e(0xf7), document[_0x55e95e(0xf2)]('subscriptionNotice')[_0x55e95e(0xe6)][_0x55e95e(0xfd)] = _0x55e95e(0xee));
} catch (_0x25e33e) {
console[_0x55e95e(0xf3)](_0x55e95e(0xf8), _0x25e33e);
}

function _0x1fea() {
var _0x485f00 = ['decrypt', '4354695SUlzzV', 'enc', '4MiEohy', 'none', '4208040aPCxoG', 'fullArticle', '221570eMVwNp', 'getElementById', 'error', '11967MYmFpn', 'toString', '6343638CPpnHZ', 'block', 'Decryption\x20failed:', 'innerHTML', '270IKyKJa', 'getItem', '511092XdGLxQ', 'display', '3872808MunLaf', 'subscriber', 'style', 'AES', '1012QMcbLm', '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'];
_0x1fea = function() {
    return _0x485f00;
};
return _0x1fea();
} 
```

At some point we can see the code
```js
if (localStorage[_0x55e95e(0xfb)](_0x55e95e(0xe5)) === 'subscriber')
```

This means that if *something* in localstorage is equal to "subscriber", we get access to the article

The way this obfuscation works is by juggling data between many functions and variables, but we have two main functions of interest

0x13f5 which takes care of accessing obfuscated array addresses 
0x1fea a function that stores all of the script strings on an array

Despite the fact that 0x13f5 is declared with two parameters, only one is used, this is part of the obfuscation process, the given address is then subtracted by 0xe5, and retrieved from 0x1fea

For the comparison that we have our interest on, resulting address is 22
22th position contains "subscriber"

So the resulting code is 
if (localStorage["subscriber"]\=\=\="subscriber"){
//decrypt
}

With this in mind, we create a new key in localStorage for this 
![[awh-front-3-1.png]]

Reload the page and we now have access to the article

![[Pasted image 20250311003517.png]]
