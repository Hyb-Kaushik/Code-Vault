``` python
frappe.call({
            method: 'frappe.desk.query_report.run',
            args: {
                report_name: 'Accounts Receivable Summary',
                filters: { 
                    company: frappe.defaults.get_default("company"),
                    party_type: "Customer",  
                    party: [frm.doc.customer],
                    // ageing_based_on: "Due Date",  
                    // based_on: "Due Date"
  
                } 
            },  
            callback(r) {
                console.log(frm.doc.customer)
                if (!r.message?.result) {
                    frappe.msgprint({
                        title: __('No Data'),
                        message: __('No receivable data found for {0}.', [frm.doc.customer]),
                        indicator: 'red'
                    });
                    return;
                }
```