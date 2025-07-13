

---
# Javascript

``` javascript
frappe.ui.form.on("Sales Order", {
    custom_purchase_order: async function(frm) {
        console.log(frm.doc.custom_purchase_order)
        if (frm.doc.custom_purchase_order) {
            const response = await frm.call({
                method: "bagaria.public.py.sales_order.get_po_items",
                args: {
                    po: frm.doc.custom_purchase_order
                }
            });
            console.log(response);
            
            const items = response.message;
            if (items && items.length > 0) {
                frm.clear_table("items");

                items.forEach(row => {
                    let new_row = frm.add_child("items");
                    
                    const meta = frappe.get_meta("Sales Order Item"); 
                    const fieldnames = meta.fields.map(f => f.fieldname);
                
                    fieldnames.forEach(field => {
                        if (row[field] !== undefined) {
                            new_row[field] = row[field];
                        }
                    });
                });

                frm.refresh_field("items");
                frappe.msgprint("Items copied from Purchase Order.");
            } else {
                frappe.msgprint("No items found in the selected Purchase Order.");
            }
        }
    }
});
```

# Python

``` python
  
  import frappe
@frappe.whitelist()
def get_po_items(po):
    po_doc = frappe.get_doc("Purchase Order",po)
    return [item.as_dict() for item in po_doc.items]
    
```