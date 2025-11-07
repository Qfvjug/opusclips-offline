# GitHub Copilot Chat Prompts für OpusClips Offline

Nutze diese Prompts direkt in GitHub Copilot Chat oder anderen AI-Coding-Tools.

---

## Phase 1: Projekt-Setup & Grundgerüst

### Prompt 1.1: Projekt-Struktur erstellen

```
Erstelle die komplette Projekt-Struktur für "opusclips-offline" mit allen Ordnern und Python-Dateien gemäß der Spezifikation. Für jede Python-Datei erstelle auch eine leere `__init__.py`. Gib mir die genaue Ordnerstruktur aus und erkläre den Zweck jeder Komponente.
```

### Prompt 1.2: Requirements.txt und Setup

```
Schreibe die `requirements.txt` Datei mit allen notwendigen Dependencies für OpusClips Offline:
- PyQt6 für GUI
- scenedetect für Scene Detection
- opencv-python für Video-Verarbeitung
- Pillow für Bildverarbeitung
- requests für API-Calls
- numpy für Numerik
- ffmpeg-python für FFmpeg-Wrapper
- ollama für lokale LLM-Integration
- openai-whisper für Spracherkennung
- python-dotenv für Konfiguration

Gib auch die genauen Versionsangaben an.
```

### Prompt 1.3: Config-System

```
Schreibe eine `config.py` Datei mit einer `Config` Klasse, die:
1. `config.json` lädt und parsed
2. Default-Werte bereitstellt falls config.json nicht existiert
3. Umgebungsvariablen überschreiben können
4. Getter-Methoden für alle Config-Werte hat
5. Type Hints und Docstrings hat

Die Config soll folgende Bereiche unterstützen:
- video (supported_formats, max_file_size_gb, temp_folder)
- scene_detection (default_sensitivity, adaptive_detector)
- clip_generation (default_target_duration, min_duration, max_duration)
- ai_models (ollama_enabled, ollama_host, whisper_model)
- export (default_format, default_quality, use_gpu)
- ui (theme, window_dimensions, font_size)

Schreibe auch einen logger mit Python's logging Modul.
```

### Prompt 1.4: Basis-PyQt6 Fenster

```
Erstelle die Hauptanwendung in `ui/main_window.py` mit PyQt6:
1. Ein QMainWindow mit Dark-Theme
2. Ein QTabWidget mit 6 Tabs (Upload, Detection, Clips, Editor, Export, Settings)
3. Jeder Tab ist leer aber als Placeholder mit Label
4. Implementiere Menübar mit File (Open, Exit) und Help (About)
5. Status Bar am unten
6. Responsive Layout mit Splitter wo sinnvoll
7. Speichere Window-Größe in config
8. Schreibe vollständigen Code mit Type Hints und Docstrings

Nutze auch das Logger-System für Debug-Infos.
```

### Prompt 1.5: FFmpeg Wrapper

```
Erstelle `utils/ffmpeg_utils.py` mit Funktionen für:
1. `get_video_info(video_path: str) -> dict` - Dauer, Auflösung, FPS, Bitrate
2. `extract_frame(video_path: str, timestamp_sec: float) -> PIL.Image`
3. `cut_video(input_path: str, start_sec: float, end_sec: float, output_path: str) -> bool`
4. `get_total_frames(video_path: str) -> int`
5. `check_ffmpeg_installed() -> bool`

Nutze subprocess oder ffmpeg-python. Schreibe Error Handling und Type Hints.
```

---

## Phase 2: Video-Analyse & Scene Detection

### Prompt 2.1: Scene Detection Wrapper

```
Erstelle `core/scene_detection.py` mit einer `SceneDetector` Klasse:

1. Methode `detect_scenes(video_path: str, sensitivity: float = 0.3) -> List[Tuple[float, float, float]]`
   - Rückgabe: [(start_sec, end_sec, confidence), ...]
   - Nutze PySceneDetect mit AdaptiveDetector
   - Handle verschiedene Video-Formate

2. Methode `adjust_sensitivity(new_sensitivity: float) -> None`
   - Ändere Erkennungsempfindlichkeit für Echtzeit-Tests

3. Methode `get_scene_info() -> dict`
   - Gib aktuelle Szenen-Daten zurück

Schreibe auch Unit Tests für die Erkennung mit Test-Videos.
```

### Prompt 2.2: Thumbnail-Generierung

