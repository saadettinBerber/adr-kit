# Neden ADR?

## Sorun

Mimari kararlar genellikle üç yerde yaşar:

1. **Geliştiricinin kafasında** — kişi projeden ayrılınca biter.
2. **Slack/Jira yorumlarında** — arama mümkün ama kronoloji kaybolur,
   "bu hâlâ geçerli mi?" sorusu cevapsız kalır.
3. **Kod içinde yorum olarak** — kod silinince yorum da gider, gerekçe
   kaybolur.

Sonuç: 6 ay sonra "bu sınıf neden böyle?" sorusu sorulduğunda kimse
emin değildir. Karar yeniden açılır, tartışma tekrar yapılır, çoğu
zaman aynı yere varılır — ama **zaman kaybedilir**.

## ADR'ın çözdüğü

Architecture Decision Record (ADR) **immutable** bir karar arşividir:

- **Numaralı** (0001, 0002, ...) — sıralı, kronolojik.
- **Tarihli** — ne zaman alındığı net.
- **Bağlamlı** — sadece "ne yaptık" değil, "hangi problem bizi buna
  zorladı".
- **Alternatifli** — "neden öyle yapmadık" sorusunun cevabı yazılı.
- **Sonuçlu** — olumlu/olumsuz trade-off'lar açıkça yazılı.
- **Supersede edilebilir** — karar değişirse eski ADR silinmez,
  `Superseded-by: NNNN` ile yeni karara bağlanır. Tarihsel iz korunur.

## "Bunu wiki/Notion'da da yapabiliriz" itirazı

Yapabilirsin, ama:

- Wiki/Notion içerikten ayrı yaşar — kod ve ADR senkron olmaz.
- ADR repo'da olduğunda **kod review'ında karar review'ı** yapabilirsin.
- ADR markdown olduğunda AI asistan onu okuyabilir (bu kit'in
  kalbi tam olarak bu).
- Wiki erişimi olmayan biri için kod read-only bilmece olur.

## ADR ne **değil**?

- **Tasarım dokümanı değil.** Tasarım belirsiz, ADR kesin.
- **Tutorial değil.** ADR "ne karar verdik" yazar, "nasıl yapılır"
  yazmaz.
- **Sprint notu değil.** ADR uzun ömürlü; sprint notu kısa.
- **Code comment değil.** Yorum kodun yanında yaşar; ADR koddan
  bağımsız.

## Bu kit'in özelliği

Çoğu ADR rehberi "şablon yaz, klasör aç, başla" der. Bu yeterli değil
çünkü AI asistanlarıyla çalışan ekiplerde ek bir risk var:

> AI asistan ADR'ı **okumadan** mimari soruyu cevaplayabilir.

Hafızadan, dosya isminden, eski sohbetten cevap üretir. Sonuç: ADR'a
yazdığın gerçek karar değil, AI'ın **özetleme uydurması** aktarılır.
Takım yanlış bilgiyle ilerler.

Bu kit bu riski **mekanik olarak** kapatır — bkz.
[why-skill-enforcement](why-skill-enforcement.md).
