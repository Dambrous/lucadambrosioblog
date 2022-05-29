## Task: Modificando Un Booleano In Res.config.settings Si Attiva Una Funzionalità O No

Il problema qui sta nel model res.config.settings che non è un modello ma un modello astratto transiente costruito solo per recuperare le info dalla company e salvarle dando all'utente un'interfaccia più comoda.

***QUI BISOGNA ***
> Mettere il campo su res.company e in res.config.settings lo metti come related attraverso company_id (Probabilmente readonly false). 
> 
> Così nel tuo model, per recuperarlo, puoi sfruttare company_id (se c'è), altrimenti metti un valore di default che dalla Company attiva prende quel campo


PERCIÒ BISOGNA PRIMA CREARE UN BOOLEANO IN "RES.CONFIG.SETTINGS"
```
class ResConfigSettings(models.TransientModel):
    _inherit = "res.config.settings"

    group_analytic_tags = fields.Boolean(related="company_id.group_analytic_tags",
                                         implied_group='analytic.group_analytic_tags',
                                         readonly=False,
                                         string='Analytic Tags')
```
POI IN RES.COMPANY
```
class Company(models.Model):
    _inherit = "res.company"

    group_analytic_tags = fields.Boolean(string="Group Analytic Tags")
```
POI NEL MODEL IN CUI DOBBIAMO AGIRE(NEL NOSTRO CASO "ACCOUNT.MOVE"
```
class AccountMove(models.Model):
    _inherit = "account.move"
    group_analytic_tags = fields.Boolean(related="company_id.group_analytic_tags")
```
ED INFINE NELLA VIEW DI RIFERIMENTO
```
	<field name="group_analytic_tags"/>
```

ECCO UN ESEMPIO SBAGLIATO(proprio perche è UN TRANSIENT MODEL DAVA 2 RISULTATI QUANDO IN REALTÀ AVEVAMO BISOGNO DI UN ***SINGLETON***)
```
is_group_analytic_accounting_visible = fields.Boolean(compute="_get_is_group_analytic_accounting_visible")

    def _get_is_group_analytic_tags_visible(self):
        settings = self.env['res.config.settings'].search([("company_id", "=", self.env.company.id)]) 
        self.is_group_analytic_tags_visible = settings.group_analytic_tags
```