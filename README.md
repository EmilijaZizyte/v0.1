#include <iostream>
#include <iomanip>
#include <vector>
#include <string>

using std::cout;
using std::cin;
using std::endl;
using std::string;
using std::vector;

struct Studentas {
    string vard;
    string pav;
    vector<int> paz;
    int egzas;
    double rez;
};

Studentas ivesk();

int main() {
    vector<Studentas> Grupe;
    int kiek;

    cout << "Kiek studentu norite ivesti? ";
    cin >> kiek;

    // --- MELYNA: įterptas ciklas į Grupe ---
    for (int j = 0; j < kiek; j++) {
        cout << "\nIveskite " << j+1 << " studenta:\n";
        Grupe.push_back(ivesk());
    }

    cout << "\nRezultatai:\n";

    // --- MELYNA: range-based for pakeistas į seną C++03 ciklą ---
    for (size_t i = 0; i < Grupe.size(); i++) {
        cout << Grupe[i].vard << " | "
             << Grupe[i].pav << " | "
             << std::fixed << std::setprecision(2)
             << Grupe[i].rez << endl;
    }

    return 0;
}

Studentas ivesk() {
    Studentas Laik;
    int sum = 0, n, m;

    cout << "Iveskite varda: ";
    cin >> Laik.vard;

    cout << "Iveskite pavarde: ";
    cin >> Laik.pav;

    cout << "Iveskite pazymiu skaiciu: ";
    cin >> n;

    for (int i = 0; i < n; i++) {
        cout << "Iveskite " << i+1 << " pazymi: ";
        cin >> m;
        Laik.paz.push_back(m);
        sum += m;
    }

    cout << "Iveskite egzamino rezultata: ";
    cin >> Laik.egzas;

    Laik.rez = Laik.egzas * 0.6 + (double)sum / Laik.paz.size() * 0.4;

    return Laik;
}
