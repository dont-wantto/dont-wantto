- 👋 Hi, I’m @dont-wantto
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
dont-wantto/dont-wantto is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <fstream>
#include <chrono>
#include <ctime>
#include <sstream>
#include <iomanip>
using namespace std;

// Enum to represent task status
enum class TaskStatus { NotCompleted, Completed };
enum class CategoryType { WORK, PERSONAL, OTHER };

class Task {
public:
    Task(const string& title, const string& description, const string& dueDate, int priority, CategoryType category)
        : title(title), description(description), dueDate(dueDate), priority(priority), status(TaskStatus::NotCompleted), category(category) {}

    string getTitle() const { return title; }
    string getDescription() const { return description; }
    string getDueDate() const { return dueDate; }
    int getPriority() const { return priority; }
    TaskStatus getStatus() const { return status; }
    CategoryType getCategory() const { return category; } // Add getCategory method for category

    void markCompleted() { status = TaskStatus::Completed; }
    void editTask(const string& newTitle, const string& newDescription, const string& newDueDate, int newPriority) {
        title = newTitle;
        description = newDescription;
        dueDate = newDueDate;
        priority = newPriority;
    }

private:
    string title;
    string description;
    string dueDate;
    int priority;
    TaskStatus status;
    CategoryType category; // Added category member
};

class User {
public:
    User(const string& userId, const string& userName, const string& userEmail, const string& userPassword)
        : userId(userId), userName(userName), userEmail(userEmail), userPassword(userPassword) {}
    string getUserId() const { return userId; }
    string getUserName() const { return userName; }
    string getUserEmail() const { return userEmail; }
    string getUserPassword() const { return userPassword; }

private:
    string userId;
    string userName;
    string userEmail;
    string userPassword;
};

class TaskManager {
public:
    void addTask(const string& title, const string& description, const string& dueDate, int priority, CategoryType category) {
        tasks.push_back(Task(title, description, dueDate, priority, category));
        cout << "Task added: " << title << endl;
        saveTasksToFile();
    }

    void viewTasks() const {
        cout << "=== Task List ===" << endl;
        for (int i = 0; i < tasks.size(); i++) {
            cout << "Task " << (i + 1) << ": " << tasks[i].getTitle() << endl;
            cout << "Description: " << tasks[i].getDescription() << endl;
            cout << "Due Date: " << tasks[i].getDueDate() << endl;
            cout << "Priority: " << tasks[i].getPriority() << endl;
            cout << "Category: " << getCategoryName(tasks[i].getCategory()) << endl; // Show category name
            cout << "Status: " << (tasks[i].getStatus() == TaskStatus::Completed ? "Completed" : "Not Completed") << endl;
        }
        cout << "=================" << endl;
    }

    // Add a getCategoryName function to convert CategoryType to string
    string getCategoryName(CategoryType category) const {
        switch (category) {
            case CategoryType::WORK: return "Work";
            case CategoryType::PERSONAL: return "Personal";
            case CategoryType::OTHER: return "Other";
            default: return "Unknown";
        }
    }
    void deleteTask(int index) {
        if (index >= 0 && index < tasks.size()) {
            cout << "Task deleted: " << tasks[index].getTitle() << endl;
            tasks.erase(tasks.begin() + index);
            saveTasksToFile();
        } else {
            cout << "Invalid task index." << endl;
        }
    }

    void markTaskAsCompleted(int index) {
        if (index >= 0 && index < tasks.size()) {
            tasks[index].markCompleted();
            cout << "Task marked as completed: " << tasks[index].getTitle() << endl;
            saveTasksToFile();
        } else {
            cout << "Invalid task index." << endl;
        }
    }

    void editTask(int index, const string& newTitle, const string& newDescription, const string& newDueDate, int newPriority) {
        if (index >= 0 and index < tasks.size()) {
            tasks[index].editTask(newTitle, newDescription, newDueDate, newPriority);
            cout << "Task edited: " << newTitle << endl;
            saveTasksToFile();
        } else {
            cout << "Invalid task index." << endl;
        }
    }

    void searchTasks(const string& keyword) const {
        cout << "=== Search Results ===" << endl;
        for (int i = 0; i < tasks.size(); i++) {
            if (tasks[i].getTitle().find(keyword) != string::npos ||
                tasks[i].getDescription().find(keyword) != string::npos) {
                cout << "Task " << (i + 1) << ": " << tasks[i].getTitle() << endl;
                cout << "Description: " << tasks[i].getDescription() << endl;
            }
        }
        cout << "=====================" << endl;
    }

