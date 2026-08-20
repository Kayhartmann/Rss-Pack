# EXD Network Resource Pack

Ein gemeinsames Resource Pack fuer alle 4 Server (Lobby, Gameserver, HappySMP,
Bauserver) - Java- und Bedrock-Variante im selben Repo, getrennt gebaut.

## Struktur

- `java-pack/` - Java-Edition-Pack (`pack_format 75`, passend zu Paper 1.21.11
  auf allen 4 Servern). GUI-Anpassungen ueber Custom-Font-Trick
  (`assets/minecraft/font/`), eigene Sounds ueber `sounds.json` +
  `assets/minecraft/sounds/`.
- `bedrock-pack/` - Bedrock-Gegenstueck fuer Geyser/Floodgate-Spieler.
  GUI-Texturen direkt unter `textures/ui/` (reine PNG-Ersetzung), Sounds ueber
  `sounds/sound_definitions.json`. Wird NICHT automatisch aus dem Java-Pack
  erzeugt - Texturen muessen separat gepflegt oder mit
  [GeyserMC/PackConverter](https://github.com/GeyserMC/PackConverter) aus dem
  Java-Pack konvertiert werden.

## Build & Release

`.github/workflows/build-release.yml` baut bei jedem Push auf `main` (der
`java-pack/` oder `bedrock-pack/` aendert) automatisch:

- `java-resourcepack.zip` + `java-resourcepack.zip.sha1` (SHA1 klein
  geschrieben, wie von `server.properties` erwartet)
- `bedrock-resourcepack.mcpack`

und haengt beides an den rollierenden Release-Tag `latest`. Die direkten
Download-Links (fuer `server.properties` bzw. Geyser) stehen danach in den
Release-Notes.

## Einbindung - EIN Pack fuer alle 4 Server

Da nur ein gemeinsames Pack gewuenscht ist, in **allen vier**
`server.properties` (Lobby, Gameserver, HappySMP, Bauserver) identisch
eintragen:

```properties
resource-pack=https://github.com/Kayhartmann/Rss-Pack/releases/download/latest/java-resourcepack.zip
resource-pack-sha1=<Hash aus java-resourcepack.zip.sha1 / Release-Notes>
```

Bedrock-Seite: `bedrock-resourcepack.mcpack` in
`plugins/Geyser-Velocity/packs/` auf dem PROXY ablegen und Geyser neu laden -
gilt dort automatisch netzwerkweit fuer alle Bedrock-Spieler, unabhaengig
davon, auf welchem Backend-Server sie gerade sind.

## Offene Punkte / naechste Schritte

- Erster Testinhalt (ein GUI-Element + ein Sound) fehlt noch
- PackSquash-Optimierung (Groesse, v. a. wegen Musik) noch nicht in die
  Pipeline eingebaut
- Bedrock-Pack-Icon (`pack_icon.png`) fehlt noch im `bedrock-pack/`-Root
