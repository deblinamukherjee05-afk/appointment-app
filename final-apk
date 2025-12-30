appointments_list = []

import datetime

def add_appointment(date_str, time_str, client_name, duration, status, notes):
    """Adds a new appointment to the global appointments_list."""
    try:
        appointment = {
            "Date": date_str,
            "Time": time_str,
            "Client Name": client_name,
            "Duration": duration,
            "Status": status,
            "Notes": notes
        }
        appointments_list.append(appointment)
        save_appointments(appointments_list) # Save after adding
        print(f"Appointment for {client_name} on {date_str} at {time_str} added successfully.")
    except Exception as e:
        print(f"Error adding appointment: {e}")

def view_all_appointments():
    """Displays all appointments in the appointments_list."""
    if not appointments_list:
        print("No appointments found.")
        return
    for i, appt in enumerate(appointments_list):
        print(f"Appointment {i+1}:")
        for key, value in appt.items():
            print(f"  {key}: {value}")

def view_appointments_by_date(date_str):
    """Displays appointments scheduled for a specific date."""
    print(f"\n--- Appointments for Date: {date_str} ---")
    found = False
    for i, appt in enumerate(appointments_list):
        if appt.get("Date") == date_str:
            found = True
            print(f"Appointment {i+1}:")
            for key, value in appt.items():
                print(f"  {key}: {value}")
    if not found:
        print(f"No appointments found for {date_str}.")

def view_appointments_by_client(client_name):
    """Displays appointments for a specific client."""
    print(f"\n--- Appointments for Client: {client_name} ---")
    found = False
    for i, appt in enumerate(appointments_list):
        if appt.get("Client Name") == client_name:
            found = True
            print(f"Appointment {i+1}:")
            for key, value in appt.items():
                print(f"  {key}: {value}")
    if not found:
        print(f"No appointments found for client: {client_name}.")

def delete_appointment(client_name_to_delete):
    """Deletes all appointments for a given client name from the appointments_list.
    """
    global appointments_list
    original_count = len(appointments_list)
    appointments_list = [appt for appt in appointments_list if appt.get("Client Name") != client_name_to_delete]
    deleted_count = original_count - len(appointments_list)

    if deleted_count > 0:
        save_appointments(appointments_list) # Save after deleting
        print(f"\nSuccessfully deleted {deleted_count} appointment(s) for client: {client_name_to_delete}.")
    else:
        print(f"\nNo appointments found for client: {client_name_to_delete}.")

# --- Time Slot Generation ---
def generate_time_slots(start_time_str, end_time_str, interval_minutes):
    """Generates a list of time slots at specified intervals."""
    dummy_date = datetime.date(2000, 1, 1)
    start_time = datetime.datetime.strptime(start_time_str, '%H:%M').time()
    end_time = datetime.datetime.strptime(end_time_str, '%H:%M').time()

    current_datetime = datetime.datetime.combine(dummy_date, start_time)
    end_datetime = datetime.datetime.combine(dummy_date, end_time)

    slots = []
    while current_datetime <= end_datetime:
        slots.append(current_datetime.strftime('%H:%M'))
        current_datetime += datetime.timedelta(minutes=interval_minutes)
    return slots

# Generate time slots from 6:30 PM (18:30) to 8:15 PM (20:15) at 15-minute intervals
time_slots = generate_time_slots('18:30', '20:15', 15)

# --- UI Imports and Initial Setup ---
import ipywidgets as widgets
from IPython.display import display, clear_output, HTML

# Output widget for displaying results
output_area = widgets.Output()

# Get current date for default value
current_date_str = datetime.date.today().strftime('%Y-%m-%d')

# --- Add Appointment Widgets ---
add_date = widgets.DatePicker(description='Date:', value=datetime.date.today())
add_time = widgets.Dropdown(description='Time:', options=time_slots, value=time_slots[0] if time_slots else None)
add_client_name = widgets.Text(description='Client Name:')
add_duration = widgets.IntText(description='Duration (min):', value=15, disabled=True)
add_status = widgets.Text(description='Status:', value='Scheduled')
add_notes = widgets.Textarea(description='Notes:')
add_button = widgets.Button(description='Add Appointment')

