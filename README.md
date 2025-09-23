#include <iostream>
#include <iomanip>
#include <vector>
#include <string>
#include <algorithm>
#include <cctype>

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
        if (cin.fail()) {             
        cin.clear();               
        cin.ignore(10000,'\n');    
        cout << "Netinkamas pasirinkimas! Bandykite dar karta.\n";
        continue;
    }

        if (rez_pasirinkimas >= 1 && rez_pasirinkimas <= 3) break;
        cout << "Netinkamas pasirinkimas! Bandykite dar karta.\n";
    }

    // Graži lentelė
    cout << "-------------------------------------------------------------\n";
    cout << left << setw(15) << "Vardas"
         << left << setw(15) << "Pavarde";

    if (rez_pasirinkimas == 1)
        cout << right << setw(15) << "Galutinis (Vid.)\n";
    else if (rez_pasirinkimas == 2)
        cout << right << setw(15) << "Galutinis (Med.)\n";
    else if (rez_pasirinkimas == 3)
        cout << right << setw(15) << "Galutinis (Vid.)"
             << right << setw(15) << "Galutinis (Med.)\n";

    cout << "-------------------------------------------------------------\n";

    for (int i = 0; i < (int)Grupe.size(); i++) {
        cout << left << setw(15) << Grupe[i].vard
             << left << setw(15) << Grupe[i].pav;

        if (rez_pasirinkimas == 1)
            cout << right << setw(15)
                 << fixed << setprecision(2)
                 << Grupe[i].rezVid << endl;
        else if (rez_pasirinkimas == 2) {
            if (Grupe[i].rezMed >= 0)
                cout << right << setw(15) << fixed << setprecision(2) << Grupe[i].rezMed << endl;
            else
                cout << right << setw(15) << "Neapskaičiuota" << endl;
        } else if (rez_pasirinkimas == 3) {
            if (Grupe[i].rezVid >= 0)
                cout << right << setw(15) << fixed << setprecision(2) << Grupe[i].rezVid;
            else
                cout << right << setw(15) << "Neapskaičiuota";

            if (Grupe[i].rezMed >= 0)
                cout << right << setw(15) << fixed << setprecision(2) << Grupe[i].rezMed << endl;
            else
                cout << right << setw(15) << "Neapskaičiuota" << endl;
        }
    }

    return 0;
}

Studentas ivesk() {
    Studentas Laik;
    int n, m;

    
    while (1) {
        cout << "Iveskite varda: ";
        cin >> Laik.vard;
        bool valid = true;
        for (int i = 0; i < (int)Laik.vard.length(); i++)
            if (!isalpha(Laik.vard[i])) valid = false;
        if (valid) break;
        cout << "Vardas gali tureti tik raides!\n";
    }

    
    while (1) {
        cout << "Iveskite pavarde: ";
        cin >> Laik.pav;
        bool valid = true;
        for (int i = 0; i < (int)Laik.pav.length(); i++)
            if (!isalpha(Laik.pav[i])) valid = false;
        if (valid) break;
        cout << "Pavarde gali tureti tik raides!\n";
    }

    
    while (1) {
        cout << "Kiek pazymiu turi studentas: ";
        cin >> n;
        if (!cin.fail() && n >= 0) break;
        cin.clear(); cin.ignore(10000,'\n');
        cout << "Pazymiu skaicius turi buti neneigiamas!\n";
    }

    for (int i = 0; i < n; i++) {
        while (1) {
            cout << "Iveskite " << i+1 << " pazymi: ";
            cin >> m;
            if (!cin.fail() && m >= 0) { Laik.paz.push_back(m); break; }
            cin.clear(); cin.ignore(10000,'\n');
            cout << "Pazymys turi buti neneigiamas skaicius!\n";
        }
    }

    
    while (1) {
        cout << "Iveskite egzamina: ";
        cin >> Laik.egzas;
        if (!cin.fail() && Laik.egzas >= 0) break;
        cin.clear(); cin.ignore(10000,'\n');
        cout << "Egzamino rezultatas turi buti neneigiamas skaicius!\n";
    }

    
    if (n > 0) {
        double suma = 0;
        for (int i = 0; i < n; i++) suma += Laik.paz[i];
        Laik.rezVid = Laik.egzas*0.6 + (suma/(double)n)*0.4;
    } else {
        Laik.rezVid = -1;
    }

    
    if (n > 0)
        Laik.rezMed = Laik.egzas*0.6 + mediana(Laik.paz)*0.4;
    else
        Laik.rezMed = -1;

    return Laik;
}

double mediana(const vector<int>& v) {
    if (v.empty()) return 0;
    vector<int> temp = v;
    sort(temp.begin(), temp.end());
    int n = temp.size();
    if (n % 2 == 0)
        return (temp[n/2 - 1] + temp[n/2]) / 2.0;
    else
        return temp[n/2];
}
