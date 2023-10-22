<%_*
let granularity = "monthly";
let default_format = {daily: "YYYY-MM-DD", weekly: "gggg-[W]ww", monthly: "YYYY-MM", quarterly: "YYYY-[Q]Q", yearly: "YYYY"};
let periodic_settings = app.plugins.plugins["periodic-notes"].settings;
let full_patterns = Object.fromEntries(Object.keys(default_format).map(p => [p, periodic_settings[p].format || default_format[p]]));
let patterns = Object.fromEntries(Object.entries(full_patterns).map(([k, v]) => [k, v.split("/").slice(-1)[0]]));
let date = moment(tp.file.title, patterns[granularity]);

tp.file.move(date.format(full_patterns[granularity]));

let prev_month = date.clone().subtract(1, "month");
let next_month = date.clone().add(1, "month");
_%>
# <% date.format("MMMM 'YY") %>

<%*
tR += `> [!hint] [[${date.format(patterns.yearly)}]] › \
[[${date.format(patterns.quarterly)}|${date.format("[Q]Q")}]] › \
[[${prev_month.format(patterns.monthly)}|«]] \
**[[${date.format(patterns.monthly)}|${date.format("MMM")}]]** \
[[${next_month.format(patterns.monthly)}|»]] › `;
let week = date.clone().startOf("month");
let last_week = date.clone().endOf("month").endOf("week");
for (; week.isSameOrBefore(last_week); week.add(1, "week")) {
  let monday = week.clone().isoWeekday(1);
  let sunday = week.clone().isoWeekday(7);
  let link_label = `${monday.format("D")}-${sunday.format("D")}`;
  let week_link = `[[${week.format(patterns.weekly)}|${link_label}]]`;
  tR += week_link;
  if (week.isoWeek() < last_week.isoWeek()) {
    tR += " · ";
  }
}
%>

#journal #monthly

