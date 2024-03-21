

# Most Streamed Songs of 2023 Power BI Dashboard

### Dashboard Link

## Problem Statement

How can we develop a Power BI dashboard that showcases Spotify's most streamed songs of 2023 and provides a comprehensive overview of the music streaming landscape on Spotify in 2023? The dashboard should feature visualizations like bar charts, line graphs, and tables to present the top songs, artists, and genres based on streaming data. It should allow users to interact with the dashboard to explore trends over time, compare different songs or artists, and view detailed information about each track's performance. The main objective is to create a dashboard that highlights the most popular songs and artists of the year and provides insights into the music streaming landscape on Spotify in 2023.


### Steps followed 

- Step 1: Make a glass morphism background in Microsoft PowerPoint
- Step 2: Open Microsoft Power BI and create a date column".
         DATE([released_year, [released_month],         [released_day]])
- Step 3: it was observed that the streams column was not in whole number implicitly because of some missing values, later on, it was converted by the power query editor.
- Step 4: An external tool Bravo is used to make a calendar table which can be used to make a calendar table which can be used and directly connect with the date column from the Spotify dataset.
- Step 5: Slicers are added for Released date(date), Track names, Artist names, and years which filters the data  
- Step 6: A new measure called max streams is made which is used to visualize the max streams of streams.
                Max_playd_song = MAX('Spotify  Dataset'[streams])
                this measure is visualized as a KPI card along with track name, artist name, artist count, streams, and date to show the top streamed song.
- Step 7: Another KPI card is used to visualize quantifier characteristics of the song like acousticness, danceability, liveliness, speechiness, and valence. 
- Step 8: Some measures are made such as "top song streams" which calculates the sum of streams and streams equal to the max of streams.

        top_strong_streams = CALCULATE(SUM('Spotify Dataset'[streams]), 'Spotify Dataset'[streams] = MAX('Spotify Dataset'[streams])).

another measure is "Avg stream per year" which calculates the average of streams removing all filters except the year.

        Average_stream_per_year = CALCULATE(AVERAGE('Spotify Dataset'[streams]), ALLEXCEPT('Spotify Dataset', 'Date'[Year])).

top song vs. average val is calculating the percentage difference which is top song streams minus average stream per year divided by average stream per year.
        
        top_song_vs_avg_val = DIVIDE([top_strong_streams] - [Average_stream_per_year], [Average_stream_per_year])

this DAX measure is formatted to find the up and down arrow depending on whether it is positive or negative.

        top_song_vs_avg = VAR x = [top_song_vs_avg_val] RETURN IF(x>0, FORMAT(x, "#.0%") & UNICHAR(9650), FORMAT(x, "#.0%") & UNICHAR(9669))

- Step 9: Two cards visual were added to the report one representing the average stream per year and another for top song vs average.
- Step 10: An image URL which was used to show the images of each track in the visual report was visualized by HTML content which is a visual that needs to be imported from Power BI as a new visual. 
- Step 11: To show the images from the URL, chat GPT is used to get the HTML code which uses the URL to show the mid-section with rounded corners. the code was copied and pasted into the DAX code which calculates the top song cover URL where the max stream is the max of stream. and in this DAX code the HTML code is pasted 
        
                image_html = VAR x = CALCULATE(MAX('Spotify Dataset'[cover_url]), 'Spotify Dataset'[streams] = MAX('Spotify Dataset'[streams]))
        RETURN

        "

        <!DOCTYPE html>
        <html lang='en'>
        <head>
        <meta charset='UTF-8'>
        <title>Image Cropping</title>
        <style>
                .image-container {
                width: 332.97px; /* Width of the container */
                height: 95.62px; /* Height of the container */
                overflow: hidden; /* Hide parts of the image that don't fit */
                border-radius: 15px; /* Rounded corners */
                position: relative; /* Relative positioning for the child element */}

        .image {
                object-fit: cover; /* Cover the entire container */
                object-position: center; /* Center the image */
                width: 100%; /* Full width */
                height: 100%; /* Full height */}
        </style>
        </head>
        <body>
        <div class='image-container'>
                <img src='"&x&"' alt='Album Cover' class='image'>
        </div>
        </body>
        </html>
        "

