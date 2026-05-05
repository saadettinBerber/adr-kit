# Felsefe

`adr-kit` sadece bir ADR şablonu değildir.
Bir **disiplin sistemidir**.

---

ADR'lar yıllardır yazılıyor. Şablonu Google'da aratırsan onlarca
varyasyon bulursun. Klasör yapısı, frontmatter, numaralandırma —
bunların hepsi çözülmüş problem.

Bu kit ne diyor o zaman?

Bu kit şunu söylüyor:

> **Yazılı bir karar, okunmadığı sürece bir karar değildir.**

Çoğu projede ADR'lar yazılır, klasöre konur, ve kimse okumaz. AI
asistan da okumaz — hafızasından, dosya isminden, ya da "benzer
projeler nasıl yapar"dan cevap üretir. Sonuç: ADR'a yazdığın gerçek
karar yerine, AI'ın **özetleme uydurması** takıma aktarılır.

Bu kit bu boşluğu **mekanik olarak** kapatır.

---

## Üç ilkesi

### 1. ADR tek doğruluk kaynağıdır.

Mimari soruların cevabı kafalarda, Slack'te, eski koddaki yorumlarda
değil — `docs/decisions/` altında. Eğer cevap orada yoksa **karar
henüz alınmamıştır**.

### 2. AI by-pass'ı isimlendirilir ve yasaklanır.

"Cevabı zaten biliyorum", "isim açıklayıcı", "CLAUDE.md'de link var"
— bunlar AI'ın kestirme cümleleridir. Yasaklanmadığı sürece AI bu
cümlelerin arkasına saklanarak ADR'ı atlar. Kit bu kestirmeleri
**isim isim** sayar ve kapatır.

### 3. Özetleme = bilgi kaybı.

ADR'ın TL;DR'ı kullanıcıya **birebir alıntılanır**. AI özetlemez,
sinonim bulmaz, "kendi kelimeleriyle anlatmaz". TL;DR yazarken
yazarın yaptığı kelime seçimi (özellikle "yasak", "zorunlu", "ayrı"
gibi kuvvetli kelimeler) korunur. Bu seçim genellikle bilinçli ve
bilgi yüklüdür.

---

## Karşıt görüşler

### "Bu paranoyak değil mi? AI zaten makul cevaplar veriyor."

Çoğu zaman evet. Ama mimari kararlar **çoğu zaman doğru cevap**
gerektirmiyor — **bu projenin kararı** gerektiriyor. AI'ın "makul"
cevabı bu projeye yanlış olabilir, çünkü her proje farklı kısıtlarla
karar verir. ADR olmadan AI'ın "makul" cevabı **standart bir cevaptır**;
projenin kararı değil.

### "Bu kadar disiplin küçük takımlar için fazla."

Belki — ama disiplin **ekip büyüdüğünde** ekleyemezsin. ADR'sız
büyüyen kod tabanlarında 50. geliştirici geldiğinde "bu kod neden
böyle" sorusu **cevapsızdır** ve kimse yetkili karar veremez.
Kit'in kurulum maliyeti 1 saatten az; ileride kurtaracağı zaman
ölçeklenmez büyüklüktedir.

### "Sadece şablon yeterli, skill'e gerek yok."

Şablon olmadan ADR yazılamaz, doğru. Ama şablon olup skill olmadığında
ADR'lar **okunmaz**. Skill yokken AI by-pass yapar, geliştiriciler de
AI'a güvendiği için ADR'ı açmaz. Skill, ADR'ı **hayatta tutan**
parçadır.

---

## Bir kelimeyle

**Disiplin.**

ADR sistemi, yazılım disiplinin kod tarafına yansımasıdır:
"Yapılmış olanı kayda al, kayda alınmamış olanı yapılmış sayma."

Bu kit, o disiplini AI çağında uygulanabilir kılar.
