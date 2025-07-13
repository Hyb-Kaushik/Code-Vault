
``` javascript
$(document).ready(function () {
    if ($('.navbar').length) {
        const companyName =  frappe.defaults.get_default("company") || "No Company Selected";

        const companyElement = $('<div>', {
            text: companyName,
            css: {
                'font-weight': 'bold',
                'font-size': '16px',
                'text-align': 'center',
                'margin': '0 auto',
                'position': 'absolute',
                'left': '50%',
                'transform': 'translateX(-50%)',
            }
        });

        $('.navbar').append(companyElement);
    }
});


```


---

>  Write this in public/js folder and import it to hooks in assets
