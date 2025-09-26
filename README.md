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
    string subGenre
    double rating;
    int year;
    string description;
    string mood;
    Movie(string t, string g, double r, int y, string d)
        : title(t), genre(g), rating(r), year(y), description(d) {}
};

class NetflixMovieFinder {
private:
    vector<Movie> movieDatabase;
    vector<Movie> lastRecommendations;

public:
    NetflixMovieFinder() {
        initializeMovieDatabase();
        srand(time(0)); 
    }
       void initializeMovieDatabase() {
        movieDatabase = {
            // Action
            Movie("Extraction", "Action", "Thriller", 6.7, 2020, 116, "A mercenary is hired to rescue a kidnapped boy.", "Intense"),
            Movie("6 Underground", "Action", "Thriller", 6.1, 2019, 128, "Six individuals fake their deaths to take down criminals.", "Intense"),
            Movie("The Gray Man", "Action", "Spy", 6.5, 2022, 129, "A CIA operative uncovers agency secrets while being hunted.", "Intense"),
            Movie("Red Notice", "Action", "Comedy", 6.4, 2021, 118, "An Interpol agent hunts an art thief.", "Light"),
            // Drama
            Movie("Marriage Story", "Drama", "Romance", 7.9, 2019, 137, "A director and his wife go through a divorce.", "Emotional"),
            Movie("Roma", "Drama", "Foreign", 7.7, 2018, 135, "Life of a maid in 1970s Mexico City.", "Emotional"),
            Movie("The Two Popes", "Drama", "Biography", 7.6, 2019, 125, "Pope Benedict and Pope Francis find common ground.", "Thought-provoking"),
            Movie("Squid Game", "Drama", "Thriller", 8.0, 2021, 60, "Players compete in deadly survival games.", "Intense"),
            // Comedy
            Movie("Murder Mystery", "Comedy", "Mystery", 6.0, 2019, 97, "A couple gets framed for murder while on vacation.", "Light"),
            Movie("Eurovision Song Contest", "Comedy", "Musical", 6.5, 2020, 123, "Two singers chase their pop star dreams.", "Light"),
            Movie("Dolemite Is My Name", "Comedy", "Biography", 7.2, 2019, 118, "The story of comedy legend Rudy Ray Moore.", "Emotional"),
            Movie("The Ridiculous 6", "Comedy", "Western", 4.8, 2015, 119, "Six half-brothers go on a wild western journey.", "Light"),
            // Sci-Fi
            Movie("Stranger Things", "Sci-Fi", "Horror", 8.7, 2016, 51, "A boy disappears, uncovering supernatural mysteries.", "Intense"),
            Movie("Black Mirror: Bandersnatch", "Sci-Fi", "Psychological", 7.1, 2018, 90, "A programmer questions reality while making a game.", "Thought-provoking"),
            Movie("The Midnight Sky", "Sci-Fi", "Drama", 5.7, 2020, 118, "A scientist tries to warn astronauts after a catastrophe.", "Thought-provoking"),
            Movie("Dark", "Sci-Fi", "Mystery", 8.7, 2017, 60, "A German town uncovers supernatural secrets.", "Thought-provoking"),
            // Horror
            Movie("Bird Box", "Horror", "Post-Apocalyptic", 6.6, 2018, 124, "A mother and children struggle against unseen dangers.", "Intense"),
            Movie("His House", "Horror", "Supernatural", 6.5, 2020, 93, "A refugee couple faces evil forces in their new home.", "Emotional"),
            Movie("Fear Street Part One: 1994", "Horror", "Slasher", 6.2, 2021, 107, "Teens face an ancient evil haunting their town.", "Intense"),
            // Documentary
            Movie("My Octopus Teacher", "Documentary", "Nature", 8.1, 2020, 85, "A filmmaker bonds with an octopus in South Africa.", "Emotional"),
            Movie("The Social Dilemma", "Documentary", "Tech", 7.6, 2020, 94, "Examines the dangers of social media.", "Thought-provoking"),
            Movie("Fyre Festival", "Documentary", "Crime", 7.2, 2019, 97, "Behind the scenes of the failed Fyre festival.", "Thought-provoking")
        };
    }
void displayWelcome() {
        cout << "\n" << string(60, '=') << "\n";
        cout << "      NETFLIX-STYLE MOVIE RECOMMENDATION SYSTEM\n";
        cout << "      Inspired by Netflix's Innovation Journey\n";
        cout << string(60, '=') << "\n\n";
    }
    int getValidInput(int min, int max) {
        int choice;
        cin >> choice;
        if (cin.fail() || choice < min || choice > max) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid input! Defaulting to " << min << ".\n";
            return min;
        }
        return choice;
    }
