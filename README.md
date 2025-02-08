# AVATARS-INTERVIEW
import random
import os
from PIL import Image, ImageDraw, ImageFont

def generate_avatar(name, size=(200, 200), colors=None, font_path=None):
    """Generates a simple, colorful avatar image.

    Args:
        name: The name to use for the avatar (initials will be used).
        size: A tuple (width, height) specifying the avatar size.
        colors: A list of RGB color tuples. If None, random colors are used.
        font_path: Path to a .ttf font file. If None, a default font is used.

    Returns:
        A PIL Image object representing the avatar. Returns None if there's an issue.
    """

    width, height = size

    # Color selection
    if colors is None:
        colors = [(random.randint(50, 255), random.randint(50, 255), random.randint(50, 255)) for _ in range(2)]
    elif len(colors) < 2:
        raise ValueError("At least two colors are required.")

    bg_color = colors[0]
    text_color = colors[1]

    # Create image
    try:
        img = Image.new("RGB", size, bg_color)
        draw = ImageDraw.Draw(img)

        # Get initials
        name_parts = name.split()
        initials = "".join([part[0].upper() for part in name_parts])
        if len(initials) > 3:  # Limit to 3 initials
            initials = initials[:3]

        # Font selection
        if font_path:
            try:
                font = ImageFont.truetype(font_path, size=int(height * 0.5))  # Adjust font size as needed
            except IOError:
                print(f"Font file not found: {font_path}. Using default.")
                font = ImageFont.load_default()
        else:
            font = ImageFont.load_default()

        # Calculate text position (centered) - Corrected for compatibility
        try:
            bbox = draw.textbbox((0, 0), initials, font=font)
            text_width = bbox[2] - bbox[0]
            text_height = bbox[3] - bbox[1]
        except AttributeError:
            text_width, text_height = draw.textsize(initials, font=font)

        x = (width - text_width) // 2
        y = (height - text_height) // 2

        # Draw text
        draw.text((x, y), initials, fill=text_color, font=font)

        return img

    except Exception as e:
        print(f"Error generating avatar: {e}")
        return None


def save_avatar(avatar_image, filename="avatar.png"):
    """Saves the avatar image to a file."""
    if avatar_image:
        try:
            avatar_image.save(filename)
            print(f"Avatar saved to {filename}")
        except Exception as e:
            print(f"Error saving avatar: {e}")


# Example usage:
names = ["John Doe", "Alice Smith", "Bob Johnson", "Eve Rodriguez", "Michael Lee", "Sophia Davis"]
# custom_colors = [(255, 105, 180), (255, 255, 255)]  # Pink background, white text
custom_colors = [(100, 149, 237), (255, 255, 255)] # Blue background, white text

for name in names:
    avatar = generate_avatar(name, colors=custom_colors) #, font_path="/path/to/your/font.ttf")  # Uncomment to use a custom font
    if avatar:
        # Create a filename based on the name (replace spaces with underscores)
        filename = f"{name.replace(' ', '_')}_avatar.png"
        save_avatar(avatar, filename)
        # avatar.show()  # to display the image (optional)
