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


# Code:

import os
import collections
from matplotlib import pyplot as plt
from matplotlib import cm
import math
import tkinter as tk
from tkinter import filedialog
from PIL import Image, ImageTk, ImageOps
from PIL.Image import Resampling

global img_ref
img_ref = None

script_dir = os.path.dirname(os.path.abspath(__file__))

# Construct the path to the image relative to the script's directory
image_path = os.path.join(script_dir, "gui-image-28-06-24.png")

def read_fasta(file_path):
    absolute_path = os.path.abspath(file_path)
    print("Attempting to open file:", absolute_path)

    try:
        with open(absolute_path, 'r') as f:
            lines = f.readlines()
    except Exception as e:
        print(f"Error opening file: {e}")
        return {}

    sequences = {}
    current_sequence = None

    for line in lines:
        line = line.strip()  # Remove leading/trailing whitespace
        if line.startswith('>'):
            current_sequence = line[1:].strip()
            sequences[current_sequence] = ""
        else:
            sequences[current_sequence] += line

    return sequences

def count_kmers(sequence, k):
    d = collections.defaultdict(int)
    for i in range(len(sequence)-(k-1)):
        d[sequence[i:i+k]] += 1
    for key in list(d.keys()):
        if "N" in key:
            del d[key]
    return d

def probabilities(kmer_count, k, sequence_length):
    probabilities = collections.defaultdict(float)
    for key, value in kmer_count.items():
        probabilities[key] = float(value) / (sequence_length - k + 1)
    return probabilities

def chaos_game_representation(probabilities, k, coords, mode):
    array_size = int(math.sqrt(4**k))
    
    if mode == 'Grayscale':
        chaos = [[0] * array_size for _ in range(array_size)]
    else:
        chaos = [[[0, 0, 0] for _ in range(array_size)] for _ in range(array_size)]

    for key, value in probabilities.items():
        posx, posy = 0, 0
        for char in key:
            posx = 2 * posx
            posy = 2 * posy
            if char in ["t", "T"]:
                dx, dy = coords['T']
            elif char in ["c", "C"]:
                dx, dy = coords['C']
            elif char in ["g", "G"]:
                dx, dy = coords['G']
            elif char in ["a", "A"]:
                dx, dy = coords['A']
            posx += dx
            posy += dy

        if mode == 'Grayscale':
            chaos[posy][posx] = value
        else:
            # Scale the value to [0, 1] range for RGB
            scaled_value = value * 255
            if char in ["t", "T"]:
                chaos[posy][posx] = [scaled_value, 0, 0]  # Red
            elif char in ["c", "C"]:
                chaos[posy][posx] = [0, scaled_value, 0]  # Green
            elif char in ["g", "G"]:
                chaos[posy][posx] = [0, 0, scaled_value]  # Blue
            elif char in ["a", "A"]:
                chaos[posy][posx] = [scaled_value, scaled_value, 0]  # Yellow (Red + Green)

    return chaos

import re

def sanitize_filename(filename):
    return re.sub(r'[\\/:*?"<>|]', '_', filename)

def generate_chaos_image(fasta_file_path, output_folder, k, coords, mode, status_label, root):
    status_label.config(text="Generating FCGR image...")
    root.update_idletasks()  # Force the GUI to update

    sequences = read_fasta(fasta_file_path)

    if not sequences:
        status_label.config(text="No valid sequences found in the input file.")
        return

    for gene_id, gene_sequence in sequences.items():
        sequence_length = len(gene_sequence)
        fk = count_kmers(gene_sequence, k)
        fk_prob = probabilities(fk, k, sequence_length)
        chaos_k = chaos_game_representation(fk_prob, k, coords, mode)

        if mode == 'Grayscale':
            plt.imshow(chaos_k, interpolation='nearest', cmap=cm.gray_r, extent=[0, 1, 0, 1])
        else:
            plt.imshow(chaos_k, interpolation='nearest', extent=[0, 1, 0, 1])
        
        plt.axis('off')

        sanitized_gene_id = sanitize_filename(gene_id)
        output_path = os.path.join(output_folder, f'{sanitized_gene_id}_k{k}.png')

        try:
            plt.savefig(output_path)
            print(f"Image saved: {output_path}")
        except Exception as e:
            print(f"Error saving the image: {e}")
            status_label.config(text=f"Error saving the image: {e}")
            status_label.after(5000, lambda: status_label.config(text=""))
            return

    status_label.config(text="Image generation completed successfully.")
    root.update_idletasks()  # Force the GUI to update
    status_label.after(5000, lambda: status_label.config(text=""))

    # Reload the default image
    default_img = Image.open(image_path)
    default_img = default_img.resize((500, 250), Resampling.LANCZOS)
    default_img_tk = ImageTk.PhotoImage(default_img)
    img_ref.config(image=default_img_tk)
    img_ref.image = default_img_tk
    root.update_idletasks()  # Force the GUI to update

