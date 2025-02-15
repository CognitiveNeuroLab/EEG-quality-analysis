EEG quality analysis pipeline
================
Douwe Horsthuis
2022-09-07

[![Contributors](https://img.shields.io/github/contributors/DouweHorsthuis/EEG-quality-analysis.svg?style=plastic)](https://github.com/DouweHorsthuis/EEG-quality-analysis/graphs/contributors)
[![Forks](https://img.shields.io/github/forks/DouweHorsthuis/EEG-quality-analysis.svg?style=plastic)](https://github.com/DouweHorsthuis/EEG-quality-analysis/network/members)
[![Stargazers](https://img.shields.io/github/stars/DouweHorsthuis/EEG-quality-analysis.svg?style=plastic)](https://github.com/DouweHorsthuis/EEG-quality-analysis/stargazers)
[![MIT
License](https://img.shields.io/github/license/DouweHorsthuis/EEG-quality-analysis.svg?style=plastic)](https://github.com/DouweHorsthuis/EEG-quality-analysis/blob/master/LICENSE.txt)
[![LinkedIn](https://img.shields.io/badge/-LinkedIn-black.svg?style=plastic&logo=linkedin&colorB=555)](https://www.linkedin.com/in/douwe-horsthuis-725bb9188/)

**This EEG pipeline will both pre-process your data and give you a
quality report afterwards that will look somewhat like this:**

<img src="images/data_quality.jpg" alt="jpg of the data" align="center" width="1040" height="598"/>

Made by [Douwe Horsthuis](https://github.com/DouweHorsthuis/) as part of
the:

<img src="images/CNL_logo.jpeg" alt="Logo" align="center" width="286"/>

**Table of Contents**

1.  [About the project](#about-the-project)  
2.  [The code](#the-code)
    -   [How to download the code and setup
        EEGLAB](#how-to-download-the-code-and-setup-eeglab)  
    -   [How to prepare your data](#how-to-prepare-your-data)  
    -   [How to setup the binlists for
        triggers](#how-to-setup-the-binlists-for-triggers)  
    -   [How to prepare the code
        itself](#how-to-prepare-the-code-itself)  
    -   [Readme files and promts](#readme-files-and-promts)  
3.  [Functions explained](#functions-explained)
    -   [readme_to_EEG](#readme_to_eeg)  
    -   [eBridge](#ebridge)
    -   [Edf2Mat](#edf2mat)
    -   [newtimef](#newtimef)
    -   [edf_to_fig](#edf_to_fig)
4.  [Notes, shortcommings upcoming
    additions](#notes-shortcommings-upcoming-additions)

## About the project

The goal of this project is to have a reliable EEG pipeline that can be
used in an objective way to show the quality of a data set, while
potentially also pre-processing EEG data. The main difference between
this pipeline and [the one it was based
on](https://github.com/DouweHorsthuis/EEG_to_ERP_pipeline_stats_R/) is
that this one ends with a PDF file that gives you some insight in the
data. [Click here for an example of what it can look
like](images/example_data_quality.pdf). Part of the idea is that this
allows for analysis data as it comes, and tells us if the basics of a
paradigms are working, if the participants responses are accurate, when
eye tracking is used if the participant is looking at the right spot.
The whole pipeline is written in Matlab, and relies on EEGLAB and some
EEGLAB plugins. More in-depth information below. Part of the idea is to
incorporate notes taken by the person collecting the data, we refer to
these notes as readme files.

**If you plan to use it, please cite**: Horsthuis, D. J., Francisco, A.
A., De Sanctis, P., Beker, S., & Molholm, S. (2022).
EEG-quality-analysis (Version 1.0.0) \[Computer software\].
<https://doi.org/10.5281/zenodo.7041685>

## The code

### How to download the code and setup EEGLAB

To download the code you can [click
here](https://github.com/DouweHorsthuis/EEG-quality-analysis/archive/refs/heads/main.zip).
The script you want to use is in the src folder, and you’ll also need
the functions from the same folder.  
To download EEGLAB [go here](https://sccn.ucsd.edu/eeglab/download.php)
and follow the instructions.  
After downloading EEGLAB, you will need to install the ERPlab plugin and
the Biosig plugin. You can find either by **opening EEGLAB**, click on
**file** (left top corner of the gui) and click on **Manage EEGLAB
extensions**.

*AFTER INSTALLING EEGLAB AND THE ERPLAB & BIOSIG PLUGINS*

In MATLAB add the folder with the functions to your path. You can do
this manually by

1.  Clicking **HOME** (left top corner)  
2.  **Set Path** (in the middle of the top panel)  
3.  **Add with Sub folders…**  
4.  Find where you have the function folder, you just downloaded, and
    add it.

### How to prepare your data

This script expect that your BDF files are organized in one folder per
paradigm with sub folder for each participant. Which looks something
like this:

\[Paradigm name\]  
├── \[IDparticipant1\] \# folder of the first participant  
├── \[IDparticipant2\] \# folder of the second participant  
:           :  
└── \[IDparticipantn\] \# folder of participant n

In line 68 and 69 it will update the loading and saving place for each
individual subject.

### How to setup the binlists for triggers

You need files that will explain what trigger represents what. We call
these binlists. You will need to create 2 binlists. 1 to plot your ERPS
and another to calculate your reaction times (RTs). You can find and
example for each in the testing folder, but for more all information on
how to make one from scratch [see
this](https://github-wiki-see.page/m/lucklab/erplab/wiki/Assigning-Events-to-Bins-with-BINLISTER:-Tutorial).

### How to prepare the code itself

While the idea is that there is very few manual (and potential
subjective) input, you will need to set some parameters.

**Updating variables inside MATLAB**  
Line 10 to 42 all relate to information MATLAB wants from you. The first
part you should set only 1 time and should be the same for each
participant you run for that paradigm.

``` matlab
subject_list = {'id'};% The ID for the particpant
load_path    = ''; %will open individual folders based on subject ID
save_path    = ''; %where will you save the data (something like 'C:\data\')
binlist_location='';%where do you store your data
logo_location= '';%if you want to add a logo you can add it here if not leave it empty
logo_filename=''; %filename + extention (eg.'CNL_logo.jpeg')
binlist_name=''; %name of the text file with your bins
rt_binlist = ''; %name of the reaction time binlist
rt_plot_n=; %which RT bins do you want to plot together (can only plot one group)
plotting_bins=; %the bins that should become ERPs
channels_names={}; %channels that you want plots for (
colors={}; %define colors of your ERPs (1 per bin), add - for solid line add -- for dashed line -. for dotted/dashed : for dotted
downsample_to=; % what is the sample rate you want to downsample to
lowpass_filter_hz=; %50hz filter
highpass_filter_hz=; %1hz filter
epoch_time = [];
baseline_time = [];
low_fq= ;%lowest frequency for time frequency plot (if you go lower than 3hz you will need a lot of data)
high_fq=; %highest frequency for time freq plot, should never be more than half your sampling or half your low pass 
```

In the testing folder you can see an example where it is all filled out.
It will all look something like this (of course with changes that will
work for where you have your data and how you named it etc.)

``` matlab
subject_list = {'10508'};% The ID for the particpant
load_path    = 'C:\Users\dohorsth\Desktop\SFARI Behav\FAST\Data\'; %will open individual folders based on subject ID
save_path    = 'C:\Users\dohorsth\Desktop\SFARI Behav\FAST\test\'; %where will you save the data (something like 'C:\data\')
binlist_location='C:\Users\dohorsth\Desktop\SFARI Behav\FAST\script\';
logo_location= 'C:\Users\dohorsth\Documents\GitHub\EEG-quality-analysis\images\';%if you want to add a logo you can add it here if not leave it empty
logo_filename='CNL_logo.jpeg'; %filename + extention (eg.'CNL_logo.jpeg')
binlist_name='binlist_fast_simple.txt'; %name of the text file with your bins
rt_binlist = 'binlist_fast_rt.txt'; %name of the reaction time binlist
rt_plot_n=1:4; %which RT bins do you want to plot together (can only plot one group)
plotting_bins=1:4; %the bins that should become ERPs
channels_names={'Cz' 'Pz' 'CPz' 'Po3' 'Poz' 'po4' 'o1' 'oz' 'o2'}; %channels that you want plots for (
colors={'k-' , 'r-' , 'b-' ,'g-' }; %define colors of your ERPs (1 per bin), add - for solid line add -- for dashed line -. for dotted/dashed : for dotted
downsample_to=256; % what is the sample rate you want to downsample to
lowpass_filter_hz=50; %50hz filter
highpass_filter_hz=1; %1hz filter
epoch_time = [-100 500];
baseline_time = [-50 0];
low_fq= 3;%lowest frequency for time frequency plot (if you go lower than 3hz you will need a lot of data)
high_fq=40;%highest frequency for time freq plot, should never be more than half your sampling or half your low pass 
```

### Readme files and promts

After making all these changes, you should be able to run the code. The
first thing that happens is that you get prompted some questions. These
are specific to each participant, this is why you will need to answer
them for each participant. In all cases you will see a question show up
in the command window of MATLAB. You need to type your answer and hit
Enter. In some cases you will be explained how you need to format your
answer (e.g. for bad channels you need to write both within {} and each
channel requires ’’ to make it into separate strings).  
**Readme files** are files with information filled out by the person
collecting data. You can find an example [by clicking
here](https://github.com/DouweHorsthuis/EEG-quality-analysis/blob/main/testing/xxxx%20F.A.S.T.%20Response%20Task%20x-xx-xxxx%20README.txt).
If MATLAB cannot find the readme file or some of the information inside
it it will add more prompts to get the same information, but manually.
Just like explained above.

## Functions explained

There are several functions that are being used. Since for
pre-processing all these functions are native to EEGLAB see [our
pipeline](https://github.com/DouweHorsthuis/EEG_to_ERP_pipeline_stats_R/)
or check out the [EEGLAB website](https://eeglab.org/) for an in-dept
explanation.

### readme_to_EEG

As mentioned [above](#readme-files-and-promts) we use readme files in
the lab to write notes during data collection. These notes will have a
lot of information that would help to say something about the quality of
the EEG. Because of that we created a function,
`[EEG]=readme_to_EEG(EEG,readme_yn,data_path,save_path_indv,subject_list{s});`
It requires the structure `EEG`, this is where EEGLAB stores it’s
information, and it’s where the functions adds the info. `Readme_yn`, is
the result of a prompt that asks if there is a readme file (yes/no).
`data_path` is the specific path to that persons data and
`save_path_indv` is the same for saving. Lastly `subject_list{s}` is
giving the subject number. All of these are auto created by the script.
The readme file is loaded as on big string and we look for specific
parts of the text to find the info we are looking for using MATLABS
`extractBetween` function. This means that if we move the readme file
around and change the template, we won’t be able to extract data
efficiently without updating this function. The readme file should be using this template
![image](https://user-images.githubusercontent.com/64816053/189417741-250fb44e-3661-4a3e-a407-85561e9fa21d.png)


### eBridge

`eBridge` is a function that uses the EEG structure and checks if there
are channels that are bridged. The full explanation can be found
[here](https://psychophysiology.cpmc.columbia.edu/software/eBridge/index.html).
Or in [this
paper](https://psychophysiology.cpmc.columbia.edu/pdf/alschuler2013a.pdf)
that was written and resulted in the function. `bridge=eBridge(EEG)`
gives us a structure in which `bridge.Bridged.Labels` gives us the
labels of all the bridged channels. Later we use this to plot a figure
of the location of bridged channels. **note that bridged channels are
not deleted**.

### Edf2Mat

We use the `Edf2Mat` function to transform our eye tracking files
(collected with SR-research’s Eyelink-1000 eye tracker system) and turn
it into a heat map of where the participant was looking on the screen.

### edf_to_fig

While we still use `EDF2Mat` to load the .edf files into MATLAB, we use
a combination of their functions in `edf_to_fig`. `edf_to_fig` only
needs the filepath to the .edf files. These need to be in separate
folders per participant, because we loop through all the files in the
folder and merge all files ending in .edf. After we have use parts of
the code from different functions from `Edf2Mat` to create a heatmap
that shows us where the participant was looking for the whole of the
paradigm.

### newtimef

Because `newtimef` is a native EEGLAB function [all the explanation can
be found
here](https://eeglab.org/tutorials/08_Plot_data/Time-Frequency_decomposition.html).
However, it is a new addition to the pipeline that might still need some
extra testing on our side **so please use with caution and report
bugs**. For now it works on 1 channel only, where it does a time
frequency analysis for the length of your epochs. If you have 1
condition, you will be prompted this question, you will only get one
plot. If you have 2, you will get a plot for both and a difference plot.
If you have more, you will get an error because this is not possible for
now. Further more, **be aware that if you have multiple bins in your
binlist, the first one or first 2 will be selected and plotted, depended
on how many conditions you chose**.

## Notes, shortcommings upcoming additions

### Shortcommings and upcoming additions

Please let us know if you notice anything that does not work. So far we
have made the following updates:  
1- including possible time frequency analysis 2- plotting the heatmap of
all eye tracking files 3- fixing the
`[EEG]=readme_to_EEG(EEG,readme_yn,data_path,save_path_indv,subject_list{s});`
function that had minor issues.

### Notes

If you have suggestions, edits, or issues, please leave them
[here](https://github.com/DouweHorsthuis/EEG-quality-analysis/issues),
or e-mail me @ <douwehorsthuis@gmail.com>. This pipeline works for our
lab,but that doesn’t mean it cannot be improved so all input is always
welcome and very much appreciated.

## Acknowledgements

-   [Douwe Horsthuis](https://github.com/DouweHorsthuis)  
-   [Ana
    Francisco](https://www.linkedin.com/in/ana-alves-francisco-3798599a/)
-   [Sophie
    Molholm](https://www.einsteinmed.edu/faculty/12028/sophie-molholm/)
-   [Pierfilippo De
    Sanctis](https://einsteinmed.edu/faculty/12347/pierfilippo-de-sanctis/)
-   [Shlomit
    Beker](https://einsteinmed.edu/faculty/15122/shlomit-nir-beker/)
-   [John
    Foxe](https://www.urmc.rochester.edu/people/29722174-john-j-foxe)
