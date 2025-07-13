
``` javascript
frappe.ui.keys.add_shortcut({
    shortcut: "f2",
    description: __("Open New Issue"),
    action: () => {
     let pe_doc = frappe.model.get_new_doc("Payment Entry");
      pe_doc.payment_type = "Pay"; 
      frappe.set_route("Form", "Payment Entry",pe_doc.name);
    }
});

=======================================================================
  
  hooks.py involves this :

# Includes in <head>

# ------------------

# include js, css files in header of desk.html

# app_include_css = "/assets/diamondpharma/css/diamondpharma.css"

app_include_js = "/assets/diamondpharma/public/js/custom_test.js"


```