string getGenrePreference() {
        cout << "SELECT YOUR PREFERRED GENRE:\n";
        cout << "1. Action\n2. Drama\n3. Comedy\n4. Sci-Fi\n5. Horror\n6. Documentary\n7. Surprise Me!\n";
        cout << "Enter your choice (1-7): ";
        int choice = getValidInput(1, 7);
        switch(choice) {
            case 1: return "Action";
            case 2: return "Drama";
            case 3: return "Comedy";
            case 4: return "Sci-Fi";
            case 5: return "Horror";
            case 6: return "Documentary";
            case 7: return "Random";
            default: return "Action";
        }
    }
    string getMoodPreference() {
        cout << "\nSELECT YOUR MOOD:\n";
        cout << "1. Intense\n2. Emotional\n3. Light\n4. Thoughtful\n5. Any\n";
        cout << "Enter your choice (1-5): ";
        int choice = getValidInput(1, 5);
        switch(choice) {
            case 1: return "Intense";
            case 2: return "Emotional";
            case 3: return "Light";
            case 4: return "Thought-provoking";
            case 5: return "Any";
            default: return "Any";
        }
    }
     int getDurationPreference() {
        cout << "\nHOW MUCH TIME DO YOU HAVE?\n";
        cout << "1. Quick (< 100 mins)\n2. Standard (100-130 mins)\n3. Long (130+ mins)\n4. Any\n";
        cout << "Enter your choice (1-4): ";
        int choice = getValidInput(1, 4);
        switch(choice) {
            case 1: return 100;
            case 2: return 130;
            case 3: return 200;
            default: return 0; 
        }
    }
vector<Movie> getRecommendations(string preferredGenre, string preferredMood, int maxDuration) {
        vector<pair<Movie, int>> scoredMovies;
        for (const auto& movie : movieDatabase) {
            int score = 0;
            if (preferredGenre == "Random" || movie.genre == preferredGenre) score += 2;
            if (preferredMood == "Any" || movie.mood == preferredMood) score += 2;
            if (maxDuration == 0 || movie.duration <= maxDuration) score += 1;
            if (score > 0) scoredMovies.push_back({movie, score});
        }
        sort(scoredMovies.begin(), scoredMovies.end(),
            [](const pair<Movie, int>& a, const pair<Movie, int>& b) {
                if (a.second == b.second) return a.first.rating > b.first.rating;
                return a.second > b.second;
            });
        lastRecommendations.clear();
        for (size_t i = 0; i < scoredMovies.size() && i < 5; i++) {
            lastRecommendations.push_back(scoredMovies[i].first);
        }
        return lastRecommendations;
    }
 void displayRecommendations(const vector<Movie>& recommendations) {
        cout << "\n" << string(70, '=') << "\n";
        cout << "     YOUR PERSONALIZED NETFLIX-STYLE RECOMMENDATIONS\n";
        cout << string(70, '=') << "\n\n";
        if (recommendations.empty()) {
            cout << "Sorry, no movies match your exact criteria.\n";
            return;
        }
        cout << left << setw(25) << "Title"
             << setw(12) << "Genre"
             << setw(8) << "Rating"
             << setw(8) << "Year"
             << setw(10) << "Mood"
             << "Description\n";
        cout << string(80, '-') << "\n";
        for (const auto& movie : recommendations) {
            cout << left << setw(25) << movie.title
                 << setw(12) << movie.genre
                 << setw(8) << movie.rating
                 << setw(8) << movie.year
                 << setw(10) << movie.mood
                 << movie.description << "\n";
        }
        cout << "\nNote: Inspired by Netflix's Cinematch recommendation algorithm.\n\n";
     void runMovieFinder() {
        displayWelcome();
        char continueSearch = 'y';
        while (continueSearch == 'y' || continueSearch == 'Y') {
            string preferredGenre = getGenrePreference();
            string preferredMood = getMoodPreference();
            int maxDuration = getDurationPreference();
            cout << "\nSearching for movies";
            for (int i = 0; i < 3; i++) {
                cout << "."; cout.flush();
                this_thread::sleep_for(chrono::milliseconds(500));
            }
            cout << " done!\n";
            vector<Movie> recommendations = getRecommendations(preferredGenre, preferredMood, maxDuration);
            displayRecommendations(recommendations);
            cout << "Would you like another search? (y/n): ";
            cin >> continueSearch;
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
        }
        if (!lastRecommendations.empty()) {
            cout << "\nTop Recommendation Overall: " << lastRecommendations.front().title
                 << " (" << lastRecommendations.front().genre << ", Rating: "
                 << lastRecommendations.front().rating << ")\n";
        }
        cout << "\nThanks for using our Netflix-inspired Movie Finder!\n";
        cout << string(60, '=') << "\n";
    }
};
int main() {
    NetflixMovieFinder finder;
    finder.runMovieFinder();
    return 0;
}
