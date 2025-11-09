# Capstone project Assignment 1
# Batch 10
# Name: Lipika Soye
# Reg.No: 2241019548
**Project Topic:** File Explorer Application

```cpp
#include <iostream>
#include <filesystem>
#include <fstream>
#include <sys/stat.h>
#include <unistd.h>
#include <string>

using namespace std;
namespace fs = std::filesystem;

// 🔍 Function: Search file recursively
void searchFile(const fs::path& path, const string& name) {
    bool found = false;
    for (const auto& entry : fs::recursive_directory_iterator(path)) {
        if (entry.path().filename() == name) {
            cout << "✅ Found: " << entry.path() << endl;
            found = true;
        }
    }
    if (!found) cout << "❌ File not found.\n";
}

// 🔐 Function: Show file permissions
void showPermissions(const fs::path& file) {
    struct stat info{};
    if (stat(file.string().c_str(), &info) == 0) {
        cout << "Permissions (Owner): "
             << ((info.st_mode & S_IRUSR) ? "r" : "-")
             << ((info.st_mode & S_IWUSR) ? "w" : "-")
             << ((info.st_mode & S_IXUSR) ? "x" : "-") << endl;
    } else {
        cout << "Cannot access file.\n";
    }
}

// ✏️ Function: Change file permissions
void changePermission(const fs::path& file) {
    mode_t mode;
    cout << "Enter new permission mode in octal (e.g., 644): ";
    int perm;
    cin >> oct >> perm;
    mode = static_cast<mode_t>(perm);
    if (chmod(file.string().c_str(), mode) == 0)
        cout << "Permissions changed successfully.\n";
    else
        cout << "Failed to change permissions.\n";
}

// 📂 Function: List directory contents
void listDirectory(const fs::path& path) {
    cout << "\nContents of " << fs::absolute(path) << ":\n";
    for (const auto& entry : fs::directory_iterator(path)) {
        cout << (entry.is_directory() ? "[DIR]  " : "[FILE] ")
             << entry.path().filename().string() << endl;
    }
}

int main() {
    fs::path currentPath = fs::current_path();
    int choice;

    while (true) {
        cout << "\n==============================\n";
        cout << "🗂️  File Explorer (Console)\n";
        cout << "==============================\n";
        cout << "Current Directory: " << currentPath << "\n";
        cout << "1. List Files & Folders\n";
        cout << "2. Enter Directory\n";
        cout << "3. Go Up One Level\n";
        cout << "4. Create File\n";
        cout << "5. Delete File\n";
        cout << "6. Copy File\n";
        cout << "7. Move File\n";
        cout << "8. Search File\n";
        cout << "9. Show File Permissions\n";
        cout << "10. Change File Permissions\n";
        cout << "0. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        if (choice == 0) {
            cout << "Exiting File Explorer... 👋\n";
            break;
        }

        switch (choice) {
            case 1: // List files
                listDirectory(currentPath);
                break;

            case 2: { // Enter directory
                string dir;
                cout << "Enter directory name: ";
                cin >> dir;
                fs::path newPath = currentPath / dir;
                if (fs::exists(newPath) && fs::is_directory(newPath))
                    currentPath = newPath;
                else
                    cout << "Invalid directory!\n";
                break;
            }

            case 3: // Go up one level
                currentPath = currentPath.parent_path();
                break;

            case 4: { // Create file
                string fname;
                cout << "Enter filename to create: ";
                cin >> fname;
                ofstream file(currentPath / fname);
                if (file)
                    cout << "File created successfully.\n";
                else
                    cout << "Failed to create file.\n";
                break;
            }

            case 5: { // Delete file
                string fname;
                cout << "Enter filename to delete: ";
                cin >> fname;
                fs::path filePath = currentPath / fname;
                if (fs::exists(filePath)) {
                    fs::remove(filePath);
                    cout << "File deleted.\n";
                } else
                    cout << "File not found.\n";
                break;
            }

            case 6: { // Copy file
                string src, dest;
                cout << "Enter source filename: ";
                cin >> src;
                cout << "Enter destination filename: ";
                cin >> dest;
                fs::path srcPath = currentPath / src;
                fs::path destPath = currentPath / dest;
                if (fs::exists(srcPath)) {
                    fs::copy(srcPath, destPath, fs::copy_options::overwrite_existing);
                    cout << "File copied.\n";
                } else
                    cout << "Source file not found.\n";
                break;
            }

            case 7: { // Move file
                string src, dest;
                cout << "Enter source filename: ";
                cin >> src;
                cout << "Enter destination filename: ";
                cin >> dest;
                fs::path srcPath = currentPath / src;
                fs::path destPath = currentPath / dest;
                if (fs::exists(srcPath)) {
                    fs::rename(srcPath, destPath);
                    cout << "File moved.\n";
                } else
                    cout << "Source file not found.\n";
                break;
            }

            case 8: { // Search file
                string fname;
                cout << "Enter filename to search: ";
                cin >> fname;
                searchFile(currentPath, fname);
                break;
            }

            case 9: { // Show permissions
                string fname;
                cout << "Enter filename: ";
                cin >> fname;
                showPermissions(currentPath / fname);
                break;
            }

            case 10: { // Change permissions
                string fname;
                cout << "Enter filename: ";
                cin >> fname;
                changePermission(currentPath / fname);
                break;
            }

            default:
                cout << "Invalid choice.\n";
        }
    }
    return 0;
}
