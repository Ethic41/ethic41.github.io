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

### Solution

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

