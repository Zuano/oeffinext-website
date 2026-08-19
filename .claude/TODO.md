# TODO – ÖffiNext Website & Domains

## Erledigt

### Domain öffinext.com leitet auf oeffinext.app weiter (2026-08-19)
- Beide Domains bei Namecheap gekauft und bezahlt (`oeffinext.app` und `öffinext.com`
  = Punycode `xn--ffinext-80a.com`, zusammen ca. €19,87, Domain Privacy gratis,
  Auto-Renewal aktiv).
- `oeffinext.app`: DNS zeigt auf GitHub Pages (A-Records 185.199.108–111.153,
  `www` → `zuano.github.io`), Seite liefert HTTP 200. Läuft.
- `öffinext.com`: In Namecheap → Advanced DNS zwei **URL Redirect Records** angelegt,
  beide **Permanent (301)** auf `https://oeffinext.app`:
  - Host `@`  → https://oeffinext.app (ersetzt den alten Parking-Redirect)
  - Host `www` → https://oeffinext.app (ersetzt den CNAME auf parkingpage.namecheap.com)
  - Geprüft: beide antworten mit `301 Moved Permanently` → `https://oeffinext.app`.


### Weiterleitung auf Cloudflare umgestellt – damit auch HTTPS geht (2026-08-19)
Die Namecheap-Weiterleitung konnte kein HTTPS (kein Zertifikat für die Quelldomain,
Port 443 tot). Deshalb läuft `öffinext.com` jetzt über Cloudflare (Free-Plan):
- Zone `öffinext.com` im Cloudflare-Konto von Christian (Konto- und Zonen-ID stehen
  im Cloudflare-Dashboard unter Übersicht → API; hier bewusst nicht notiert, weil das
  Repository öffentlich ist).
- Zwei A-Records (`@` und `www`) auf die Platzhalter-IP `192.0.2.1`, **Proxy aktiv**.
  Die IP wird nie erreicht – Cloudflare fängt die Anfrage am Edge ab.
- **Redirect Rule** (Phase `http_request_dynamic_redirect`): Bedingung `true`,
  Ziel `concat("https://oeffinext.app", http.request.uri.path)`, **301**, Query bleibt erhalten.
- SSL-Modus `full`.
- Nameserver bei Namecheap auf `dilbert.ns.cloudflare.com` / `kinsley.ns.cloudflare.com`
  umgestellt; Zone ist bei Cloudflare **aktiv**, Universal-SSL-Zertifikat ausgestellt
  (deckt `öffinext.com` und `*.öffinext.com` ab).
- **Verifiziert am 2026-08-19:** `https://öffinext.com`, `https://www.öffinext.com` und
  `http://öffinext.com` antworten alle mit `301` → `https://oeffinext.app/`;
  Pfade bleiben erhalten (`/linznext/` → `https://oeffinext.app/linznext/`);
  Kette endet mit `200`; TLS ohne Zertifikatsfehler.
- Die alten Namecheap-URL-Redirects sind damit wirkungslos (BasicDNS wird nicht mehr gefragt).
- E-Mail-Weiterleitung war bei der Domain nie eingerichtet – kein Verlust durch den NS-Wechsel.

**Stolperfalle für die Zukunft:** Cloudflares Assistent „Domain verbinden" bleibt bei
Umlaut-Domains (IDN) ewig im Ladezustand hängen. Ursache: der Vorab-Check
`/registrar/domains/batch_check` antwortet mit HTTP 400 „Invalid domain name" – sowohl für
`öffinext.com` als auch für die Punycode-Form `xn--ffinext-80a.com`. Workaround: Zone direkt
per API anlegen (`POST /api/v4/zones` mit der Punycode-Form), danach funktioniert alles normal.

## Offen / zu klären

### Uncommittete Änderungen im Repo (nicht von der Domain-Session)
Stand 2026-08-19 liegen 4 geänderte, nicht committete Dateien im Arbeitsverzeichnis:
`linznext/index.html`, `linznext/en/index.html`, `linznext/nutzungsbedingungen.html`,
`linznext/terms.html`. Inhalt: Claim „sekundengenau" → „in Echtzeit" und Abo-Preis
1,99 € → 0,99 €. Diese Änderungen sind **noch nicht live** (Website-Stand ist vom 2026-07-20).
→ Gehören vermutlich zu einer anderen Session. Vor dem Committen klären, ob sie fertig sind.

## Änderungsprotokoll

- **2026-08-19** – Domains `oeffinext.app` und `öffinext.com` bei Namecheap gekauft.
  DNS-Prüfung: `.app` läuft live auf GitHub Pages, `.com` stand noch auf der Parkseite.
- **2026-08-19** – Für `öffinext.com` beide Parking-Einträge (`@` und `www`) durch
  301-URL-Redirects auf `https://oeffinext.app` ersetzt und per curl verifiziert.
  HTTPS-Einschränkung dokumentiert (siehe oben).
- **2026-08-19** – `öffinext.com` von Namecheap-Weiterleitung auf Cloudflare umgestellt
  (Zone + DNS + 301-Redirect-Rule angelegt, Nameserver gewechselt), damit auch
  `https://öffinext.com` mit gültigem Zertifikat weiterleitet. Cloudflare-IDN-Bug dokumentiert.
- **2026-08-19** – Cloudflare-Zone aktiviert (Free-Plan), SSL-Zertifikat ausgestellt,
  Weiterleitung inkl. HTTPS end-to-end getestet und bestätigt. Damit ist die
  HTTPS-Einschränkung von Namecheap behoben.
