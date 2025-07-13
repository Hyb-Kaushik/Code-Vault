# JavaScript

``` javascript
frappe.ui.form.on('My Task', {
    refresh: function(frm) {
        // Bind click event to 'Add Assignment' button
        $(document).on('click', '.add-assignment-btn', function () {

            // Delay to ensure dialog is fully rendered
            setTimeout(() => {
                const openDialogs = frappe.ui?.open_dialogs || [];

                if (openDialogs.length > 0) {
                    const lastDialog = openDialogs[openDialogs.length - 1];

                    if (lastDialog.fields_dict) {
                        // Loop through dialog fields
                        for (let fieldname in lastDialog.fields_dict) {
                            if (fieldname !== "assign_to_me") {
                                // Hide field and refresh UI
                                lastDialog.fields_dict[fieldname].df.hidden = 1;
                                lastDialog.fields_dict[fieldname].refresh();
                            }
                        }
                    }
                }
            }, 1000);
        });
    }
});
```

[[Home]]