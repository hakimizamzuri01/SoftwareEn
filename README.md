# Hotel Reservation System with Firebase and tkinter GUI
## Group 9 by Muhammad Hakimi, Bimo Pranata, Adib Naufal, Aideal Hakimi

## get started
## Initial setup
* We need to use python as for our project and to make the experince easier we are going to use an IDE such as:
  - [VScode](https://visualstudio.microsoft.com/downloads/).
  - [Pycharm](https://www.jetbrains.com/pycharm/download/?section=windows)
  - [Thonny](https://thonny.org/) for linux user 

* Cloud database(FIrebase)
  - [Firebase](https://console.firebase.google.com/u/0/) use this link.
  - after registering start a new project.
![Screenshot 2024-01-21 021843](https://github.com/hakimizamzuri01/SoftwareEn/assets/74414164/dd96bac5-a065-4726-bcbc-7ebc48e1001a)
  - after entering choose the Firestore database because this is what we going to use for our database
![Screenshot 2024-01-21 022216](https://github.com/hakimizamzuri01/SoftwareEn/assets/74414164/55201ba8-8f21-4fa0-97f7-e6e27abd74e4)
  - once enter, you will be greated by create database pop up choose your location and proceed
  - then go to Project Overview>>Project Setting>>Service Account>> Choose the python option and generate new private key

![Screenshot 2024-01-21 022944](https://github.com/hakimizamzuri01/SoftwareEn/assets/74414164/ae01b6b7-fa09-4246-84e4-089fee0c8b3d)
  -the json file is IMPORTANT to allow us to use(UPDATE,DELETE,GET) from database from python IDE

    
## Pyhton Part1(database)
* install the firebase_admin library in python
```bash
pip install firebase-admin
```
* Import the library in python enviorment
```bash
import firebase_admin
from firebase_admin import credentials
from firebase_admin import firestore
from google.cloud.firestore_v1.base_query import FieldFilter, Or
#
cred = credentials.Certificate(r"Put_json_file_here.json")
firebase_admin.initialize_app(cred)
db = firestore.client()
```
* Code function for adding to the database
```bash
def add_data_to_firestore(collection,document):

    # Collection and document are your directory inside the firestore
    doc_ref = db.collection(collection).document(document)
    doc_ref.set({
    #thise is what is inside that you need to put
        'field_name': 'field_value'
    })
# this is the call function code
add_data_to_firestore()
```
* Code function for get data from the database
```bash
#this is for the entire colection
def get_all_docs(collectionName):
    # Get the reference to the collection
    # collection_ref = db.collection(collectionName)
    docs = (
        db.collection(collectionName)
        .stream()
    )

    # Iterate over the documents and store their IDs and data in a list
    documents_list = []
    for doc in docs:
	#doc.to_dict() is the function to able to read the database code structure
        doc_data = doc.to_dict()
        doc_data['id'] = doc.id
        doc_data['docData'] = doc._data
        # print(doc._data)
        documents_list.append(doc_data)
    # Print the list of documents
    for doc_data in documents_list:
        print(f"Document ID: {doc_data['id']}")
        print(f"Document Data: {doc_data['docData']}")
        print()

```
* for selected collection document
```bash
#this is for the selected colection
def get_document(collection_name, document_id):
    doc_ref = db.collection(collection_name).document(document_id)

    print(doc_ref)

    doc = doc_ref.get()

    print(doc)

    if doc.exists:

        return doc.to_dict()

    else:

        print(f"Document '{document_id}' not found in collection '{collection_name}'.")

        return None

```

* deleting database 
```bash
#this is for the selected colection
def get_document(collection_name, document_id):
    doc_ref = db.collection(collection_name).document(document_id)

    print(doc_ref)

    doc = doc_ref.get()

    print(doc)

    if doc.exists:

        return doc.to_dict()

    else:

        print(f"Document '{document_id}' not found in collection '{collection_name}'.")

        return None

```


## Pyhton Part2(GUI)

* install the library tkinter in the python enviorment
```bash
#intall tkinter library
pip install tkinter
```
* initialize the library
```bash
import tkinter as tk
from tkinter import messagebox

# Dictionary to store registered users
registered_users = {}

# Dictionary to store booked rooms
booked_rooms = {}

# Define show_rooms_button, show_booked_rooms_button, and sign_out_button as global variables
show_rooms_button = None
show_booked_rooms_button = None
sign_out_button = None
rooms_window = None  # Initialize rooms_window to None
```
* example of the UI code
```bash

def display_rooms():
    global rooms_window  # Use the global keyword to update the global variable
    rooms_window = tk.Toplevel(root)
    rooms_window.title("Rooms List")

    # Create a text widget to display the list of rooms
    room_list = tk.Text(rooms_window, height=10, width=30)
    room_list.pack(padx=10, pady=10)

    # Pricing for each room
    room_price = 100

    # Insert room information into the text widget, excluding booked rooms
    for room_number in range(1, 11):
        if room_number not in booked_rooms:
            room_list.insert(tk.END, f"Room {room_number} - ${room_price}\n")

    # Dropdown list to choose a room
    chosen_room_var = tk.StringVar()
    chosen_room_var.set("Choose a room")
    room_dropdown = tk.OptionMenu(rooms_window, chosen_room_var, *range(1, 11))
    room_dropdown.pack(pady=10)

    # Dropdown list for stay duration
    stay_duration_label = tk.Label(rooms_window, text="Stay Duration (days)")
    stay_duration_label.pack()

    stay_duration_var = tk.StringVar()
    stay_duration_var.set(1)  # Default stay duration is 1 day
    stay_duration_dropdown = tk.OptionMenu(rooms_window, stay_duration_var, *range(1, 6))
    stay_duration_dropdown.pack(pady=10)

    def book_room():
        global rooms_window  # Use the global keyword to access the global variable
        chosen_room = chosen_room_var.get()
        stay_duration = stay_duration_var.get()

        if chosen_room.isdigit():
            chosen_room = int(chosen_room)
            if chosen_room not in booked_rooms:
                booked_rooms[chosen_room] = {"status": "Booked", "stay_duration": stay_duration}
                messagebox.showinfo("Room Booking", f"Room {chosen_room} booked successfully for {stay_duration} days!")
                if rooms_window:
                    rooms_window.destroy()  # Close the booking room window after successful booking
                    confirm_booking_button.config(state=tk.NORMAL)  # Enable "Confirm Booking" button after room selection
            else:
                messagebox.showwarning("Room Booking", f"Room {chosen_room} is already booked.")
        else:
            messagebox.showerror("Room Booking", "Please choose a valid room.")

    # Button to book the selected room
    book_button = tk.Button(rooms_window, text="Book Room", command=book_room)
    book_button.pack()


def display_booked_rooms():
    # Create a new window to display the list of booked rooms
    booked_rooms_window = tk.Toplevel(root)
    booked_rooms_window.title("Booked Rooms")

    # Create a text widget to display the list of booked rooms
    booked_rooms_list = tk.Text(booked_rooms_window, height=10, width=60)
    booked_rooms_list.pack(padx=10, pady=10)

    # Insert booked room information into the text widget
    for room_number, status in booked_rooms.items():
        booked_rooms_list.insert(tk.END, f"Room {room_number} - {status}\n")

def sign_out():
    result = messagebox.askquestion("Sign Out", "Are you sure you want to sign out?")
    if result == "yes":
        root.deiconify()
        root.title("Hotel Room List")
        show_rooms_button.config(state=tk.DISABLED)
        show_booked_rooms_button.config(state=tk.DISABLED)
        sign_out_button.config(state=tk.DISABLED)
        confirm_booking_button.config(state=tk.DISABLED)  # Disable "Confirm Booking" button after signing out

def login():
    username = username_entry.get()
    password = password_entry.get()

    # Check if the username exists in registered_users and the password matches
    if username in registered_users and registered_users[username]["password"] == password:
        messagebox.showinfo("Login Successful", "Welcome, " + username + "!")
        show_rooms_button.config(state=tk.NORMAL)  # Enable "Show Rooms" button after login
        show_booked_rooms_button.config(state=tk.NORMAL)  # Enable "Show Booked Rooms" button after login
        sign_out_button.config(state=tk.NORMAL)  # Enable "Sign Out" button after login
        root.deiconify()  # Deiconify the main window
        login_screen.destroy()  # Destroy the login screen
    else:
        messagebox.showerror("Login Failed", "Invalid username or password")


def create_login_screen():
    global login_screen
    login_screen = tk.Toplevel(root)
    login_screen.title("Login")
    login_screen.geometry("300x200")  # Set width and height

    tk.Label(login_screen, text="Username").pack()
    global username_entry
    username_entry = tk.Entry(login_screen)
    username_entry.pack()

    tk.Label(login_screen, text="Password").pack()
    global password_entry
    password_entry = tk.Entry(login_screen, show="*")
    password_entry.pack()

    tk.Button(login_screen, text="Login", command=login).pack(side=tk.LEFT)
    tk.Button(login_screen, text="Cancel", command=cancel_login).pack(side=tk.RIGHT)

def cancel_login():
    login_screen.destroy()
    root.deiconify()

def show_registration_screen():
    registration_screen = tk.Toplevel(root)
    registration_screen.title("Register")
    registration_screen.geometry("300x200")  # Set width and height

    tk.Label(registration_screen, text="Enter details for registration:").pack()

    tk.Label(registration_screen, text="Email").pack()
    email_entry = tk.Entry(registration_screen)
    email_entry.pack()

    tk.Label(registration_screen, text="Username").pack()
    new_username_entry = tk.Entry(registration_screen)
    new_username_entry.pack()

    tk.Label(registration_screen, text="Password").pack()
    new_password_entry = tk.Entry(registration_screen, show="*")
    new_password_entry.pack()

    tk.Button(registration_screen, text="Register", command=lambda: register_user(email_entry.get(), new_username_entry.get(), new_password_entry.get(), registration_screen)).pack(side=tk.LEFT)
    tk.Button(registration_screen, text="Cancel", command=lambda: cancel_registration(registration_screen)).pack(side=tk.RIGHT)

def cancel_registration(registration_screen):
    registration_screen.destroy()

def register_user(email, new_username, new_password, registration_screen):
    # Store the new user in the dictionary
    registered_users[new_username] = {"email": email, "password": new_password}
    messagebox.showinfo("Registration", f"Registration successful!\nEmail: {email}\nUsername: {new_username}\nPassword: {new_password}")
    registration_screen.destroy()

# Create the main window
root = tk.Tk()
root.title("Hotel Room List")

# Make the main window 50% smaller
new_width = int(root.winfo_screenwidth() * 0.5)
new_height = int(root.winfo_screenheight() * 0.5)
root.geometry(f"{new_width}x{new_height}")

# Welcome message
welcome_label = tk.Label(root, text="Welcome to Hotel California!", font=("Jasmine And Greentea", 24))
welcome_label.pack(pady=20)

# Create buttons for login and register
login_button = tk.Button(root, text="Login", command=create_login_screen, width=15, height=2)
login_button.pack(side=tk.LEFT, padx=10)

register_button = tk.Button(root, text="Register", command=show_registration_screen, width=15, height=2)
register_button.pack(side=tk.RIGHT, padx=10)

# Create a button to display the list of rooms
show_rooms_button = tk.Button(root, text="Show Rooms", command=display_rooms, state=tk.DISABLED)
show_rooms_button.pack(pady=5)

# Create a button to display the list of booked rooms
show_booked_rooms_button = tk.Button(root, text="Show Booked Rooms", command=display_booked_rooms, state=tk.DISABLED)
show_booked_rooms_button.pack(pady=5)

# Function to calculate the total price of booked rooms
# Pricing for each room
room_price = 100

# Function to calculate the total price of booked rooms
def calculate_total_price():
    total_price = 0

    # Calculate total price based on booked rooms and stay duration
    for room_number, booking_info in booked_rooms.items():
        total_price += room_price * int(booking_info["stay_duration"])

    return total_price

# Function to confirm booking and display total price
def confirm_booking():
    total_price = calculate_total_price()

    # Ask the user to confirm the booking
    result = messagebox.askyesno("Confirm Booking", f"Total Price: ${total_price}\nDo you want to confirm the booking?")

    if result:
        messagebox.showinfo("Booking Confirmed", "Your booking has been confirmed!\nWe will contact your email shortly.")
    else:
        messagebox.showinfo("Booking Cancelled", "Your booking is not confirmed.")

# Create a button to confirm booking
confirm_booking_button = tk.Button(root, text="Confirm Booking", command=confirm_booking, state=tk.DISABLED)
confirm_booking_button.pack(pady=5)

# Create a button to sign out
sign_out_button = tk.Button(root, text="Sign Out", command=sign_out, state=tk.DISABLED)
sign_out_button.pack(pady=5)


# Run the Tkinter event loop
root.mainloop()

```

* Final code(example of when we combine partA+partB)


```bash
import tkinter as tk
from tkinter import messagebox
import firebase_admin
from firebase_admin import credentials
from firebase_admin import firestore
from google.cloud.firestore_v1.base_query import FieldFilter, Or
cred = credentials.Certificate(r"hotel room system.json")
firebase_admin.initialize_app(cred)
db = firestore.client()


# Dictionary to store registered users
registered_users = {}

# Define show_rooms_button, show_booked_rooms_button, and sign_out_button as global variables
show_rooms_button = None
show_booked_rooms_button = None
sign_out_button = None
rooms_window = None  # Initialize rooms_window to None

def get_price():
    Room_price=[]
    rooms_ref = db.collection('rooms')
    query = rooms_ref.where(filter=FieldFilter("available", "==", "1"))
    result = query.get()
    for room in result:
        price=room.get('room_price')
        Room_price.append(price)
    return Room_price


def get_room_number():
    Room_id=[]
    rooms_ref = db.collection('rooms')
    query = rooms_ref.where(filter=FieldFilter("available", "==", "1"))
    result = query.get()
    for room in result:
        id=room.get('room_id')
        Room_id.append(id)

    return Room_id

def get_room_unavailable():
    Unavail_id=[]
    rooms_ref = db.collection('rooms')
    query = rooms_ref.where(filter=FieldFilter("available", "==", "0"))
    result = query.get()
    for room in result:
        Un=room.get('room_id')
        Unavail_id.append(Un)

    return Unavail_id


# Dictionary to store booked rooms
#nobook=[]
#nb=get_room_unavailable()
#nobook.append(nb)
booked_rooms = {}
def display_rooms():
    global rooms_window  # Use the global keyword to update the global variable
    rooms_window = tk.Toplevel(root)
    rooms_window.title("Rooms List")

    # Create a text widget to display the list of rooms
    room_list = tk.Text(rooms_window, height=10, width=30)
    room_list.pack(padx=10, pady=10)

    # Pricing for each room
    room_price== 100
    id=get_room_number()


    # Insert room information into the text widget, excluding booked rooms
    for room_number in id:
        if room_number not in booked_rooms:
            room_list.insert(tk.END, f"Room {room_number} - ${room_price}\n")

    # Dropdown list to choose a room
    chosen_room_var = tk.StringVar()
    chosen_room_var.set("Choose a room")
    room_dropdown = tk.OptionMenu(rooms_window, chosen_room_var, *id)
    room_dropdown.pack(pady=10)

    # Dropdown list for stay duration
    stay_duration_label = tk.Label(rooms_window, text="Stay Duration (days)")
    stay_duration_label.pack()

    stay_duration_var = tk.StringVar()
    stay_duration_var.set(1)  # Default stay duration is 1 day
    stay_duration_dropdown = tk.OptionMenu(rooms_window, stay_duration_var, *range(1, 6))
    stay_duration_dropdown.pack(pady=10)

    def book_room():
        global rooms_window  # Use the global keyword to access the global variable
        global chosen_room
        chosen_room = chosen_room_var.get()
        stay_duration = stay_duration_var.get()

        if chosen_room.isdigit():
            chosen_room = int(chosen_room)
            if chosen_room not in booked_rooms:
                booked_rooms[chosen_room] = {"status": "Booked", "stay_duration": stay_duration}
                messagebox.showinfo("Room Booking", f"Room {chosen_room} booked successfully for {stay_duration} days!")
                if rooms_window:
                    rooms_window.destroy()  # Close the booking room window after successful booking
                    confirm_booking_button.config(state=tk.NORMAL)  # Enable "Confirm Booking" button after room selection
            else:
                messagebox.showwarning("Room Booking", f"Room {chosen_room} is already booked.")
        else:
            messagebox.showerror("Room Booking", "Please choose a valid room.")

    # Button to book the selected room
    book_button = tk.Button(rooms_window, text="Book Room", command=book_room)
    book_button.pack()


def display_booked_rooms():
    # Create a new window to display the list of booked rooms
    booked_rooms_window = tk.Toplevel(root)
    booked_rooms_window.title("Booked Rooms")

    # Create a text widget to display the list of booked rooms
    booked_rooms_list = tk.Text(booked_rooms_window, height=10, width=60)
    booked_rooms_list.pack(padx=10, pady=10)

    # Insert booked room information into the text widget
    for room_number, status in booked_rooms.items():
        booked_rooms_list.insert(tk.END, f"Room {room_number} - {status}\n")


def update_existing_document(roomid,name):
    # Get the reference to the collection
    collection_ref = db.collection('rooms')
    # Get the document you want to update by its ID
    doc_ref = collection_ref.document(roomid)
    # Update the document
    doc_ref.update({
        'Name': name
    })
def sign_out():
    result = messagebox.askquestion("Sign Out", "Are you sure you want to sign out?")
    if result == "yes":
        root.deiconify()
        root.title("Hotel Room List")
        show_rooms_button.config(state=tk.DISABLED)
        show_booked_rooms_button.config(state=tk.DISABLED)
        sign_out_button.config(state=tk.DISABLED)
        confirm_booking_button.config(state=tk.DISABLED)  # Disable "Confirm Booking" button after signing out

def login():
    global username
    username = username_entry.get()
    password = password_entry.get()
    # Check if the username exists in registered_users and the password matches
    if username in registered_users and registered_users[username]["password"] == password:
        messagebox.showinfo("Login Successful", "Welcome, " + username + "!")
        show_rooms_button.config(state=tk.NORMAL)  # Enable "Show Rooms" button after login
        show_booked_rooms_button.config(state=tk.NORMAL)  # Enable "Show Booked Rooms" button after login
        sign_out_button.config(state=tk.NORMAL)  # Enable "Sign Out" button after login
        root.deiconify()  # Deiconify the main window
        login_screen.destroy()  # Destroy the login screen
    else:
        messagebox.showerror("Login Failed", "Invalid username or password")


def create_login_screen():
    global login_screen
    login_screen = tk.Toplevel(root)
    login_screen.title("Login")
    login_screen.geometry("300x200")  # Set width and height

    tk.Label(login_screen, text="Username").pack()
    global username_entry
    username_entry = tk.Entry(login_screen)
    username_entry.pack()

    tk.Label(login_screen, text="Password").pack()
    global password_entry
    password_entry = tk.Entry(login_screen, show="*")
    password_entry.pack()

    tk.Button(login_screen, text="Login", command=login).pack(side=tk.LEFT)
    tk.Button(login_screen, text="Cancel", command=cancel_login).pack(side=tk.RIGHT)

def cancel_login():
    login_screen.destroy()
    root.deiconify()

def show_registration_screen():
    registration_screen = tk.Toplevel(root)
    registration_screen.title("Register")
    registration_screen.geometry("300x200")  # Set width and height

    tk.Label(registration_screen, text="Enter details for registration:").pack()

    tk.Label(registration_screen, text="Email").pack()
    email_entry = tk.Entry(registration_screen)
    email_entry.pack()

    tk.Label(registration_screen, text="Username").pack()
    new_username_entry = tk.Entry(registration_screen)
    new_username_entry.pack()

    tk.Label(registration_screen, text="Password").pack()
    new_password_entry = tk.Entry(registration_screen, show="*")
    new_password_entry.pack()

    tk.Button(registration_screen, text="Register", command=lambda: register_user(email_entry.get(), new_username_entry.get(), new_password_entry.get(), registration_screen)).pack(side=tk.LEFT)
    tk.Button(registration_screen, text="Cancel", command=lambda: cancel_registration(registration_screen)).pack(side=tk.RIGHT)
    return(new_username_entry)

def cancel_registration(registration_screen):
    registration_screen.destroy()

def register_user(email, new_username, new_password, registration_screen):
    # Store the new user in the dictionary
    registered_users[new_username] = {"email": email, "password": new_password}
    messagebox.showinfo("Registration", f"Registration successful!\nEmail: {email}\nUsername: {new_username}\nPassword: {new_password}")
    registration_screen.destroy()

# Create the main window
root = tk.Tk()
root.title("Hotel Room List")

# Make the main window 50% smaller
new_width = int(root.winfo_screenwidth() * 0.5)
new_height = int(root.winfo_screenheight() * 0.5)
root.geometry(f"{new_width}x{new_height}")

# Welcome message
welcome_label = tk.Label(root, text="Welcome to Hotel California!", font=("Jasmine And Greentea", 24))
welcome_label.pack(pady=20)

# Create buttons for login and register
login_button = tk.Button(root, text="Login", command=create_login_screen, width=15, height=2)
login_button.pack(side=tk.LEFT, padx=10)

register_button = tk.Button(root, text="Register", command=show_registration_screen, width=15, height=2)
register_button.pack(side=tk.RIGHT, padx=10)

# Create a button to display the list of rooms
show_rooms_button = tk.Button(root, text="Show Rooms", command=display_rooms, state=tk.DISABLED)
show_rooms_button.pack(pady=5)

# Create a button to display the list of booked rooms
show_booked_rooms_button = tk.Button(root, text="Show Booked Rooms", command=display_booked_rooms, state=tk.DISABLED)
show_booked_rooms_button.pack(pady=5)

# Function to calculate the total price of booked rooms
# Pricing for each room
room_price = 100

# Function to calculate the total price of booked rooms
def calculate_total_price():
    total_price = 0

    # Calculate total price based on booked rooms and stay duration
    for room_number, booking_info in booked_rooms.items():
        total_price += room_price * int(booking_info["stay_duration"])

    return total_price

# Function to confirm booking and display total price
def confirm_booking():
    global total_Price
    total_Price = calculate_total_price()

    # Ask the user to confirm the booking
    result = messagebox.askyesno("Confirm Booking", f"Total Price: ${total_Price}\nDo you want to confirm the booking?")

    if result:
        messagebox.showinfo("Booking Confirmed", "Your booking has been confirmed!\nWe will contact your email shortly.")
    else:
        messagebox.showinfo("Booking Cancelled", "Your booking is not confirmed.")

# Create a button to confirm booking
confirm_booking_button = tk.Button(root, text="Confirm Booking", command=confirm_booking, state=tk.DISABLED)
confirm_booking_button.pack(pady=5)

# Create a button to sign out
sign_out_button = tk.Button(root, text="Sign Out", command=sign_out, state=tk.DISABLED)
sign_out_button.pack(pady=5)

# Run the Tkinter event loop
root.mainloop()
```

> **Note**
> You can also get the code in from the script hotel.py.


