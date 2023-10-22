<%_*
let granularity = "yearly";
let default_format = {daily: "YYYY-MM-DD", weekly: "gggg-[W]ww", monthly: "YYYY-MM", quarterly: "YYYY-[Q]Q", yearly: "YYYY"};
let periodic_settings = app.plugins.plugins["periodic-notes"].settings;
let full_patterns = Object.fromEntries(Object.keys(default_format).map(p => [p, periodic_settings[p].format || default_format[p]]));
let patterns = Object.fromEntries(Object.entries(full_patterns).map(([k, v]) => [k, v.split("/").slice(-1)[0]]));
let date = moment(tp.file.title, patterns[granularity]);

tp.file.move(date.format(full_patterns[granularity]));

let prev_year = date.clone().subtract(1, "year");
let next_year = date.clone().add(1, "year");
_%>
# The year <% date.format("y") %>

<%*
tR += `> [!hint] \
[[${prev_year.format(patterns.yearly)}|«]] \
**[[${date.format(patterns.yearly)}]]** \
[[${next_year.format(patterns.yearly)}|»]] › `;
for (let i = 1; i <= 4; i++) {
  let quarter = date.clone().quarter(i);
  let first_month = quarter.clone().startOf("quarter");
  let last_month = quarter.clone().endOf("quarter");
  let label = `${first_month.format("MMM")}-${last_month.format("MMM")}`;
  tR += `[[${quarter.format(patterns.quarterly)}|${label}]]`;
  if (i < 4) {
    tR += " · ";
  }
}
tR += "<br />";
for (let i = 0; i <= 11; i++) {
  let month = date.clone().month(i);
  tR += `[[${month.format(patterns.monthly)}|${month.format("MMM")}]]`;
  if (i < 11) {
    tR += " · ";
  }
}
%>

#journal #yearly

