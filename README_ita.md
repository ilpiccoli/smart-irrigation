## **Versione in italiano**
# Irrigazione Smart V1.2

Ho cercato per settimane un'automazione che funzionasse bene ma fosse al tempo stesso semplice per irrigare le piante sul mio terrazzo. Fino ad ora ho usato una centralina di irrigazione a betteria e ha sempre funzionato perfettamente, ma aveva due difetti: 
1) Non saltava il turno di irrigazione nel caso stia piovendo (o abbia piovuto il giorno prima), quindi nei giorni di pioggia il terrazzo grondava acqua (e sprecavo litri di acqua senza alcun motivo);
2) Aveva possibilità limitate in fatto di programmazione, ma soprattutto irrigava ogni giorno per lo stesso tempo, indipendentemente dalle condizioni meteo.
Ho trovato alcune alternative ma erano o troppo complicate o progettate per il giardino (irrigo le mie piante da 1 a 3 minuti al giorno, mentre quei programmi mi suggerivano di irrigare per più di 10 minuti al giorno), quindi ho creato questa automazione su HomeAssistant, personalizzabile e molto semplice.

# Pre-Requisiti
- Uso questa automazione per controllare il mio [Shelly 1](https://amzn.to/3iJHOZV) connesso ad [un'elettrovalvola normalmente chiusa](https://amzn.to/3iJHOZV), quando lo Shelly si accende, la valvola si apre. Avrete bisogno di un interruttore/centralina che possiate controllare tramite homeassistant, come vedrete è semplicissimo modificare l'automazione per adattarla alle vostre necessità;
- **L'integrazione OpenWeatherMap configurata e funzionante**, è necessario registrarsi e ottenere un API, è molto semplice, per ogni informazione potete visitare la [pagina dell'integrazione](https://www.home-assistant.io/integrations/openweathermap/) ;
- Avrete bisogno di alcuni aiutanti, in particolare:
  - **Un _input_number_ per la durata base dell'irrigazione** (nel mio caso si chiama _input_number.durata_irrigazione_);
  - **Un _input_number_ che verrà usato per registrare la temperatura massima di oggi** (nel mio caso si chiama _input_number.temperatura_massima_oggi_ e ha passo 1);
  - **Un _input_number_ che verrà usato per registrare la temperatura massima di ieri** (nel mio caso si chiama _input_number.temperatura_massima_ieri_ e ha passo 1);
  - **Un _input_number_ che verrà usato per registrare le precipitazioni di oggi** (nel mio caso si chiama _input_number.pioggia_oggi_ e ha passo 0.1);
  - **Un _input_number_ che verrà usato per registrare le precipitazioni di ieri** (nel mio caso si chiama _input_number.pioggia_ieri_ e ha passo 0.1).
- Avrete bisogno **un sensore _history_stats_ che verrà usato nella notifica** per dirvi per quanto la presa è rimasta accesa la presa (nel mio caso si chiama _sensor.irrigazione_terrazzo_oggi_).

# Installazione
0) Avrete bisogno di avere già collegato lo Shelly ad HomeAssistant (nel mio caso si chiama _switch.irrigazione_terrazzo_) e avere già configurato l'integrazione di OpenWeatherMap;
2) Dovrete copiare _**sensori_history_stats.yaml**_ nella vostra cartella "sensor" (se avete [splittato la vostra configurazione](https://www.home-assistant.io/docs/configuration/splitting_configuration/)) , oppure potete copiare il suo contenuto sotto a _sensor:_ nel file _configuration.yaml_;
3) Dovrete copiare _**auto_irrigazione.yaml**_ nella vostra cartella "automation", o potete copiare il suo contenuto sotto _automations:_ nel file _configuration.yaml_;
4) Riavviate HomeAssistant;
5) Se avete fatto tutto correttamente dovreste avere una nuova automazione già attiva.

# Come funziona?
1) Ogni giorno a 00:00 il sensore "Pioggia Oggi" e "Temperatura Massima Oggi" vengono resettati;
2) Ogni ora il sensore "Pioggia Oggi" aumenta il suo valore in base alle precipitazioni dell'ultima ora;
3) Ogni volta che il sensore temperatura cambia, se è più alto del valore, il valore "Temperatura Massima Oggi" viene aggiornato;
4) Quanto la condizione "trigger" viene soddisfatta (nel mio caso quando quando il sole è più alto di 35°) l'automazione viene avviata;
5) Se "Pioggia Ieri" è maggiore di 4mm o "Pioggia Domani" è maggiore di 4mm, l'automazione **non parte**;
6) L'irrigazione viene avviata;
7) Il contatore "delay" viene avviato, in base a "Temeperatura Massima Ieri", se T>30 dura 3 volte la durata base, se T>25 per 2 volte, se T>20 per la durata standard, altrimenti (se T<20) dura la metà della durata base;
8) L'irrigazione viene spenta;
9) La notifica viene inviata all'app;
10) Ogni giorno, alle 23:59, "Pioggia Ieri" copia il valore dal sensore "Pioggia Oggi" e "Temperatura Massima Ieri" copia il valore "Temperatura Massima Oggi".

# Problemi noti
- **L'automazione è progettata per funzionare con solo 1 irrigatore;**
    - *Soluzione alternativa: Potreste creare un gruppo o aggiungere più switch prima e dopo il delay per farli funzionare tutti in contemporanea, oppure potete aggiungere un'altra sequenza accesa-delay-spenta (prima della parte legata alla notifica)*.
    
- **L'automazione è progettata per irrigare tra 00:00 e 23:59, se l'irrigazione parte prima delle 23:59 e finisce dopo 00:00 l'automazione non funziona correttamente;**        
    - *Soluzione alternativa: Ci sono molti modi per evitare questo: si deve resettare a un orario diverso e cambiare la configurazione del sensore _history_stats_.* 

# Come puoi supportarmi?
Compra attraverso i link che ho lasciato sopra, sono link affiliati ad Amazon, guadagno una piccola percentuale di quello che paghi (a te non costa nulla!), oppure [offrimi un caffè](https://www.buymeacoffee.com/ilpiccoli
    
- **L'automazione è progettata per irrigare solo una volta al giorno;**    
    - *Soluzione alternativa: potete aggiungere altri trigger per fare partire l'automazione in base ad altri eventi o per avviarla in più orari.*
    
- **L'automazione è progettata per partire quando l'inclinazione del sole, poichè il mio terrazzo è orientato verso ovest e il sole colpisce le piante solo quando è >40°;**    
    - *Soluzione alternativa: potete scegliere un altro tipo, per esempio in base all'orario, a un altro evento o per altri tipi di trigger.*
