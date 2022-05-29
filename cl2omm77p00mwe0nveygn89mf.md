## TASK: Modificare campo upload file(Lettura file .xml)

Descrizione Completa:

1. Creazione campo che al caricamento di un file zip legga il contenuto.
2. Gestire i due file all'interno(.xml/.pdf).
3. Il file .pdf deve essere agganciato ad un field presente nella form.
4. Il file .xml deve essere letto e i dati estrapolati devono essere confrontati con record presenti nella tabella "Sezione" che avevamo creato in precedenza(ulteriore articolo).
5. Gestire gli "state".
6. Gestire il confronto dei dati.(altro articolo)
7. Risultato

> 1.
Questo field si trova all'interno di ifs_datamonitoring.documento
> 
> models.py(PYTHON)
> Importiamo la libreria(io) che ci servirà dopo.
> ```
> import io
> ```
> ```
> class Documento(models.Model):
>     _name = 'ifs_datamonitoring.documento'
>     
>     attachment_zip = fields.Binary('FILE ZIP', help='Importa il file zip', store=True, attachment=True,)
> ```
> 
> ```
> def read_files_from_zip(self):
>     with zipfile.ZipFile(io.BytesIO(base64.b64decode(self.attachment_zip))) as zip_file:
>          for name in zip_file.namelist():
> ```

> 2.
> Utilizziamo la funzione .endswith() di python per prendere di tutti i file(ciclando name) che ci sono solo quelli che finiscono in un determinato modo. 
> ```
> for name in zip_file.namelist():
>     if name.endswith('.pdf'):
>         with zip_file.open(name) as pdf:
>         data = pdf.read()
> ```
> 3.Dato che il primo file (.pdf) deve essere attaccato ad un field già esistente nella form,dobbiamo cercarlo tra gli attachment nella tabella ir.attchment e nel caso in cui non ci sia crearlo sennò prenderlo e attaccarlo al field.
> 
> ```
> attach_zip = self.env['ir.attachment'].create(
>                                     {'name': name.replace('.pdf', ''),
>                                     'datas': base64.b64encode(data),
>                                     'res_model': 'ifs_datamonitoring.documento',
>                                     'type': 'binary',
>                                     'res_id': self.id,
>                                     'mimetype': 'application/pdf'})
> self.attachment_ids = [(4, attach_zip.id)]
> ```
> Ricordiamo che "attachment_ids" è il field many2many dove verrà attaccato l'attachment
> ```
> attachment_ids = fields.Many2many('ir.attachment', string="Allegato", help="Pdf document", )
> ```
> Gestito il primo file, ci occupiamo del secondo che ricordiamo essere un file (.xml) che dovrà essere letto perciò:
> ```
> if name.endswith('.xml'):
>     with zip_file.open(name) as data_xml:
>         tree = self.xml_to_data(data_xml.read())
>         self.elaborate_data(tree)
>         self.state = 'check_section'
> ```
> La nuova variabile "tree"

