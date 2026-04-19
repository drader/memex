# Wiki Schema — Anayasa

Bu dosya wiki'nin anayasasıdır. LLM her wiki yazımında bu kurallara uyar.

## Amaç

Alan-bağımsız kalıcı bilgi arşivi. Her proje kendi CLAUDE.md'sinde amacını tanımlar.

## Klasör Yapısı

| Klasör | İçerik | İçermez |
|---|---|---|
| `raw/` | Ham kaynaklar — makaleler, PDF, transkript | İşlenmiş hiçbir şey |
| `sources/` | Her ham kaynak için bir özet sayfası | Yorum, sentez |
| `entities/` | Kişiler, ürünler, servisler, dosyalar | Soyut kavramlar |
| `concepts/` | Soyut kavramlar, terimler, fikirler | Somut varlıklar |
| `decisions/` | Atomik kararlar (bir karar = bir sayfa) | Tartışma süreci |
| `syntheses/` | Üst düzey sentez, karşılaştırma, analiz | Ham kaynak özetleri |
| `archive/` | Eskiyen sayfalar — asla silinmez | Aktif içerik |
| `_seed/` | Başlangıç şablonları — ilk kurulumda silinebilir | Gerçek bilgi |

## Sayfa Formatı

```markdown
---
title: Sayfa Başlığı
tags: [etiket1, etiket2]
source: sources/YYYY-MM-DD-slug.md
date: YYYY-MM-DD
status: active
---

# Sayfa Başlığı

İçerik. Her önemli iddia [[kaynak]] ile desteklenir.

## ÇELİŞKİ (varsa)
[Kaynak A] şunu derken, [Kaynak B] bunu diyor. Çözülmedi — [tarih].

## Sources
- [[sources/YYYY-MM-DD-slug]]

## Related
- [[concepts/ilgili]]
- [[entities/ilgili]]
```

## Naming Convention

- Dosya adları: kebab-case (`hook-engagement.md`)
- Source özetleri: `YYYY-MM-DD-slug.md`
- Entity sayfaları: varlığın kanonik adı (`openai-gpt4.md`)
- Concept sayfaları: kavramın kanonik adı (`retrieval-augmented-generation.md`)

## Hard Rules

1. `raw/` asla değiştirilmez — sadece okunur
2. Her iddia kaynağa bağlıdır — kaynaksız iddia yasak
3. Çelişki `## ÇELİŞKİ` başlığıyla işaretlenir, silinmez
4. Sayfa silinmez — `archive/` altına taşınır, status: archived
5. Çift yönlü link zorunludur: A → B ise B de A'yı `## Related`'da listeler
6. Her operasyon `log.md`'ye kaydedilir
