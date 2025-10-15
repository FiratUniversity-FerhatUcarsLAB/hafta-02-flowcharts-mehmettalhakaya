BAŞLA
    // Sistem başlatılıyor
    SENSÖRLERİ başlat()
    AĞ bağlantısını kontrol et()
    YAZ "Sistem aktif - 7/24 izleme başlatıldı"

    DÖNGÜ DOĞRU    // Sonsuz döngü
        // 1) Sensör verilerini oku
        hareket <- oku(hareket_sensörü)
        duman   <- oku(duman_sensörü)
        kapı    <- oku(kapı_sensörü)
        sıcaklık <- oku(sıcaklık_sensörü)

        // 2) Tehdit algılama
        tehdit_seviyesi <- 0
        EĞER hareket == ALGILANDI İSE tehdit_seviyesi <- tehdit_seviyesi + 1
        EĞER duman == ALGILANDI İSE tehdit_seviyesi <- tehdit_seviyesi + 2
        EĞER kapı == ZORLANIYOR İSE tehdit_seviyesi <- tehdit_seviyesi + 2
        EĞER sıcaklık > 80 İSE tehdit_seviyesi <- tehdit_seviyesi + 3

        // 3) Aksiyonlar
        EĞER tehdit_seviyesi == 0 İSE
            YAZ "Durum: Güvenli"
        DEĞİLSE EĞER tehdit_seviyesi <= 2 İSE
            YAZ "Uyarı: Düşük seviye tehdit"
            bildir("Ev sahibi", "Düşük seviye tehdit algılandı")
        DEĞİLSE EĞER tehdit_seviyesi <= 4 İSE
            YAZ "Uyarı: Orta seviye tehdit"
            alarm_çal()
            bildir("Polis", "Orta seviye tehdit")
        DEĞİLSE
            YAZ "Acil durum!"
            alarm_çal()
            bildir("İtfaiye", "Yüksek sıcaklık veya yangın tehdidi")
        BİTİR

        // 4) Alarm sıfırlama kontrolü
        OKU "Alarm sıfırlansın mı? (E/H)" -> cevap
        EĞER cevap == "E" İSE
            alarm_sıfırla()
            YAZ "Alarm sıfırlandı"
        BİTİR

        // 5) Döngü gecikmesi
        BEKLE(5 saniye)
    BİTİR DÖNGÜ
BİTİR
