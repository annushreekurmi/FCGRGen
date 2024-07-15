# FCGRGen
FCGRGen: Frequency Chaos Game Representation Generator
FCGRGen is a graphical user interface (GUI) tool for generating Frequency Chaos Game Representation (FCGR) images from DNA sequences in FASTA format. This tool allows users to specify nucleotide positions, image modes, and other parameters to create visual representations of DNA sequences.
# Features
Read DNA sequences from FASTA files.
Generate FCGR images in Grayscale or RGB mode.
Customize nucleotide positions for generating the FCGR.
Real-time status updates during the image generation process.
Output images saved in the specified directory.
# System Requirements
Operating System: Windows 10 (also tested on Linux)
Python: 3.6 or higher
RAM: Minimum 4 GB
# Installation
Windows
Clone the repository or download the ZIP file and extract it.
Install the required Python packages:
pip install matplotlib Pillow tkinter
Run the script directly:
python fcgrGen-15-07-24.py

Linux
Clone the repository or download the ZIP file and extract it.
Install the required Python packages:
pip install matplotlib Pillow tkinter
Run the script directly:
python fcgrGen-15-07-24.py
# Usage
Launch the Tool: Run the script fcgrGen-15-07-24.py using Python.

Input FASTA File: Click the "Browse" button next to the "Input FASTA file" field to select a FASTA file containing DNA sequences.

Output Folder: Click the "Browse" button next to the "Output folder" field to select the directory where the generated images will be saved.

Value of k: Enter the desired value of k (an integer) for k-mer analysis.

Nucleotide Positions: Select the nucleotide positions for bottom left, bottom right, top left, and top right.

Image Mode: Select the image mode (Grayscale or RGB) from the dropdown menu.

Generate Image: Click the "Generate FCGR Image" button to start the image generation process.

# Real-Time Feedback
Progress Update Messages: The status label will display messages indicating the current stage of the operation, such as reading the input FASTA file and generating the FCGR images.

# Troubleshooting
Ensure all input fields are correctly filled before clicking the "Generate FCGR Image" button.
Check for any error messages displayed in the status label during the image generation process.
Verify that the output folder has write permissions.

# License
This project is licensed under the MIT License. See the LICENSE file for details.

# Contact
For any inquiries or issues, please contact Annushree Kurmi at annushreekurmi@gmail.com
