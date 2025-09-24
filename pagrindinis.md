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
double mediana(const vector<int>& v); /////////////
void skaiciuokRezultatus(Studentas& s);/////////////
void nuskaitykIsFailo(vector<Studentas>& Grupe);////////////

int main() {
	srand((unsigned)time(0)); //paima laika sekundemeis konvertuoja i tinkama tipa, o strand nustato atsitiktiniu skaiciu generavimo pradzia
	vector<Studentas> Grupe; //mano struktura studentas laikanti informacija apie studentus ir juos saugo pavadinimu Grupe 

    while (true) {
        cout << "\nPasirinkite:\n";
        cout << "1 - Prideti viena studenta\n";
        cout << "2 - Rodyti studentu rezultatus\n";
        cout << "3 - Baigti programa\n";
        cout << "4 - Nuskaityti studentus is failo\n";
        cout << "Jusu pasirinkimas: ";

        int pasirinkimas;
        cin >> pasirinkimas;
        if (cin.fail()) {
            cin.clear();
            cin.ignore(10000, '\n'); //isvalo klaviaturos ivedimo eilutes likucius, kad kitas ivedimas veiktu tvarkingai
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
			cin.ignore(); // nurodo isvalyti ivedima nuo likusiu simboliu

			Studentas Laik; //kuriamas laikinas Studentas tipo kintamasis Laik
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

			skaiciuokRezultatus(Laik); ///sugenertuotus rezultatus ar ivestus nusiuncia i funkcija skaiciuokRezultatus
			Grupe.push_back(Laik);//sugeneruotus rezultatus prideda i grupe

        }
        else if (pasirinkimas == 2) {
            if (Grupe.empty()) {
                cout << "Sarasas tuscias.\n";
                continue;
            }
            sort(Grupe.begin(), Grupe.end(), [](const Studentas& a, const Studentas& b) {////////
                return a.vard < b.vard;
                });

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
				Studentas& s = Grupe[i]; //trumpinimas, kad nereiktu visada rasyti Grupe[i], galima pasiekti grupeje Studentas i-tojo elemento reiksmes
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
        else {
            cout << "Netinkamas pasirinkimas.\n";
        }
    }

    return 0;
}

Studentas ivesk() { //deklaruoja funkcija ivesk, kuri grazina Studentas tipo reiksme
	Studentas Laik; //kuriamas laikinas Studentas tipo kintamasis Laik
	string input; // reiksme laikanti eilute

    while (true) {
        cout << "Iveskite varda: ";
        cin >> Laik.vard;
        bool valid = true;
		for (size_t i = 0; i < Laik.vard.size(); i++) //size_t visada teigiamas reiksmes
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

    cout << "Iveskite namu darbu pazymius (vienas per eilute). Baigti tuscia eilute (spauskite enter):\n";
    cin.ignore(); // isvalome buferi nuo likusiu simboliu
    while (true) {
        getline(cin, input); // nuskaityti visą eilutę
        if (input.empty()) break; // jei tuscia eilutė, baigti įvedimą

		stringstream ss(input);//stringstream leidzia apdoroti eilute kaip srauta jei rasyciau tik cin >> m, tai butu blogai, nes jei ivestis bloga, pvz "5 abc", tai cin uzstrigtu
        int m;
        string extra; // papildomas kintamasis, skirtas patikrinti, ar po skaiciaus yra daugiau simboliu
		// bandoma nuskaityti sveika skaiciu is srauto ir tikrinama ar po jo nera daugiau simboliu

		if (ss >> m && !(ss >> extra) && m >= 0 && m <= 10) { //ss >> extra tikrina ar po pirmo skaiciaus 
            // m sėkmingai nuskaitytas, ir po jo daugiau simbolių nėra
            Laik.paz.push_back(m);
        }
        else {
            cout << "Bloga ivestis! Iveskite skaiciu 0-10 arba tuscia eilute pabaigai\n";
        }
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
	vector<int> temp = v;//temp laikina vektoriaus kopija, kad nesugadinti pradinio vektoriaus
	sort(temp.begin(), temp.end());// surusiuoja temp vektoriu
	int n = (int)temp.size();// vektoriaus dydis int tipas
    if (n % 2 == 0) return (temp[n / 2 - 1] + temp[n / 2]) / 2.0;
    else return temp[n / 2];
}

void skaiciuokRezultatus(Studentas& s) {// priima Studentas tipo kintamaji s kaip nuoroda, kad nereiktu kopijuoti viso struktūros
	// jei yra namu darbu, skaiciuojame galutini pagal vidurki
    if (!s.paz.empty()) {
        double suma = 0;
        for (size_t i = 0; i < s.paz.size(); i++) suma += s.paz[i];
        s.rezVid = s.egzas * 0.6 + (suma / (double)s.paz.size()) * 0.4;
        s.rezMed = s.egzas * 0.6 + mediana(s.paz) * 0.4;
    }
    else {
        // jei namų darbų nėra, galutinis = egzaminas
        s.rezVid = s.rezMed = s.egzas*0.6;
    }
}

void nuskaitykIsFailo(vector<Studentas>& Grupe) {
	string failoVardas; // sring tipo kintamasis failoVardas
    cout << "Iveskite failo kelia: ";
    cin >> failoVardas;

	ifstream in(failoVardas.c_str()); //skirta atidaryti duomenų failą skaitymui // c_str() konvertuoja string i const char* //negalima kazkaip keisti, nes failo vardas turi buti konstantos tipo
    if (!in) {
        cout << "Nepavyko atidaryti failo\n";
        return;
    }

	string eilute; //string tipo kintamasis eilute
	getline(in, eilute); //pirmoji eilute yra antraste, jos nuskaitom, bet niekur neissaugom

	while (getline(in, eilute)) { // nuskaito po viena eilute is failo, get line grizta false, kai pasiekia failo pabaiga, in yra ifstream tipas
        if (eilute.empty()) continue;

		stringstream ss(eilute); //stringstream leidzia apdoroti eilute kaip srauta
		Studentas s; //kuriamas laikinas Studentas tipo kintamasis s
        if (!(ss >> s.vard >> s.pav)) { 
            cout << "Neapskaiciuota: truksta vardas/pavarde -> " << eilute << "\n";
            continue;
        }

		vector<int> laik; //laikinas vektorius laik, skirtas saugoti pazymius
        int sk;
        while (ss >> sk) laik.push_back(sk);

        if (laik.empty()) {
            cout << "Neapskaiciuota: studentas " << s.vard << " " << s.pav << " neturi nei vieno skaiciaus.\n";
            continue;
        }

		s.egzas = laik.back(); // paskutinis skaicius yra egzaminas
		laik.pop_back();// pasalina paskutini elementa is laik vektoriaus
		s.paz = laik;// likusieji skaiciai yra namu darbu pazymiai

        skaiciuokRezultatus(s);
		Grupe.push_back(s);// prideda i grupe
    }

    cout << "Failas nuskaitytas";
}

