NOTE: For full README with images, see the pdf file attached or view it on google drive:
https://drive.google.com/file/d/1xNfi3euccrfgi2QrgIzhJhTxmc3s2fIx/

# Jazz-Ensemble-Scheduler

This project provides a tool that allows a usere to build ensemble rosters and schedules for small jazz ensembles (combos) in a university setting.

It includes:
  - A Streamlit interface for data input, configuration, and constraint customization
  - Pyomo for constructing and solving the scheduling optimization model
  - CBC as the MILP Solver

As is, the scheduler currerntly supports users inputting customizable combo instrumentation requirements, rehearsal frequency, combo size limits, and more. 
The focus of this project was to develop a tool that could be intuitively used by anyone while using technical Operations Research techniques to solve a real-world problem.

## How to start scheduling?

Note that this program can be run locally using Streamlit and the CBC solver. Users must have Python with packages Streamlit, Pandas, Numpy, and Pyomo for full functionality, as well as the CBC solver.

### Loading CSV
The main way the solver receives information about the system is through the user-provided CSV file. For the scheduler to work properly, at least the following information is required in the CSV file:
  - A column that includes all student names
  - A column that includes what instrument each student plays
  - A column for each possible time slot with an entry that desribes whether that person (row) is available for that time slot. (EX: 'Yes' or 'No')

The following format is recommended:
  - One row per person
  - One instrument per person. Instruments should be selected from a dropdown to reduce the likelihood of typos and errors. The default list of instruments is:
      - Piano
      - Guitar
      - Other Chord
      - Bass
      - Drums
      - Other Percussion
      - Alto Sax
      - Tenor Sax
      - Bari Sax
      - Trombone
      - Trumpet
      - Flute
      - Clarinet
      - Other Horn
      - Vox
    If this is not the list used, edits can be made within the interface.

Upload the populated CSV under the 'Load Data' Tab:
<img width="1020" height="500" alt="image" src="https://github.com/user-attachments/assets/ba02eb02-380a-419d-8321-86d66eac6988" />

A preview of the uploaded data will display. This can be used as a reference when configuring how the data is read.

### Configuring Data
Next, under the 'CSV Settings' tab, users can tell the model which columns contain the names of students (Name Column), students' instruments (Instrument Column), as well as which words correspond to availability / unavailability. The data preview can be used as a reference.

<img width="387" height="500" alt="image" src="https://github.com/user-attachments/assets/9951dcf6-d3a4-42cd-ad22-6d294311797f" />

It is important that all entries here match what is actually in the CSV file, otherwise errors will occur when trying to build the model.

### Editing Instruments
As mentioned before, if users have not decided to use the default list of instruments provided above, the list of all possible instruments can be edited under the 'Instruments' tab. Here, users can delete existing instruments or add new ones. Again, users can use the data preview under the 'Load Data' tab to cross-reference, as instrument names must be inputted exactly as they will appear in the CSV.

<img width="768" height="352" alt="image" src="https://github.com/user-attachments/assets/b5c87763-3507-4d67-9061-d2242d94619a" />

### Setting 'Rehearsal Time Groups'
Here is where the customizable aspect of the Jazz Ensembles Scheduler starts to shine.
The 'Rehearsal Time Groups' allows users to categorize possible rehearsal times into groups.
For example, the default rehearsal time groups in the scheduler are 'Directed' and 'Self Directed'. That is, users can designate rehearsal slots as times where a director will be present to lead the rehearsal ('Directed') and times where combo members
themselves will lead the rehearsal ('Self Directed'). 

<img width="785.5" height="535.5" alt="image" src="https://github.com/user-attachments/assets/94888e6e-e2b1-46ae-bd69-f4b771c4d82c" />

In the above image, times on Monday and Tuesday have been designated as 'Self Directed' while times on Friday have been listed as 'Directed'.

Users have the option to delete existing rehearasal time groups or create new ones.

