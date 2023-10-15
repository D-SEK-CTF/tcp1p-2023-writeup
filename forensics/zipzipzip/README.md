# zipzipzip

The solution consists of extracting each of the 25.000 layers, with each layer containing a password.txt file and a new .zip file. By scripting this process you can do it in a reasonable amount of time.

Solution using 7-zip:

```python
import subprocess
import os

def unzip_with_7z(zip_path: str, output_dir: str, password: str):
    result = subprocess.run(
        [
            "7z",
            "e",
            f"-p{password}",
            "-o" + output_dir,
            zip_path,
            "-aoa",
        ],
        capture_output=True,
        text=True,
    )

def main():
    initial_zip = "zip-25000.zip"
    initial_password = "Mo0YAEJs"
    output_dir = "./unzip_temp"
    current_password = initial_password

    if not os.path.exists(output_dir):
        os.makedirs(output_dir)

    unzip_with_7z(initial_zip, output_dir, initial_password)

    iteration = 1
    while True:
        print(f"Processing layer {iteration}, pass: '{current_password}'...")

        zip_files = sorted([f for f in os.listdir(output_dir) if f.endswith(".zip")])
        password_file = "password.txt"

        if not zip_files:
            print("No more .zip files found. Exiting.")
            break

        current_zip = os.path.join(output_dir, zip_files[-1])
        password_path = os.path.join(output_dir, password_file)
        if os.path.exists(password_path):
            with open(password_path, "r") as f:
                current_password = f.read().strip()

        unzip_with_7z(current_zip, output_dir, current_password)
        os.remove(current_zip)
        iteration += 1

if __name__ == "__main__":
    main()
```

Flag: `TCP1P{1_TH1NK_U_G00D_4T_SCR1PT1N9_botanbell_1s_h3r3^_^}`