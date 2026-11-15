# clearance-skill

[English](README.md) | **Deutsch**

Agenten-Skill und Datenschutzhinweis für **Clearance**, eine Forge-App für Jira, die KI-Agenten eine Frage beantwortet: Wer darf jetzt an einem Vorgang arbeiten oder eine knappe Ressource nutzen? Die Zuweisung sagt, wer arbeiten *soll*; Clearance sagt, wer arbeiten *darf*.

Dieses Repository enthält nur den öffentlichen Skill-Text und den Datenschutzhinweis, nicht die App selbst.

> **Status: early, Vorschau.** Laut Datenschutzhinweis ist die App eine Vorschau für Tests mit synthetischen Aufgaben und Ressourcen. Der Skill beschreibt, wie ein Agent die App über MCP-Werkzeuge nutzt.

## Inhalt

| Datei | Zweck |
|---|---|
| [`skill.md`](skill.md) | Der Skill: Verbindung, Werkzeugliste, Arbeitsablauf für Agenten und Regeln |
| [`PRIVACY.md`](PRIVACY.md) | Welche Daten die App verarbeitet, wo sie gehostet wird, Aufbewahrung und Kontakt zum Anbieter |
| [`LICENSE`](LICENSE) | MIT-Lizenz |

## Die Idee in Kürze

- Ein **Claim ist ein Lease**: immer nur ein Halter, er läuft ab, wenn er nicht verlängert wird.
- Jede Vergabe trägt eine **Fence-Nummer**, die nur steigt. Ist ein Lease abgelaufen und hat jemand anderes übernommen, funktioniert die alte `claim_id` nicht mehr, sodass ein veralteter Agent neuere Arbeit nicht überschreiben kann.
- Geteilte Ressourcen (zum Beispiel eine Staging-Umgebung) haben eine Kapazität; sind alle Plätze belegt, wird der Agent eingereiht.

## Im Skill beschriebene Werkzeuge

| Werkzeug | Zweck |
|---|---|
| `next-task` | Den einen nächsten Vorgang holen, den der Agent nehmen darf (Status To Do, Label `agent-ok`, nicht `needs-human`, nicht vergeben; nach Priorität, dann Rang) |
| `claim-task` | Einen Vorgang für begrenzte Zeit beanspruchen (`ttlMinutes` 1 bis 240, Standard 30) |
| `renew-claim` | Einen Claim vor Ablauf verlängern (die Fence bleibt gleich) |
| `report-work` | Das Ergebnis am Vorgang kommentieren und den Claim freigeben |
| `release-claim` | Einen Claim vorzeitig zurückgeben (Vorgänge oder Ressourcen) |
| `claim-resource` | Einen Platz einer geteilten Ressource nehmen oder eingereiht werden |
| `clearance-status` | Halter, Ablauf, Fence, Warteschlange und Prüfspur anzeigen |
| `define-resource` | Eine Ressource mit Kapazität anlegen |

Ablauf laut Skill: nur den Vorgang bearbeiten, den `next-task` liefert, ihn beanspruchen, vor Ablauf verlängern, geteilte Ressourcen vor der Nutzung beanspruchen, mit `report-work` abschließen und sofort aufhören zu schreiben, wenn ein Aufruf meldet, dass der Claim nicht mehr gültig ist. Vorgänge mit dem Label `needs-human` sind nicht für Agenten. Ein Jira-Workflow-Validator („Clearance required") kann zusätzlich das Verschieben nach In Progress sperren, solange der Verschiebende den Lease nicht hält.

## Nutzung

Die App wird über den Atlassian-MCP-Endpunkt für Forge-Apps erreicht (OAuth 2.1 mit Zustimmung der Nutzerin oder des Nutzers); zuvor muss ein Site-Admin den MCP-Server der App freigeben. Die genauen Verbindungsdaten stehen in [`skill.md`](skill.md). Lade `skill.md` im Skill-Mechanismus deines Agenten; die Datei trägt Front Matter mit `name` und `description`.

## Datenschutz

Siehe [`PRIVACY.md`](PRIVACY.md). Kurz: Forge SQL speichert pro Installation Lease-Einträge, Prüfereignisse, Ressourcendefinitionen und Warteschlangeneinträge; die Produktiv-App deklariert keinen externen Netzwerkzugriff; Lease-Zeilen und Prüfereignisse bleiben nach Ablauf erhalten. Keine personenbezogenen oder sensiblen Angaben in Agenten-Labels, Beschreibungen oder Notizen eintragen.

## Bezug zu ellmos

`clearance-skill` liegt in der Organisation ellmos-ai neben den Koordinationswerkzeugen des Ökosystems, etwa [lock-master](https://github.com/ellmos-ai/lock-master) (Dateisperren für gemischte Agententeams) und [task-master](https://github.com/ellmos-ai/task-master). Clearance nutzt einen vergleichbaren Lease-und-Fence-Ansatz für Jira-Arbeit.

## Lizenz

MIT, siehe [`LICENSE`](LICENSE).
