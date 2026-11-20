# Biometric Attendance Sync Tool (Modified for ERPNext v14)

Python Scripts to poll your biometric attendance system _(BAS)_ for logs and sync with your ERPNext instance.

**This is a modified version of the original [frappe/biometric-attendance-sync-tool](https://github.com/frappe/biometric-attendance-sync-tool)** tailored for:
- ERPNext: v14.80.0 (version-14)
- Frappe Framework: v14.92.0 (version-14)
- Frappe HR: v14.36.3 (version-14)

## Key Modifications
- **Autosync based on last Employee Checkin**: Instead of relying solely on local success logs to determine the starting point for synchronization, this version queries the ERPNext `Employee Checkin` DocType for the most recent checkin associated with the device_id. It then syncs only punches newer than that timestamp. This makes recovery more robust (e.g., after log file loss or server changes) and reduces reliance on local state.
- Compatible with the `hrms` app endpoint used in Frappe HR v14+.
- Default `ERPNEXT_VERSION` set to 14.

## Table of Contents
 - [Pre-requisites](#pre-requisites)
 - [Usage](#usage)
    - [GUI](#gui)
    - [CLI](#cli)
 - [Setup Specifications](#setup-specifications-(for-cli))
    - [UNIX](#unix)
    - [Windows](#windows)
  - [Setting Up Config](#setting-up-config)
  - [Resources](#resources)
  - [License](#license)

## Pre-requisites
* Python 3.6+

## Usage
There's two ways you can use this tool. If accessing the CLI is a bit of a pain for you, the GUI has a simple form to guide you through the process.

### GUI
Run the `install.py` or build the executable as described below.

### CLI
The `erpnext_sync.py` file is the "backbone" of this project. Apart from Windows _(which has its own wrapper `erpnext_sync_win.py`)_, this file can be directly used to set up the sync tool.

## Setup Specifications (For CLI)

1. Setup dependencies
    ```
    cd biometric-attendance-sync-tool-erpnext-v14
      && python3 -m venv venv
      && source venv/bin/activate
      && pip install -r requirements.txt
    ```
2. Setup `local_config.py`

   Make a copy of and rename `local_config.py.template` file. [Learn More](#setting-up-config)

3. Run this script using `python3 erpnext_sync.py` (it will loop infinitely, pulling at the configured frequency)

### UNIX

See original Wiki for production setup tips.

### Windows

Installing as a Windows service

1. Install pywin32 using `pip install pywin32`
2. Got to this repository's Directory
3. Install the windows service using `python erpnext_sync_win.py install`
4. Done

#### Update the installed windows service
    python erpnext_sync_win.py update

#### Stop the windows service
    net stop ERPNextBiometricPushService

#### To see the status of the service
    mmc Services.msc


## Setting up config
- You need to make a copy of `local_config.py.template` file and rename it to `local_config.py`
- Please fill in the relevant sections in this file as per the comments in it.
- Below are the delineation of the keys contained in `local_config.py`:
  - ERPNext connection configs:
    - `ERPNEXT_API_KEY`: The API Key of the ERPNext User
    - `ERPNEXT_API_SECRET`: The API Secret of the ERPNext User

      > Please refer to [this link](https://frappe.io/docs/user/en/guides/integration/how_to_set_up_token_based_auth#generate-a-token) to learn how to generate API key and secret for a user in ERPNext.
      > The ERPNext User who's API key and secret is used, needs to have at least the following permissions:
      > 1. Create Permissions for 'Employee Checkin' DocType.
      > 2. Write Permissions for 'Shift Type' DocType.
      > 3. Read Permissions for 'Employee Checkin' (for the new last-checkin lookup).

    - `ERPNEXT_URL`: The web address at which you would access your ERPNext. eg:`'https://yourcompany.erpnext.com'`, `'https://erp.yourcompany.com'`
    - `ERPNEXT_VERSION`: The base version of your ERPNext app. Set to 14 for this fork.
  - This script's operational configs:
    - `PULL_FREQUENCY`: The time in minutes after which a pull for punches from the biometric device and push to ERPNext is attempted again.
    - `LOGS_DIRECTORY`: The Directory in which the logs related to this script's whereabouts are stored.
    - `IMPORT_START_DATE`: The date after which the punches are pushed to ERPNext. Expected Format: `YYYYMMDD`. Used as a floor if no prior checkins exist or are earlier.

## To build executable file for GUI
### Linux and Windows:
1. Activate virtual environment.
1. Navigate to the repository folder (where `gui.py` located)
1. Run the following commands:
    ```
    pip install pyinstaller
    ```

    ```
    python -m PyInstaller --name="attendance-sync" --windowed --onefile gui.py
    ```
1. The executable file `attendance-sync` created inside `dist/` folder.

### Resources

* Original Article on [ERPNext Documentation](https://docs.erpnext.com/docs/user/manual/en/setting-up/articles/integrating-erpnext-with-biometric-attendance-devices).
* Original Repo's [/Wiki](https://github.com/frappe/biometric-attendance-sync-tool/wiki).

### License

This project is licensed under [GNU General Public License v3.0](LICENSE) (same as the original).
