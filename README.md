# SISMillingMachine
sis project for the computer architecture course

## Project description (Italian)

Si progetti un dispositivo per la gestione di un centro di lavoro per asportazione di truciolo (fresa). Il 
sistema è composto da una macchina utensile a controllo 
numerico (Milling Machine) e due unità di controllo qualità poste prima dell’ingresso e dopo l’uscita del 
pezzo dalla macchina.
Il dispositivo di controllo prende in ingresso una sequenza di 7 bit, che assumono significato diverso in 
funzione dello stato in cui si trova l’impianto.

| Impianto Off    | ON2 | ON1   | ON0   |       |       |       |       |
|-----------------|-----|-------|-------|-------|-------|-------|-------|
| Check In        | QI  | VIN5  | VIN4  | VIN3  | VIN2  | VIN1  | VIN0  |
| Milling machine | EM  | VOUT5 | VOUT4 | VOUT3 | VOUT2 | VOUT1 | VOUT0 |
| Check Out       | Q0  |       |       |       |       |       |       |

Il principio di funzionamento dell’impianto è espresso dalle seguenti fasi:
- L’impianto è inizialmente spento (O/I=0). L’impianto si accende quando il comando di accensione 
(ON) di 3 bit è composto dalla sequenza 111 (ON=111). L’impianto si accende (O/I=1) e inizia a 
lavorare aprendo il gate A (GA=1) e permettendo il caricamento di un pezzo grezzo nell’unità di 
controllo in ingresso. Il dispositivo deve inoltre incrementare un contatore dei pezzi in ingresso 
(NA).
- Nel modulo di ispezione prodotti in ingresso viene controllata la qualità del pezzo grezzo (QI, 
1=buono, 0=scarto) e ne viene misurato il volume (VIN) espresso in 6 bit. 
    - Se il pezzo risulta scarto (QI=0), il dispositivo deve aprire il gate B (GB=1) mandando il 
pezzo in un deposito di scarti e aumentare il contatore dei pezzi scartati in ingresso (NB). 
Al ciclo successivo il dispositivo ricomincia il ciclo aprendo il gate A.
    - Se il pezzo risulta buono (QI=1) si apre il gate C (GC=1) ed il pezzo può passare alla fresa 
incrementando il contatore NC.
- Nella macchina il pezzo grezzo viene fresato per ottenere un pezzo lavorato; la macchina fornisce 
un valore binario EM che indica la riuscita o meno della lavorazione, ed il volume del pezzo 
lavorato (VOUT) espresso in 6 bit.
    - se la macchina ha generato un errore (EM=0) l’impianto deve spegnersi generando il 
codice di errore ERR=001.
    - Se la lavorazione è andata a buon fine (EM=1), il pezzo viene scaricato attraverso il gate 
D (GD=1) e passa al controllo qualità in uscita. La differenza tra il volume del pezzo grezzo
VIN e quello del pezzo lavorato VOUT è scarto che va a finire nel serbatoio della macchina.
Quando la quantità di scarto nella macchina supera il valore 200 l’impianto deve spegnersi 
generando il codice di errore ERR=010.
- Il prodotto lavorato entra nel modulo di ispezione in uscita che restituisce l’indicatore di qualità 
del pezzo lavorato (QO, 1=buono, 0=scarto).
    - Se il pezzo risulta buono (QO=1), il dispositivo apre il gate F (GF=1) per mandare il pezzo 
a magazzino, incrementando il contatore dei pezzi lavorati correttamente (NF).
    - Se il pezzo risulta scarto (QO=0), il dispositivo apre il gate E (GE=1) per mandare il pezzo 
in un deposito scarti lavorati ed aumenta il contatore relativo (NE).
In entrambi i casi al ciclo successivo la macchina riprende il ciclo aprendo il gate A.

Tutti i contatori dei pezzi (NA, NB, NC, NE e NF) sono espressi in 4 bit. Nel caso in cui il numero di pezzi nel 
deposito degli scarti in ingresso (NB) generi un overload l’impianto deve spegnersi con codice di errore 
ERR=101. Analogamente per quanto riguarda il deposito degli scarti in uscita (NE), generando il codice 
ERR=110.
Ad ogni ciclo di clock il dispositivo deve restituire una sequenza di 30 bit indicativi delle seguenti variabili 
(nell’ordine di seguito indicato!!!):
O/I ERR GA GB GC GD GE GF NA NB NC NE NF
NB: ad ogni ciclo di clock può essere aperto al massimo un gate!
Si richiede inoltre che il circuito sia mappato sulla libreria tecnologica synch.genlyb e che venga 
ottimizzato per area.
Lanciando il comando SIS source test_in.txt, l'output deve coincidere con il file test_out.txt.