    void sortTasks(int option) {
        switch (option) {
        case 1:
            // Sort by due date
            sort(tasks.begin(), tasks.end(), [](const Task& a, const Task& b) {
                return a.getDueDate() < b.getDueDate();
            });
            break;
        case 2:
            // Sort by priority
            sort(tasks.begin(), tasks.end(), [](const Task& a, const Task& b) {
                return a.getPriority() < b.getPriority();
            });
            break;
        case 3:
            // Sort by title
            sort(tasks.begin(), tasks.end(), [](const Task& a, const Task& b) {
                return a.getTitle() < b.getTitle();
            });
            break;
        default:
            cout << "Invalid sorting option." << endl;
        }
    }

   void showNotifications() const {
    cout << "=== Task Notifications ===" << endl;
    auto now = chrono::system_clock::to_time_t(chrono::system_clock::now());
    bool hasReminders = false;

    for (const Task& task : tasks) {
        if (task.getStatus() == TaskStatus::NotCompleted) {
            tm tm = {};
            istringstream dueDateStream(task.getDueDate());
            dueDateStream >> get_time(&tm, "%Y-%m-%d");
            auto dueDate = chrono::system_clock::from_time_t(mktime(&tm));
            auto timeDifference = dueDate - chrono::system_clock::from_time_t(now);

            if (timeDifference > chrono::hours(0) && timeDifference <= chrono::hours(24)) {
                cout << "Task: " << task.getTitle() << " is due in less than 24 hours on " << task.getDueDate() << endl;
                hasReminders = true;
            }

            if (task.getPriority() == 1) {  // Check for high-priority tasks
                cout << "Reminder: Don't forget the high-priority task - " << task.getTitle() << endl;
                hasReminders = true;
            }
        }
    }

    if (!hasReminders) {
        cout << "No important reminders at the moment." << endl;
    }

    cout << "===========================" << endl;
}


    void wishMe() {
        time_t now = time(0);
        tm* currentTime = localtime(&now);
        int currentHour = currentTime->tm_hour;
        if (currentHour < 12) {
            cout << "Good morning!" << endl;
        } else if (currentHour < 17) {
            cout << "Good afternoon!" << endl;
        } else {
            cout << "Good evening!" << endl;
        }
    }

private:
    vector<Task> tasks;

    void saveTasksToFile() {
        ofstream outFile("tasks.txt");
        if (!outFile) {
            cerr << "Error: Could not open file for writing." << endl;
            return;
        }
        for (const Task& task : tasks) {
            outFile << task.getTitle() << '\n';
            outFile << task.getDescription() << '\n';
            outFile << task.getDueDate() << '\n';
            outFile << task.getPriority() << '\n';
            outFile << static_cast<int>(task.getStatus()) << '\n';
            outFile << static_cast<int>(task.getCategory()) << '\n'; // Save category as an integer
        }
        outFile.close();
    }
};

