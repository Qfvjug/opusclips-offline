# OpusClips Offline - Vollständiger GitHub Copilot Prompt

## 🎯 Projektübersicht

Du wirst eine **vollständig offline funktionierende Desktop-Anwendung** entwickeln, die wie OpusClips funktioniert. Die Anwendung teilt lange Videos automatisch in mehrere kürzere, Social-Media-optimierte Clips auf - **ohne Internet und ohne Cloud-Services**.

---

## 📋 Kernfeatures

### 1. **Video-Upload & Verwaltung**
- Lokale Video-Datei-Upload (MP4, MKV, WebM)
- Zeige Video-Informationen: Dauer, Auflösung, Bitrate, Framerate
- Video-Vorschau in der Benutzeroberfläche
- Speichere Video-Metadaten lokal

### 2. **Automatische Szenerkennung (Scene Detection)**
- Verwende **PySceneDetect** mit `AdaptiveDetector()`
- Erkenne automatisch Schnitte, Übergänge und Szenenwechsel
- **Konfigurierbare Erkennungs-Schwellenwerte:**
  - Scene Sensitivity: 0.1 - 0.9 (Standard: 0.3)
  - Content-basierte Erkennung
  - Adaptive Frameanalyse
- Zeige erkannte Szenen als Timeline mit Thumbnails
- Export erkannter Szenen als Liste

### 3. **Intelligente Clip-Generierung**
- **Automatische Clip-Erstellung:** 
  - Zielclip-Länge: 15s, 30s, 60s (konfigurierbar)
  - Mindest-/Maximal-Längen
  - Intelligentes Zusammenfassen von sehr kurzen Szenen
  - Überlappung vermeiden oder hinzufügen (konfigurierbar)

- **Intelligente Moment-Erkennung mit Ollama:**
  - Verwende lokales LLM (Ollama) zur Videoanalyse
  - Nutze Whisper (lokal) für Sprach-Transkription
  - Erkenne "Highlight-Momente" basierend auf:
    - Sprachintensität (laute, energische Abschnitte)
    - Schnelle Szenenwechsel
    - Musik/Sound-Spitzen
    - Benutzermarker (manuell gesetzt)

### 4. **Clip-Bewertung & Ranking**
- **Automatische Bewertung (1-10 Punkte):**
  - Geschwindigkeit der visuellen Änderungen (Schnitte)
  - Audio-Dynamik (Lautstärke-Spitzen)
  - Szenenkomplexität
  - Manuell anpassbar durch Slider
- **Filtern nach Bewertung:** Zeige nur Clips mit Score > X
- **Ranking-Algorithmus:** Sortiere Clips nach Viral-Potenzial

### 5. **Video-Bearbeitung & Export**
- **Clip-Bearbeitung:**
  - Trimmen (Start/End-Frames)
  - Crop-Funktion (Bildausschnitt)
  - Geschwindigkeit anpassen (0.5x - 2x)
  - Text-Overlay hinzufügen
  - Logo/Watermark-Integration
  - Übergänge zwischen Clips

- **Audio-Verarbeitung:**
  - Musik extrahieren / Audio isolieren
  - Musik zu Clips hinzufügen
  - Volume normalisieren
  - Audio-Fade In/Out

- **Export-Optionen:**
  - Mehrere Formate: MP4 (H.264/H.265), WebM, MKV
  - Zielauflösungen: 720p, 1080p, 4K
  - Optimierte Presets für Social Media:
    - YouTube Shorts (1080x1920, 9:16)
    - TikTok (1080x1920, 9:16)
    - Instagram Reels (1080x1920, 9:16)
    - Instagram Feed (1080x1080, 1:1)
    - Twitter/X (1280x720, 16:9)
  - Qualitäts-Presets: Low, Medium, High, Ultra

### 6. **Batch-Verarbeitung**
- Verarbeite mehrere Videos gleichzeitig
- Queue-System für große Projekte
- Fortschrittsanzeige für jedes Video
- Fehlerbehandlung und Logs

### 7. **Lokale KI-Integration**

#### **Ollama Integration:**
- Starten/Stoppen von Ollama-Server
- Model Management (Download, List, Delete)
- Nutzbare Modelle:
  - Text-Analyse: llama3.1, mistral, neural-chat
  - Vision (optional): llava (für Bildanalyse)
- API-Calls für:
  - Szenenbeschreibung generieren
  - Keywords/Tags extrahieren
  - Engagements-Score basierend auf Inhaltsanalyse

