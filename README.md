Was sick and bored so I made a pretty lame challange - web

I created robots.txt
      User-agent: *
      Allow: /
      Disallow: /usr
      
In the folder usr their was a file: usr
content of usr file(a base64 decoder string):
```
KioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqDQphY3RpdmUgdXNlcnM6DQp6ZXIwZmtpDQprNDVraW8NCg0KZGVhY3RpdmF0ZWQgdXNlcnM6DQpjMG1yYWRlDQpzMGxvDQoqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKio=
```
```
base64 decode:
>>> base64.b64decode('KioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqDQphY3RpdmUgdXNlcnM6DQp6ZXIwZmtpDQprNDVraW8NCg0KZGVhY3RpdmF0ZWQgdXNlcnM6DQpjMG1yYWRlDQpzMGxvDQoqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKio=')
'*********************************************\r\n
active users:\r\
nzer0fki\r\
nk45kio\r\n\r\n

deactivated users:\r\
nc0mrade\r\
ns0lo\r\n
*********************************************'
```

I left a few clues when no one seemed to try to solve it, one of them was:
'Users can possibly have weak passwords'

And a couple of hours later d3vnu11 solved it :)

https://swehack.org/viewtopic.php?f=37&t=2462

```
<?php
$realm = 'top secret';
$users = array('zer0fki' => 'passw0rd', 'k45kio' => 'iloveu');
if (empty($_SERVER['PHP_AUTH_DIGEST'])) {
    header('HTTP/1.1 401 Unauthorized');
    header('WWW-Authenticate: Digest realm="'.$realm.
           '",qop="auth",nonce="'.uniqid().'",opaque="'.md5($realm).'"');

    die('good decision');
}

if (!($data = http_digest_parse($_SERVER['PHP_AUTH_DIGEST'])) ||
    !isset($users[$data['username']])){
    	header('HTTP/1.1 401 Unauthorized', true, 401);
    	die('Wrong Credentials!');
    }
$A1 = md5($data['username'] . ':' . $realm . ':' . $users[$data['username']]);
$A2 = md5($_SERVER['REQUEST_METHOD'].':'.$data['uri']);
$valid_response = md5($A1.':'.$data['nonce'].':'.$data['nc'].':'.$data['cnonce'].':'.$data['qop'].':'.$A2);

if ($data['response'] != $valid_response){
   	 header('HTTP/1.1 401 Unauthorized', true, 401);	
	 die('Wrong Credentials!');

}   	
// ok, valid username & password
echo '<center>flag{7h3_c0mpu73r_h45_b33n_dr1nk1n6_n07_m3}<br><br>';
echo '<img src="http://i.imgur.com/zWeHndt.gif"></img>';


// function to parse the http auth header
function http_digest_parse($txt)
{
    // protect against missing data
    $needed_parts = array('nonce'=>1, 'nc'=>1, 'cnonce'=>1, 'qop'=>1, 'username'=>1, 'uri'=>1, 'response'=>1);
    $data = array();
    $keys = implode('|', array_keys($needed_parts));

    preg_match_all('@(' . $keys . ')=(?:([\'"])([^\2]+?)\2|([^\s,]+))@', $txt, $matches, PREG_SET_ORDER);

    foreach ($matches as $m) {
        $data[$m[1]] = $m[3] ? $m[3] : $m[4];
        unset($needed_parts[$m[1]]);
    }

    return $needed_parts ? false : $data;
}
?>
```
