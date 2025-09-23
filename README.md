#include <iostream>
#include <iomanip>
#include <vector>
#include <string>
#include <algorithm>
#include <cctype>
#include <sstream>
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

Studentas ivesk();
double mediana(const vector<int>& v);

int main() {
    srand(time(0));
    vector<Studentas> Grupe;

    while (true) {
        cout << "\n Pasirinkite \n";
        cout << "1 - Prideti viena studenta\n";
        cout << "2 - Rodyti studentu rezultatus\n";
        cout << "3 - Baigti programa\n";
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
                cin.clear(); cin.ignore(10000,'\n');
                cout << "Netinkamas pasirinkimas! Iveskite 1 arba 2.\n";
            }
            cin.ignore();

            Studentas Laik;
            if (metode == 2) {
                int vardSkaicius = rand() % 100;
                int pavSkaicius = rand() % 100;
                Laik.vard = "vardas" + to_string(vardSkaicius);
                Laik.pav = "pavarde" + to_string(pavSkaicius);

                int nd_kiek = 5 + rand() % 6; // 5-10 pazymiu
                for (int i = 0; i < nd_kiek; i++)
                    Laik.paz.push_back(rand() % 11);

                Laik.egzas = rand() % 11;
            } else {
                Laik = ivesk();
            }

            // Skaiciuojame rezultatus
            if (!Laik.paz.empty()) {
                double suma = 0;
                for (size_t i = 0; i < Laik.paz.size(); i++) suma += Laik.paz[i];
                Laik.rezVid = Laik.egzas * 0.6 + (suma / (double)Laik.paz.size()) * 0.4;
                Laik.rezMed = Laik.egzas * 0.6 + mediana(Laik.paz) * 0.4;
            } else {
                Laik.rezVid = Laik.rezMed = -1;
            }

            Grupe.push_back(Laik);

        } else if (pasirinkimas == 2) {
            if (Grupe.empty()) {
                cout << "Sarasas tuscias.\n";
                continue;
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
                cin.clear(); cin.ignore(10000,'\n');
                cout << "Netinkamas pasirinkimas! Bandykite dar karta.\n";
            }

            cout << "-------------------------------------------------------------\n";
            cout << left << setw(15) << "Vardas" << left << setw(15) << "Pavarde";
            if (rez_pasirinkimas == 1) cout << right << setw(15) << "Galutinis (Vid.)\n";
            else if (rez_pasirinkimas == 2) cout << right << setw(15) << "Galutinis (Med.)\n";
            else cout << right << setw(15) << "Galutinis (Vid.)" << right << setw(15) << "Galutinis (Med.)\n";
            cout << "-------------------------------------------------------------\n";

            for (auto &s : Grupe) {
                cout << left << setw(15) << s.vard << left << setw(15) << s.pav;
                if (rez_pasirinkimas == 1) cout << right << setw(15) << fixed << setprecision(2) << s.rezVid << endl;
                else if (rez_pasirinkimas == 2) cout << right << setw(15) << fixed << setprecision(2) << s.rezMed << endl;
                else cout << right << setw(15) << fixed << setprecision(2) << s.rezVid
                          << right << setw(15) << fixed << setprecision(2) << s.rezMed << endl;
            }

        } else if (pasirinkimas == 3) {
            cout << "Programa baigta.\n";
            break;
        } else {
            cout << "Netinkamas pasirinkimas.\n";
        }
    }

    return 0;
}

Studentas ivesk() {
    Studentas Laik;
    string input;
    int m;

    while (true) {
        cout << "Iveskite varda: ";
        cin >> Laik.vard;
        bool valid = all_of(Laik.vard.begin(), Laik.vard.end(), ::isalpha);
        if (valid) break;
        cout << "Vardas gali tureti tik raides! Bandykite dar karta.\n";
    }

    while (true) {
        cout << "Iveskite pavarde: ";
        cin >> Laik.pav;
        bool valid = all_of(Laik.pav.begin(), Laik.pav.end(), ::isalpha);
        if (valid) break;
        cout << "Pavarde gali tureti tik raides! Bandykite dar karta.\n";
    }

    cout << "Iveskite namu darbu pazymius (vienas per eilute). Baigti tuscia eilute:\n";
    cin.ignore();
    while (true) {
        getline(cin, input);
        if (input.empty()) break;
        stringstream ss(input);
        if (ss >> m && m >= 0 && m <= 10) Laik.paz.push_back(m);
        else cout << "Bloga ivestis! Iveskite skaiciu 0-10 arba tuscia eilute pabaigai.\n";
    }

    while (true) {
        cout << "Iveskite egzamina: ";
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
    int n = temp.size();
    if (n % 2 == 0) return (temp[n/2-1] + temp[n/2]) / 2.0;
    else return temp[n/2];
}