```
Erstelle in `core/scene_detection.py` die Methode:

`generate_thumbnails(video_path: str, scenes: List[Tuple[float, float, float]], quality: str = 'medium') -> Dict[int, PIL.Image]`

1. Für jede Szene extrahiere die mittlere Sekunde als Thumbnail
2. Qualität: 'low' (320x180), 'medium' (640x360), 'high' (1280x720)
3. Caching der Thumbnails im Temp-Folder
4. Gib Dictionary mit scene_id -> PIL.Image zurück
5. Nutze FFmpeg oder OpenCV

Optimiere für Performance (parallel processing wenn möglich).
```

### Prompt 2.3: Detection Tab UI

```
Implementiere den "Detection" Tab in `ui/tabs/detection_tab.py`:

1. Oben: Video-Name, Dauer, Auflösung, Anzahl Szenen
2. Mitte: Slider für Sensitivity (0.1 - 0.9)
   - Live-Update bei Änderungen
   - "Re-detect" Button
3. Timeline-Widget mit erkannten Szenen
   - Zeige Thumbnails
   - Hover = Tooltip mit Zeit
   - Click = Video-Position spulen
4. Unten: "Export Scenes as CSV" Button

Nutze Threading für Re-detection ohne Einfrieren der UI.
```

### Prompt 2.4: Timeline Widget

```
Erstelle `ui/widgets/timeline_widget.py` mit `TimelineWidget` Klasse:

1. Zeige horizontale Timeline mit Szenen-Blöcken
2. Jeder Block = 1 Szene mit Thumbnail
3. Hover-Effekt (helleres Thumbnail)
4. Click-Event zum Navigieren im Video-Player
5. Scrollbar wenn mehr Szenen als Platz
6. Färbe Blöcke basierend auf Länge/Score
7. Custom QWidget mit paintEvent für Rendering

Optional: Zeige auch Audio-Waveform als Hintergrund.
```

---

## Phase 3: Intelligente Clip-Generierung

### Prompt 3.1: Clip Generator

```
Erstelle `core/clip_generator.py` mit `ClipGenerator` Klasse:

1. Methode `generate_clips_from_scenes(scenes: List[Tuple[float, float, float]], 
                                       target_duration: float = 30,
                                       min_duration: float = 10,
                                       max_duration: float = 60) -> List[Dict]`
   - Gruppiere Szenen in Clips der Zieldauer
   - Clips sollten nicht länger als max_duration sein
   - Clips sollten nicht kürzer als min_duration sein (außer letzte Szene)
   - Rückgabe: List[{start, end, duration, scene_indices}]

2. Methode `merge_short_clips(clips: List[Dict], min_duration: float = 10) -> List[Dict]`
   - Fusioniere zu kurze Clips mit Nachbarn

3. Methode `add_padding(clips: List[Dict], padding_ms: int = 0) -> List[Dict]`
   - Kann Padding zwischen Clips hinzufügen oder entfernen

Schreibe auch Tests für verschiedene Szenen-Kombinationen.
```

### Prompt 3.2: Clip Ranker

```
Erstelle `core/clip_ranker.py` mit `ClipRanker` Klasse:

1. Methode `extract_features(video_path: str, clip: Dict) -> Dict`
   - Extrahiere: scene_change_speed (0-1), audio_dynamics (0-1), visual_complexity (0-1)
   - scene_change_speed: Wie viele Schnitte pro Sekunde
   - audio_dynamics: RMS-Lautstärke-Spitzen (nutze FFmpeg)
   - visual_complexity: Histogram-Unterschiede zwischen Frames

2. Methode `calculate_score(features: Dict, manual_boost: float = 0) -> float`
   - Score = (scene_changes * 3 + audio * 3 + complexity * 2) / 8
   - Manual-Boost kann -1 bis +1 sein
   - Clamp auf 1-10 Skala

3. Methode `rank_clips(video_path: str, clips: List[Dict]) -> List[Dict]`
   - Score alle Clips und sortiere sie

Implementiere caching der Features um Performance zu sparen.
```

### Prompt 3.3: Metadata Handler

```
Erstelle `core/metadata_handler.py` mit Funktionen:

1. `save_project(project_name: str, video_path: str, scenes: List, clips: List, config: Dict) -> str`
   - Speichere alles in JSON mit timestamp
   - Gib Pfad zur gespeicherten JSON zurück

2. `load_project(project_path: str) -> Dict`
   - Lade komplettes Projekt inkl. Szenen, Clips, etc.

3. `export_clips_csv(clips: List[Dict], output_path: str) -> None`
   - Exportiere Clips als CSV für externe Bearbeitung

4. `update_clip_metadata(project_path: str, clip_id: int, metadata: Dict) -> None`
   - Update einzelnen Clip nach Benutzer-Anpassung

Nutze JSON für Speicherung. Gib auch Versionsnummer rein für zukünftige Kompatibilität.
```

