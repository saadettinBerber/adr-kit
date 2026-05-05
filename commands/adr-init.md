---
description: ADR sistemini bu projeye kurar — docs/ iskeletini oluşturur, ilk index'i yazar, kurulum sonrası adımları gösterir.
---

# /adr-kit:adr-init

Sen `adr-kit` plugin'inin kurulum komutusun. Görevin: kullanıcının mevcut projesine **ADR dokümantasyon iskeletini** kurmak.

## Yapılacaklar

### 1. Proje köküne git ve mevcut durumu kontrol et

```bash
pwd
ls docs/decisions/ 2>/dev/null
```

Eğer `docs/decisions/` zaten varsa ve **boş değilse**: kullanıcıya "Bu projede zaten ADR'lar var. Mevcut yapıyı bozmadan eksik parçaları (şablon, README, index) eklemeyi öneriyorum. Devam edeyim mi?" diye sor. Onay almadan dosya **yazma**.

Eğer `docs/decisions/` yok veya boşsa: doğrudan adım 2'ye geç.

### 2. Klasör iskeletini oluştur

```bash
mkdir -p docs/decisions docs/concepts
```

### 3. Plugin'in template dosyalarını kopyala

Plugin'in `templates/docs/` klasöründeki şu dosyaları projenin `docs/` klasörüne yaz:

- `templates/docs/README.md` → `docs/README.md`
- `templates/docs/index.md` → `docs/index.md`
- `templates/docs/decisions/0000-template.md` → `docs/decisions/0000-template.md`

Plugin dosyalarını okumak için: plugin yüklü olduğu için bu dosyalar plugin cache'inde mevcut. Glob veya Read kullanarak `**/templates/docs/README.md` gibi bul ve içeriğini hedefe yaz. Eğer plugin yolu net değilse, kullanıcıya plugin'in nereye kurulduğunu sor (`/plugin` komutu ile görebilir).

`docs/concepts/` boş kalır (kullanıcı concept sayfaları ekledikçe doldurur).

### 4. Kullanıcıya sonraki adımları söyle

Kurulum bitince **birebir** şu mesajı yazdır:

> ✅ ADR iskeleti `docs/` altına kuruldu.
>
> **Sonraki adımlar:**
>
> 1. **CLAUDE.md entegrasyonu** — AI asistanın ADR disiplinine uyması için:
>    `/adr-kit:adr-install-claude-md`
>
> 2. **İlk ADR'ını yaz** — projende zaten alınmış kararları kayda al:
>    `/adr-kit:adr-new <kısa-başlık>`
>
> 3. **Test et** — Claude Code'a "bu projenin mimari yaklaşımı nedir?" diye sor.
>    AI **önce** `architecture-decision` skill'ini çağırmalı, sonra cevap vermeli.
>
> Felsefe ve detay için: plugin'in `PHILOSOPHY.md` ve `docs/why-adrs.md` dosyalarına bak.

## Önemli kurallar

- **Var olan dosyaları üzerine yazma.** Eğer `docs/index.md` zaten varsa kullanıcıya sor.
- **Git ile takipsiz dizinde çalışıyorsan** uyar — kullanıcı `git init` yapmadan sürdürmek istemeyebilir.
- **Adım adım rapor ver** — her dosya yazıldığında "✓ docs/index.md yazıldı" gibi.
