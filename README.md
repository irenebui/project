try:
    from PIL import Image, ImageTk
except ModuleNotFoundError:
    import subprocess
    import sys
    print("Pillow is not installed. Installing it now...")
    subprocess.check_call([sys.executable, "-m", "pip", "install", "pillow"])
    from PIL import Image, ImageTk  # Retry import after installation

import tkinter as tk
from tkinter.colorchooser import askcolor
from PIL import Image, ImageTk


class ColorLabApp:
    def __init__(self, root):
        self.root = root
        self.root.title("ColorLab: Interactive Coloring Game")

        # Canvas setup
        self.canvas = tk.Canvas(root, width=800, height=600, bg="white", highlightthickness=0)
        self.canvas.pack(side=tk.LEFT, padx=10, pady=10)

        #not sure if .webp is supported for the graphic so convert it to .png
        input_path= "TulipColoringPage.webp"
        output_path= "TulipColoringPage.png"
        image= Image.open(input_path)
        image.save(output_path, form= "PNG")

        # Load and display the graphic
        self.image = Image.open("TulipColoringPage.png")
        width, height= self.image.size
        aspect_ratio=width/height
        new_width= 600
        new_height= int(new_width/ aspect_ratio)
        if new_height> 400:
            new_height=400
            new_width= int(new_height*aspect_ratio)
        self.image = self.image.resize((new_width, new_height))
        self.graphic = ImageTk.PhotoImage(self.image)
        self.canvas.create_image(100, 100, anchor=tk.NW, image=self.graphic)

        # Color selection buttons
        self.selected_color = "black"
        self.color_frame = tk.Frame(root)
        self.color_frame.pack(side=tk.RIGHT, padx=10, pady=10)

        colors = ["red", "blue", "green", "yellow", "pink", "black"]
        for color in colors:
            btn = tk.Button(self.color_frame, bg=color, width=3, height=2, command=lambda c=color: self.set_color(c))
            btn.pack(pady=5)

        # Custom color picker
        custom_color_btn = tk.Button(self.color_frame, text="Custom", command=self.choose_color)
        custom_color_btn.pack(pady=5)

        # Reset button
        reset_btn = tk.Button(self.color_frame, text="Reset", command=self.reset_canvas)
        reset_btn.pack(pady=20)

        # Bind canvas clicks
        self.canvas.bind("<Button-1>", self.color_region)

    def set_color(self, color):
        self.selected_color = color

    def choose_color(self):
        color_code = askcolor(title="Choose Color")[1]
        if color_code:
            self.selected_color = color_code

    def color_region(self, event):
        x, y = event.x, event.y
        self.canvas.create_oval(x-5, y-5, x+5, y+5, fill=self.selected_color, outline=self.selected_color)

    def reset_canvas(self):
        self.canvas.delete("all")
        self.canvas.create_image(100, 100, anchor=tk.NW, image=self.graphic)


if __name__ == "__main__":
    root = tk.Tk()
    app = ColorLabApp(root)
    root.mainloop()

