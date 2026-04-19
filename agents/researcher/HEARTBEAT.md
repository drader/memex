---
schedule: "0 9 * * 2-5"
token_limit: 8000
---

# Researcher Heartbeat

Salı-Cuma 09:00'da çalışır.

## Her Döngüde

### 1. Bağlamı Oku
- `memory/status.md` ve son journal girişleri
- `wiki/index.md` — wiki'nin mevcut durumu
- Kendi `MEMORY.md`'yi oku — önceki döngüden kalıplar

### 2. Değerlendir
- `wiki/raw/` altında ingest edilmemiş kaynak var mı?
  - Var → WIKI_INGEST skill'ini çalıştır
  - Yok → Wakeup rutinini çalıştır

### 3. Wakeup (kuyruk boşsa)
1. `wiki/index.md`'yi tara — backlink'siz sayfaları tespit et
2. `wiki/concepts/` altında eksik sayfa olan kavramları listele
3. `scratch/ideas.md`'deki ham fikirleri gözden geçir
4. `journal/YYYY-MM-DD.md`'ye "Bu hafta araştırılabilecekler" listesi yaz

### 4. Logla
- `wiki/log.md`'ye operasyon kaydı
- `MEMORY.md`'ye onaylanan yeni kalıplar

## Eskalasyon
- `wiki/raw/` 2+ haftadır boşsa → `memory/status.md`'ye uyarı yaz
- INGEST sırasında kaynak okunamıyorsa → `journal/` hata kaydı, devam et
