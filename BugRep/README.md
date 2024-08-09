# Running your own BugRep server

BugRep is the system we used to get notified of crashes and other bugs within ArcOS. Some reports were automatic, others were manually submitted by users (through the **Bug Reports** app). Because ArcOS has been shut down, we don't host our BugRep server anymore. You CAN however host your own, using these instructions.

First of all, get a fork or local download of both the [ArcOS frontend](https://github.com/IzK-ArcOS/v6) (instructions for cloning are in the repository's README), **and** the [BugRep frontend](https://github.com/IzK-ArcOS/ArcOS-Reports-Panel), and change the BugRep Server URL from `pb.arcapi.nl` to your own in both:

- In ArcOS: It's on line 18 of `src/ts/bugrep/index.ts`
- In BugRep: It's on line 5 of `src/ts/pb/main.ts`

I recommend using a subdomain for your Pocketbase instance, as it's just simply the most convenient.

1. Go ahead and download and install [Pocketbase](https://pocketbase.io) on your server, and expose it to the internet. We used an NGiNX reverse proxy with Cloudflare, as such:

- In NGiNX:

```
http {
  server {
    listen 80;
    listen [::]:80;

    server_name pb.arcapi.nl;

    location / {
      proxy_pass http://localhost:8091;
    }
  }
}
```

- In Cloudflare's DNS records:

| Type | Name   | Content | Proxy status | TTL  |
| ---- | ------ | ------- | ------------ | ---- |
| A    | bugrep | <ip>    | Proxied      | Auto |

2. Once you've deployed Pocketbase, and you've verified that it's working, log in on the admin frontend (`/_` is the pathname, e.g. `https://pb.arcapi.nl/_`). Go to **Settings** -> **Import collections** and click **Load from JSON file**. Select the `pocketbase_schema.json` file in this README file's folder.

If all goes well you should now have your very own ArcOS Pocketbase instance, so that BugRep may work again.
