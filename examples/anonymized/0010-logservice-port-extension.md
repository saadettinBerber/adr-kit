# ADR 0010: LogService Port İmza Genişletmesi

- Status: Accepted
- Date: 2026-03-22
- Supersedes: -
- Superseded-by: -

> **Not:** Bu, gerçek bir projeden türetilmiş **anonimleştirilmiş** örnektir.
> "Mevcut bir port'un imzasını genişleten ufak ama mimari olan kararın"
> nasıl ADR'a yazıldığını göstermek için seçildi.

## TL;DR (özet istendiğinde bunu alıntıla)

> **`LogService` portu (`common-domain`) `debug` + `trace` seviyeleri,
> SLF4J-style `{}` placeholder (`Object...` varargs) ve `Throwable`
> overload'larıyla genişletilir.** Yeni metodlar `default` olarak
> tanımlanır — mevcut adapter'lar (özellikle test-double'lar) hiç
> değişmeden çalışmaya devam eder, sadece üç çekirdek metodu
> (`info/warn/error(String)`) implement etmeleri yeterlidir.
>
> **Genişletmenin tetikleyicisi:** Belirli bir alt-modülde dört sınıfın
> SLF4J `LoggerFactory`'i doğrudan kullandığı tespit edildi. Bu sınıfların
> log çağrı pattern'leri (`logger.error("msg", throwable)`,
> `logger.warn("stderr {}", line)`) mevcut dar `LogService` imzasına
> sığmıyor — port genişletilmeden hexagonal kuralı (ADR-0001) ihlali
> kapatılamaz.
>
> **Kapsam dışı:** SLF4J `Marker`, MDC, kullanıcı-olay (audit) metodları
> bu ADR'a girmez. Gerçek ihtiyaç oluştuğunda ayrı bir karar ile eklenir.

## Context

ADR-0001 `LogService` port imzasını minimal tuttu:

```java
public interface LogService {
    void info(String message);
    void warn(String message);
    void error(String message);
}
```

Bu, **kasıtlı bir minimal kapsam tercihiydi**: "Genişletme geriye-uyumlu
(yeni metod ekleme): ihtiyaç gelince `default` metod ile veya breaking
değişiklik olmadan eklenebilir."

İhtiyaç şimdi ortaya çıktı. Bir alt-modülün loglama denetiminde dört
sınıfın ADR-0001'i (domain ve adapter loglarının port üzerinden gitmesi)
ihlal ettiği görüldü:

| Sınıf | Çağrı pattern'i | Mevcut imzaya uyar mı |
|-------|------------------|------------------------|
| `ProcessRunner` | `logger.warn("stderr {}", line)` | ❌ placeholder |
| `ProcessRunner` | `logger.warn("Failed to read stdout", e)` | ❌ Throwable |
| `JobRunner` | `logger.error("worker task failed", e)` | ❌ Throwable |
| `Reconciler` | `logger.info("reconciled {} interrupted job(s)", count)` | ❌ placeholder |

Mevcut dar imzayla bu sınıfları port'a taşımanın yolu sadece
`String.format` ile manuel concat — okunabilirliği bozar, lazy
formatting'i kaybeder, ve `Throwable.getMessage()` yapıştırmak stack
trace bilgisini yutar.

Yani bu ADR olmadan ADR-0001 ihlali **kapatılamaz**, sadece başka bir
biçimde ihlal edilir.

## Decision

`LogService` portuna aşağıdaki metodlar **`default` olarak** eklenir:

```java
public interface LogService {
    // Mevcut çekirdek (zorunlu)
    void info(String message);
    void warn(String message);
    void error(String message);

    // Genişletme — default
    default void debug(String message) {}
    default void trace(String message) {}
    default void info(String format, Object... args) { info(fmt(format, args)); }
    default void warn(String format, Object... args) { warn(fmt(format, args)); }
    default void error(String format, Object... args) { error(fmt(format, args)); }
    default void error(String message, Throwable t) { error(message); }
}
```

Adapter (`AppLoggerAdapter`) yeni overload'ları altta yatan log
platformunun SLF4J-uyumlu API'sine doğrudan delege eder.

## Alternatives considered

### A. Yeni ayrı port (`ExtendedLogService`)

Eski portu bozmamak için ikinci bir port. Reddedildi — domain'de iki
benzer port'un seçim kriteri belirsiz, "hangisini kullanmalıyım"
sorusu üretir.

### B. Dar imzayla kalıp `String.format` ile manuel concat

Kod okunabilirliği düşer, lazy formatting kaybolur, `Throwable` stack
trace yutulur. Reddedildi.

### C. Tüm SLF4J API'sini (Marker, MDC) eklemek

Henüz ihtiyaç yok. Reddedildi — gerçek ihtiyaç gelince ayrı ADR.

## Consequences

**Olumlu:**
- ADR-0001 ihlali (domain'de SLF4J doğrudan kullanım) kapatılabilir.
- Mevcut test-double'lar (el yazımı `FakeLogService`) değişmeden çalışır.
- Failure path'lerinde stack trace artık port üzerinden geçirilebilir.

**Olumsuz:**
- Port imzası 3 metoddan ~10 metoda çıkar — implement etmesi
  zorunlu olmasa da imza okunması ağırlaşır.
- `default` metodların `fmt(...)` fallback'i çok ilkel; üretim
  adapter'ı her zaman override etmeli.

## Open items

_Yok — tüm SLF4J ihlalleri ADR-0001 uyumuna taşındı._

## Affected areas

- `common/common-domain/.../LogService.java`
- `common/common-infra/.../AppLoggerAdapter.java`
- Etkilenen alt-modüldeki 4 sınıfın `LoggerFactory` → `LogService` taşıması
- [[0001-hexagonal-architecture-and-logging]] (genişlettiği port)
