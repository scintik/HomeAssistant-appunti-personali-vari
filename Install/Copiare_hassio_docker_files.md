# Hassio
## copiare docker files e db (Raspberry)

Ho una scheda SD con Rasbian (Raspi OS), con su anche il docker per Hassio (Home Assistant Supervisor). Gioca e rigioca con la distribuzione Raspbian alla fine ho incasinato tutto il sistema operativo e vorrei re installarlo da capo.
MA! Hassio e Home assistant funzionano benissimo e non vorrei perdere tutto! E nemmeno riconfigurare tutto. Potrei provare con gli snapshot.. idea!

Eseguo un backup che mi tenga anche i permessi utente della cartella /usr/share/**hassio**
```bash
cd /home/pi
sudo tar zcf /usr/share/hassio ./hassio
```
adesso nella cartella home ho il file *hassio.tar.gz* contenente tutto quanto ho "creato" fin'ora con home assistant.

Mi copio il file *hassio.tar.gz* su di una ciavetta USB o tramite ssh su di un altro computer, per poterlo usare in un secondo momento.

Adesso ho due opzioni: usare la stessa micro SD oppure un'altra per sicurezza.

Il caso vuole che ne abbia una seconda (per altro uguale, stessa marca, modello, capienza.. ma non importa).

Scarico l'ultima versione di Raspi OS (in caso di versione senza desktop si faccia attenzione e riferimento a @Install/---), 
copio l'immagine sulla micro SD ed infine mi configuro Il Raspberry come da guida @Install/--.

ATTENZIONE!! Abilitare SSH se avete copiato il file *hassio.tar.gz* su di un'altro computer e volete usare la conessione SSH.

Configuro, sempre come da guida @Install/---, anche il docker per avere un nuova immagine di Hassio supervisor.

Verifico che si apra la pagina **http**://192.168.x.x:8123 (senza la **s**) per vedere se tutto gira correttamente: 
dovrei avere la schermata d'inizio dove dovrei creare un nuovo utente ecc. ecc.

Bene inizia l'operazione di migrazione:

Fermiamo il docker con hassio *nuovo/vuoto*:
```bash
sudo docker stop homeassistant
```
rinominiamo la cartella da poco creata con la nuova installazione:
```bash
sudo mv /usr/share/hassio /usr/share/hassio_old
```
copiamo il vecchio hassio supervisor (ad esempio) nella cartella /home/pi e poi scompattiamo con:
```bash
cd /usr/share
sudo tar zxvf /home/pi/hassio.tar.gz ./
```
Quando riavremo la consolle disponibile (quindi finito di copiare il vecchio hassio) per sicurezza
procediamo con un reboot di tutto il piccolo Raspberry Pi.
```bash
sudo reboot
```
Lasciamo scatenarlo fino a che il led verde non si calma.

Adesso possiamo verificare con un browser all'indirizzo http**s**://192.168.x.x:8123 e dovremmo avere
la schermata di login e non più quella di inizializzazione, entriamo e tutto dovrebbe essere di nuovo li' al suo posto.


