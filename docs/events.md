---
title: Events in Eddington
permalink: events
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
    margin-bottom: 10lvh;
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

    time_range = function(x){
      if(x[START_TIME].getHours() == 0 && x[END_TIME].getHours() == 0){
        return "all day";
      }
      return short_time(x[START_TIME], x[END_TIME]) + "-" + short_time(x[END_TIME]);
    }
    info_link = (x) => x[NEEDS_SIGNUP] == "Yes" ? `sign&nbsp;up` : `details`

    let sep = `<span class="separator">|</span>`
    var events_html = day_events.map(
      (x)=>`<a href="${x[URL]}">${x[NAME]}</a>\n`
          +` ${sep} ${time_range(x)}\n`
          +` ${sep} ${x[LOCATION]}\n<br/>\n`
          +` ${x[DESCRIPTION].trim()}`
          + (x[URL] ? `  <a href="${x[URL]}">${info_link(x)}</a>`:"")
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
