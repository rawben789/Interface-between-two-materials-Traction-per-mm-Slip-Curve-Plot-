# Interface-between-two-materials-Traction-per-mm-Slip-Curve-Plot-
This is the python model for the curve generation for the column friction interface modeling. It a simple program that will accept excel with average traction data arranged for different models that is used in my research of Himalayan Dry-Stone Masonry.  


import pandas as pd
import matplotlib.pyplot as plt
import tkinter as tk
from tkinter import filedialog
import random

# Random color and marker generator
def get_random_style():
    colors = ['b', 'g', 'r', 'c', 'm', 'y', 'k']
    markers = ['o', 's', '^', 'D', 'v', '*', 'x', '+']
    return random.choice(colors), random.choice(markers)

# File selection dialog
def select_excel_file():
    root = tk.Tk()
    root.withdraw()
    file_path = filedialog.askopenfilename(title="Select Excel File", filetypes=[("Excel files", "*.xlsx *.xls")])
    return file_path

# Main plotting function
def plot_selected_curves():
    file_path = select_excel_file()
    if not file_path:
        print("No file selected.")
        return

    df = pd.read_excel(file_path)

    # Set fixed axis labels with bold font
    x_label = "Load Steps"
    y_label = "Traction per mm Slip (N/mm)"

    x_values = df.iloc[:, 0]  # First column as x-axis

    plt.figure(figsize=(10, 6))

    # Define color and marker mapping for known curve names
    style_map = {
        'WIWW':    ('b', 'o'),
        'WIWW_GS': ('g', 's'),
        'WIWOW':   ('r', '^'),
        'WIWOW_G': ('orange', 'D'),
        'WIWW_G':  ('purple', 'x'),
        'WIWW_S':  ('k', '*'),
    }

    # Ask user if they want pointers (markers)
    use_pointers = input("Show pointers/markers on curves? (y/n): ").strip().lower() == 'y'
    marker_size = 5
    if use_pointers:
        if input("Use smallest possible pointer size? (y/n): ").strip().lower() == 'y':
            marker_size = 3

    for col_name in df.columns[1:]:
        y_values = df[col_name]
        if col_name in style_map:
            color, marker = style_map[col_name]
        else:
            color, marker = get_random_style()

        if use_pointers:
            # Show marker for all values
            plt.plot(
                x_values, y_values,
                color=color, marker=marker, linestyle='-',
                label=col_name,
                markersize=marker_size
            )
        else:
            # Plot line only, no markers
            plt.plot(
                x_values, y_values,
                color=color, linestyle='-', label=col_name
            )

    plt.xlabel(x_label, fontweight='bold')
    plt.ylabel(y_label, fontweight='bold')
    plt.title("Generated Curves")
    plt.legend()
    plt.grid(True)
    plt.tight_layout()
    plt.show()

# Run the function
plot_selected_curves()
