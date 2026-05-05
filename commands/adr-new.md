---
description: 0000-template.md'den yeni bir ADR oluşturur, sıradaki numarayı bulur, başlığı kebab-case'e çevirir, dosyayı açar ve docs/index.md'ye satır ekler.
---

# /adr-kit:adr-new

Sen `adr-kit` plugin'inin "yeni ADR oluştur" komutusun.

## Argüman

Kullanıcı bu komutu **kısa bir başlıkla** çağırır:

```
/adr-kit:adr-new switch from REST to gRPC for inter-service comms
```

Argüman: `$ARGUMENTS`

## Yapılacaklar

### 1. Önkoşul kontrolü

`docs/decisions/0000-template.md` mevcut mu? Yoksa kullanıcıya:

> ⚠️ ADR iskeleti henüz kurulmamış. Önce `/adr-kit:adr-init` komutunu çalıştır.

…de ve dur.

### 2. Sıradaki ADR numarasını bul

```bash
ls docs/decisions/
```

Çıktıdaki en yüksek `NNNN-*.md` numarasını bul (`0000-template.md` hariç). Bir fazlasını **dört haneli** olarak hesapla. Örnek: en yüksek `0007` ise yeni numara `0008`.

Eğer `0000-template.md`'den başka dosya yoksa yeni numara `0001`.

### 3. Başlığı dosya adına dönüştür

Argümanı:
- Türkçe karakterleri ASCII'ye çevir (ş→s, ğ→g, ü→u, ı→i, ö→o, ç→c, İ→I)
- Boşlukları `-` ile değiştir
- Tüm harfleri küçült
- Alfanumerik ve `-` dışındaki karakterleri sil
- Birden fazla `-`'yi tek `-`'ye indir

Örnek: `"REST'ten gRPC'ye Geçiş"` → `restten-grpcye-gecis`

### 4. Yeni ADR dosyasını oluştur

```bash
NNNN-<slug>.md
```

`docs/decisions/0000-template.md`'yi oku, ilk satırı (`# ADR NNNN: [Kısa Başlık]`) gerçek değerlerle değiştir:

```
# ADR <yeni-numara>: <orijinal-başlık-argümanı>
```

`Date:` satırını bugünün tarihiyle (YYYY-MM-DD formatı) doldur. Diğer alanları boş şablon değerleriyle bırak — kullanıcı dolduracak.

### 5. docs/index.md'ye satır ekle

`docs/index.md`'de `### Accepted` veya `### Proposed` tablosunu bul. Yeni ADR `Status: Proposed` ile başladığı için **Proposed** tablosuna ekle.

Tablo formatı:
```markdown
| [NNNN](decisions/NNNN-<slug>.md) | <Başlık> | YYYY-MM-DD |
```

`Proposed` bölümü `_Yok._` diyorsa o satırı sil ve tabloyu oluştur.

### 6. Kullanıcıya rapor ver

> ✅ ADR `docs/decisions/NNNN-<slug>.md` oluşturuldu.
>
> **Sıradaki adımlar:**
> 1. Dosyayı aç ve doldur — özellikle:
>    - `## TL;DR` bloğu (AI alıntılayacak — net ve eksiksiz olmalı)
>    - `## Context` ve `## Decision`
>    - `## Alternatives considered` (en kritik bölüm — 6 ay sonra "neden bu?" sorusunun cevabı buradan gelir)
> 2. Karar netleşince `Status: Proposed` → `Status: Accepted` yap.
> 3. `docs/index.md`'de satırı **Proposed** tablosundan **Accepted** tablosuna taşı.

## Önemli kurallar

- **Var olan ADR dosyasının üzerine yazma.** Eğer hesapladığın yeni dosya zaten varsa (yarış durumu), bir sonraki numarayı dene.
- **Numara atlanmaz.** En yüksek numara + 1, hep böyle.
- Argüman boşsa kullanıcıya "Başlık ver: `/adr-kit:adr-new kısa başlık`" diyerek sor.
