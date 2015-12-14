# C3 Lösung

**Wichtig: Immer Team Namen an das aktuelle Team anpassen.**

## Repository klonen

```bash
git clone git@github.com:ci-sw2015/team-bowser.git

# oder bei anderen Teams entsprechend hinten den Namen anpassen, 
# aber ist auch in der Angabe so
git clone git@github.com:ci-sw2015/team-koopa-troopa.git
```

## Tests via Maven ausführen

```bash
cd team-bowser

# das schlägt fehl, aber dann sieht man alle Tasks/Goals
mvn

# richtig ist folgendes (dauert eine Weile, weil zum testen 
# auch die Abhängigkeiten runtergeladen werden)
mvn test
```

## Jenkins starten

```bash
# ~ ist das Verzeichnis des angemeldeten Benutzers (ci)
# wird aufgelöst nach /home/ci
cd ~/Desktop

java -jar jenkins.war

# Troubleshooting falls es Probleme gibt, z.B. process in use oder so
killall java

# falls Port belegt anderen Port auswählen, muss dann auch mit dem Browser stimmen
# also z.B. http://localhost:9000
java -jar jenkins.war -httpPort 9000

# browser kann man auch von Konsole aus starten
firefox http://localhost:9000
```

## Build starten

Auf `schedule a build` klicken und dann sieht man links unten wie er das ausführt. Oder man klickt direkt auf den Teamnamen, dann sieht man die ganze Build History links.

## Git Hook erstellen

Kann man über den Texteditor machen und dann einfach dort abspeichern oder von der Konsole aus:

```bash
# man muss sich hierfür im Verzeichnis des Repositories befinden 
echo '#!/bin/sh' > .git/hooks/pre-push

# auch hier wieder Pfad anpassen
# doppel >> hängt an, einmal > würde überschreiben
echo 'curl "http://localhost:8080/git/notifyCommit?url=git@github.com:ci-sw2015/team-bowser.git"' >> .git/hooks/pre-push

# ganz wichtig: ausführbar (eXecutable) machen
chmod +x .git/hooks/pre-push

# oder so (ist aber nicht optimal)
chmod 777 .git/hooks/pre-push
```

## Git Hook überprüfen

```bash
# Option 1)

# kleine Änderung durchführen
echo 'Los gehts!' >> README.md

# add, commit, push
git add README.md
git commit -m "Add motivational message"
git push

# Option 2)
echo 'Los gehts!' >> README.md && git commit -am "Add motivational message" && git push

# falls es beim Push Probleme gibt
git push --all
```

## Plugins installieren

Bei Jenkins auf `Configure` --> `Manage plugins` --> `Available`, dann beide auswählen und installieren. Das Häkchen setzen, so dass Jenkins neustartet. Den Neustart bekommt man dann aber nur auf der Konsole mit, deswegen einfach nach so 10-15 Sekunden F5 drücken.

Das Chuck Norris Plugin muss man in unserem Projekt bei Jenkins konfigurieren. Dazu auf `Configure` und dann ganz unten bei `Post Build Actions` Chuck Norris auswählen.
 
## Fragen

Die Antworten sind teils subjektiv und lassen Raum für Diskussion. Evtl. merken oder notieren und dann in C4 nochmal aufbringen.

1. Wie viel Code Coverage wird durch die Tests erziehlt?

> Steht auf der ersten Seite unseres Projekts in Jenkins

2. Was ist der Unterschied zwischen Branch und Line Coverage?
**Nachfragen was besser ist: auf jeden Fall Branch Coverage, alles andere kann lügen**

> Line Coverage prüft nur jedes Statement, aber würde z.B. bei einem if nicht prüfen ob dies einmal true und einmal false ist
> Branch Coverage prüft in dem Beispiel dann ob das if einmal war und einmal falsch ist
> Pfad Coverage würde alle möglichen Pfade abdecken, ist aber nicht möglich auf Grund von Rechenzeit

3. Welche Art von Problemen zeigt Checkstyle auf?

> Formatierung, fehlen von Kommentaren, Indentierung, generell Coding Styles

4. Sollte man davon alle beheben? Was macht Sinn?

> Ich meine ja, man sollte alle beheben, ABER NUR, wenn sie so vom Team definiert und akzeptiert wurden und nicht pauschal einfach den Standard nehmen; einmal, am besten zu Projektstart alles definieren und konfigurieren und dann aber versuchen auf 0 zu bleiben

5. Betrachten Sie die Meldungen von PMD. Wie unterscheiden sich diese von Checkstyle?

> PMD geht tiefer und versteht mehr vom Code, hier werden z.B. sinnlose Ausführungen die nie erreicht werden gemeldet

> Offiziell: PMD is a source code analyzer. It finds common programming flaws like unused variables, empty catch blocks, unnecessary object creation, and so forth. It supports Java, JavaScript, PLSQL, Apache Velocity, XML, XSL. 
Additionally it includes CPD, the copy-paste-detector.

## PMD Probleme

Dazu einfach auf PMD in der Übersicht unseres Projekts in Jenkins klicken und dort dann an den jeweiligen Stellen über den gehighlighteten Code schauen. Beim Mouse Over stehen die nötigen Änderungen. Diese dann adden, commiten und pushen, so dass am Schluss die PMD Meldungen auf 0 sind.