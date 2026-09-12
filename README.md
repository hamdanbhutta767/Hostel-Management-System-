# Hostel-Management-System-
This is a Hostel management system Project CPP code .
#include <iostream>
#include <fstream>
#include <iomanip>
#include <cstring>
#include <windows.h>
using namespace std;

class Person {
protected:
    int id;
    char name[50];
    char Department[50];
public:
    Person(int id=0,const char* name="",const char* Department="") {
        this->id=id;
        strcpy(this->name,name);
        strcpy(this->Department,Department);
    }
    virtual void input()=0;
    virtual void display()=0;
    int getID() { return this->id; }
    const char* getName() { return this->name; }
    const char* getDepartment() { return this->Department; }
    virtual ~Person() {}
};

class Student:public Person {
private:
    int roomNo;
    float cgpa;
    float paidFee;
    float remainingFee;
public:
    static constexpr float FIXED_FEE=5000;
    Student(int id=0,const char* name="",int roomNo=0,float cgpa=0,float paidFee=0):Person(id,name) {
        this->roomNo=roomNo;
        this->cgpa=cgpa;
        this->paidFee=paidFee;
        this->remainingFee=FIXED_FEE-paidFee;
    }
    void input() override {
        cout<<"\nEnter Student ID : ";
        cin>>this->id;
        cin.ignore();
        cout<<"Enter Student Name : ";
        cin.getline(this->name,50);
        cout<<"Enter Student Department : ";
        cin.getline(this->Department,50);
        cout<<"Enter Room Number : ";
        cin>>this->roomNo;
        cout<<"Enter CGPA : ";
        cin>>this->cgpa;
        cout<<"Fixed Hostel Fee = 5000\n";
        cout<<"Enter Paid Fee : ";
        cin>>this->paidFee;
        if(this->paidFee>FIXED_FEE)
            this->paidFee=FIXED_FEE;
        this->remainingFee=FIXED_FEE-this->paidFee;
    }
    void display() override {
        cout<<"\n==================================================";
        cout<<"\n               STUDENT INFORMATION";
        cout<<"\n==================================================\n";
        cout<<left<<setw(20)<<"Student ID"<<": "<<this->id<<endl;
        cout<<left<<setw(20)<<"Student Name"<<": "<<this->name<<endl;
        cout<<left<<setw(20)<<"Room Number"<<": "<<this->roomNo<<endl;
        cout<<left<<setw(20)<<"CGPA"<<": "<<this->cgpa<<endl;
        cout<<left<<setw(20)<<"Department"<<": "<<this->Department<<endl;
        cout<<left<<setw(20)<<"Paid Fee"<<": "<<this->paidFee<<endl;
        cout<<left<<setw(20)<<"Remaining Fee"<<": "<<this->remainingFee<<endl;
        cout<<"==================================================\n";
    }
    int getRoomNo() { return this->roomNo; }
    ~Student() {}
};

class Warden:public Person {
private:
    char duty[50];
public:
    Warden(int id=0,const char* name="",const char* duty=""):Person(id,name) {
        strcpy(this->duty,duty);
    }
    void input() override {
        cout<<"\nEnter Warden ID : ";
        cin>>this->id;
        cin.ignore();
        cout<<"Enter Warden Name : ";
        cin.getline(this->name,50);
        cout<<"Enter Warden Duty : ";
        cin.getline(this->duty,50);
    }
    void display() override {
        cout<<"\n==================================================";
        cout<<"\n               WARDEN INFORMATION";
        cout<<"\n==================================================\n";
        cout<<left<<setw(20)<<"Warden ID"<<": "<<this->id<<endl;
        cout<<left<<setw(20)<<"Warden Name"<<": "<<this->name<<endl;
        cout<<left<<setw(20)<<"Duty"<<": "<<this->duty<<endl;
        cout<<"==================================================\n";
    }
    ~Warden() {}
};

