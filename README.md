import os
import sys
import datetime

def create_sudo_file(user_id):
    # Check if the user ID ends with "er"
    if not user_id.endswith("er"):
        print("User ID must end with 'er'. Exiting.")
        return

    # Create a filename for the sudo file
    process_id = os.getpid()
    timestamp = datetime.datetime.now().strftime("%Y%m%d%H%M%S")
    sudo_filename = f"/etc/sudoers.d/24hoursudo_{user_id}_{process_id}_{timestamp}"

    # Write sudo access entry to the sudo file
    sudo_entry = f"{user_id} ALL=(ALL:ALL) ALL\n"
    try:
        with open(sudo_filename, "w") as sudo_file:
            sudo_file.write(sudo_entry)
    except IOError as e:
        print(f"Error writing to the sudo file: {e}")
        return

    print("Sudo file has been successfully created.")

    # Calculate the time to run the cron job after 22 hours from sudo file creation
    run_time = datetime.datetime.now() + datetime.timedelta(hours=22)
    cron_minute = run_time.minute
    cron_hour = run_time.hour
    cron_date = run_time.day

    # Create the cron file
    cron_filename = f"/etc/cron.d/sudo_{user_id}_{process_id}_{timestamp}"
    cron_entry = f"{cron_minute} {cron_hour} {cron_date} * * root rm -rf {sudo_filename} {cron_filename}\n"
    try:
        with open(cron_filename, "w") as cron_file:
            cron_file.write(cron_entry)
    except IOError as e:
        print(f"Error writing to the cron file: {e}")
        return

    print("Cron file has been successfully created.")

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python script.py <username>")
        sys.exit(1)

    user_id = sys.argv[1]
    create_sudo_file(user_id)


<!---
ssekhawata/ssekhawata is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
