# KPI Dashboard Notes

This documents the Donut and horizontal progress stat components displayed prominently in the Spotlight tab of the KPI dashboard.

## 🍩 Donut/Radial/Pie Gauge

There's javascript class called `DonutChart` in `donut-chart.js`.

### Argument Structure

It takes a blob of arguments structered as:

```typescript
type DonutChartArgs = {
    selector: string;
    options: {
        radius: number;
        strokeWidth: number;
        innerValue: string;
        innerLabel: string;
    };
    data: {
        name: string;
        value: number;
        formattedValue: string;
    }[]
};
```

#### Argument Details

- `selector` should be the ID of the HTML element you are targetting.
- `options.radius` is now always `137.5` but is not reflected in the code and should be passed in as `137.5`.
- `options.strokeWidth` should be passed in as `41.15`
- `options.innerLabel` shows up as the smaller text in the center of the chart, visually speaking.
- `options.innerValue` is the bigger text in the center of the chart.
- `[data]` is an array of objects that describe the donut chart segments.
- `[data].name` shows up as one of the metric key labels below the chart.
- `[data].value` determines the length of the segment.
- `[data].formattedValue` shows up as a value in the metric keys below the chart.

#### Examples

The following is what is currently being passed in, and an example of how the data should be structured. *Note* that the class is instantiated with the `new` keyword.

```js
    new DonutChart({
        selector: '#Tile1__Donut',
        options: {
            radius: 137.5,
            strokeWidth: 41.15,
            innerValue: '500',
            innerLabel: 'Total Registrations'
        },
        data: [
            {
                name: 'XPR',
                value: 500,
                formattedValue: '500'
            },
            {
                name: 'LGE',
                value: 300,
                formattedValue: '300'
            },
            {
                name: 'EVT',
                value: 200,
                formattedValue: '200'
            },
            {
                name: 'TMS',
                value: 200,
                formattedValue: '200'
            }
        ]
    })
```

For reference, the serialized form also works:

```json
{
    "selector": "#Tile1__Donut",
    "options": {
        "radius": 137.5,
        "strokeWidth": 41.15,
        "innerValue": "500",
        "innerLabel": "Total Registrations"
    },
    "data": [
        {
            "name": "XPR",
            "value": 500,
            "formattedValue": "500"
        },
        {
            "name": "LGE",
            "value": 300,
            "formattedValue": "300"
        },
        {
            "name": "EVT",
            "value": 200,
            "formattedValue": "200"
        },
        {
            "name": "TMS",
            "value": 200,
            "formattedValue": "200"
        }
    ]
}
```

#### Segment Values

- `value` can accept negative numbers. The segment will be colored red automatically.
- `isGrayed` is an optional value. If set to `true` the segment value will be light gray, and the corresponding metric key value will be colored a darker gray. It represents the concept of a _budgetted value_, i.e. a fixed maximum.

Example:

```json
{
    "name": "TVL",
    "value": -1500,
    "formattedValue": "-$15K"
}
```

```json
{
    "name": "Sent",
    "value": 1000,
    "formattedValue": "1000",
    "isGrayed": true
}
```

## ➡️ Progress Stats (Single/Group)

There is a javascript class called `ProgressStat` in `progress-stat.js`. A progress stat is a single component that has an icon, a horizontal progress bar that comprised of 2 to 3 bars (the first being the maximum containing bar), bar labels and an indicator.

It also has a method for creating a list group of progress-stats.

### Single Progress Stat Arguments

It takes a blob of arguments structured as:

```typescript
type SingleProgressStatArgs = {
    selector: string
    options: {
        img: URL
        title: string
        link: URL
        formattedValue: string
        formattedValueType: 'positive' | 'negative' | 'even' | null
    },
    data: {
        name: string
        formattedValue: string
        value: number
    }[]
}
```

#### Argument Details

- `selector` should be the ID of the HTML element you are targetting.
- `options.img` optional. Shows up on the left side of the bar.
- `options.title`
- `options.link` optional. If present, the title becomes a link to this URL.
- `options.formattedValue` optional. This shows up on the right side of the bar, typically as a percentage like "18%" and an arrow icon if needed.
- `options.formattedValueType` optional. If `formattedValue` is present, it adds color and an arrow according to type.
- `[data]` is an array of data series that help describe the bar properties. *Data must be sorted from the maximum value series to the least to line up the labels*.
- `[data].name` shows up as a label below the bar with a dot that matches the color of the bar being described.
- `[data].value` determines the length of the bar.
- `[data].formattedValue` appears next to the label created by `name`.

#### Multi-Variable bars

There's always a maximum bar, typically "Budget", in `ProgressStat`. Multiple variables can be shown as bars (right now only 2).

#### Bar colors

The `ProgressStat` component will color the bar automatically according to percentage.

#### Example

Here's an example of a progress-stat with multiple variables. A single variable bar would only have 2 items in the array.

```json
{
    "selector": "#Tile2__ProgressStats",
    "data": [
        {
            "name": "Budget",
            "formattedValue": "$5K",
            "value": 100
        },
        {
            "name": "Invoiced",
            "formattedValue": "$5K",
            "value": 97
        },
        {
            "name": "Paid",
            "formattedValue": "$5K",
            "value": 90
        }
    ],
    "options": {
        "img": "https://i.postimg.cc/PqrMKDJW/brand-academyboys.png",
        "title": "2023 Country Lax Festival",
        "link": "#",
        "formattedValue": "18%",
        "formattedValueType": "positive"
    }
}
```

### Progress Stat Group

There is a method called `ProgressStat.createGroup(...)`. It takes a blob argument structed as:

```typescript
type ProgressStatGroupBlob = {
    selector: string
    title: string?
    progressStats: { ...SingleProgressStatArgs }[]
}
```

#### Details

- `selector` is the HTML element in the `.kpi-tile-body` element to be targetted.
- `title` optional. Fills in the `h3` element above the group.
- `progressStats` the array of single-progress-stat arguments.

#### Example

The following is an example of a progress-stat-group with 2 progress stats.

```json
{
    "selector": "#Tile1__ProgressStats",
    "title": "Most Active Program Registrations",
    "progressStats": [
        {
            "options": {
                "img": "https://i.postimg.cc/PqrMKDJW/brand-academyboys.png",
                "title": "2023 Chicago FCU Elite Camp - North Shore Camp",
                "link": "#",
                "formattedValue": "10%",
                "formattedValueType": "positive"
            },
            "data": [
                {
                    "name": "Budget",
                    "formattedValue": "$5K",
                    "value": 100
                },
                {
                    "name": "Paid",
                    "formattedValue": "$5K",
                    "value": 90
                }
            ]
        },
        {
            "options": {
                "img": "https://i.postimg.cc/PqrMKDJW/brand-academyboys.png",
                "title": "XPR Programs Roll Up",
                "link": "#",
                "formattedValue": "20%",
                "formattedValueType": "negative"
            },
            "data": [
                {
                    "name": "Budget",
                    "formattedValue": "$5K",
                    "value": 100
                },
                {
                    "name": "Paid",
                    "formattedValue": "$5K",
                    "value": 80
                }
            ]
        },
    ]
}
```
