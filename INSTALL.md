# Kurulum

`adr-kit`'i iki şekilde kurabilirsin: **plugin olarak** (önerilen) veya
**manuel** (klonla, dosyaları kopyala).

---

## Yol 1: Plugin olarak (önerilen)

### Önkoşul

- Claude Code, plugin sistemi destekli sürümde olmalı
- (`/plugin` komutu çalışmalı; çalışmazsa Claude Code'u güncelle)

### Adımlar

Aşağıda **üç alternatif** var. Sadece **birini** uygula.

#### 1a. Terminal komutu ile (REPL açmadan) — önerilen

Sistem terminalinde:

```bash
claude plugin marketplace add saadettinBerber/adr-kit
claude plugin install adr-kit@adr-kit
```

#### 1b. Claude Code REPL içinde slash komutu

Önce `claude` ile REPL'i aç, sonra:

```shell
/plugin marketplace add https://github.com/saadettinBerber/adr-kit
/plugin install adr-kit@adr-kit
```

#### 1c. `settings.json` ile deklaratif (takım / CI)

`~/.claude/settings.json` (kullanıcı geneli) veya proje kökünde
`.claude/settings.json` (proje-spesifik) dosyasına:

```json
{
  "extraKnownMarketplaces": {
    "adr-kit": {
      "source": { "source": "github", "repo": "saadettinBerber/adr-kit" }
    }
  },
  "enabledPlugins": {
    "adr-kit@adr-kit": true
  }
}
```

Claude Code bir sonraki başlatmada otomatik çekip aktif eder.
Repoya commit'lersen, klonlayan herkes için sıfır manuel adım demek.

Kurulum sonrası `/help` çıktısında şu skill ve command'ler görünmeli:
- `adr-kit:architecture-decision` (skill)
- `/adr-kit:adr-init`
- `/adr-kit:adr-new`
- `/adr-kit:adr-install-claude-md`

#### 3. Projeye iskeleti kur

Projenin köküne git, sonra:

```shell
/adr-kit:adr-init
```

Bu komut:
- `docs/decisions/` ve `docs/concepts/` klasörlerini oluşturur
- `0000-template.md` şablonunu yerleştirir
- Boş `index.md` ve `README.md` ekler

#### 4. CLAUDE.md'ne enforcement bloğunu ekle

```shell
/adr-kit:adr-install-claude-md
```

Bu komut `templates/CLAUDE.md.snippet` içeriğini projenin
`CLAUDE.md` (veya `CLAUDE.local.md`) dosyasına ekler. AI asistan
artık mimari sorularda otomatik olarak ADR'ları okur.

#### 5. İlk ADR'ını yaz

```shell
/adr-kit:adr-new architecture-style
```

Bu komut:
- Sıradaki numarayı bulur (`0001`)
- Şablondan yeni dosya oluşturur (`docs/decisions/0001-architecture-style.md`)
- `docs/index.md`'e Proposed tablosuna satır ekler

Dosyayı aç, doldur. Karar netleşince `Status: Proposed` →
`Status: Accepted` yap.

### Test

Claude Code'u yeniden başlat (veya `/clear`), sonra şunu sor:

> "Bu projenin mimari yaklaşımı nedir?"

Claude **önce** `adr-kit:architecture-decision` skill'ini çağırmalı,
sonra ADR'ı okuyup TL;DR'ı alıntılamalı. Doğrudan tahminle cevap
verirse:

- CLAUDE.md güncellenmemiş olabilir → `/adr-kit:adr-install-claude-md`
  tekrar çalıştır
- Plugin yüklü olmayabilir → `/plugin` ile kontrol et
- Skill devre dışı olabilir → `/help` çıktısında görünüyor mu bak

---

## Yol 2: Manuel kurulum (plugin sistemi yoksa)

Plugin sistemini kullanamıyorsan veya hibrit istiyorsan:

### 1. Repo'yu klonla

```bash
git clone https://github.com/saadettinBerber/adr-kit.git /tmp/adr-kit
```

### 2. Skill'i kopyala

```bash
mkdir -p .claude/skills
cp -r /tmp/adr-kit/skills/architecture-decision .claude/skills/
```

Sonuç:

```
your-project/
└── .claude/
    └── skills/
        └── architecture-decision/
            └── SKILL.md
```

### 3. docs/ iskeletini kopyala

```bash
cp -r /tmp/adr-kit/templates/docs ./docs
```

### 4. CLAUDE.md'yi güncelle

`/tmp/adr-kit/templates/CLAUDE.md.snippet` dosyasını aç. HTML yorum
bloğu (`<!-- ... -->`) **dışındaki** içeriği projenin `CLAUDE.md`
veya `CLAUDE.local.md` dosyasının sonuna yapıştır.

### 5. İlk ADR'ını yaz

```bash
cp docs/decisions/0000-template.md docs/decisions/0001-architecture-style.md
```

Aç, doldur, `docs/index.md`'e satır ekle.

---

## Mevcut projeye ekleme (zaten ADR'ları varsa)

Projende zaten ADR'lar varsa:

1. **Plugin yolu:** `/adr-kit:adr-init` mevcut yapıyı korur, sadece
   eksik dosyaları (`README.md`, `0000-template.md`) ekler. Senin için
   onay isteyecek.
2. **Manuel yol:** Sadece `templates/docs/decisions/0000-template.md`
   ve `templates/CLAUDE.md.snippet` dosyalarını kopyala. Mevcut
   ADR'larına dokunma.

Mevcut ADR'larında `## TL;DR (özet istendiğinde bunu alıntıla)` bloğu
yoksa **eklemeyi öner** — skill bu bloğu birebir alıntılayacak şekilde
çalışıyor.

---

## Kaldırma

### Plugin
```shell
/plugin uninstall adr-kit
```

Bu sadece plugin'i kaldırır; projendeki `docs/decisions/` ve
CLAUDE.md değişikliklerine **dokunmaz**. Onları manuel sileceksin.

### Manuel
```bash
rm -rf .claude/skills/architecture-decision
# CLAUDE.md'den enforcement bloğunu manuel sil
# docs/ klasörü senin projenin parçası — saklamak isteyebilirsin
```
