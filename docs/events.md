---
title: Events in Eddington
permalink: events
redirect_from:
  - /events/calendar
---

The ERA promotes and supports non-commercial local events to help build a connected community.
Follow us on [Instagram](https://instagram.com/eddington_ra),
[Twitter](https://x.com/EddingtonRA)
and [Facebook](https://m.facebook.com/EddingtonRA) and
sign up for [our newsletter](/newsletter) to receive a monthly
digest to your inbox.

Want to see your event on this list? Find out [how we can help](/events/help-for-your-event)
with funding, volunteers and promotion.

<div id="events_html">
  <i>Loading events....</i>
</div>
<p class="text-center"><a href="?reload=1" title="load latest events">⟳</a></p>

{% include main_js.html %}
<style>
  #events_table {
    margin-bottom: 20lvh;
  }
  #events_table tbody {
    position: relative;
  }
  #events_table .month th {
    position: sticky;
    top: 0;
    z-index: 2;
  }
  main.all-white #events_table .month th {
    background-color: white;
  }
  #events_table .month th h2 {
    padding: 0;
    margin: 0;
  }
  #events_table .date {
    text-align: right;
    padding-right: 1.5em;
    text-wrap: nowrap;
  }
  #events_table td {
    vertical-align: top;
  }
  #events_table td:first-child() {
    width: 75px;
  }
  #events_table .event {
    margin-bottom: 1em;
  }
  #events_table .day.weekend {
    background-color: rgba(0, 0, 0, 0.05);
  }
  #events_table .date > a {
    color: black;
  }
  #events_table :target {
    background-color: #fec;
  }
</style>
<script>
window.addEventListener("load", (event) => {
  getEventsData(function(data){
    var now = new Date();
    var events = getEventsForPeriod(data, now, addMonths(now, 3))
    document.getElementById("events_html").innerHTML = makeEventsPageHtml(events, now);
    window.setTimeout(scrollToChosenEvent, 500)  // prevent race condition with events_html injection
  });
})

function scrollToChosenEvent(){
  // if the page has a url fragment, try to scroll to the targeted element
  // from https://stackoverflow.com/a/74937610
  let element = document.getElementsByName(location.hash.substring(1))[0];
  if (element) {
      // Enable the element state without changing scroll position.
      let scrollY = window.scrollY;
      let hash = location.hash;
      location.hash = '';
      location.hash = hash;
      window.scrollTo({top: scrollY});

      // Smooth scroll the element into view.
      element.scrollIntoView({behavior: 'smooth', block: 'center'});
  }
}

function fmt(date, options){
  return date.toLocaleString('default', options)
}

function makeEventsPageHtml(events, start_date){
  var html = `<table id="events_table">\n  <tbody>`
  if(!events.length){
    html += "<tr><td style='padding: 5em 10em; font-style: italic;'>No events found</td></tr></tbody></table>"
    return html
  }
  var first_event_date = new Date(Math.min(...events.map((x)=>x[DATE])));
  var last_event_date = new Date(Math.max(...events.map((x)=>x[DATE])));
  var prev_date = addMonths(first_event_date, -1);
  var end_date = addMonths(last_event_date, 1)

  console.log(`making html for ${events.length} events between ${first_event_date} and ${last_event_date}`);
  for(var date = start_date; date < end_date; date = new Date(date.getFullYear(), date.getMonth(), date.getDate() + 1)){
    if(prev_date.getMonth() != date.getMonth()){
      html += `<tr class="month"><th colspan=2 ><h2>${fmt(date, { month: 'long' })}</h2></th></tr>\n`;
    }
    var weekend_class = [0, 6].includes(date.getDay()) ? "weekend" : "";
    var date_str = `${fmt(date, {weekday: "short"})}&nbsp;${fmt(date, {day: "numeric"})}<span class="date-ordinal">${date_th(date)}</span>`;  // like "Wed 27th"
    var date_name = fmt(date, {year: "numeric"}) + "-" + fmt(date, {month: "2-digit"}) + "-" + fmt(date, {day: "2-digit"});  // like "2024-03-23"
    var next_date = new Date(date.getFullYear(), date.getMonth(), date.getDate() + 1);
    var day_events = events.filter((x)=>x[DATE] >= date && x[DATE] < next_date)
    day_events.sort((x, y) => x[START_TIME].getTime() - y[START_TIME].getTime());

    time_range = function(x){
      if(x[START_TIME].getHours() == 0 && x[END_TIME].getHours() == 0){
        return "all day";
      }
      return short_time(x[START_TIME], x[END_TIME]) + "-" + short_time(x[END_TIME]);
    }
    info_link = (x) => x[NEEDS_SIGNUP] == "Yes" ? `sign&nbsp;up` : `details`
    price = (x) => x[PRICE] == "0" ? `free` : `&pound;${x[PRICE]}`

    let sep = `{%- include separator.md -%}`
    var events_html = day_events.map(
      (x)=>`<a href="${x[URL]}">${x[NAME]}</a>\n`
          +` ${sep} ${time_range(x)}\n`
          +` ${sep} ${x[LOCATION]}\n\n`
          +` ${sep} ${price(x)}\n`
          +` &nbsp; &nbsp; ${add_to_calendar_button(x)}\n<br/>\n`
          +` ${x[DESCRIPTION].trim()}`
          + (x[URL] ? `  <a href="${x[URL]}">${info_link(x)}</a>`:"")
          +`<br><br>\n\n`
      ).join("\n")

    html += `<tr class="day ${weekend_class}">
    <td class="date"><a href="#${date_name}" name="${date_name}">${date_str}</a></td>
    <td class="events">
      ${events_html}
    </td>
</tr>\n`

    prev_date = date;
  }
  html += `  </tbody>\n</table>`

  return html
}

function add_to_calendar_button(x){
  return `<add-to-calendar-button
  name="${x[NAME]}"
  description="${x[DESCRIPTION]}\n\n${x[URL]}"
  startDate="${x[DATE].toISOString().split('T')[0]}"
  startTime="${x[START_TIME].toLocaleTimeString()}"
  endTime="${x[END_TIME].toLocaleTimeString()}"
  timeZone="Europe/London"
  location="${x[LOCATION]}"
  options="'Apple','Google','iCal','Outlook.com','Yahoo'"
  hideTextLabelButton
  hideBackground
  size="2"
  style="float: right; margin-bottom: -10px;"
></add-to-calendar-button>`
}
</script>
<script src="https://cdn.jsdelivr.net/npm/add-to-calendar-button@2.6.14" integrity="sha384-APA3+zG8NRE4ML4hiWv+3NFvcbooelKsBJHModWOORVG8BwjqysAzxB6/gdpqRhF" crossorigin="anonymous"></script>
