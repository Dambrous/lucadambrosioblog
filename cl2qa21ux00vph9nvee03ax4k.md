## TASK: Una volta creato un record con un valore specifico modificato, crea su 'mail.alias' un record con un dizionario specifico.


**> CI troviamo nel model 'heiq.capability'**
```
@api.model
    def create(self, values):
        if values['ref']:
            model_id = self.env['ir.model'].search(
                [('model', '=', 'heiq.capability')])
            vals = {
                'alias_name': values['ref'],
                'alias_model_id': model_id.id
            }
            self.env['mail.alias'].create(vals)
        return super().create(values)
```
***'alias_name' e 'alias_model_id' sono field all'interno del model 'mail.alias'***