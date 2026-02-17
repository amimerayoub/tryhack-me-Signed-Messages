# Signed Messages – TryHackMe Exploit Script

This Python script solves the **Signed Messages** challenge in the [TryHackMe room “Love at First Breach 2026”](https://tryhackme.com/room/lafb2026e8).  
It generates a forged RSA‑PSS signature for the user `admin` by exploiting a **predictable seed** used to derive the server’s 512‑bit RSA key.

Once the signature is submitted on the web application’s verification page, you are authenticated as `admin` and the flag is revealed.

---

## 🔍 How It Works

1. **Predictable Key Generation**  
   The server constructs its RSA private key from a seed:  
   `admin_lovenote_2026_valentine`  
   The script reproduces exactly the same primes `p` and `q` by:
   - Hashing the seed with SHA‑256
   - Converting the hash to an integer
   - Taking the next prime number (`sympy.nextprime`)

   For `q` the seed is extended with `b"pki"` to obtain a different prime.

2. **Message to Sign**  
   The exact message expected by the server is hardcoded (as found in the page source). Any deviation (spaces, punctuation) will break the signature.

3. **Signature Creation**  
   - The message is hashed with SHA‑256.
   - The maximum allowed salt length for PSS is calculated from the key size.
   - Using the reconstructed private key, the script signs the hash with PSS padding and the computed salt length.

4. **Verification**  
   The resulting hexadecimal signature is submitted on the `/verify` page with username `admin`. The server accepts it, granting access and displaying the flag.

---

## 📦 Requirements

- Python 3.6+
- [pycryptodome](https://pycryptodome.readthedocs.io/) – `pip install pycryptodome`
- [sympy](https://www.sympy.org/) – `pip install sympy`

---

## 🚀 Usage

1. **Install dependencies**  
   ```bash
   pip install pycryptodome sympy
   ```
  2. Run the script

 ```bash
python3 solve.py
 ```
Copy the generated signature (the long hex string printed between the = lines).

3.Access the target web application
Open http://<target-IP>:5000 in your browser (the IP is provided in the TryHackMe room panel).

4.Navigate to the verification page

Usually found at /verify (try http://<target-IP>:5000/verify directly).

.If not, look for a “Verify” link on the main page.
 ```
Submit the signature

Username: admin
 ```
Signature: paste the hex string from step 3.

5.Click the verification button.

6.Retrieve the flag
After successful verification, the flag is displayed on the page.
