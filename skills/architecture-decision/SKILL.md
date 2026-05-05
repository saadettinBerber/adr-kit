---
name: architecture-decision
description: Use this skill whenever the user asks about architectural decisions (why we chose X, how module Y fits, what's our approach to Z), OR when making code changes that touch module boundaries, database/cache/queue choices, authentication/authorization model, API protocols (REST/gRPC/GraphQL), cross-cutting concerns (logging, error handling, i18n, observability), deployment topology, or any decision that affects multiple modules. Covers both reading existing ADRs to answer architectural questions AND the procedure for creating, updating, or superseding ADRs when architecture changes. Trigger even if the user doesn't explicitly say "architectural decision" — infer from the scope of the change. Scope is this repository only.
---

# Architecture Decision Skill

Mimari kararlar `docs/` altında ADR (Architecture Decision Record) olarak
yaşar ve **source of truth**'tur. Kod bu kararları uygular. Mimari bir
değişiklik olduğunda **önce ADR, sonra kod.**

## 1. Mimari soru geldiğinde

1. Önce `docs/index.md` dosyasını oku.
2. İlgili ADR'ı (`docs/decisions/NNNN-*.md`) ve bağlı concept sayfalarını
   (`docs/concepts/*.md`) **tam olarak** oku (sadece başlığa bakıp
   geçme). Wiki-link'leri (`[[0002-...]]`, `[[concepts/...]]`) takip et.
3. Cevabı ADR numarasına atıfla ver: "ADR-0002'ye göre..."
4. Wiki'de cevap yoksa **uydurma** — "Bu konuda ADR yok, ister misin
   birlikte yazalım?" de.

### Özet/açıklama verirken kritik kurallar — ZORUNLU

**1. TL;DR bloğunu aynen alıntıla.** Her ADR'ın başında
`## TL;DR (özet istendiğinde bunu alıntıla)` bölümü vardır. Kullanıcı
ADR hakkında özet/açıklama istediğinde:

```
Cevabın ilk kısmı ADR'ın TL;DR blockquote'unun birebir yapıştırılması
olmalı. Sonra istersen kendi ek açıklamanı koy. TL;DR bölümünde ne
yazıyorsa o — yeniden formüle etme, sinonim arama, kendi kelimelerinle
anlatma.
```

Yani:

```markdown
ADR-0001'e göre:

> [TL;DR bölümünün tamamı buraya birebir yapıştırılır — bullet'lar,
>  italic, bold, terminoloji hepsi aynen kalır]

(İsteğe bağlı: ek yorum, linkler)
```

**2. TL;DR yoksa ADR'ın ilgili tablosunu/başlığını alıntıla.** Kendi
cümlenle özetleme.

**3. İsimlerden anlam çıkarsama.** Bir sınıf/port/modül ismi belirli bir
kelime içeriyor diye anlamını oradan tahmin etme. Gerekçe ADR'da ne
yazıyorsa odur — kod isimlendirmeleri niyeti yansıtmaz, niyet ADR'da.

**4. Mevcut durum ≠ hedef durum ise ikisini de söyle.** ADR "şu an X,
ideal Y, refactor TODO" diyorsa sadece Y'yi söyleme. "Şu an X (teknik
borç), ADR'a göre Y'ye taşınacak" de. Bu bilgi genelde ADR'ın
**Consequences** veya **Open items** bölümünde olur — oraya mutlaka bak.

**5. Tam ADR'ı oku, sadece TL;DR ile yetinme.** TL;DR alıntı içindir;
kullanıcı "neden" veya "detay" sorarsa ADR'ın Context, Decision,
Consequences bölümlerinden cevap verirsin. Sadece ilk satırı okuyup
geçme.

## 2. Mimari değişiklik geldiğinde

Değişikliğin ADR gerektirip gerektirmediğini değerlendir (aşağıdaki
kriterler). Gerekiyorsa:

1. **Önce ADR işle:**
   - `docs/decisions/` altında bir sonraki numarayı bul (0001, 0002, ...).
   - Yeni ADR'ı aşağıdaki şablonla yaz.
   - Mevcut bir kararı değiştiriyorsa: eski ADR'ı **silme**, frontmatter'ına
     `Superseded-by: NNNN` ekle, status'u `Superseded` yap.
   - `docs/index.md`'yi güncelle.
   - Etkilenen `docs/concepts/*.md` sayfalarını güncelle.
2. **Sonra kod değişikliğini yap.**

## 3. ADR gerektirir / gerektirmez

**Gerektirir:**
- Yeni servis veya modül sınırı
- Database, queue, cache seçimi
- Authentication / authorization modeli
- API protokolü (REST / gRPC / GraphQL)
- Cross-cutting concerns (logging, error handling, i18n, observability)
- Deployment topolojisi

**Gerektirmez:**
- Bug fix
- Tek modül içi küçük refactor
- Lokal tasarım tercihleri
- Test ekleme
- Minor dependency bump
- **Mevcut ADR'ın `Open items` veya teknik borç maddesini kapatmak**
  (aşağıya bak)

Emin değilsen kullanıcıya sor: "Bu değişiklik ADR gerektiriyor gibi
duruyor çünkü [neden]. Açayım mı?"

### Mevcut ADR'ın borcunu kapatırken — yeni ADR AÇMA

Bir ADR'ın `Open items` veya `Consequences → Olumsuz` bölümünde
"teknik borç", "TODO", "refactor'ı ayrı commit" gibi maddeler vardır.
Bu maddelerin uygulanması **yeni bir karar değildir** — mevcut kararın
geciktirilmiş kod uygulamasıdır.

İş akışı:

1. Kodu değiştir (TDD veya proje akışına uygun commit'lerle).
2. İlgili ADR'ı düzenle:
   - `Open items`'daki ilgili maddeyi **sil**.
   - Gerekirse `Consequences → Olumsuz` notunu güncelle ("Tamamlandı
     YYYY-MM-DD" eklenebilir veya kalıcı trade-off değilse silinir).
3. Etkilenen concept sayfalarındaki "Açık durumlar" tablosunu güncelle.
4. Ayrı bir commit'le doküman değişikliğini yaz.

İstisna: borç kapatılırken **tasarımın kendisi değişiyorsa**
(örn. "aslında hedef yapı da yanlışmış, farklı bir yaklaşım
seçiyoruz"), o zaman eski ADR'ı **supersede eden** yeni bir ADR aç.
Normal borç kapatma bu durumu içermez.

## 4. ADR şablonu

```markdown
# ADR NNNN: [Kısa Başlık]

- Status: Proposed | Accepted | Superseded
- Date: YYYY-MM-DD
- Supersedes: - | ADR-XXXX
- Superseded-by: - | ADR-XXXX

## TL;DR (özet istendiğinde bunu alıntıla)

> Kararın 2-5 satırlık özü. Bullet'lar, vurgular, terminoloji.
> AI bu bloğu kullanıcıya birebir alıntılayacak — bu yüzden tek
> başına anlamlı olmalı.

## Context
Hangi problem bu kararı zorunlu kıldı? Hangi kısıtlar var?

## Decision
Ne yapıldığı (tek cümlelik özet + gerekiyorsa detay).

## Alternatives considered
Hangi alternatifler değerlendirildi, neden elendi?

## Consequences
Olumlu ve olumsuz sonuçlar, trade-off'lar.

## Open items
Henüz uygulanmamış maddeler (varsa). Borç kapandıkça buradan silinir.

## Affected areas
- `src/...` (etkilenen kod yolları)
- [[concepts/ilgili-kavram]] (etkilenen concept sayfaları)
```

## 5. Klasör yapısı

```
docs/
├── README.md
├── index.md           # tüm ADR'ların listesi
├── decisions/         # 0001-*.md, 0002-*.md, ...
└── concepts/          # yaşayan kavram sayfaları
```

## 6. Wiki-link kuralı

ADR'lar ve concept'ler birbirine `[[0002-event-driven-core]]` veya
`[[concepts/data-flow]]` formatında bağlanır. Yazarken eksik linkleri
kapat, okurken takip et.
