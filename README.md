import tkinter as tk
import math

class CircularTimer:
    def __init__(self, root):
        self.root = root
        self.root.title("Circular Timer")

        # Create canvas for drawing the timer
        self.canvas = tk.Canvas(root, width=400, height=400, bg="white")
        self.canvas.pack()

        # Entry for time input
        self.time_entry = tk.Entry(root, width=10)
        self.time_entry.pack(pady=20)

        # Start button
        self.start_button = tk.Button(root, text="Start Timer", command=self.start_timer)
        self.start_button.pack(pady=5)

        self.remaining_time = 0
        self.running = False

    def start_timer(self):
        try:
            total_seconds = int(self.time_entry.get())
            if total_seconds < 0:
                raise ValueError
            self.remaining_time = total_seconds
            self.running = True
            self.update_timer()
        except ValueError:
            self.canvas.delete("all")
            self.canvas.create_text(200, 200, text="Enter valid seconds", font=("Helvetica", 16), fill="red")

    def update_timer(self):
        if self.running and self.remaining_time > 0:
            # Calculate hours, minutes, and seconds
            hours, remainder = divmod(self.remaining_time, 3600)
            minutes, seconds = divmod(remainder, 60)

            # Clear the canvas
            self.canvas.delete("all")

            # Draw the circular timer
            self.draw_circle(hours, minutes, seconds)

            self.remaining_time -= 1
            self.root.after(1000, self.update_timer)  # Call this method again after 1 second
        elif self.remaining_time == 0:
            self.canvas.delete("all")
            self.canvas.create_text(200, 200, text="Time's up!", font=("Helvetica", 32), fill="red")
            self.running = False

    def draw_circle(self, hours, minutes, seconds):
        # Center of the circle
        center_x = 200
        center_y = 200
        radius = 150

        # Draw the outer circle
        self.canvas.create_oval(center_x - radius, center_y - radius, center_x + radius, center_y + radius)

        # Calculate the angle for each time unit
        total_seconds = 3600  # Total seconds in one hour for complete rotation
        elapsed_seconds = (hours * 3600) + (minutes * 60) + seconds
        angle = (elapsed_seconds / total_seconds) * 360

        # Calculate end position for the arc
        end_x = center_x + radius * math.sin(math.radians(angle))
        end_y = center_y - radius * math.cos(math.radians(angle))

        # Draw the arc indicating the elapsed time
        self.canvas.create_arc(center_x - radius, center_y - radius, center_x + radius, center_y + radius,
                                start=90, extent=-angle, outline="blue", style=tk.ARC, width=5)

        # Display the remaining time in the center
        time_str = f"{hours:02d}:{minutes:02d}:{seconds:02d}"
        self.canvas.create_text(center_x, center_y, text=time_str, font=("Helvetica", 24))

if __name__ == "__main__":
    root = tk.Tk()
    timer_app = CircularTimer(root)
    root.mainloop()
