---
title: Events in Eddington
permalink: events
---

The ERA aims to promote non-commercial local events to help build a connected community.
If you run an event we can help you promote it on this site,
via our [newsletter](/newsletter) and on our
[social](https://instagram.com/eddington_ra)
[media](https://x.com/EddingtonRA)
[accounts](https://m.facebook.com/EddingtonRA).

See [how we could help](/events/help-for-your-event), or
<a class="btn btn-sm btn-primary" href="https://forms.gle/paW22ugLwrbk4Ccb8" target="_BLANK">Submit your event</a>

<div id="events_html">
  <i>Loading events....</i>
</div>

{% include main_js.html %}
<style>
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
</style>
<script>
window.addEventListener("load", (event) => {
  getEventsData(function(data){
    var now = new Date();
    var events = getEventsForPeriod(data, now, addMonths(now, 3))
    document.getElementById("events_html").innerHTML = makeEventsPageHtml(events, now);
  });
})

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
      html += `<tr class="month"><th colspan=2 ><h2>${date.toLocaleString('default', { month: 'long' })}</h2></th></tr>\n`;
    }
    var weekend_class = [0, 6].includes(date.getDay()) ? "weekend" : "";
    var date_str = date.toLocaleString('default', {weekday: "short"}) + " " + date.toLocaleString('default', {day: "numeric"}) + date_th(date);
    var next_date = new Date(date.getFullYear(), date.getMonth(), date.getDate() + 1);
    var day_events = events.filter((x)=>x[DATE] >= date && x[DATE] < next_date)
    day_events.sort((x, y) => x[START_TIME].getTime() - y[START_TIME].getTime());

    time_range = (x) => short_time(x[START_TIME], x[END_TIME]) + "-" + short_time(x[END_TIME]);
    signup_icon = (x) => x[NEEDS_SIGNUP] == "Yes" ? `<span class="signup-required-badge" title="requires signup before you attend">sign</span>` : ""

    var events_html = day_events.map(
      (x)=>`${time_range(x)}\n`
          +`    <a href="${x[URL]}">${x[NAME]}</a>\n`
          +`    ${x[LOCATION]}\n`
          +`    ${signup_icon(x)}\n<br>\n`
          +`  ${x[DESCRIPTION].trim()}`
          + (x[URL] ? `  <a href="${x[URL]}">details</a>`:"")
          +`<br><br>\n\n`
      ).join("\n")

    html += `<tr class="day ${weekend_class}">
    <td class="date">${date_str}</td>
    <td class="events">
      ${events_html}
    </td>
</tr>\n`

    prev_date = date;
  }
  html += `  </tbody>\n</table>`

  return html
}
</script>
