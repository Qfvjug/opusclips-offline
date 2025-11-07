# Quick Start Guide - OpusClips Offline mit Copilot

## 🚀 Schnelleinstieg

### Schritt 1: Dateien Download
Du hast zwei Dateien erhalten:
1. **OpusClips-Offline-Copilot-Prompt.md** - Vollständige Spezifikation (nutze diese als Referenz)
2. **Copilot-Prompts-Details.md** - Detaillierte Copilot Prompts für jede Phase

### Schritt 2: Vorbereitung
Installiere zuerst:
```bash
# Python 3.9+
python --version

# Erstelle Projekt-Ordner
mkdir opusclips-offline
cd opusclips-offline

# Git initialisieren (optional)
git init
```

### Schritt 3: Mit Copilot starten

**Option A: GitHub Copilot Chat (VS Code)**
1. Öffne VS Code und installiere die GitHub Copilot Extension
2. Öffne einen neuen Chat (Ctrl+Shift+I / Cmd+Shift+I)
3. Starte mit dem ersten Prompt aus **Copilot-Prompts-Details.md**:

```
Erstelle die komplette Projekt-Struktur für "opusclips-offline" mit allen Ordnern und Python-Dateien gemäß der Spezifikation...
```

**Option B: Claude / ChatGPT / Andere LLMs**
- Kopiere zuerst die gesamte **OpusClips-Offline-Copilot-Prompt.md**
- Paste sie als System-Context
- Dann verwende die Prompts aus **Copilot-Prompts-Details.md**

### Schritt 4: Phase für Phase entwickeln

Die Entwicklung ist in 6 Phasen unterteilt:

| Phase | Fokus | Dauer |
|-------|-------|-------|
| 1 | Setup, Struktur, Basis-UI | 1-2 Tage |
| 2 | Scene Detection, Timeline | 1-2 Tage |
| 3 | Clip-Generierung, Ranking | 1-2 Tage |
| 4 | Ollama + Whisper Integration | 1-2 Tage |
| 5 | Editor + Export | 2-3 Tage |
| 6 | Optimierung, Polishing | 1-2 Tage |

**Gesamtdauer: ~1-2 Wochen mit Copilot-Unterstützung**

### Schritt 5: Bei jedem Phase-Wechsel

Beispiel-Prompt-Template:
```
Ich habe Phase [N-1] abgeschlossen. Der Code liegt in [./path/to/files/].
Implementiere nun Phase [N] wie in der Spezifikation beschrieben.
Nutze die bestehende Struktur und integriere mit vorherigen Phasen.
[Kopiere den spezifischen Prompt aus Copilot-Prompts-Details.md]
```

---

## 🎯 Wichtige Checkpoints

### Nach Phase 1 solltest du haben:
- ✅ Komplette Ordner-Struktur
- ✅ requirements.txt mit allen Dependencies
- ✅ config.py mit Konfigurationssystem
- ✅ main.py mit PyQt6 Hauptfenster
- ✅ 6 leere Tab-Platzhalter

**Test:** `python main.py` sollte das Fenster öffnen

### Nach Phase 2 solltest du haben:
- ✅ PySceneDetect Integration
- ✅ Scene Detection funktionsfähig
- ✅ Thumbnail-Generierung
- ✅ Detection Tab mit Timeline Widget
- ✅ Video-Vorschau funktioniert

**Test:** Upload eines Videos → Scene Detection läuft → Szenen werden angezeigt

### Nach Phase 3 solltest du haben:
- ✅ Automatische Clip-Generierung
- ✅ Clip-Ranking & Scoring
- ✅ Clip-Liste Tab mit Filterung
- ✅ Metadata speichern/laden

**Test:** Nach Scene Detection sollten Clips mit Scores angezeigt werden

### Nach Phase 4 solltest du haben:
- ✅ Ollama Server Control
- ✅ Whisper Transkription
- ✅ AI Analysis für Clips
- ✅ Settings Tab mit AI-Controls

**Test:** Ollama starten → Video analysieren → Verbesserte Scores

### Nach Phase 5 solltest du haben:
- ✅ Video-Editor mit allen Funktionen
- ✅ Video Player Widget
- ✅ Multi-Format Export
- ✅ Social Media Presets

**Test:** Clip editieren → mit verschiedenen Formaten exportieren

### Nach Phase 6 solltest du haben:
- ✅ Task Queue für Batch-Verarbeitung
- ✅ Caching-System
- ✅ Performance Monitoring
- ✅ GPU-Support

