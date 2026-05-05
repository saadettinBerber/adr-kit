# ADR 0001: Hexagonal Architecture ve Domain-İzole Loglama

- Status: Accepted
- Date: 2026-01-15
- Supersedes: -
- Superseded-by: -

> **Not:** Bu, gerçek bir projeden türetilmiş **anonimleştirilmiş** örnektir.
> Asıl amaç ADR formatını, TL;DR alıntı disiplinini ve "3 ayrı port" gibi
> niteliksel ayrımların nasıl gerekçelendirildiğini göstermektir.

## TL;DR (özet istendiğinde bunu alıntıla)

> **Hexagonal Architecture uygulanır. Domain katmanında hiçbir framework
> bağımlılığı olmaz** — `@Slf4j`, `@Component`, `org.springframework.*`,
> `jakarta.*` **yasak**.
>
> **Loglama 3 ayrı port üzerinden yapılır — bu ayrım bilinçlidir,
> birleştirilmez:**
>
> - **`LogService`** (`common-domain`) → **cross-module** log portu.
>   Common'da yaşar, `common`'ı tüketen her modül kullanabilir.
> - **`LogOutputPort`** (`app-domain`) → **module-local** log portu.
>   `app` modülü `common`'daki `LogService`'e domain seviyesinde bağımlı
>   olmasın diye ayrı port tanımlanmıştır. Bağımlılık yönünü temiz tutar.
> - **`StreamLogPort`** (`app-domain/stream`) → **performance-critical**
>   log portu. Yüksek hacimli olay işleme yapan alt-domain için
>   lazy format (`"event {} from {}", i, src`) **şarttır** — string
>   concat her olayda çalışamaz. `trace/debug` seviyeleri +
>   `(String format, Object... args)` overload'ları + `error(String, Throwable)` mevcut.
>
> **Adapter konumu:** Tüm log adapter'ları **infra katmanında**, ortak bir
> log platformu üzerine kuruludur. Container `@Bean` metodları sadece
> wiring yapar — `return new AppLogger…Adapter()`.

## Context

Örnek uygulama iki temel dış dünyaya temas eder:

1. **OS process'leri** (harici servis watchdog'u, alt-process'ler)
2. **Yüksek hacimli akış işleme** (ağ paketi, mesaj kuyruğu vb.)

Bu iki sorumluluk zaman içinde değişen altyapı seçimleriyle gelir:
process orkestrasyon kütüphanesi değişebilir, akış kaynağı (kafka,
rabbitmq, raw socket) değişebilir, loglama framework'ü taşınabilir.
Domain mantığı (ne zaman restart edilir, hangi olay filtrelenir, hangi
kaynak hangi tüketiciye bağlanır) bu değişimlerden etkilenmemelidir.

Ek olarak proje senior seviye Java/Spring kalıplarını uygular; domain'e
framework sızdırılması hem test edilebilirliği hem de ileride olası
framework değişimlerini zorlaştırır.

## Decision

**Hexagonal Architecture (Ports & Adapters)** uygulanır. Her ana modül üç
katmana bölünür:

```
<module>/
├── <module>-domain       # saf Java — port interface'leri + domain logic
├── <module>-infra        # port'ların adapter implementasyonları
└── <module>-container    # Spring Boot wiring + @Configuration
```

**Domain katmanında hiçbir framework bağımlılığı olmaz.** Özellikle:

- `@Slf4j` / `LoggerFactory` / `org.slf4j.*` import **yasak**.
- `@Component` / `@Service` / `@Autowired` / `@Value` **yasak**.
- `jakarta.*`, `org.springframework.*` **yasak**.

**Loglama** hexagonal port'lar üzerinden yapılır. Üç ayrı port vardır ve
bu ayrım **bilinçlidir** — birleştirilmez.

