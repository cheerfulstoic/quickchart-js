quickchart js client
---

This is a Javascript client for [quickchart.io](https://quickchart.io), a web service for generating static charts.

# Installation

If you are using npm:

```
npm install quickchart
```

# Usage

This library provides a **QuickChart** object.  Import it, instantiate it, and set your [Chart.js](https://www.chartjs.org) config:

```js
const QuickChart = require('quickchart');

const myChart = new QuickChart();
myChart.setConfig({
  type: 'bar',
  data: { labels: ['Hello world', 'Foo bar'], datasets: [{ label: 'Foo', data: [1, 2] }] },
});
```

Use `getUrl()` on your quickchart object to get the encoded URL that renders your chart:

```js
console.log(myChart.getUrl());
// Prints:  https://quickchart.io/chart?c=%7Btype%3A%27bar%27%2Cdata%3A%7Blabels%3A%5B%27Hello+world%27%2C%27Foo+bar%27%5D%2Cdatasets%3A%5B%7Blabel%3A%27Foo%27%2Cdata%3A%5B1%2C2%5D%7D%5D%7D%7D&w=500&h=300&bkg=transparent&f=png
```

If you have a large or complicated chart, use `getShortUrl()` on your quickchart object to get a fixed-length URL using the quickchart.io web service:
```js
const data = await myChart.getShortUrl();
console.log(data.url);
// Prints: https://quickchart.io/chart/render/f-a1d3e804-dfea-442c-88b0-9801b9808401
```

The URLs produce this chart image:

<img src="https://quickchart.io/chart?c=%7Btype%3A%27bar%27%2Cdata%3A%7Blabels%3A%5B%27Hello+world%27%2C%27Foo+bar%27%5D%2Cdatasets%3A%5B%7Blabel%3A%27Foo%27%2Cdata%3A%5B1%2C2%5D%7D%5D%7D%7D&w=500&h=300&bkg=transparent&f=png" width=500 />

## Customizing your chart

### setConfig(chart)

Use this config to customize the Chart.js config object that defines your chart.  You must set this before generating a URL!

### setWidth(width: int)

Sets the width of the chart in pixels.  Defaults to 500.

### setHeight(height: int)

Sets the height of the chart in pixels.  Defaults to 300.

### setFormat(format: string)

Sets the format of the chart.  Defaults to `png`.  `svg` is also valid.

### setBackgroundColor(color: string)

Sets the background color of the chart.  Any valid HTML color works.  Defaults to `#ffffff` (white).  Also takes `rgb`, `rgba`, and `hsl` values.

### setDevicePixelRatio(ratio: float)

Sets the device pixel ratio of the chart.  This will multiply the number of pixels by the value.  This is usually used for retina displays.  Defaults to 1.0.

## Getting URLs

There are two ways to get a URL for your chart object.

### getUrl(): string

Returns a URL that will display the chart image when loaded.

### getShortUrl(): Promise

Uses the quickchart.io web service to create a fixed-length chart URL that displays the chart image.  The Promise resolves with the following structure:

```js
{
  success: bool,
  url: string,
}
```

Here's an example:

```js
{
  success: true,
  url: 'https://quickchart.io/chart/render/f-a1d3e804-dfea-442c-88b0-9801b9808401',
}
```

Note that short URLs expire after a few days for users of the free service.  You can [subscribe](https://quickchart.io/pricing/) to keep them around longer.

## More examples

Check out the `examples/` directory to see other usage.  Here's a simple test that uses some of the custom parameters:

```js
const QuickChart = require('../index');

const qc = new QuickChart();

qc.setConfig({
  type: 'bar',
  data: { labels: ['Hello world', 'Foo bar'], datasets: [{ label: 'Foo', data: [1, 2] }] },
});
qc.setWidth(500).setHeight(300).setBackgroundColor('transparent');

console.log(qc.getUrl());
// https://quickchart.io/chart?c=%7Btype%3A%27bar%27%2Cdata%3A%7Blabels%3A%5B%27Hello+world%27%2C%27Foo+bar%27%5D%2Cdatasets%3A%5B%7Blabel%3A%27Foo%27%2Cdata%3A%5B1%2C2%5D%7D%5D%7D%7D&w=500&h=300&bkg=transparent&f=png
```

Here's a more complicated chart that includes some Javascript:

```js
qc.setConfig({
  type: 'bar',
  data: {
    labels: ['January', 'February', 'March', 'April', 'May'],
    datasets: [
      {
        label: 'Dogs',
        data: [50, 60, 70, 180, 190],
      },
    ],
  },
  options: {
    scales: {
      yAxes: [
        {
          ticks: {
            callback: function (value) {
              return '$' + value;
            },
          },
        },
      ],
    },
  },
});
qc.setWidth(500).setHeight(300).setBackgroundColor('#0febc2');

console.log(qc.getUrl());
// https://quickchart.io/chart?c=%7Btype%3A%27bar%27%2Cdata%3A%7Blabels%3A%5B%27January%27%2C%27February%27%2C%27March%27%2C%27April%27%2C%27May%27%5D%2Cdatasets%3A%5B%7Blabel%3A%27Dogs%27%2Cdata%3A%5B50%2C60%2C70%2C180%2C190%5D%7D%5D%7D%2Coptions%3A%7Bscales%3A%7ByAxes%3A%5B%7Bticks%3A%7Bcallback%3Afunction+%28value%29+%7B%0A++return+%27%24%27+%2B+value%3B%0A%7D%7D%7D%5D%7D%7D%7D&w=500&h=300&bkg=%230febc2&f=png
```

As we customize these charts, the URLs are getting a little long for my liking.  There's a `getShortUrl` function that uses the QuickChart.io web service to generate a short(er), fixed-length URL:

```js
// Fill the chart with data from 0 to 100
const data = [...Array(100).keys()];
qc.setConfig({
  type: 'bar',
  data: { labels: ['Hello world', 'Foo bar'], datasets: [{ label: 'Foo', data }] },
});

async function printShortUrl() {
  const resp = await qc.getShortUrl();
  if (!resp.success) {
    console.log('Something went wrong, could not generate shorturl');
  } else {
    console.log(resp.url);
  }
}
printShortUrl();
// https://quickchart.io/chart/render/f-a1d3e804-dfea-442c-88b0-9801b9808401
```