#### **Whisper Integration (lokale Spracherkennung):**
- Audio aus Video extrahieren
- Transkription ohne Cloud
- Timestamps für Sprachpassagen
- Genutzt für:
  - Moment-Erkennung (laute Momente)
  - Text-basierte Szenenerkennung
  - Automatische Captions generieren

### 8. **Benutzeroberfläche (UI/UX)**
- **Tab 1: Upload & Konfiguration**
  - Video-Datei auswählen
  - Erkennungs-Parameter einstellen
  - Start-Button
  
- **Tab 2: Scene Detection & Vorschau**
  - Timeline mit erkannten Szenen
  - Video-Vorschau
  - Thumbnail-Grid
  - Interaktive Einstellung (Schwellenwerte in Echtzeit anpassen)

- **Tab 3: Clip-Liste & Management**
  - Tabelle mit allen generierten Clips
  - Spalten: Start, Dauer, Score, Tags, Vorschau-Button
  - Filterbar nach Score, Länge, Tags
  - Sortierbar nach Score, Länge, Position

- **Tab 4: Clip-Editor**
  - Video-Player mit Schnitt-Tools
  - Bearbeitungs-Optionen (Trim, Crop, Speed, Text, Logo)
  - Live-Vorschau
  - Undo/Redo

- **Tab 5: Batch Export**
  - Wähle Clips zum Exportieren
  - Format/Qualität Einstellungen
  - Presets für Social Media
  - Export-Progress
  - Open Output Folder Button

- **Tab 6: Settings & Tools**
  - Ollama Server Status & Control
  - Whisper Modell-Download
  - Speicher-Pfade konfigurieren
  - Advanced Scene Detection Settings
  - Logs anschauen
  - System-Informationen (GPU, RAM)

### 9. **Erweiterte Features**

#### **KI-gestützte Moment-Erkennung:**
- Kombiniere PySceneDetect + Whisper + Ollama
- Erkenne "virale" Momente:
  - Plötzliche Reaktionen
  - Punchlines oder Witze
  - Musik-Beats
  - Dramatische Momente

#### **Auto-Tagging:**
- Tags automatisch basierend auf:
  - Visuelle Inhaltsanalyse (Ollama Vision)
  - Audio-Inhalt (Whisper Transkription)
  - Szenenerkennung
- Nutzerbare Tags für Filter/Organisation

#### **Clip-Vorlagen:**
- Speichere Bearbeitungs-Einstellungen als Vorlagen
- Quick-Apply auf neue Clips
- Standard-Vorlagen für verschiedene Inhaltstypen

#### **Performance-Optimierung:**
- Multi-Threading für Video-Verarbeitung
- GPU-Beschleunigung (wenn verfügbar)
- Caching von analysierten Frames
- Fortlaufende Verarbeitung im Hintergrund

---

## 🛠️ Technologie-Stack

