# Avvolgibili (shutter-curtain-blind)

#### Modulo MiniCurtain <bR> Mini WIFI Curtain Module QS-WIFI-S10-C4 (Tuya)

<img src="../Images/MiniWIFICurtainModule.png" alt="Mini WIFI Curtain Module" style="width: 30%;"></img>
<!--- 
![Mini WIFI Curtain Module](../Images/MiniWIFICurtainModule.png)
--->
Il modulo da me acquistato usa un modulo **CBLC9**, al cui interno si trova un chip BK7231N programmato per il mondo *Tuya*.
Dopo un primo tentativo, funzionante, con [OpenBeken](https://www.elektroda.com/rtvforum/topic4049271.html) è stato più arduo (impossibile?) integrarlo in *Home Assistant* come tapparella, per il motivo di come veniva esposto il device su MQTT dal firmware. Nota, si può gestire anche tramite plugin "Tasmota". I dispositivi per comando tapparelle dotati di Tasmota, vengono presentati in MQTT (o per ESPHome) come dispositivo unico con diverse entità (switch e ralay) incorporati. OpenBeken presenta ogni singolo switch separatamente. Si veda [qui](https://github.com/openshwprojects/OpenBK7231T_App/blob/main/docs/homeAssistant.md) (consultato il 15/09/2026).

<img src="../Images/CBLC9_default.png" alt="CBLC9" style="width: 50%;"></img>

*Immagine di esempio che potrebbe essere diversa*

<!--- 
![CBLC9](../Images/CBLC9_default.png) 
--->
Dopo una prima idea di intervenire sul software cercando di andare a capire il funzionamento del protocollo MQTT, mi sono reso conto che era fuori dalla mia portata.
Alla fine ho optato per una modifica hardware sostituendo il modulo *CBLC9* con un *ESP-02S* e programmazione di quest'ultimo con Tasmota.

<img src="../Images/ESP-02s.png" alt="ESP-02s" style="width: 50%;"></img>
<!--- 
![ESP-02s](../Images/ESP-02s.png)
--->

Questo ha comportato l'apertura dello scatolino e la <u>dissaldatura</u> del modulo originale.
Poi, oviamente, sulle piazzole del PCB sono stati saldati gli otto fili necessari e <u>riportati fuori dalla scatola</u> per poter essere connessi al <u>modulo ESP-02S posto esternamente</u>.

Sul modulo CBCL9 abbiamo i seguenti piedini (riporto anche le relative funzioni):

| Nome | Funzione | Colore assegn.|
| - | - | - |
| P8 | LED | $\color{#ff7900}{\text{Arancio}}$ |
| P7 | Relay DOWN| $\color{brown}{\text{Marrone}}$ |
| VCC | 3.3V | $\color{red}{\text{Rosso}}$ |
| GND | GND | Nero |
|| Altro lato||
| P9 | Relay UP | $\color{blue}{\text{Blu}}$ |
| P6 | Switch esterno SU | $\color{purple}{\text{Viola}}$ |
| P24 | Switch interno | $\color{green}{\text{Verde}}$ |
| P26 | Switch Esterno GIU | $\color{#ffcc00}{\textsf{Giallo}}$ |

Prima di saldare l'**ESP-02S** ai vari cavi è stato caricato su quest'ultimo il firmware Tasmota ultima versione.

La connessione dei cavi su ESP ha seguito combinazione
| Pin | Colore |
| - | - |
| RST | -- NC --|
| A0 | -- NC --|
| 13 | $\color{blue}{\text{Blu}}$ |
| 04 | $\color{#ffcc00}{\textsf{Giallo}}$ | 
| 05 | $\color{green}{\text{Verde}}$ |
| Altro lato |
| 14 | $\color{purple}{\text{Viola}}$ |
| 12 | $\color{brown}{\text{Marrone}}$ |
| TX | $\color{#ff7900}{\text{Arancio}}$ |
| RX | -- NC --|
| GND | Nero |
| VCC | $\color{red}{\text{Rosso}}$ |

Una volta avviato il modulo per la prima volta Tasmota si metterà in modalità AP e collegandosi possiamo inserire i parametri della nostra rete.
A questo punto va configurato il firmware Tasmota in modo da fargli apprendere su quale attuatore esso si trovi.
Andare nel menu “**Configuration**” > “**Module**” e impostare il modello d’interesse (eg. Sonoff Dual).  <---

Poi recarsi alla voce di menu “**Configuration**” > “**Configure Other**” e impostare la seguente stringa:

~~**`{"NAME":"SC500W","GPIO":[0,0,0,576,160,161,0,0,224,32,225,0,0,0],"FLAG":0,"BASE":18}`**~~

*N.b. La stringa NOME_DEVICE può essere personalizzata.*
(La stringa sopra è sbagliata: va ricalcolata)

Selezionare la spunta “**Activate**” e cliccare poi su “**Save**“.

Portarsi ora sul menu principale, entrare su “<u>Console</u>” e inserire il seguente comando come prima cosa per definire che il dispositivo è un attuatore per tapparelle, tende, ecc:

> $\color{green}{\textbf{SetOption80 1}}$  ([Rif.](https://tasmota.github.io/docs/Blinds-and-Shutters/))

che restituirà il seguente output: $\color{blue}{\textbf{ RESULT = {“SetOption80″:”ON”} }}$

Adesso è necessario attivare l’interblocco tra i due tasti/comandi di salita e discesa. Questo per fare in modo che i due canali di erogazione elettrica non siano mai attivati contemporaneamente con rischio (praticamente certezza) di bruciare il motore della tapparella.

Sulla console digitiamo i seguenti comandi:

> $\color{green}{\textbf{Interlock 1,2}}$

> $\color{green}{\textbf{Interlock ON}}$

Ed infine la modalità di funzionamento dei singoli pulsanti. Tipicamente: SU/STOP e GIU/STOP ovvero, alla prima pressione si invia il comando di salita o discesa alla seconda pressione si ferma la procedura, cioè blocchiamo la tapparella dove si trova.

> $\color{green}{\textbf{ShutterRelay1 1}}$  ([Rif.](https://tasmota.github.io/docs/Blinds-and-Shutters/#shutter-modes))

**Incongruenza** Se non metto *SwitchMode1 3* e *SwitchMode2 3* sembra no funzionare quanto sopra: **da approfondire**

Ora è necessario cronometrare i tempi di alzata e discesa della tapparella. <br\>
Per farlo portarsi in una delle due posizioni estreme (tutto aperto o tutto chiuso) e attivare la funzione desiderata cronometrando il tempo che impiega la tapparella e poi viceversa. <br\>
Partiamo con la situazione in cui la tapparella è tutta aperta.<br\>
In <i>console</i> diamo il comando:
> $\color{green}{\textbf{ShutterSetOpen 1}}$
Quindi digitiamo i tempi di salita e discesa (ad esempio 32 secondi per chiudere e 38 per aprire):
> $\color{green}{\textbf{ShutterOpenDuration1 32}}$
> $\color{green}{\textbf{ShutterCloseDuration1 38}}$

In console avremo sempre una risposta di tipo $\color{blue}{\textbf{ RESULT = {“RESULT=.."}}$ con il comando eseguito e cosa è successo.

A questo punto l'attuatore dovrebbe funzionare correttamente, eventualmente ricontrollare i vari passaggi con calma.

--->

