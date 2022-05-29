## TASK: Creare campo per gestire l'etichetta analitica
Funzionalità che se popolo il campo con una etichetta analitica (mi popola tutte le righe)


![12.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1652793252561/-I10D66XZ.jpg align="left")

```
force_analytic_account_tag_id = fields.Many2one(
        "account.analytic.tag", string="Label Analytic Account to Force on Lines"
    )
```
```
def force_analytic_account_tag(self, values):
        move_id = self
        if "force_analytic_account_tag_id" in values:
            if move_id.force_analytic_account_tag_id:
                for line_id in move_id.invoice_line_ids:
                    line_id.analytic_tag_ids = [(5, 0, 0), (6, 0, move_id.force_analytic_account_tag_id.ids)]
```

```
<data>
		<record id="view_move_form_force_account" model="ir.ui.view">
			<field name="name">account.move.form</field>
			<field name="model">account.move</field>
			<field name="priority">100</field>
			<field name="inherit_id" ref="account.view_move_form" />
			<field name="arch" type="xml">
				<xpath expr="//div[@name='journal_div']" position="after">
					<field name="force_account_id" domain="[('deprecated', '=', False)]" attrs="{'invisible':[('move_type','=','entry')]}" />
					<field name="force_analytic_account_id" attrs="{'invisible':[('move_type','=','entry')]}" />
					<field name="force_analytic_account_tag_id" attrs="{'invisible':[('move_type','=','entry')]}"/>
				</xpath>
			</field>
		</record>
	</data>
```