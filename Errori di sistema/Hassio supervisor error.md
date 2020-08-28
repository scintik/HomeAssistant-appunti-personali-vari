# Hassio supervisor error
## You are running an unsupported installation.
## Sta girando una installazione non supportata

Questo tipo di errore può capitare quando si ha installato un sistema base di linux come (si veda..) e non è lo stesso dello specifiche di Home Assitant.

Dato che Home Assistant lavora sul concetto del "paccketto completo", ovvero anche il sistema operativo chi stesse usando docker o python env potrebbe incappare in questo errore.

Esempio:

![](https://github.com/scintik/HomeAssistant_appunti_vari/blob/master/img/Supervisor_error.png)

Qui home assistant gira su un Raspberry con distribuzione Stretch (9.x) ma dall'immagine si vede che il Supervisor è il 235 che richiede delle specifiche particolari.
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
Avendo, sul Raspberry, una Debian 9 (stretch) per tenere aggiornato Home Assistant bisogna aggiornare anche tutto il sistema alla versione richiesta (qui la 10, cioe' "burst")

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
```

Verificare che questo li abbia rilevati tutti eseguendo quanto segue, senza aspettarsi alcun output.
Verify this caught them all by running the following, expecting no output. 
Se il comando restituisce qualcosa dopo aver eseguito in precedenza i comandi sed sopra, significa che potrebbe essere necessario modificare più file.
If the command returns anything having previously run the sed commands above, it means more files may need tweaking. 
Run the sed command for each. The aim is to replace all instances of “stretch”.
Esegui il comando sed per ciascuno. L'obiettivo è sostituire tutte le istanze di "stretch".

Velocizziamo i passaggi successivi rimuovendo il pacchetto di modifica dell'elenco.
```bash
sudo apt-get remove apt-listchanges
```

*da finire*

# Do the Upgrade

To update existing packages without updating kernel modules or removing packages, run the following.

$ sudo apt-get update && sudo apt-get upgrade -y

Alternatively, to include kernel modules and removing packages if required, run the following (choose one, not both. See this question for details).

$ sudo apt-get update && sudo apt-get full-upgrade -y

Cleanup old outdated packages.

$ sudo apt-get autoremove -y && sudo apt-get autoclean

Verify with cat /etc/os-release.
Update Firmware

You’ve come this far, might as well get the latest firmware.

$ sudo rpi-update    

