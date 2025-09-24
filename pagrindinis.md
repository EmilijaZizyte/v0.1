#include <iostream>
#include <iomanip>
#include <vector>
#include <string>
#include <algorithm>
#include <cctype>
#include <sstream>
#include <cstdlib>
#include <ctime>
#include <fstream>
#include <limits>

using namespace std;

struct Studentas {
    string vard;
    string pav;
    vector<int> paz;
    int egzas;
    double rezVid;
    double rezMed;
};

Studentas ivesk();
double mediana(const vector<int>& v);
void skaiciuokRezultatus(Studentas& s);
void nuskaitykIsFailo(vector<Studentas>& Grupe);
void apdorokDideliFaila(const string& failoVardas, vector<Studentas>& Grupe);

int main() {
    srand((unsigned)time(0));
    vector<Studentas> Grupe;

    while (true) {
        cout << "\nPasirinkite:\n";
        cout << "1 - Prideti viena studenta\n";
        cout << "2 - Rodyti studentu rezultatus\n";
        cout << "3 - Baigti programa\n";
        cout << "4 - Nuskaityti studentus is failo\n";
        cout << "5 - Nuskaityti labai dideli faila (pvz., 1 mln studentu)\n";
        cout << "6 - Parodyti kiek studentu neislaike egzamino\n";
        cout << "7 - Parodyti kiek studentu islaike egzamina\n";
        cout << "Jusu pasirinkimas: ";

        int pasirinkimas;
        cin >> pasirinkimas;
        if (cin.fail()) {
            cin.clear();
            cin.ignore(10000, '\n');
            cout << "Bandykite dar karta\n";
            continue;
        }
        cin.ignore();

        if (pasirinkimas == 1) {
            int metode;
            while (true) {
                cout << "Ar norite ivesti ranka (1) ar generuoti atsitiktinai (2)? ";
                cin >> metode;
                if (!cin.fail() && (metode == 1 || metode == 2)) break;
                cin.clear(); cin.ignore(10000, '\n');
                cout << "Netinkamas pasirinkimas! Iveskite 1 arba 2\n";
            }
            cin.ignore();

            Studentas Laik;
            if (metode == 2) {
                int vardSkaicius = rand() % 100;
                int pavSkaicius = rand() % 100;
                Laik.vard = "vardas" + to_string(vardSkaicius);
                Laik.pav = "pavarde" + to_string(pavSkaicius);

                int nd_kiek = 5 + rand() % 6;
                for (int i = 0; i < nd_kiek; i++)
                    Laik.paz.push_back(rand() % 11);

                Laik.egzas = rand() % 11;
            }
            else {
                Laik = ivesk();
            }

            skaiciuokRezultatus(Laik);
            Grupe.push_back(Laik);

        }
        else if (pasirinkimas == 2) {
            if (Grupe.empty()) {
                cout << "Sarasas tuscias.\n";
                continue;
            }
            int rikiuot;
            cout << "\nAr rikiuoti studentus?\n";
            cout << "1 - taip\n";
            cout << "2 - ne\n";
            cin >> rikiuot;
            if (rikiuot == 1) {
                sort(Grupe.begin(), Grupe.end(),
                    [](const Studentas& a, const Studentas& b) {
                        return a.vard < b.vard;
                    });
            }

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

            cout << "-------------------------------------------------------------\n";
            cout << left << setw(15) << "Vardas" << left << setw(15) << "Pavarde";
            if (rez_pasirinkimas == 1) cout << right << setw(15) << "Galutinis (Vid.)\n";
            else if (rez_pasirinkimas == 2) cout << right << setw(15) << "Galutinis (Med.)\n";
            else cout << right << setw(15) << "Galutinis (Vid.)" << right << setw(15) << "Galutinis (Med.)\n";
            cout << "-------------------------------------------------------------\n";

            for (size_t i = 0; i < Grupe.size(); i++) {
                Studentas& s = Grupe[i];
                cout << left << setw(15) << s.vard << left << setw(15) << s.pav;
                if (rez_pasirinkimas == 1)
                    cout << right << setw(15) << fixed << setprecision(2) << s.rezVid << endl;
                else if (rez_pasirinkimas == 2)
                    cout << right << setw(15) << fixed << setprecision(2) << s.rezMed << endl;
                else
                    cout << right << setw(15) << fixed << setprecision(2) << s.rezVid
                    << right << setw(15) << fixed << setprecision(2) << s.rezMed << endl;
            }

        }
        else if (pasirinkimas == 3) {
            cout << "Programa baigta.\n";
            break;

        }
        else if (pasirinkimas == 4) {
            nuskaitykIsFailo(Grupe);

        }
        else if (pasirinkimas == 5) {
            string failoVardas;
            cout << "Iveskite didelio failo kelia: ";
            cin >> failoVardas;
            apdorokDideliFaila(failoVardas, Grupe);

        }
        else if (pasirinkimas == 6)
        {
            if (Grupe.empty()) {
                cout << "Sarasas tuscias\n";
                continue;
            }

            int kiekis = 0;
            for (size_t i = 0; i < Grupe.size(); i++)
            {
                if (Grupe[i].egzas < 5) kiekis++;
            }
            cout << "Studentu, neislaikiusiu egzamino: " << kiekis << "\n";
        }
        else if (pasirinkimas == 7)
        {
            if (Grupe.empty()) {
                cout << "Sarasas tuscias\n";
                continue;
            }

            int skaicius = 0;
            for (size_t i = 0; i < Grupe.size(); i++)
            {
                if (Grupe[i].egzas >= 5) skaicius++;
            }
            cout << "Studentu, islaikiusiu egzamina: " << skaicius << "\n";
        }
        else {
            cout << "Netinkamas pasirinkimas, bandykite dar karta\n";
        }
    }

    return 0;
}

