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


There is no need to self-manage the site, or even git commit/pull everytime I make an update, as was the case with the previous implementation of a similar sub-blog at <https://notion.rkpblog.tech>

## Get Started

---

On a high level, we are utilizing [Cloudflare Workers](https://blog.cloudflare.com/introducing-cloudflare-workers/) to rewrite traffic. The solution is inspired by [this script](https://gist.github.com/mayneyao/b9fefc9625b76f70488e5d8c2a99315d) (thank you Mayne!), and I added my own features like pretty links.

- Step 0: Prerequisite
    1. Enable Public Access on your desired pages through Notion's Share menu, and Allow Search Engines.
    2. Purchase your desired domain with a registrar like Namecheap, or use your existing domain or subdomain.

- Step 1: Set up your Cloudflare account (5 mins)
    1. Sign up for an account: [https://dash.cloudflare.com/sign-up](https://dash.cloudflare.com/sign-up)

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5c7779fb-47cf-4cb7-813a-83b9d8bf9b35%2FUntitled.png?table=block&id=f0d94463-cf43-42f7-a61d-0784b8af1e7f&width=5570&cache=v2)

    2. Enter your custom domain name. If you would like to use a subdomain, you should still entire your root domain name here.

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fed1a3c23-a9ec-4d11-9391-a75cb56c3975%2FUntitled.png?table=block&id=d5ff74b3-5375-4732-8464-e712a1096487&width=5570&cache=v2)

    3. Select the Free plan

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7e547892-a740-421d-95d4-121230a3bbd0%2FUntitled.png?table=block&id=e6ecebc7-bce8-4f3c-968b-fb3b41814060&width=5570&cache=v2)

    4. If you don't have any A records imported, add one with your root domain as the Name and `1.1.1.1` as the Content. Otherwise, click Continue on the DNS Record page.
        - If you are using a subdomain and don't have any CNAME records imported, add one with your subdomain as the Name and `1.1.1.1` as the Content.

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9e36279b-9c8b-4c9b-bfeb-44b98d91437f%2FUntitled.png?table=block&id=ed7b571e-25ec-4f66-a683-1ec6cdeace85&width=5570&cache=v2)

    5. Copy the 2 nameservers, which end with `.ns.cloudflare.com`

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe93d1654-3278-4d62-987c-227022f5454b%2FUntitled.png?table=block&id=e5853c2d-e226-4e2c-b660-59a0e6fef2fd&width=5570&cache=v2)

    6. Paste the nameservers in the domain setting page at your registrar (Namecheap in my case). Make sure you save the setting.

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1b0eff3a-8f6b-4ca6-93da-dfb059d48d51%2FUntitled.png?table=block&id=9f0649d6-4f56-4069-b943-c95eb131e295&width=5570&cache=v2)

    7. Wait for a minute, then click Done, check nameservers

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff84e767f-1d6e-4503-a68f-a9d67fb1c2ce%2FUntitled.png?table=block&id=d6b6b3fc-4399-440f-93d3-56e3d197c32f&width=5570&cache=v2)

    8. Select Flexible SSL/TLS encryption mode

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F86fb45ea-88ba-4f12-8ec0-0c294227c586%2FUntitled.png?table=block&id=28ea024d-3e41-4f15-9d09-a298866ec9bb&width=5570&cache=v2)

    9. Turn on Always Use HTTPS, Auto Minify, and Brotli (all 3 optional but recommended)

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4ea044ae-69cb-4e20-9870-792ad3e35c6c%2FUntitled.png?table=block&id=a5505ef3-d6b2-4177-ad04-a45222943ece&width=1340&cache=v2)

    10. Select Done

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F6f76a854-7098-4ce6-a332-e694b39ab2f2%2FUntitled.png?table=block&id=65412f29-aca1-4245-b293-c9e46a23645c&width=5570&cache=v2)

    11. You should see this screen. If Cloudflare hasn't detected your site, click Re-check your site, and refresh the page.

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0c85e5ba-3dc3-4871-bee5-c100a58cc3ab%2FUntitled.png?table=block&id=cec95165-e5cf-4644-ab3d-4255c1881c65&width=5570&cache=v2)

    12. Select the Workers page (one of the blue boxes) and then click Manage Workers

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F065a77fe-072f-4932-9828-070e77fb1bb4%2FUntitled.png?table=block&id=b7a15896-1b6e-431e-b875-30564c44c355&width=5570&cache=v2)

    13. Choose any available subdomain for your worker (it doesn't really matter what you pick)

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe3bcafea-22dd-4e01-9b86-542674bdbe5e%2FUntitled.png?table=block&id=43462f78-dd49-4919-a6ef-bdc9704e5a16&width=5570&cache=v2)

    14. Click Set Up and then click Confirm

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd81dd492-c47d-4885-b970-bfb46af60e05%2FUntitled.png?table=block&id=7a64796f-0e03-4f55-9bf8-d60af3e5c52a&width=5570&cache=v2)

    15. Choose the Free plan
        - If your site gets a lot of visitors, you can change to the paid Unlimited plan later.

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5e42872c-c904-4642-ba1e-67ffbea17228%2FScreen_Shot_2020-05-04_at_11.14.24_PM.png?table=block&id=4f0e37db-7efa-400b-9efd-54c3421ec1eb&width=5570&cache=v2)

    16. Verify your email if you haven't, then go back to the Manage Workers page (see #12)

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F16e82d73-b971-41fe-9fb4-c25c8d1b14ae%2FUntitled.png?table=block&id=bee917a4-089b-4181-8803-ba069d67808e&width=5570&cache=v2)

    17. Click Create a Worker

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fdcdcea91-9270-4a4f-9de5-c7bc1cbb615a%2FUntitled.png?table=block&id=3588dc91-2981-4b4f-a9c9-91e72923fe16&width=5570&cache=v2)