int main() {
    // Greeting
    cout << "Welcome to the Task Management System!" << endl;

    // Sample users (for demonstration)
    vector<User> users;
    users.push_back(User("1", "John Doe", "john@example.com", "password1"));
    users.push_back(User("2", "Jane Smith", "jane@example.com", "password2"));

    TaskManager taskManager;
    taskManager.wishMe(); // Greet the user based on the time of day

    int choice;
    string userId, title, description, dueDate, keyword;
    int priority, index, sortingOption;
    bool userFound = false;
    string password;

    do {
        cout << "===== Main Menu =====" << endl;
        cout << "1. Log In" << endl;
        cout << "2. Show Notifications" << endl;
        cout << "0. Exit" << endl;
        cout << "Enter your choice: ";
        cin >> choice;
        cin.ignore();

        switch (choice) {
            case 1:
                // Log In
                cout << "Enter User ID: ";
                cin >> userId;
                cout << "Enter Password: ";
                cin >> password;
                userFound = false;

                for (const User& user : users) {
                    if (user.getUserId() == userId && user.getUserPassword() == password) {
                        userFound = true;
                        break;
                    }
                }

                if (userFound) {
                    int userChoice;
                    do {
                        cout << "===== User Menu =====" << endl;
                        cout << "1. Add Task" << endl;
                        cout << "2. View Tasks" << endl;
                        cout << "3. Delete Task" << endl;
                        cout << "4. Mark Task as Completed" << endl;
                        cout << "5. Edit Task" << endl;
                        cout << "6. Search Tasks" << endl;
                        cout << "7. Sort Tasks" << endl;
                        cout << "8. Show Notifications" << endl;
                        cout << "9. Show Current Time and Date" << endl;
                        cout << "10. Log Out" << endl;
                        cout << "Enter your choice: ";
                        cin >> userChoice;
                        cin.ignore();

                        switch (userChoice) {
                          case 1:
    // Adding Task
    cout << "Enter Task Title: ";
    getline(cin, title);
    cout << "Enter Task Description: ";
    getline(cin, description);
    cout << "Enter Due Date: ";
    getline(cin, dueDate);
    cout << "Enter Priority (1 for high, 2 for medium, 3 for low): ";
    cin >> priority;
    cout << "Enter Category (1 for WORK, 2 for PERSONAL, 3 for OTHER): ";
    int categoryInt;
    cin >> categoryInt;

    // Convert the integer to CategoryType
    CategoryType category;
    if (categoryInt == 1) {
        category = CategoryType::WORK;
    } else if (categoryInt == 2) {
        category = CategoryType::PERSONAL;
    } else if (categoryInt == 3) {
        category = CategoryType::OTHER;
    } else {
        category = CategoryType::OTHER; // Default to OTHER if an invalid category is entered.
    }

    taskManager.addTask(title, description, dueDate, priority, category);
    break;

                            case 2:
                                // Viewing Tasks
                                taskManager.viewTasks();
                                break;
                            case 3:
                                // Deleting Task
                                cout << "Enter the index of the task to delete: ";
                                cin >> index;
                                taskManager.deleteTask(index - 1);
                                break;
                            case 4:
                                // Marking Task as Completed
                                cout << "Enter the index of the task to mark as completed: ";
                                cin >> index;
                                taskManager.markTaskAsCompleted(index - 1);
                                break;
                            case 5:
                                // Editing Task
                                cout << "Enter the index of the task to edit: ";
                                cin >> index;
                                cin.ignore();
                                cout << "Enter new Task Title: ";
                                getline(cin, title);
                                cout << "Enter new Task Description: ";
                                getline(cin, description);
                                cout << "Enter new Due Date: ";
                                getline(cin, dueDate);
                                cout << "Enter new Priority (1 for high, 2 for medium, 3 for low): ";
                                cin >> priority;
                                taskManager.editTask(index - 1, title, description, dueDate, priority);
                                break;
                            case 6:
                                // Searching Tasks
                                cout << "Enter keyword to search: ";
                                cin.ignore();
                                getline(cin, keyword);
                                taskManager.searchTasks(keyword);
                                break;
                            case 7:
                                // Sorting Tasks
                                cout << "Sort tasks by:" << endl;
                                cout << "1. Due Date" << endl;
                                cout << "2. Priority" << endl;
                                cout << "3. Title" << endl;
                                cout << "Enter sorting option: ";
                                cin >> sortingOption;
                                taskManager.sortTasks(sortingOption);
                                break;
                            case 8:
                                // Show Notifications
                                taskManager.showNotifications();
                                break;
                           case 9:
    // Show Current Time and Date
    {
        auto currentTime = chrono::system_clock::to_time_t(chrono::system_clock::now());
        struct tm tm = *localtime(&currentTime);
        cout << "Current Time and Date: " << put_time(&tm, "%Y-%m-%d %H:%M:%S") << endl;
    }
    break;

                            case 10:
                                // Log Out
                                cout << "Logging out..." << endl;
                                break;
                            default:
                                cout << "Invalid choice. Please try again." << endl;
                                break;
                        }
                    } while (userChoice != 10);
                } else {
                    cout << "Invalid credentials. Please try again." << endl;
                }
                break;
            case 2:
                // Show Notifications
                taskManager.showNotifications();
                break;
            case 0:
                // Exit
                cout << "Goodbye!" << endl;
                break;
            default:
                cout << "Invalid choice. Please try again." << endl;
                break;
        }

        cout << endl;
    } while (choice != 0);

    return 0;
}
