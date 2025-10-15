BAŞLA
    // 0) Giriş
    OKU ogr_no, sifre
    EĞER kimlik_dogrula(ogr_no, sifre) DEĞİLSE
        YAZ "Giriş başarısız" ; BİTİR
    BİTİR

    // 1) Ders listesi ve seçim döngüsü
    DERS_LISTESI <- dersleri_getir(bolum, donem)
    YAZ DERS_LISTESI

    secilenler <- ∅
    toplam_kredi <- 0
    kredi_limiti <- kredi_limiti_hesapla(ogr_no)

    DÖNGÜ true
        OKU "Ders kodu girin (bitirmek için 0): " -> ders_kodu
        EĞER ders_kodu == "0" İSE ÇIK DÖNGÜ BİTİR

        ders <- ders_bul(ders_kodu)

        // 2) Kontroller
        EĞER kontenjan(ders) <= kayitli_sayi(ders) İSE
            YAZ "Kontenjan dolu" ; DEVAM ET DÖNGÜ
        BİTİR

        EĞER on_kosul_saglandi_mi(ogr_no, ders) DEĞİLSE
            YAZ "Ön koşul sağlanmıyor" ; DEVAM ET DÖNGÜ
        BİTİR

        EĞER zaman_cakisiyor_mu(ders, secilenler) İSE
            YAZ "Zaman çakışması var" ; DEVAM ET DÖNGÜ
        BİTİR

        EĞER (toplam_kredi + kredi(ders)) > kredi_limiti İSE
            YAZ "Kredi limiti aşılıyor" ; DEVAM ET DÖNGÜ
        BİTİR

        // Geçtiyse geçici seçime ekle
        secilenler <- secilenler ∪ {ders}
        toplam_kredi <- toplam_kredi + kredi(ders)
        YAZ "Ders sepete eklendi"
    BİTİR DÖNGÜ

    // 3) Onay akışı
    YAZ "Seçilen dersler ve toplam kredi: " + toplam_kredi
    OKU "Kaydı danışman onayına gönderilsin mi? (E/H)" -> gonder

    EĞER gonder == "H" İSE
        YAZ "Kayıt gönderilmedi" ; BİTİR
    BİTİR

    sonuc <- danisman_onayi_iste(ogr_no, secilenler, toplam_kredi)

    EĞER sonuc == "ONAY" İSE
        kesinlestir_kayit(ogr_no, secilenler)
        YAZ "Kayıt kesinleşti"
    DEĞİLSE
        YAZ "Kayıt reddedildi: " + ret_nedeni(sonuc)
    BİTİR
BİTİR