# --- View Appointment Widgets ---
view_all_button = widgets.Button(description='View All Appointments')
view_by_date_input = widgets.DatePicker(description='View by Date:')
view_by_date_button = widgets.Button(description='View by Date')
view_by_client_input = widgets.Text(description='View by Client Name:')
view_by_client_button = widgets.Button(description='View by Client')

# --- Delete Appointment Widgets ---
delete_client_name = widgets.Text(description='Client Name to Delete:')
delete_button = widgets.Button(description='Delete Appointment(s) by Name')

# --- Layout the widgets ---
add_widgets = widgets.VBox([
    widgets.HTML('<h3>Add New Appointment</h3>'),
    add_date, add_time, add_client_name, add_duration, add_status, add_notes, add_button
])

view_widgets = widgets.VBox([
    widgets.HTML('<h3>View Appointments</h3>'),
    view_all_button,
    widgets.HBox([view_by_date_input, view_by_date_button]),
    widgets.HBox([view_by_client_input, view_by_client_button])
])

delete_widgets = widgets.VBox([
    widgets.HTML('<h3>Delete Appointment(s)</h3>'),
    delete_client_name, delete_button
])

# Display the UI elements
ui = widgets.VBox([add_widgets, view_widgets, delete_widgets, output_area])

# --- Callback Functions ---
def on_add_button_clicked(button):
    with output_area:
        clear_output()
        date_obj = add_date.value
        time_str = add_time.value
        client_name = add_client_name.value
        duration = add_duration.value
        status = add_status.value
        notes = add_notes.value

        if duration > 30:
            print("Error: Duration cannot be more than 30 minutes.")
            return

        date_str = date_obj.strftime('%Y-%m-%d')

        try:
            appointment_datetime = datetime.datetime.strptime(f"{date_str} {time_str}", '%Y-%m-%d %H:%M')
            if appointment_datetime < datetime.datetime.now():
                print("Error: Cannot add an appointment in the past.")
                return
        except ValueError:
            print("Error: Invalid date or time format. Please use YYYY-MM-DD and HH:MM.")
            return

        add_appointment(date_str, time_str, client_name, duration, status, notes)
        add_date.value = datetime.date.today()
        add_time.value = time_slots[0] if time_slots else None
        add_client_name.value = ''
        add_duration.value = 15
        add_status.value = 'Scheduled'
        add_notes.value = ''

add_button.on_click(on_add_button_clicked)

def on_view_all_button_clicked(button):
    with output_area:
        clear_output()
        view_all_appointments()

def on_view_by_date_button_clicked(button):
    with output_area:
        clear_output()
        date_obj = view_by_date_input.value
        if date_obj:
            date_str = date_obj.strftime('%Y-%m-%d')
            view_appointments_by_date(date_str)
        else:
            print("Please select a date.")

def on_view_by_client_button_clicked(button):
    with output_area:
        clear_output()
        client_name = view_by_client_input.value
        view_appointments_by_client(client_name)

view_all_button.on_click(on_view_all_button_clicked)
view_by_date_button.on_click(on_view_by_date_button_clicked)
view_by_client_button.on_click(on_view_by_client_button_clicked)

def on_delete_button_clicked(button):
    with output_area:
        clear_output()
        try:
            client_name_to_delete = delete_client_name.value
            if client_name_to_delete:
                delete_appointment(client_name_to_delete)
                delete_client_name.value = ''
            else:
                print("Please enter a client name to delete.")
        except Exception as e:
            print(f"Error deleting appointment: {e}")

delete_button.on_click(on_delete_button_clicked)

import json

def save_appointments(appointments):
    """Saves the current list of appointments to a JSON file."""
    try:
        with open('appointments.json', 'w') as f:
            json.dump(appointments, f, indent=4)
        print("Appointments saved successfully.")
    except IOError as e:
        print(f"Error saving appointments: {e}")