- Step 2: Customize and generate the script (2 mins)

<iframe class="airtable-embed" src="https://csb-vydqj.stephenou.now.sh/" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

- Step 3: Paste the script in Cloudflare (1 min)
    1. Delete the existing code, and paste the code you copied

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb2aedf89-4fbd-4e60-8858-5598d7329370%2FUntitled.png?table=block&id=9eb82064-43f1-43b6-aa47-d67f53b5df34&width=5570&cache=v2)

    2. Click Save and Deploy

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbf2a1616-304e-4cbb-9cbe-dfa670e55838%2FUntitled.png?table=block&id=555eb822-a581-4d29-a1b7-eafcc5a38ab2&width=5570&cache=v2)

    3. After saving, click on your site name on the top of the page

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd5a7add2-6b33-4be5-951d-10dc3ff869b7%2FUntitled.png?table=block&id=b738f601-1231-4f52-9d59-7784e350d94a&width=5570&cache=v2)

    4. Go to the Workers page and select Add Route

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb2600ac1-577b-4352-869c-7f8f7b11ced5%2FUntitled.png?table=block&id=a61c8688-b401-4744-b6c8-2ace15fe0005&width=5570&cache=v2)

    5. Type `yourdomain.com/*` (or `[subdomain.yourdomain.com/*](http://subdomain.yourdomain.com/*)` if you would like to use a subdomain) as the Route and select the Worker you just created

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F04ef4ebd-0684-4d92-8c3d-6cac01320bff%2FUntitled.png?table=block&id=bb22e74b-9eac-4975-b33d-65a0534be24b&width=5570&cache=v2)

    6. Hit save, and you're done! You can now visit your site. 🎉

        ![](https://fruitionsite.com/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc921e63e-a491-42c4-9279-c0c7bbb9f2fd%2FUntitled.png?table=block&id=9a34cebd-68b3-4a95-a623-9a26606107cc&width=5570&cache=v2)
