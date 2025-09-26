#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <iomanip>
#include <ctime>
#include <cstdlib>
#include <thread>
#include <chrono>
#include <limits>

using namespace std;

struct Movie {
    string title;
    string genre;
    double rating;
    int year;
    string description;
    Movie(string t, string g, double r, int y, string d)
        : title(t), genre(g), rating(r), year(y), description(d) {}
};

vector<Movie> initializeDatabase() {
    return {
        {"Extraction", "Action", 6.7, 2020, "A mercenary is hired to rescue a kidnapped boy."},
        {"6 Underground", "Action", 6.1, 2019, "Six individuals fake their deaths to take down criminals."},
        {"Marriage Story", "Drama", 7.9, 2019, "A director and his wife go through a divorce."},
        {"Roma", "Drama", 7.7, 2018, "Life of a maid in 1970s Mexico City."},
        {"Murder Mystery", "Comedy", 6.0, 2019, "A couple gets framed for murder while on vacation."},
        {"Eurovision Song Contest", "Comedy", 6.5, 2020, "Two singers chase their pop star dreams."},
        {"Stranger Things", "Sci-Fi", 8.7, 2016, "A boy disappears, uncovering supernatural mysteries."},
        {"Dark", "Sci-Fi", 8.7, 2017, "A German town uncovers supernatural secrets."},
        {"Bird Box", "Horror", 6.6, 2018, "A mother and children struggle against unseen dangers."},
        {"My Octopus Teacher", "Documentary", 8.1, 2020, "A filmmaker bonds with an octopus in South Africa."}
    };
}

int getValidInput(int min, int max) {
    int choice;
    cin >> choice;
    if (cin.fail() || choice < min || choice > max) {
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
        return min;
    }
    return choice;
}
