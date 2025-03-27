<%_*
let granularity = "weekly";
let default_format = {daily: "YYYY-MM-DD", weekly: "gggg-[W]ww", monthly: "YYYY-MM", quarterly: "YYYY-[Q]Q", yearly: "YYYY"};
let periodic_settings = app.plugins.plugins["periodic-notes"].settings;
let full_patterns = Object.fromEntries(Object.keys(default_format).map(p => [p, periodic_settings[p].format || default_format[p]]));
let patterns = Object.fromEntries(Object.entries(full_patterns).map(([k, v]) => [k, v.split("/").slice(-1)[0]]));
let date = moment(tp.file.title, patterns[granularity]);

tp.file.move(date.format(full_patterns[granularity]));

let monday = date.clone().isoWeekday(1);
let sunday = date.clone().isoWeekday(7);
let prev_week = date.clone().subtract(1, "week");
let next_week = date.clone().add(1, "week");
_%>
# Week <% date.format("W") %>, <% monday.format("MMMM Do") %> to <% sunday.format("MMMM Do, Y") %>

<%*
tR += `> [!hint] [[${monday.format(patterns.yearly)}]]`;
if (monday.year() != sunday.year()) {
  tR += ` · [[${sunday.format(patterns.yearly)}]]`;
}
tR += ` › [[${monday.format(patterns.quarterly)}|${monday.format("[Q]Q")}]]`;
if (monday.quarter() != sunday.quarter()) {
  tR += ` · [[${sunday.format(patterns.quarterly)}|${sunday.format("[Q]Q")}]]`;
}
tR += ` › [[${monday.format(patterns.monthly)}|${monday.format("MMM")}]]`;
if (monday.month() != sunday.month()) {
  tR += ` · [[${sunday.format(patterns.monthly)}|${sunday.format("MMM")}]]`;
}
tR += ` › [[${prev_week.format(patterns.weekly)}|«]] \
**[[${date.format(patterns.weekly)}|${date.format("[W]W")}]]** \
[[${next_week.format(patterns.weekly)}|»]] › `;
for (let i = 0; i < 7; i++) {
  let week_day = monday.clone().add(i, "day");
  let day_link = `[[${week_day.format(patterns.daily)}|${week_day.format("dd DD")}]]`;
  tR += day_link;
  if (i < 6) {
    tR += " · ";
  }
}
%>

#journal #weekly

