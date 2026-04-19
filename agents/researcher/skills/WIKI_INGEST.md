# Skill: WIKI_INGEST

Yeni kaynak → wiki entegrasyonu.

## Tetikleyici
`wiki/raw/` altında henüz `wiki/sources/` altında özeti olmayan dosya var.

## Adımlar

1. `wiki/raw/` altındaki en son değiştirilen işlenmemiş kaynağı bul
2. Kaynağı oku. Çıkar:
   - Ana konu ve amaç
   - Anahtar bulgular (5 madde)
   - Bahsedilen entity'ler (kişi, ürün, servis, dosya)
   - Geçen kavramlar
   - Alınan veya önerilen kararlar
   - Açık konular / sorular
3. `wiki/sources/YYYY-MM-DD-slug.md` yaz (SCHEMA.md formatı):
   - Frontmatter: title, tags, source (raw dosya yolu), date, status: active
   - Bölümler: Amaç, Anahtar Bulgular, Kararlar, Açık Konular, Sources, Related
4. İlgili her entity için `wiki/entities/` altında sayfa oluştur veya güncelle
   - Çift yönlü link kur: entity → source, source → entity
5. İlgili her kavram için `wiki/concepts/` altında sayfa oluştur veya güncelle
6. Her karar için `wiki/decisions/` altında atomik sayfa yaz
7. `wiki/index.md`'yi güncelle — yeni sayfaları ilgili kategoriye ekle
8. `wiki/log.md`'ye kayıt yaz:
   ```
   ## [YYYY-MM-DD] ingest | slug
   Dokunulan sayfalar: sources/X, entities/Y, concepts/Z
   ```

## Kurallar
- `wiki/raw/` altındaki dosyayı asla değiştirme — sadece oku
- Her iddia `sources/` sayfasına referans versin
- Bir kaynak zaten ingest edilmişse: mevcut source sayfasını güncelle, yenisini oluşturma
- Hata olursa logla ve devam et — tek kaynak tüm döngüyü durdurmasın
