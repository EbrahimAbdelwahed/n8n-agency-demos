# Demo 2 · Il report mensile che si scrive da solo

[<img src="../../docs/img/02-report.jpg" width="640">](../../docs/video/02-report-mensile.mp4)

▶ [Guarda il video (1 min 18 s)](../../docs/video/02-report-mensile.mp4)

## Il problema

A inizio mese gli account passano ore a copiare numeri da Meta Ads e Google Analytics, impaginare grafici e scrivere un commento che il titolare capisca. Con venti clienti sono giornate di lavoro, ogni mese.

## Cosa fa

1. **Il primo del mese alle 8:00** legge le campagne Meta Ads e le visite al sito (GA4) del mese appena chiuso e di quello precedente.
2. Calcola i numeri che contano per il cliente: investimento, contatti ottenuti, costo per contatto, visite al sito, con la variazione sul mese prima e il dettaglio per campagna e per canale.
3. L'AI scrive **il commento del mese in italiano semplice**, senza gergo tecnico e usando solo i numeri che riceve, senza inventarne: sintesi, cosa ha funzionato, cosa migliorare, prossimi passi.
4. Impagina un **report HTML pronto da inviare**, con grafici e tabelle, e avvisa l'account su Slack con la sintesi e il link: lo rivede in cinque minuti e lo manda.

## Il flusso

```
Ogni 1° del mese alle 8:00   (oppure: Prova adesso)
  → Config cliente                  agenzia, cliente, account Meta, proprietà GA4, canale Slack
  → Calcola i periodi               mese scorso e mese precedente
  → Meta Ads · mese scorso / mese precedente
  → GA4 · mese scorso / mese precedente
  → Calcola i KPI
  → Scrivi il commento del mese (AI)
        └ Groq · gpt-oss-120b + Formato della risposta (titolo, sintesi, cosa ha funzionato, da migliorare, prossimi passi)
  → Impagina il report
  → Salva il report
  → Avvisa l'account su Slack
```

## Personalizzarlo per un nuovo cliente

Nel nodo **Config cliente** si cambiano `cliente`, `meta_ad_account`, `ga4_property` e `slack_webhook`. Il nome dell'agenzia (`agenzia`) compare nell'intestazione del report: è il punto in cui il prodotto diventa white label.

## In produzione

- **Meta Ads**: `meta_api` diventa `https://graph.facebook.com/v21.0`, con un token di sistema dell'agenzia (Business Manager) salvato come credenziale n8n.
- **GA4**: `ga4_api` diventa `https://analyticsdata.googleapis.com/v1beta`, con il nodo Google Analytics di n8n o un service account.
- **Google Ads**: si aggiunge un ramo uguale a quello Meta.
- **Consegna**: al posto di `Salva il report` si può generare un PDF e caricarlo su Google Drive, o mandarlo per e-mail dopo l'approvazione dell'account.
