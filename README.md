# AutomatedFreeSurfer

## Description
These scripts are tailored to process T1 images in a BIDS-formatted dataset using FreeSurfer's `recon-all` and the hippocampal and amygdala subfields segmentation stream with minimal (really, minimal) user input. It automates the tasks of creating a quality control montage of the FreeSurfer output for each subject and session, and extracting the metrics into a CSV file. The suite provides the added capability of running the longitudinal stream when more than one session per subject is present (also with minimal user input)

Designed for simplicity, users will interact through a series of prompts to navigate specific processes. Apart from setting some paths, no additional programming is required, the pipeline takes care of it. Ensure that all scripts are downloaded from this repository and saved in your BIDS folder alongside your subjects' directories.

## Table of Contents

1. [Author Information](#author-information)
2. [Prerequisites](#prerequisites)
3. [Instructions](#instructions)
4. [Scripts Overview](#scripts-overview)
5. [Steps-by-Step Guide](#step-by-step)
6. [Troubleshooting](#troubleshooting)
7. [Contact](#contact)

## Citation
- Ferreira-Atuesta, C., Terziev, R., Marr, H., & Galovic, M. (2023). AutomatedFreeSurfer (Version 1.0) [Computer software]. https://github.com/cfatuesta/AutomatedFreeSurfer/tree/main



#### Prerequisites

1. **BIDS Dataset**: Your dataset must be organized according to the [BIDS specification](https://bids.neuroimaging.io/), ensuring correctly named main directory and subdirectories. Here's an example of how it should look like:
     <img width="1232" alt="Screenshot 2023-10-19 at 3 01 07 PM" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/ae08346e-0fe4-4014-a8c4-862b544e7092">


2. **NIFTI Files**: The images should be in the nifti format (.nii or .nii.gz), accompanied by their corresponding .JSON files. These files are typically generated by DICOM to NIFTI converters like `dcm2niix`. Note: Use `dcm2niix` and not `dcm2nii`.
   
   **Check if dcm2niix exists**:
   ```bash
   which dcm2niix
   ```
   If it doesn't exist, you can install it from its GitHub repository:
   - [dcm2niix GitHub repository](https://github.com/rordenlab/dcm2niix)

3. **FreeSurfer**: FreeSurfer must be installed, and the necessary scripts (`main_script.sh`, `process_csv.py`, `merge_csv.py`, and `process_longitudinal.py`) should reside in your `SUBJECTS_DIR` path (refer to the Enigma folder in the provided image).
   
   **Check if FreeSurfer is installed**:
   ```bash
   which recon-all
   ```

4. **Freeview & ImageMagick**: The tools `freeview` (from FreeSurfer) and `magick` (from ImageMagick) should be readily available.
   
   **Check for freeview**:
   ```bash
   which freeview
   ```
   **Check for magick**:
   ```bash
   which magick
   ```
   If ImageMagick isn't installed, run:
   ```bash
   brew install imagemagick
   ```

5. **Python3**: Ensure Python3 is installed on your system. You can check the version using:
   ```bash
   python --version
   ```
   If you don't have Python3:
   ```bash
   brew install pyenv
   pyenv install 3.10.10
   pyenv global 3.10.10
   ```

6. **Homebrew**: Several of the installations utilize Homebrew. If you don't have Homebrew installed:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

By following these prerequisites, you ensure a smooth setup and avoid potential issues during execution. Make sure to follow the checks and installation steps accordingly.

# Instructions

## Starting the Main Script
1. **Initialization:** Open your terminal or command prompt. Navigate to the directory containing your scripts and data. Execute the `main_script.sh` or `main_script_parallelized.sh` by typing `bash main_script.sh` or `bash main_script_parallelized.sh` and pressing Enter.
2. **Dataset Validation:** The script first validates the organization and naming conventions of the dataset. If inconsistencies are detected, you can immediately exit the script by pressing `Ctrl+C`. If everything appears in order, you can proceed by pressing Enter.

## Enter Paths
1. **Directory Check:** The script checks if environment variables `SUBJECTS_DIR` and `FREESURFER_HOME` are set. 
    - If they are not set, you will be prompted to manually provide the paths:
        - `SUBJECTS_DIR`: The directory where your subjects' data is.
        - `FREESURFER_HOME`: The installation directory of Freesurfer.
    - If they are set, the paths will be displayed for confirmation. You can choose to continue with these paths or provide alternate ones.

## Processing T1 Images
1. **Metadata Extraction:** All detected T1 images are processed to extract metadata from their associated .JSON headers. This metadata is then consolidated into a CSV file named "T1s_metadata.csv" in the working directory.
   <img width="811" alt="Screenshot 2023-10-07 at 09 33 45" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/0f4128de-c5b0-40c3-84f9-d49da389b076">

3. **Processing Check:** The script searches for T1 images that haven't been processed by Freesurfer. This is done by reading the `recon-all.log` associated with each image.
    - If the log states "finished without error", that specific image is considered already processed and thus, is skipped.
    - If there's no log or if the log states "finished with errors", the image will be selected for further processing.
4. **Processing Confirmation:** After identifying all unprocessed images, you will be asked whether you'd like to continue processing them using the cross-sectional pipeline. Type `yes` to proceed.
5. ❗️ **Important Note:** If you have T1 images processed by Freesurfer but stored in different directories, you must move them to their respective subject directories in the main dataset. Ensure you relocate the `log` folder and its contents, as the scripts utilize these logs for decision-making. See image below to recreate the appropiate subfolders. Make sure you keep the same structure and naming as the image below (except for subjects' ID). 
   
<img width="1342" alt="Screenshot 2023-10-19 at 3 10 19 PM" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/3745cff9-5a6c-4b9c-ad9d-3fd3b24937be">

## Generating Montages
1. **Montage Creation:** For T1 images lacking visual montages, the script will propose generating these montages. Confirm by typing `yes`.
2. **Montage Types:** Two distinct montages will be created for each subject/session:
    - **3D Montage:** Showcasing three-dimensional aspects of the imaging.
      ![montage-3d](https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/2a2266b6-e7c1-419d-ba85-cdd8499be334)
    - **2D Montage:** Providing two-dimensional slices for detailed examination.
      ![montage-2d](https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/26d51497-8489-4618-a81d-91ab0483dac7)
      

## Tables Creation
1. **Data Compilation:** The script organizes and tabulates the cross-sectional and, if present, longitudinal data. This structured data is then saved for future analysis and referencing.
   
<img width="942" alt="Screenshot 2023-10-07 at 09 32 48" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/72973e2f-94ec-4e9a-809a-f6daa696d830">

## Longitudinal Stream
1. **Session Check:** The script evaluates if subjects have more than one processed session.
2. **Pipeline Activation:** If multi-session criteria are met, the script activates Freesurfer's longitudinal pipeline, systematically creating new directories for every output stage, namely: base, time 1, and time 2.

## Scripts Details

### Main Script (Parallelized)
- **`main_script_parallelized.sh`:** This script functions similarly to `main_script.sh` but is optimized to handle parallel processing. This enables faster processing of multiple images by distributing the tasks across available computational resources. Especially handy for large datasets, this script reduces the time required for processing without compromising on the output's quality.

### Main Script (Regular)
- **`main_script.sh`:** This script, as described above, provides a sequential approach to process the T1 images. It is designed to be more straightforward and is suitable for datasets of a moderate size or when computational resources are limited.

The following scripts are part of both the main_script and main_script_parallelized:

**process_csv.py**: Adjusts CSV files for better readability, extracts relevant data, and determines the pipeline type.
**merge_csv.py**: (Though current functionality is similar to `extract_metadata.py`,) Expected to merge multiple CSV files.
**extract_metadata.py**: Fetches metadata from JSON files associated with neuroimaging data and compiles them into a CSV.
**process_longitudinal.py**: Manages the processing of longitudinal MRI data, including setting up symbolic links for different sessions.

Remember to adjust your script's execution based on the desired mode: parallelized or regular. Depending on the size and complexity of your dataset and available computational resources, choose the script that aligns best with your needs.


### New to programming? Here's a step-by-step guide on how to run these scripts

1. **Setup:** Download the scripts `main_script.sh` (or `main_main_parallelized.sh`), `process_csv.py`, `merge_csv.py`, `extract_metadata.py`, and `process_longitudinal.py` from this repository.
2. Move these scripts into your primary BIDS dataset directory, where your subjects' folders are located.
<img width="494" alt="Screenshot 2023-10-19 at 3 07 14 PM" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/40be8f00-8dd9-4ab0-b45e-a06a1f580184">

3. Open the terminal (or command prompt).
4. Navigate to your BIDS directory using `cd path/to/your/BIDS/directory`.
5. Type `bash main_script.sh` or `bash main_script_parallelized.sh` and hit Enter.
6. Follow the on-screen instructions and provide inputs as prompted.


# Output
- **Quality Control Montage:** For each subject and session, a visual quality check is provided through montages generated from Freesurfer's outputs.
  <img width="1695" alt="Screenshot 2023-10-19 at 3 10 38 PM" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/205500af-7a8b-419c-9beb-74769922d9be">

- **Freesurfer Metrics:** Detailed metrics obtained from Freesurfer processing are saved as a CSV file.
  <img width="495" alt="Screenshot 2023-10-19 at 3 09 47 PM" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/d59dcdcc-29b3-44e7-8ec0-69bfd5c8788d">

- **Longitudinal Data Stream:** For subjects with more than one session, additional longitudinal data is processed and stored.
  <img width="1621" alt="Screenshot 2023-10-19 at 3 13 03 PM" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/4faf8d5f-dd54-43c6-8177-41041e2181f4">

- **T1 Metadata:** Metadata associated with each unique T1 image is extracted and compiled into a dedicated CSV file.
  <img width="495" alt="Screenshot 2023-10-19 at 3 09 47 PM" src="https://github.com/cfatuesta/AutomatedFreeSurfer/assets/42354106/d59dcdcc-29b3-44e7-8ec0-69bfd5c8788d">

# Best Practices
- **Script Names:** Ensure that you keep the original names of all the required scripts within your `SUBJECTS_DIR` path.
- **Path Accuracy:** Always verify that the paths you've set or entered are correct before executing the scripts. Incorrect paths can trigger errors.
- **Dataset Organization:** Your dataset should be in order and correctly named. Always ensure it's structured according to the BIDS specification.

# Troubleshooting
- **Command Issues:** If you find the `freeview` or `magick` commands unrecognized, refer to the provided error message's instructions to troubleshoot.
- **Path-Related Errors:** If you input paths that either don't exist or aren't directories, you'll receive an error message, causing the script to terminate. Always double-check your paths before retrying.

# Contact
For any issues or inquiries about the scripts, reach out to Carolina Ferreira-Atuesta at cfatuesta@gmail.com.
