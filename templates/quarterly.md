<%_*
let granularity = "quarterly";
let default_format = {daily: "YYYY-MM-DD", weekly: "gggg-[W]ww", monthly: "YYYY-MM", quarterly: "YYYY-[Q]Q", yearly: "YYYY"};
let periodic_settings = app.plugins.plugins["periodic-notes"].settings;
let full_patterns = Object.fromEntries(Object.keys(default_format).map(p => [p, periodic_settings[p].format || default_format[p]]));
let patterns = Object.fromEntries(Object.entries(full_patterns).map(([k, v]) => [k, v.split("/").slice(-1)[0]]));
let date = moment(tp.file.title, patterns[granularity]);

tp.file.move(date.format(full_patterns[granularity]));

let prev_quarter = date.clone().subtract(1, "quarter");
let next_quarter = date.clone().add(1, "quarter");
let first_month = date.clone().startOf("quarter");
let middle_month = first_month.clone().add(1, "month");
let last_month = date.clone().endOf("quarter");
_%>
# <% first_month.format("MMMM") %> to <% last_month.format("MMMM, 'YY") %>

<%*
tR += `> [!hint] [[${date.format(patterns.yearly)}]] › \
[[${prev_quarter.format(patterns.quarterly)}|«]] \
**[[${date.format(patterns.quarterly)}|${date.format("[Q]Q")}]]** \
[[${next_quarter.format(patterns.quarterly)}|»]] › \
[[${first_month.format(patterns.monthly)}|${first_month.format("MMM")}]] · \
[[${middle_month.format(patterns.monthly)}|${middle_month.format("MMM")}]] · \
[[${last_month.format(patterns.monthly)}|${last_month.format("MMM")}]]`;
%>

#journal #quarterly

