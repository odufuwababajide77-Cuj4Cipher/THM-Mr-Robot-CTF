# TryHackMe: Mr. Robot CTF - Walkthrough & Methodology

**Platform:** TryHackMe
**Difficulty:** Medium
**Focus Areas:** Web Enumeration, WordPress Exploitation, Password Cracking, Linux Privilege Escalation (SUID)

## 📌 Objective
The goal of this boot2root machine is to compromise a target web server, establish an initial foothold, and escalate privileges to `root` to retrieve three hidden flags. This lab demonstrates practical skills in open-source intelligence (OSINT), web vulnerability identification, and secure configuration auditing.

---

## 🛠️ Methodology & Attack Narrative

### Phase 1: Reconnaissance & Enumeration
I began by mapping out the target's attack surface using `nmap` to identify open ports and running services. 

*   **Nmap Scan:** Discovered ports 80 (HTTP) and 443 (HTTPS) were open, indicating a web server.
*   **Web Enumeration:** Browsing the site revealed a Mr. Robot-themed interactive terminal. To find hidden directories, I utilized directory brute-forcing tools and checked the standard `robots.txt` file.
*   **Findings:** `robots.txt` exposed two critical files:
    1.  `key-1-of-3.txt` (Providing the first flag: `073403c8a58a1f80d943455fb30724b9`)
    2.  `fsocity.dic` (A custom wordlist/dictionary file)

### Phase 2: Initial Access (Web Exploitation)
Further enumeration revealed a WordPress login portal (`/wp-login.php`). 
*   **Wordlist Optimization:** The `fsocity.dic` file was quite large and contained duplicate entries. I sorted the file and removed duplicates to optimize the brute-force attack.
*   **Brute-Forcing:** Using the optimized wordlist, I successfully identified a valid username (`elliot`) and brute-forced the password.
*   **Reverse Shell:** Once authenticated as an administrator in WordPress, I modified the 404.php template file in the Appearance editor, injecting a PHP reverse shell. I set up a Netcat listener on my attacking machine and triggered the payload to catch a shell as the `daemon` user.

### Phase 3: Lateral Movement
Checking the `/home` directory, I found the `robot` user's folder containing the second key and an MD5 password hash (`password.raw-md5`). 
*   Because the key was only readable by `robot`, I needed to pivot.
*   **Password Cracking:** I cracked the MD5 hash using the `fsocity.dic` wordlist, revealing the password for the `robot` user.
*   **Pivoting:** I used `su robot` to switch users, gaining access to `key-2-of-3.txt` (`822c73956184f694993bede3eb39f959`).

### Phase 4: Privilege Escalation
To reach the `root` user, I performed local enumeration to find misconfigurations, specifically looking for binaries with the SUID bit set.
*   **Command:** `find / -perm -4000 -type f 2>/dev/null`
*   **Discovery:** I found an outdated version of `nmap` with the SUID bit set. 
*   **Exploitation:** Older versions of Nmap feature an interactive mode (`nmap --interactive`) that allows users to execute shell commands. Because the binary had root SUID privileges, running `!sh` inside the interactive prompt spawned a root shell.
*   **Completion:** Navigated to the `/root` directory and retrieved `key-3-of-3.txt` (`04787ddef27c3dee1ee161b21670b4e4`).

---

## 🛡️ Key Takeaways & Mitigation
*   **Information Disclosure:** Never leave sensitive files like wordlists or raw keys exposed in `robots.txt`. 
*   **Default/Weak Credentials:** Implement strong password policies and rate limiting to prevent successful brute-force attacks on login portals like WordPress.
*   **Principle of Least Privilege:** SUID bits should be heavily restricted. Standard utilities like Nmap should never run with root SUID privileges on a production server.
