---
title: "Using Clickhouse as an events store on Railway.app"
datePublished: Sun Jan 28 2024 11:42:02 GMT+0000 (Coordinated Universal Time)
cuid: clrxfkwle000d09jq9b8k3tej
slug: using-clickhouse-as-an-events-store-on-railwayapp
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1706441477799/78527bfa-cda9-4bfb-8796-683c98bc1ff1.png
tags: databases, clickhouse, railway-app

---

> NOTE: I encountered this while working on the 1px.li project. You can find the source code of that [here](https://github.com/championswimmer/onepixel_backend). It is hosted on Railway. You can find the hosting configuration and what all services it uses on [Railway here.](https://railway.app/project/d26a3016-de8c-4f9e-a873-aa810176f825)

For most things on [http://1px.li](https://t.co/MzBScT2FOM) so far I want to have a clean Dockerfile and docker-compose - and have a setup that replicates well on localhost as well as on Railway. So far Postgres was a charm. Clickhouse is not an officially supported "database" on Railway though.

![Image](https://pbs.twimg.com/media/GE7IvITboAAVASC?format=jpg&name=medium align="left")

But searching for Clickhouse does turn up a community-maintained image from marketplace, made by [Greg Scheir](https://twitter.com/GregorySchier), a Railway employee. It is basically straight up `clickhouse/clickhouse-server:23.3.8.21` docker image only, which is great for my "whatever works on localhost, works same on Railway" principle.

![Image](https://pbs.twimg.com/media/GE7I8anakAI_9pR?format=jpg&name=large align="left")

Since the docker image takes variables `CLICKHOUSE_USER`, `CLICKHOUSE_PASSWORD` and `CLICKHOUSE_DB` by default, it is really easy to configure those things when deploying to Railway - just set those variables.

![Image](https://pbs.twimg.com/media/GE7KggTbkAAkDHc?format=jpg&name=4096x4096 align="left")

![Image](https://pbs.twimg.com/media/GE7Kk2mbUAApinr?format=jpg&name=large align="left")

The challenge really comes in connecting to this database from other Railway services. So what happens for a databases like Postgres is that Railway sets up a TCP proxy for you - so that you can connect with the database's native driver that talks over raw TCP. Some databases (including Clickhouse) do allow connecting over HTTP(S) as well - but typically that is inefficient and unnecessary if your servers are inside a VPC already. While the docs at [Railway.app](https://railway.app) do seem to say that TCP proxying is available for any service, at least when I launched the Clickhouse template I found there is no way to set up TCP proxy, but only use the HTTP connection.

![Image](https://pbs.twimg.com/media/GE7MRWfboAEexz9?format=jpg&name=large align="left")

![Image](https://pbs.twimg.com/media/GE7MTWgacAE64_O?format=jpg&name=large align="left")

Using Clickhouse over HTTP is something that'll take slight bit of digging to figure out all the pieces of the puzzle.

![Image](https://pbs.twimg.com/media/GE7NfTXbQAAsBF1?format=jpg&name=large align="left")

The official drivers seems to be made only for native connections, but actually it does support HTTP mode as well. It is said to be "***experimental***" though - I believe some types of data might fail while escaping it to be able to pass through HTTP bodies. (I haven't faced any problem so far)

**But**, I was not interested in using the raw driver. I am already using Gorm, and wanted to use Gorm for Clickhouse as well. But the gorm driver doesn't quite clearly mention that you *can* connect over HTTP too just by passing `http://` as the schema instead of `clickhouse://`

![Image](https://pbs.twimg.com/media/GE7N7PTaEAAK8I-?format=jpg&name=large align="left")

The gorm clickhouse driver has moved to v2 of the [clickhouse golang library](https://github.com/ClickHouse/clickhouse-go), and thus, it can automatically use HTTP mode when the URL is HTTP protocol. Had to take to look through the source code to figure out that they handle it, before I could use it. Now to actually connect to this service on Railway, the best is to formulate 2 URLs, one for private and one for public access.

**Private Access:**

* you can use http, not https (within your cloud)
    
* server is exposed on the port of that service (default: 8123)
    

**Public Access**

* use https (Railway will handle TLS for you)
    
* server is exposed on 443, not the internal port, so don't specify port in this URL
    

![Image](https://pbs.twimg.com/media/GE7PbYNbQAAhO_j?format=jpg&name=medium align="left")

The public URL is what you can connect to from your laptop. The private URL is what you should use from your server hosted inside Railway to access this.