- Step 12: In the report, DENEB visual is used for visualizing the energy of the song which is imported from.

        https://github.com/PBI-David/Deneb-Showcase

 which is a unit chart

<img width="127" alt="energy ring" src="https://github.com/nishant696/Most-Streamed-Songs-of-2023-Power-BI-Dashboard/assets/73593128/1568a72c-c578-468d-b3f4-ea3368d1393f">

before being used in the visual this JSON file is modified according to the data report with the following code

                {
        "$schema": "https://vega.github.io/schema/vega/v5.json",
        "width": 175,
        "height": 150,
        "padding": 10,
        "align": "fill",
        "signals": [
        {
        "name": "textGradient",
        "update": "{gradient: 'linear', stops: [{offset: 0, color: '#036d19'}, {offset: 1, color: '1db954'}]}"
        },
        {
        "name": "percent",
        "update": "0",
        "on": [
                {
                "events": {
                "type": "timer",
                "throttle": 0
                },
                "update": "round(data('dataset')[0]['Average of energy_%'])"
                }
        ]
        }
        ],
        "data": [
        {"name": "dataset"},
        {
        "name": "back",
        "values": [],
        "transform": [
                {"type": "sequence", "start": 0, "stop": 100, "step": 1, "as": "val"},
                {"type": "formula", "expr": "1", "as": "t"},
                {"type": "pie", "field": "t", "startAngle": {"signal": "0"}, "endAngle": {"signal": "2*PI"}}
        ]
        },
        {
        "name": "front",
        "values": [],
        "transform": [
                {"type": "sequence", "start": 0, "stop": {"signal": "percent"}, "step": 1, "as": "val"},
                {"type": "formula", "expr": "1", "as": "t"},
                {"type": "pie", "field": "t", "startAngle": {"signal": "0"}, "endAngle": {"signal": "((2*PI)/100)*percent"}}
        ]
        }
        ],
        "scales": [
        {
        "name": "color",
        "type": "linear",
        "domain": {"data": "back", "field": "val"},
        "range": ["#036d19","1db954"]
        }
        ],
        "marks": [
        {
        "type": "arc",
        "from": {"data": "back"},
        "encode": {
                "enter": {
                "fill": {"value": "#9092a1"},
                "x": {"signal": "width / 2"},
                "y": {"signal": "height / 2"}
                },
                "update": {
                "startAngle": {"field": "startAngle"},
                "endAngle": {"field": "endAngle"},
                "padAngle": {"value": 0.015},
                "innerRadius": {"signal": "(width / 2) - 15"},
                "outerRadius": {"signal": "width / 2"}
                }
        }
        },
        {
        "type": "arc",
        "from": {"data": "front"},
        "encode": {
                "enter": {
                "fill": {"scale": "color", "field": "val"},
                "x": {"signal": "width / 2"},
                "y": {"signal": "height / 2"}
                },
                "update": {
                "startAngle": {"field": "startAngle"},
                "endAngle": {"field": "endAngle"},
                "padAngle": {"value": 0.015},
                "innerRadius": {"signal": "(width / 2) - 15"},
                "outerRadius": {"signal": "width / 2"}
                }
        }
        },
        {
        "type": "arc",
        "data": [{"a": 1}],
        "encode": {
                "enter": {
                "fill": {"value": "#9092a1"},
                "x": {"signal": "width / 2"},
                "y": {"signal": "height / 2"}
                },
                "update": {
                "startAngle": {"signal": "0"},
                "endAngle": {"signal": "2*PI"},
                "innerRadius": {"signal": "(width / 2) - 25"},
                "outerRadius": {"signal": "(width / 2) - 20"}
                }
        }
        },
        {
        "type": "text",
        "encode": {
                "update": {
                "text": {"signal": "percent + '%'" },
                "align": {"value": "center"},
                "fontWeight": {"value": "bold"},
                "fill": {"signal": "textGradient"},
                "x": {"signal": "width / 2"},
                "y": {"signal": "70"},
                "fontSize": {"value": 20}
                }
        }
        },
        {
        "type": "text",
        "encode": {
                "update": {
                "text": {"value": "energy level"},
                "align": {"value": "center"},
                "fontWeight": {"value": "bold"},
                "fill": {"value": "#9092a1"},
                "x": {"signal": "width / 2"},
                "y": {"signal": "90"},
                "dy": {"value": -5},
                "fontSize": {"value": 20}
                }
        }
        }
        ]
        }
