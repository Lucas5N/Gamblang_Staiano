# GAMBLANG
GAMBLANG è un linguaggio di programmazione probabilistico che introduce il concetto di casualità e gestione del rischio sia nella memorizzazione delle variabili sia nel controllo del flusso. 
Il sorgente viene analizzato tramite un parser LALR, validato semanticamente e infine transpilato in codice C standard, che viene compilato ed eseguito automaticamente tramite `gcc`.

---

## Sistema dei Tipi e Sintassi

Il linguaggio si basa su tre costrutti di dichiarazione per le variabili e su un blocco condizionale specifico per la gestione del caso:

### 1. Variabili Deterministiche (`steady`)

Rappresentano le variabili tradizionali stabili. Si dichiarano ed assegnano usando l'operatore `=`.

```gamblang
steady x = 10
x = 20
```

### 2. Variabili con Memoria (`gamble`)

Variabili speciali che tengono traccia della cronologia dei valori assegnati. La dichiarazione e i successivi inserimenti avvengono tramite l'operatore `<-`. Quando si richiama la variabile all'interno di un'espressione, questa restituisce uno dei valori salvati nella cronologia, estratto in modo casuale.

```gamblang
gamble g <- 5
g <- 15
g <- 25
print g  # Restituirà casualmente 5, 15 o 25
```

### 3. Lancio della Moneta (`coin`)

Variabili booleane alimentate da una probabilità (compresa tra `0.0` e `1.0`). Al momento della dichiarazione, viene calcolato un lancio casuale: se la probabilità ha successo il valore sarà `1`, altrimenti `0`.

```gamblang
coin c = 0.75  # 75% di probabilità di essere 1 (true)
```

### 4. Flusso Condizionale Probabilistico (`risky`)

Un costrutto condizionale regolato dal caso. Il blocco interno viene eseguito solo se il controllo probabilistico ha successo, altrimenti viene eseguito il blocco alternativo `or do`.

```gamblang
risky(0.5) do
    print "Il blocco è stato eseguito"
or do
    print "Il blocco è fallito"
end
```

GAMBLANG supporta inoltre le strutture di controllo standard come `if/else`, i cicli `loop ... do ... end`, l'input da tastiera tramite `ask` (solo per variabili `steady`) e le operazioni aritmetiche/logiche di base.

---

## Architettura del Compilatore

La pipeline di compilazione è suddivisa nei seguenti moduli:

* **`gamblang`**: Script d'ingresso Bash che centralizza l'esecuzione del compilatore.
* **`main.py`**: Punto di ingresso che coordina il parsing, l'analisi semantica, scrive il sorgente temporaneo `output.c` e invoca `gcc` per la build finale.
* **`gamblang.lark`**: Grammatica formale del linguaggio definita con la sintassi Lark.
* **`semantics.py`**: Analizzatore semantico. Costruisce la Symbol Table e verifica la coerenza dei tipi (impedendo ad esempio l'uso di `=` su variabili `gamble` o l'uso di `ask` su variabili non `steady`).
* **`codegen.py`**: Generatore di codice C. Mappa i costrutti probabilistici in strutture C native (come struct con array di storici e macro di clamping per i limiti di probabilità).

---

## Requisiti

* Python 3
* Libreria Lark (`pip install lark`)
* Compilatore `gcc` configurato nel PATH del sistema

---

## Installazione e Uso

1. Clona il repository:

```bash
git clone https://github.com/tuo-username/gamblang.git
cd gamblang
```

2. Configura i permessi di esecuzione per lo script di avvio:

```bash
chmod +x gamblang
```

3. Esegui il tuo codice sorgente (es. `programma.gamblang`):

```bash
./gamblang programma.gamblang
```

---

## Esempio Completo

```gamblang
# Dichiarazioni iniziali
steady soglia = 0.4
coin moneta = 0.65
gamble storico <- 10
storico <- 20
storico <- 30
if moneta == 1
    print "Lancio della moneta riuscito!"
    print storico
end
risky(soglia) do
    print "Rischio calcolato con successo"
or do
    print "Tentativo fallito"
end
```
