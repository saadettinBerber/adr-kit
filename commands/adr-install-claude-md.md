---
description: adr-kit'in CLAUDE.md entegrasyon bloğunu projenin CLAUDE.md (veya CLAUDE.local.md) dosyasına ekler. AI asistanın ADR disiplinine uymasını zorlar.
---

# /adr-kit:adr-install-claude-md

Sen `adr-kit` plugin'inin CLAUDE.md enjeksiyon komutusun.

## Görev

Plugin'in `templates/CLAUDE.md.snippet` dosyasındaki **ADR enforcement bloğunu** projenin CLAUDE.md (veya CLAUDE.local.md) dosyasına ekle.

## Yapılacaklar

### 1. Hedef CLAUDE.md dosyasını belirle

Şu sırayla kontrol et ve **ilk bulunanı** kullan:

1. `CLAUDE.local.md` — varsa bu (gitignore'daki kişisel/yerel sürüm)
2. `CLAUDE.md` — varsa bu

İkisi de yoksa kullanıcıya sor:

> Bu projede CLAUDE.md veya CLAUDE.local.md bulamadım. Hangisini oluşturmamı istersin?
>
> 1. `CLAUDE.md` — git'e commit edilir, takım paylaşır (önerilen)
> 2. `CLAUDE.local.md` — gitignore'da kalır, kişisel kullanım

Kullanıcının cevabına göre boş bir dosya oluştur, sonra adım 2'ye geç.

### 2. Mevcut CLAUDE.md'yi kontrol et — zaten kurulu mu?

Hedef dosyada şu metinler **zaten varsa** kullanıcıya bildir ve dur:

- `architecture-decision` skill referansı
- "Forbidden shortcuts" başlığı veya `Yasak kestirmeler:` ifadesi
- `adr-kit` plugin adı

> ⚠️ CLAUDE.md'de zaten ADR enforcement bloğu görünüyor. Üzerine yazmak istersen önce mevcut bloğu manuel olarak sil, sonra komutu tekrar çalıştır.

…de ve dur.

### 3. Snippet içeriğini hazırla

Plugin'in `templates/CLAUDE.md.snippet` dosyasını oku. **HTML yorum bloğunu** (`<!-- ... -->`) çıkar — sadece içerik kalsın.

Snippet içinde geçen `architecture-decision` skill referansını, plugin namespace'iyle uyumlu kullanıcı için **iki türde de çalışacak şekilde** bırak — snippet zaten bu hesaba göre yazılmış olmalı.

### 4. CLAUDE.md'nin sonuna ekle

Hedef dosyanın **mevcut içeriğinin sonuna** iki boş satır + snippet içeriğini ekle (üzerine yazma — append).

### 5. Kullanıcıya rapor ver

> ✅ ADR enforcement bloğu `<dosya-adı>`'na eklendi.
>
> **Test etmek için:** Claude Code'u yeniden başlat veya `/clear` ile sohbeti temizle, sonra şunu sor:
>
> > "Bu projenin loglama yaklaşımı nedir?"
>
> Claude **önce** `adr-kit:architecture-decision` skill'ini çağırıp ADR'ları okumalı. Eğer doğrudan tahminden cevap verirse:
> - CLAUDE.md doğru güncellenmemiş olabilir (`cat <dosya>` ile son satırlara bak)
> - Plugin yüklü olmayabilir (`/plugin` ile kontrol et)
>
> Henüz ADR yazmadıysan: `/adr-kit:adr-new <başlık>` ile başla.

## Önemli kurallar

- **Üzerine yazma — append et.** Mevcut CLAUDE.md içeriği korunmalı.
- **HTML yorum bloğunu sil** — kullanıcının CLAUDE.md'sine `<!-- ... -->` enjekte etme.
- **İdempotent ol** — aynı komutu iki kez çalıştırırsa ikinci kez yine eklemesin (adım 2'deki kontrol bunu sağlar).
