# DA SCRIVERE!!
##sotto appunti presi a giro per la rete con ERRORI.

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
