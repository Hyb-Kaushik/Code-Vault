```javascript
                    frm.page.remove_inner_button("Sales Order", "Create");
===============================================================================   
 setTimeout(() => {
            frm.page.wrapper.find('span.actions-btn-group-label:contains("Actions")').closest('button').hide();
            
        }, 2000);
// if above not works use below
  setTimeout(() => {
			frm.page.wrapper
				.find('button:contains("Set as Lost")')
				.hide();
                    }, 100); 
=========================================
            frm.page.remove_inner_button("Project", "Create");


```




# [[Home]]