3# Student Grade Calculator
# Module: CMP-L031-0 Software Development - Coursework 1-A
# Name: YOUR NAME
# Student ID: YOUR STUDENT ID
# Last updated: DD Mon YYYY
#
# This program asks for the details of up to 3 students, works out
# their overall score, category and age, shows the results in a table
# and saves the table to students.txt.

from datetime import datetime, date
from tabulate import tabulate


# Works out the weighted overall score and returns it as a whole number
def calculate_overall_score(cw1, cw2, cw3, test):
    score = (cw1 * 0.10) + (cw2 * 0.20) + (cw3 * 0.30) + (test * 0.40)
    # round() in Python turns 72.5 into 72, so I add 0.5 to round up
    return int(score + 0.5)


# Finds the category for an overall score, e.g. 75 gives "72- First"
def determine_category(score):
    if score == 100:
        return "100- Aurum Standard"
    elif score >= 82:
        return "82- Upper First"
    elif score >= 72:
        return "72- First"
    elif score >= 62:
        return "62- 2:1"
    elif score >= 52:
        return "52- 2:2"
    elif score >= 42:
        return "42- Third"
    elif score >= 32:
        return "32- Condonable Fail"
    elif score > 0:
        return "5- Fail"
    else:
        return "0- Deficit Opus"


# Works out a student's age in years from their date of birth
def calculate_age(dob):
    today = date.today()
    age = today.year - dob.year
    # If the birthday has not happened yet this year, take one off
    if (today.month, today.day) < (dob.month, dob.day):
        age = age - 1
    return age


# Asks for a student ID until a valid one is entered (or 'end')
def get_student_id(used_ids):
    while True:
        student_id = input("Enter student ID (or 'end' to stop): ").strip()
        if student_id.lower() == "end":
            return "end"
        if student_id == "":
            print("ID cannot be empty. Try again.")
        elif not student_id.isalnum():
            print("ID can only have letters and numbers. Try again.")
        elif len(student_id) < 4 or len(student_id) > 10:
            print("ID must be 4 to 10 characters long. Try again.")
        elif student_id in used_ids:
            print("This ID has already been entered. Try again.")
        else:
            return student_id


# Asks for the student's name until it is valid
# (letters, plus spaces, - and ' for names like Mary-Jane O'Brien)
def get_name():
    while True:
        name = input("Enter student name: ").strip()
        letters = name.replace(" ", "").replace("-", "").replace("'", "")
        if letters.isalpha():
            return name
        print("Name should only contain letters, spaces, - or '. Try again.")


# Asks for a date of birth until a real, sensible date is entered
def get_dob():
    while True:
        dob_text = input("Enter date of birth (DD/MM/YYYY): ").strip()
        try:
            dob = datetime.strptime(dob_text, "%d/%m/%Y").date()
        except ValueError:
            print("Wrong date format. Use DD/MM/YYYY. Try again.")
            continue
        if dob > date.today():
            print("Date of birth cannot be in the future. Try again.")
        elif calculate_age(dob) < 16 or calculate_age(dob) > 100:
            print("Age must be between 16 and 100. Try again.")
        else:
            return dob


# Asks for one score until a number from 0 to 100 is entered
def get_score(part_name):
    while True:
        try:
            score = float(input("Enter " + part_name + " score (0-100): "))
        except ValueError:
            print("Please enter a number. Try again.")
            continue
        if score >= 0 and score <= 100:
            return score
        print("Score must be between 0 and 100. Try again.")


# Main program: collects up to 3 students, then shows and saves the table
def main():
    students = []    # list of dictionaries, one dictionary per student
    used_ids = []    # to stop the same ID being entered twice

    print("Student Grade Calculator")
    print("You can enter up to 3 students.")

    while len(students) < 3:
        print("\nStudent", len(students) + 1)
        student_id = get_student_id(used_ids)
        if student_id == "end":
            break

        name = get_name()
        dob = get_dob()
        cw1 = get_score("Coursework 1")
        cw2 = get_score("Coursework 2")
        cw3 = get_score("Coursework 3")
        test = get_score("Final Test")

        overall = calculate_overall_score(cw1, cw2, cw3, test)

        student = {
            "ID": student_id,
            "Name": name,
            "D.o.B": dob.strftime("%d/%m/%Y"),
            "Age": calculate_age(dob),
            "CW1": cw1,
            "CW2": cw2,
            "CW3": cw3,
            "Test": test,
            "Overall": overall,
            "Category": determine_category(overall)
        }
        students.append(student)
        used_ids.append(student_id)

    if len(students) == 0:
        print("No students were entered.")
        return

    table = tabulate(students, headers="keys", tablefmt="grid")
    print()
    print(table)

    file = open("students.txt", "w")
    file.write(table)
    file.close()
    print("Table saved to students.txt")


# Only run main() when this file is run directly
if __name__ == "__main__":
    main()        
