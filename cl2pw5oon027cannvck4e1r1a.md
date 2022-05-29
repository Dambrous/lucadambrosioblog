## TASK: Aggiunta campi in Task

Creazione nuovo model con annesse tree e form

Python
```
from odoo import models, fields

class FIELDFIELD(models.Model):
    _name = 'field.field'

    name = fields.Char("Field Esempio")
```
Xml
```
<record model="ir.ui.view" id="field_project_tree">
            <field name="name">field.project.project.tree</field>
            <field name="model">field.field</field>
            <field name="arch" type="xml">
                <tree string="Test">
                    <field name="name"/>
                </tree>
            </field>
        </record>

        <record id="field_action_config_project" model="ir.actions.act_window">
            <field name="name">Field</field>
            <field name="res_model">field.field</field>
            <field name="view_mode">tree,form</field>
        </record>

        <menuitem
        id="project_project_menu_config_field"
        name="Field"
        parent="project.menu_project_config"
        action="field_action_config_project"
        sequence="50"/>
```
Security 
```
access_field_field,access.field.field,model_field_field,base.group_user,1,1,1,1
```
Risultato:

![Screenshot from 2022-05-03 10-16-45.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651565876877/2ZTUlhc2y.png align="left")
![Screenshot from 2022-05-03 10-16-52.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651565884151/nJXmdsUYd.png align="left")
![Screenshot from 2022-05-03 10-16-58.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651565888174/dJmnxfsja.png align="left")

Ora aggiungiamo l'oggetto "product.template" a questa vista (nella tabella "Timesheet")
![Screenshot from 2022-05-03 10-21-36.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651566111598/D2KX7jz2E.png align="left")

Perciò:

![Screenshot from 2022-05-03 10-22-52.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1651566464914/hbGjg7FoL.jpg align="left")

Python(account_analytic_line.py)
```
from odoo import models, fields

class TaskInherit(models.Model):
    _inherit = "account.analytic.line"

    quantity_number = fields.Float(string="Quantità")

    product_name_id = fields.Many2one('product.template', string="Prodotto")

    field_example_name = fields.Many2one('field.field',)
```

Xml(project_task.xml)
```
<record model="ir.ui.view" id="view_task_form3_inherited">
            <field name="name">project.task.form.inherited.timesheet</field>
            <field name="model">project.task</field>
            <field name="inherit_id" ref="hr_timesheet.view_task_form2_inherited" />
            <field name="groups_id" eval="[(6,0, (ref('hr_timesheet.group_hr_timesheet_user'),))]"/>
            <field name="arch" type="xml">
                <xpath expr="//field[@name='company_id']" position="after">
                    <field name="field_example_name"/>
                    <field name="product_name_id"/>
                    <field name="quantity_number"/>
                </xpath>
            </field>
        </record>
```
BIsogna mettere una dipendenza nel manifest
```
"name": "Custom",
    "version": "0.0.0.0.1",
    "category": "API",
    "author": "Rapsodoo",
    "website": "",
    "summary": "Customization ",
    "support": "info@rapsodoo.com",
    "description": """""",
    "depends": [
        'project',
        'hr_timesheet',
    ],
```
Risultato:

![Screenshot from 2022-05-03 10-29-54.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651566649782/rR_kvxJD0.png align="left")

