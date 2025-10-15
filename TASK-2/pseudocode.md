BAŞLA
    // Kullanıcı oturumu
    OKU kullanıcı_adı, şifre
    EĞER kimlik_dogrula(kullanıcı_adı, şifre) İSE
        YAZ "Giriş başarılı"
    DEĞİLSE
        YAZ "Hatalı giriş"
        BİTİR
    BİTİR

    // Ürün ekleme döngüsü
    DÖNGÜ true
        OKU ürün_id, adet
        EĞER stok_yeterli_mi(ürün_id, adet) İSE
            SEPETE_EKLE(ürün_id, adet)
            YAZ "Ürün sepete eklendi"
        DEĞİLSE
            YAZ "Yetersiz stok"
        BİTİR

        OKU "Başka ürün eklensin mi? (E/H)" -> cevap
        EĞER cevap == "E" İSE DEVAM ET DÖNGÜ
        DEĞİLSE ÇIK DÖNGÜ
    BİTİR DÖNGÜ

    // İndirim kodu (opsiyonel)
    OKU "İndirim kodu var mı? (E/H)" -> ind_sor
    EĞER ind_sor == "E" İSE
        OKU indirim_kodu
        EĞER indirim_gecerli_mi(indirim_kodu) İSE
            uygula_indirim(indirim_kodu)
            YAZ "İndirim uygulandı"
        DEĞİLSE
            YAZ "Geçersiz indirim kodu"
        BİTİR
    BİTİR

    // Kargo ve adres
    OKU teslimat_adresi
    kargo_ucreti <- kargo_hesapla(sepet_agirligi, teslimat_adresi)

    // Toplam tutar
    ara_toplam <- sepet_toplam()
    genel_toplam <- ara_toplam + kargo_ucreti
    YAZ "Ödenecek Tutar: " + genel_toplam

    // Ödeme
    OKU ödeme_yöntemi   // (Kredi Kartı / Havale vb.)
    EĞER ödeme_yöntemi == "Kredi Kartı" İSE
        OKU kk_no, son_kul, cvc
    BİTİR
    EĞER ödeme_yetkilendir(genel_toplam, ödeme_yöntemi) İSE
        sipariş_no <- sipariş_olustur()
        YAZ "Ödeme başarılı. Sipariş No: " + sipariş_no
        YAZ "E-posta ile makbuz gönderildi."
    DEĞİLSE
        YAZ "Ödeme reddedildi."
        OKU "Ödemeyi tekrar denemek ister misiniz? (E/H)" -> tekrar
        EĞER tekrar == "E" İSE
            GİT Ödeme    // (aynı bloğu tekrar dene)
        DEĞİLSE
            BİTİR
        BİTİR
    BİTİR
BİTİR