| Port | Modül | Signature | Gerekçe |
|------|-------|-----------|---------|
| `LogService` | `common-domain` | `info/warn/error(String)` | **Cross-module** log port'u. Common modülde yaşar çünkü `common`'ı tüketen her modül kullanabilir. Signature basit tutulur — genel amaçlı. |
| `LogOutputPort` | `app-domain/port/` | `info/warn/error(String)` | **Module-local** log port'u. `app` modülü `common`'daki `LogService`'e domain seviyesinde bağımlı olmaz — kendi portunu tanımlar. Bağımlılık yönünü temiz tutar. |
| `StreamLogPort` | `app-domain/stream/port/output/` | `trace/debug/info/warn/error` + **`(String format, Object... args)`** overload'ları + `error(String, Throwable)` | **Performance-critical** log port'u. Stream alt-domain'i yüksek hacimli olay işleme yapar; lazy format şarttır çünkü string concat her olayda çalışmamalıdır. `trace/debug` seviyeleri akış debug'ı için gereklidir. |

Özetle: ayrım **bağlam × bağımlılık yönü × performans** ekseninde yapılmıştır.
- Cross-module mu, module-local mi? → `LogService` vs `LogOutputPort`
- Yüksek hacim / performans kaygısı var mı? → `StreamLogPort`

**Adapter konumu:** Port adapter'ları **infra katmanında** yaşar.

- `LogService` → `common-infra/.../log/AppLoggerAdapter`
- `LogOutputPort` → `app-infra/.../log/AppLoggerLogOutputAdapter`
- `StreamLogPort` → `app-infra/.../log/AppLoggerStreamLogAdapter`

Container `@Bean` metodları sadece `return new AppLogger…Adapter()`
şeklinde wiring yapar.

**Maven versiyon yönetimi** sadece root `pom.xml` `<properties>` altında
yapılır; alt modüllerde hardcoded versiyon yazılmaz.

## Alternatives considered

### 1. Layered architecture (controller → service → repository)

Daha basit ve Spring Boot için varsayılan. Ama domain'i I/O'dan izole
etmez — service katmanı her zaman framework'e bağımlı kalır. İleride
altyapı değiştirilirken domain logic'ini ayrıştırmak zor olur. Reddedildi.

### 2. Hexagonal + domain'de SLF4J kullanımı

"Framework değil, library" argümanıyla SLF4J'yi domain'e sokmak. Ama:
- `@Slf4j` Lombok bağımlılığı getirir (annotation processor).
- Domain test'lerinde log çıktısı doğrulanamaz hale gelir (SLF4J static).
- "İstisna açınca" tutarlılık bozulur: neden SLF4J serbest de
  `@Component` yasak? Karar zayıflar.

Reddedildi — hexagonal kuralı **istisnasız** uygulanır.

### 3. Domain'de adapter implementasyonu (port+adapter aynı modülde)

Monolit kolaylığı. Ama infra bağımlılığı domain modülünün `pom.xml`'ine
sızar ve hexagonal'in anlamı kalmaz. Reddedildi.

## Consequences

**Olumlu:**
- Domain modülleri saf Java, Spring/Lombok olmadan derlenir ve test edilir.
- Domain testlerinde Mockito'ya gerek yok — el yazımı fake port'lar yeterli.
- Framework değişimi (ör. log framework'ü, Spring versiyon yükseltme)
  domain'i etkilemez.

**Olumsuz:**
- Her yeni dış kaynak için **port tanımlama + adapter yazma + wiring**
  üçlüsü gerekir — küçük işlerde overhead hissedilir.
- 3 katmanlı modül yapısı küçük projelerde ağır durur; ileride bir modül
  gerçekten çok küçük kalırsa tek modüle indirmek ayrı bir ADR
  gerektirir.

## Open items

_Yok._

## Affected areas

- `common/common-domain/` — `LogService` port'u
- `app/app-domain/` — `LogOutputPort`, `StreamLogPort`
- `app/app-container/.../BeanConfiguration.java` — adapter wiring
- `pom.xml` (root) — merkezi versiyon yönetimi
- [[concepts/module-map]] — modül iskeleti ve bağımlılık grafiği
