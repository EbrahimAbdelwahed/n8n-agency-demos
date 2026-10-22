# Demo 1 · Il lead che riceve risposta in 60 secondi

[<img src="../../docs/img/01-lead.jpg" width="640">](../../docs/video/01-lead-in-60-secondi.mp4)

▶ [Guarda il video (1 min 34 s)](../../docs/video/01-lead-in-60-secondi.mp4)

## Il problema

L'agenzia porta contatti con le campagne, ma l'agenzia immobiliare li richiama dopo uno o due giorni. Nel frattempo chi cercava casa ha già fissato una visita con qualcun altro, e il cliente pensa che le campagne non funzionino.

La demo è costruita su un'agenzia immobiliare, ma lo stesso flusso va bene per qualsiasi attività che raccoglie contatti dalle campagne: palestre, scuole, concessionarie, servizi per la casa.

## Cosa fa

1. Chi compila il modulo (Meta Lead Ads o sito) riceve **in pochi secondi un WhatsApp personalizzato**: risponde alla sua richiesta specifica e propone due orari di visita. A chi ha fretta propone la visita del giorno stesso.
2. L'AI **assegna temperatura e punteggio** al contatto (caldo, tiepido, freddo) e spiega il perché.
3. L'agente riceve **un avviso su Slack solo per i contatti caldi** (mutuo già approvato, trasferimento vicino, casa da vendere), con chi è, cosa cerca e perché chiamarlo subito.
4. Ogni contatto finisce **in una tabella CRM** con il messaggio inviato e i secondi alla risposta: numeri da mostrare al cliente nel report.

## Il flusso

```
Nuovo lead dal modulo (webhook)
  → Config cliente                          nome agenzia, firma, immobile, orari di visita, canali
  → Pulisci i dati del lead                 nome, telefono in formato internazionale, fonte
  → Qualifica il lead e scrivi la risposta (AI)
        └ Groq · gpt-oss-120b + Formato della risposta (temperatura, punteggio, motivo, urgente, messaggio)
  → Numero WhatsApp valido?
        ├ sì → Invia WhatsApp (con la firma) → Salva nel CRM
        └ no →                  Salva nel CRM
  → È un lead caldo? → Avvisa l'agente su Slack
  → Conferma al modulo
```

## Personalizzarlo per un nuovo cliente

Si cambia solo il nodo **Config cliente**:

| Campo | Esempio |
|---|---|
| `cliente`, `citta`, `firma` | Casa Ticino Immobiliare, Pavia, "Laura – Casa Ticino Immobiliare" |
| `immobile` | trilocale con terrazzo in centro a Pavia, 92 m², box e cantina, 289.000 € (l'AI non cita altri prezzi o caratteristiche) |
| `slot_liberi`, `slot_oggi` | in produzione si leggono da Google Calendar o dal gestionale dell'agenzia |
| `whatsapp_api`, `whatsapp_phone_number_id` | dati del numero WhatsApp Business |
| `slack_webhook` | canale degli agenti |

## In produzione

- **Ingresso**: il webhook accetta qualunque modulo. Per Meta Lead Ads si usa il nodo *Facebook Lead Ads Trigger* di n8n al posto del webhook.
- **WhatsApp**: `whatsapp_api` diventa `https://graph.facebook.com/v21.0` con una credenziale n8n al posto di `Bearer demo-token`. Il primo messaggio verso un contatto nuovo deve usare un **modello approvato da Meta** (template); il testo personalizzato dell'AI si inserisce come variabile del modello.
- **CRM**: la Data Table di n8n va bene per partire; in alternativa Google Sheets, HubSpot o il gestionale dell'agenzia (per esempio con le API di Immobiliare.it Insights, Getrix o Gestim, dove disponibili).
- **Privacy (GDPR)**: il modulo raccoglie il consenso al trattamento dei dati e all'essere ricontattati su WhatsApp. Al modello AI arrivano solo nome, richiesta e messaggio del contatto; il prompt vieta di chiedere dati su reddito, salute o famiglia. In produzione conviene un fornitore AI con server nell'UE e un accordo sul trattamento dei dati (DPA) firmato, da citare nell'informativa privacy del cliente.
- **Dati della demo**: nomi inventati, email su `example.com` e numeri con prefisso 300, che non è assegnato ai cellulari.