**Test:** Mehrere Videos gleichzeitig verarbeiten

---

## 📚 Spezifikations-Referenz

Wenn du während der Entwicklung Fragen hast:

- **Features übersicht:** Siehe "Kernfeatures" in OpusClips-Offline-Copilot-Prompt.md
- **Tech Stack:** Siehe "Technologie-Stack" 
- **Architektur:** Siehe "Dateistruktur" und "Code-Anforderungen"
- **Konfiguration:** Siehe "Konfigurationsbeispiel (config.json)"

---

## 🔧 Häufige Copilot-Anfragen

### "Der Code funktioniert nicht"
→ Gib Copilot den Error-Output und den Dateipfad
→ Frage: "Warum könnte dieser Error auftreten und wie behebe ich ihn?"

### "Ich brauche Erklärung für X"
→ Frage: "Erkläre die Architektur von [Komponente] und warum wir diese Struktur nutzen"

### "Feature funktioniert, aber langsam"
→ Frage: "Optimiere [Funktion] für bessere Performance. Nutze Threading/Caching wo möglich"

### "Ich will Feature X hinzufügen"
→ Frage: "Wie würde ich [neue Feature] in die bestehende Architektur integrieren?"

### "Tests schreiben"
→ Frage: "Schreibe Unit Tests für [Modul] mit pytest. Teste auch Edge Cases"

---

## 🐛 Debugging-Tipps

1. **Aktiviere Logging:**
   - Alle Funktionen nutzen `logging` statt `print()`
   - In config.py: `LOG_LEVEL = "DEBUG"` für verbose Output

2. **Nutze den Performance Monitor:**
   - Settings Tab zeigt CPU, Memory, GPU Usage
   - Findet Performance-Bottlenecks schnell

3. **Video Test-Dateien:**
   - Nutze kurze Test-Videos (5-30 Sekunden) für schnelle Tests
   - "Big Buck Bunny" ist ein gutes Referenz-Video

4. **Ollama Debugging:**
   - `ollama list` - Zeige installierten Modelle
   - `ollama serve` - Starte Server manuell um Logs zu sehen

5. **FFmpeg Debugging:**
   - Copilot kann FFmpeg-Commands testen
   - Frage: "Gib mir einen FFmpeg-Testbefehl für [Operation]"

---

## 📦 Deployment & Distribution

Nach Fertigstellung kannst du die App mit PyInstaller packen:

```bash
# Installiere PyInstaller
pip install pyinstaller

# Erstelle standalone Executable
pyinstaller --windowed --onefile --name OpusClips main.py
```

Output findest du in `./dist/OpusClips`

---

## 🤝 Community & Help

Falls du Probleme hast:

1. **Copilot fragen:** Es kennt die Spezifikation (du gibst sie ihm ja)
2. **GitHub Issues:** Erstelle Issue wenn es Bug ist
3. **Reddit/Discord:** Frage in r/LocalLLaMA oder ähnlichen Communities

---

## ✨ Pro-Tipps

1. **Nutze Copilot für Boilerplate**
   - Frage: "Schreibe Boilerplate für ein QWidget mit [Feature]"

2. **Code-Generierung beschleunigen**
   - Gib Copilot die bestehende Struktur (paste 1-2 ähnliche Dateien)
   - Es wird dann ähnliche Dateien schneller und konsistenter erstellen

3. **Architektur-Diskussionen**
   - Bevor du mit Phase anfängst: "Sollen wir für [Komponente] Pattern X oder Y nutzen?"
   - Copilot gibt gute Argumente für beide

4. **Refactoring**
   - Periodisch: "Refaktoriere [Modul] für bessere Code-Qualität"
   - Copilot macht gute Verbesserungen

---

## 📝 Progress Tracking

Kopiere diese Checkliste und hacke sie ab:

- [ ] Phase 1 abgeschlossen
- [ ] Phase 2 abgeschlossen
- [ ] Phase 3 abgeschlossen
- [ ] Phase 4 abgeschlossen
- [ ] Phase 5 abgeschlossen
- [ ] Phase 6 abgeschlossen
- [ ] Unit Tests geschrieben
- [ ] Mit Test-Videos getestet
- [ ] Dokumentation aktualisiert
- [ ] Executable gepackt
- [ ] Mit Freunden getestet
- [ ] Alles funktioniert! 🎉

---

Viel Erfolg beim Entwickeln! 🚀
