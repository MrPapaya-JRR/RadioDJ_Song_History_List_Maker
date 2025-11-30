RadioDJ Song History List Maker v1.13 – README  
==============================================  

## Overview  

RadioDJ Song History List Maker is a companion utility for RadioDJ v2.0 that monitors the now playing information and generates a rolling history of recently played tracks.

The program presents a tabbed graphical interface that makes it easy to start and stop monitoring, review the last 10 played tracks, manage exclusions, and edit the configuration INI file.  

Yes, I know there are other ways to accomplish the same results. This was originally a simple script to create a song history text file that I could view on my phone or tablet. I wanted to learn how to put together larger programs that use a GUI instead of the console, so this is what I decided to work on first. I think it turned out pretty good. I was super proud to see that GUI window actually open up for the first time and 'sort of' work. Everyone has to start somewhere, so be kind. If you like this program and have ideas for improvement, let me know. RadioDJ forever!


## Key Features  

- Monitors the RadioDJ “now playing” track file and maintains a circular buffer of the last 10 tracks played.
- Displays the current track and a formatted “Last 10 Played Tracks” list in the Main tab.
- Loads and shows exclusion rules so unwanted tracks can be filtered from the history display.
- Supports an AutoStart mode so monitoring begins automatically when the program is launched.
- Retrieves and caches SubCategory and Genre names from the RadioDJ MySQL database to append descriptive labels to track entries.
- Provides an integrated Settings/INI editor tab to view, edit, save, and reload the configuration file without leaving the program.
- Includes an About/Help tab that displays detailed program information through a read‑only editor.
- Uses a mutex to prevent multiple instances from running simultaneously and warns the user if another copy is already active.
- Optional debug logging, enabled via the -DEBUG command‑line flag, writes diagnostic information to text files for troubleshooting.


## Version History (Summary)  

- v1.00 – Initial version with a single‑tab interface.
- v1.01 – Converted to a multi‑tab layout; added AutoStart toggle, Settings/INI editor tab, and About/Help tab.
- v1.02 – Corrected exclusions display behavior and added persistence for window size between sessions.
- v1.03 – Adjusted layout by shrinking the Top 10 area and enlarging the exclusions area; removed a redundant Help menu.
- v1.04 – Fixed an additional bug related to the exclusions area.
- v1.05 – Implemented mutex‑based single‑instance protection and automatic exit after displaying a warning if another instance is running.
- v1.06 – Began retrieving SubCategory and Genre names from the database and appending them to track names.
- v1.07–v1.09 – Iterative debugging of SubCategory/Genre handling and parsing of data from now_playing.txt into internal maps.
- v1.10 – Added the -DEBUG command‑line flag, expanded debugging output, updated About text, and improved executable search routines.
- v1.11 - If only one ID number is found at the end of the track name, it will be assumed to be the SubCatID. Two numbers will be SubCatID & GenreID
- v1.12 - If AutoStart is activated, the program window will open minimized.
- v1.13 - Fixed. The minimized window wasn't restoring to the previous size.

## Requirements  

- RadioDJ v2.0 with a working MySQL database (access to subcategory and genre tables is required for name expansion).
- Windows 10/11
- Access to the RadioDJ “now playing” text file and the track history list file path as configured in the program’s INI.
- MySQL command‑line client (mysql.exe). The program will search "C:\Program Files", it's own program folder, the system path, etc


## Installation  

1. Place the executable for RadioDJ Song History List Maker v1.10 in a convenient folder, ideally alongside your existing RadioDJ tools and scripts.
   I use C:\RadioDJv2\JRR_Tools\RadioDJ_Song_History_List_Maker\
2. Ensure the INI file used by the program is present and configured with correct paths for: now_playing file, track history output, server, database, username, and password.
3. There are a few settings in RadioDJ that may need adjusted. See the next section ##RadioDJ Configuration.


## RadioDJ Configuration

To make this program work correctly, RadioDJ must output track information in a specific format that includes artist, title, subcategory ID, and genre ID.

### Stream Titles Setup
1. Open RadioDJ and go to **Options > Stream Titles** tab.
2. Find the **File Type Titles** section and select **Music** from the list.
3. Set the format to one of these options:  
   - '$artist$ - $title$  $subcat-id$  $genre-id$' (full format with subcategory and genre)
   - '$artist$ - $title$  $subcat-id$' (artist, title, and subcategory only)
   - '$artist$ - $title$' (artist and title only, no subcategory/genre)

You can also configure formats for other file types (like jingles or ads) in the same section if needed.

### Now Playing File Path
1. Go to **Options > Plugins > MetaData Export > File** tab.
2. In the **Target File** field, enter the full path where the now playing file will be saved.
   Example: `R:\RadioDJ_temp\JRR_Now_Playing.txt`