### **Backend:**
- **Python 3.9+**
- **PySceneDetect** für Scene Detection
- **FFmpeg/PyAV** für Video-Verarbeitung
- **Ollama** (lokal laufender LLM-Server) für KI-Features
- **Whisper** (OpenAI's offline speech-to-text)
- **NumPy, Pillow** für Bild-/Frame-Verarbeitung

### **Frontend:**
- **Tkinter** (Desktop) ODER **PyQt6** (professionellere UI) ODER **FastAPI + React/Vue** (Web-Interface)
  - *Empfehlung:* PyQt6 für professionelle Desktop-App mit modernem Design
  
### **Dateistruktur:**
```
opusclips-offline/
├── main.py                          # Entry Point
├── ui/
│   ├── main_window.py              # Haupt-Fenster
│   ├── tabs/
│   │   ├── upload_tab.py           # Upload & Config
│   │   ├── detection_tab.py        # Scene Detection
│   │   ├── clips_tab.py            # Clip-Liste
│   │   ├── editor_tab.py           # Clip-Editor
│   │   ├── export_tab.py           # Batch Export
│   │   └── settings_tab.py         # Einstellungen
│   └── widgets/
│       ├── video_player.py         # Video-Player Widget
│       ├── timeline_widget.py      # Scene Timeline
│       └── progress_widget.py      # Progress Bar
├── core/
│   ├── scene_detection.py          # PySceneDetect Wrapper
│   ├── video_processor.py          # FFmpeg Wrapper
│   ├── clip_generator.py           # Clip-Generierungs-Logik
│   ├── clip_ranker.py              # Bewertungs-Algorithmus
│   ├── ai_integration.py           # Ollama + Whisper Integration
│   ├── metadata_handler.py         # JSON Metadaten speichern
│   └── config.py                   # Konfigurationshandler
├── utils/
│   ├── ffmpeg_utils.py             # FFmpeg Commands
│   ├── ollama_client.py            # Ollama API Client
│   ├── whisper_handler.py          # Whisper Integration
│   ├── logger.py                   # Logging System
│   └── validators.py               # Input Validation
├── requirements.txt                 # Python Dependencies
├── README.md                        # Dokumentation
└── config.json                      # Standard-Config

```

---

## 🚀 Implementierungs-Schritte (für Copilot)

### **Phase 1: Grundgerüst**
1. Erstelle Projekt-Struktur
2. Implementiere Konfigurationssystem
3. Baue Basis-UI mit PyQt6
4. Verbinde FFmpeg für Basic Video-Info

### **Phase 2: Video-Analyse**
1. Integriere PySceneDetect
2. Implementiere Scene Detection
3. Frame-Extraction & Thumbnail-Generierung
4. Zeige Timeline in UI

### **Phase 3: Clip-Generierung**
1. Automatische Clip-Generierung basierend auf Szenen
2. Intelligente Längen-Anpassung
3. Clip-Ranking & Bewertung
4. Speichere Metadaten (JSON)

### **Phase 4: KI-Integration**
1. Ollama Server Control (start/stop)
2. Whisper Spracherkennung
3. Intelligente Moment-Erkennung
4. Auto-Tagging

### **Phase 5: Editor & Export**
1. Video-Editor mit Trimmen/Crop
2. Text-Overlay & Logo-Funktionen
3. Multi-Format Export (MP4, WebM, MKV)
4. Social Media Presets

### **Phase 6: Batch-Verarbeitung & Optimierung**
1. Queue-System
2. Multi-Threading
3. GPU-Beschleunigung
4. Caching-System

---

## 💡 Spezifische Code-Anforderungen

### **Scene Detection Module:**
```
- Funktion: `detect_scenes(video_path, sensitivity=0.3, detector_type='adaptive')`
  Rückgabe: List[(start_time_seconds, end_time_seconds, confidence_score)]

- Funktion: `get_scene_thumbnails(video_path, scene_list, quality='medium')`
  Rückgabe: Dict[scene_id -> PIL.Image]

- Funktion: `adjust_sensitivity_realtime(video_path, new_sensitivity)`
  Echtzeitanpassung des Erkennungsalgorithmus
```

### **Clip Generator Module:**
```
- Klasse: `ClipGenerator`
  - Methode: `generate_clips_from_scenes(scenes, target_duration=30, min_duration=10, max_duration=60)`
  - Methode: `auto_extend_short_clips(clip_list)`
  - Methode: `merge_overlapping_clips(clip_list)`
  
- Klasse: `ClipRanker`
  - Methode: `score_clip(clip, frame_data)`  # Score 1-10
  - Methode: `extract_features(clip)` # [speed_score, sound_score, complexity]
  - Methode: `rank_by_virality(clip_list)` # Sortierte Liste
```

### **AI Integration Module:**
```
- Klasse: `OllamaIntegrator`
  - Methode: `start_server()` / `stop_server()`
  - Methode: `analyze_scene_content(frame_list)` # Beschreibung generieren
  - Methode: `extract_keywords(scene_description)` # Tags generieren
  - Methode: `rate_virality(scene_analysis)` # 1-10 Score

- Klasse: `WhisperIntegrator`
  - Methode: `transcribe_audio(video_path)` # [(time, text)]
  - Methode: `detect_loud_moments(audio, threshold_db)` # [(start, end)]
  - Methode: `generate_captions(transcription, subtitle_format='srt')`
```

### **Video Editor Module:**
```
- Klasse: `VideoEditor`
  - Methode: `trim_clip(input_path, start_frame, end_frame, output_path)`
  - Methode: `crop_video(input_path, x, y, width, height, output_path)`
  - Methode: `change_speed(input_path, speed_factor, output_path)` # 0.5 - 2.0
  - Methode: `add_text_overlay(input_path, text, position, duration, output_path)`
  - Methode: `add_logo(input_path, logo_path, position, size, output_path)`
  - Methode: `add_transition(clip1, clip2, transition_type='fade', duration=0.5)`
```

### **Export Module:**
```
- Klasse: `VideoExporter`
  - Methode: `export_batch(clips, format='mp4', quality='high', preset='youtube_shorts')`
  - Methode: `get_social_media_presets()` # Dict mit Presets
  - Methode: `normalize_audio(clip_list)` # Level-Normalisierung
  - Methode: `verify_export_quality(exported_video_path)`
```

---

## 📊 Bewertungs-Algorithmus (Beispiel)

```python
def calculate_clip_score(clip_data):
    """
    Berechne Viral-Score für einen Clip (1-10)
    
    Faktoren:
    - scene_cut_speed: Wie schnell wechseln Szenen (0-3 Punkte)
    - audio_dynamics: Lautstärke-Spitzen (0-3 Punkte)
    - scene_complexity: Visuelle Komplexität (0-2 Punkte)
    - manual_boost: Nutzer-Bewertung (-1 bis +1 Punkte)
    """
    
    base_score = (
        scene_cut_speed * 3 +
        audio_dynamics * 3 +
        scene_complexity * 2
    ) / 8  # Normalisiert auf 0-1
    
    final_score = (base_score * 10) + manual_boost
    return max(1, min(10, final_score))  # Clamp 1-10
```

---

## 🔧 Ollama & Whisper Setup (für Anwender)

### **Ollama Installation:**
```bash
# macOS
brew install ollama
ollama run llama3.1

# Linux
curl -fsSL https://ollama.ai/install.sh | sh
ollama run llama3.1

# Windows
# Download von: https://ollama.ai/download
```

### **Whisper Installation:**
```bash
pip install openai-whisper
whisper --help
```

---

## ⚙️ Konfigurationsbeispiel (config.json)

```json
{
  "video": {
    "supported_formats": ["mp4", "mkv", "webm", "mov"],
    "max_file_size_gb": 50,
    "temp_folder": "./temp"
  },
  "scene_detection": {
    "default_sensitivity": 0.3,
    "adaptive_detector": true,
    "save_keyframes": true
  },
  "clip_generation": {
    "default_target_duration": 30,
    "min_duration": 10,
    "max_duration": 60,
    "auto_extend_short_clips": true
  },
  "ai_models": {
    "ollama_enabled": true,
    "ollama_host": "http://localhost:11434",
    "ollama_model": "llama3.1",
    "whisper_model": "base",
    "whisper_device": "auto"
  },
  "export": {
    "default_format": "mp4",
    "default_quality": "high",
    "default_preset": "youtube_shorts",
    "use_gpu": true
  },
  "ui": {
    "theme": "dark",
    "window_width": 1400,
    "window_height": 900,
    "font_size": 11
  }
}
```

---

## ✅ Quality Assurance Checklist

- [ ] Alle Videos mit unterschiedlichen Codecs funktionieren
- [ ] Scene Detection ist zuverlässig (teste verschiedene Schwellenwerte)
- [ ] Clips sind korrekt geschnitten (Keyframes korrekt)
- [ ] Ratings sind konsistent und nachvollziehbar
- [ ] Ollama läuft stabil im Hintergrund
- [ ] Whisper transkribiert präzise
- [ ] Exporte sind in allen Social-Media-Formaten korrekt
- [ ] Performance ist akzeptabel (GPU-Nutzung)
- [ ] UI ist responsive, auch bei großen Videos
- [ ] Error-Handling ist umfassend
- [ ] Logs sind hilfreich und detailliert

---

## 🎓 Zusätzliche Informationen für Copilot

- Verwende **Type Hints** überall (Python 3.9+)
- Schreibe **Docstrings** für alle Funktionen
- Verwende **Logging** statt Print-Statements
- Implementiere **Error Handling** mit aussagekräftigen Messages
- Erstelle **Unit Tests** für kritische Funktionen
- Nutze **Async/Await** für lange Operationen wo sinnvoll
- Optimiere für **CPU und GPU** Performance
- Dokumentiere **alle Konfigurationsoptionen**
- Achte auf **Memory-Leaks** bei großen Videodateien
- Teste mit **Videos verschiedener Längen** (5min bis 2h+)

---

## 🚀 Start-Befehl für Copilot Chat

**Nutze diesen Prompt am Anfang:**

> "Erstelle ein vollständiges Python-Projekt basierend auf dem OpusClips-Offline-Spezifikation-Dokument. Starte mit Phase 1 (Grundgerüst + Projekt-Struktur). Verwende PyQt6 für die UI, PySceneDetect für Scene Detection. Erstelle alle Dateien, die für die erste Phase notwendig sind, mit vollständigem, produktionsfertigem Code. Schreibe Docstrings, Type Hints und Error Handling überall. Erkläre deine Architektur-Entscheidungen."

Dann für jede weitere Phase:
> "Implementiere Phase [N] des OpusClips-Offline-Projekts. Verwende die bestehende Struktur und integriere mit vorherigen Phasen. Schreibe produktionsfertigen Code mit Type Hints und Docstrings."

---

**Ende des Prompts - Viel Erfolg mit der Entwicklung! 🎬🚀**
