import re
import math
from tqdm import tqdm
import os
import streamlit as st

def clean_text(text):
    cleaned_text = re.sub(r'[^a-zA-Z\s]', '', text)
    return cleaned_text.lower()

def load_existing_words(output_file):
    existing_words = set()
    if os.path.exists(output_file):
        with open(output_file, 'r', encoding='utf-8') as html_file:
            for line in html_file:
                match = re.search(r'<p>\d+\. (.+)</p>', line)
                if match:
                    word = match.group(1)
                    existing_words.add(word)
    return existing_words

def store_unique_words(input_file, output_file, words_per_column=100000):
    existing_words = load_existing_words(output_file)
    unique_words = set(existing_words)

    with open(input_file, 'r', encoding='utf-8') as file:
        for line in tqdm(file, desc="Processing lines", unit="line"):
            words = line.split()
            cleaned_words = [clean_text(word) for word in words]
            unique_words.update(word for word in cleaned_words if len(word) > 2)

    sorted_unique_words = sorted(list(unique_words))
    num_columns = math.ceil(len(sorted_unique_words) / words_per_column)

    with open(output_file, 'w', encoding='utf-8') as html_file:
        html_file.write('<html><body>\n')
        total_words = len(sorted_unique_words)

        for col in range(num_columns):
            html_file.write(f'<div style="float:left; width:50%;">\n')
            html_file.write(f'<h2>Column {col + 1}</h2>\n')

            start_idx = col * words_per_column
            end_idx = min((col + 1) * words_per_column, total_words)

            for idx, word in tqdm(enumerate(sorted_unique_words[start_idx:end_idx], start=start_idx + 1),
                                  desc=f"Writing to HTML (Column {col + 1})", total=end_idx - start_idx, unit="word"):
                html_file.write(f'<p>{idx}. {word}</p>\n')

            html_file.write('</div>\n')

        html_file.write('</body></html>')

    return total_words

def main():
    st.title("English Words Streamlit App")

    input_txt_file = st.file_uploader("Choose a text file", type=["txt"])
    if input_txt_file is not None:
        output_html_file = st.text_input("Enter the output HTML file name", "english_words.html")

        if st.button("Generate HTML"):
            total_words = store_unique_words(input_txt_file, output_html_file, words_per_column=100000)
            st.success(f"Total English words stored: {total_words}")
            st.success(f"Unique English words with serial numbers have been stored in {output_html_file}")

if __name__ == "__main__":
    main()
