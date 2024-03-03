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

<a class="btn btn-primary" href="https://forms.gle/paW22ugLwrbk4Ccb8">Submit your event</a>

<div id="events_html">
  <i>Loading events....</i>
</div>

{% include main_js.html %}
<script>
window.addEventListener("load", (event) => {
  getEventsData(function(data){
    var now = new Date();
    start = now;
    var end  = new Date(now.getFullYear(), now.getMonth() + 2, 1);
    var events = getEventsForPeriod(data, start, end)
    document.getElementById("events_html").innerHTML = makeNewsletterHtml(events, start);
  });
})
</script>
