# memex — Master Şema

Bu dosya sistemin anayasasıdır. Her oturumda okunur.

## Sistem Mimarisi

```
TypeScript Motor (src/)     →  scheduler, runner, locker, validator, budget, consolidator
Markdown İçerik             →  wiki/, agents/, memory/, commands/
MCP Server                  →  obsidian-hybrid-search (wiki/ üzerinde semantic arama)
```

## İki Hafıza Katmanı

| Katman | Konum | İçerik | Kim Yazar |
|---|---|---|---|
| Süreç hafızası | `agents/*/MEMORY.md` | Kalıplar, anti-kalıplar, hipotezler | Ajan |
| Domain bilgisi | `wiki/` | Kavramlar, kararlar, sentezler | Ajan (LLM) |

Haftalık `consolidator` süreç hafızasından domain bilgisine köprü kurar.

## Tier Sistemi

- **Tier 1** — Her oturumda yüklenir: `memory/`, `scratch/ideas.md`, `journal/` (son 3 gün)
- **Tier 2** — İlgili ajan/domain aktifken: `wiki/index.md`, `agents/[aktif]/AGENT.md + MEMORY.md`
- **Tier 3** — Sadece açıkça istenince: `wiki/archive/`, `outputs/`, eski `journal/`

## Operasyonlar

- **INGEST** — Yeni kaynak → wiki'ye entegre et (`/ingest`)
- **QUERY**  — Wiki'ye soru sor, cevabı geri-dosyala (`/query`)
- **LINT**   — Wiki sağlık kontrolü (`/lint`)
- **CONSOLIDATE** — MEMORY.md → wiki köprüsü (orchestrator haftalık)

## Hard Rules

1. `wiki/raw/` asla değiştirilmez — sadece okunur
2. Her wiki iddiası bir kaynağa bağlıdır — kaynaksız iddia yasak
3. Çelişkiler silinmez — `## ÇELİŞKİ` başlığıyla işaretlenir
4. Sayfa silinmez — `wiki/archive/` altına taşınır
5. Her operasyon `wiki/log.md`'ye zaman damgalı yazılır
6. `agents/` altındaki başka ajanın dosyası değiştirilmez
7. `wiki/` yazımları locker üzerinden geçer — doğrudan yazma yasak

## Dizin Rehberi

```
CLAUDE.md          bu dosya — master şema
MANIFEST.md        tier routing haritası
CONVENTIONS.md     isimlendirme kuralları
AGENT_REGISTRY.md  aktif ajanlar dizini

src/               TypeScript motor (scheduler, runner, locker, validator, budget, consolidator)
wiki/              domain bilgi tabanı (LLM-Wiki pattern)
agents/            ajan tanımları ve süreç hafızaları
memory/            sistem geneli Tier 1 hafıza
scratch/           ham fikir gelen kutusu
journal/           ajanlar arası iletişim kanalı
commands/          slash command prompt şablonları
scripts/           kurulum ve yönetim scriptleri
outputs/           tarihli çıktılar ve raporlar
```
