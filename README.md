# Fun with calendars

Depending on your depth of understanding of JavaScript and how dates and `input=date` works with browsers, you may want to review the [dynamic-calendar](https://github.com/blackfalcon/dynamic-calendar) repo before digging into this one.

In the *dynamic-calendar* repo, I went through exhaustive measures to achieve some pretty cool things. For this demo, I forked the *dynamic-calendar* repo and added support for two new libraries, moment.js and pikaday.js to accomplish some additional tasks.

## The HTML

For this demo, there is a slight difference in the HTML. We need the `type=date` input, a container to display the prettified date and then a container where we want the new calendar UI to appear.

```
<input id="event-date" type="date" class="hide" />
<div id="display-cal-value"></div>
<div id="cal-container" class="cal-container"></div>
```

The reason for the calendar container `div` is mainly to have better control over how it appears in the view. You don't need it, so that's up to you.

## moment.js

For this demo, I replaced some of the manual functions to determine date and format with those of functions from moment.js. This can be seen in two functions, `setCurrentInputDate()` and `displayOrdinalDate()`. As illustrated below, you will see how I used the moment API to get and set the current dates.

```
// parse date value and apply ordinal values
function displayOrdinalDate(date) {
    var dateArray = date,
        dateFormat = 'YYYY-MM-DD',
        year = moment( dateArray, dateFormat ).year(),
        month = moment( dateArray, dateFormat ).month(),
        day = moment( dateArray, dateFormat ).date(),

        // updates date value to that of value with ordinal indicator,
        ordinalDay = ordinalSuffix(day),

        // updates month value to that of month name
        displayMonth = utcMonthString(month);

    // uncomment the console.log to see the returned array
    // console.log(dateArray);

    return displayMonth + " " + ordinalDay + "," + " " + year;
};

// Addresses time-zone support
function setCurrentInputDate() {
    var currentDate = moment().format('YYYY-MM-DD');
    return currentDate;
};
```

## pikaday.js

Using pipkaday.js changed a number of things from the previous repo example. Mainly, it has a fully expressed API for many of the features we had to manually address. To address the functionality, I wrapped all the code into a single function, `makeItPikaday()`. Again, we want to restrict this to only desktop experiences, so it's all wrapped inside a `window.matchMedia()` if statement as well.

Note, as we instantiate a new `picker` object from the `Pikaday` prototype, we then pass in the features we want to support. See all the comments in the example below.

```
// function to instantiate the Pickaday functionality
function makeItPikaday() {
    // only want function to fire on desktop devices
    var mq = window.matchMedia('(min-width: 769px)');

    if(mq.matches) {
        var dateFormat = 'YYYY-MM-DD';
        var currentDate = setCurrentInputDate();
        var myMomentDate = moment(currentDate, dateFormat);
        var thisYear = moment(moment(currentDate, dateFormat ).year());
        var maxYear = thisYear + 10;

        var picker = new Pikaday(
            {
                // input filed with date value
                field: document.getElementById('event-date'),
                // click event trigger to open calendar
                trigger: document.getElementById('display-cal-value'),
                // Sets calendar to start on Sunday
                firstDay: 0,
                // sets minimum date of calendar to be TODAY
                minDate: new Date(myMomentDate),
                // sets maximum calendar date
                maxDate: new Date(maxYear, 12, 31),
                // sets range of year drop-down option
                yearRange: [thisYear, maxYear],
                // sets container for where calendar will be rendered
                container: document.getElementById('cal-container'),
                // internationalization
                i18n: {
                    previousMonth : '< Prev month',
                    nextMonth     : 'Next month >',
                    months        : ['January','February','March','April','May','June','July','August','September','October','November','December'],
                    weekdays      : ['Sunday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday'],
                    weekdaysShort : ['S','M','T','W','T','F','S']
                },
            });
        }
};
```

## pickaday.css

The pikaday.js project did a great job in modularizing it's CSS, so it was super easy to theme this after some clicking around in the Chrome Inspector.

### The diff

To make this project a little easier to follow through, I made individual commits so that you can see the difference in the HTML/CSS/JS.

* [HTML diff](https://github.com/blackfalcon/dynamic-cal-pikaday-support/commit/8accd037021ad37cd909d7f54ec50623b42a0074)
* [CSS diff](https://github.com/blackfalcon/dynamic-cal-pikaday-support/commit/4e7b29cf19e12fbd83c843d8ff163c21c0d6d58b)
* [JS diff](https://github.com/blackfalcon/dynamic-cal-pikaday-support/commit/eb10eb8a8e83e02a10a566da82a53c977e170904)

The JavaScript diff is the most complex of course. Notice the removal of custom functions like in place of the moment.js lib. Like, we don't need to split the date and convert back to numbers again. This also necessitates the need to maintain JavaScript numbering in the calendar date to month conversion, e.g. `0 == "Jan"` kind of stuff.

## In conclusion

In the end, the initial *dynamic-calendar* exercise is not wasted. It was an essential step in understanding how some of these minor parts work. Then when moving into the new steps of adding support for much improved calendar UI, we clearly understand all the moving parts.

Happy coding!
