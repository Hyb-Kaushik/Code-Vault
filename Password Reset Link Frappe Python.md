
``` python
user_doc = frappe.get_doc("User", "kaushik@mail.hybrowlabs.com")
reset_link = generate_reset_password_link(user_doc.name, send_email=False)
frappe.throw(reset_link)  # remove in production
```


``` python

def generate_reset_password_link(user, send_email=False, password_expired=False):
    from frappe.utils import now_datetime
    from frappe.utils.data import sha256_hash
    from frappe.utils import get_url

    key = frappe.generate_hash()
    hashed_key = sha256_hash(key)

    frappe.db.set_value("User", user, {
        "reset_password_key": hashed_key,
        "last_reset_password_key_generated_on": now_datetime()
    })
    frappe.db.commit()
    base_url = "http://127.0.0.1:8003" if frappe.local.dev_server else get_url()

    url = f"/update-password?key={key}"
    if password_expired:
        url += "&password_expired=true"

    link = f"{base_url}{url}"

    if send_email:
        user_doc = frappe.get_doc("User", user)
        user_doc.password_reset_mail(link)

    return link
```