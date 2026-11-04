# Final-Python-Project-CampusConnect-Management-System
Final Python Project: CampusConnect Management System
# ============================================================
# CAMPUSCONNECT MANAGEMENT SYSTEM
# Final College Python Project
# ============================================================

import json
import os

DATA_FILE = "campusconnect_data.json"


# -------------------- DATA FUNCTIONS --------------------

def load_data():
    """Load saved data from JSON file."""
    if os.path.exists(DATA_FILE):
        try:
            with open(DATA_FILE, "r") as file:
                return json.load(file)
        except:
            pass

    return {
        "students": {},
        "courses": {}
    }


def save_data(data):
    """Save all data to JSON file."""
    with open(DATA_FILE, "w") as file:
        json.dump(data, file, indent=4)


data = load_data()


# -------------------- STUDENT FUNCTIONS --------------------

def add_student():
    print("\n--- ADD STUDENT ---")

    student_id = input("Enter Student ID: ").strip()

    if student_id in data["students"]:
        print("❌ Student ID already exists!")
        return

    name = input("Enter Full Name: ").strip()
    program = input("Enter Program/Course: ").strip()
    year = input("Enter Year Level: ").strip()

    data["students"][student_id] = {
        "name": name,
        "program": program,
        "year": year,
        "courses": {},
        "grades": {}
    }

    save_data(data)
    print("✅ Student successfully added!")


def view_students():
    print("\n--- ALL STUDENTS ---")

    if not data["students"]:
        print("No students found.")
        return

    print("-" * 65)
    print(f"{'ID':<15} {'NAME':<25} {'PROGRAM':<15} {'YEAR'}")
    print("-" * 65)

    for student_id, student in data["students"].items():
        print(
            f"{student_id:<15} "
            f"{student['name']:<25} "
            f"{student['program']:<15} "
            f"{student['year']}"
        )


def search_student():
    print("\n--- SEARCH STUDENT ---")

    keyword = input("Enter Student ID or Name: ").lower()
    found = False

    for student_id, student in data["students"].items():
        if keyword in student_id.lower() or keyword in student["name"].lower():

            print("\nStudent Found!")
            print("-" * 35)
            print("Student ID:", student_id)
            print("Name:", student["name"])
            print("Program:", student["program"])
            print("Year Level:", student["year"])

            found = True

    if not found:
        print("❌ No student found.")


def delete_student():
    print("\n--- DELETE STUDENT ---")

    student_id = input("Enter Student ID: ").strip()

    if student_id in data["students"]:
        confirm = input("Are you sure? (yes/no): ").lower()

        if confirm == "yes":
            del data["students"][student_id]
            save_data(data)
            print("✅ Student deleted successfully!")
        else:
            print("Deletion cancelled.")
    else:
        print("❌ Student not found.")


# -------------------- COURSE FUNCTIONS --------------------

def add_course():
    print("\n--- ADD COURSE ---")

    course_code = input("Enter Course Code: ").upper().strip()

    if course_code in data["courses"]:
        print("❌ Course already exists!")
        return

    course_name = input("Enter Course Name: ").strip()

    while True:
        try:
            units = int(input("Enter Number of Units: "))
            break
        except ValueError:
            print("Please enter a valid number.")

    data["courses"][course_code] = {
        "name": course_name,
        "units": units
    }

    save_data(data)
    print("✅ Course successfully added!")


def view_courses():
    print("\n--- AVAILABLE COURSES ---")

    if not data["courses"]:
        print("No courses available.")
        return

    print("-" * 55)
    print(f"{'CODE':<15} {'COURSE NAME':<30} {'UNITS'}")
    print("-" * 55)

    for code, course in data["courses"].items():
        print(
            f"{code:<15} "
            f"{course['name']:<30} "
            f"{course['units']}"
        )


# -------------------- ENROLLMENT FUNCTIONS --------------------

def enroll_student():
    print("\n--- ENROLL STUDENT IN COURSE ---")

    student_id = input("Enter Student ID: ").strip()

    if student_id not in data["students"]:
        print("❌ Student not found.")
        return

    view_courses()

    course_code = input("\nEnter Course Code: ").upper().strip()

    if course_code not in data["courses"]:
        print("❌ Course not found.")
        return

    data["students"][student_id]["courses"][course_code] = \
        data["courses"][course_code]["name"]

    save_data(data)

    print("✅ Student successfully enrolled!")


