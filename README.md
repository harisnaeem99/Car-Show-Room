# Car-Show-Room
#include <iostream>
#include <string>
#include <fstream>
using namespace std;

class Car {
public:
    string model, brand;
    double price;
    bool sold, isUserOwned;
    Car* next;

    Car(string m, string b, double p, bool userOwned = false, bool s = false)
        : model(m), brand(b), price(p), sold(s), isUserOwned(userOwned), next(nullptr) {}
};

class CarShowroom {
public:
    Car* head;
    CarShowroom() : head(nullptr) {
        loadCarsFromFile();
        if (!head) {  
            addCar("Corolla GLI", "Toyota", 75000);
            addCar("Civic", "Honda", 55000);
            addCar("Sportage", "KIA", 30000);
        }
    }

    ~CarShowroom() {
        saveCarsToFile();
        while (head) {
            Car* temp = head->next;
            delete head;
            head = temp;
        }
    }

    void addCar(const string& model, const string& brand, double price, bool isUserOwned = false, bool sold = false) {
        Car* newCar = new Car(model, brand, price, isUserOwned, sold);
        if (!head) head = newCar;
        else {
            Car* temp = head;
            while (temp->next) temp = temp->next;
            temp->next = newCar;
        }
    }

    void viewCars() const {
        Car* temp = head;
        bool available = false;
        cout << "Available Cars:\n";
        while (temp) {
            if (!temp->sold) {
                cout << temp->model << " (" << temp->brand << "), $" << temp->price
                    << (temp->isUserOwned ? " [Used]" : "") << "\n";
                available = true;
            }
            temp = temp->next;
        }
        if (!available) cout << "No available cars.\n";
    }

    void sellCar() {
        string model;
        cout << "Enter Car Model to Sell: ";
        cin >> model;
        for (Car* temp = head; temp; temp = temp->next) {
            if (equalsIgnoreCase(temp->model, model) && !temp->sold) {
                temp->sold = true;
                cout << "Car sold successfully!\n";
                return;
            }
        }
        cout << "Car not found or already sold.\n";
    }

    void buyCar() {
        viewCars();
        string model;
        cout << "Enter model to buy or 'exit' to cancel: ";
        cin >> model;
        if (model == "exit") return;
        for (Car* temp = head; temp; temp = temp->next) {
            if (equalsIgnoreCase(temp->model, model) && !temp->sold) {
                temp->sold = true;
                cout << "Congratulations! You bought " << model << "!\n";
                return;
            }
        }
        cout << "Car not found or already sold.\n";
    }

    void sellOwnCar() {
        string model, brand;
        double price;
        cout << "Enter Model, Brand, and Price of the car you're selling: ";
        cin >> model >> brand >> price;
        addCar(model, brand, price, true);
        cout << "Your car has been added to the showroom!\n";
    }

    void mainMenu() {
        while (true) {
            cout << "\n*** CAR SHOWROOM MENU ***\n1. Add Car\n2. View Cars\n3. Sell Car\n4. Buy Car\n5. Sell Own Car\n6. Exit\nChoose an option: ";
            int choice;
            cin >> choice;
            switch (choice) {
            case 1: addCarPrompt(); break;
            case 2: viewCars(); break;
            case 3: sellCar(); break;
            case 4: buyCar(); break;
            case 5: sellOwnCar(); break;
            case 6: cout << "Thank you for using the Car Showroom System!\n"; return;
            default: cout << "Invalid choice.\n";
            }
        }
    }

private:
    void addCarPrompt() {
        string model, brand;
        double price;
        cout << "Enter Car Model, Brand, and Price: ";
        cin >> model >> brand >> price;
        addCar(model, brand, price);
        cout << "Car added successfully!\n";
    }

    static bool equalsIgnoreCase(const string& a, const string& b) {
        if (a.size() != b.size()) return false;
        for (size_t i = 0; i < a.size(); ++i) {
            if (tolower(a[i]) != tolower(b[i])) return false;
        }
        return true;
    }

    void saveCarsToFile() {
        ofstream file("cars.txt");
        Car* temp = head;
        while (temp) {
            file << temp->model << " " << temp->brand << " " << temp->price << " " << temp->sold << " " << temp->isUserOwned << "\n";
            temp = temp->next;
        }
        file.close();
    }

    void loadCarsFromFile() {
        ifstream file("cars.txt");
        if (!file) return;
        string model, brand;
        double price;
        bool sold, isUserOwned;
        while (file >> model >> brand >> price >> sold >> isUserOwned) {
            addCar(model, brand, price, isUserOwned, sold);
        }
        file.close();
    }
};

int main() {
    system("color A0");
    CarShowroom showroom;
    cout << "WELCOME TO THE CAR ZONE\n";
    showroom.mainMenu();
    return 0;
}
