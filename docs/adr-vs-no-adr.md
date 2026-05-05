# ADR Gerektirir / Gerektirmez

Her değişiklik ADR yazmayı hak etmez. Çok yazmak da, hiç yazmamak da
sorunludur. Bu sayfa karar kriterlerini netleştirir.

## ADR **gerektirir**

- **Yeni servis veya modül sınırı** — "bu kod hangi modüle gider"
  sorusunu sonsuza kadar tetikler.
- **Database, queue, cache seçimi** — başka bir takıma kullanım
  sorunu çıkar; "neden Postgres değil de Mongo?" sorusu ileride gelir.
- **Authentication / authorization modeli** — güvenlik kararıdır,
  geriye dönüş zordur.
- **API protokolü** (REST / gRPC / GraphQL / SSE) — istemci tarafını
  kilitler; değişimi pahalıdır.
- **Cross-cutting concerns** — loglama, error handling, i18n,
  observability. Bir kez yanlış kurulursa her dosyaya bulaşır.
- **Deployment topolojisi** — mono mu, micro mu; container mı,
  serverless mı.
- **İki veya daha fazla modülü etkileyen herhangi bir değişiklik.**

## ADR **gerektirmez**

- Bug fix.
- Tek modül içi küçük refactor.
- Lokal tasarım tercihleri (örn. iki private metod yerine bir, ya da
  guard clause sırası).
- Test ekleme.
- Minor dependency bump (major bump bazen ADR isteyebilir — özellikle
  framework değişikliği yaratıyorsa).
- **Mevcut ADR'ın `Open items` veya teknik borç maddesini kapatmak.**

## Mevcut ADR'ın borcunu kapatmak ≠ yeni ADR

Bu nokta sık karıştırılır. Bir ADR'ın `Open items` veya
`Consequences → Olumsuz` bölümünde "TODO", "teknik borç", "ayrı commit'te
kapatılacak" gibi maddeler **mevcut kararın geciktirilmiş kod
uygulamasıdır** — yeni karar değildir.

İş akışı:

1. Kodu değiştir.
2. İlgili ADR'ı düzenle: `Open items`'daki maddeyi **sil** (ya da
   "Tamamlandı YYYY-MM-DD" not düş).
3. Etkilenen concept sayfalarını güncelle.
4. Doküman değişikliğini ayrı commit'le yaz.

İstisna: Borç kapatılırken **tasarımın kendisi değişiyorsa** ("hedef
yapı da yanlışmış, farklı yaklaşım seçiyoruz"), eski ADR'ı **supersede
eden** yeni bir ADR aç. Normal borç kapatma bu durumu içermez.

## Emin değilsen

Skill kullanıcıya sorar: *"Bu değişiklik ADR gerektiriyor gibi duruyor
çünkü [neden]. Açayım mı?"*

Bu soru güvenli bir varsayılandır — yanlış pozitif (gereksiz ADR
açma) yanlış negatiften (kararın kayda geçmemesi) çok daha az
zararlıdır. Çünkü gereksiz ADR `Superseded` olabilir veya silinmesi
ucuzdur; kayda geçmemiş karar **kaybedilir**.

## Kuyumcu testi

Bir değişikliği "ADR mı, değil mi?" diye sorgularken kendine sor:

1. **6 ay sonra biri bu kodu görüp "neden böyle?" diye soracak mı?**
   Evet → ADR.
2. **Bu karar bir alternatife karşı bilinçli bir tercih mi?**
   Evet → ADR.
3. **Bu karar geri alınırsa kaç dosya etkilenir?** Birden fazla → ADR.
4. **Bu kararı yeni gelen bir geliştiriciye anlatmam gerekir mi?**
   Evet → ADR.

Üç ya da dört "evet" → kesinlikle ADR. Bir "evet" → muhtemelen değil.