class Room {
private:
    int roomNo;
    int floor;
    int beds;
    int lockers;
public:
    Room(int roomNo=0,int floor=0,int beds=0,int lockers=0) {
        this->roomNo=roomNo;
        this->floor=floor;
        this->beds=beds;
        this->lockers=lockers;
    }
    void setRoom(int roomNo,int floor,int beds,int lockers) {
        this->roomNo=roomNo;
        this->floor=floor;
        this->beds=beds;
        this->lockers=lockers;
    }
    void display() {
        cout<<"\n==================================================";
        cout<<"\n                 ROOM INFORMATION";
        cout<<"\n==================================================\n";
        cout<<left<<setw(20)<<"Room Number"<<": "<<this->roomNo<<endl;
        cout<<left<<setw(20)<<"Floor"<<": "<<this->floor<<endl;
        cout<<left<<setw(20)<<"Beds"<<": "<<this->beds<<endl;
        cout<<left<<setw(20)<<"Lockers"<<": "<<this->lockers<<endl;
        cout<<"==================================================\n";
    }
    int getRoomNo() { return this->roomNo; }
    int getBeds() { return this->beds; }
    ~Room() {}
};

class HostelManagementSystem {
public:
    HostelManagementSystem() {}
    void initializeRooms() {
        ofstream file("rooms.dat",ios::binary);
        int roomNo=1;
        for(int floor=1;floor<=2;floor++) {
            for(int i=1;i<=4;i++) {
                Room r;
                if(roomNo<=3)
                    r.setRoom(roomNo,floor,2,2);
                else if(roomNo<=6)
                    r.setRoom(roomNo,floor,3,3);
                else
                    r.setRoom(roomNo,floor,4,4);
                file.write((char*)&r,sizeof(r));
                roomNo++;
            }
        }
        file.close();
    }
    bool isStudentIDExists(int id) {
        Student s;
        ifstream file("students.dat",ios::binary);
        while(file.read((char*)&s,sizeof(s))) {
            if(s.getID()==id) {
                file.close();
                return true;
            }
        }
        file.close();
        return false;
    }
    int getRoomCapacity(int roomNo) {
        Room r;
        ifstream file("rooms.dat",ios::binary);
        while(file.read((char*)&r,sizeof(r))) {
            if(r.getRoomNo()==roomNo) {
                file.close();
                return r.getBeds();
            }
        }
        file.close();
        return 0;
    }
    int getStudentsInRoom(int roomNo) {
        Student s;
        int count=0;
        ifstream file("students.dat",ios::binary);
        while(file.read((char*)&s,sizeof(s))) {
            if(s.getRoomNo()==roomNo)
                count++;
        }
        file.close();
        return count;
    }
    void addStudent() {
        Student s;
        s.input();
        if(isStudentIDExists(s.getID())) {
            cout<<"\nStudent ID Already Exists!\n";
            return;
        }
        if(getStudentsInRoom(s.getRoomNo())>=getRoomCapacity(s.getRoomNo())) {
            cout<<"\nRoom is FULL!\n";
            return;
        }
        ofstream file("students.dat",ios::binary|ios::app);
        file.write((char*)&s,sizeof(s));
        file.close();
        cout<<"\nStudent Added Successfully!\n";
    }
    void viewStudents() {
        Student s;
        ifstream file("students.dat",ios::binary);
        cout<<"\n========= STUDENT RECORD =========\n";
        while(file.read((char*)&s,sizeof(s))) {
            Person* p=&s;
            p->display();
        }
        file.close();
    }
    void searchStudent() {
        int id;
        bool found=false;
        Student s;
        cout<<"\nEnter Student ID : ";
        cin>>id;
        ifstream file("students.dat",ios::binary);
        while(file.read((char*)&s,sizeof(s))) {
            if(s.getID()==id) {
                Person* p=&s;
                p->display();
                found=true;
                break;
            }
        }
        if(!found)
            cout<<"\nStudent Not Found!\n";
        file.close();
    }
    void deleteStudent() {
        int id;
        bool found=false;
        Student s;
        cout<<"\nEnter Student ID To Delete : ";
        cin>>id;
        ifstream in("students.dat",ios::binary);
        ofstream out("temp.dat",ios::binary);
        while(in.read((char*)&s,sizeof(s))) {
            if(s.getID()!=id)
                out.write((char*)&s,sizeof(s));
            else
                found=true;
        }
        in.close();
        out.close();
        remove("students.dat");
        rename("temp.dat","students.dat");
        if(found)
            cout<<"\nStudent Deleted Successfully!\n";
        else
            cout<<"\nStudent Not Found!\n";
    }
    void viewRooms() {
        Room r;
        ifstream file("rooms.dat",ios::binary);
        cout<<"\n========= ROOM RECORD =========\n";
        while(file.read((char*)&r,sizeof(r)))
            r.display();
        file.close();
    }
    void addWarden() {
        Warden w;
        w.input();
        ofstream file("wardens.dat",ios::binary|ios::app);
        file.write((char*)&w,sizeof(w));
        file.close();
        cout<<"\nWarden Added Successfully!\n";
    }
    void viewWardens() {
        Warden w;
        ifstream file("wardens.dat",ios::binary);
        cout<<"\n========= WARDEN RECORD =========\n";
        while(file.read((char*)&w,sizeof(w))) {
            Person* p=&w;
            p->display();
        }
        file.close();
    }
    void searchWarden() {
        int id;
        bool found=false;
        Warden w;
        cout<<"\nEnter Warden ID : ";
        cin>>id;
        ifstream file("wardens.dat",ios::binary);
        while(file.read((char*)&w,sizeof(w))) {
            if(w.getID()==id) {
                Person* p=&w;
                p->display();
                found=true;
                break;
            }
        }
        if(!found)
            cout<<"\nWarden Not Found!\n";
        file.close();
    }
    void deleteWarden() {
        int id;
        bool found=false;
        Warden w;
        cout<<"\nEnter Warden ID To Delete : ";
        cin>>id;
        ifstream in("wardens.dat",ios::binary);
        ofstream out("temp_warden.dat",ios::binary);
        while(in.read((char*)&w,sizeof(w))) {
            if(w.getID()!=id)
                out.write((char*)&w,sizeof(w));
            else
                found=true;
        }
        in.close();
        out.close();
        remove("wardens.dat");
        rename("temp_warden.dat","wardens.dat");
        if(found)
            cout<<"\nWarden Deleted Successfully!\n";
        else
            cout<<"\nWarden Not Found!\n";
    }
    ~HostelManagementSystem() {}
};

