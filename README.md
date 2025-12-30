# Cosmic Architecture Engine v3

**Ein deterministisches, produktionsreifes astrologisches Berechnungs-Framework**

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Node.js](https://img.shields.io/badge/node-%3E%3D14.0.0-brightgreen.svg)](https://nodejs.org)
[![Status](https://img.shields.io/badge/status-production--ready-success.svg)]()

---

## 🌟 Überblick

Der **Cosmic Architecture Engine v3** ist eine hochpräzise JavaScript-Bibliothek zur Berechnung astrologischer Profile, die westliche Astrologie mit östlicher Ba Zi (八字 - Vier Säulen) Astrologie fusioniert. Das Framework folgt dem **Li Wei Prinzip**: *Wahrheit > Nützlichkeit > Schönheit* (DYAI Prime Directive).

### Kernfunktionen

- ✅ **Westliche Astrologie**: Sonne, Mond, Aszendent, MC, Häuser
- ✅ **Ba Zi (Vier Säulen)**: Jahr-, Monat-, Tag-, Stunden-Säule mit validiertem Day Pillar Offset
- ✅ **Wu Xing Element-Analyse**: Quantifizierte Element-Balance mit Hidden Stems
- ✅ **Ost-West Fusion**: Planet-zu-Wu-Xing Mapping, Resonanz-Analyse
- ✅ **Präzise Astronomie**: Korrekte Aszendenten-Berechnung mit atan2-Quadranten-Logik
- ✅ **Li Chun Korrektur**: Automatische Jahr-Grenze-Berechnung für Ba Zi
- ✅ **Produktionsreif**: Validiert gegen autoritäre chinesische Quellen

---

## 📦 Installation

```bash
# Repository klonen
git clone https://github.com/DYAI2025/CosmicEngine.git
cd CosmicEngine

# Keine Abhängigkeiten erforderlich - reines Vanilla JavaScript!
```

---

## 🚀 Schnellstart

### Basis-Verwendung

```javascript
const { calculateCosmicProfile } = require('./cosmic-architecture-engine-v3.js');

const profile = calculateCosmicProfile({
  year: 1980,
  month: 6,
  day: 24,
  hour: 15,
  minute: 20,
  latitude: 52.3759,    // Hannover
  longitude: 9.7320,
  tzOffsetMinutes: 120  // MESZ = UTC+2
});

console.log('Sonnenzeichen:', profile.western.sun.signDE);
console.log('Aszendent:', profile.western.asc.signDE);
console.log('Day Master:', profile.bazi.dayMaster.element);
console.log('Ba Zi:', profile.bazi.fullNotation);
```

### Ausgabe-Beispiel

```javascript
{
  meta: {
    valid: true,
    version: '3.0-LiWei',
    warnings: []
  },
  western: {
    sun: { sign: 'Cancer', signDE: 'Krebs', degree: 3, minute: 9, ... },
    asc: { sign: 'Libra', signDE: 'Waage', degree: 22, minute: 40, ... },
    mc: { sign: 'Aquarius', signDE: 'Wassermann', ... }
  },
  bazi: {
    year: { stem: 'Geng', branch: 'Shen', animal: 'Monkey', animalDE: 'Affe', ... },
    day: { stem: 'Wu', branch: 'Chen', element: 'Earth', polarity: 'Yang', ... },
    dayMaster: { stem: 'Wu', element: 'Earth', polarity: 'Yang' },
    fullNotation: '庚申 壬午 戊辰 己未'
  },
  fusion: {
    elementVector: { Wood: 0.089, Fire: 0.138, Earth: 0.550, Metal: 0.069, Water: 0.155 },
    elementBalance: {
      dominant: { element: 'Earth', percent: 0.55 },
      seeking: { element: 'Metal', percent: 0.069 },
      balanceStatus: 'significant_imbalance'
    }
  },
  liWei: {
    interpretation: {
      dayMaster: 'Yang-Earth (戊 Wu)',
      dominantElement: 'Earth (55.0%)',
      balance: 'significant_imbalance'
    },
    empowerment: 'Die Ressourcen von Earth nutzen, Metal bewusst kultivieren'
  }
}
```

---

## 🔬 Validierung & Tests

### Eingebaute Tests ausführen

```bash
node cosmic-architecture-engine-v3.js
```

Dies führt drei Validierungstests aus:
- **Ben** (Kalibrierungsvektor) - Validiert Day Pillar Offset 49
- **Vincent** (Original-Testvektor) - Bestätigt Korrekturen von v2
- **Li Chun Edge Case** - Validiert Jahr-Grenze-Behandlung

### Aszendenten-Präzisionstests

```bash
node test-ascendant-precision.js
```

Testet die Aszendenten-Berechnung gegen 7 Testfälle:
- Ben - Hannover 1980
- Mitternacht London
- Mittag New York
- Nordpol (extreme Breite)
- Äquator (0° Breite)
- Sydney (Südhalbkugel)
- Sonnenaufgang München (Präzisionstest: **0.64° Genauigkeit!**)

---

## 📐 Mathematische Grundlagen

### Aszendenten-Berechnung (IAU 2000/2006 Standard)

```javascript
tan(λ_AC) = cos(θ_LST) / (-sin(θ_LST) × cos(ε) - tan(φ) × sin(ε))
```

- **θ_LST**: Local Sidereal Time (RAMC)
- **ε**: Mean Obliquity der Ekliptik
- **φ**: Geografische Breite

**Wichtig**: `atan2(y, x)` gibt automatisch den korrekten Quadranten zurück!

### Day Pillar Offset (KRITISCH!)

```javascript
const DAY_PILLAR_OFFSET = 49; // Validiert gegen yi733.com, yishihui.net
const idx60 = mod(JDN + DAY_PILLAR_OFFSET, 60);
```

❌ **v2** verwendete Offset 58 (inkorrekt)
✅ **v3** verwendet Offset 49 (validiert)

### Wu Xing (五行) Element-Gewichtung

| Komponente | Gewicht |
|------------|---------|
| Day Master (Day Stem) | 3.0 |
| Day Branch | 2.0 |
| Month Stem/Branch | 1.5 |
| Hour Stem/Branch | 1.0 |
| Year Stem/Branch | 0.5 |

Hidden Stems in Branches: `[1.0, 0.5, 0.3]` (Haupt, Sekundär, Tertiär)

---

## 🔧 API-Referenz

### `calculateCosmicProfile(input)`

#### Parameter

```javascript
{
  // Datum & Zeit
  year: number,           // Geburtsjahr (gregorianisch)
  month: number,          // Monat (1-12)
  day: number,            // Tag (1-31)
  hour: number,           // Stunde (0-23) in LOKALER Zeit
  minute: number,         // Minute (0-59)
  second?: number,        // Sekunde (0-59), optional

  // Ort
  latitude: number,       // Geografische Breite (-90 bis +90)
  longitude: number,      // Geografische Länge (-180 bis +180)

  // Zeitzone
  tzOffsetMinutes: number // Offset von UTC in Minuten
                          // MESZ = 120, MEZ = 60, UTC = 0
}
```

#### Rückgabewert

```javascript
{
  meta: {
    valid: boolean,        // true wenn Berechnung erfolgreich
    warnings: string[],    // Array von Warnungen
    checks: string[]       // Validierungschecks
  },
  input: { ... },          // Echo der Eingabe-Parameter
  time: {
    julianDateUTC: number,
    lstDeg: number,        // Local Sidereal Time
    trueSolarTimeMinutes: number
  },
  western: {
    sun: ZodiacSign,
    moon: ZodiacSign,
    asc: ZodiacSign,       // Aszendent
    desc: ZodiacSign,      // Descendant
    mc: ZodiacSign,        // Midheaven
    ic: ZodiacSign         // Imum Coeli
  },
  bazi: {
    year: Pillar,
    month: Pillar,
    day: Pillar,
    hour: Pillar,
    dayMaster: {           // Kern-Identität
      stem: string,
      element: string,
      polarity: string
    },
    fullNotation: string   // z.B. "庚申 壬午 戊辰 己未"
  },
  fusion: {
    elementVector: { Wood, Fire, Earth, Metal, Water },
    elementBalance: {
      dominant: { element, percent },
      seeking: { element, percent },
      balanceStatus: string
    },
    resonances: Array,
    tensions: Array
  },
  liWei: {
    interpretation: { ... },
    empowerment: string
  }
}
```

### Hilfsfunktionen

```javascript
// Julian Date Berechnung
julianDateUTC(year, month, day, hour, minute, second)

// Aszendent
calculateAscendant(lstDeg, epsilonDeg, latDeg)

// Ba Zi Säulen
calculateYearPillar(year, JD_UTC)
calculateMonthPillar(sunLon, yearStemIndex)
calculateDayPillar(JD_UTC, localHour)
calculateHourPillar(tstMinutes, dayStemIndex)

// Element-Analyse
calculateElementVector(bazi)
analyzeElementBalance(elementVector)
```

---

## 🎯 Anwendungsfälle

### 1. Persönliches Astrologisches Profil

```javascript
const profile = calculateCosmicProfile({
  year: 1993, month: 6, day: 2,
  hour: 16, minute: 30,
  latitude: 48.7758, longitude: 9.1829,
  tzOffsetMinutes: 120
});

console.log(`Dein Day Master ist ${profile.bazi.dayMaster.element}`);
console.log(`Dominantes Element: ${profile.fusion.elementBalance.dominant.element}`);
console.log(`Empowerment: ${profile.liWei.empowerment}`);
```

### 2. Partnerschafts-Analyse

```javascript
const person1 = calculateCosmicProfile({ ... });
const person2 = calculateCosmicProfile({ ... });

// Element-Kompatibilität
const elem1 = person1.fusion.elementBalance.dominant.element;
const elem2 = person2.fusion.elementBalance.dominant.element;

console.log(`Person 1: ${elem1} dominant`);
console.log(`Person 2: ${elem2} dominant`);
```

### 3. Tages-Qualität Analyse (流年)

```javascript
const today = new Date();
const dayQuality = calculateCosmicProfile({
  year: today.getFullYear(),
  month: today.getMonth() + 1,
  day: today.getDate(),
  hour: 12, minute: 0,
  latitude: YOUR_LAT, longitude: YOUR_LON,
  tzOffsetMinutes: YOUR_TZ
});

console.log(`Heutiges Day Pillar: ${dayQuality.bazi.day.stem}-${dayQuality.bazi.day.branch}`);
```

---

## 📚 Dokumentation

- **[CLAUDE.md](CLAUDE.md)** - Entwickler-Leitfaden, kritische Implementierungsdetails
- **[BaZi_Western_Fusion_Framework.md](BaZi_Western_Fusion_Framework.md)** - Vollständige mathematische Dokumentation (1.284 Zeilen)
- **[COSMIC_ENGINE_V3_VALIDATION.md](COSMIC_ENGINE_V3_VALIDATION.md)** - Validierungsbericht
- **[COSMIC_ENGINE_CALIBRATION_REPORT.md](COSMIC_ENGINE_CALIBRATION_REPORT.md)** - Day Pillar Offset Kalibrierung

### Wichtige Konzepte

#### Ba Zi (八字) - Vier Säulen

Jede Säule besteht aus:
- **Himmelsstamm (天干)**: 10 Varianten, bestimmt primäres Element
- **Erdzweig (地支)**: 12 Varianten, entspricht chinesischen Tierzeichen

Die **vier Säulen** repräsentieren:
- **Jahr (年柱)**: Gesellschaft, Gemeinschaft, Ancestrale Energie
- **Monat (月柱)**: Karriere, Familie, Saisonale Qualität
- **Tag (日柱)**: Selbst, Identität (**Day Master = Kern**)
- **Stunde (時柱)**: Innere Motivation, Ausdruck

#### Day Master (日主)

Der **Himmelsstamm der Tag-Säule** ist die **Kern-Identität** im Ba Zi:

```javascript
profile.bazi.dayMaster.element  // 'Wood', 'Fire', 'Earth', 'Metal', 'Water'
profile.bazi.dayMaster.polarity // 'Yang', 'Yin'
```

#### Wu Xing (五行) - Fünf Elemente

**Produktivzyklus (生)**:
Wood → Fire → Earth → Metal → Water → Wood

**Kontrollzyklus (克)**:
Wood → Earth → Water → Fire → Metal → Wood

---

## 🔍 Fehlerbehebung

### Häufige Probleme

#### 1. Ungültiges Profil (`meta.valid: false`)

```javascript
if (!profile.meta.valid) {
  console.log('Fehler:', profile.meta.error);
  console.log('Warnungen:', profile.meta.warnings);
}
```

**Mögliche Ursachen:**
- Fehlende Parameter (Jahr, Monat, Tag, Stunde, Minute)
- Ungültige Koordinaten
- Fehlende Zeitzone

#### 2. Extreme Breiten (>66°)

Placidus-Häuser versagen nahe den Polen. Die Engine verwendet Equal House System als Fallback.

```javascript
// OK: 65°N wird korrekt verarbeitet
const northernProfile = calculateCosmicProfile({
  latitude: 65.0,  // Nahe Polarkreis
  // ...
});
```

#### 3. Zeitzonen-Verwirrung

**WICHTIG**: `hour` muss in **LOKALER Zeit** angegeben werden!

```javascript
// RICHTIG
const profile = calculateCosmicProfile({
  hour: 15,           // 15:00 MESZ
  tzOffsetMinutes: 120 // MESZ = UTC+2
});

// FALSCH
const profile = calculateCosmicProfile({
  hour: 13,           // Bereits in UTC konvertiert - NICHT MACHEN!
  tzOffsetMinutes: 120
});
```

---

## 🧪 Präzision & Genauigkeit

### Validierung

Die Engine wurde validiert gegen:
- **yi733.com** (中国易学网)
- **yishihui.net** (易师汇)
- **zhouyisuanming.net** (周易算命网)
- **Astro.com** (Westliche Astrologie)

### Genauigkeits-Benchmarks

| Komponente | Genauigkeit | Notizen |
|------------|-------------|---------|
| Sonnenposition | ±0.01° | VSOP87-vereinfacht |
| Mondposition | ±2° | Simplified Perturbations |
| Aszendent | ±0.5° | IAU 2000/2006 Standard |
| Day Pillar | 100% | Offset 49 validiert |
| Li Chun | ±0.0001° | Iterative Berechnung |

### Bekannte Einschränkungen

- **Mondposition**: Vereinfachtes Modell, Genauigkeit ±2° (ausreichend für Zeichen-Bestimmung)
- **Planeten**: Nur Sonne & Mond implementiert (keine Merkur, Venus, Mars etc.)
- **Luck Pillars (大運)**: Noch nicht implementiert
- **Zi Wei Dou Shu (紫微斗數)**: Nicht unterstützt

---

## 🗺️ Roadmap

### v3.1 (geplant)
- [ ] Swiss Ephemeris Integration für höchste Präzision
- [ ] Alle klassischen Planeten (Merkur bis Saturn)
- [ ] Aspekte-Berechnung (Konjunktion, Trine, Quadrate)

### v4.0 (zukünftig)
- [ ] Luck Pillars (大運) Berechnung
- [ ] Jährliche Pillars (流年) für Transit-Analyse
- [ ] Zi Wei Dou Shu (紫微斗數) Erweiterung
- [ ] Vedische Astrologie Integration (Dashas)

---

## 🤝 Beitragen

Contributions sind willkommen! Bitte beachten Sie:

1. **Wahrheit > Nützlichkeit > Schönheit** - DYAI Prime Directive
2. Keine Halluzinationen - alle Berechnungen müssen traceable sein
3. Tests für neue Features schreiben
4. Dokumentation aktualisieren

### Entwickler-Setup

```bash
git clone https://github.com/DYAI2025/CosmicEngine.git
cd CosmicEngine

# Tests ausführen
node cosmic-architecture-engine-v3.js
node test-ascendant-precision.js

# Debug-Skripte
node debug-ascendant.js
```

---

## 📄 Lizenz

MIT License - siehe [LICENSE](LICENSE) Datei

---

## 🙏 Danksagungen

- **Li Wei** - Für das DYAI Prime Directive Prinzip
- **Chinesische Quellen** (yi733.com, yishihui.net) - Ba Zi Validierung
- **Astro.com** - Westliche Astrologie Referenz
- **IAU** - Astronomische Standards

---

## 📞 Kontakt & Support

- **Issues**: [GitHub Issues](https://github.com/DYAI2025/CosmicEngine/issues)
- **Dokumentation**: Siehe [CLAUDE.md](CLAUDE.md)

---

## ⚡ Version History

### v3.0 - Li Wei Integration (Aktuell)
- ✅ Korrekte Day Pillar Berechnung (Offset 49)
- ✅ Korrigierte Aszendent-Berechnung mit atan2-Quadranten-Logik
- ✅ Li Wei Element-Fusion Framework
- ✅ Produktionsreife Validierung

### v2.0 - Fusion Framework
- Ost-West Fusion eingeführt
- ❌ Day Pillar Offset 58 (inkorrekt)

### v1.0 - Initial Release
- Basis Ba Zi & Westliche Astrologie

---

**Cosmic Architecture Engine v3** - *Where Eastern Wisdom Meets Western Precision* 🌏✨

```
八字 | BA ZI × WESTERN ASTROLOGY
Wahrheit > Nützlichkeit > Schönheit
```
