
``` javascript
frappe.listview_settings['Sales Invoice'] = {
    onload: function(listview) {
            frappe.route_options = {
                company: frappe.defaults.get_default("company")
        }
    }
};
```