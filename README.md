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

## Testinhalt 1: Grosser Profil-Kopf (/stats)

`assets/exd/items/profile_head_big.json` + `assets/exd/models/item/profile_head_big.json`
- Eigener Namespace `exd`, NICHT `assets/minecraft/items/player_head.json`
  ueberschrieben - sonst waeren ALLE Spielerkoepfe im Netzwerk (Crates,
  Quests, Kopf-Sammlungen) betroffen, nicht nur der im Profil-Menue.
- Basis 1:1 aus dem echten `assets/minecraft/models/item/template_skull.json`
  (extrahiert aus dem offiziellen 1.21.11-Client-Jar), nur `display.gui.scale`
  von `[1,1,1]` auf `[2.5,2.5,2.5]` erhoeht + `oversized_in_gui: true` in der
  Item-Definition, damit der Kopf ueber die Slot-Grenze hinaus rendern darf.
- Bleibt ein normaler `minecraft:special` / `minecraft:player_head`-Renderer
  -> zeigt weiterhin das echte, aktuelle Skin des Spielers (kein statisches
  Bild).
- Anwendung: Skript/SkBee muss auf dem GUI-ItemStack die `item_model`-Component
  auf `exd:profile_head_big` setzen (exakte SkBee-Syntax dafuer noch pruefen -
  Component-Setzen ist in SkBee 3.22.0 vorhanden, Name/Expression noch nicht
  verifiziert).
- Layout-Konsequenz: die Slots rings um den Kopf im GUI muessen leer/frei
  bleiben, sonst ueberlappt der vergroesserte Kopf sichtbar mit anderen Icons.
- **Noch nicht ingame getestet** - `translation`/`scale`-Werte sind ein erster
  Ansatz, muessen nach dem ersten Sichttest evtl. nachjustiert werden.

## Offene Punkte / naechste Schritte

- Ersten Sound noch nicht angelegt (nur Platzhalter-Beispiel in `sounds.json`)
- Grossen Profil-Kopf ingame testen und Scale/Translation feinjustieren
- SkBee-Syntax zum Setzen der `item_model`-Component im Profil-Skript pruefen
  und einbauen
- PackSquash-Optimierung (Groesse, v. a. wegen Musik) noch nicht in die
  Pipeline eingebaut
- Bedrock-Pack-Icon (`pack_icon.png`) fehlt noch im `bedrock-pack/`-Root
- Bedrock-Seite: grosser Kopf betrifft nur Java - fuer Bedrock-Spieler muesste
  das separat ueber das Bedrock-Pack geloest werden (dort gibt es keine
  Live-Skin-Spezialrenderer wie bei Java, siehe fruehere Erklaerung)
