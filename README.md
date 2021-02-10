# Dependency

```bash
npm i puppeteer
```

# Project

```jsx
const puppeteer = require('puppeteer')

function extractedItems() {
    const extractedItems = Array.from(document.querySelectorAll('#boxes > div.box'))
    const items = extractedItems.map(element => element.innerText)
    return items 
}

async function scrapeInfiniteScrollItems(page, extractedItems, targetItemCount, delay = 1000) {
    let items = []
    try {
        let previousHeight 
        while(items.length < targetItemCount) {
            items = await page.evaluate(extractedItems)
            previousHeight = await page.evaluate('document.body.scrollHeight')
            await page.evaluate('window.scrollTo(0, document.body.scrollHeight)') //function 실행.. scroll to 
            await page.waitForFunction(`document.body.scrollHeight > ${previousHeight}`) 
            await page.waitForTimeout(delay)
        }
    } catch(error) {
        console.error(error)
    }
    return items 
}
 
async function main() {
    const browser = await puppeteer.launch({headless: false})
    const page = await browser.newPage()
    page.setViewport({ width: 1280, height: 926 })

    await page.goto('https://intoli.com/blog/scrape-infinite-scroll/demo.html')

    const targetItemCount = 100
    const items = await scrapeInfiniteScrollItems(page, extractedItems, targetItemCount)

    console.log(items)

}

main()
```