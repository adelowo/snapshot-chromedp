<p align="center">
 <img src="https://user-images.githubusercontent.com/19553554/52535979-c0d0e680-2d8f-11e9-85c8-2e9f659e7c6f.png" width=300 height=300 />
</p>

<h1 align="center">snapshot-chromedp</h1>
<p align="center">
    <em> ⚔️ The extension for render image of go-echarts.</em>
</p>

The `snapshot-chromedp` is powered by the [`chromedp`](https://github.com/chromedp/chromedp)
which enable to snapshot charts in HTML to images.  
[`chromedp`](https://github.com/chromedp/chromedp) is a faster, simpler way to drive browsers supporting the Chrome
DevTools Protocol, which means this extension works under Chrome DevTools Protocol supporting.

> [!WARNING]
> This is a fork to allow writing the file to an io.Writer instead. I am not upstreaming since
> it was a quick hack. If and when i do find time, will clean up and push changes upstream

## Install

> Support since go-echarts v2.4.0-rc1

```go
require github.com/go-echarts/snapshot-chromedp v0.0.5
```

## Configuration

Normally, if you only need render the chart to image, the handy call is enough.

```go
    render.MakeChartSnapshot(myChart.RenderContent(), "my-chart.png")
    render.MakeChartSnapshotWriter(myChart.RenderContent(), os.Stdout)
```

By default, it will generate the HTML content first and do the snapshot, clean the HTML then.

If you want to have full control on it, the whole config list here.

```go
type SnapshotConfig struct {
    // RenderContent the content bytes of charts after rendered
    RenderContent []byte
    // Path the path to save image
    Path string
    // FileName image name
    FileName string
    // Suffix image format, png, jpeg
    Suffix string
    // Quality the generated image quality, aka pixelRatio
    Quality int
    // KeepHtml whether keep the generated html also, default false
    KeepHtml bool
    // HtmlPath where to keep the generated html, default same to image path
    HtmlPath string
    // Timeout  the timeout config
    Timeout time.Duration
    // MultiCharts ONLY enable it when you have multi charts in the single page, better to set larger quality
    MultiCharts bool
}
```

## Example

> [!NOTE]
> You must disable the chart's `Animation` option to ensure the chart being rendered immediately.

```go
package main

import (
 "github.com/go-echarts/go-echarts/v2/charts"
 "github.com/go-echarts/go-echarts/v2/opts"
 "github.com/go-echarts/snapshot-chromedp/render"
 "math/rand"
)

var (
 itemCnt = 7
 weeks   = []string{"Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"}
)

func generateBarItems() []opts.BarData {
 items := make([]opts.BarData, 0)
 for i := 0; i < itemCnt; i++ {
  items = append(items, opts.BarData{Value: rand.Intn(300)})
 }
 return items
}

func barTitle() *charts.Bar {
 bar := charts.NewBar()
 bar.SetGlobalOptions(
  charts.WithInitializationOpts(opts.Initialization{
   BackgroundColor: "#FFFFFF",
  }),
  // Don't forget disable the Animation
  charts.WithAnimation(false),
  charts.WithTitleOpts(opts.Title{
   Title:    "title and legend options",
   Subtitle: "go-echarts is an awesome chart library written in Golang",
   Link:     "https://github.com/go-echarts/go-echarts",
   Right:    "40%",
  }),
  charts.WithLegendOpts(opts.Legend{Right: "80%"}),
 )
 bar.SetXAxis(weeks).
  AddSeries("Category A", generateBarItems()).
  AddSeries("Category B", generateBarItems())
 return bar
}

func barTooltip() *charts.Bar {
 bar := charts.NewBar()
 bar.SetGlobalOptions(
  charts.WithAnimation(false),
  charts.WithTitleOpts(opts.Title{Title: "tooltip options"}),
  charts.WithLegendOpts(opts.Legend{Right: "80px"}),
 )
 bar.SetXAxis(weeks).
  AddSeries("Category A", generateBarItems()).
  AddSeries("Category B", generateBarItems())
 return bar
}

func main() {
 barTitleChart := barTitle()
 barTooltipChart := barTooltip()
 render.MakeChartSnapshot(barTitleChart.RenderContent(), "my-bar-title.png")
 render.MakeChartSnapshot(barTooltipChart.RenderContent(), "my-bar-tooltip.jpg")
}
```

Snapshot multi charts in one `Page` is also available now.  
Please also make sure each chart animation disabled.  
You can simply enable it by:

```go
render.MakeSnapshot(NewSnapshotConfig(asset.RenderPageContent(), fileImage, func(config *SnapshotConfig) {
         // current page contains multi charts
  config.MultiCharts = true
  // higher quality
  config.Quality = 100
 })

```

# Special Thanks

[chromedp](https://github.com/chromedp/chromedp)

# License

MIT [@Koooooo-7](https://github.com/Koooooo-7)