Currently, users do NOT have the ability to designate a slot to be a part of multiple rehearsal time groups. If a time slot may be eligible for multiple rehearsal time groups, multiple columns for this time should be created within the CSV and each column can be entered into a time group.

If the user wants to treat all possible rehearsal times equally, all rehearsal times should be put into one Rehearsal Time Group.

### Setting 'Instrument Type Groups'
Instrument Type Groups, similarly to Rehearsal Time Groups, allow the usere to customize categories for instruments. 
For example, the default Instrument groups are 'Chord' and 'Melody'. That is, users can designate instruments from the instrument list as either 'Chord' or 'Melody' instruments.
The purpose of instrument type groups will become apparent when building model constrarints. With groups of instruments defined, users can then create constraints such as 'All Combos must have at least 1 Chord Instrument', which could not be created if the 'Chord' group were not defined.

<img width="690" height="537.5" alt="image" src="https://github.com/user-attachments/assets/4bf1b7e3-3ee1-425f-aad2-cf3f4dead08d" />

In the above image, the instruments 'Piano', 'Guitar', and 'Othere Chord' have been designated as 'Chord' instruments while 'Alto Sax', 'Tenor Sax', 'Bari Sax', 'Trumpet', 'Trombone', 'Flute', 'Clarinet', and 'Other Horn' have been assigned to be 'Melody' instruments.
Users have the ability to delete or add instrument type groups, should they so wish.

Note that instruments, unlike rehearal times, are not required to be in a instrument group to be used. That is, even though 'Drums' is not assigned to a group in this example, constraints using this instrument can still be built later.
Additionally, instruments CAN be in multiple instrument type groups, unlike rehearsal times. For example, the instrument 'Accordion' could be assigned to both 'Chord' and 'Melody', if desired.

### Preparing Data
Next, under the 'Prepare Data' tab, the model will take the information the user has given on how to read the CSV and convert the data into binary values which the model can work with.
This step is critical for ensuring the success of the program. Once all information has been reviewed and is to the user's liking, they should select both 'Prepare Availability' and 'Prepare Instrumentation'. 

<img width="667" height="520.5" alt="image" src="https://github.com/user-attachments/assets/7b313c85-05d8-4ded-91e6-48c51d7a8d06" />

The binary matrix can be previewed, if the user so wishes.

<img width="420" height="330" alt="image" src="https://github.com/user-attachments/assets/5957c52b-c7e7-4dd3-a42b-17c50e13ab3d" />

### Building Constraints
Now that we have prepared the data, we can begin building the constraints and telling the program what we wish to schedule. To begin, select the 'Take me to the Scheduler!' button under the 'Begin Scheduling' tab once you are ready.

<img width="700" height="237.5" alt="image" src="https://github.com/user-attachments/assets/0819b741-77ca-4a13-87e6-7c7a4b0fb464" />

Next, specify how many combos you would like to schedule, along with their names.

<img width="660" height="564.5" alt="image" src="https://github.com/user-attachments/assets/9429cdd0-b443-4a24-9bae-9945ad9f465b" />

Note that combos must have distinct names, and cannot be named 'Instrument' or 'Name'.
If at any time you wish to edit the number of combos and/or the names of the combos, select the 'Edit Combos to be Scheduled' button under the 'Edit Combos' tab.

### Instrumentation Constraints

Under the 'Instrumentation' tab is where users can define constraints relating to combo instrumentation. For example, users can define constraints such as "Each Combo must contain At Least 1 Trumpet Player", etc.
The constraints in words is described under the selection boxes. Constraints will follow the following format:

[Combo Selection] must have [At Least / At Most / Exactly] [Number] of Instrument type: [Instrument / Instrument Group]

If you have made changes to the instrumentation matrix, you must select 'Implement Changes' to save them.

<img width="520" height="609" alt="image" src="https://github.com/user-attachments/assets/78b027d4-0847-4959-a53b-59dec8d09648" />

<img width="300" height="170" alt="image" src="https://github.com/user-attachments/assets/ced5f576-119a-4a2c-9caf-aa36df4f3973" />

