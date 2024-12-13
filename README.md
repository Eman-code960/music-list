
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

struct Song {
    string title;
    string artist;
    Song* next;
};

class Playlist {
public:
    Song* head;

    Playlist() {
        head = nullptr;
    }

    void addSong(string title, string artist) {
        Song* newSong = new Song;
        newSong->title = title;
        newSong->artist = artist;
        newSong->next = head;
        head = newSong;
        saveToFile();
    }

    void searchSong(string title) {
        Song* temp = head;
        while (temp != nullptr) {
            if (temp->title == title) {
                cout << "Song found: " << temp->title << " by " << temp->artist << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Song is not found." << endl;
    }

    void deleteSong(string title) {
        Song* temp = head;
        Song* prev = nullptr;

        while (temp != nullptr && temp->title != title) {
            prev = temp;
            temp = temp->next;
        }

        if (temp == nullptr) {
            cout << "Song is not found." << endl;
            return;
        }

        if (prev == nullptr) {
            head = head->next;
        } else {
            prev->next = temp->next;
        }

        delete temp;
        cout << "Song is deleted" << endl;
        saveToFile();
    }

    void displaySongs() {
        Song* temp = head;
        if (temp == nullptr) {
            cout << "Empty playlist" << endl;
            return;
        }
        while (temp != nullptr) {
            cout << "Title: " << temp->title << ", Artist: " << temp->artist << endl;
            temp = temp->next;
        }
    }

    void loadFromFile() {
        ifstream file("playlist.txt");
        if (!file.is_open()) return;

        string title, artist;
        while (getline(file, title) && getline(file, artist)) {
            addSong(title, artist);
        }
        file.close();
    }

    void saveToFile() {
        ofstream file("playlist.txt");
        Song* temp = head;
        while (temp != nullptr) {
            file << temp->title << endl;
            file << temp->artist << endl;
            temp = temp->next;
        }
        file.close();
    }

    int countSongs() {
        int count = 0;
        Song* temp = head;
        while (temp != nullptr) {
            count++;
            temp = temp->next;
        }
        return count;
    }

    void editSong(string oldTitle, string newTitle, string newArtist) {
        Song* temp = head;
        while (temp != nullptr) {
            if (temp->title == oldTitle) {
                temp->title = newTitle;
                temp->artist = newArtist;
                cout << "Song details updated successfully!" << endl;
                saveToFile();
                return;
            }
            temp = temp->next;
        }
        cout << "Song not found!" << endl;
    }

    void clearPlaylist() {
        while (head != nullptr) {
            Song* temp = head;
            head = head->next;
            delete temp;
        }
        cout << "Playlist cleared successfully!" << endl;
        saveToFile();
    }

    void reversePlaylist() {
        Song* prev = nullptr;
        Song* current = head;
        Song* next = nullptr;

        while (current != nullptr) {
            next = current->next;
            current->next = prev;
            prev = current;
            current = next;
        }
        head = prev;
        cout << "Playlist reversed successfully!" << endl;
        saveToFile();
    }

    void displaySongsByArtist(string artist) {
        Song* temp = head;
        bool found = false;

        while (temp != nullptr) {
            if (temp->artist == artist) {
                cout << "Title: " << temp->title << endl;
                found = true;
            }
            temp = temp->next;
        }
        if (!found) {
            cout << "No songs found for this artist." << endl;
        }
    }

    ~Playlist() {
        Song* temp;
        while (head != nullptr) {
            temp = head;
            head = head->next;
            delete temp;
        }
    }
};

int main() {
    Playlist playlist;
    playlist.loadFromFile();

    int choice;
    string title, artist, newTitle, newArtist;

    do {
        cout << "\n1. Add Song\n2. Search Song\n3. Delete Song\n4. Display Songs\n5. Count Songs\n";
        cout << "6. Display Songs by Artist\n7. Edit Song\n8. Clear Playlist\n9. Reverse Playlist\n10. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;
        cin.ignore();

        switch (choice) {
        case 1:
            cout << "Enter song title: ";
            getline(cin, title);
            cout << "Enter artist name: ";
            getline(cin, artist);
            playlist.addSong(title, artist);
            cout << "Song added successfully" << endl;
            break;
        case 2:
            cout << "Enter song title to search: ";
            getline(cin, title);
            playlist.searchSong(title);
            break;
        case 3:
            cout << "Enter song title to delete: ";
            getline(cin, title);
            playlist.deleteSong(title);
            break;
        case 4:
            cout << "Playlist:" << endl;
            playlist.displaySongs();
            break;
        case 5:
            cout << "Total songs in playlist: " << playlist.countSongs() << endl;
            break;
        case 6:
            cout << "Enter artist name to search songs: ";
            getline(cin, artist);
            playlist.displaySongsByArtist(artist);
            break;
        case 7:
            cout << "Enter the title of the song to edit: ";
            getline(cin, title);
            cout << "Enter the new title: ";
            getline(cin, newTitle);
            cout << "Enter the new artist: ";
            getline(cin, newArtist);
            playlist.editSong(title, newTitle, newArtist);
            break;
        case 8:
            playlist.clearPlaylist();
            break;
        case 9:
            playlist.reversePlaylist();
            break;
        case 10:
            cout << "Exiting" << endl;
            break;
        default:
            cout << "Invalid choice. Please try again" << endl;
        }
    } while (choice != 10);

    return 0;
}
