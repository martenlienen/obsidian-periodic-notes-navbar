<%*
let granularity = "daily";
let default_format = {daily: "YYYY-MM-DD", weekly: "gggg-[W]ww", monthly: "YYYY-MM", quarterly: "YYYY-[Q]Q", yearly: "YYYY"};
let periodic_settings = app.plugins.plugins["periodic-notes"].settings;
let full_patterns = Object.fromEntries(Object.keys(default_format).map(p => [p, periodic_settings[p].format || default_format[p]]));
let patterns = Object.fromEntries(Object.entries(full_patterns).map(([k, v]) => [k, v.split("/").slice(-1)[0]]));
let date = moment(tp.file.title, patterns[granularity]);

tp.file.move(date.format(full_patterns[granularity]));
_%>
# <% date.format("dddd, MMMM Do, Y") %>

<%*
tR += `> [!hint] [[${date.format(patterns.yearly)}]] › \
[[${date.format(patterns.quarterly)}|${date.format("[Q]Q")}]] › \
[[${date.format(patterns.monthly)}|${date.format("MMM")}]] › \
[[${date.format(patterns.weekly)}|${date.format("[W]W")}]] › `;
let monday = date.clone().startOf("week");
tR += `[[${monday.clone().subtract(1, "day").format(patterns.daily)}|«]] `;
for (let i = 0; i < 7; i++) {
  let week_day = monday.clone().add(i, "day");
  let day_link = `[[${week_day.format(patterns.daily)}|${week_day.format("dd DD")}]]`;
  if (week_day.isoWeekday() == date.isoWeekday()) {
    day_link = `**${day_link}**`;
  }
  tR += day_link;
  if (week_day.isoWeekday() < 7) {
    tR += " · ";
  }
}
tR += ` [[${monday.clone().add(1, "week").format(patterns.daily)}|»]]`;
%>

#journal #daily

