# /setup

İlk kurulum — intake interview. Soğuk başlangıç problemini çözer.

## Kullanım

```
/setup
```

`npm run setup` tarafından çağrılır. Doğrudan Claude Code içinde de çalıştırılabilir.

## Adımlar

Kullanıcıya sırayla şu 7 soruyu sor (her soru için cevap bekle):

1. **Alan**: Bu vault hangi alan için?
   _(araştırma / ürün geliştirme / kitap okuma / kişisel / takım wiki / diğer)_

2. **Amaç**: Projenin amacını tek cümleyle yaz.

3. **Kavramlar**: Bu alanda sık kullandığın veya takip etmek istediğin 5 kavram neler?

4. **Kararlar**: Hangi tür kararları belgelemek istiyorsun? (3 örnek ver)

5. **Ajanlar**: Hangi ajanlar aktif olsun?
   - `researcher` — kaynak ingest + wiki sorgu (önerilir)
   - Başka ajan gerekiyor mu? (isim + misyon)

6. **Dil**: Wiki sayfaları hangi dilde yazılsın? (Türkçe / English)

7. **Mevcut belgeler**: `wiki/raw/` içine koyabileceğin mevcut belgeler var mı?
   _(makale, not, transkript, PDF)_

## Kurulum Çıktısı

Cevaplar alındıktan sonra:

1. `CLAUDE.md` — Amaç ve alan bilgisiyle güncelle
2. `memory/status.md` — Proje bağlamıyla doldur
3. `memory/glossary.md` — 5 kavramı ekle
4. `wiki/concepts/` — Her kavram için başlangıç sayfası yaz (LLM alan bilgisinden)
5. `wiki/index.md` — Güncellenmiş katalog
6. `agents/*/MEMORY.md` — Bootstrap Hypotheses bölümünü alan-spesifik hipotezlerle doldur
7. Mevcut belgeler varsa: hemen `/ingest` başlat

## Kural
Tahmin veya uydurma içerik yazma.
Kavram sayfaları "Bu kavram hakkında wiki'de henüz kaynak yok.
İlk ingest sonrası bu sayfa zenginleşecek." notu içerebilir.
