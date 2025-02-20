---
title: Cloudflare cache responses
pcx_content_type: reference
meta:
  title: Cloudflare cache responses
---

# Cloudflare cache responses

The `CF-Cache-Status` header output indicates whether a resource is cached or not. To investigate cache responses returned by this header, use services like [Redbot](https://redbot.org/), [webpagetest.org](http://www.webpagetest.org/), or a visual tool like [Chrome's Dr. Flare plugin](https://community.cloudflare.com/t/community-tip-dr-flare-debug-tool-for-cloudflare-chrome-extension/110166).

Below you can find a comprehensive breakdown of Cloudflare's cache response statuses.

## `HIT`

The resource was found in Cloudflare's cache.

## `MISS`

The resource was not found in Cloudflare's cache and was served from the origin web server.

## `NONE/UNKNOWN`

Cloudflare generated a response that denotes the asset is not eligible for caching. This may have happened because:

- A Worker generated a response without sending any subrequests. In this case, the response did not come from cache, so the cache status will be `none/unknown`.

- A Worker request made a subrequest (`fetch`). In this case, the subrequest will be logged with a cache status, while the main request will be logged with `none/unknown` status (the main request did not hit cache, since Workers sits in front of cache).

- A WAF custom rule was triggered to block a request. The response will come from the Cloudflare global network before it hits cache. Since there is no cache status, Cloudflare will log as `none/unknown`.

- A redirect cache rule caused the global network to respond with a redirect to another asset/URL. This redirect response happens before the request reaches cache, so the cache status is `none/unknown`.

## `EXPIRED`

The resource was found in Cloudflare's cache but was expired and served from the origin web server.

## `STALE`

The resource was served from Cloudflare's cache but was expired. Cloudflare could not contact the origin to retrieve an updated resource.

## `BYPASS`

The origin server instructed Cloudflare to bypass cache via a `Cache-Control` header set to `no-cache`, `private`, or `max-age=0` even though Cloudflare originally preferred to cache the asset. BYPASS is returned when enabling [Origin Cache-Control](/cache/concepts/cache-control/). Cloudflare also sets BYPASS when your origin web server sends cookies in the response header. If the Request to your origin includes an `Authorization` header, in some cases the response will also be BYPASS. Refer to [Conditions](/cache/concepts/cache-control/#conditions) in the Origin Cache-Control behavior section for more details.

## `REVALIDATED`

The resource is served from Cloudflare's cache but is stale. The resource was revalidated by either an `If-Modified-Since` header or an `If-None-Match` header.

## `UPDATING`

The resource was served from Cloudflare's cache and was expired, but the origin web server is updating the resource. `UPDATING` is typically only seen for very popular cached resources.

## `DYNAMIC`

Cloudflare does not consider the asset eligible to cache and your Cloudflare settings do not explicitly instruct Cloudflare to cache the asset. Instead, the asset was requested from the origin web server. Use [Cache Rules](/cache/how-to/cache-rules/) to implement custom caching options.