### Prompt 3.4: Clips Tab UI

```
Implementiere "Clips" Tab in `ui/tabs/clips_tab.py`:

1. Oben: Filter-Optionen
   - Score Min/Max Slider
   - Length Min/Max Slider
   - Suchbox für Tags
   - Sort-Dropdown (Score, Length, Position)

2. Mitte: QTableWidget mit Spalten
   - # (Index)
   - Start Time
   - Duration
   - Score (mit Farbcode: rot < 5, gelb 5-7, grün > 7)
   - Tags (editable)
   - Action-Buttons (Preview, Edit, Delete)

3. Preview: Kleines Video-Player Widget wenn Row geklickt

4. Bottom: "Select All/None", "Delete Selected", "Refresh", "Save Project"

Nutze Threading für große Clip-Listen (100+).
```

---

## Phase 4: Lokale KI-Integration

### Prompt 4.1: Ollama Client

```
Erstelle `utils/ollama_client.py` mit `OllamaClient` Klasse:

1. Methode `start_server() -> bool`
   - Starte Ollama Server mit subprocess
   - Check ob bereits läuft
   - Return True/False

2. Methode `stop_server() -> bool`
   - Stoppe Ollama Server gracefully

3. Methode `is_running() -> bool`
   - Prüfe via HTTP Request zu Ollama API

4. Methode `pull_model(model_name: str) -> bool`
   - Download Ollama Modell

5. Methode `list_models() -> List[str]`
   - Gib alle installierten Modelle zurück

6. Methode `analyze_text(prompt: str, model: str = "llama3.1") -> str`
   - Sende Prompt zu Ollama und gib Response zurück

Behandle Errors (Server nicht verfügbar, etc.) mit aussagekräftigen Messages.
```

### Prompt 4.2: Whisper Handler

```
Erstelle `utils/whisper_handler.py` mit `WhisperTranscriber` Klasse:

1. Methode `transcribe_video(video_path: str, model_size: str = "base") -> List[Dict]`
   - Extrahiere Audio aus Video
   - Transkribiere mit Whisper
   - Rückgabe: [{time: float, text: str, confidence: float}, ...]
   - Modell-Größe: "tiny" (39M), "base" (140M), "small" (244M)

2. Methode `detect_loud_moments(video_path: str, threshold_db: int = -20) -> List[Tuple[float, float]]`
   - Erkenne Momente mit lauter Sprache/Musik
   - Nutze ffmpeg um Audio zu analysieren
   - Rückgabe: [(start_sec, end_sec), ...]

3. Methode `get_speaker_segments(transcription: List[Dict]) -> List[Tuple[float, float]]`
   - Finde zusammenhängende Sprach-Segmente

4. Methode `download_model(model_size: str) -> bool`
   - Download Whisper Modell wenn nicht vorhanden

Nutze Thread-Pool für Audio-Extraktion.
```

### Prompt 4.3: AI Integration Module

```
Erstelle `core/ai_integration.py` mit `AIAnalyzer` Klasse:

1. Methode `analyze_scene_for_keywords(frame: PIL.Image, ollama_client: OllamaClient) -> List[str]`
   - Nutze Ollama um Bild zu beschreiben (nutze llava Modell)
   - Extrahiere 5-10 Keywords
   - Return: ["keyword1", "keyword2", ...]

2. Methode `extract_emotional_moments(transcription: List[Dict]) -> List[Tuple[float, str]]`
   - Erkenne emotional erregte Momente aus Transkription
   - Return: [(timestamp, emotion_type), ...]
   - Nutze Ollama für Sentiment Analysis

3. Methode `calculate_virality_score(
       scene_changes: float,  # 0-1
       audio_peaks: float,     # 0-1
       keywords: List[str],    # könnte viral sein
       emotion_spikes: int,    # Anzahl emotionaler Momente
   ) -> float`  # 1-10
   - Kombiniere alle Faktoren zu Viral-Score
   - Nutze Gewichte: scene_changes (30%), audio (30%), keywords (20%), emotion (20%)

Schreibe Prompts die gut mit lokalen LLMs funktionieren (kurz, fokussiert).
```

### Prompt 4.4: Settings Tab - AI Configuration

