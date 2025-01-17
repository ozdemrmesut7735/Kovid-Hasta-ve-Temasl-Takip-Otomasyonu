# Mesut Özdemir 2312721053 Kovid-Hasta-ve-Temasl-Takip-Otomasyonu
/*Mesut Özdemir 2312721053*\
#include <iostream>
#include <fstream>
#include <cstring>

using namespace std;

const int MAX_HASTA_SAYISI = 100;
const int MAX_TEMASLI_SAYISI = 30;

struct Temasli {
    char adi[100];
    char adres[200];
};

struct Hasta {
    char adi[100];
    char soyadi[100];
    char telefon[20];
    char adres[200];
    bool testSonucu;
    float vucutIsi;
    Temasli temaslilar[MAX_TEMASLI_SAYISI];
    int temasliSayisi;
};

Hasta hastalar[MAX_HASTA_SAYISI];
int hastaSayisi = 0;

void VeriKaydet() {
    ofstream dosya("veriler.dat", ios::out | ios::binary);
    if (!dosya) {
        cout << "Dosya olusturulamadi!" << endl;
        return;
    }

    dosya.write(reinterpret_cast<char*>(&hastaSayisi), sizeof(hastaSayisi));
    for (int i = 0; i < hastaSayisi; ++i) {
        dosya.write(reinterpret_cast<char*>(&hastalar[i]), sizeof(Hasta));
    }

    dosya.close();
    cout << "Veriler .dat dosyasina kaydedildi." << endl;
}

void VeriYukle() {
    ifstream dosya("veriler.dat", ios::in | ios::binary);
    if (!dosya) {
        cout << "Kayitli veri bulunamadi." << endl;
        return;
    }

    dosya.read(reinterpret_cast<char*>(&hastaSayisi), sizeof(hastaSayisi));
    for (int i = 0; i < hastaSayisi; ++i) {
        dosya.read(reinterpret_cast<char*>(&hastalar[i]), sizeof(Hasta));
    }

    dosya.close();
    cout << "Veriler .dat dosyasindan yuklendi." << endl;
}

void HastaEkle() {
    if (hastaSayisi < MAX_HASTA_SAYISI) {
        Hasta yeniHasta;
        cout << "Hasta Adi: ";
        cin >> yeniHasta.adi;
        cout << "Hasta Soyadi: ";
        cin >> yeniHasta.soyadi;
        cout << "Telefon: ";
        cin >> yeniHasta.telefon;
        cout << "Adres: ";
        cin.ignore();
        cin.getline(yeniHasta.adres, 200);
        cout << "Test Sonucu (1: Pozitif, 0: Negatif): ";
        cin >> yeniHasta.testSonucu;
        cout << "Vucut Isisi: ";
        cin >> yeniHasta.vucutIsi;

        yeniHasta.temasliSayisi = 0;
        hastalar[hastaSayisi] = yeniHasta;
        hastaSayisi++;

        cout << "Hasta kaydi olusturuldu." << endl;
    } else {
        cout << "Maksimum hasta sayisina ulasildi." << endl;
    }
}

void TemasliEkle() {
    char hastaAdi[100];
    char hastaSoyadi[100];

    cout << "Temasli eklemek istediginiz hastanin adini girin: ";
    cin >> hastaAdi;
    cout << "Hastanin soyadini girin: ";
    cin >> hastaSoyadi;

    for (int i = 0; i < hastaSayisi; ++i) {
        if (strcmp(hastalar[i].adi, hastaAdi) == 0 && strcmp(hastalar[i].soyadi, hastaSoyadi) == 0) {
            if (hastalar[i].temasliSayisi < MAX_TEMASLI_SAYISI) {
                Temasli yeniTemasli;
                cout << "Temasli Adi_Soyadi: ";
                cin >> yeniTemasli.adi;
                cout << "Temasli Adresi: ";
                cin.ignore();
                cin.getline(yeniTemasli.adres, 200);

                hastalar[i].temaslilar[hastalar[i].temasliSayisi] = yeniTemasli;
                hastalar[i].temasliSayisi++;
                cout << "Temasli eklendi." << endl;
            } else {
                cout << "Maksimum temasli sayisina ulasildi." << endl;
            }
            return;
        }
    }

    cout << "Hasta bulunamadi." << endl;
}

void HastaListele() {
    if (hastaSayisi > 0) {
        for (int i = 0; i < hastaSayisi; ++i) {
            cout << "Hasta Adi: " << hastalar[i].adi << " " << hastalar[i].soyadi << endl;
            cout << "Telefon: " << hastalar[i].telefon << endl;
            cout << "Adres: " << hastalar[i].adres << endl;
            cout << "Test Sonucu: " << (hastalar[i].testSonucu ? "Pozitif" : "Negatif") << endl;
            cout << "Vucut Isisi: " << hastalar[i].vucutIsi << endl;

            cout << "Temaslilar:" << endl;
            for (int j = 0; j < hastalar[i].temasliSayisi; ++j) {
                cout << "  - Isim: " << hastalar[i].temaslilar[j].adi << endl;
                cout << "    Adres: " << hastalar[i].temaslilar[j].adres << endl;
            }
            cout << "-----------------------" << endl;
        }
    } else {
        cout << "Kayitli hasta bulunamadi." << endl;
    }
}

void HastaSil() {
    if (hastaSayisi == 0) {
        cout << "Kayitli hasta bulunamadi." << endl;
        return;
    }

    char hastaAdi[100];
    char hastaSoyadi[100];
    cout << "Silmek istediginiz hastanin adini girin: ";
    cin >> hastaAdi;
    cout << "Hastanin soyadini girin: ";
    cin >> hastaSoyadi;

    bool bulundu = false;
    for (int i = 0; i < hastaSayisi; ++i) {
        if (strcmp(hastalar[i].adi, hastaAdi) == 0 && strcmp(hastalar[i].soyadi, hastaSoyadi) == 0) {
            bulundu = true;

            for (int j = i; j < hastaSayisi - 1; ++j) {
                hastalar[j] = hastalar[j + 1];
            }
            hastaSayisi--;
            cout << "Hasta kaydi silindi." << endl;
            VeriKaydet();
            break;
        }
    }

    if (!bulundu) {
        cout << "Belirtilen hasta bulunamadi." << endl;
    }
}

int main() {
    VeriYukle();

    char secim;
    do {
        cout << "=======================================================\n";
        cout << "         KOVID HASTA VE TEMASLI TAKIP SISTEMI          \n";
        cout << "=======================================================\n";
        cout << "  1. Hasta Ekle\n";
        cout << "  2. Hasta Listele\n";
        cout << "  3. Hasta Sil\n";
        cout << "  4. Temasli Ekle\n";
        cout << "  5. Verileri Kaydet\n";
        cout << "  6. Cikis\n";
        cout << "=======================================================\n";
        cout << "  Lutfen bir secim yapin: ";
        cin >> secim;

        switch (secim) {
            case '1':
                HastaEkle();
                break;
            case '2':
                HastaListele();
                break;
            case '3':
                HastaSil();
                break;
            case '4':
                TemasliEkle();
                break;
            case '5':
                VeriKaydet();
                break;
            case '6':
                VeriKaydet();
                cout << "Programdan cikiliyor..." << endl;
                break;
            default:
                cout << "Gecersiz secim. Tekrar deneyin." << endl;
        }
    } while (secim != '6');

    return 0;
}
