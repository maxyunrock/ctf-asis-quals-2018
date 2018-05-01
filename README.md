# ctf-asis-quals-2018
ASIS CTF QUALS 2018: Buy Flags

polarized
Category: WEB
Description: 
Here is an online shop that sells flags :) but we don’t have enough money! Can you buy the flag?




Checking the source code:
We see that its just a check for coupon,flags and and card . We also are able to see /pay .
 
 

Intercepting in BURP and examining session cookie. We find session cookie has something interesting half of session cookie value remains same in repeater. Now examining cookie for relevant info:

 

We base64 decode the session cookie and see output. Part of output is readable with fields coupon[] and card[] appended with random  sequence part which changes. This represented the static part of cookie was just coupon and card field. Attempts of figure out other part are in vain. 

So taking into account another vector. We can see from source code snippet. There is field count. Trying with  abuse count parameter.
 
We get response as:  not enough credit.
 

We change value of count to -1. The server response is:
Item count must be greater than zero.
 

We try with count 4 but get response: not enough credit 

Finally changing the count to NaN as NaN cannot be true in comparions:

 
We get pay success with flag in response:
 




