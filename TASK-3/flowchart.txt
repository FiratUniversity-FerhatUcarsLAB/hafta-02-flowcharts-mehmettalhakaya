digraph HastaneSistemi {
    rankdir=TB; nodesep=0.45; ranksep=0.7;
    node [fontname="Helvetica", fontsize=11, shape=box];

    // Ana menü
    start   [shape=ellipse, label="BAŞLA"];
    menu    [shape=diamond, label="Modül seçimi?"];
    end     [shape=ellipse, label="BİTİR"];

    start -> menu;

    // ===== Modül 1: Randevu Alma =====
    subgraph cluster_randevu {
        label="Modül 1: Randevu Alma";
        color="#97b"; style="rounded";

        r_login   [shape=parallelogram, label="OKU TCKN, Şifre"];
        r_ok      [shape=diamond, label="Kimlik doğrulandı mı?"];
        r_bad     [shape=parallelogram, label="YAZ Giriş başarısız"];
        r_pol     [shape=parallelogram, label="OKU Poliklinik"];
        r_list    [label="DOKTOR_LISTESI = getir(poliklinik)"];
        r_outlist [shape=parallelogram, label="YAZ Doktor listesi"];
        r_doc     [shape=parallelogram, label="OKU Doktor"];
        r_slots   [label="UYGUN_SAATLER = saatleri_getir(doktor)"];
        r_outs    [shape=parallelogram, label="YAZ Uygun saatler"];
        r_pick    [shape=parallelogram, label="OKU Seçilen saat"];
        r_avail   [shape=diamond, label="Saat müsait mi?"];
        r_ok2     [label="randevu_onayla(...)"];
        r_sms     [shape=parallelogram, label="SMS: Randevu onaylandı"];
        r_done    [shape=parallelogram, label="YAZ Randevu oluşturuldu"];
        r_full    [shape=parallelogram, label="YAZ Saat dolu"];

        // Akış (Modül 1)
        r_login -> r_ok;
        r_ok -> r_bad  [label="Hayır"];
        r_bad -> r_exit;
        r_ok -> r_pol  [label="Evet"];
        r_pol -> r_list -> r_outlist -> r_doc -> r_slots -> r_outs -> r_pick -> r_avail;
        r_avail -> r_ok2  [label="Evet"];
        r_ok2 -> r_sms -> r_done -> r_exit;
        r_avail -> r_full [label="Hayır"];
        r_full -> r_exit;

        r_exit [shape=ellipse, label="Modül 1 BİTİR", fontsize=10];
    }

    // ===== Modül 2: Tahlil Sonuçları =====
    subgraph cluster_tahlil {
        label="Modül 2: Tahlil Sonuçları";
        color="#7b9"; style="rounded";

        t_login   [shape=parallelogram, label="OKU TCKN, Şifre"];
        t_ok      [shape=diamond, label="Kimlik doğrulandı mı?"];
        t_bad     [shape=parallelogram, label="YAZ Giriş başarısız"];
        t_exist   [shape=diamond, label="Tahlil kaydı var mı?"];
        t_none    [shape=parallelogram, label="YAZ Kayıt yok"];
        t_ready   [shape=diamond, label="Sonuç hazır mı?"];
        t_get     [label="SONUC = sonucu_getir()"];
        t_show    [shape=parallelogram, label="YAZ Sonuç"];
        t_pdfq    [shape=diamond, label="PDF indirilsin mi?"];
        t_pdf     [label="pdf_indir(SONUC)"];
        t_pdfok   [shape=parallelogram, label="YAZ PDF indirildi"];
        t_wait    [shape=parallelogram, label="YAZ Sonuç hazırlanıyor"];

        // Akış (Modül 2)
        t_login -> t_ok;
        t_ok -> t_bad   [label="Hayır"];
        t_bad -> t_exit;
        t_ok -> t_exist [label="Evet"];
        t_exist -> t_none [label="Hayır"];
        t_none -> t_exit;
        t_exist -> t_ready [label="Evet"];
        t_ready -> t_get  [label="Evet"];
        t_get -> t_show -> t_pdfq;
        t_pdfq -> t_pdf   [label="Evet"];
        t_pdf -> t_pdfok -> t_exit;
        t_pdfq -> t_exit  [label="Hayır"];
        t_ready -> t_wait [label="Hayır"];
        t_wait -> t_exit;

        t_exit [shape=ellipse, label="Modül 2 BİTİR", fontsize=10];
    }

    // Ana menüden modüllere giriş ve geri dönüşler
    menu -> r_login [label="Randevu"];
    menu -> t_login [label="Tahlil"];

    r_exit -> end;
    t_exit -> end;
}
