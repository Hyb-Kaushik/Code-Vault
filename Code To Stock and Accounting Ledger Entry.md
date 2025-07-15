
``` python
from frappe.utils import nowdate, nowtime
import json
from erpnext.accounts.general_ledger import make_gl_entries

def on_submit(doc, method):
    if doc.custom_delivery_note_id:
        delivery_note = frappe.get_doc("Delivery Note", doc.custom_delivery_note_id)

        if frappe.db.exists("Stock Ledger Entry", {"voucher_no": delivery_note.name}):
            frappe.msgprint("Stock Ledger Entry already created for this Delivery Note.")
        else:
            for item in delivery_note.items:
                last_sle = frappe.db.sql("""
                    SELECT qty_after_transaction, stock_value
                    FROM `tabStock Ledger Entry`
                    WHERE item_code = %s AND warehouse = %s
                    ORDER BY posting_date DESC, posting_time DESC, creation DESC
                    LIMIT 1
                """, (item.item_code, item.warehouse), as_dict=True)

                previous_qty = last_sle[0].qty_after_transaction if last_sle else 0
                previous_stock_value = last_sle[0].stock_value if last_sle else 0

                actual_qty = -item.qty
                incoming_rate = item.rate

                stock_value_difference = actual_qty * incoming_rate
                qty_after_transaction = previous_qty + actual_qty
                stock_value = previous_stock_value + stock_value_difference
                valuation_rate = (stock_value / qty_after_transaction) if qty_after_transaction else 0

                sle = frappe.new_doc("Stock Ledger Entry")
                sle.item_code = item.item_code
                sle.warehouse = item.warehouse
                sle.posting_date = nowdate()
                sle.posting_time = nowtime()
                sle.voucher_type = "Delivery Note"
                sle.voucher_no = delivery_note.name
                sle.voucher_detail_no = item.name
                sle.stock_uom = item.uom
                sle.company = delivery_note.company

                sle.actual_qty = actual_qty
                sle.qty_after_transaction = qty_after_transaction
                sle.incoming_rate = incoming_rate
                sle.valuation_rate = valuation_rate
                sle.stock_value = stock_value
                sle.stock_value_difference = stock_value_difference
                sle.stock_queue = json.dumps([[actual_qty, incoming_rate]])

                sle.save()
                frappe.db.commit()

        if frappe.db.exists("GL Entry", {"voucher_no": delivery_note.name}):
            frappe.msgprint("GL Entry already created for this Delivery Note.")
        else:
            if not delivery_note.flags.skip_accounting_entry:
                delivery_note.run_method("make_gl_entries")
                frappe.db.commit()
```