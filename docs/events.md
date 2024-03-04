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
<script>
window.addEventListener("load", (event) => {
  getEventsData(function(data){
    var now = new Date();
    var events = getEventsForPeriod(data, now, addMonths(now, 3))
    document.getElementById("events_html").innerHTML = makeNewsletterHtml(events, now);
  });
})
</script>
