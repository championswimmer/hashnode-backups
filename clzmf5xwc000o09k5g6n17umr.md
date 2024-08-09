---
title: "Making one Jest test file depend on outputs from another"
seoDescription: "Guide to making one Jest test file depend on the outputs from another using a global observable object and promises"
datePublished: Fri Aug 09 2024 08:02:35 GMT+0000 (Coordinated Universal Time)
cuid: clzmf5xwc000o09k5g6n17umr
slug: making-one-jest-test-file-depend-on-outputs-from-another
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1723190454944/f91c46ed-9f8c-4d70-9603-e4e15a965325.png
tags: testing, typescript, jest

---

I'll start off by saying this is an ugly hack, and I hope if you have reached this article, you really know what you're doing.

Tests should be independent, and ideally not block each other. Jest is designed to not make test file execution order deterministic, nor sequential, and there's a reason for that. `one.spec.ts` and `two.spec.ts` should be completely independent set of tests that can run from start to end without interacting with each other. There's a reason unit tests are called *unit tests.*

That said, let me describe where I wanted to do something like this. Below is a simplified diagram of some systems that we use, and the kind of tests we want to do.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723187129332/773a2a8f-5608-417d-a2e9-ea8af1fc36ff.png align="center")

### The flow of the system

* home page - a list of categories (from content service)
    
* categories - a list of contents (from content service)
    
* contents - contain a content id with which further data is to be fetched from playback service
    
* player - gets playback URLs and runs the correct rendition\* of content based on network conditions / device capability etc.
    

> A 'rendition' means different formats like 4K, 1080p, with or without Dolby audio etc.

### The test files and what they do

#### content.tests.ts

Tests that home page data can be fetched, and further data of all the contents inside it can be fetched. This is not a typical unit test, this is a periodic test that runs every hour, and we want it to fail if the API is not sending the correct data. (Think of this as more of an API monitoring test)

#### playback.tests.ts

This gets a content id, and based on that fetches content metadata and playback URLs from the playback service. It tests that playback URLs for all content ids are actually available, and the playback URLs contain valid playable video content / streams.

### Why we want tests to be dependent on each other?

When `content.tests.ts` runs, it already fetches all the content ids (N categories, avg M contents in each - so `M x N` content ids)

We wish to run `playback.tests.ts` on all of those `M x N` content ids. Instead of repeating the work of fetching them inside `playback.tests.ts` we want to simply use the contents fetched inside `content.tests.ts`

### Hacking up a basic global 'observable' inside Jest tests

#### content.tests.ts

So here's what we do. We make a global object `contentList` in `content.tests.ts` that will be imported in `playback.tests.ts` and we make a promise `contentListReady` that resolves when the content is downloaded (or times out at 6 seconds)

```typescript
export const contentList: Content[] = []
// internal variable to be set true when all assets are downloaded
let _contentsReady = false

// hack to wait for all assets to be downloaded so that they can be used in `playback.tests.ts`
export const contentListReady: Promise<Boolean> = new Promise((resolve, reject) => {
     // Poll every second to check if asset list is ready
     // If asset list is ready, resolve the promise
     // If asset list is not ready after 6 seconds, reject the promise
     // Either way, clear the interval and the timeout
    const looper = setInterval(() => {
        if (_assetListReady && assetList.length > 0) {
            clearTimeout(waiter)
            clearInterval(looper)
            resolve(true)
        }
    }, 1000)
    const waiter = setTimeout(() => {
        clearInterval(looper)
        clearTimeout(waiter)
        reject("Asset List not ready")
    }, 6000)
})

describe('content', () => {
    test('get home', async () => { /* ... */ })
    test('get categories', async () => { /* ... */ })
    test('get contents', async () => { 
        // contents are available here 
        contents.forEach( content => contentList.push(content) )
        _contentsReady = true
    })
})
```

In the due course of running all iterations of content tests, the \`contentList\` will get populated. When all the contents are fetched, we will set `_contentsReady` to true.

#### playback.tests.ts

In the `beforeAll` hook we wait for content list to be ready

```typescript
describe('playback', () => {
    beforeAll(async () => {
        info("Waiting for asset list to be ready...")
        if (await assetListReady) info("Asset list is ready")
        else error("Asset timed out")

    })

    test('playback sample', () => {
        expect(true).toBeTruthy()
    })
})
```

Thus even when jest actually starts all your tests in parallel, `playback.tests.ts` won't actually start executing its tests, till `content.tests.ts` either resolves or rejects the `contentListReady` promise.

Do remember to clear the timeout as well as the interval in that `contentListReady` promise, or else Jest will warn you about open handles at the end of the tests.