```
Implementiere AI-Einstellungen in `ui/tabs/settings_tab.py`:

1. Sektion "Ollama Server"
   - Status-Label (Running / Stopped)
   - Start/Stop Buttons
   - Model Dropdown (liste verfügbare Modelle)
   - Model-Download Button
   - Custom Host URL Input

2. Sektion "Whisper"
   - Model Size Dropdown (tiny, base, small)
   - Download Button
   - Test Transcription Button

3. Sektion "AI Analysis"
   - Checkbox "Enable Scene Analysis"
   - Checkbox "Enable Keyword Extraction"
   - Checkbox "Enable Emotion Detection"
   - Slider "Analysis Confidence Threshold"

4. Sektion "Performance"
   - Checkbox "Use GPU" (wenn verfügbar)
   - CPU Threads Spinner (4-32)
   - RAM Limit Input

5. Log-Viewer
   - QTextEdit mit letzten 100 Log-Zeilen
   - Buttons: Clear, Save, Auto-Scroll Toggle

Nutze Threading für lange Operationen.
```

---

## Phase 5: Video-Editor & Export

### Prompt 5.1: Video Editor

```
Erstelle `core/video_editor.py` mit `VideoEditor` Klasse:

Methoden für jede Operation:

1. `trim_clip(input_path: str, start_frame: int, end_frame: int, output_path: str) -> bool`
   - Schneide Video auf exakte Frames

2. `crop_video(input_path: str, x: int, y: int, width: int, height: int, output_path: str) -> bool`
   - Croppt Video auf Ausschnitt

3. `change_speed(input_path: str, speed_factor: float, output_path: str) -> bool`
   - speed_factor: 0.5 (halb so schnell) bis 2.0 (doppelt so schnell)

4. `add_text_overlay(input_path: str, text: str, position: str, font_size: int, duration_sec: float, output_path: str) -> bool`
   - position: "top-left", "top-right", "bottom-left", "bottom-right", "center"
   - Nutze FFmpeg Filter (drawtext)

5. `add_logo(input_path: str, logo_path: str, position: str, size_percent: float, duration_sec: float, output_path: str) -> bool`
   - logo_path: Path zu Logo PNG/JPG
   - size_percent: 0-100% der Video-Breite
   - Nutze FFmpeg Overlay-Filter

6. `add_fade_transition(input_path: str, duration_sec: float = 0.5, fade_type: str = "fade_in", output_path: str) -> bool`
   - fade_type: "fade_in", "fade_out", "fade_inout"

7. `normalize_audio(input_path: str, target_loudness_db: float = -14.0, output_path: str) -> bool`

Alle Funktionen nutzen FFmpeg über subprocess. Schreibe Error Handling und Progress-Callbacks.
```

### Prompt 5.2: Video Player Widget

```
Erstelle `ui/widgets/video_player.py` mit `VideoPlayerWidget` Klasse (QWidget):

1. Zeige Video mit OpenCV (oder FFmpeg)
2. Playback Controls (Play, Pause, Stop, Seek-Bar)
3. Zeit-Anzeige (current_time / total_time)
4. Volume Slider (0-100%)
5. Speed Dropdown (0.5x, 1.0x, 1.5x, 2.0x)
6. Frame-by-Frame Navigation (← →)
7. Keyboard Shortcuts (Space=Play/Pause, ←/→=Seek)
8. Auto-loop Option

Nutze Threading für Video-Decoding um UI nicht zu blocken.

Optional: Thumbnail-Preview beim Hover über Seek-Bar.
```

### Prompt 5.3: Clip Editor Tab

```
Implementiere "Editor" Tab in `ui/tabs/editor_tab.py`:

1. Oben: Clip Selection Dropdown
   - Zeige alle verfügbaren Clips

2. Mitte-Links: Video Player Widget
   - Zeige aktuellen Clip
   - Trim Handles (Start/End Drag Bar)

3. Mitte-Rechts: Edit Options
   - Trim: Start/End Time Spinners
   - Crop: X, Y, Width, Height Inputs + Visual Crop-Preview
   - Speed: Slider 0.5-2.0
   - Text Overlay: Text Input + Position Dropdown + Font Size
   - Logo: File Browser + Position + Size Slider
   - Fade: Type Dropdown + Duration

4. Unten: Preview Button, Save Changes Button, Reset Button

5. Status: "Unsaved changes" Indicator

Nutze Threading für Preview-Generierung.
```

### Prompt 5.4: Video Exporter

