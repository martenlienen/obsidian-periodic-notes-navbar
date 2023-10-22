<%*
let templater = app.plugins.plugins["templater-obsidian"];
let template_folder = templater.settings.templates_folder || "templates";

let default_format = {daily: "YYYY-MM-DD", weekly: "gggg-[W]ww", monthly: "YYYY-MM", quarterly: "YYYY-[Q]Q", yearly: "YYYY"};
let periodic_settings = app.plugins.plugins["periodic-notes"].settings;
let note_types = Object.entries(default_format).map(([granularity, format]) => ({
  format: periodic_settings[granularity].format.split("/").slice(-1)[0] || format,
  template: `${granularity}.md`,
}));

for (const note_type of note_types) {
    // If the note title matches the format
    if (moment(tp.file.title,  note_type.format,  true).isValid()) {
        try {
            let path = `${template_folder}/${note_type.template}`;
            let template_file = app.vault.getAbstractFileByPath(path);

            if (!template_file) {
                let msg = `Template not found: ${path} does not exist.`;
                new Notice(msg);
                throw Error(msg);
            }

            // Insert template
	        templater.templater.append_template_to_active_file(template_file);
        } catch (e) {
            console.error(e);
        }
    }
}
-%>
