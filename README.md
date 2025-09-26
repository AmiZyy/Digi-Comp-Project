#include <iostream>
#include <vector>
#include <string>
#include <cstdlib>
#include <ctime>
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