Update your program's INI file to point to this same file path after making these changes in RadioDJ.


## Launching the Program  

- Standard launch: run the executable normally to open the tabbed GUI.
- Debug launch: run the executable with the -DEBUG command‑line argument to enable additional diagnostic logging to text files (for example, track expansion and database load debugging).

When the program starts, it creates a uniquely named mutex using its name and version string to ensure only one instance is active; if an instance is already running, a timed message box appears and the program exits after a short delay.


## Main Tab – Monitoring  

The Main tab is the primary control panel where you monitor RadioDJ activity and view the most recent songs.

Main elements:  
- Current Track frame: shows the raw text from the now playing track file in a highlighted text gadget.
- Last 10 Played Tracks frame: displays a numbered list of the last 10 tracks seen by the monitor, using a circular buffer to maintain order.
- Status Information frame: shows whether monitoring is running or stopped and displays elapsed monitoring time in seconds.
- Start Monitoring / Stop Monitoring button: toggles file monitoring and updates the button label and colors to reflect the current state.
- Auto‑Start Control frame: hosts the AutoStart button, which indicates whether automatic monitoring on startup is enabled.
- Exclusions frame: shows the currently loaded exclusion entries in a read‑only editor, including a count of how many rules are active.

The monitoring loop periodically checks the now playing file and, when a change is detected, adds the track to the Last 10 buffer, updates the display, and refreshes status and elapsed time.


## About/Help Tab  

The About/Help tab displays detailed program information and usage notes through a read‑only editor gadget.


## Settings/INI Tab  

The Settings/INI tab is an integrated configuration editor that lets you manage the program’s INI file from within the GUI.

Elements and behavior:  
- Settings editor: loads the entire INI content and displays it in an editable editor gadget using a monospaced font.
- Save Settings button: writes any changes back to the INI file.
- Reload from File button: discards current editor changes and reloads the INI directly from disk.

Use this tab to adjust paths, database credentials, and AutoStart or exclusion‑related settings without manually opening the INI in an external editor.


## Track Name Expansion  

The program can enhance track names by appending human‑readable SubCategory and Genre names extracted from the RadioDJ database.

If a SubCategory or Genre ID is not found in the maps, the program falls back to placeholder labels such as “SubCat_XX” or “Genre_YY” so that identifiers are still visible in the output.


## Exclusion Handling  

The program supports a dynamic list of exclusions so certain tracks or patterns are not shown in the Last 10 list & history file. Any filenames containing at least once of the Exceptions will not be displayed. For ex. if an Exception is WEATHER, it will ignore the file Main_WeatherForecast.wav 

The Main tab’s Exclusions frame shows the loaded list and the number of entries so you can quickly confirm which items are active.

This mechanism allows you to maintain a flexible and easily extensible set of exclusions through the INI file.


## AutoStart Behavior  

AutoStart makes the program begin monitoring automatically when launched, without requiring a manual button press.

- When AutoStartEnabled is set in the INI, the program updates the AutoStart button text to “AutoStart: ON” and changes its background color for visual feedback.
- If AutoStart is enabled at startup, the program window will open minimized, the program starts monitoring immediately and loads SubCategory and Genre names so the system is fully ready.

You can toggle AutoStart on or off at any time via the AutoStart button in the Main tab; the setting is saved to the INI.


## Window Management and Layout  

- The program opens with a default window size (for example, 750×650). The window size is saved to the .ini file and loaded on startup so the GUI reopens at the last used dimensions.


## Single Instance Protection  

To avoid conflicts and duplicate monitoring, the program uses a Windows mutex. This design prevents accidental multiple copies from interfering with RadioDJ or with each other.


## Debugging and Log Files  

When launched with the -DEBUG flag, additional diagnostics are written to text files in the same folder as the INI.

Examples:  
- expand_debug.txt: captures details about how SubCategory and Genre IDs are parsed from track strings and whether the corresponding map lookups succeed.
- database_load_debug.txt: reports the total number of SubCategory and Genre entries loaded and enumerates map keys and values to help verify database connectivity.

These logs are intended for diagnosing problems getting the program to operate. For normal operation, use of the -DEBUG flag is not needed.


## Usage Tips  

- Configure all path and database settings in the INI before first use to avoid connection errors.
- Start with AutoStart disabled until you are comfortable with how the tool behaves alongside RadioDJ.
- Enable -DEBUG if track names are not expanding correctly or if the Last 10 list does not match expectations; then inspect the log files for details.
- Use the Exclusions section in the INI to omit sweepers, jingles, or other non‑music items from your public or on‑air history display.