NOTE: If contradictory constraints are created, the program will fail to find a feasible schedule. (EX: 'Each Combo must contain at most 2 Melody' and 'Each Combo must contain at least 3 Melody')

### Rehearsal Count Constraints

Under the 'Rehearsal Count' tab, users can define constraints relating to how many times combos must rehearse. 
The constraints in words is described under the selection boxes. Constraints will follow the following format:

[Combo Selection] must rehearse in rehearsal type [Rehearsal Time Group Selection] [Number] times weekly.

<img width="510" height="500" alt="image" src="https://github.com/user-attachments/assets/3d075aee-f181-4c86-8337-221f6fa9470f" />

In the above image, constraints have been added such that all combos must rehearse in the 'Directed' and 'Self Directed' slots once weekly.
As with before, ensure constraints are not contradictory, and ensure that you select 'Implement Changes' to save your changes.

### Combo Size Constraints

Combo size constraints follow a similar format, but for constraining the size of assigned combos.
<img width="525" height="415" alt="image" src="https://github.com/user-attachments/assets/a5249c28-1c77-44e3-953c-e6a67495a310" />

Once again, ensure constraints are not contradictory, and ensure that 'Implement Changes' is selected to save changes.

### Global Constraints

Next, under the 'Global Constraints' tab are housed other important constraints. These include:
  1. Each combo may not have more than one of a given instrument
  2. Each person may only be assigned to one combo
  3. Each time slot may only be used once
  4. Each person must be available for the rehearsal time they are assigned

These constraints can either be set to on or off.
Please make sure you select 'Implement Constraints', even if you have not toggled any of these constraints. **These constraints need to be implemented just like all other types.**  
In practice, constraints 3 and 4 will likely never need to be turned off.
In the circumstance where combos may have duplicate instruments, disable constraint 1. There is currently no support for when only some combos can have duplicate instruments while others cannot.

In the circumstance where people may be assigned to multiple combos, disable cconstraint 2.

In the specific circumstance where one or few individuals may need to be assigned to multiple combos, these individuals should have multiple entries in the CSV file and constraint 1 should remain on.

<img width="517" height="447" alt="image" src="https://github.com/user-attachments/assets/4b48cbac-cc5a-41fc-bed6-c06c224b5d15" />

### Creating Roster and Schedule

The 'View Constraints' tab can be used to review all constraints that have been implemented into the model. This tab shows first the key which the model uses to identify the constraint, as well as a description of the constraint in words.

EX:  *chord_LB                       All Combos must have at least 1 instrument(s) of type: Chord*

If all constraints are to your liking, move on to the 'Schedule' tab and select the 'Schedule Combos' button. You will be prompted to confirm you wish to continue.

After a few short moments, one of two things should happen:

In the case where a successful roster and schedule was created, it should list each combo, its roster, as well as a display of combos' rehearsal times.

<img width="485" height="309.5" alt="image" src="https://github.com/user-attachments/assets/1b2d0fd2-0c46-4e9c-b9fc-a3b71a63b4b4" />
<img width="500" height="260" alt="image" src="https://github.com/user-attachments/assets/6657ce9e-375c-4cb7-bda2-37d69a9f0b31" />

If you wish to change the constraints and reschedule combos, select the 'Change Constraints' button. Note that this will unimplement all constraints. You will need to reimplement constraints in all tabs once you have made changes.

In the event where a roster and schedule was not found, the following message wil be shown:
<img width="500" height="150" alt="image" src="https://github.com/user-attachments/assets/d66e4afe-6711-43b7-b78d-83c07abba53f" />

Should this message be received, double check your constraints to ensure none are contradictory. If no constraints contradict others, it is likely there is just not enough availability to create a schedule that fits the constraints given, or there was a mistake in the data configuration step.

## Closing Remarks

Thank you for taking the time to explore the Jazz Ensemble Scheduler! This project has been an incredibly challenging yet rewarding one, and I am excited to share it with all of you! Please keep in mind this is only a prototype, and UI/UX polish as well as QoL feature development is ongoing.
