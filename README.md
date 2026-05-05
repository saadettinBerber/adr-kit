# adr-kit

> Claude Code kullanan ekipler için ADR (Architecture Decision Record)
> disiplin sistemi — **plugin olarak kurulan**, ADR şablonu + skill +
> CLAUDE.md entegrasyonu paketi.

ADR yazmak yetmez — **AI asistanın onları okuduğundan emin olmak
gerekir**. Bu kit ADR şablonu + Claude Code skill'i + slash command'lar
+ CLAUDE.md entegrasyonu olarak gelir; AI'ın hafızadan / isimden /
özetlemeden cevap vermesini mekanik olarak engeller.

## Ne sağlar?

- 🤖 **`architecture-decision` skill'i** — AI asistana ADR'ı okuma ve
  birebir alıntılama disiplinini zorlar.
- ⚡ **3 slash command** — `/adr-kit:adr-init`, `/adr-kit:adr-new`,
  `/adr-kit:adr-install-claude-md` ile sıfır manuel kurulum.
- 📜 **ADR şablonu** — TL;DR bloğu, Open items disiplini, supersede
  mekanizması.
- 🔒 **CLAUDE.md entegrasyonu** — AI'ın "cevabı zaten biliyorum"
  kestirmesini yasaklar (otomatik enjekte edilebilir).
- 📚 **Anonim örnek ADR'lar** — gerçek projeden türetilmiş, format ve
  ton referansı.
- 📖 **Felsefe + SSS** — neden bu kadar disiplin gerekli, ne zaman
  ADR yazmalı / yazmamalı.

## Kurulum — iki yol

### Yol 1: Plugin olarak (önerilen)

```shell
# 1. Marketplace'i ekle
/plugin marketplace add https://github.com/<your-username>/adr-kit

# 2. Plugin'i kur
/plugin install adr-kit@adr-kit

# 3. Projeye iskeleti kur
/adr-kit:adr-init

# 4. CLAUDE.md'ne enforcement bloğunu ekle
/adr-kit:adr-install-claude-md

# 5. (Opsiyonel) İlk ADR'ını yaz
/adr-kit:adr-new architecture-style
```

> 💡 5. adım **opsiyonel.** İlk ADR'ını şimdi yazmak zorunda değilsin —
> bkz. [İlk ADR'ını ne zaman yazmalısın?](#i̇lk-adrını-ne-zaman-yazmalısın)

### Yol 2: Manuel (klonla, kopyala)

Plugin sistemini kullanmıyorsan: [INSTALL.md](INSTALL.md)'deki adım
adım talimatları takip et.

## İlk ADR'ını ne zaman yazmalısın?

Hemen yazmak zorunda değilsin. Plugin'i kur, CLAUDE.md snippet'ını
ekle, ve devam et. Bir sonraki mimari karar geldiğinde AI sana ADR
açmayı önerecek — o zaman yaz. Geçmiş kararları **toplu yazmaktansa
organik biriktirmek** daha sürdürülebilir bir alışkanlıktır.

İki yaklaşım var, ikisi de geçerli:

| Yaklaşım | Ne zaman uygun? |
|----------|-----------------|
| **Retrospektif** — mevcut kararları oturup yaz | Proje küçük, kararlar net hatırlanıyor, ekip ADR alışkanlığını hızlı kurmak istiyor |
| **Organik** — sıfır ADR'la başla, kararlar geldikçe yaz | Proje karmaşık, geçmişi yazmak günler sürer, ekip yeni alışkanlığı doğal yoldan benimsesin |

**Organik yaklaşım için kritik adım:** `/adr-kit:adr-install-claude-md`
mutlaka çalıştırılmalı. Bu adım atlanırsa AI mimari soruları eski
usul cevaplar ve "ADR açalım mı?" diye sormaz — kit yarım çalışır.

Skill kuruluyken `docs/decisions/` boşsa, AI mimari bir soru aldığında
şunu der: *"Bu konuda ADR yok, ister misin birlikte yazalım?"* — bu,
kit'in tasarımının doğal sonucu.

## Hızlı bakış — repo yapısı

```
adr-kit/
├── .claude-plugin/
│   ├── plugin.json                         # Plugin manifesti
│   └── marketplace.json                    # Tek-plugin marketplace
│
├── skills/
│   └── architecture-decision/
│       └── SKILL.md                        # ADR okuma/yazma disiplini
│
├── commands/                                # Slash commands
│   ├── adr-init.md                         # /adr-kit:adr-init
│   ├── adr-new.md                          # /adr-kit:adr-new
│   └── adr-install-claude-md.md            # /adr-kit:adr-install-claude-md
│
├── templates/
│   ├── docs/                               # /adr-kit:adr-init bunu kopyalar
│   │   ├── README.md
│   │   ├── index.md
│   │   ├── decisions/0000-template.md
│   │   └── concepts/
│   └── CLAUDE.md.snippet                   # /adr-kit:adr-install-claude-md bunu enjekte eder
│
├── examples/anonymized/                    # Format referansı (2 ADR)
├── docs/                                   # Repo dokümantasyonu
│   ├── why-adrs.md
│   ├── why-skill-enforcement.md
│   ├── adr-vs-no-adr.md
│   └── faq.md
│
├── PHILOSOPHY.md                           # Repo manifestosu
├── INSTALL.md                              # Manuel kurulum
├── README.md
└── LICENSE
```

## Felsefe

> Yazılı bir karar, **okunmadığı sürece** bir karar değildir.

Detay: [PHILOSOPHY.md](PHILOSOPHY.md)

## Hangi soruya cevap arıyorsan

| Soru | Belge |
|------|-------|
| ADR neden yazmalıyım? | [docs/why-adrs.md](docs/why-adrs.md) |
| Skill / CLAUDE.md kuralı niye gerekli? | [docs/why-skill-enforcement.md](docs/why-skill-enforcement.md) |
| Bu değişiklik ADR gerektirir mi? | [docs/adr-vs-no-adr.md](docs/adr-vs-no-adr.md) |
| Sıkça sorulanlar | [docs/faq.md](docs/faq.md) |
| Format nasıl olmalı? | [examples/anonymized/](examples/anonymized/) |
| Plugin nasıl kurulur? | Yukarıdaki "Yol 1" |
| Manuel nasıl kurulur? | [INSTALL.md](INSTALL.md) |

## Test edilen ortam

- Claude Code (plugin sistemi destekli sürüm)
- Türkçe ADR'lar (İngilizce kullanım için ADR'ları kendi dilinde yaz —
  skill her iki dilde de çalışır)

## Lisans

[MIT](LICENSE) — kullan, kopyala, değiştir, yay.
