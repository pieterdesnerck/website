{%- assign d=include.day -%}
{{ d }}<span class="date-ordinal">
{%- case d -%}
  {%- when "1" or "21" or "31" -%}st
  {%- when "2" or "22" -%}nd
  {%- when "3" or "23" -%}rd
  {%- else -%}th
{%- endcase -%}
</span>
