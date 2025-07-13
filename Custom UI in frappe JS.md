
``` javascript
function view_share_loan_file_popup(data) {
    frappe.call({
        method: 'frappe.client.get',
        args: {
            doctype: 'Lender',
            name: data.lender
        },
        callback: function (response) {
            const doc = response.message;
            const dialog = new frappe.ui.Dialog({
                title: 'Share Loan Details',
                size: 'medium',
                fields: [
                    {
                        fieldtype: 'HTML',
                        fieldname: 'content_html'
                    }
                ]
            });

            const lender_user = doc.lender_users;
            const groupedByBranch = {};
            const fetches = [];

            if (lender_user.length > 0) {
                lender_user.forEach(item => {
                    const fetch = frappe.call({
                        method: 'frappe.client.get',
                        args: {
                            doctype: 'Portal User',
                            name: item.portal_user
                        }
                    }).then(res => {
                        const portalUserDoc = res.message;
                        const userId = portalUserDoc.user;

                        return frappe.call({
                            method: 'frappe.client.get',
                            args: {
                                doctype: 'User',
                                name: userId
                            }
                        }).then(userRes => {
                            const userDoc = userRes.message;
                            const userEmail = userDoc.email;

                            const enrichedItem = {
                                ...item,
                                email: userEmail
                            };

                            if (!groupedByBranch[item.branch_name]) {
                                groupedByBranch[item.branch_name] = [];
                            }

                            groupedByBranch[item.branch_name].push(enrichedItem);
                        });
                    });

                    fetches.push(fetch);
                });

                Promise.all(fetches).then(() => {
                    let htmlContent = '';
                    Object.keys(groupedByBranch).forEach(branch => {
                        htmlContent += `
                            <div style="margin: 20px 0; text-align: left;">
                                <h3 style="text-transform: capitalize;">Branch: ${branch}</h3>
                                <ul style="list-style: none; padding: 0;">
                        `;
                        groupedByBranch[branch].forEach(user => {
                            htmlContent += `
                                <li>
                                    <label>
                                        <input type="checkbox" name="portal_user" class="portal-checkbox" value="${user.email}">
                                        ${user.email}
                                    </label>
                                </li>
                            `;
                        });

                        htmlContent += `</ul></div>`;
                    });

                    htmlContent += `
                        <div style="text-align: center; margin-top: 30px;">
                            <button class="btn btn-primary" id="send-users-btn">Send</button>
                        </div>
                    `;

                    dialog.fields_dict.content_html.$wrapper.html(htmlContent);

                    dialog.fields_dict.content_html.$wrapper
                        .find('#send-users-btn')
                        .on('click', function () {
                            const selectedUsers = [];
                            dialog.fields_dict.content_html.$wrapper
                            .find('.portal-checkbox:checked')
                            .each(function () {
                                selectedUsers.push($(this).val());
                            });

                            if (selectedUsers.length === 0) {
                                frappe.msgprint(__('Please select at least one user.'));
                                return;
                            }

                            frappe.call({
                                method: 'edufund.api.loan_opportunity.share_loan_detail',
                                args: {
                                    users: selectedUsers,
                                },
                            })
                                .then(res => {
                                    dialog.hide();
                                })
                        });

                    dialog.show();
                });
            } else {
                dialog.fields_dict.content_html.$wrapper.html(`
                    <div style="text-align: center; padding: 20px;">
                        <p>Here is No lender User to Share</p>
                    </div>
                `);
                dialog.show();
            }
        }
    });
}

```