def load_appointments():
    """Loads appointments from a JSON file."""
    try:
        with open('appointments.json', 'r') as f:
            appointments = json.load(f)
        print("Appointments loaded successfully.")
        return appointments
    except FileNotFoundError:
        print("Starting with an empty list.")
        return []
    except json.JSONDecodeError:
        print("Error decoding JSON from appointments.json. File might be corrupted or empty. Starting with an empty list.")
        return []
    except Exception as e:
        print(f"An unexpected error occurred while loading appointments: {e}")
        return []

# Initialize appointments_list by trying to load from file
appointments_list = load_appointments()

import datetime

def add_appointment(date_str, time_str, client_name, duration, status, notes):
    """Adds a new appointment to the global appointments_list."""
    try:
        appointment = {
            "Date": date_str,
            "Time": time_str,
            "Client Name": client_name,
            "Duration": duration,
            "Status": status,
            "Notes": notes
        }
        appointments_list.append(appointment)
        save_appointments(appointments_list) # Save after adding
        print(f"Appointment for {client_name} on {date_str} at {time_str} added successfully.")
    except Exception as e:
        print(f"Error adding appointment: {e}")

def view_all_appointments():
    """Displays all appointments in the appointments_list."""
    if not appointments_list:
        print("No appointments found.")
        return
    for i, appt in enumerate(appointments_list):
        print(f"Appointment {i+1}:")
        for key, value in appt.items():
            print(f"  {key}: {value}")

def view_appointments_by_date(date_str):
    """Displays appointments scheduled for a specific date."""
    print(f"\n--- Appointments for Date: {date_str} ---")
    found = False
    for i, appt in enumerate(appointments_list):
        if appt.get("Date") == date_str:
            found = True
            print(f"Appointment {i+1}:")
            for key, value in appt.items():
                print(f"  {key}: {value}")
    if not found:
        print(f"No appointments found for {date_str}.")

def view_appointments_by_client(client_name):
    """Displays appointments for a specific client."""
    print(f"\n--- Appointments for Client: {client_name} ---")
    found = False
    for i, appt in enumerate(appointments_list):
        if appt.get("Client Name") == client_name:
            found = True
            print(f"Appointment {i+1}:")
            for key, value in appt.items():
                print(f"  {key}: {value}")
    if not found:
        print(f"No appointments found for client: {client_name}.")

def delete_appointment(client_name_to_delete):
    """Deletes all appointments for a given client name from the appointments_list.
    """
    global appointments_list
    original_count = len(appointments_list)
    appointments_list = [appt for appt in appointments_list if appt.get("Client Name") != client_name_to_delete]
    deleted_count = original_count - len(appointments_list)

    if deleted_count > 0:
        save_appointments(appointments_list) # Save after deleting
        print(f"\nSuccessfully deleted {deleted_count} appointment(s) for client: {client_name_to_delete}.")
    else:
        print(f"\nNo appointments found for client: {client_name_to_delete}.")

# --- Time Slot Generation ---
def generate_time_slots(start_time_str, end_time_str, interval_minutes):
    """Generates a list of time slots at specified intervals."""
    dummy_date = datetime.date(2000, 1, 1)
    start_time = datetime.datetime.strptime(start_time_str, '%H:%M').time()
    end_time = datetime.datetime.strptime(end_time_str, '%H:%M').time()

    current_datetime = datetime.datetime.combine(dummy_date, start_time)
    end_datetime = datetime.datetime.combine(dummy_date, end_time)

    slots = []
    while current_datetime <= end_datetime:
        slots.append(current_datetime.strftime('%H:%M'))
        current_datetime += datetime.timedelta(minutes=interval_minutes)
    return slots

# Generate time slots from 6:30 PM (18:30) to 8:15 PM (20:15) at 15-minute intervals
time_slots = generate_time_slots('18:30', '20:15', 15)

# --- UI Imports and Initial Setup ---
import ipywidgets as widgets
from IPython.display import display, clear_output, HTML

# Output widget for displaying results
output_area = widgets.Output()

# Get current date for default value
current_date_str = datetime.date.today().strftime('%Y-%m-%d')