in this JSON code the color codes, data, width, and height of the visual are changed 

- Step 13: Another visual deneb is used which is the heat map which is used to visualize the track name, day of week and month.

this visual is taken from the below GitHub profile as JSON code

        https://github.com/PowerBI-tips/Deneb-Templates/blob/main/templates/heatmap%20with%20bars%20-%20red%20themed.json

<img width="151" alt="heat map" src="https://github.com/nishant696/Most-Streamed-Songs-of-2023-Power-BI-Dashboard/assets/73593128/d8d2935f-ca0a-4851-87bc-c5138e05b137">

The JSON code for this visual is 

        {
        "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
        "usermeta": {
        "deneb": {
        "build": "1.4.0.0",
        "metaVersion": 1,
        "provider": "vegaLite",
        "providerVersion": "5.4.0"
        },
        "interactivity": {
        "tooltip": true,
        "contextMenu": true,
        "selection": true,
        "highlight": true,
        "dataPointLimit": 50
        },
        "information": {
        "name": "heatmap with bars - themed",
        "description": "Heatmap, uses the 5th theme color as bar chart, 8th color for font, and sentiment colors for gradient. X and Y axis are using Date table from Bravo, and are sorted for X= Month, Y = Day of Week. If you are using a different ordinal column, please make sure the columns are properly sorted",
        "author": "Injae Park",
        "uuid": "57ee5f3a-4a51-4230-8885-7db391a05805",
        "generated": "2022-10-19T11:12:58.962Z"
        },
        "dataset": [
        {
                "key": "Day of Week",
                "name": "Day of Week",
                "description": "From Bravo, is 3 character abbreviation, i.e. Mon, Tue, Wed",
                "type": "text",
                "kind": "column"
        },
        {
                "key": "Month",
                "name": "Month",
                "description": "From Bravo, is 3 character abbreviation, i.e. Jan, Feb, Mar",
                "type": "text",
                "kind": "column"
        },
        {
                "key": "track",
                "name": "track",
                "description": "",
                "type": "numeric",
                "kind": "measure"
        }
        ]
        },
        "config": {
        "autosize": {
        "type": "fit",
        "contains": "padding"
        },
        "view": {"stroke": "transparent"}
        },
        "data": {"name": "dataset"},
        "spacing": 5,
        "bounds": "flush",
        "vconcat": [
        {
        "height": 22,
        "width": {"step": 22},
        "mark": {
                "type": "bar",
                "stroke": null,
                "cornerRadiusEnd": 4,
                "tooltip": true,
                "color": {"expr": "pbiColor(4)"}
        },
        "encoding": {
                "x": {
                "field": "Month",
                "sort": [
                "Jan",
                "Feb",
                "Mar",
                "Apr",
                "May",
                "Jun",
                "Jul",
                "Aug",
                "Sep",
                "Oct",
                "Nov",
                "Dec"
                ],
                "axis": null
                },
                "y": {
                "field": "track",
                "aggregate": "mean",
                "axis": null
                }
        }
        },
        {
        "spacing": 5,
        "bounds": "flush",
        "hconcat": [
                {
                "mark": {
                "type": "rect",
                "stroke": "white",
                "tooltip": true,
                "cornerRadius": 6
                },
                "width": {"step": 22},
                "height": {"step": 22},
                "encoding": {
                "y": {
                "field": "Day of Week",
                "type": "ordinal",
                "title": "",
                "sort": [
                        "Mon",
                        "Tue",
                        "Wed",
                        "Thu",
                        "Fri",
                        "Sat",
                        "Sun"
                ],
                "axis": {
                        "domain": false,
                        "ticks": false,
                        "labels": true,
                        "labelAngle": 0,
                        "labelPadding": 5,
                        "labelColor": {
                        "expr": "pbiColor(7)"
                        }
                }
                },
                "x": {
                "field": "Month",
                "type": "ordinal",
                "sort": [
                        "Jan",
                        "Feb",
                        "Mar",
                        "Apr",
                        "May",
                        "Jun",
                        "Jul",
                        "Aug",
                        "Sep",
                        "Oct",
                        "Nov",
                        "Dec"
                ],
                "axis": {
                        "domain": false,
                        "ticks": false,
                        "labels": true,
                        "labelAngle": 0,
                        "labelColor": {
                        "expr": "pbiColor(7)"
                        },
                        "titleColor": {
                        "expr": "pbiColor(7)"
                        }
                }
                },
                "color": {
                "aggregate": "mean",
                "field": "track",
                "type": "quantitative",
                "title": "Orders",
                "scale": {
                        "scheme": "pbiColorLinear"
                },
                "legend": null
                }
                }
                },
                {
                "mark": {
                "type": "bar",
                "stroke": null,
                "cornerRadiusEnd": 4,
                "tooltip": true,
                "color": {
                "expr": "pbiColor(4)"
                }
                },
                "width": 22,
                "height": {"step": 22},
                "encoding": {
                "y": {
                "field": "Day of Week",
                "axis": null,
                "sort": [
                        "mon",
                        "tue",
                        "wed",
                        "thu",
                        "fri",
                        "sat",
                        "sun"
                ]
                },
                "x": {
                "field": "track",
                "type": "quantitative",
                "aggregate": "mean",
                "axis": null
                }
                }
                }
        ]
        }
        ]
        }