Studentas ivesk() {
    Studentas Laik;
    string input;

    while (true) {
        cout << "Iveskite varda: ";
        cin >> Laik.vard;
        bool valid = true;
        for (size_t i = 0; i < Laik.vard.size(); i++)
            if (!isalpha(Laik.vard[i])) {
                valid = false;
                break;
            }
        if (valid) break;
        cout << "Vardas gali tureti tik raides. Bandykite dar karta.\n";
    }

    while (true) {
        cout << "Iveskite pavarde: ";
        cin >> Laik.pav;
        bool valid = true;
        for (size_t i = 0; i < Laik.pav.size(); i++)
            if (!isalpha(Laik.pav[i])) {
                valid = false;
                break;
            }
        if (valid) break;
        cout << "Pavarde gali tureti tik raides. Bandykite dar karta\n";
    }

    cout << "Iveskite namu darbu pazymius atskirdami tarpais. Baigti tuscia eilute (spauskite enter du kartus):\n";
    cin.ignore(); //jei nebus, programa paims jau kas yra buve cin
    getline(cin, input); //nuskaito visos eilutes ivesti, cin 

    stringstream ss(input); //stringstream duoda galimybe skaityti is string kaip is failo
    int m;

    while (ss >> m) { //kol pavyksta nuskaityti skaiciu
        Laik.paz.push_back(m); //ideda i pazymiu vektoriu
    }

    while (true) {
        cout << "Iveskite egzamina desimtbaleje sistemoje: ";
        cin >> Laik.egzas;
        if (!cin.fail() && Laik.egzas >= 0 && Laik.egzas <= 10) break;
        cin.clear(); cin.ignore(10000, '\n');
        cout << "Egzamino rezultatas turi buti 0-10! Bandykite dar karta.\n";
    }

    return Laik;
}

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
        for (size_t i = 0; i < s.paz.size(); i++) suma += s.paz[i];
        s.rezVid = s.egzas * 0.6 + (suma / (double)s.paz.size()) * 0.4;
        s.rezMed = s.egzas * 0.6 + mediana(s.paz) * 0.4;
    }
    else {
        s.rezVid = s.rezMed = s.egzas * 0.6;
    }
}

void nuskaitykIsFailo(vector<Studentas>& Grupe) {
    string failoVardas;
    cout << "Iveskite failo kelia: ";
    cin >> failoVardas;

    ifstream in(failoVardas.c_str());
    if (!in) {
        cout << "Nepavyko atidaryti failo\n";
        return;
    }

    string eilute;
    getline(in, eilute);

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

        s.egzas = laik.back();
        laik.pop_back();
        s.paz = laik;

        skaiciuokRezultatus(s);
        Grupe.push_back(s);
    }

    cout << "Failas nuskaitytas\n";
}

void apdorokDideliFaila(const string& failoVardas, vector<Studentas>& Grupe) {
    ifstream in(failoVardas);
    if (!in) {
        cout << "Nepavyko atidaryti failo!\n";
        return;
    }

    Grupe.clear();
    Grupe.reserve(1'000'000);

    string vard, pav, eilute;
    int sk;

    getline(in, eilute);

    while (in >> vard >> pav) {
        Studentas s;
        s.vard = vard;
        s.pav = pav;

        vector<int> laik;
        while (in.peek() != '\n' && in >> sk) {
            laik.push_back(sk);
        }
        in.ignore(numeric_limits<streamsize>::max(), '\n');

        if (laik.empty()) continue;

        s.egzas = laik.back();
        laik.pop_back();
        s.paz = std::move(laik);

        skaiciuokRezultatus(s);
        Grupe.emplace_back(std::move(s));
    }

    cout << "Nuskaityta studentu: " << Grupe.size() << "\n";
}
