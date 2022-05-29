## TASK: Cambiare password di un utente di odoo da termina

Per controllare tutti gli utenti in un db dobbiamo entrare da terminale nel db. Per fare ciò basterà aprire un terminale e digitare psql "nome_db"
```
luca@pop-os:~$ psql contabilita 
psql (12.9 (Ubuntu 12.9-0ubuntu0.20.04.1))
Type "help" for help.

contabilita=#
```

Fatto ciò per accedere alla tabella degli "utenti di odoo" scriviamo 
```
luca@pop-os:~$ psql contabilita 
psql (12.9 (Ubuntu 12.9-0ubuntu0.20.04.1))
Type "help" for help.

contabilita=# select * from res_users;
```
Si aprirà una tabella "Lunghissima" ma ci interessanno solo le colonne relative al "login" e la "password". 
```
id | active |              login               |                                                              password                                                              | company_id | partner_id |        create_date         |                         signature                         | action_id | share | create_uid | write_uid |         write_date         | totp_secret | notification_type |  odoobot_state   | odoobot_failed | sale_team_id | target_sales_won | target_sales_done | target_sales_invoiced | website_id 
----+--------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+------------+------------+----------------------------+-----------------------------------------------------------+-----------+-------+------------+-----------+----------------------------+-------------+-------------------+------------------+----------------+--------------+------------------+-------------------+-----------------------+------------
  4 | f      | public                           | $pbkdf2-sha512$25000$ZEzp3buXcq61NiaEsNZ6jw$Zmqo.LGEzdE6WeqyIm4o51mdAOsCL5H1Qz8ru4PLTQH8yQMcd432pTXZNIiOfl.kuJYhzRGG27VYmXnCr1ohzQ |          1 |          4 | 2020-12-14 14:28:39.813116 |                                                           |           | t     |          1 |         1 | 2020-12-14 14:28:39.813116 |             | email             |                  |                |              |                  |                   |                       |           
  5 | f      | portaltemplate                   |                                                                                                                                    |          1 |          6 | 2020-12-14 14:28:39.813116 |                                                           |           | t     |          1 |         1 | 2020-12-14 14:28:39.813116 |             | email             |                  |                |              |                  |                   |                       |           
  8 | t      | esempio@example.com                  | $pbkdf2-sha512$25000$1xqjtNb6v1fqXcuZc86Z8w$rVnsCH1FFiCFc7qWKu19l3tXgStMJwV.kEEyJVstL/C1WR7wSnwR7bxar3IPLy3QJfJ2LIjv8JJQzgaaB1g/.A |          1 |         43 | 2021-06-23 09:18:14.449059 | <p><br></p>                                               |           | f     |          2 |         2 | 2022-05-05 14:17:30.3961   |             | email             | onboarding_emoji |                |              |                  |                   |                       |           
  9 | t      | esempio | $pbkdf2-sha512$25000$ao3R2ntvzXmPUYpxbo3xPg$mcbajCjGHJvfyx7pl8wVzL2DHZA8kWmWmnQ.p6.CEAej8ntH7RNusSwAagIk4S9q/xkuK01yGDJ.PLqEIHv3vA |          1 |        218 | 2022-01-21 11:49:39.500399 | <p><br></p>                                               |           | f     |          2 |         2 | 2022-05-05 14:17:30.3961   |             | email             | onboarding_emoji |                |              |                  |                   |                       |           
 10 | t      | utente@example.com               | $pbkdf2-sha512$25000$WGvNGYOQ8v4/5xwj5FxLaQ$rNxr5fva08RoJfPrjEIRweAGD3Vn2BOgHVKV3qLDOa/6DeL/WicpyR.opiOCtCi01J.uOR9evuXA68YU1hmdQg |          1 |        219 | 2022-01-25 17:36:58.013343 | <p><br></p>                                               |           | f     |          2 |         2 | 2022-05-05 14:17:30.3961   |             | email             | onboarding_emoji |                |              |                  |                   |                       |           
  7 | t      | example@example.it                 | $pbkdf2-sha512$25000$/F.rFcIYg1CqlVIq5Zwz5g$8HX/WzZxnOJ7RPkdsraSKL20GPGA.6m.HkS2FYDAYN7Yt83W6SxttCd2AbrA2Om8vPf/mjYQYgIXiFZQzSUy8Q |          1 |         41 | 2021-06-22 07:14:26.194184 | <p><br></p>                                               |           | f     |          2 |         2 | 2022-05-05 14:17:30.3961   |             | email             | onboarding_emoji |                |              |                  |                   |                       |           
  3 | f      | default                          |                                                                                                                                    |          1 |          5 | 2020-12-14 14:28:39.813116 |                                                           |           | f     |          1 |         2 | 2022-05-05 14:17:30.3961   |             | email             |                  |                |              |                  |                   |                       |           
  1 | f      | __system__                       |                                                                                                                                    |          1 |          2 | 2020-12-14 14:28:39.813116 | <span data-o-mail-quote="1">-- <br data-o-mail-quote="1">+|           | f     |            |         2 | 2022-05-05 14:17:30.3961   |             | inbox             | disabled         |                |              |                  |                   |                       |           
    |        |                                  |                                                                                                                                    |            |            |                            | System</span>                                             |           |       |            |           |                            |             |                   |                  |                |              |                  |                   |                       | 
  2 | t      | a                                | $pbkdf2-sha512$25000$2PsfwxiDMCbEeA9hTGkt5Q$wQEx.uk7ETUT2yZ7M6EMux4VPVWIMdJvCAUU/8NLV0WZUZjJl.SsaOlEEcMRmseTJ8W2nz97cnqT2Fcc9KyVew |          1 |          3 | 2020-12-14 14:28:39.813116 | <span data-o-mail-quote="1">-- <br data-o-mail-quote="1">+|           | f     |          1 |         2 | 2022-05-05 14:17:30.3961   |             | email             | onboarding_emoji |                |            1 |                  |                   |                       |           
    |        |                                  |                                                                                                                                    |            |            |                            | Administrator</span>                                      |           |       |            |           |                            |             |                   |                  |                |              |                  |                   |                       | 
(9 rows)
```
Qui abbiamo il quadro generale degli utenti in odoo però non possiamo ancora accedere se non sappiamo la "traduzione" delle password. Perciò ci basterà cambiare la password da terminale così:
```
luca@pop-os:~$ psql contabilita 
psql (12.9 (Ubuntu 12.9-0ubuntu0.20.04.1))
Type "help" for help.

contabilita=# update res_users set "password" = '$pbkdf2-sha512$25000$SInROmcM4RwD4FwLIWQMAQ$duQYpkMNNT9Wz7vjoxHrDv9CG/8lFZCmsfPRaB3JvUNLGCJ1yoS29ybPI/w8pZ/q1tSEydCTs9OQKOC7qX/R1w' where login = 'esempio';
UPDATE 0
contabilita=#
```
In questo caso UPDATE È 0 perciò non ha trovato utenti come 'esempio',basterà inserire un utente nella colonna login e cambiargli la password. Il gioco è fatto.