this modified code includes changes in days at line 112 which are case-sensitive

removing legend at line 169 by null

removed time title at line 133 

The last change made is the size of the visual and the spacing between the horizontal and vertical bars

- Step 14: The formatting of the report is done by customizing the theme by the Spotify player color palette which gave the hex code of the colors of the Spotify player.
these hex codes are input into the theme 

<img width="264" alt="theme color sc" src="https://github.com/nishant696/Most-Streamed-Songs-of-2023-Power-BI-Dashboard/assets/73593128/9627348a-440c-4e23-ac43-9afb7c8c3cea">

the other changes in the theme include every other element being white such as text, visuals, colors, page and filter pane 

the color codes are sourced from the following web URL 

        https://www.color-hex.com/color-palette/53188  

        Note: By changing the theme of the report the color of the heat map is also changed

- Step 15: The date, track name, artist name, and year visual slicers are changed, artist name and track name visuals are changed to a dropdown list, and the year visual slicer is changed to tile.

<img width="444" alt="d,t,a,y sc" src="https://github.com/nishant696/Most-Streamed-Songs-of-2023-Power-BI-Dashboard/assets/73593128/7e7c5d7d-8efc-44c1-a37d-ac4444ebe2ad">

        
 - Step 16: For the top song vs. average val card the conditional formatting is done ie.
if the proportion is more than zero then the color must be green and if it is worse than zero then it is red.

 
 - Step 17: For the images of tracks the track names are overlapped on the image visual with no background and altered to horizontally to fit the size of the visual. 

- Step 18: The last step is to add a bookmark button or a clear all slicers button and turn off the button text to clear all slicers

 # Report Snapshot (Power BI DESKTOP)

 <img width="602" alt="dashbord sc" src="https://github.com/nishant696/Most-Streamed-Songs-of-2023-Power-BI-Dashboard/assets/73593128/a2b7dda9-91b7-4460-9e91-750b55bc5ff6">




