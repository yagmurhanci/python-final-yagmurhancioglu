# python-final-yagmurhancioglu
# Python Final Ödevi - Mekansal Analiz

## Proje Amacı

Bu proje, Python kullanarak dünyadaki Populated Places (şehir noktaları) veri seti içinde Türkiye sınırları içinde kalan şehirleri belirlemek amacıyla yapılmıştır. QGIS ve PyQGIS kütüphanesi kullanılarak mekansal analiz gerçekleştirilmiş, sonuçlar bir shapefile dosyası olarak kaydedilmiş ve görselleştirilmiştir.

## Kullanılan Araçlar ve Veri Setleri

- **QGIS**: PyQGIS ile Python konsolunda analiz yapılmıştır.
- **Natural Earth Veri Seti**: Populated Places (şehir noktaları) ve Admin 0 - Countries (ülke sınırları) katmanları kullanılmıştır.
  - [Natural Earth Data](https://www.naturalearthdata.com/)
- **Python**: Mekansal analiz ve verilerin işlenmesi için PyQGIS modülü kullanılmıştır.

## İzlenen Adımlar

1. **Veri Setlerinin Yüklenmesi**:
   - Populated Places (şehir noktaları) ve Countries (ülke sınırları) veri setleri QGIS'e yüklendi.

2. **Türkiye'nin Sınırlarının Belirlenmesi**:
   - Countries katmanında `NAME` alanında "Turkey" değeri kullanılarak Türkiye sınırları seçildi.

3. **Populated Places İçinden Türkiye'ye Ait Şehirlerin Filtrelenmesi**:
   - Populated Places veri setindeki noktalar, Türkiye sınırları içinde olup olmadıklarına göre filtrelendi.

4. **Sonuçların Kaydedilmesi**:
   - Türkiye sınırları içindeki şehirler, yeni bir shapefile dosyası olarak kaydedildi.

5. **Sonuçların Görselleştirilmesi**:
   - Yeni shapefile dosyası QGIS'te harita üzerine eklenerek görselleştirildi.

## Kullanılan Python Kodu

```python
# Türkiye'nin sınırlarını belirlemek ve şehirleri filtrelemek için kullanılan Python kodu

# Türkiye'yi bul
country_name_field = "NAME"  # Türkiye'nin bulunduğu alan adı
country_name_value = "Turkey"  # Türkiye'nin adı
selected_country = None

for feature in countries_layer.getFeatures():
    if feature[country_name_field] == country_name_value:
        selected_country = feature
        break

if not selected_country:
    print(f"{country_name_value} bulunamadı! Alan adını ve değerini kontrol edin.")
else:
    print(f"{country_name_value} bulundu!")

# Türkiye'nin geometrisini al
country_geometry = selected_country.geometry()

# Türkiye sınırları içindeki şehirleri filtrele
selected_cities = []

for city in cities_layer.getFeatures():
    if city.geometry().within(country_geometry):
        selected_cities.append(city)

print(f"Populated Places veri seti içinde Türkiye sınırları içinde {len(selected_cities)} şehir bulundu.")

# Seçilen şehirleri yeni bir shapefile olarak kaydet
output_path = "C:/Users/cemku/OneDrive/Masaüstü/pythonfinal/turkey_populated_places.shp"
fields = cities_layer.fields()

writer = QgsVectorFileWriter(
    output_path,
    "UTF-8",
    fields,
    QgsWkbTypes.Point,  # Katman türü: Nokta
    cities_layer.crs(),  # Katmanın koordinat referans sistemi
    "ESRI Shapefile"  # Shapefile formatı
)

if writer.hasError() != QgsVectorFileWriter.NoError:
    print(f"Yazma hatası oluştu: {writer.hasError()}")

for city in selected_cities:
    writer.addFeature(city)

del writer  # Yazma işlemini tamamla
print(f"Yeni shapefile başarıyla kaydedildi: {output_path}")
```

## Çıktılar

![turkey_map_populated_places](https://github.com/user-attachments/assets/d1898f81-8551-4991-a19a-e2472a641025)