```
Erstelle `core/video_exporter.py` mit `VideoExporter` Klasse:

1. Methode `export_batch(clips: List[Dict], 
                        video_path: str,
                        format: str = "mp4",
                        quality: str = "high",
                        preset: str = "youtube_shorts",
                        output_folder: str) -> List[str]`
   - Exportiere alle Clips als separate Videos
   - Return: List[output_paths]

2. Methode `get_social_media_presets() -> Dict`
   - Return Dict mit Presets:
   ```python
   {
       "youtube_shorts": {height: 1080, width: 1920, ratio: "9:16", fps: 30},
       "tiktok": {height: 1080, width: 1920, ratio: "9:16", fps: 30},
       "instagram_reels": {height: 1080, width: 1920, ratio: "9:16", fps: 30},
       "instagram_feed": {height: 1080, width: 1080, ratio: "1:1", fps: 30},
       "twitter": {height: 720, width: 1280, ratio: "16:9", fps: 30},
   }
   ```

3. Methode `export_with_settings(
       input_path: str,
       output_path: str,
       format: str,
       quality_level: str,  # "low", "medium", "high", "ultra"
       preset: str,
       on_progress: Callable) -> bool`
   - on_progress: Callback mit (current, total, percentage)

4. Methode `verify_export(output_path: str) -> bool`
   - Check ob Datei gültig, hat richtige Auflösung, etc.

Nutze FFmpeg mit optimierten Profiles für jede Plattform.
```

### Prompt 5.5: Export Tab UI

```
Implementiere "Export" Tab in `ui/tabs/export_tab.py`:

1. Oben: Clip Selection
   - Checkbox-Liste aller Clips
   - "Select All / None" Buttons
   - Filter nach Score/Length

2. Export Settings (Left Panel)
   - Format: MP4 / WebM / MKV Dropdown
   - Quality: Low / Medium / High / Ultra Dropdown
   - Social Media Preset: Dropdown mit Presets
   - Custom Resolution: Width/Height Spinners
   - FPS: Spinner
   - Bitrate: "Auto" oder Manual Input

3. Advanced (Collapsible)
   - Video Codec: Dropdown (h264, h265, VP9, AV1)
   - Audio Codec: Dropdown
   - Normalize Audio: Checkbox + dB Input
   - Add Watermark: Checkbox + File Browser

4. Mitte/Unten: Batch Export
   - "Start Export" Button (disabled wenn keine Clips ausgewählt)
   - Progress Bar (gesamt und pro Clip)
   - Status-Text (Processing clip 3/10: file.mp4 - 45% done)
   - Output Folder Browser
   - "Open Output Folder" Button nach Export

5. Cancel Button (visible während Export läuft)

Nutze Threading damit Export nicht UI blockt.
```

---

## Phase 6: Batch-Verarbeitung & Optimierung

### Prompt 6.1: Task Queue System

```
Erstelle `core/task_queue.py` mit `TaskQueue` Klasse:

1. Verwende Thread-Pool (ThreadPoolExecutor)
2. Jede Task hat Status: "pending", "processing", "completed", "failed"
3. Methode `add_task(task_type: str, params: Dict) -> str`
   - task_type: "detect_scenes", "generate_clips", "export_clip", "analyze_ai"
   - Return: task_id

4. Methode `get_task_status(task_id: str) -> Dict`
   - Return: {status, progress_percent, result/error}

5. Methode `cancel_task(task_id: str) -> bool`

6. Methode `get_all_tasks() -> List[Dict]`

7. Callback-System für Task-Updates
   - on_progress, on_complete, on_error Callbacks

Implementiere auch Persistierung: Speichere Task-Queue als JSON damit sie nach App-Restart wiederhergestellt werden kann.
```

### Prompt 6.2: Caching System

```
Erstelle `utils/cache_manager.py` mit `CacheManager` Klasse:

1. Cache für:
   - Extracted Frames (PIL.Image)
   - Generated Thumbnails
   - Scene Detection Results
   - Transcription Results
   - AI Analysis Results

2. Methode `set_cache(key: str, value: Any, ttl_minutes: int = 60) -> None`
   - TTL = Time-To-Live

3. Methode `get_cache(key: str) -> Any | None`

4. Methode `clear_cache(pattern: str | None = None) -> None`
   - Clear alles oder spezifische Pattern

5. Methode `get_cache_stats() -> Dict`
   - Zeige Cache-Size, Hit-Rate, etc.

Nutze SQLite oder einfach JSON-Dateien im Temp-Folder für Cache-Speicherung.

Optional: LRU-Cache um Speicher zu sparen.
```

