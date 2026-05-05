# Niye Skill Zorunlu? — AI By-pass'ları

## Problem

ADR yazmak yetmez. AI asistan onları **okumadan** cevap verebilir.

Üç tipik by-pass yolu:

### 1. "Hafızadan cevap"

Geliştirici sorar: *"Bizim log stratejimiz neydi?"*

AI cevaplar: *"Genelde projeler şöyle yapar: SLF4J ile..."*

Sorun: Cevap **bu projenin ADR'ı** değil; AI'ın "benzer projeler nasıl
yapar" tahmini. Kullanıcı farkında olmadan **tahmini** karara dönüştürür.

### 2. "Dosya isminden çıkarsama"

Geliştirici sorar: *"`LogOutputPort` ne yapıyor?"*

AI cevaplar: *"İsminden anlaşıldığı kadarıyla logları dış sisteme
gönderiyor olmalı."*

Sorun: İsim niyeti yansıtmaz — niyet ADR'da. Belki de bu port "module
sınırını korumak için" var, "dış sisteme yayın" için değil. AI
**ismi yorumlar**, gerçek gerekçeyi okumaz.

### 3. "Özet uydurma"

Geliştirici sorar: *"ADR-0001'i özetler misin?"*

AI cevaplar: *"Hexagonal kullanıyoruz, domain saf, vs."*

Sorun: ADR'da yazan **kelime kelime kararlar** kayboldu. Özette
"3 ayrı log port'u var" detayı atlanırsa takım bunu **birleştirme**
işine girişir — ADR ihlali. Özetleme = bilgi kaybı.

## Skill bu üçünü mekanik olarak kapatır

`architecture-decision` skill'i AI'a şunu zorlar:

| By-pass | Skill'in cevabı |
|---------|-----------------|
| Hafızadan cevap | "Önce `docs/index.md` oku, ilgili ADR'ı **tam olarak** oku, ADR numarasına atıfla cevapla." |
| İsimden çıkarsama | "İsimlerden anlam çıkarsama. Gerekçe ADR'da ne yazıyorsa odur." |
| Özet uydurma | "TL;DR bloğunu **birebir alıntıla** — kendi cümlenle özetleme." |

Bu kurallar **prompt seviyesinde** zorlanır. Skill çağrılmadığında bile
CLAUDE.md'deki entegrasyon bloğu (`templates/CLAUDE.md.snippet`) AI'a
"mimari soruda **önce skill'i çağır**" der.

## "Forbidden shortcuts" — neden açıkça yasaklıyoruz?

CLAUDE.md snippet'ında şu satırlar var:

- "Cevabı zaten biliyorum" → yine de skill'i kullan.
- "CLAUDE.md'de link var" → kasten yok; skill'i kullan.
- "İsim zaten açıklayıcı" — isimler niyeti yansıtmaz; skill'i kullan.

Bunlar AI'ın **kullandığı tipik kestirme cümleleridir**. Yasaklanmadığı
sürece AI bu cümlelerin arkasına saklanarak skill çağrısını atlar.
İsimlendirip yasaklamak — AI'ın kendi savunmasını bozar.

## Kit'in bütünü ne sağlar?

```
[Geliştirici sorusu]
        ↓
[CLAUDE.md kuralı: "mimari sorularda skill çağır"]
        ↓
[Skill: "ADR'ı tam oku, TL;DR alıntıla, isimden çıkarsama"]
        ↓
[ADR'ın TL;DR'ı: "kararı birebir aktarılan kelimelerle"]
        ↓
[Doğru cevap]
```

Tek halka eksik olursa zincir kırılır. Bu yüzden kit **dört parça
birden** içerir: skill + şablon + CLAUDE.md snippet + örnek ADR'lar.
