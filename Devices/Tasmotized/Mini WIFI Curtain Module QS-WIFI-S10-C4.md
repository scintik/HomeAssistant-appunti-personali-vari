# Avvolgibili (shutter-curtain-blind)

#### Modulo MiniCurtain <bR> Mini WIFI Curtain Module QS-WIFI-S10-C4 (Tuya)

<img src="../Images/MiniWIFICurtainModule.png" alt="Mini WIFI Curtain Module" style="width: 30%;">
<!--- 
![Mini WIFI Curtain Module](../Images/MiniWIFICurtainModule.png)
--->
Il modulo da me acquistato usa un modulo **CBLC9**, al cui interno si trova un chip BK7231N programmato per il mondo *Tuya*.
Dopo un primo tentativo, funzionante, con [OpenBeken](https://www.elektroda.com/rtvforum/topic4049271.html) è stato più arduo (impossibile?) integrarlo in *Home Assistant* come tapparella, per il motivo di come veniva esposto il device su MQTT dal firmware. I dispositivi per comando tapparelle dotati di Tasmota, vengono presentati in MQTT (o per ESPHome) come dispositivo unico con diverse entità (switch e ralay) incorporate. OpenBeken presenta ogni singolo switch separatamente. Si veda [qui](https://github.com/openshwprojects/OpenBK7231T_App/blob/main/docs/homeAssistant.md) consultato il 15/09/2026.

<img src="../Images/CBLC9_default.png" alt="CBLC9" style="width: 50%;">
<!--- 
![CBLC9](../Images/CBLC9_default.png)
--->
Dopo una prima idea di intervenire sul software cercando di andare a capire il funzionamento del protocollo MQTT, mi sono reso conto che era fuori dalla mia portata.
Alla fine ho optato per una modifica hardware sostituendo il modulo *CBLC9* con un *ESP-02S* e programmazione di quest'ultimo con Tasmota.

<img src="../Images/ESP-02s.png" alt="ESP-02s" style="width: 50%;">
<!--- 
![ESP-02s](../Images/ESP-02s.png)
--->

Questo ha comportato l'apertura dello scatolino e la <u>dissaldatura</u> del modulo originale.
Poi, oviamente, sulle piazzole del PCB sono stati saldati gli otto fili necessari e <u>riportati fuori dalla scatola</u> per poter essere connessi al <u>modulo ESP-02S posto esternamente</u>.

Sul modulo CBCL9 abbiamoi i seguenti piedini (con relative funzioni):

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

**`{“NAME”:”NOME_DEVICE”,”GPIO”:255,255,0,255,0,22,255,17,21,56,0,0,0],”FLAG”:0,”BASE”:39}`**

*N.b. La stringa NOME_DEVICE può essere personalizzata.*

Selezionare la spunta “**Activate**” e cliccare poi su “**Save**“.

Portarsi ora sul menu principale, entrare su “<u>Console</u>” e inserire il seguente comando:

$\color{green}{\textbf{SetOption80 1}}$  ([Rif.](https://tasmota.github.io/docs/Blinds-and-Shutters/))

che restituirà il seguente output: $\color{blue}{\textbf{ RESULT = {“SetOption80″:”ON”} }}$

Adesso è necessario attivare l’interblocco tra i due tasti/comandi di salita e discesa. Questo per fare in modo che i due canali di erogazione elettrica non siano mai attivati contemporaneamente con rischio (praticamente certezza) di bruciare il motore della tapparella.

Sulla console digitiamo i seguenti comandi:

$\color{green}{\textbf{Interlock 1,2}}$

$\color{green}{\textbf{Interlock ON}}$

Ed infine la modalità di funzionamento dei singoli pulsanti. Tipicamente: SU/STOP e GIU/STOP ovvero, alla prima pressione si invia il comando di salita o discesa alla seconda pressione si ferma la procedura, cioè blocchiamo la tapparella dove si trova.

$\color{green}{\textbf{ShutterRelay1 1}}$  ([Rif.](https://tasmota.github.io/docs/Blinds-and-Shutters/#shutter-modes))

<!---  ###### Da finire ######   https://indomus.it/guide/gestire-le-tapparelle-elettriche-tramite-firmware-tasmota-v2/

Cronometraggio
Ora è necessario cronometrare i tempi di alzata e discesa della tapparella (a meno che non siano già disponibili perché ereditati dalla fork).

Per farlo, portare la tapparella nella posizione di massima chiusura, attivare la salita cronometrandone la durata fino al punto di massima apertura; effettuare poi la cosa contraria misurando la discesa. Si consiglia la massima accuratezza possibile.

Ora, sempre alla voce “Console” dell’interfaccia web, eseguire il seguente comando:

ShutterSetClose 1

che confermerà al firmware la posizione di chiusura della tapparella:

RESULT = {“ShutterSetClose1″:”Configuration reset”}

Per impostare il tempo di apertura totale della tapparella, se il tempo è ad esempio di 28 secondi, digitiamo:

ShutterOpenDuration1 28

Il risultato sarà:

ShutterMode: 1
RESULT = {“ShutterOpenDuration1”:28.0}

Per impostare il tempo di chiusura totale della tapparella, se il tempo è ad esempio di 25 secondi, digitiamo:

ShutterCloseDuration1 25

Il risultato sarà:

ShutterMode: 1
RESULT = {“ShutterCloseDuration1”:25.0}
 

Complimenti: il vostro attuatore è così configurato correttamente.

--->

