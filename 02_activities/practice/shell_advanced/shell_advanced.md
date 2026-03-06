# Unix Shell Advanced Practice Problems
#!/bin/bash
## Introduction
#Kaylee and Simeon are biomedical engineering researchers trying to reanalyze data from an undergraduate project, #where they collected electroencephalography (EEG), electromyography (EMG), and actigraphy (acceleration and #rotational) recordings, and other information from subjects as they tried to keep their balance on a moving #platform for a balance project.

#Unfortunately, when they were silly undergraduates, they didn't know to keep their data tidy. They'd like your #help efficiently doing the following data management tasks using your newfound Bash knowledge.

## Tasks
* Complete the listed tasks in Bash
* This problem set requires commands that were not covered in the teaching sessions.
* Feel free to use any commands or strategy that you'd like. Suggested commands are listed.

### Task 1: Setup your environment
cd "/Users/levi/Documents/Documents/Graduate School Stuff/DSI certification class/DSI coding/Assignement 1/shell/02_activities/practice/shell_advanced"
1. Download their undergraduate data package: [dataset.zip](dataset.zip?raw=1)
#done via git clone already
1. Unzip it into your directory
    * *Hint*: Consider the `unzip` command
    cd "/Users/levi/Documents/Documents/Graduate School Stuff/DSI certification class/DSI coding/Assignement 1/shell/02_activities/practice/shell_advanced"
    unzip -q dataset.zip
1. Change directory into the folder containing the data package contents
    * *Hint*: Consider the `cd` command
    cd dataset
1. To make sure we're keeping good records this time, print the current path to your working directory
    * *Hint*: Consider the `pwd` command
    pwd
1. Make a new folder named `tidied_data`
    * *Hint*: Consider the `mkdir` command
    mkdir tidied_data

### Task 2: Taking inventory
1. List the contents of the data folder
    * *Hint*: Consider the `ls` command
    ls
1. List all the EEG files and write this list to a text file in the tidied_data folder named `eeg_inventory.txt`. 
    * *Hint*: Consider the `ls` and `head` commands, and either the `>` file redirection operator or `|` pipe operator and the `tee` command
    
    touch tidied_data/eeg_inventory.txt
    ls eeg* >> tidied_data/eeg_inventory.txt
    * Preview the **first 8 lines** of this file using the `head` command, does this look right?
    head tidied_data/eeg_inventory.txt

### Task 3: Taking inventory, part 2
1. Let's double check: does the naming convention of the EEG files match `eeg_[subjectid]_[session].edf`?
    * List the contents of the folder and inspect it visually!
    ls
1. Based on the `eeg_inventory.txt` file and the naming convention, generate a list of subject numbers and write it a file named `subject_ids_from_eeg.txt`. 
    * Preview the **first 8 lines** of this file.
    * *Hint*: Consider the `cut` command
    touch tidied_data/subject_ids_from_eeg.txt
    cut -d '_' -f 2 tidied_data/eeg_inventory.txt >> tidied_data/subject_ids_from_eeg.txt
1. Sort the `subject_ids_from_eeg.txt` file and write the output to `subject_ids_from_eeg_sorted.txt`. 
    * Preview the **first 8 lines** of this file.
    sort tidied_data/subject_ids_from_eeg.txt >> tidied_data/subject_ids_from_eeg_sorted.txt
1. Let's double check: does each subject have multiple EEG files? Are their subject IDs duplicated?
    * *Hint*: Look through the whole with the `less` command
    less tidied_data/subject_ids_from_eeg_sorted.txt
1. Create a unique list of subject IDs and write the output to `subject_ids.txt`. 
    * *Hint*: Consider the `uniq` command
    * Preview the **first 8 lines** of this file.
    uniq tidied_data/subject_ids_from_eeg_sorted.txt >> tidied_data/subject_ids.txt
### Task 4: The life changing magic of tidying up
1. For each subject:
    1. create a folder named after the subject ID in the `tidied_data` directory
        * *Hint*: Consider a `for` loop
    for subject in $(cat tidied_data/subject_ids.txt)
    do
        mkdir tidied_data/"$subject"
    done
    1. Move all files relating to that subject into their respective directories (eg. `tidied_data/[subjectid]/eeg_[subjectid]_[session].edf`)
        * *Hint*: Consider the `mv` command
    for subject in $(cat tidied_data/subject_ids.txt)
    do
        mv *$subject* tidied_data/$subject
    done
1. Notice that all the notes files have not been named consistently. Rename all the note files to `[subjectid]_notes.txt` within each subject folder.
    * *Hint*: Consider the `mv` command inside a `for` loop or using `find ... -exec mv ...`
    for folder in $(cat tidied_data/subject_ids.txt)
    do
        base=$(basename "$folder")
        mv tidied_data/${base}/${base}*.txt tidied_data/${base}/${base}_notes.txt
    done
### Task 5: Checking our work
1. Confirm that you've copied all the files over to the `tidied_data` directory
    1. Count the number of files copied:
        1. Generate a list of all the files within all the directories in `tidied_data` 
            * *Hint*: Consider the `find` command and look for files with the `-type` option
            touch tidied_data/file_list.txt
           find . -mindepth 3 -maxdepth 3 -type f >  tidied_data/file_list.txt
        1. Count the number of lines in the file list
            * *Hint*: Consider the `wc` command. How do we make it count lines?
            wc -l tidied_data/file_list.txt
    1. Count the number of files in the original folder, using the same strategy as above
            find . -mindepth 1 -maxdepth 1 -type f >> tidied_data/OG_folder_filelist.txt
            wc -l tidied_data/OG_folder_filelist.txt
1. Do the file counts match?
    * *Hint*: Consider using variables, an `if` statement, and `-eq`
if [ "$(wc -l < tidied_data/file_list.txt)" -eq "$(wc -l < tidied_data/file_list.txt)" ]
then
    echo yes
fi
    