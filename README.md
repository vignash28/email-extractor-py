import re
import sys
import os
import csv
from PyPDF2 import PdfReader

def extract_emails(text):
    return re.findall(r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}", text)

def from_txt(file_path):
    with open(file_path, 'r', encoding='utf-8') as f:
        return f.read()

def from_pdf(file_path):
    reader = PdfReader(file_path)
    text = ""
    for page in reader.pages:
        text += page.extract_text()
    return text

def save_to_csv(emails, output_file):
    with open(output_file, 'w', newline='', encoding='utf-8') as f:
        writer = csv.writer(f)
        writer.writerow(['Email'])
        for email in emails:
            writer.writerow([email])

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python extract_emails.py <file_path>")
        sys.exit(1)

    path = sys.argv[1]
    if not os.path.exists(path):
        print("File not found!")
        sys.exit(1)

    if path.endswith('.txt'):
        content = from_txt(path)
    elif path.endswith('.pdf'):
        content = from_pdf(path)
    else:
        print("Unsupported file type. Use .txt or .pdf.")
        sys.exit(1)

    emails = list(set(extract_emails(content)))
    if emails:
        save_to_csv(emails, 'emails_output.csv')
        print(f"✅ Found {len(emails)} emails. Saved to emails_output.csv.")
    else:
        print("No emails found.")