def view_student_courses():
    print("\n--- VIEW STUDENT COURSES ---")

    student_id = input("Enter Student ID: ").strip()

    if student_id not in data["students"]:
        print("❌ Student not found.")
        return

    student = data["students"][student_id]

    print("\nStudent:", student["name"])
    print("Enrolled Courses:")

    if not student["courses"]:
        print("No enrolled courses.")
        return

    for code, name in student["courses"].items():
        print(f"• {code} - {name}")


# -------------------- GRADING FUNCTIONS --------------------

def add_grade():
    print("\n--- RECORD STUDENT GRADE ---")

    student_id = input("Enter Student ID: ").strip()

    if student_id not in data["students"]:
        print("❌ Student not found.")
        return

    student = data["students"][student_id]

    if not student["courses"]:
        print("❌ Student is not enrolled in any course.")
        return

    print("\nEnrolled Courses:")
    for code, name in student["courses"].items():
        print(f"{code} - {name}")

    course_code = input("\nEnter Course Code: ").upper().strip()

    if course_code not in student["courses"]:
        print("❌ Student is not enrolled in this course.")
        return

    while True:
        try:
            grade = float(input("Enter Grade (0-100): "))

            if 0 <= grade <= 100:
                break
            else:
                print("Grade must be between 0 and 100.")

        except ValueError:
            print("Please enter a valid number.")

    student["grades"][course_code] = grade

    save_data(data)
    print("✅ Grade successfully recorded!")


def calculate_status(grade):
    if grade >= 75:
        return "PASSED"
    return "FAILED"


def student_report():
    print("\n--- STUDENT ACADEMIC REPORT ---")

    student_id = input("Enter Student ID: ").strip()

    if student_id not in data["students"]:
        print("❌ Student not found.")
        return

    student = data["students"][student_id]

    print("\n" + "=" * 55)
    print("          CAMPUSCONNECT ACADEMIC REPORT")
    print("=" * 55)

    print("Student ID:", student_id)
    print("Name:", student["name"])
    print("Program:", student["program"])
    print("Year Level:", student["year"])

    print("\n" + "-" * 55)
    print(f"{'COURSE':<15} {'GRADE':<15} {'STATUS'}")
    print("-" * 55)

    grades = []

    for course, grade in student["grades"].items():
        status = calculate_status(grade)

        print(
            f"{course:<15} "
            f"{grade:<15.2f} "
            f"{status}"
        )

        grades.append(grade)

    if grades:
        average = sum(grades) / len(grades)

        print("-" * 55)
        print(f"GENERAL AVERAGE: {average:.2f}")
        print("OVERALL STATUS:", calculate_status(average))
    else:
        print("No grades recorded yet.")

    print("=" * 55)


# -------------------- SYSTEM STATISTICS --------------------

def system_statistics():
    print("\n--- SYSTEM STATISTICS ---")

    total_students = len(data["students"])
    total_courses = len(data["courses"])

    total_enrollments = 0

    for student in data["students"].values():
        total_enrollments += len(student["courses"])

    print("\n📊 CAMPUSCONNECT STATISTICS")
    print("-" * 35)
    print("Total Students:", total_students)
    print("Total Courses:", total_courses)
    print("Total Enrollments:", total_enrollments)
    print("-" * 35)


# -------------------- MAIN MENU --------------------

def main_menu():

    while True:

        print("\n" + "=" * 55)
        print("        🎓 CAMPUSCONNECT MANAGEMENT SYSTEM")
        print("=" * 55)

        print("""
1.  Add Student
2.  View All Students
3.  Search Student
4.  Delete Student

5.  Add Course
6.  View Courses

7.  Enroll Student
8.  View Student Courses

9.  Record Grade
10. Student Academic Report

11. System Statistics
0.  Exit System
""")

        choice = input("Enter your choice: ").strip()

        if choice == "1":
            add_student()

        elif choice == "2":
            view_students()

        elif choice == "3":
            search_student()

        elif choice == "4":
            delete_student()

        elif choice == "5":
            add_course()

        elif choice == "6":
            view_courses()

        elif choice == "7":
            enroll_student()

        elif choice == "8":
            view_student_courses()

        elif choice == "9":
            add_grade()

        elif choice == "10":
            student_report()

        elif choice == "11":
            system_statistics()

        elif choice == "0":
            print("\nThank you for using CampusConnect!")
            print("System shutting down...")
            break

        else:
            print("❌ Invalid choice. Please try again.")


# -------------------- RUN PROGRAM --------------------

if __name__ == "__main__":
    main_menu()
