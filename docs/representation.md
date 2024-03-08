---
title: Community Representation
permalink: representation
---

The ERA is a member of [FeCRA](https://www.fecra.org.uk/) (Federation of Cambridge Residents’ Associations), and is representing the Eddington residents in different groups and committees locally and city-wide.

{% assign now_timestamp = "now"|date:"%s"|plus:0 %}
{% assign meetings = site.meetings|sort:"-sort_order"|reverse %}

{% for meeting in meetings %}
{% assign collection = site.collections | where: "label", "meetings" | first %}
{% assign next_meeting_timestamp = meeting.next_date|date:"%s"|plus:0 %}
{% assign minutes_prefix = "/minutes/" | append:meeting.minutes_dir | append:"/" %}
{% assign file_path_filter = "file.path contains '" | append:minutes_prefix | append:"'" %}
{% assign minutes = collection.files | where_exp:"file", file_path_filter %}

## {{ meeting.organisation }} {{meeting.sort_order}}

Meets {{ meeting.frequency }}
&nbsp; | &nbsp; next meeting:
{% if next_meeting_timestamp > now_timestamp -%}
  {{ meeting.next_date|date: "%A %d %B %Y"}}
{%- else -%}
  TBD
{%- endif %}
{%- if meeting.website -%}
&nbsp; | &nbsp;  [organisation website]({{meeting.website}})
{%- endif %}

{{ meeting.content }}

{%- if meeting.is_public_minutes -%}
Previous meetings and minutes:<br/>
{%- else -%}
Previous meetings (minutes are not yet made public):<br/>
{%- endif -%}
{% for date in meeting.prev_dates -%}
  {%- assign date_str = date|date: "%Y-%m-%d" -%}
  {%- assign file_path_filter = "file.path contains '" | append: date_str | append:"'" -%}
  {%- assign file = minutes | where_exp:"file", file_path_filter | first -%}
  {%- assign day = date|date:"%-d" -%}
  {%- if file %} <a href="{{ file.path|replace_first: "_", "" }}">{%- include date_ordinal.md day=day %}{{- date|date: " %B %Y" -}}</a>,&nbsp;
  {%- else -%}
  {{ date|date: "%-d %B %Y"  }},
  {% endif -%}
{%- endfor %}

{% endfor %}