### Prompt 6.3: Performance Monitoring

```
Erstelle `utils/performance_monitor.py` mit `PerformanceMonitor` Klasse:

1. Track CPU, Memory, GPU Usage über Zeit
2. Track Operation-Zeiten für jede Funktion
3. Methode `start_monitoring() -> None`
4. Methode `stop_monitoring() -> None`
5. Methode `get_metrics() -> Dict`
   - Return: {cpu_percent, memory_mb, gpu_percent, current_operation, operation_time}
6. Methode `log_operation(operation_name: str, duration_sec: float)`

Nutze `psutil` Library. Zeige diese Metriken in der Settings Tab "Performance" Section.
```

### Prompt 6.4: GPU Acceleration

```
Erstelle `utils/gpu_utils.py`:

1. Funktion `is_gpu_available() -> bool`
   - Check ob NVIDIA GPU mit CUDA verfügbar

2. Funktion `get_gpu_info() -> Dict | None`
   - Return: {gpu_name, vram_total_gb, vram_available_gb, compute_capability}
   - Nutze `torch.cuda` oder `pycuda`

3. Funktion `get_ffmpeg_gpu_flags() -> List[str]`
   - Return FFmpeg Flags für GPU-Encoding
   - Bei NVIDIA: ["-hwaccel", "cuda", "-hwaccel_output_format", "cuda"]

4. Nutze diese Flags in VideoExporter wenn GPU=True

Hinweis: GPU hilft vor allem bei Export (Video Encoding), weniger bei Scene Detection.
```

---

## Zusätzliche Prompts: Testing & Troubleshooting

### Testing Prompt

```
Schreibe Unit Tests für OpusClips Offline:

1. Test Scene Detection mit verschiedenen Videos
2. Test Clip Generation Algorithmen
3. Test Ranking/Scoring Logic
4. Test Ollama Integration (Mock wenn nötig)
5. Test Whisper Integration
6. Test Video Export mit verschiedenen Formaten
7. Test UI Responsiveness

Nutze `pytest` Framework. Teste auch Edge Cases:
- 1-Frame Videos
- 10-Sekunden Videos vs 2-Stunden Videos
- Videos ohne Audio
- Sehr schnelle Szenenwechsel
- Keine Szenenwechsel (statisches Video)

Gib mir auch ein `conftest.py` für Test-Fixtures (Test-Videos generieren).
```

### Troubleshooting Prompt

```
Schreibe ein Troubleshooting-System:

1. Häufige Fehler und deren Lösungen
2. System-Kompatibilitäts-Check beim Start
3. Dependency-Check
4. FFmpeg-Check
5. Ollama-Verfügbarkeit-Check
6. GPU-Support-Check

Wenn Fehler erkannt werden: Zeige dem Nutzer aussagekräftige Fehlermeldung und Lösungsschritte.

Beispiel: "FFmpeg nicht gefunden. Installiere mit: brew install ffmpeg (macOS) / apt install ffmpeg (Linux) / Download von ffmpeg.org (Windows)"
```

---

## Best Practices für die Copilot-Nutzung

1. **Pro Prompt: 1 Feature/Komponente**
   - Nicht zu viel auf einmal fragen
   - Fokussiert auf spezifische Funktionalität

2. **Iteratives Vorgehen**
   - Anfrage stellen
   - Code reviewen
   - Fragen zur Verbesserung stellen
   - Refinement

3. **Error Handling**
   - Immer nach Error-Handling fragen falls nicht erwähnt
   - Spezifische Exception-Typen nutzen

4. **Documentation**
   - Docstrings sind wichtig
   - Type Hints überall
   - Kommentare für komplexe Logik

5. **Performance**
   - Nach Threading/Async fragen bei langen Operationen
   - Caching nicht vergessen
   - GPU-Support erwähnen wo möglich

6. **Testing**
   - Immer Unit Tests für kritische Funktionen fordern
   - Edge Cases testen

---

## Vollständiger Workflow-Beispiel

1. Starten Sie mit: "Erstelle die Projekt-Struktur und main.py"
2. Dann: "Implementiere die Config-System"
3. Dann: "Baue die Haupt-UI in PyQt6"
4. Dann: "Integriere FFmpeg Wrapper"
5. Dann: "Füge Scene Detection hinzu"
... und so weiter Phase für Phase.

Am Ende werden Sie ein vollständig funktionales Projekt haben! 🚀
