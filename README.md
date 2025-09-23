#include <iostream>
#include <iomanip>
#include <vector>
#include <string>
#include <algorithm>
#include <cctype>
#include <sstream> 

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
    vector<Studentas> Grupe;
    int kiek;

    
    while (1) {
        cout << "Kiek studentu norite ivesti? ";
        cin >> kiek;
        if (cin.fail() || kiek <= 0) {
            cin.clear();
            cin.ignore(10000, '\n');
            cout << "Iveskite teigiama skaiciu!\n";
        } else break;
    }
    cin.ignore(); 

    for (int j = 0; j < kiek; j++) {
        cout << "\nIveskite " << j+1 << " studenta:\n";
        Grupe.push_back(ivesk());
    }

    
    int rez_pasirinkimas;
    while (1) {
        cout << "\nKaip skaiciuoti galutini bala?\n";
        cout << "1 - pagal vidurki\n";
        cout << "2 - pagal mediana\n";
        cout << "3 - abu\n";
        cout << "Jusu pasirinkimas: ";
        cin >> rez_pasirinkimas;
        if (cin.fail()) { cin.clear(); cin.ignore(10000,'\n'); continue; }
        if (rez_pasirinkimas >= 1 && rez_pasirinkimas <= 3) break;
        cout << "Netinkamas pasirinkimas! Bandykite dar karta.\n";
    }

    
    cout << "-------------------------------------------------------------\n";
    cout << left << setw(15) << "Vardas"
         << left << setw(15) << "Pavarde";

    if (rez_pasirinkimas == 1)
        cout << right << setw(15) << "Galutinis (Vid.)\n";
    else if (rez_pasirinkimas == 2)
        cout << right << setw(15) << "Galutinis (Med.)\n";
    else
        cout << right << setw(15) << "Galutinis (Vid.)"
             << right << setw(15) << "Galutinis (Med.)\n";

    cout << "-------------------------------------------------------------\n";

    for (int i = 0; i < (int)Grupe.size(); i++) {
        cout << left << setw(15) << Grupe[i].vard
             << left << setw(15) << Grupe[i].pav;

        if (rez_pasirinkimas == 1)
            cout << right << setw(15) << fixed << setprecision(2) << Grupe[i].rezVid << endl;
        else if (rez_pasirinkimas == 2)
            cout << right << setw(15) << fixed << setprecision(2) << Grupe[i].rezMed << endl;
        else
            cout << right << setw(15) << fixed << setprecision(2) << Grupe[i].rezVid
                 << right << setw(15) << fixed << setprecision(2) << Grupe[i].rezMed << endl;
    }

    return 0;
}


Studentas ivesk() {
    Studentas Laik;
    string input;
    int m;

    
    while (1) {
        cout << "Iveskite varda: ";
        cin >> Laik.vard;
        bool valid = true;
        for (size_t i=0;i<Laik.vard.size();i++) if (!isalpha(Laik.vard[i])) valid=false;
        if (valid) break;
        cout << "Vardas gali tureti tik raides!\n";
    }

    
    while (1) {
        cout << "Iveskite pavarde: ";
        cin >> Laik.pav;
        bool valid = true;
        for (size_t i=0;i<Laik.pav.size();i++) if (!isalpha(Laik.pav[i])) valid=false;
        if (valid) break;
        cout << "Pavarde gali tureti tik raides!\n";
    }

    
    cin.ignore(); 
    cout << "Iveskite namu darbu pazymius (vienas per eilute). Baigti tuscia eilute:\n";
    while (1) {
        getline(cin, input);
        if (input.empty()) break; 
        stringstream ss(input);
        if (ss >> m && m >= 0 && m<=10 ) {
            Laik.paz.push_back(m);
        } else {
            cout << "Bloga ivestis, iveskitedesimtabaleje sitemoje esanti skaiciu arba tuscia eilute pabaigai.\n";
        }
    }

    
    while (1) {
        cout << "Iveskite egzamina: ";
        cin >> Laik.egzas;
        if (!cin.fail() && Laik.egzas >= 0 && Laik.egzas <= 10) break;
        cin.clear(); cin.ignore(10000,'\n');
        cout << "Egzamino rezultatas turi buti desimtbaleje sistemoje!\n";
    }

    
    if (!Laik.paz.empty()) {
        double suma = 0;
        for (size_t i=0;i<Laik.paz.size();i++) suma += Laik.paz[i];
        Laik.rezVid = Laik.egzas*0.6 + (suma/Laik.paz.size())*0.4;
    } else Laik.rezVid = -1;

    if (!Laik.paz.empty())
        Laik.rezMed = Laik.egzas*0.6 + mediana(Laik.paz)*0.4;
    else Laik.rezMed = -1;

    return Laik;
}


double mediana(const vector<int>& v) {
    vector<int> temp = v;
    sort(temp.begin(), temp.end());
    int n = temp.size();
    if (n % 2 == 0)
        return (temp[n/2 - 1] + temp[n/2]) / 2.0;
    else
        return temp[n/2];
}
