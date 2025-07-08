# gemini_upload.py
from selenium import webdriver
from selenium.webdriver.common.by import By
import os, time

INPUT_DIR = 'input_docs'
OUTPUT_DIR = 'processed_output'

options = webdriver.ChromeOptions()
options.add_argument("--start-maximized")
driver = webdriver.Chrome(options=options)

driver.get("https://gemini.google.com/")

# Wait for user login or auto-login using saved cookies

for file_name in os.listdir(INPUT_DIR):
    file_path = os.path.join(INPUT_DIR, file_name)
    if not file_name.lower().endswith(('.pdf', '.docx')):
        continue

    # Click upload button
    upload_button = driver.find_element(By.XPATH, '//button[contains(text(), "Upload file")]')
    upload_button.click()

    # File upload using <input type='file'>
    file_input = driver.find_element(By.XPATH, '//input[@type="file"]')
    file_input.send_keys(os.path.abspath(file_path))

    # Wait for processing (adjust as needed)
    time.sleep(20)

    # Get output text
    output_text = driver.find_element(By.CLASS_NAME, 'output-text-class').text  # Example selector
    with open(os.path.join(OUTPUT_DIR, file_name + '.txt'), 'w') as f:
        f.write(output_text)

    # Get output JSON (if available)
    # This part depends on how Gemini shows structured output

driver.quit()
