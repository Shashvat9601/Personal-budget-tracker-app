#include <iostream>
#include <vector>
#include <string>
#include <iomanip>
#include <fstream>

using namespace std;

struct Transaction {
    string type;       // "Income" or "Expense"
    string description;
    double amount;
};

class BudgetTracker {
private:
    vector<Transaction> history;
    double balance;

public:
    BudgetTracker() : balance(0.0) {}

    void addTransaction(const string& type, const string& description, double amount) {
        if (type == "Expense" && amount > balance) {
            cout << "⚠️  Insufficient balance for this expense.\n";
            return;
        }

        Transaction t = {type, description, amount};
        history.push_back(t);

        if (type == "Income") balance += amount;
        else if (type == "Expense") balance -= amount;

        cout << "✅ Transaction added successfully.\n";
    }

    void showBalance() const {
        cout << fixed << setprecision(2);
        cout << "\n💰 Current Balance: ₹" << balance << "\n";
    }

    void showHistory() const {
        if (history.empty()) {
            cout << "\n📂 No transactions recorded yet.\n";
            return;
        }

        cout << "\n📜 Transaction History:\n";
        cout << "-------------------------------\n";
        for (const auto& t : history) {
            cout << t.type << " | ₹" << fixed << setprecision(2) << t.amount << " | " << t.description << "\n";
        }
        cout << "-------------------------------\n";
    }

    void saveToFile(const string& filename) {
        ofstream file(filename);
        if (!file) {
            cout << "❌ Error saving to file.\n";
            return;
        }

        file << balance << "\n";
        for (const auto& t : history) {
            file << t.type << "," << t.amount << "," << t.description << "\n";
        }

        file.close();
        cout << "💾 Data saved to " << filename << "\n";
    }

    void loadFromFile(const string& filename) {
        ifstream file(filename);
        if (!file) {
            cout << "❌ File not found. Starting fresh.\n";
            return;
        }

        history.clear();
        file >> balance;
        file.ignore(); // skip newline

        string line;
        while (getline(file, line)) {
            size_t pos1 = line.find(',');
            size_t pos2 = line.find(',', pos1 + 1);
            if (pos1 == string::npos || pos2 == string::npos) continue;

            string type = line.substr(0, pos1);
            double amount = stod(line.substr(pos1 + 1, pos2 - pos1 - 1));
            string description = line.substr(pos2 + 1);

            history.push_back({type, description, amount});
        }

        file.close();
        cout << "📂 Data loaded from " << filename << "\n";
    }
};

void showMenu() {
    cout << "\n=== 📊 Shashvat Budget Tracker ===\n";
    cout << "1. Add Income\n";
    cout << "2. Add Expense\n";
    cout << "3. Show Balance\n";
    cout << "4. Show History\n";
    cout << "5. Save to File\n";
    cout << "6. Load from File\n";
    cout << "0. Exit\n";
    cout << "Choose an option: ";
}

int main() {
    BudgetTracker shashvat;
    shashvat.loadFromFile("shashvat_data.txt");

    int choice;
    do {
        showMenu();
        cin >> choice;
        cin.ignore(); // Clear newline after input

        string desc;
        double amt;

        switch (choice) {
            case 1:
                cout << "Enter income description: ";
                getline(cin, desc);
                cout << "Enter amount: ₹";
                cin >> amt;
                cin.ignore();
                shashvat.addTransaction("Income", desc, amt);
                break;

            case 2:
                cout << "Enter expense description: ";
                getline(cin, desc);
                cout << "Enter amount: ₹";
                cin >> amt;
                cin.ignore();
                shashvat.addTransaction("Expense", desc, amt);
                break;

            case 3:
                shashvat.showBalance();
                break;

            case 4:
                shashvat.showHistory();
                break;

            case 5:
                shashvat.saveToFile("shashvat_data.txt");
                break;

            case 6:
                shashvat.loadFromFile("shashvat_data.txt");
                break;

            case 0:
                cout << "👋 Exiting Shashvat. Goodbye!\n";
                break;

            default:
                cout << "❗ Invalid choice. Try again.\n";
        }

    } while (choice != 0);

    return 0;
}
