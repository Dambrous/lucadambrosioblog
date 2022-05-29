## TASK:Fare in modo che i prodotti abbiano una sola imposta nella configurazione

```
class ProductTemplate(models.Model):
    _inherit = "product.template"

    def write(self, vals):
        ris = super(ProductTemplate, self).write(vals)
        if not self.env.company.use_multitaxes and len(self.taxes_id) > 1:
            raise UserError("You cannot have more than one Tax")
        else:
            pass

        return ris
```
***CODICE CORRETTO(DOPO REVIEW SU GITHUB)
>>>>>>>>>>>>>>>>>>***
L'HEADER GIUSTO, LE CONSTRAINS SI CICLANO SEMPRE E LA SUPER SI PUÒ SCRIVERE ANCHE super() Senza il nome della classe.
```
# Copyright 2022-TODAY Rapsodoo Italia S.r.L. (www.rapsodoo.com)
# License LGPL-3.0 or later (https://www.gnu.org/licenses/lgpl).

from odoo import models, api
from odoo.exceptions import UserError


class ProductTemplate(models.Model):
    _inherit = "product.template"

    @api.constrains("taxes_id")
    def check_number_of_tax(self):
        for line in self:
            if not line.env.company.use_multitaxes and len(line.taxes_id) > 1:
                raise UserError("You cannot have more than one Tax")
```

Fare in modo che i prodotti abbiano una sola imposta nella configurazione e che il campo sia legato alla company e che solo per le company italiane ci sia il limite.



In MAGAZZINO>PRODOTTI>PRODOTTO(NAME)>IMPOSTE CLIENTE(MANY2MANY)

Il field IMPOSTE CLIENTE che è una Many2many può essere selezionato solo UNO SE ATTIVO IL FLAG "USE MULTITAXES"(che può essere attivato o disattivato in "Settings" Contabilità), ma se vengono selezionate 2 tasse ma il "FLAG MULTITAXES" è disattivato apparirà un messaggio di errore.(Come in allegato).


![Screenshot from 2022-05-23 14-29-43.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653309390069/B39KzUMbn.png align="left")
