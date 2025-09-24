#include <iostream>
#include <iomanip>
#include <vector>
#include <string>
#include <algorithm>
#include <sstream>
#include <fstream>
#include <cstdlib>
#include <ctime>

using namespace std;

struct Studentas {
    string vard;
    string pav;
    vector<int> paz;
    int egzas;
    double rezVid;
    double rezMed;
};

// Funkcijų deklaracijos
double mediana(const vector<int>& v);
void skaiciuokRezultatus(Studentas& s);
void nuskaitykIsFailo(vector<Studentas>& Grupe, const string& failoVardas);
void spausdinkRezultatus(const vector<Studentas>& Grupe, int rez_pasirinkimas, const string& failoVardas);

int main() {
    vector<Studentas> Grupe;
    string failoVardas;

    // 1. Paprastas įvedimas: prašome nurodyti failo kelią
    cout << "Iveskite failo kelia su studentu duomenimis: ";
    cin >> failoVardas;

    // 2. Nuskaitymas iš failo
    nuskaitykIsFailo(Grupe, failoVardas);

    if (Grupe.empty()) {
        cout << "Nepavyko nuskaityti studentu arba failas tuscias.\n";
        return 0;
    }

    // 3. Galutinio balo pasirinkimas
    int rez_pasirinkimas;
    while (true) {
        cout << "\nKaip skaiciuoti galutini bala?\n";
        cout << "1 - pagal vidurki\n";
        cout << "2 - pagal mediana\n";
        cout << "3 - abu\n";
        cout << "Jusu pasirinkimas: ";
        cin >> rez_pasirinkimas;
        if (!cin.fail() && rez_pasirinkimas >= 1 && rez_pasirinkimas <= 3) break;
        cin.clear(); cin.ignore(10000, '\n');
        cout << "Netinkamas pasirinkimas! Bandykite dar karta.\n";
    }

    // 4. Nuspręskime, į kokį failą rašyti rezultatus
    string failoRezultatai;
    cout << "Iveskite failo pavadinima, i kuri issaugoti rezultatus: ";
    cin >> failoRezultatai;

    // 5. Spausdiname rezultatus į failą
    spausdinkRezultatus(Grupe, rez_pasirinkimas, failoRezultatai);

    return 0;
}

// ---------- Funkcijų realizacijos ----------

double mediana(const vector<int>& v) {
    vector<int> temp = v;
    sort(temp.begin(), temp.end());
    int n = (int)temp.size();
    if (n % 2 == 0) return (temp[n / 2 - 1] + temp[n / 2]) / 2.0;
    else return temp[n / 2];
}

void skaiciuokRezultatus(Studentas& s) {
    if (!s.paz.empty()) {
        double suma = 0;
        for (int p : s.paz) suma += p;
        s.rezVid = s.egzas * 0.6 + (suma / s.paz.size()) * 0.4;
        s.rezMed = s.egzas * 0.6 + mediana(s.paz) * 0.4;
    }
    else {
        s.rezVid = s.rezMed = s.egzas * 0.6;
    }
}

void nuskaitykIsFailo(vector<Studentas>& Grupe, const string& failoVardas) {
    ifstream in(failoVardas);
    if (!in) {
        cout << "Nepavyko atidaryti failo: " << failoVardas << endl;
        return;
    }

    string eilute;
    getline(in, eilute); // praleidžiame antraštę

    while (getline(in, eilute)) {
        if (eilute.empty()) continue;

        stringstream ss(eilute);
        Studentas s;
        if (!(ss >> s.vard >> s.pav)) {
            cout << "Neapskaiciuota: truksta vardas/pavarde -> " << eilute << "\n";
            continue;
        }

        vector<int> laik;
        int sk;
        while (ss >> sk) laik.push_back(sk);

        if (laik.empty()) {
            cout << "Neapskaiciuota: studentas " << s.vard << " " << s.pav << " neturi nei vieno skaiciaus.\n";
            continue;
        }

        s.egzas = laik.back(); laik.pop_back();
        s.paz = laik;

        skaiciuokRezultatus(s);
        Grupe.push_back(s);
    }

    cout << "Failas nuskaitytas: " << Grupe.size() << " studentu.\n";
}

void spausdinkRezultatus(const vector<Studentas>& Grupe, int rez_pasirinkimas, const string& failoVardas) {
    ofstream out(failoVardas);
    if (!out) {
        cout << "Nepavyko sukurti failo: " << failoVardas << endl;
        return;
    }

    out << "-------------------------------------------------------------\n";
    out << left << setw(15) << "Vardas" << left << setw(15) << "Pavarde";
    if (rez_pasirinkimas == 1) out << right << setw(15) << "Galutinis (Vid.)\n";
    else if (rez_pasirinkimas == 2) out << right << setw(15) << "Galutinis (Med.)\n";
    else out << right << setw(15) << "Galutinis (Vid.)" << right << setw(15) << "Galutinis (Med.)\n";
    out << "-------------------------------------------------------------\n";

    for (const auto& s : Grupe) {
        out << left << setw(15) << s.vard << left << setw(15) << s.pav;
        if (rez_pasirinkimas == 1)
            out << right << setw(15) << fixed << setprecision(2) << s.rezVid << endl;
        else if (rez_pasirinkimas == 2)
            out << right << setw(15) << fixed << setprecision(2) << s.rezMed << endl;
        else
            out << right << setw(15) << fixed << setprecision(2) << s.rezVid
            << right << setw(15) << fixed << setprecision(2) << s.rezMed << endl;
    }

    cout << "Rezultatai issaugoti faile: " << failoVardas << endl;
}