void displayWelcomeScreen(int count) {
    if(count==1) {
        system("cls");
        cout<<"\n\t[SYSTEM] Initializing Hostel Management Core";
        for(int i=0;i<3;i++) {
            cout<<".";
            Sleep(300);
        }
        cout<<"\n\t[SYSTEM] Connecting to Student Database";
        for(int i=0;i<3;i++) {
            cout<<".";
            Sleep(300);
        }
        cout<<"\n\t[SYSTEM] Loading Room Management Modules";
        for(int i=0;i<4;i++) {
            cout<<".";
            Sleep(250);
        }
        cout<<" [READY]";
        Sleep(700);
    }
    system("cls");
    cout<<"\n\t+=======================================================================+";
    cout<<"\n\t|                  HOSTEL MANAGEMENT SYSTEM v1.0                        |";
    cout<<"\n\t+=======================================================================+\n";
    cout<<"\n";
    cout<<"\n";
    cout<<"\t  _    _  ____   _____ _______       _\n";
    cout<<"\t | |  | |/ __ \\ / ____|__   __|/\\   | |\n";
    cout<<"\t | |__| | |  | | (___    | |  /  \\  | |\n";
    cout<<"\t |  __  | |  | |\\___ \\   | | / /\\ \\ | |\n";
    cout<<"\t | |  | | |__| |____) |  | |/ ____ \\| |____\n";
    cout<<"\t |_|  |_|\\____/|_____/   |_/_/    \\_\\______|\n";
    cout<<"\n";
    cout<<"\n";
    cout<<"\t ======================================================\n";
    cout<<"\t        STUDENT HOSTEL MANAGEMENT SYSTEM\n";
    cout<<"\t ======================================================\n";
    cout<<"\n";
    cout<<"\t +-----------------------------------------------+\n";
    cout<<"\t | Features                                      |\n";
    cout<<"\t +-----------------------------------------------+\n";
    cout<<"\t |  * Student Management                         |\n";
    cout<<"\t |  * Room Allocation                            |\n";
    cout<<"\t |  * Hostel Fee Tracking                        |\n";
    cout<<"\t |  * Warden Management                          |\n";
    cout<<"\t +-----------------------------------------------+\n";
    cout<<"\t  | ";
    cout<<"DEVELOPMENT TEAM (2nd E-A)        ";
    std::cout<<" | ";
    cout<<"      PROJECT SPECS              |";
    std::cout<<"\n";
    std::cout<<"\t  +------------------------------------+----------------------------------+\n";
    cout<<"\t  | ";
    cout<<"[*] MUHAMMAD HAMDAN BHUTTA (2538)-Lead ";
    cout<<"| ";
    cout<<"|      Submitted to:         |             \n";
    cout<<"\t  | [-] MUHAMMAD MUAZ   (253881)| ";
    cout<<"           |PROFESSOR WAQAR HUSSAIN.    |\n ";
    cout<<"\t  |[-] MUHAMMAD ALI  (253872)   |       \n";
    cout<<"\t  +------------------------------------+----------------------------------+\n";
    cout<<"\n\n";
    cout<<"\n";
    cout<<"\t\t          +-----------------------------+\n";
    cout<<"\t\t          | PRESS ENTER TO CONTINUE     |\n";
    cout<<"\t\t          +-----------------------------+\n";
    cin.get();
}

