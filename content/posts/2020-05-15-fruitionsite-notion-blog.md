---
toc: true
layout: post
description: Using Cloudfalre Workers to run edge JavaScriipt to set up a public facing Notion Page
categories: [markdown]
title: Blogging using Notion Pages
tags: [javascript, notion, configuraton]
---

> Courtesy: [fruitonsite](https://fruitionsite.com)


I've set up a sub-blog at https://notionso.rkpblog.tech, that's directly published from a public Notion Page in my Notion account.


There is no need to self-manage the site, or even git commit/pull everything I make an update, as was the case wit hteh previouss implementation of a similar sub-blog at <https://notion.rkpblog.tech>

## Get Started

---

On a high level, we are utilizing [Cloudflare Workers](https://blog.cloudflare.com/introducing-cloudflare-workers/) to rewrite traffic. The solution is inspired by [this script](https://gist.github.com/mayneyao/b9fefc9625b76f70488e5d8c2a99315d) (thank you Mayne!), and I added my own features like pretty links.

- Step 0: Prerequisite
    1. Enable Public Access on your desired pages through Notion's Share menu, and Allow Search Engines.
    2. Purchase your desired domain with a registrar like Namecheap, or use your existing domain or subdomain.

- Step 1: Set up your Cloudflare account (5 mins)
    1. Sign up for an account: [https://dash.cloudflare.com/sign-up](https://dash.cloudflare.com/sign-up)

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5c7779fb-47cf-4cb7-813a-83b9d8bf9b35/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5c7779fb-47cf-4cb7-813a-83b9d8bf9b35/Untitled.png)

    2. Enter your custom domain name. If you would like to use a subdomain, you should still entire your root domain name here.

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ed1a3c23-a9ec-4d11-9391-a75cb56c3975/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ed1a3c23-a9ec-4d11-9391-a75cb56c3975/Untitled.png)

    3. Select the Free plan

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7e547892-a740-421d-95d4-121230a3bbd0/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7e547892-a740-421d-95d4-121230a3bbd0/Untitled.png)

    4. If you don't have any A records imported, add one with your root domain as the Name and `1.1.1.1` as the Content. Otherwise, click Continue on the DNS Record page.
        - If you are using a subdomain and don't have any CNAME records imported, add one with your subdomain as the Name and `1.1.1.1` as the Content.

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9e36279b-9c8b-4c9b-bfeb-44b98d91437f/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9e36279b-9c8b-4c9b-bfeb-44b98d91437f/Untitled.png)

    5. Copy the 2 nameservers, which end with `.ns.cloudflare.com`

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e93d1654-3278-4d62-987c-227022f5454b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e93d1654-3278-4d62-987c-227022f5454b/Untitled.png)

    6. Paste the nameservers in the domain setting page at your registrar (Namecheap in my case). Make sure you save the setting.

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1b0eff3a-8f6b-4ca6-93da-dfb059d48d51/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1b0eff3a-8f6b-4ca6-93da-dfb059d48d51/Untitled.png)

    7. Wait for a minute, then click Done, check nameservers

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f84e767f-1d6e-4503-a68f-a9d67fb1c2ce/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f84e767f-1d6e-4503-a68f-a9d67fb1c2ce/Untitled.png)

    8. Select Flexible SSL/TLS encryption mode

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/86fb45ea-88ba-4f12-8ec0-0c294227c586/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/86fb45ea-88ba-4f12-8ec0-0c294227c586/Untitled.png)

    9. Turn on Always Use HTTPS, Auto Minify, and Brotli (all 3 optional but recommended)

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4ea044ae-69cb-4e20-9870-792ad3e35c6c/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4ea044ae-69cb-4e20-9870-792ad3e35c6c/Untitled.png)

    10. Select Done

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6f76a854-7098-4ce6-a332-e694b39ab2f2/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6f76a854-7098-4ce6-a332-e694b39ab2f2/Untitled.png)

    11. You should see this screen. If Cloudflare hasn't detected your site, click Re-check your site, and refresh the page.

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0c85e5ba-3dc3-4871-bee5-c100a58cc3ab/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0c85e5ba-3dc3-4871-bee5-c100a58cc3ab/Untitled.png)

    12. Select the Workers page (one of the blue boxes) and then click Manage Workers

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/065a77fe-072f-4932-9828-070e77fb1bb4/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/065a77fe-072f-4932-9828-070e77fb1bb4/Untitled.png)

    13. Choose any available subdomain for your worker (it doesn't really matter what you pick)

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e3bcafea-22dd-4e01-9b86-542674bdbe5e/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e3bcafea-22dd-4e01-9b86-542674bdbe5e/Untitled.png)

    14. Click Set Up and then click Confirm

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d81dd492-c47d-4885-b970-bfb46af60e05/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d81dd492-c47d-4885-b970-bfb46af60e05/Untitled.png)

    15. Choose the Free plan
        - If your site gets a lot of visitors, you can change to the paid Unlimited plan later.

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5e42872c-c904-4642-ba1e-67ffbea17228/Screen_Shot_2020-05-04_at_11.14.24_PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5e42872c-c904-4642-ba1e-67ffbea17228/Screen_Shot_2020-05-04_at_11.14.24_PM.png)

    16. Verify your email if you haven't, then go back to the Manage Workers page (see #12)

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/16e82d73-b971-41fe-9fb4-c25c8d1b14ae/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/16e82d73-b971-41fe-9fb4-c25c8d1b14ae/Untitled.png)

    17. Click Create a Worker

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dcdcea91-9270-4a4f-9de5-c7bc1cbb615a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dcdcea91-9270-4a4f-9de5-c7bc1cbb615a/Untitled.png)

- Step 2: Customize and generate the script (2 mins)

    ![https://csb-vydqj.stephenou.now.sh](https://csb-vydqj.stephenou.now.sh)

- Step 3: Paste the script in Cloudflare (1 min)
    1. Delete the existing code, and paste the code you copied

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b2aedf89-4fbd-4e60-8858-5598d7329370/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b2aedf89-4fbd-4e60-8858-5598d7329370/Untitled.png)

    2. Click Save and Deploy

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf2a1616-304e-4cbb-9cbe-dfa670e55838/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf2a1616-304e-4cbb-9cbe-dfa670e55838/Untitled.png)

    3. After saving, click on your site name on the top of the page

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d5a7add2-6b33-4be5-951d-10dc3ff869b7/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d5a7add2-6b33-4be5-951d-10dc3ff869b7/Untitled.png)

    4. Go to the Workers page and select Add Route

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b2600ac1-577b-4352-869c-7f8f7b11ced5/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b2600ac1-577b-4352-869c-7f8f7b11ced5/Untitled.png)

    5. Type `yourdomain.com/*` (or `[subdomain.yourdomain.com/*](http://subdomain.yourdomain.com/*)` if you would like to use a subdomain) as the Route and select the Worker you just created

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/04ef4ebd-0684-4d92-8c3d-6cac01320bff/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/04ef4ebd-0684-4d92-8c3d-6cac01320bff/Untitled.png)

    6. Hit save, and you're done! You can now visit your site. 🎉

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c921e63e-a491-42c4-9279-c0c7bbb9f2fd/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c921e63e-a491-42c4-9279-c0c7bbb9f2fd/Untitled.png)
