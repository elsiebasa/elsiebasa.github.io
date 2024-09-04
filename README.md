## Developer Portfolio Landing Page Template

### Introduction

Use this template if you need a quick developer / data science portfolio! Based on a Minimal Jekyll theme for GitHub Pages.

<img src="images/demo.gif?raw=true"/>

### Installation

See full step by step tutorial [on Medium](https://medium.com/@evanca/set-up-your-portfolio-website-in-less-than-10-minutes-with-github-pages-d0efa8ff56fd).
___

You can use the editor on GitHub to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Roadmap

See the [open issues](https://github.com/evanca/quick-portfolio/issues) for a list of proposed features (and known issues).
___

### References

[1] Jekyll theme "Minimal" for GitHub Pages: https://github.com/pages-themes/minimal (CC0 1.0 Universal License)
<br>[2] Dummy photo via: https://pixabay.com/photos/man-male-adult-person-caucasian-1209494/ (Pixabay License)
<br>[3] Dummy thumbnail image created by rawpixel.com: https://www.freepik.com/free-vector/set-elements-infographic_2807573.htm (Standard Freepik License)


import os
import csv
from PyPDF2 import PdfReader
from pdf2image import convert_from_path
import pytesseract

def extract_text_from_pdf(pdf_path):
    reader = PdfReader(pdf_path)
    images = convert_from_path(pdf_path)
    full_text = ""

    for page_num, page in enumerate(reader.pages):
        # Extract text from the text layer of the PDF page
        text = page.extract_text() or ""
        full_text += text

        # Extract text from the corresponding image of the same page using OCR
        if page_num < len(images):
            ocr_text = pytesseract.image_to_string(images[page_num])
            full_text += "\n" + ocr_text

    return full_text

def process_pdfs(folder_a, folder_b, output_csv):
    files_a = sorted(os.listdir(folder_a))
    files_b = sorted(os.listdir(folder_b))

    with open(output_csv, 'w', newline='', encoding='utf-8') as csvfile:
        csv_writer = csv.writer(csvfile)
        csv_writer.writerow(['Report Name', 'Report Text', 'Comments Name', 'Comments Text'])

        for file_a, file_b in zip(files_a, files_b):
            path_a = os.path.join(folder_a, file_a)
            path_b = os.path.join(folder_b, file_b)

            text_a = extract_text_from_pdf(path_a)
            text_b = extract_text_from_pdf(path_b)

            csv_writer.writerow([file_a, text_a, file_b, text_b])

if __name__ == "__main__":
    folder_a = "path/to/folder_a"
    folder_b = "path/to/folder_b"
    output_csv = "output.csv"

    process_pdfs(folder_a, folder_b, output_csv)

