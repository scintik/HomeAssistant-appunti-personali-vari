# Hassio supervisor error
## Sta girando una installazione non supportata (You are running an unsupported installation.)

Questo tipo di errore *può capitare* quando si ha installato un sistema base di linux come (si veda..) e non è lo stesso dello specifiche di Home Assitant.

Dato che Home Assistant lavora sul concetto del "pacchetto completo" (ovvero fornire anche il sistema operativo) chi stesse usando docker o python env potrebbe incappare in questo tipo di messaggio.

Esempio:

![](https://github.com/scintik/HomeAssistant_appunti_vari/blob/master/img/Supervisor_error.png)

*Supervisor -> System*

Nel nostro caso, da cui è trtto l'esempio, Home Assistant gira su un Raspberry con distribuzione Stretch (9.x) e dall'immagine sopra si vede che il Supervisor e' il 235 (in questo caso) che richiede delle specifiche particolari.
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

Vedremo come aggiornare il sottosistema ma è importante tenere presente che:
### NON E' DETTO CHE BASTI AGGIORNARE
Infatti come dicevo prima Home Assistant è considerato dagli sviluppatori un blocco unico da loro fornito.

Nel nostro caso è probabile che anche l'aggiornamento a Raspbian 10 Burst (o RaspyOS 10 come si chiamerà), no nrisolverà il problema poichè almeno nelle prime versioni delle probabile release NetworkManager non soddisfera' le caratteristiche.

**E' grave avere questo errore?**
Non esiste una risposta precisa, poichè bisogna vedere cosa cambia da una versione all'altra di un specifico pacchetto e cosa e' stato usato dagli svillupatori. Per quanto possa sembrare una risposta imprecisa attualemente vale la regola di provare e vedere se tutto funziona ed eventualemnte dare uno sguardo anche ai log che non ci siano segnali di ERROR.

## Aggiornare il sottosistema Linux
Si veda articolo...