def open_file_dialog(entry):
    file_path = filedialog.askopenfilename()
    entry.delete(0, tk.END)
    entry.insert(0, file_path)

def open_folder_dialog(entry):
    folder_path = filedialog.askdirectory()
    entry.delete(0, tk.END)
    entry.insert(0, folder_path)

def start_processing(entry_file, entry_output, entry_k, dropdowns, img_mode, status_label, root, img_ref):
    fasta_file_path = entry_file.get()
    output_folder = entry_output.get()
    k = int(entry_k.get())
    
    coords_mapping = {
        'bottom_left': (0, 0),
        'bottom_right': (1, 0),
        'top_left': (0, 1),
        'top_right': (1, 1)
    }
    
    nucleotides = {}
    for position, dropdown in dropdowns.items():
        nucleotide = dropdown.get()
        if nucleotide in nucleotides:
            status_label.config(text=f"Error: {nucleotide} is assigned to multiple positions.")
            return
        nucleotides[nucleotide] = coords_mapping[position]
    
    if len(nucleotides) != 4:
        status_label.config(text="Error: Each nucleotide must be assigned a unique position.")
        return

    coords = {nucleotide: coords for nucleotide, coords in nucleotides.items()}
    mode = img_mode.get()

    try:
        os.makedirs(output_folder, exist_ok=True)
    except Exception as e:
        print(f"Error creating the output folder: {e}")
        status_label.config(text=f"Error creating the output folder: {e}")
        return

    generate_chaos_image(fasta_file_path, output_folder, k, coords, mode, status_label, root)

root = tk.Tk()
root.title("FCGRGen: Frequency Chaos Game Representation Generator")

# Set a minimum size for the window to prevent shrinking
root.minsize(600, 600)

tk.Label(root, text="Input FASTA file:").grid(row=0, column=0, padx=5, pady=5)
entry_file = tk.Entry(root)
entry_file.grid(row=0, column=1, padx=5, pady=5)
tk.Button(root, text="Browse", command=lambda: open_file_dialog(entry_file)).grid(row=0, column=2, padx=5, pady=5)

tk.Label(root, text="Output folder:").grid(row=1, column=0, padx=5, pady=5)
entry_output = tk.Entry(root)
entry_output.grid(row=1, column=1, padx=5, pady=5)
tk.Button(root, text="Browse", command=lambda: open_folder_dialog(entry_output)).grid(row=1, column=2, padx=5, pady=5)

tk.Label(root, text="Value of k:").grid(row=2, column=0, padx=5, pady=5)
entry_k = tk.Entry(root)
entry_k.grid(row=2, column=1, padx=5, pady=5)

positions = ['bottom_left', 'bottom_right', 'top_left', 'top_right']
labels = ['Select nucleotide for bottom left:', 'Select nucleotide for bottom right:', 'Select nucleotide for top left:', 'Select nucleotide for top right:']
dropdowns = {}

# List of unique default nucleotides
default_nucleotides = ["A", "T", "C", "G"]

for i, pos in enumerate(positions):
    tk.Label(root, text=labels[i]).grid(row=3+i, column=0, padx=5, pady=5)
    dropdown = tk.StringVar(root)
    dropdown.set(default_nucleotides[i])  # Set different default nucleotide
    dropdowns[pos] = dropdown
    tk.OptionMenu(root, dropdown, "A", "T", "C", "G").grid(row=3+i, column=1, padx=5, pady=5)

tk.Label(root, text="Image mode:").grid(row=7, column=0, padx=5, pady=5)
img_mode = tk.StringVar(root)
img_mode.set("Grayscale")
tk.OptionMenu(root, img_mode, "Grayscale", "RGB").grid(row=7, column=1, padx=5, pady=5)

status_label = tk.Label(root, text="")
status_label.grid(row=8, column=0, columnspan=3, padx=5, pady=5)

default_img = Image.open(image_path)
default_img = default_img.resize((500, 250), Resampling.LANCZOS)
default_img_tk = ImageTk.PhotoImage(default_img)
img_ref = tk.Label(root, image=default_img_tk)
img_ref.grid(row=10, column=0, columnspan=3, padx=5, pady=5)

tk.Button(root, text="Generate FCGR Image", command=lambda: start_processing(entry_file, entry_output, entry_k, dropdowns, img_mode, status_label, root, img_ref)).grid(row=9, column=0, columnspan=3, pady=10)

root.mainloop()


