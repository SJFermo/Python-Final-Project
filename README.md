schedule = {"projector": {}, "view board": {}}
resource_counts = {"projector": 10, "view board": 10}
credentials = {}
current_user = None  

def start():
    """Entry point for the scheduler."""
    print("Welcome to the CICS Scheduler!")
    print("Manage your resource reservations with ease.")
    while True:
        print("\n1. Login\n2. Register\n3. Exit")
        choice = input("Choose an option: ").strip()
        if choice == "1":
            login()
        elif choice == "2":
            register()
        elif choice == "3":
            print("Goodbye!")
            break
        else:
            print("Invalid choice. Please try again.")

def login():
    """Authenticate the user."""
    global current_user  
    username = input("Enter username: ").strip()
    password = input("Enter password: ").strip()

    if credentials.get(username) == password:
        current_user = username
        print(f"Login successful! Welcome, {current_user}.")
        main_menu()
    else:
        print("Invalid username or password. Please try again.")

def register():
    """Register a new user."""
    username = input("Enter a username: ").strip()
    if username in credentials:
        print("Username already exists!")
        return

    password = input("Enter a password: ").strip()
    credentials[username] = password
    print("Registration successful!")


def main_menu():
    """Main menu for resource management."""
    global current_user

    if not current_user:
        print("You must log in first!")
        return

    while True:
        print("\n1. Reserve Resource")
        print("2. View Available Slots")
        print("3. Cancel Reservation")
        print("4. View Reservations")
        print("5. Logout")
        choice = input("Choose an option: ").strip()

        if choice == "1":
            reserve()
        elif choice == "2":
            view_slots()
        elif choice == "3":
            cancel_reservation()
        elif choice == "4":
            view_reservations()
        elif choice == "5":
            print(f"Goodbye, {current_user}!")
            current_user = None  
            break
        else:
            print("Invalid choice. Please try again.")

def reserve():
    """Handle resource reservation."""
    if not is_logged_in():
        return

    resource = input("Enter resource (projector/view board): ").strip().lower()
    time_slot = input("Enter time slot (e.g., 8:00 - 9:00): ").strip()
    name = input("Enter your name: ").strip()
    location = input("Enter location: ").strip()

    if resource not in schedule:
        print("Invalid resource.")
        return

    if not name or not location:
        print("Please fill in all fields!")
        return

    if time_slot in schedule[resource]:
        print("Time slot already reserved!")
        return

    schedule[resource][time_slot] = {"name": name, "location": location}
    print("Reservation successful!")

def view_slots():
    """Display available slots along with the count of available resources."""
    if not is_logged_in():
        return

    print("\nAvailable Slots:")
    for resource, slots in schedule.items():
        print(f"\n{resource.capitalize()}:")
        available_count = resource_counts[resource] - len(slots)
        print(f"  Available: {available_count}")
        for hour in range(8, 18):
            time_slot = f"{hour}:00 - {hour+1}:00"
            if time_slot not in slots:
                print(f"  {time_slot}")

def cancel_reservation():
    """Cancel a reservation."""
    if not is_logged_in():
        return

    resource = input("Enter resource (projector/view board): ").strip().lower()
    time_slot = input("Enter time slot (e.g., 8:00 - 9:00): ").strip()

    if resource not in schedule or time_slot not in schedule[resource]:
        print("No reservation found for this slot!")
        return

    del schedule[resource][time_slot]
    print("Reservation canceled!")

def view_reservations():
    """Display current reservations."""
    if not is_logged_in():
        return

    print("\nCurrent Reservations:")
    for resource, slots in schedule.items():
        print(f"\n{resource.capitalize()}:")
        for time_slot, details in slots.items():
            print(f"  {time_slot}: {details['name']} at {details['location']}")

def is_logged_in():
    """Check if the user is logged in and notify them if not."""
    if not current_user:
        print("You must log in to perform this action.")
        return False
    return True

if __name__ == "__main__":
    start()
