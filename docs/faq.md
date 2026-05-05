# SSS

## ADR'ı kim yazar?

Kararı **alan kişi** — genellikle özelliği geliştiren veya tasarımı
öneren. Tek başına yazılabilir, sonra PR review'ında ekiple gözden
geçirilir.

## ADR'ı ne zaman yazarım — koddan önce mi sonra mı?

**Önce ADR, sonra kod.** İdeal akış:

1. Problem netleşince ADR'ı `Status: Proposed` ile yaz.
2. Ekipten review al.
3. `Status: Accepted` yap.
4. Kodu yaz.

Pratikte bazen kod ilk yazılır (özellikle prototip aşamasında), ama
mimari boyutlu kararsa **merge'den önce** ADR yazılır. ADR olmadan
master'a giren mimari kararı, takımın geri kalanı 6 ay sonra "ne zaman
böyle olduk" diye sorar.

## ADR'ı silebilir miyim?

Hayır — `Status: Superseded` yap, `Superseded-by: NNNN` ekle, yeni
ADR'a yönlendir. Tarihsel iz korunur. Yanlış yazılmış bir ADR
("aslında bunu hiç yapmamalıydık") da silinmez — supersede edilir.

İstisna: Henüz `Status: Proposed` aşamasında ekip "vazgeçtik" derse
silinebilir. Ama Accepted bir kez verildi mi — sadece supersede.

## Iki ADR çelişiyor — hangisi geçerli?

Frontmatter'a bak: `Superseded-by` alanı dolu olan **eski**'dir.
Yeni ADR `Supersedes: NNNN` ile hangisini geçersiz kıldığını söyler.
Bağlantı çift yönlü.

İndex (`docs/index.md`) hangi ADR'ın hangi statüde olduğunu listeler;
karar arıyorsan önce buradan başla.

## Concept sayfaları neyi yapar?

ADR **immutable**, concept sayfaları **yaşar**. Örnek:

- ADR-0002: "Modül sınırları şu şekildedir." (immutable karar)
- `concepts/module-map.md`: "Bugünkü modül haritası, bağımlılık grafı,
  açık durumlar tablosu." (yaşayan referans)

Modül sayısı zamanla artar — ADR-0002 değişmez ama `module-map.md`
güncellenir. Concept sayfası ADR'ın **aktif yansımasıdır**.

## Bu kit'i kullanmak için Claude Code şart mı?

ADR pratiği ve şablonu için **hayır** — `templates/` ve `examples/`
herhangi bir takımda kullanılabilir.

Ama kit'in özel değeri olan **AI by-pass koruması** (skill +
CLAUDE.md snippet) Claude Code kullananlar için geçerlidir. Cursor,
Copilot vs. için benzer bir mekanizma yazılabilir ama bu repo'nun
kapsamında değil.

## ADR'lar neden numaralı?

- **Sıra önemli** — kararlar kronolojiktir.
- **Stabil referans** — "ADR-0007" yıllar sonra da aynı dosyaya işaret
  eder, başlığı değişse bile.
- **Index'leme kolay** — `0001-*` ile başlayan dosya `ls` çıktısında
  doğru sırada gelir.

Numara atlanmaz, geri kullanılmaz. Bir ADR Superseded olsa bile
numarası rezervdir.

## Türkçe mi İngilizce mi yazmalıyım?

Bu kit Türkçe yazılmıştır çünkü Türk takımlar için tasarlandı.
ADR'larını hangi dilde yazacağın **takım tercihi**:

- Takım tek dilli ise → o dilde yaz.
- Takım çok dilli ise → İngilizce daha güvenli.
- AI asistana açıklama yaptıracaksan ADR'la asistanın arayüz dili
  aynı olsun (TL;DR Türkçe ise asistana da Türkçe konuş).

## ADR çok mu kısa olmalı, çok mu uzun?

**TL;DR** kesinlikle kısa olsun (5-15 satır). Bu blok alıntılanır.

**Body** ne kadar gerekiyorsa o kadar olsun — bağlam karmaşıksa
uzun olur, basit kararsa 30 satır yeter. "Kısa olsun" diye gerekçeyi
budama; 6 ay sonra "neden bu alternatif elendi?" sorusunun cevabı
ADR'da olmalı.
