
I saw a video where AIA (https://www.youtube.com/@AIAdev) made a competition where devs tried to make the best AI-car possible in a Formula 1-like competition. 

I'll probably try and make one of those at some time, but that is not the topic of today. 

There was a part where he made an animation of a "driver" showing a team radio-like text. 

I really liked the template, so I immediately went into Canva and tried to make my own template for my videos. However, I the found it bothersome to have to go back and make it over and over again. Specially if I wished to use it for a future video [[Formula 1 chat (1)]], It would be boring and time consuming to have to make the graphics. 

So i went into a duel with ChatGPT to write a code that generates the template...

```python
from PIL import Image, ImageDraw, ImageFont, ImageFilter

import random

import math

def random_color():

    # 2024 F1 team colors (approximate RGB values)

    colors = [

        (0, 120, 255),    # Red Bull Racing (dark blue)

        (255, 0, 0),      # Ferrari (red)

        (0, 255, 255),    # Mercedes-AMG (teal/Petronas)

        (255, 255, 0),    # McLaren (papaya yellow-ish)

        (255, 80, 5),     # McLaren (papaya orange)

        (0, 255, 0),      # Aston Martin (British racing green)

        (245, 0, 87),     # Alpine (pink BWT)

        (255, 20, 147),   # Stake F1 (formerly Alfa Romeo - pink/magenta)

        (128, 0, 128),    # Williams (purple/blue blend)

        (255, 192, 203),  # Haas (soft red/pink)

    ]

    return random.choice(colors)

  

def draw_waveform_line(base_img, bar_color):

    """Draw wide randomized waveform with glow effect, 10px margin on each side"""

    width, height = base_img.size

    waveform_img = Image.new("RGBA", base_img.size, (0, 0, 0, 0))

    draw = ImageDraw.Draw(waveform_img)

    # Parameters

    bar_width = 6

    spacing = 4

    max_height = int(height * 0.15)

    bar_base_y = int(height * 0.46)

    margin = 10

  

    # Compute how many bars can fit between margins

    available_width = width - 2 * margin

    num_bars = (available_width + spacing) // (bar_width + spacing)

    total_width = (num_bars - 1) * (bar_width + spacing) + bar_width

    start_x = (width - total_width) // 2  # still center just in case margin isn't even

  

    mid = num_bars // 2

  

    # Randomized waveform heights

    heights = []

    for i in range(num_bars):

        proximity = 1 - abs(i - mid) / mid

        base = max_height * (0.5 + 0.5 * proximity)

        noise = random.uniform(0.4, 1.0)

        heights.append(int(base * noise))

  

    # Draw bars

    for i, h in enumerate(heights):

        x = start_x + i * (bar_width + spacing)

        draw.rectangle([x, bar_base_y - h, x + bar_width, bar_base_y], fill=bar_color)

  

    # Draw bottom line

    draw.line([(start_x, bar_base_y + 4),

               (start_x + total_width, bar_base_y + 4)],

              fill=bar_color, width=4)

  

    # Glow effect

    blurred = waveform_img.filter(ImageFilter.GaussianBlur(radius=6))

    base_img.alpha_composite(blurred)

    base_img.alpha_composite(waveform_img)

  

def wrap_text(text, font, max_width):

    """Wraps text to fit within max_width using font metrics and preserves line breaks"""

    lines = []

    for raw_line in text.split('\n'):  # handle explicit newlines first

        words = raw_line.split()

        current_line = ""

        for word in words:

            test_line = current_line + word + " "

            bbox = font.getbbox(test_line)

            text_width = bbox[2] - bbox[0]

            if text_width <= max_width:

                current_line = test_line

            else:

                lines.append(current_line.strip())

                current_line = word + " "

        lines.append(current_line.strip())

    return lines

  
  

def generate_message(driver_name, driver_msg, engineering_msg, output_file="radio_output.png"):

    # Create a black background base image

    base_img = Image.open("Formula 1 template.png").convert("RGBA")

  

    # Pick color for the team/driver theme

    team_color = random_color()

  

    # Draw waveform using selected team color

    draw_waveform_line(base_img, bar_color=team_color)

  

    # Load fonts

    font_driver_name = ImageFont.truetype("C:/Windows/Fonts/arial.ttf", 100)

    font_quote_blue = ImageFont.truetype("C:/Windows/arial.ttf", 70)

    font_quote_white = ImageFont.truetype("C:/Windows/arialbd.ttf", 70)

  

    # Draw text

    draw = ImageDraw.Draw(base_img)

    max_text_width = (base_img.size[0] - 160)//2  # Leave 50px margin each side

    print(f"Max text width: {max_text_width}")

    wrapped_lines = wrap_text(driver_msg, font_quote_blue, max_text_width)

    draw.text((500, 50), driver_name, font=font_driver_name, fill=team_color)

    # Draw each wrapped line

    bbox = font_quote_blue.getbbox("A")

    line_height = (bbox[3] - bbox[1]) + 10  # Add spacing

    start_y = 600

    end_y = 600 + len(wrapped_lines) * line_height

    for i, line in enumerate(wrapped_lines):

        draw.text((500, start_y + i * line_height), line, font=font_quote_blue, fill=team_color)

    # draw.text((400, 600), driver_msg, font=font_quote_blue, fill=team_color)

    draw.text((50, end_y+50), engineering_msg, font=font_quote_white, fill=(255, 255, 255))

  

    # Save image

    base_img.save(output_file)


# Example usage

generate_message("Iker Fuentes", "I want to push now.", "Understood")
```


The general idea was to get an input prompt for the driver name, the message and the **engineer's** message. 

```python
def generate_message(driver_name, driver_msg, engineering_msg, output_file="radio_output.png"):

```


I initially had the idea to use the wave pattern I got from an actual image, but I soon realized that it had a fixed color so I would not be able to change the color and have a fixed template. So, that pretty much left me like I started. 

To actually get the image, I got this function to generate the wavelength with the color.

```python
def draw_waveform_line(base_img, bar_color):

    """Draw wide randomized waveform with glow effect, 10px margin on each side"""

    width, height = base_img.size

    waveform_img = Image.new("RGBA", base_img.size, (0, 0, 0, 0))

    draw = ImageDraw.Draw(waveform_img)

  

    # Parameters

    bar_width = 6

    spacing = 4

    max_height = int(height * 0.15)

    bar_base_y = int(height * 0.46)

    margin = 10

  

    # Compute how many bars can fit between margins

    available_width = width - 2 * margin

    num_bars = (available_width + spacing) // (bar_width + spacing)

    total_width = (num_bars - 1) * (bar_width + spacing) + bar_width

    start_x = (width - total_width) // 2  # still center just in case margin isn't even

  

    mid = num_bars // 2

  

    # Randomized waveform heights

    heights = []

    for i in range(num_bars):

        proximity = 1 - abs(i - mid) / mid

        base = max_height * (0.5 + 0.5 * proximity)

        noise = random.uniform(0.4, 1.0)

        heights.append(int(base * noise))

  

    # Draw bars

    for i, h in enumerate(heights):

        x = start_x + i * (bar_width + spacing)

        draw.rectangle([x, bar_base_y - h, x + bar_width, bar_base_y], fill=bar_color)

  

    # Draw bottom line

    draw.line([(start_x, bar_base_y + 4),

               (start_x + total_width, bar_base_y + 4)],

              fill=bar_color, width=4)

  

    # Glow effect

    blurred = waveform_img.filter(ImageFilter.GaussianBlur(radius=6))

    base_img.alpha_composite(blurred)

    base_img.alpha_composite(waveform_img)
```


> [!NOTE] Wave Image
> ![[Pasted image 20250705201906.png]]


That is an example of the generate wave image. To be honest, I still want to refine it and make it more like the actual team radio message, so I'll improve that and report on that in a future note. 


The other problem was the size of the messages.

Even though it was not my intention to add a lot of text, if I were, for any reason going to right a slightly larger text, It would overflow and look ugly. Therefore I made a code that divides the text in lines and then draws them, to avoid the overflow. 


```python
def wrap_text(text, font, max_width):

    """Wraps text to fit within max_width using font metrics and preserves line breaks"""

    lines = []

    for raw_line in text.split('\n'):  # handle explicit newlines first

        words = raw_line.split()

        current_line = ""

        for word in words:

            test_line = current_line + word + " "

            bbox = font.getbbox(test_line)

            text_width = bbox[2] - bbox[0]

            if text_width <= max_width:

                current_line = test_line

            else:

                lines.append(current_line.strip())

                current_line = word + " "

        lines.append(current_line.strip())

    return lines
```


After all that, the end result was something like this...


![[Pasted image 20250705202233.png]]