``` python
from erpnext.stock.doctype.delivery_note.delivery_note import DeliveryNote
import frappe

class CustomDeliveryNote(DeliveryNote):
    def on_submit(self):
        if self.custom_hold_stock_entry:
            self.flags.skip_stock_entry = True
            self.flags.skip_accounting_entry = True
            frappe.msgprint("Stock and Accounting posting is on hold until Installation Note is submitted.")
        else:
            super().on_submit(
              
              

```

# [[Home]]