# TODO

Idee di miglioramento non ancora implementate (raccolte da un piano
precedente per una riscrittura del server mai realizzata — restano valide
a prescindere, si applicano al `server.py` attuale).

- [ ] **Guardia anti-frontmatter**: bloccare/avvisare in `create_article` e
      `update_article` se `body_markdown` inizia con `---` (YAML
      frontmatter). Dev.to lo interpreta e sovrascrive silenziosamente
      titolo/tag passati nel JSON — bug reale già osservato altrove.
- [ ] **Validazione tag prima della chiamata**: Dev.to accetta max 4 tag,
      solo alfanumerici. Validare lato server evita un 422 dopo aver già
      scritto tutto il corpo dell'articolo.
- [ ] **Retry/backoff su 429 e 5xx**: le chiamate `httpx` attuali non
      ritentano mai. Aggiungere un retry con backoff esponenziale
      (rispettando l'header `Retry-After`) le renderebbe più robuste sotto
      rate limit.
- [ ] **Verifica raggiungibilità `cover_image`**: `create_article`/
      `update_article` accettano un URL esterno già ospitato (Dev.to non fa
      upload immagini) — un controllo HEAD prima di salvare evita di
      scoprire il link rotto solo a pubblicazione avvenuta.
- [ ] **Gestione errori più leggibile in `update_article`/`create_article`**:
      avvolgere le chiamate `httpx` in un `try/except` e restituire un
      messaggio d'errore leggibile (status code + corpo della risposta)
      invece di lasciar propagare l'eccezione grezza. Spunto preso da un
      fork (`pentium10/devto-mcp`) esaminato prima di aprire la PR upstream
      — l'unica cosa utile lì dentro, il resto (pre-fetch dell'articolo,
      `dotenv`, riformattazione output) non va ripreso: il pre-fetch in
      particolare è una regressione, rompe le bozze proprie (vedi fix già
      fatta in `fetch_own_article_by_id`).
