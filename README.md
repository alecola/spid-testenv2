# spid-testenv2
Identity Provider di test per SPID

[![Join the #spid-testenv channel](https://img.shields.io/badge/Slack%20channel-%23spid--testenv-blue.svg?logo=slack)](https://developersitalia.slack.com/messages/C7ESTMQDQ)
[![Get invited](https://slack.developers.italia.it/badge.svg)](https://slack.developers.italia.it/)
[![SPID on forum.italia.it](https://img.shields.io/badge/Forum-SPID-blue.svg)](https://forum.italia.it/c/spid)

Questo repository ospita lo sviluppo di un nuovo Identity Provider di test per consentire agli sviluppatori di verificare le proprie integrazioni con [SPID](https://www.spid.gov.it) in modo semplice, ottenendo messaggi diagnostici chiari ed essere certi dell'interoperabilità.

## Board Kanban attività
[SPID Environment](https://trello.com/b/ISkTVBDY/spid-environment)

## WORK IN PROGRESS
Per ora si raccomanda di usare l'IdP di test già disponibile in https://github.com/italia/spid-testenv 

## Istruzioni

1) Create ed attivare un virtualenv
    ```
    virtualenv -p /path/to/pythonbin env
    . env/bin/actiate
    ```

2) Installare i pacchetti necessari tramite pip
    ```
    pip install -r requirements.txt
    ```

3) Creare e configurare il file config.json o config.yaml
    ```
    cp config.<ext>.example config.<ext>
    ```

4) Lanciare l'eseguibile
    ```
    python spid-testenv.py
    ```

## Generazione certificato

Per generare un certificato digitale da associare all'IdP utilizzare il seguente comando

```
openssl req -x509 -nodes -sha256 -days 365 -newkey rsa:2048 -keyout idp.key -out idp.crt
```

Nota: Le path dei due file (chiave e certificato) vanno inserite nel file di configurazione config.* .

## Ansible install

Alternativamente alla procedura di installazione manuale riportata sopra, è possible installare l'Identity Provider di test tramite lo strumento di configuration management [ansible](https://www.ansible.com/).

Prima di iniziare:

- effettuare il provisioning dell'[host](https://docs.ansible.com/ansible/devel/reference_appendices/glossary.html#term-host) su cui si vuole installare il testenv (che può essere una macchina fisica, una macchina virtuale o un container), con il sistema operativo **Debian 9.4 (stretch)**

- assicurarsi che sull'host:
  - python sia installato (è richiesto da ansible)
  - esista un utente non privilegiato (username di difetto: `simevo`)

- assicurarsi che il controller (il computer da cui si intende controllare l'host):
  - abbia ansible 2.2 o posteriori installato
  - possa raggiungere l'host con un FQDN (valore di difetto `idp.simevo.com`)
  - possa effettuare l'accesso ssh con chiave crittografica come utente root e come utente non privilegiato

Configurare le variabili nel file `ansible/spid-testenv2_vars.yml` e il nome dell'host in `ansible/hosts` avviare l'installazione con il comando:
```
ansible-playbook -i ansible/hosts ansible/site.yml
```

Verifica dell'installazione: [https://idp.simevo.com/metadata](https://idp.simevo.com/metadata) ritorna i metadati dell'IDP.

## Logging

I log del flusso di login / logout viene registrato nel file idp.log (tramite configurazione pysaml2) e
tramite log di Flask.

## Endpoints

* `/add-user` (GET, POST), accesso libero: Consente di aggiungere un utente sull'idp impostandone eventuali attributi spid primari e secondari. Ritorna una response con status code 200.
* `/login` (GET, POST), accesso tramite chiave salvata in sessione: Gestisce e processa una AuthnRequest ritornando eventuali messaggei di errore.
* `/continue-response` (POST), accesso tramite chiave salvata in sessione: Ritorna una response per la AuthnRequest in ingresso se l'utente acconsente a trasferire gli attributi richiesti.

Nota: gli endpoint (e rispettivi binding) per SSO e SLO sono impostati tramite file di configurazione.

## Link
* [SPID](https://developers.italia.it/it/spid) su Developers Italia
