# spur

Verschluesselte GPS-Spur eines Wohnmobils, eine Datei je Tag.

Die Inhalte sind AES-256-GCM verschluesselt. Der Schluessel wird aus einer Passphrase
abgeleitet (PBKDF2-SHA256) und liegt nicht hier. Ohne ihn sind die Dateien Rauschen.

Gelesen werden sie von <https://thorstengrote.github.io/trk/>.

Die Dateinamen tragen das Datum. Dass an einem Tag aufgezeichnet wurde, ist damit
sichtbar, wo, nicht.

## Format

`JJJJ-MM-TT.bin`

```
Byte  0.. 11   Zufalls-IV (12 Byte, AES-GCM)
Byte 12..       Geheimtext, am Ende 16 Byte Authentisierungsmarke
```

Klartext ist CSV:

```
zeit;art;lat;lon;hoehe;tempo;genauigkeit
2026-09-30T11:05:24+0200;start;;;;;
2026-09-30T11:05:54+0200;f;50.786329;7.029381;105.4;12.4;8
```

`art` sagt, warum die Zeile da steht: `start`, `f` (Fahrt, mehr als 15 m seit dem letzten
Punkt), `s` (Stand, Herzschlag nach fuenf Minuten), `kein-fix`, `ende` beim Tageswechsel.
Aus einer Luecke allein liesse sich der Zustand nicht ablesen: Stehen, Geraet aus und kein
Empfang saehen gleich aus.

## Schluesselableitung

```
PBKDF2-SHA256, 200000 Runden, Salt "womo-spur-1", 256 Bit
```
