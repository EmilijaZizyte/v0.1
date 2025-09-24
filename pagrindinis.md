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


double mediana(const vector<int>& v);
void skaiciuokRezultatus(Studentas& s);
void nuskaitykIsFailo(vector<Studentas>& Grupe, const string& failoVardas);
void spausdinkRezultatus(const vector<Studentas>& Grupe, int rez_pasirinkimas, const string& failoVardas);

int main() {
    vector<Studentas> Grupe;
    string failoVardas;

    cout << "Iveskite failo kelia su studentu duomenimis: ";
    cin >> failoVardas;

	nuskaitykIsFailo(Grupe, failoVardas); //nuoroda i funkcija skaityti is failo, kuri priskiria reiksmes i Grupe

    if (Grupe.empty()) {
        cout << "Nepavyko nuskaityti studentu arba failas tuscias.\n";
        return 0;
    }

    
    int rez_pasirinkimas;
    while (true) {
        cout << "\nKaip skaiciuoti galutini bala?\n";
        cout << "1 - pagal vidurki\n";
        cout << "2 - pagal mediana\n";
        cout << "3 - abu\n";
        cout << "Jusu pasirinkimas: ";
        cin >> rez_pasirinkimas;
		if (!cin.fail() && rez_pasirinkimas >= 1 && rez_pasirinkimas <= 3) break; //cin.fail() - patikrina ar ivestis yra tinkama, tai yra, ar ivestis yra skaicius
		cin.clear(); cin.ignore(10000, '\n'); //cin.clear() - isvalo klaidos busena, cin.ignore() - ignoruoja netinkama ivesti
        cout << "Netinkamas pasirinkimas! Bandykite dar karta.\n";
    }

    
	string failoRezultatai; //striga, i kuri saugosime rezultatus
    cout << "Iveskite failo pavadinima, i kuri issaugoti rezultatus: ";
    cin >> failoRezultatai;

    
	spausdinkRezultatus(Grupe, rez_pasirinkimas, failoRezultatai); //nuoroda i funkcija spausdinti rezultatus i faila, grupe, pasirinkimas, failo pavadinimas

    return 0;
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
        for (int p : s.paz) suma += p;
        s.rezVid = s.egzas * 0.6 + (suma / s.paz.size()) * 0.4;
        s.rezMed = s.egzas * 0.6 + mediana(s.paz) * 0.4;
    }
    else {
        s.rezVid = s.rezMed = s.egzas * 0.6;
    }
}

void nuskaitykIsFailo(vector<Studentas>& Grupe, const string& failoVardas) { //vectorius Grupe perduodamas kaip nuoroda, kad butu galima keisti originalu vektoriu, const string& failoVardas duodamas kaip nuoroda, kad nereiketu kopijuoti stringo
    ifstream in(failoVardas);
    if (!in) {
        cout << "Nepavyko atidaryti failo: " << failoVardas << endl;
        return;
    }

    string eilute;
	getline(in, eilute); // in reiskia failo srauta, o jis reiskia pirma eilute (antraste), eilute kintamasis duomenu

    while (getline(in, eilute)) {
        if (eilute.empty()) continue;

		stringstream ss(eilute); //stringstream - leidzia skaityti eilutes kaip is failo
		Studentas s; //s kuris duoda nauja studento struktura
		if (!(ss >> s.vard >> s.pav)) { //ss>s vard ir pav - jei nepavyksta nuskaityti vardo ir pavardes
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

		s.egzas = laik.back(); laik.pop_back(); //paskutinis skaicius yra egzaminas, ji issaugome ir pasaliname is laik
        s.paz = laik;

        skaiciuokRezultatus(s);
        Grupe.push_back(s);
    }

    cout << "Failas nuskaitytas: " << Grupe.size() << " studentu.\n";
}

void spausdinkRezultatus(const vector<Studentas>& Grupe, int rez_pasirinkimas, const string& failoVardas) { //const vector<Studentas>& Grupe - perduodame kaip nuoroda, kad nereiketu kopijuoti vektoriaus, bet negalime keisti originalaus vektoriaus
	ofstream out(failoVardas);//ofstream - failo srautas, skirtas rasymui i faila
    if (!out) {
        cout << "Nepavyko sukurti failo: " << failoVardas << endl;
        return;
    }
	vector<Studentas> surusiuotaGrupe = Grupe; //sukurti kopija, kad galetume rusiuoti
	sort(surusiuotaGrupe.begin(), surusiuotaGrupe.end(), [](const Studentas& a, const Studentas& b) { //surusiuojam naudojam lambda funkcija [](const Studentas& a, const Studentas& b) - anonimine funkcija, kuri priima du studentus ir grazina bool reiksme
		if (a.vard == b.vard) return a.pav < b.pav; //jei vardai vienodi, rusiavimas pagal pavarde
        return a.vard < b.vard;
        });

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
