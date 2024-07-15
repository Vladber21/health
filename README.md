# health
import tkinter as tk
from tkinter import messagebox
import time
import json

class ProductiveTimeTracker:
    def __init__(self, root):
        self.root = root
        self.root.title("Productive Time Tracker")
        
        self.start_time = None
        self.total_time = 0
        self.running = False

        self.load_data()

        self.label = tk.Label(root, text="Press Start to begin tracking productive time")
        self.label.pack()

        self.start_button = tk.Button(root, text="Start", command=self.start)
        self.start_button.pack()

        self.stop_button = tk.Button(root, text="Stop", command=self.stop, state=tk.DISABLED)
        self.stop_button.pack()

        self.total_label = tk.Label(root, text=f"Total productive time: {self.format_time(self.total_time)}")
        self.total_label.pack()

        self.save_button = tk.Button(root, text="Save", command=self.save_data)
        self.save_button.pack()

    def start(self):
        if not self.running:
            self.start_time = time.time()
            self.label.config(text="Tracking productive time...")
            self.start_button.config(state=tk.DISABLED)
            self.stop_button.config(state=tk.NORMAL)
            self.running = True

    def stop(self):
        if self.running:
            end_time = time.time()
            elapsed_time = end_time - self.start_time
            self.total_time += elapsed_time
            self.label.config(text=f"Session time: {self.format_time(elapsed_time)}")
            self.total_label.config(text=f"Total productive time: {self.format_time(self.total_time)}")
            self.start_button.config(state=tk.NORMAL)
            self.stop_button.config(state=tk.DISABLED)
            self.running = False

    def format_time(self, seconds):
        mins, secs = divmod(seconds, 60)
        hours, mins = divmod(mins, 60)
        return f"{int(hours):02}:{int(mins):02}:{int(secs):02}"

    def save_data(self):
        data = {
            "total_time": self.total_time
        }
        with open("productive_time.json", "w") as file:
            json.dump(data, file)
        messagebox.showinfo("Data Saved", "Your productive time has been saved.")

    def load_data(self):
        try:
            with open("productive_time.json", "r") as file:
                data = json.load(file)
                self.total_time = data.get("total_time", 0)
        except FileNotFoundError:
            self.total_time = 0

if __name__ == "__main__":
    root = tk.Tk()
    app = ProductiveTimeTracker(root)
    root.mainloop()