# --- Add Appointment Widgets ---
add_date = widgets.DatePicker(description='Date:', value=datetime.date.today())
add_time = widgets.Dropdown(description='Time:', options=time_slots, value=time_slots[0] if time_slots else None)
add_client_name = widgets.Text(description='Client Name:')
add_duration = widgets.IntText(description='Duration (min):', value=15, disabled=True)
add_status = widgets.Text(description='Status:', value='Scheduled')
add_notes = widgets.Textarea(description='Notes:')
add_button = widgets.Button(description='Add Appointment')

# --- View Appointment Widgets ---
view_all_button = widgets.Button(description='View All Appointments')
view_by_date_input = widgets.DatePicker(description='View by Date:')
view_by_date_button = widgets.Button(description='View by Date')
view_by_client_input = widgets.Text(description='View by Client Name:')
view_by_client_button = widgets.Button(description='View by Client')

# --- Delete Appointment Widgets ---
delete_client_name = widgets.Text(description='Client Name to Delete:')
delete_button = widgets.Button(description='Delete Appointment(s) by Name')

# --- Layout the widgets ---
add_widgets = widgets.VBox([
    widgets.HTML('<h3>Add New Appointment</h3>'),
    add_date, add_time, add_client_name, add_duration, add_status, add_notes, add_button
])

view_widgets = widgets.VBox([
    widgets.HTML('<h3>View Appointments</h3>'),
    view_all_button,
    widgets.HBox([view_by_date_input, view_by_date_button]),
    widgets.HBox([view_by_client_input, view_by_client_button])
])

delete_widgets = widgets.VBox([
    widgets.HTML('<h3>Delete Appointment(s)</h3>'),
    delete_client_name, delete_button
])

# Display the UI elements
ui = widgets.VBox([add_widgets, view_widgets, delete_widgets, output_area])

# --- Callback Functions ---
def on_add_button_clicked(button):
    with output_area:
        clear_output()
        date_obj = add_date.value
        time_str = add_time.value
        client_name = add_client_name.value
        duration = add_duration.value
        status = add_status.value
        notes = add_notes.value

        if duration > 30:
            print("Error: Duration cannot be more than 30 minutes.")
            return

        date_str = date_obj.strftime('%Y-%m-%d')

        try:
            appointment_datetime = datetime.datetime.strptime(f"{date_str} {time_str}", '%Y-%m-%d %H:%M')
            if appointment_datetime < datetime.datetime.now():
                print("Error: Cannot add an appointment in the past.")
                return
        except ValueError:
            print("Error: Invalid date or time format. Please use YYYY-MM-DD and HH:MM.")
            return

        add_appointment(date_str, time_str, client_name, duration, status, notes)
        add_date.value = datetime.date.today()
        add_time.value = time_slots[0] if time_slots else None
        add_client_name.value = ''
        add_duration.value = 15
        add_status.value = 'Scheduled'
        add_notes.value = ''

add_button.on_click(on_add_button_clicked)

def on_view_all_button_clicked(button):
    with output_area:
        clear_output()
        view_all_appointments()

def on_view_by_date_button_clicked(button):
    with output_area:
        clear_output()
        date_obj = view_by_date_input.value
        if date_obj:
            date_str = date_obj.strftime('%Y-%m-%d')
            view_appointments_by_date(date_str)
        else:
            print("Please select a date.")

def on_view_by_client_button_clicked(button):
    with output_area:
        clear_output()
        client_name = view_by_client_input.value
        view_appointments_by_client(client_name)

view_all_button.on_click(on_view_all_button_clicked)
view_by_date_button.on_click(on_view_by_date_button_clicked)
view_by_client_button.on_click(on_view_by_client_button_clicked)

def on_delete_button_clicked(button):
    with output_area:
        clear_output()
        try:
            client_name_to_delete = delete_client_name.value
            if client_name_to_delete:
                delete_appointment(client_name_to_delete)
                delete_client_name.value = ''
            else:
                print("Please enter a client name to delete.")
        except Exception as e:
            print(f"Error deleting appointment: {e}")

delete_button.on_click(on_delete_button_clicked)

display(ui)
