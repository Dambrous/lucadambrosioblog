## Task:creare Un Menu(tree E Form) E Mettere Di Default Group By Appena Entro

```
<record model="ir.ui.view" id="view_tree_account_tax_group">
            <field name="name">View Tax Group</field>
            <field name="model">account.tax.group</field>
            <field name="priority" eval="16"/>
            <field name="arch" type="xml">
                <tree>
                    <field name="name"/>
                    <field name="company_id"/>
                    <field name="property_tax_payable_account_id"/>
                    <field name="property_tax_receivable_account_id"/>
                    <field name="l10n_it_is_a_withholding_tax"/>
                    <field name="has_pro_rata"/>
                </tree>
            </field>
        </record>

        <record id="account_tax_group" model="ir.ui.view">
            <field name="name">account.tax.group.search.view</field>
            <field name="model">account.tax.group</field>
            <field name="arch" type="xml">
                <search>
                    <group expand="0" string="Group By">
                        <filter name="group_by_company" string="Company" context="{'group_by': 'company_id'}"/>
                    </group>
                </search>
            </field>
        </record>

        <record id="action_account_tax_group" model="ir.actions.act_window">
            <field name="name">Tax Group</field>
            <field name="res_model">account.tax.group</field>
            <field name="view_mode">tree,form</field>
            <field name="context">{'search_default_group_by_company': 1}</field>
        </record>

        <menuitem id="menu_account_tax_group" name="Tax Group" action="action_account_tax_group" parent="account.account_account_menu" sequence="2"/>
```

Per impostare il group_by di default appena entri nella tree ho dovuto creare una view e inserire all'interno una search e un group e un filter con il name
<search>
                    ```
<group expand="0" string="Group By">
                        <filter name="group_by_company" string="Company" context="{'group_by': 'company_id'}"/>
                    </group>
                </search>
```
Fatto ciò nella action(window) ho aggiunto 
 ```
<field name="context">{'search_default_group_by_company': 1}</field>
```
Il gioco è fatto, questo è il risultato.
![Screenshot from 2022-05-11 17-01-55.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652281340206/RC5WodxEQ.png align="left")
