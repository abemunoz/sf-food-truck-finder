# sf-food-truck-finder

## Project setup

It's a Vue app, you got this! 😉

## Project URL

[https://abemunoz.github.io/sf-food-truck-finder/](https://abemunoz.github.io/sf-food-truck-finder/)

## About

A simple webapp to find local food trucks in San Francisco with the ability to filter food trucks that are currenlty open or closest to you. [San Francisco government’s website](https://dev.socrata.com/foundry/data.sfgov.org/jjew-r69b) was used to gather and query the data.

Vue was used since it is used at URBN, for me to get more practice, and not to mention it's a great framework!. There are still a lot of optimizations/additions I would like to do like breaking down the application further, integrating Vuex, enhance the search results transition, converting your time zone to PST, test cases, etc.

The API has querying ability similar to [SQL](https://dev.socrata.com/docs/queries/) so I leveraged that to find the current open restaurants. In order to get your closest food trucks I used the browser geolocation API to get your location and the haversine formula since the API provided the latitude and longitude of the food truck. So it does not give you shortest commute time but making another API call to Google Maps or the like would have been costly and more times than not haversine distance is also the shortest commute time.

Hope it can help you find the next food truck you want to try out in SF! 😋
