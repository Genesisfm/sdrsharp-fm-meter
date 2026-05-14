# FM Guard

Een eerste prototype voor breedband FM-beoordeling met een Airspy/SpyServer bron.

De app draait lokaal als browser UI met een Python/Numpy DSP-backend. De huidige build bevat:

- live RF spectrum en waterfall
- WFM demodulatiepad
- MPX spectrum tot 80 kHz
- deviation peak/RMS met 75 kHz referentie
- pilot 19 kHz, stereo subcarriergebied en RDS-band indicatoren
- simulatiebron voor testen zonder dongel
- aparte SpyServer adapterlaag voor de echte Airspy netwerkbron

## SDR# plugin

Er staat ook een eerste C# SDR# plugin in `sdrsharp_fm_meter/`. Die maakt een paneel voor FM deviation, RDS power, pilotniveau en FM signaalsterkte. Voor bouwen zijn de SDR# Plugin SDK DLL's nodig:

```text
sdrsharp_fm_meter/lib/SDRSharp.Common.dll
sdrsharp_fm_meter/lib/SDRSharp.Radio.dll
```

Daarna:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\sdrsharp_fm_meter\build.ps1
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\sdrsharp_fm_meter\install.ps1 -SdrSharpRoot C:\SDRSharp
```

## Starten

Gebruik de meegeleverde Codex Python runtime:

```powershell
& 'C:\Users\Robert\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe' -m fm_guard
```

Open daarna:

```text
http://127.0.0.1:8765
```

## Broninstellingen

De simulator staat standaard aan. De SpyServer-adapter staat in `fm_guard/spyserver.py`.
Airspy SpyServer wordt door SDR# gebruikt als `AIRSPY Server Network`; Airspy documenteert vooral het gebruik en de serverconfiguratie, terwijl de client-wire-format beperkt publiek gedocumenteerd is. Daarom zit de clientcode als losse module in dit project.

Voor echte metingen is het doelpad:

1. verbind met `sdr://host:5555`
2. vraag Full IQ of voldoende breed IQ aan
3. stream complex IQ naar `FmAnalyzer`
4. toon RF spectrum, MPX spectrum en deviation-statistiek

## Projectstructuur

```text
fm_guard/
  __main__.py       startpunt
  server.py         lokale HTTP API
  dsp.py            WFM DSP en meetwaarden
  sources.py        simulator en broninterface
  spyserver.py      Airspy/SpyServer clientadapter
  static/           browser UI
```
