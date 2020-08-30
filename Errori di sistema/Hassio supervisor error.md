# Hassio supervisor error
## You are running an unsupported installation.
## Sta girando una installazione non supportata

Questo tipo di errore può capitare quando si ha installato un sistema base di linux come (si veda..) e non è lo stesso dello specifiche di Home Assitant.

Dato che Home Assistant lavora sul concetto del "paccketto completo", ovvero anche il sistema operativo chi stesse usando docker o python env potrebbe incappare in questo errore.

Esempio:

![](https://github.com/scintik/HomeAssistant_appunti_vari/blob/master/img/Supervisor_error.png)

*Supervisor -> System*

Qui home assistant gira su un Raspberry con distribuzione Stretch (9.x) ma dall'immagine si vede che il Supervisor e' il 235 (in questo caso) che richiede delle specifiche particolari.
Nella stessa schermata è possibile vedere che versione di Linux sta girando.

Cliccando sul link [[Learn More](https://github.com/home-assistant/architecture/blob/master/adr/0014-home-assistant-supervised.md)] si può leggere le specifiche:

>### Supported Operating System, System dependencies and versions
>
>Docker CE (Community Edition) is the only supported containerization method for Home Assistant Supervised. We only support FHS 3.0 on the host file system.
>
>- Docker CE >= 19.03
>- Systemd >= 239
>- NetworkManager >= 1.18.0
>- Avahi >= 0.7
>- AppArmor == 2.13.x (built into the kernel)
>- Debian Linux Debian 10 aka Buster (no derivatives)
>
>Only the above-listed version of Debian Linux is supported for running this installation method. When a new major version of Debian is released, the previous major version is dropped, with a deprecation time of 4 months. An exception to this rule occurs if the new version does not meet the requirements of the Supervisor.

Si può notare all'ultimo punto che e' supportato Debian 10 (al momento della stesura di questo articolo).
Avendo, sul Raspberry, una Debian 9 (Stretch) per tenere aggiornato Home Assistant bisogna aggiornare anche tutto il sistema alla versione richiesta (qui la 10, cioe' "Burst")

## Aggiornare
Per prima cosa provvederemo ad aggiornare il sistema alla versione attuale con i classici:
```bash
sudo apt-get update && sudo apt-get upgrade -y
```
Verifichiamo che tutto sia a posto, non ci siano errori dopo i precedenti comandi ed eventualmente corregerli.
```bash
dpkg -C
apt-mark showhold
```
### Preparare i sorgenti di apt-get
Aggiornare i sorgenti di apt-get rimpiazzando "stretch" con "burst" nei repository. 
```bash
sudo sed -i 's/stretch/buster/g' /etc/apt/sources.list    
sudo sed -i 's/stretch/buster/g' /etc/apt/sources.list.d/raspi.list
sudo sed -i 's/stretch/buster/g' /etc/apt/sources.list.d/docker.list
```
Verificare che questi comandi siano andati a vuon fine: equivale a dire che non avremo alcun tipo di output sulla console.
Per verificare le 2 righe sopra riportate possiamo eseguire 2 controlli:
- verificare sia stato tolto dai repository la versione *Stretch*
```bash
cat /etc/apt/sources.list | grep stretch
cat /etc/apt/sources.list.d/raspi.list | grep stretch
```
Se tutto è andato bene non dovremmo avere nessun output a schermo
- Se vogliamo vedere che sia stato messa la versione *Burst* nei ripository:
```bash
cat /etc/apt/sources.list | grep burst
cat /etc/apt/sources.list.d/raspi.list | grep burst
```
In questo caso dovremmo ottenere una o piu' righe con la parola evidenziata (su un raspberry che usa *bash* come console in genere la parola è in rosso)

Adesso diamo un comando per velocizzare i passaggi successivi: rimuoviamo il pacchetto che tiene traccia delle modifiche dell'elenco.
```bash
sudo apt-get remove apt-listchanges
```
### Facciamo l'Upgrade
Il modo piu' semplice e' l'esecuzione dei comanandi
```bash
sudo apt-get dist-upgrade
sudo apt-get install apt-listchanges
```

***
### Metodo alternativo...
**...che onestamente non mi ha convinto: da rivedere un po' tutto l'articolo!**

**ATTENZIONE** Scegliere uno solo dei due passaggi sotto riportati: **non** eseguirli entrambi!!!

(**NON consigliato** per il nostro caso ma qualcuno potrebbe preferirlo) 
Per aggiornare i pacchetti installati senza aggiornare il kernel ed i suoi moduli, eseguire:
```bash
sudo apt-get update && sudo apt-get upgrade -y
```
(**Consigliato**) 
Per aggiornare tutti i pacchetti installati, compreso kernel e moduli.
```bash
sudo apt-get update && sudo apt-get full-upgrade -y
```
Usate il *secondo* se non siete ultra sicuri di cosa state facendo!

*Entrambi i metodi potrebbero richiedere diversi minuti*

Puliamo i pacchetti obsoleti rimuovendoli.
```bash
sudo apt-get autoremove -y && sudo apt-get autoclean
```
Verifichiamo che siamo passati alla versione desiderata con:
```bash
cat /etc/os-release
```
Adesso è rimasto soltanto di eseguire un reboot
