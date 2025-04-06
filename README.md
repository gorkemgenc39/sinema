const readline = require('readline');

const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});

const MAX_FILM = 10;
const MAX_MUSTERI = 20;

let filmAdlari = [];
let filmSureleri = [];
let filmTurleri = [];

let musteriAdlari = [];
let musteriEmailleri = [];

let biletler = Array.from({ length: MAX_MUSTERI }, () => Array(MAX_FILM).fill(false));

function anaMenu() {
    console.log("\n--- Sinema Müşteri Kayıt Sistemi ---");
    console.log("1. Film Ekle");
    console.log("2. Müşteri Ekle");
    console.log("3. Bilet Satış (Müşteri-Film Eşleştirme)");
    console.log("4. Biletleri Listele");
    console.log("0. Çıkış");
    rl.question("Seçiminiz: ", secim => {
        switch (secim) {
            case '1':
                filmEkle();
                break;
            case '2':
                musteriEkle();
                break;
            case '3':
                biletSatisi();
                break;
            case '4':
                biletleriListele();
                break;
            case '0':
                console.log("Çıkılıyor...");
                rl.close();
                break;
            default:
                console.log("Geçersiz seçim!");
                anaMenu();
        }
    });
}

function filmEkle() {
    if (filmAdlari.length >= MAX_FILM) {
        console.log("Film kapasitesi doldu!");
        return anaMenu();
    }

    rl.question("Film adı: ", ad => {
        rl.question("Film süresi (dakika): ", sure => {
            rl.question("Film türü: ", tur => {
                filmAdlari.push(ad);
                filmSureleri.push(parseInt(sure));
                filmTurleri.push(tur);
                console.log("Film eklendi.");
                anaMenu();
            });
        });
    });
}

function musteriEkle() {
    if (musteriAdlari.length >= MAX_MUSTERI) {
        console.log("Müşteri kapasitesi doldu!");
        return anaMenu();
    }

    rl.question("Müşteri adı: ", ad => {
        rl.question("Müşteri email: ", email => {
            musteriAdlari.push(ad);
            musteriEmailleri.push(email);
            console.log("Müşteri eklendi.");
            anaMenu();
        });
    });
}

function biletSatisi() {
    if (musteriAdlari.length === 0 || filmAdlari.length === 0) {
        console.log("Müşteri veya film bilgisi eksik.");
        return anaMenu();
    }

    console.log("\nMüşteriler:");
    musteriAdlari.forEach((ad, i) => {
        console.log(`${i}. ${ad} (${musteriEmailleri[i]})`);
    });

    rl.question("Müşteri seç (index): ", musteriIndex => {
        musteriIndex = parseInt(musteriIndex);
        if (isNaN(musteriIndex) || musteriIndex < 0 || musteriIndex >= musteriAdlari.length) {
            console.log("Geçersiz müşteri seçimi.");
            return anaMenu();
        }

        console.log("\nFilmler:");
        filmAdlari.forEach((ad, i) => {
            console.log(`${i}. ${ad} - ${filmTurleri[i]} (${filmSureleri[i]} dk)`);
        });

        rl.question("Film seç (index): ", filmIndex => {
            filmIndex = parseInt(filmIndex);
            if (isNaN(filmIndex) || filmIndex < 0 || filmIndex >= filmAdlari.length) {
                console.log("Geçersiz film seçimi.");
                return anaMenu();
            }

            biletler[musteriIndex][filmIndex] = true;
            console.log("Bilet satışı başarıyla gerçekleşti.");
            anaMenu();
        });
    });
}

function biletleriListele() {
    musteriAdlari.forEach((ad, i) => {
        console.log(`\nMüşteri: ${ad} (${musteriEmailleri[i]})`);
        const izlenenler = [];
        biletler[i].forEach((izledi, j) => {
            if (izledi) {
                izlenenler.push(filmAdlari[j]);
            }
        });
        if (izlenenler.length > 0) {
            console.log("İzlediği Filmler: " + izlenenler.join(", "));
        } else {
            console.log("Henüz film seçilmedi.");
        }
    });
    anaMenu();
}

// Başlat
anaMenu();
