## TASK: Tabella sezione e soglie

> La tabella è stata creata all'interno di ifs_datamonitoring.documento

***section.py (PYTHON)***
```
class Sezione(models.Model):
    _name = 'ifs_datamonitoring.section'

    name = fields.Char(string="SEZIONE", required=True)
    convergenza_diametrale_media_attenzione = fields.Float(String="CONVERGENZA DIAMETRALE MEDIA ATTENZIONE(Metri)", required=True)
    spostamento_singola_mira_attenzione = fields.Float(String="SPOSTAMENTO SINGOLA MIRA ATTENZIONE(Metri)", required=True)
    convergenza_diametrale_media_allarme = fields.Float(String="CONVERGENZA DIAMETRALE MEDIA ALLARME(Metri)", required=True)
    spostamento_singola_mira_allarme = fields.Float(String="SPOSTAMENTO SINGOLA MIRA ALLARME(Metri)", required=True)
```

***views.xml (XML)***
FORM
```
<record model="ir.ui.view" id="view_sezione_form_view">
			<field name="name">View Sezione</field>
			<field name="model">ifs_datamonitoring.section</field>
			<field name="priority" eval="16"/>
			<field name="arch" type="xml">
				<form>
					<sheet>
						<div>
							<div>
							<h3>SEZIONE</h3>
							<group>
								<field name="name"></field>
							</group>
							</div>
							<div>
								<h3>SOGLIA ATTENZIONE</h3>
								<group>
									<field name="convergenza_diametrale_media_attenzione"></field>
									<field name="spostamento_singola_mira_attenzione"></field>
								</group>
							</div>
							<div>
								<h3>SOGLIA ALLARME</h3>
								<group>
									<field name="convergenza_diametrale_media_allarme"></field>
									<field name="spostamento_singola_mira_allarme"></field>
								</group>
							</div>
						</div>
					</sheet>
				</form>
			</field>
		</record>
```
TREE/ACTION/MENU_ITEM

```
<record model="ir.ui.view" id="view_sezione_tree_view">
			<field name="name">View Sezione</field>
			<field name="model">ifs_datamonitoring.section</field>
			<field name="priority" eval="16"/>
			<field name="arch" type="xml">
				<tree>
					<field name="name"></field>
					<field name="convergenza_diametrale_media_attenzione"></field>
					<field name="spostamento_singola_mira_attenzione"></field>
					<field name="convergenza_diametrale_media_allarme"></field>
					<field name="spostamento_singola_mira_allarme"></field>
				</tree>
			</field>
		</record>

		<record id="sezione_window" model="ir.actions.act_window">
			<field name="name">SEZIONE</field>
			<field name="res_model">ifs_datamonitoring.section</field>
			<field name="view_mode">tree,form</field>
		</record>

		<menuitem id="menu_sezione" name="Sezione" action="sezione_window" parent="ifs_datamonitoring.menu_2" sequence="90" />
```