> ![Screenshot from 2022-05-02 12-37-01.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651487851829/Rvr7gPh37.png align="left")
> 
> La funzione self.xml_to_data() che fa ritornare la variabile tree una "stringa" che può essere.
> ```
> def xml_to_data(self, xml_data):
>         tree = etree.fromstring(xml_data)
>         return tree
> ```
> 
4.Parliamo Invece di self.elaborate_data(tree), che contiene altre funzioni, elaborate_data() ha il "solo scopo" di raggruppare altre funzioni per non infilare tutte le funzioni una sotto l'altra ogni volta.
> ```
>     def elaborate_data(self, tree):
>         self.get_wbs(tree)
>         self.get_tipologia_strumento(tree)
>         self.get_progressiva(tree)
>         self.get_cdma(tree)
>         self.get_ssma(tree)
> ``` 
> Analizziamo la funzione get_wbs() che ha lo scopo di leggere il wbs all'interno del file(.xml) e assegnarlo alla variabile nella "form" in odoo.(deve essere autoinserito nel field)
> 
> Esempio di file (.xml)
> ![Screenshot from 2022-05-02 12-54-20.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651488881321/bfks0CZV8.png align="left")
> 
> ```
> def get_wbs(self, tree):
>         for name in tree.xpath('//Geo3D_Convergenze'):
>             if name:
>                 wbs = name.xpath('//Geo3D_Convergenze//FileHeader//Progetto//Titolo_2')[0].text
>                 if wbs:
>                     env = self.env['ifs_datamonitoring.wbs']
>                     wbs_ids = env.search([('name', '=', wbs[5:])])
>                     if not wbs_ids:
>                         self.wbs_id = env.create({
>                             'name': wbs[5:],
>                         })
>                     else:
>                         self.wbs_id = wbs_ids[0]
>                 else:
>                     raise ValidationError("Wbs non è definito all'interno del file .xml")
> ```
> 
> Dato che la variabile "tree" è considerabile una vera e propria tree di odoo, cicliamo il path e assegniamo alla variabile "wbs" il .text ovvero "stringa.
> 
> ```
> def get_tipologia_strumento(self, tree):
>         for name in tree.xpath('//Geo3D_Convergenze'):
>             if name:
>                 tipologia_strumento = name.xpath('//Geo3D_Convergenze//FileHeader//Creation//ToolName')[0].text
>                 if tipologia_strumento:
>                     env = self.env['ifs_datamonitoring.tipologiastrumento']
>                     tipologia_strumento_ids = env.search([('name', '=', tipologia_strumento)])
>                     if not tipologia_strumento_ids:
>                         self.tipologiastrumento_id = env.create({
>                             'name': tipologia_strumento,
>                         })
>                     else:
>                         self.tipologiastrumento_id = tipologia_strumento_ids[0]
>                 else:
>                     raise ValidationError("Tipologia Strumento non è definito all'interno del file .xml")
> ```
> FUNZIONE PER GESTIRE LA PROGRESSIVA
> 
> ```
> def get_progressiva(self, tree):
>         for name in tree.xpath('//Geo3D_Convergenze'):
>             if name:
>                 progressiva_numero = name.xpath('//Geo3D_Convergenze//FileHeader//Progessiva')[0].text
>                 if progressiva_numero: #esempio 5345.000
>                     env = self.env['ifs_datamonitoring.progressiva'] #il codice funziona c'è il punto sennò vi attaccate al cazzo
>                     cifre = progressiva_numero.split(".")  #["5345","000"]  [0,0]
>                     if len(cifre[0]) <= 3:
>                         km = str(0)
>                         metri = cifre[0]
>                     else:
>                         km = cifre[0][0:-3]
>                         metri = cifre[0][-3:]
>                     centesimi = cifre[1][0:2]
> 
>                     env_progressiva = self.env['ifs_datamonitoring.progressiva']
>                     progressiva_id = env_progressiva.search([('check_name', '=', km + metri + '.' + centesimi)],limit=1)
>                     if not progressiva_id:
>                         progressiva_id = env.create({
>                             'km': km,
>                             'metri': metri,
>                             'centesimi': centesimi,
>                             'wbs_id': self.wbs_id.id,
>                         })
>                         progressiva_id._set_name()
>                     self.progressiva_id = progressiva_id
> ```
> Funzione che prende il valore del cdma dal file .xml che poi verrà confrontato (in un altra funzione)
> ```
> def get_cdma(self, tree):
>         for name in tree.xpath('//Geo3D_Convergenze'):
>             if name:
>                 #CONVERGENZA DIAMETRALE MEDIA ATTENZIONE-ALLARME
>                 cdma = name.xpath('//Geo3D_Convergenze//Variazioni_Corda_Media/Value/@Delta')
>                 self.cdma_number = abs(float(cdma[-1]))
> ```
> Funzione che prende il valore del ssma dal file .xml che poi verrà confrontato (in un altra funzione)
> ```
> def get_ssma(self, tree):
>         for name in tree.xpath('//Geo3D_Convergenze'):
>             if name:
>                 #SPOSTAMENTO SINGOLA MIRA ATTENZIONE-ALLARME
>                 ssma_1 = name.xpath('//Geo3D_Convergenze//Asimmetrie_Trasversali/Value/@Spostamento_Trasversale_Mira_1')
>                 ssma_5 = name.xpath('//Geo3D_Convergenze//Asimmetrie_Trasversali/Value/@Spostamento_Trasversale_Mira_5')
>                 ssma_number_1 = abs(float(ssma_1[-1]))
>                 ssma_number_5 = abs(float(ssma_5[-1]))
>                 self.ssma_highest_number = max(ssma_number_1, ssma_number_5)
> ```
> 5. Nella funzione di partenza dove viene scompattato il file zip read_files_from_zip() lo state viene cambiato a "check_section" poichè in questo stato avverà il confronto dei dati ottenuti in precedenza
> ```
> self.state = 'check_section'
> ```
> In questo state deve essere selezionato una sezione (come in foto)
> 
> ![Screenshot from 2022-05-02 13-11-11.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651489898989/HiqNK9Kl2.png align="left")
> 
> Il campo presente è 
> ```
> sezione_id = fields.Many2one('ifs_datamonitoring.section', string='Sezione Applicata')
> ```
> Dopo aver selezionato il record, bisognerà schiacciare un bottone per confrontare i dati.(il flusso di confronto verrà visto in un altro articolo)
> ```
> <button type="object" class="oe_highlight" name="check_section_data"
> 							string="Controllo Sezione"
> 							attrs="{'invisible': [('state', '!=', 'check_section')]}" />
> ```
> 7. Dopo aver confrontato i dati questo è il risultato
> 
> ![Screenshot from 2022-05-02 13-14-28.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651490095326/hmVmtJLNG.png align="left")
