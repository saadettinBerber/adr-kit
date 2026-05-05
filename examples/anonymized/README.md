# Anonim Örnek ADR'lar

Bu klasördeki ADR'lar gerçek bir Java/Spring Boot projesinden
**anonimleştirilerek** türetilmiştir. Proje-özgü isimler ve detaylar
generic karşılıklarıyla değiştirilmiş, ama:

- TL;DR formatı ve "alıntıla" disiplini
- Bölüm yapısı (Context / Decision / Alternatives / Consequences / Open items)
- Niteliksel ayrımların gerekçelendirilme şekli
- Wiki-link kullanımı

aynen korunmuştur. Amaç, bu ADR'ları kopyalayıp kullanmak değil —
**format ve gerekçelendirme tonunu** göstermektir.

## Örnekler

### [0001 — Hexagonal Architecture ve Domain-İzole Loglama](0001-hexagonal-architecture-and-logging.md)

**Gösterdiği:** Büyük, kuruluş seviyesi mimari kararın nasıl ADR'a
yazıldığı. Özellikle dikkat çekici nokta: **3 ayrı log port'unun**
(`LogService`, `LogOutputPort`, `StreamLogPort`) nasıl tek bir tabloda
"bağlam × bağımlılık yönü × performans" ekseninde gerekçelendirildiği.

İsim benzerliği nedeniyle birleştirilmesi cazip görünen üç port'un
**neden ayrı olması gerektiğinin** ADR'da yazılı olması, AI asistanın
"bunlar aslında aynı port" diye yanlış özetleme yapmasını engeller.

### [0010 — LogService Port İmza Genişletmesi](0010-logservice-port-extension.md)

**Gösterdiği:** Ufak ama mimari olan kararların nasıl ADR'a yazıldığı.
"Sadece bir interface'e metod ekledik" gibi görünen bu değişiklik
aslında:

- ADR-0001'in (kasıtlı minimal port imzası) **anlamını değiştiriyor**
- `default` metod tercihi **geriye uyumluluğu** korumayı amaçlıyor
- "Kapsam dışı" bölümünün açık olması **gelecekteki kararı**
  kolaylaştırıyor (Marker/MDC eklenmek istendiğinde)

Bu yüzden bug-fix değil, ADR'a yazılması gereken bir karardır.

## Bu örneklerden öğrenilecek

1. **TL;DR mutlaka `> blockquote` formatında.** AI asistan bu bloğu
   alıntılayacak; markdown formatlama (bold, bullet, code) korunmalı.
2. **Tablolar gerekçeyi sıkıştırır.** Üç port'u prose ile anlatmak
   yerine tabloda bağlam/sebep yazmak hem yazara hem okura kolaylık.
3. **"Open items" bölümü yaşıyor.** Borç kapandıkça madde silinir,
   ADR yeniden yazılmaz. Bu format borcu görünür tutar.
4. **Alternatives bölümü reddedilen tercihi de yazar.** "Neden öyle
   yapmadık" sorusunun cevabı 6 ay sonra kafanda olmaz; ADR'da olur.
