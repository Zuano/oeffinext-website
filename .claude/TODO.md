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

### Uncommittete Änderungen unter `linznext/` — Rest-Texte, bewusst geparkt
Die geänderten, nicht committeten Dateien (`linznext/index.html`,
`linznext/nutzungsbedingungen.html`, `linznext/terms.html`) stammen aus der noch offenen
Session **„Berthold Android Navigation und Delay-Anzeige Bugs"** (2026-08-09) und gehören
zum **Release 2.2.0 (neues Abo-Modell)**.

Der **Preis-Teil daraus ist am 2026-08-19 live gegangen** (Commit 81090d7) — die Preise
0,99 / 6,99 / 34,99 € und der Badge „Spare 41%" stimmen mit beiden Stores überein
(verifiziert per App-Store-Connect-API und Play Console). `linznext/en/index.html` ist
dadurch vollständig abgedeckt und taucht nicht mehr als geändert auf.

**Noch geparkt (nicht committen, nicht pushen):**
- FAQ-Text „7 Tage uneingeschränkt gratis testen … Premium speichert deine
  Lieblings-Haltestellen" — beschreibt **2.2.0-Funktionen (Testphase + Favoriten)**, die
  noch nicht im Store sind (ausgeliefert wird 2.0.2). Erst mit dem 2.2.0-Release live.
- Claim „sekundengenau" → „in Echtzeit" (Meta-Tags, Hero, Feature-Karte „0:59" → „3 min").
- Rechtsseiten ohne konkrete Beträge + Google Play als zweite Zahlungsabwicklung
  (statt nur Apple) — inhaltlich sinnvoll, aber Teil desselben Pakets.

→ Sobald 2.2.0 in beiden Stores live ist: die drei Dateien committen und pushen.

## Änderungsprotokoll

- **2026-08-19** – **LinzNext-Preise auf der Website korrigiert (Commit 81090d7).** Startseite
  DE+EN und beide Rechtsseiten unter `linznext/` von 1,99 / 7,99 / 12,99 € (DE) bzw.
  1,99 / 8,99 / 14,99 € (EN) auf **0,99 / 6,99 / 34,99 €** gebracht, Badge „Spare 63%" →
  **„Spare 41%"** (12 × 0,99 = 11,88 gegen 6,99). Preise vorher gegengeprüft: App Store
  Connect per API (Territorium AUT) und Play Console für Österreich + Deutschland — beide
  Stores identisch. Bewusst **nur die Zahlen** geändert; die 2.2.0-Feature-Texte aus der
  Berthold-Session bleiben geparkt (siehe oben). Vorgehen dabei: fremde Arbeitsstände vorher
  gesichert, Dateien aus `git show HEAD:` neu erzeugt, committet, danach die fremden Stände
  zurückgeschrieben — so blieb fremde, nicht committete Arbeit unangetastet.

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
