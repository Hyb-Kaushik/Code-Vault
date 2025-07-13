
``` javascript
refresh: function(frm) {
        if (frm.doc.docstatus === 1) {
            frm.add_custom_button(__('Quotation'), function() {
                frappe.model.with_doctype('Quotation', function() {
                    let quotation_doc = frappe.model.get_new_doc('Quotation');
                    quotation_doc.custom_boq_id = frm.doc.name;
                    quotation_doc.party_name = frm.doc.customer_name;
                    quotation_doc.custom_lead_id = frm.doc.lead_id;
                    if (frm.doc.items && frm.doc.items.length > 0) {
                        frm.doc.items.forEach(item => {
                            let quotation_item = frappe.model.add_child(quotation_doc, 'Quotation Item', 'items');
                            quotation_item.item_code = item.item_code || '';
                            quotation_item.qty = item.qty || 0;
                            quotation_item.uom = item.uom || '';
                            quotation_item.description = item.description || '';
                            quotation_item.custom_purchase_rate = item.purchase_rate || 0;
                            quotation_item.amount = item.amount || 0;
                            quotation_item.item_name = item.item_name;
                        });
                    }

                    frappe.set_route('Form', 'Quotation', quotation_doc.name);
                });
            }, __('Create'));
        }
    },

```