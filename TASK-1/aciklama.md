İsim - Soy isim: Mehmet Talha Kaya
Öğrenci No: 250542009

sistemin kısa açıklaması (maks. 5-6 satır)
Bu sistem, bir ATM para çekme sürecinin mantıksal akışını tanımlar.
Kullanıcı önce PIN kodunu girer ve sistem üç hatalı denemeden sonra kartı bloke eder.
Doğru girişten sonra, sistem çekilecek tutarın 20 TL katı olup olmadığını, bakiye yeterliliğini ve günlük limitin aşılmadığını denetler.
Tüm koşullar uygunsa para çekme işlemi yapılır ve bakiye güncellenir.
Son olarak kullanıcıya başka işlem yapmak isteyip istemediği sorulur; “Evet” yanıtında döngü yeniden başlar, “Hayır” yanıtında işlem sona erer.

Pseudocode:
BAŞLA
    // Sistem değişkenleri
    TANIMLA gunluk_limit, cekilen_bugun = 0
    TANIMLA bakiye
    TANIMLA deneme = 0

    // PIN doğrulama (en fazla 3 hak)
    DÖNGÜ deneme < 3
        OKU pin_girdisi
        EĞER pin_girdisi DOĞRU İSE
            ÇIK PIN_DOGRULANDI
        DEĞİLSE
            deneme <- deneme + 1
            YAZ "Hatalı PIN. Kalan hak: " + (3 - deneme)
        BİTİR
    BİTİR DÖNGÜ

    EĞER deneme == 3 İSE
        YAZ "Kart bloke edildi. İşlem sonlandırılıyor."
        BİTİR
    BİTİR

    // Para çekme döngüsü
    DÖNGÜ true
        OKU cekilecek_tutar

        EĞER (cekilecek_tutar MOD 20) != 0 İSE
            YAZ "Tutar 20 TL katı olmalı."
            DEVAM ET DÖNGÜ
        BİTİR

        EĞER cekilecek_tutar > bakiye İSE
            YAZ "Yetersiz bakiye."
            DEVAM ET DÖNGÜ
        BİTİR

        EĞER (cekilen_bugun + cekilecek_tutar) > gunluk_limit İSE
            YAZ "Günlük limit aşılıyor."
            DEVAM ET DÖNGÜ
        BİTİR

        // İşlem
        bakiye <- bakiye - cekilecek_tutar
        cekilen_bugun <- cekilen_bugun + cekilecek_tutar
        YAZ "Para verildi."

        OKU "Başka işlem? (E/H)" -> cevap
        EĞER cevap == "E" İSE
            DEVAM ET DÖNGÜ
        DEĞİLSE
            ÇIK DÖNGÜ
        BİTİR
    BİTİR DÖNGÜ
BİTİR