void title() {
    cout<<"\n==============================================================";
    cout<<"\n                HOSTEL MANAGEMENT SYSTEM";
    cout<<"\n==============================================================";
    cout<<"\n Manage Students | Rooms | Fees | Wardens";
    cout<<"\n==============================================================\n";
}

int main() {
    HostelManagementSystem hms;
    hms.initializeRooms();
    displayWelcomeScreen(1);
    int choice;
    do {
        system("cls");
        title();
        cout<<"\n [1] Add Student";
        cout<<"\n [2] View Students";
        cout<<"\n [3] Search Student";
        cout<<"\n [4] Delete Student";
        cout<<"\n [5] View Rooms";
        cout<<"\n [6] Add Warden";
        cout<<"\n [7] View Wardens";
        cout<<"\n [8] Search Warden";
        cout<<"\n [9] Delete Warden";
        cout<<"\n [10] Exit";
        cout<<"\n\n Enter the Choice : ";
        cin>>choice;
        system("cls");
        switch(choice) {
            case 1:
                title();
                hms.addStudent();
                break;
            case 2:
                title();
                hms.viewStudents();
                break;
            case 3:
                title();
                hms.searchStudent();
                break;
            case 4:
                title();
                hms.deleteStudent();
                break;
            case 5:
                title();
                hms.viewRooms();
                break;
            case 6:
                title();
                hms.addWarden();
                break;
            case 7:
                title();
                hms.viewWardens();
                break;
            case 8:
                title();
                hms.searchWarden();
                break;
            case 9:
                title();
                hms.deleteWarden();
                break;
            case 10:
                cout<<"\nProgram Closed Successfully!\n";
                cout<<"\nThank You For Using Hostel Management System\n";
                break;
            default:
                cout<<"\nInvalid Choice!\n";
        }
        if(choice!=10) {
            cout<<"\n\nPress Enter To Continue...";
            cin.ignore();
            cin.get();
        }
    } while(choice!=10);
    return 0;
}
