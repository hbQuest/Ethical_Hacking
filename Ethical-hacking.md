NETWORK HACKING - PRE-CONNECTION ATTACKS
-----
### <span style="color: #569cd6">My laptop *already* has Wi-Fi, so why buy another one?</span>


There are two main reasons: one is about **software (VMware)** and one is about **hardware (The Rules of Networking)**.


### 1. The "Virtual Wall" (Why your internal card fails)
The biggest reason is effectively a "glitch" in how Virtual Machines work.

* **The Situation:** You are running Kali Linux inside a Virtual Machine (VMware).
* **The Problem:** VMware creates a "fake" computer for Kali. It takes your laptop's Wi-Fi connection and feeds it to Kali through a **fake wired cable**.
* **The Result:** If you look inside Kali, it thinks it is plugged into the wall with an Ethernet cable. It doesn't even know your laptop *has* a Wi-Fi card.
* **The Fix:** You cannot use a "wired" connection to hack Wi-Fi. By plugging in an external USB adapter, you can use "USB Passthrough" to give Kali direct control of that specific USB device, bypassing the "fake wire" problem entirely.

### 2. The Mechanism: "Polite" vs. "Rude" Cards
Even if you weren't using a Virtual Machine (e.g., if you installed Kali directly on your laptop), your internal card would likely still fail. This is because standard Wi-Fi cards are designed to be **polite**.

#### Normal Mode (Managed Mode) = The "Polite" Guest
* **Mechanism:** When your laptop connects to Wi-Fi, it follows strict rules. It only listens to data specifically addressed to **your** IP address.
* **Analogy:** Imagine being at a crowded party. You are polite, so you only listen when someone says your name. If two strangers are whispering a secret code (password) near you, you effectively plug your ears and ignore them because it's "not your business."
* **Result:** You cannot hack because you are ignoring the data you need to steal.

#### Monitor Mode = The "Nosy" Spy
* **Mechanism:** This mode tells the Wi-Fi card to ignore the rules. It removes the "filter" that usually blocks other people's data. It captures *every* radio wave flying through the air, regardless of who it is for.
* **Analogy:** You stop being polite. You walk around the party recording **everyone's** conversations, even the whispers between strangers.
* **Result:** You can now capture the "Handshake" (the secret code) used to crack the password.

#### Packet Injection = The "Bully"
* **Mechanism:** Hacking isn't just listening; sometimes you have to force something to happen. Standard cards can only "speak" when the router allows them to. Hacking cards can "shout" whenever they want.
* **Analogy:** You want to hear the secret password, but the strangers aren't talking. So, you walk over and scream **"FIRE!"** (this is the De-authentication attack).
* **The Hack:** The strangers get scared, leave the room (disconnect), and immediately come back in. When they come back, they have to re-introduce themselves (send the password). Because you are in **Monitor Mode**, you record this re-introduction.

### Summary
You need the adapter because:
1.  **VMware** blinds your internal card, turning it into a "wired" connection.
2.  **Hacking** requires a card that can be "rude" (Monitor/Injection), but standard internal cards are hard-wired to be "polite" (Managed Mode).

------


### 1. Why can't I use my internal Wi-Fi card?
You cannot use your internal card because of the **"Virtualization Barrier."**

When you run Kali Linux inside VMware (or VirtualBox), the virtualization software isolates the "Guest" (Kali) from your "Host" (your physical computer hardware).
* **The Lie:** VMware takes your computer's Wi-Fi connection and bridges it to Kali. However, it presents this connection to Kali as a **Wired Ethernet connection** (usually named `eth0`).
* **The Consequence:** Because Kali "thinks" it is plugged in via a wire, it does not load wireless drivers. You cannot ask a wired cable to "scan for Wi-Fi channels" or "capture wireless packets."
* **The Fix (USB Passthrough):** VMware *cannot* give a Virtual Machine direct control over an internal PCI card (your built-in Wi-Fi). However, it **can** give direct control over a USB port. By using a USB adapter, you can "disconnect" it from your main computer and "connect" it directly to Kali. This gives Kali raw, low-level control over the hardware, which is required for hacking.

### 2. The Mechanism: Why do we need "Powerful" Cards?
Ethical hacking requires your Wi-Fi card to break the standard rules of polite networking. Most standard cards (like the one in your laptop) are "hardwired" by their drivers to be polite. You need a card that supports "rule-breaking" modes.

Here is the breakdown of the three critical modes:

#### A. Monitor Mode ( The "Spy" )
* **Normal Behavior (Managed Mode):** Your Wi-Fi card usually ignores all traffic that isn't addressed specifically to it. It’s like being in a room and only listening when someone says your name.
* **Hacking Behavior (Monitor Mode):** This forces the card to listen to **everything** flying through the air, regardless of who sent it or who it is for.
* **Why you need it:** You need this to capture "handshakes." When a neighbor connects to their Wi-Fi, their phone sends a coded password to the router. Monitor mode allows you to silently copy that coded message from the air so you can try to crack it later.

#### B. Packet Injection ( The "Disruptor" )
* **Normal Behavior:** A standard card only speaks when spoken to or when it wants to download something.
* **Hacking Behavior:** This allows your card to create custom, fake packets and "inject" them into the air.
* **Why you need it:** The most common use is the **Deauthentication Attack**.
    * *Scenario:* You are listening (Monitor Mode), but no one is connecting, so you can't capture a password handshake.
    * *Attack:* You use packet injection to shout a fake "Disconnect!" order to a victim's device. The device drops off the Wi-Fi and immediately reconnects.
    * *Result:* This forced reconnection generates the handshake you need to capture. Without injection, you might wait hours for this to happen naturally.

#### C. AP Mode ( The "Imposter" )
* **Normal Behavior:** Your card connects *to* a network (Client).
* **Hacking Behavior:** Your card *becomes* the network (Master).
* **Why you need it:** This is used for **"Evil Twin" attacks**. You create a fake Wi-Fi spot with the exact same name as a legitimate one (e.g., "Starbucks_WiFi"). If your signal is stronger, victims will connect to your adapter instead of the real router, allowing you to intercept their traffic.

### Which adapter should you get?
Your instructor is correct about the chipsets. The **chipset** is the brain inside the plastic shell; it matters more than the brand name.

| Chipset | Pros | Cons |
| :--- | :--- | :--- |
| **Atheros AR9271** | The "Gold Standard" for stability. Works natively with Kali without installing drivers. | **2.4 GHz only.** Cannot hack modern 5 GHz networks. |
| **Realtek RTL8812AU** | Supports **2.4 GHz and 5 GHz**. Highly powerful and supports modern standards (AC). | usually requires manually installing drivers in Kali (easy to do). |

**Popular Choices:**
* **Alfa AWUS036NHA** (Uses Atheros AR9271) - Best for beginners, but 2.4GHz only.
* **Alfa AWUS036ACH** (Uses Realtek RTL8812AU) - Powerful, dual-band, looks like a beast.

-----
### <span style="color: #569cd6">The "setup phase" for the hacking hardware - How to physically transfer control of a USB device from your main computer (Windows) to your virtual computer (Kali).</span>


### 1. The "Car Engine" Analogy (Chipsets vs. Brands)
**"The brand does not matter."**

Think of the wireless adapter like a car:
* **The Brand (e.g., TP-Link, Alfa):** This is just the plastic shell and the logo. It doesn't matter if the car is red or blue.
* **The Chipset (Realtek or Atheros):** This is the **engine**. In racing, the engine is the only thing that matters.

**Why this matters:**
Kali Linux only knows how to "speak" to specific engines (chipsets). If you buy a fancy-looking adapter but it has an engine Kali doesn't understand, it won't work. You need an adapter with a **Realtek** or **Atheros** engine so Kali can tell it to "break the rules" (enter Monitor Mode).

### 2. The "Handover" (VMware USB Settings)
When you plug a USB into your computer, your Windows OS immediately grabs it.
* **The Problem:** If Windows is holding the USB, Kali cannot use it. A USB device cannot serve two masters at the same time.
* **The Solution:** You need to force Windows to "let go" so Kali can "grab it."

**The Steps Explained:**
1.  **VMware Settings:** You add a "USB Controller" in the settings. This is like building a physical port on your virtual machine. Setting it to **USB 3.1** ensures it runs at high speed (important for modern adapters).
2.  **The Popup:** When you plug the adapter in, VMware asks: *"Who should control this? The Host (Windows) or the VM (Kali)?"*
3.  **The Choice:** By selecting **"Connect to a virtual machine,"** you are physically cutting the connection to Windows and giving Kali total control.

### 3. Decoding the `ifconfig` Command
Run `ifconfig`. This is the most important command in Linux networking. It asks the system: *"What network gear do I have installed?"*

Here is what the three results mean in plain English:

| Interface Name | What it is | What it does |
| :--- | :--- | :--- |
| **`eth0`** | **The Fake Wire** | VMware tricks Kali into thinking it is plugged into the internet via a cable. **This is where your Internet comes from.** |
| **`lo`** | **The Mirror** | This stands for "Loopback." It is used for the computer to talk to itself. You can ignore this for now. |
| **`wlan0`** | **The Weapon** | This is your USB Wireless Adapter. If you see this, it means the "Handover" worked! **This is used for Hacking, NOT for Internet.** |

### 4. The "Don't Connect" Rule
**"Do not connect to Wi-Fi with this adapter."**

**Why?**
* **Your Internet Source:** Kali already has internet through `eth0` (which borrows internet from your Windows Wi-Fi).
* **Your Hacking Tool:** The `wlan0` adapter is your weapon.
    * If you use `wlan0` to connect to Wi-Fi, it becomes busy sending emails and loading Google.
    * You want `wlan0` to be free so you can switch it to **Monitor Mode** (Spy Mode). It cannot be in Spy Mode if it is busy being connected to a router.

### Summary Checklist
Based on this lecture, here is your success checklist:
1.  [ ] You have an adapter with a supported **Chipset**.
2.  [ ] You added the **USB 3.1 Controller** in VMware settings.
3.  [ ] When you plugged it in, you selected **"Connect to Virtual Machine."**
4.  [ ] You typed `ifconfig` and saw **`wlan0`** appear.
5.  [ ] You have **NOT** connected `wlan0` to your home Wi-Fi (it should be disconnected/idle).

------

### <span style="color: #569cd6">Spoofing (faking) your hardware identity.</span>


### 1. What is a MAC Address?
* **The Definition:** MAC stands for **Media Access Control**.
* **The Analogy:** Think of the MAC address like your **fingerprint** or a **tattoo** on your network card.
    * **IP Address:** Like your home address. It changes depending on where you live (or which network you connect to).
    * **MAC Address:** Like your DNA. It is burned into the hardware at the factory. No two devices in the world have the same one.
* **The Format:** It looks like six pairs of letters and numbers separated by colons (e.g., `00:11:22:33:44:55`).

### 2. Why do hackers change it?
If the MAC address is permanent, why does the instructor teach you to change it?

* **Anonymity:** Since the MAC address is unique to your device, it can be used to track you. If you hack a network using your real MAC address, you leave your digital fingerprints at the crime scene. Changing it creates a "fake ID."
* **Bypassing Filters (The "VIP List"):** Many secure networks use **MAC Filtering**. They have a list of "allowed" devices (like the CEO's laptop). If you change your MAC address to match the CEO's, the network thinks you are the CEO and lets you in. This is called **Impersonation**.

### 3. The "Surgery" Procedure (The Commands)
Think of this process like performing surgery on a patient. You cannot perform heart surgery while the patient is running a marathon; you have to put them to sleep first.

#### Step 1: Check the patient (`ifconfig`)
First, you need to see what your current name is and which interface you are changing.
* **Command:** `ifconfig`
* **Look for:** The word `ether` (this is your current MAC) and the interface name (usually `wlan0` for your Wi-Fi adapter or `eth0` for wired).

#### Step 2: Anesthesia (`down`)
You must turn off the network card before you can modify it. It cannot be transmitting data while you change its identity.
* **Command:** `ifconfig wlan0 down`
* *Note: Replace `wlan0` with your specific interface name.*

#### Step 3: The Surgery (`hw ether`)
This is where the magic happens. You tell the system: "Change the Hardware (hw) Ethernet (ether) address to this new number."
* **Command:** `ifconfig wlan0 hw ether 00:11:22:33:44:55`
* *Note: The `00` at the start is a safe bet to ensure the network accepts it as a valid address.*

#### Step 4: Wake up (`up`)
Now that the ID is changed, you turn the card back on so it can start working again.
* **Command:** `ifconfig wlan0 up`

#### Step 5: Verify
Check to make sure the surgery was successful.
* **Command:** `ifconfig`
* **Result:** The `ether` line should now show your new `00:11:22...` address.

### Important Notes

**1. It is Temporary**
This change happens in the **RAM (Memory)**, not on the physical chip.
* **The Good News:** You didn't permanently break your hardware.
* **The Bad News:** As soon as you restart your computer (or the Virtual Machine), the MAC address will go back to the original factory one. You have to do this every time you restart if you want to remain anonymous.

**2. The "Network Manager" Glitch**
The instructor mentions a common bug where the MAC address changes back to the original *immediately*, even without restarting.
* **Why?** Kali Linux has a background program called "Network Manager" that tries to be helpful by "fixing" your settings.
* **The Fix:** If this happens to you, don't worry. There is a specific video in the course resources to fix this. It usually involves editing a small text file to tell the Network Manager to leave your MAC address alone.

---

### <span style = "color: #569cd6">Wireless adapter stops acting like a normal Wi-Fi card and starts acting like a hacker's tool.</span>

### 1. The Concept: Managed vs. Monitor Mode

To understand this, imagine you are sitting in a coffee shop.

**Managed Mode (The Default / "Polite" Mode)**
* **How it works:** Your ear (the Wi-Fi card) listens to the room. If someone shouts "Hey [Your Name]!", you listen. If they shout "Hey Dave!", you ignore it because it's not for you.
* **The Technical side:** Your card checks the **Destination MAC address** on every packet. If the address matches your card, it accepts it. If it doesn't, it throws it away.
* **Why we change it:** As a hacker, you don't care if the packet is for you. You want *everyone's* packets. You want to hear what "Dave" is downloading.

**Monitor Mode (The Hacker / "Nosy" Mode)**
* **How it works:** You stop filtering. You record **everything** anyone says in the coffee shop, regardless of who they are talking to.
* **The Technical side:** The card captures every packet flying through the air within its range, even if the Destination MAC is for someone else's laptop or phone.

---

### 2. The Commands (The "Recipe")

The instructor gives you a specific recipe to switch modes. Here is what each ingredient does:

#### Step 1: `iwconfig`
* **What it does:** This is the cousin of `ifconfig`.
    * `ifconfig`: Shows **ALL** interfaces (wired, wireless, loopback).
    * `iwconfig`: Shows **ONLY** wireless interfaces.
* **Why use it:** To check your current mode. You will see `Mode: Managed`.

#### Step 2: `ifconfig wlan0 down`
* **What it does:** Turns off the card.
* **Why:** You cannot change the fundamental mode of the card while it is running. You have to shut it down first.

#### Step 3: `airmon-ng check kill` (Crucial Step!)
* **What it does:** This kills "interfering processes."
* **The details:** Linux has a "Network Manager" program that tries to be helpful. If it sees your card acting weird (Monitor Mode), it tries to "fix" it by forcing it back to Managed Mode.
* **The Consequence:** This command kills the Network Manager. **Your Internet icon will disappear, and you will lose internet access on Kali.**
* **Don't Panic:** This is fine. You don't need the internet to hack a local Wi-Fi; you just need to talk to the airwaves.

#### Step 4: `iwconfig wlan0 mode monitor`
* **What it does:** This is the actual switch. It forces the driver to stop filtering packets.

#### Step 5: `ifconfig wlan0 up`
* **What it does:** Turns the card back on.

Run `iwconfig` again.
* **Before:** You saw `Mode: Managed`.
* **After:** You should see `Mode: Monitor`.

### 3. Summary of the Workflow

Think of this process like modifying a car for a race:

1.  **Check the car (`iwconfig`):** "Okay, it's currently set to 'Street Legal' mode (Managed)."
2.  **Turn off the engine (`ifconfig down`):** You can't work on a running engine.
3.  **Fire the safety inspector (`airmon-ng check kill`):** There is a safety inspector (Network Manager) who will try to stop you from modifying the car. You have to kick him out of the garage.
4.  **Modify the engine (`mode monitor`):** You switch it to 'Race Mode'.
5.  **Turn the engine back on (`ifconfig up`):** Ready to go.

**A Note on Hardware:**
The instructor repeats that **"Not all adapters support this."** If you bought one of the adapters we discussed earlier (Atheros or Realtek), this will work. If you are using a random cheap USB stick, Step 4 might give you an error saying "Operation not supported."

---
### <span style = "color: #569cd6">Airodump-ng</span>
If your wireless adapter is the "hardware weapon," this program is the "software radar."

### 1\. The Concept: The "Digital Radar"

Imagine you are a spy sitting in a van. You turn on a special radio scanner that picks up every signal in the neighborhood.

  * **Monitor Mode:** This turns your ear "on" to hear everything.
  * **Airodump-ng:** This is the notepad where you write down everything you hear.

**What it does:** It captures raw data flying through the air and organizes it into a neat table so you can see:

  * Who is nearby? (Routers)
  * How strong is their signal? (Distance)
  * What kind of security do they use? (The type of lock on the door)
  * Who is connected to them? (Victims)

-----

### 2\. The Command

The command is very simple. You are telling the program *what* to run and *which* ear to listen with.

  * **Command:** `airodump-ng [Interface Name]`
  * **Example:** `airodump-ng wlan0` (or `mon0`, depending on your system).

**Important Note on Interface Names:**
It can show `mon0`. Your system might show `wlan0` or `wlan0mon`. **The name does not matter.** As long as you successfully ran the "Monitor Mode" command from the previous lecture, just use whatever name `iwconfig` showed you.

**How to Stop:**
This program will run forever, constantly refreshing the list. To stop it, press **`Ctrl + C`** on your keyboard.

-----

### 3\. Decoding the Output (The Matrix)

When you run this, your screen will fill with text. It looks scary, but it is actually just a simple spreadsheet.

Here is the translation of the columns you need to know:

| Column Name | What it stands for | Plain English Explanation |
| :--- | :--- | :--- |
| **BSSID** | Basic Service Set ID | **The Router's MAC Address.** This is the unique "fingerprint" of the router you want to hack. You will need to copy-paste this later. |
| **PWR** | Power | **Signal Strength.** <br>This is tricky: **The closer to 0, the better.** <br>• `-20` is a very strong signal (you are right next to it). <br>• `-80` is a weak signal (you are far away). <br>• `-100` means the signal is lost. |
| **Beacons** | Beacons | **"I'm Here\!" Signals.** Routers constantly shout "I am here\!" to the world. If this number is moving fast, the connection is good. |
| **\#Data** | Data Packets | **The Gold Mine.** These are packets containing actual user activity (emails, downloads). <br>**For hacking, you need this number to go UP.** If it stays at 0, no one is using the Wi-Fi, making it harder to hack. |
| **CH** | Channel | **The Frequency Lane.** Wi-Fi works on channels (like TV channels). You need to know if the router is on Channel 1, 6, or 11 so you can tune your attack to that specific channel. |
| **ENC** | Encryption | **The Lock Type.** <br>• **OPEN:** No password. <br>• **WEP:** Old, very easy to hack. <br>• **WPA2:** Modern, harder to hack (requires capturing a handshake). |
| **ESSID** | Extended Service Set ID | **The Wi-Fi Name.** This is what you see on your phone (e.g., "Starbucks\_WiFi" or "Home\_Network"). |

### 4\. Why is this important?

You cannot hack a target you cannot see. This step is called **Reconnaissance (Recon).**

Before you launch an attack, you run `airodump-ng` to answer three questions:

1.  **Where is the target?** (Find the ESSID/Name).
2.  **Is the signal strong enough?** (Check PWR. If it's -90, move your laptop closer).
3.  **Are there people using it?** (Check \#Data. If there is data flowing, you can capture a handshake).

### Quick Summary

1.  Enable Monitor Mode (previous lecture).
2.  Run `airodump-ng wlan0`.
3.  Look at the list.
4.  Find the network you want to test.
5.  Check if the **PWR** is good (closer to -20) and if **ENC** is WPA2.

---

### <span style = "color: #569cd6">Frequency Bands</span>
**Why can't I see my own home Wi-Fi in the list?**

### 1. The Concept: The Two Highways
Imagine the air around you is a highway for data.
* **2.4 GHz:** This is the "Old Highway." It has fewer lanes (channels), but the signal travels further through walls. Almost every device uses this.
* **5 GHz:** This is the "New Highway." It is much wider, faster, and less crowded, but the signal doesn't go through walls as well.

**The Problem:**
By default, `airodump-ng` (your scanner) only looks at the **2.4 GHz** highway.
If your home router is broadcasting on the **5 GHz** highway, `airodump-ng` is effectively blind to it. It’s like trying to listen to an FM radio station while your radio is set to AM mode.



### 2. The Hardware Requirement
Before you type any commands, there is a **physical rule** you must obey: **You cannot use software to upgrade your hardware.**

* **The "NHA" Adapter (Atheros AR9271):** The instructor mentioned this is his favorite card. It is famous for being stable, but it is **2.4 GHz ONLY**. If you have this card, **you cannot hack 5 GHz networks.** No command will fix this.
* **The "ACH" Adapter (Realtek RTL8812AU):** This is a modern card. It has two antennas inside: one for 2.4 GHz and one for 5 GHz.

**Check your adapter:**
If you bought a cheap adapter or an older one, it might not support 5 GHz. If that is the case, the commands below will simply not show any new results.

### 3. The "Missing Victim" Mystery
This is a very important hacking concept mentioned in the lecture.

**Scenario:**
You run `airodump-ng` and you see the target Router (e.g., "Home_Wifi").
However, you see **zero** clients connected to it. You know your brother is in the next room watching Netflix on that Wi-Fi. Why can't you see him?

**The Reason:**
Modern routers are **"Dual Band."** They broadcast the same Wi-Fi name on both highways at the same time.
* Your scanning tool is watching the 2.4 GHz lane.
* Your brother's iPad automatically connected to the faster 5 GHz lane.
* **Result:** You are watching the wrong lane, so you cannot attack him. You need to switch bands to see him.

### 4. The Commands
To fix this, we use the `--band` argument. This tells `airodump-ng` which highway to look at.

#### A. Scanning 5 GHz Only (`band a`)
This forces the adapter to ignore the old highway and focus purely on the new one.
* **Command:** `airodump-ng --band a mon0`
* **What happens:** You will see a list of networks that ends in "5G" or high-speed networks that were previously invisible.

#### B. Scanning EVERYTHING (`band abg`)
This tells the adapter to scan all frequencies at the same time.
* **Command:** `airodump-ng --band abg mon0`
* **The Code:**
    * **a** = 5 GHz
    * **b** = 2.4 GHz (Old standard)
    * **g** = 2.4 GHz (Newer standard)
* **The Trade-off:** Note that this is **slower**.
    * *Why?* Imagine a radio that has to scan 100 channels instead of 11. It takes longer to complete a full circle. You might miss a packet while the scanner is busy checking the other bands.

### Summary Checklist
1.  **Hardware Check:** Does your physical USB adapter support 5 GHz? (If you don't know, try the command below. If you see nothing, it probably doesn't).
2.  **The Command:** Use `airodump-ng --band a mon0` to find modern networks.
3.  **The Strategy:** If you can't see a victim (client) on a network, they are likely hiding in the 5 GHz band. Switch bands to find them.

Now, try running `airodump-ng --band a wlan0` (or whatever your interface name is).
* **If your list stays empty:** Your adapter likely does not support 5 GHz.
* **If you see new networks:** Congratulations, you have a dual-band adapter!

--- 

### <span style = "color: #569cd6">Transitions from "Scanning the Neighborhood" (finding all networks) to "Staking Out the House" (watching one specific target intensely).</span>


### 1\. The Goal: Targeted Sniffing

In the previous lecture, you saw *every* network. That's messy.
Now, you want to focus your "radar" on **one single target** (e.g., your home router). This allows you to:

  * See exactly who is connected to it (Station/Clients).
  * Capture every single packet from that specific network without distraction.
  * Save the data to a file for analysis.

### 2\. The Command Structure

The command looks long, but it is just a sentence made of specific parts.

**The Command:**
`airodump-ng --bssid [Router MAC] --channel [Number] --write [Filename] [Interface]`

**The Breakdown:**

| Part | What it does | Why we need it |
| :--- | :--- | :--- |
| `airodump-ng` | The Program | Calls the "radar" software. |
| `--bssid 11:22:33...` | The Target ID | Tells the radar: *"Ignore everyone else. Only look at this specific Router MAC address."* |
| `--channel 2` | The Frequency | Tells the radio: *"Tune to Channel 2 only."* <br>**Crucial:** If you don't set this, the scanner will keep jumping channels (1, 2, 3...) and you will miss data while it's on the wrong channel. |
| `--write test` | The Recorder | Tells the program: *"Save everything you hear into a file named 'test'."* |
| `mon0` | The Ear | Specifies which wireless adapter to use. |

### 3\. Reading the New Output

When you run this targeted command, a **new section** appears at the bottom of the screen.

  * **Top Section:** The Router (The Access Point).
  * **Bottom Section:** The **Stations** (The Clients/Victims).

**What you see in the Station Section:**

  * **Station MAC:** The unique ID of the device connected (e.g., an iPhone, a Laptop).
  * **Probes:** If a device is not connected, but is "looking" for a known Wi-Fi (like "Starbucks"), it shows up here. This reveals where that person has been before\!

### 4\. The "Gibberish" Problem (Encryption)

`ls` and you can see the file being saved with `.cap` extension

Open the saved file (`test-01.cap`) in a program called **Wireshark**.

  * **What is Wireshark?** A tool that lets you read the contents of the captured packets.
  * **The Result:** The data looks like random nonsense characters.
  * **The Reason:** **Encryption (WPA2).**
      * Even though you captured the data flying through the air, it is locked inside a digital safe.
      * You have the safe (the packet), but you don't have the key (the Wi-Fi password).

**Key Takeaway:**
Right now, you cannot read the emails or passwords inside the packets because you haven't cracked the Wi-Fi password yet. This capture file is useless for spying *until* you learn how to break the encryption (which is the next section).

### 5\. Why is this useful then?

If we can't read the data, why do we do this?

1.  **Identify Targets:** You can see exactly which devices are connected (Apple, Huawei) to plan your attack.
2.  **Capture the Handshake:** This "Targeted Sniffing" mode is **required** to capture the WPA Handshake. You cannot capture a handshake if your scanner is jumping between channels. You must be locked onto the target's channel to catch that split-second login code.

### Summary Checklist

1.  **Find Target:** Run general scan, copy the **BSSID** and **Channel**.
2.  **Lock Target:** Run the specific command: `airodump-ng --bssid [MAC] --channel [Ch] --write [Name] [Interface]`.
3.  **Verify:** Do you see the "Stations" (clients) appearing at the bottom?
4.  **Save:** Remember that your data is being saved to a `.cap` file (e.g., `test-01.cap`) in your Home folder.

---

### <span style = "color: #569cd6">Deauthentication Attack</span>
or "Deauth" for short.
This is one of the most exciting (and scary) lectures because it is the first time you will actively **disrupt** a network rather than just listening to it.

### 1\. The Concept: The "Master Imposter"

How can you disconnect someone if you don't even know the Wi-Fi password?
You do it by **Identity Theft (Spoofing).**

Imagine a conversation between a Router (Access Point) and a Laptop (Client):

  * **Router:** "Here is your data, Laptop."
  * **Laptop:** "Thanks, Router."

**The Attack Mechanism:**
Your hacking machine (Kali) steps into the middle and shouts two lies at the same time:

1.  **To the Laptop:** "I am the Router\! Get off my network immediately\!"
2.  **To the Router:** "I am the Laptop\! I am disconnecting now, bye\!"

Because Wi-Fi management packets are usually **unencrypted** (even on secure networks), both devices believe you. The connection is instantly cut.

[Image of Wi-Fi deauthentication attack diagram]

### 2\. The Command Structure (`aireplay-ng`)

We use a tool called `aireplay-ng`. Think of `airodump` as the *Scanner* and `aireplay` as the *Shooter*.

**The Command:**
`aireplay-ng --deauth [Number] -a [Router MAC] -c [Client MAC] [Interface]`

**The Breakdown:**

| Part | What it means | Plain English Explanation |
| :--- | :--- | :--- |
| `aireplay-ng` | The Tool | "Open the packet injection tool." |
| `--deauth 100000` | The Attack Type | "Send **100,000** disconnect packets." <br>*(A high number keeps them offline for a long time. A 0 means 'infinite'.)* |
| `-a [BSSID]` | The Router | "Pretend to be this Router." (The Access Point MAC). |
| `-c [Station]` | The Victim | "Target ONLY this specific device." (The Client MAC). <br>*Note: If you remove this part, you will disconnect EVERYONE on the network.* |
| `mon0` | The Weapon | "Use this wireless adapter." |

### 3\. The "Two-Terminal" Secret

The instructor emphasizes a very specific workflow that beginners often miss: **You need two black windows (Terminals) open at the same time.**

  * **Terminal 1 (The Target Lock):**
      * Run `airodump-ng` against the specific channel and router.
      * **Why?** This keeps your wireless card "tuned" to the correct frequency (e.g., Channel 2). If you close this, your card might drift to Channel 6, and your attack in Terminal 2 will miss.
  * **Terminal 2 (The Attack):**
      * Run the `aireplay-ng` command to fire the packets.

### 4\. What happens to the Victim?

  * **On their screen:** The Wi-Fi icon might just spin, or they might see "No Internet."
  * **In reality:** Their device is frantically trying to reconnect, but your machine keeps shouting "Disconnect\!"
  * **How to stop:** Press **`Ctrl + C`** in your Kali terminal. Once you stop shouting, the victim's device will automatically reconnect (usually within a few seconds).

### 5\. Why do we do this?

We don't do this just to be annoying. This is a strategic move to set up other attacks:

1.  **Capture the Handshake (Crucial):** When you stop the attack, the victim's device reconnects. During this reconnection, it sends the **password hash** (the handshake). This is exactly what we need to steal to crack the password later.
2.  **Social Engineering:** You cut their internet. They get frustrated. You call them pretending to be "IT Support" and say, "install this update to fix your Wi-Fi." (The update is a virus).
3.  **Evil Twin:** You kick them off the real network so they are forced to connect to your fake network (which has the same name).

### Beginner Tips for Success:

  * **Targeting:** Ensure the Client MAC (`-c`) is actually currently connected. If the "Station" list in airodump is empty, the attack won't work because there is no one to kick off.
  * **5GHz Warning:** As the instructor noted, if the victim is on 5GHz, you need a 5GHz adapter and you usually need to add the `-D` flag (though for beginners, stick to 2.4GHz targets first).
  * **Legal Warning:** Do not do this to your neighbors. Disconnecting people is active interference and is illegal in many countries. Only do this on your own equipment.

---

NETWORK HACKING - GAINING ACCESS - WEP CRACKING
===

### <span style = "color: #569cd6">Moving away from **Network Hacking** (attacking the Wi-Fi signals in the air) and entering the world of **System Hacking** (taking control of the actual devices).</span>


Setting the stage by explaining the "Philosophy of Hacking" and the two main roads you can take to break into a computer.

### 1. The Mindset: "Everything is a Computer"
Stop looking at devices as "phones," "routers," "web servers," or "laptops."
* **The Reality:** They are **ALL** just computers.
* **The Commonality:** They all have:
    1.  Hardware (Processor/RAM).
    2.  An Operating System (Windows, Linux, Android, iOS).
    3.  Software/Apps installed on top.
* **Why this matters:** If you learn how to hack a computer (Windows/Linux), you automatically learn the concepts to hack a phone or a smart TV. The *technique* might change slightly, but the *concept* is exactly the same.

### 2. The Two Paths of Attack
There are two distinct ways to break into a system. Think of the target computer as a **House**.

#### Path A: Server-Side Attacks (The "Burglar" Method)
* **Definition:** Attacking the computer directly without any human help.
* **Target:** Usually servers, web servers, or neglected computers.
* **Mechanism:** You look for a "hole in the wall."
    * Every computer has software running on it (like a web server app or a file sharing app).
    * If that software has a "bug" or a vulnerability (an unlocked window), you use it to crawl inside.
* **User Interaction:** **None.** The victim could be asleep or on vacation. You only need the **IP Address** to launch this attack.
* **Difficulty:** Harder today because software companies patch (fix) these bugs very quickly.

#### Path B: Client-Side Attacks (The "Con Artist" Method)
* **Definition:** Tricking the human user into opening the door for you.
* **Target:** People (Office workers, home users).
* **Mechanism:** You cannot find a hole in the wall (the software is secure), so you knock on the front door.
    * You send a fake update, a picture, or a PDF.
    * This file contains a **Trojan** (a virus hidden inside a harmless file).
* **User Interaction:** **Required.** The attack fails if the user does nothing. The user *must* click, download, or open something.
* **Social Engineering:** This is the art of lying. You have to convince them to click. (e.g., "Here is the invoice you asked for," or "Your computer needs this urgent update.")
* **Difficulty:** High success rate because it relies on human error, which cannot be "patched."

### 3. Post-Exploitation (What happens next?)
This simply means: **"I am inside. Now what?"**

Regardless of whether you entered through the window (Server-Side) or the front door (Client-Side), the result is the same: You now control the computer.

**Post-Exploitation tasks include:**
* **Stealing Data:** Copying passwords, files, or emails.
* **Spying:** Turning on the webcam or microphone.
* **Pivoting:** Using this computer as a launchpad to attack *other* computers in the same building (like jumping from the reception desk computer to the CEO's computer).
* **Privilege Escalation:** You might enter as a "Guest" user; you want to hack the system internally to become the "Administrator."

### Summary Table

| Feature | Server-Side Attacks | Client-Side Attacks |
| :--- | :--- | :--- |
| **Analogy** | Finding an unlocked window. | Tricking the owner to open the door. |
| **Human Interaction** | **Zero.** (The user does nothing). | **Required.** (The user must click). |
| **Main Target** | The Software / OS bugs. | The Human / Social Engineering. |
| **What you need** | Just the IP Address. | Knowledge about the person (email, habits). |

### What to expect next in the course
Based on this intro, the next few sections of your course will likely be organized like this:
1.  **Scanning:** Finding the IP address and open ports (finding the house).
2.  **Server-Side:** Using tools (like Metasploit) to attack bugs in the software.
3.  **Client-Side:** Learning to generate malicious files (Trojans) and how to deliver them to victims.
4.  **Post-Exploitation:** Managing the computers you have hacked.

---

### <span style = "color: #569cd6">WEP Cracking</span>

Wired Equivalent Privacy - WEP encryption and why it is so easy to break!

### 1. The Basic Concept: "The Secret Language"
First, you need to understand how normal Wi-Fi encryption works (the "Polite" way).

* **The Goal:** You want to send a letter ("Hello Router") to the Router.
* **The Process:** You take your Wi-Fi Password and use it to scramble the letter into gibberish ("Xy7#b9!").
* **The Transmission:** You throw the gibberish into the air.
* **The Receiver:** The Router catches the gibberish. Since it has the **same Password**, it can unscramble "Xy7#b9!" back into "Hello Router."
* **The Hacker:** If a hacker catches the gibberish, they can't read it because they don't have the password.

### 2. The WEP Mechanism: "The Random Ingredient"
The designers of WEP knew that using the *exact same* password to scramble every single packet was dangerous. If you use the same key too many times, patterns appear.

So, they added a "Random Ingredient" called the **IV (Initialization Vector)**.

* **The Recipe:** To encrypt a packet, WEP uses a formula:
    $$\text{Network Password} + \text{Random IV} = \text{Unique Key Stream}$$
* **The Result:** Every single packet is encrypted with a slightly different "Unique Key."

**How the Router reads it:**
When you send the packet, you must tell the router which "Random Ingredient" (IV) you used, so the router can do the math in reverse.
* **You send:** [ The Encrypted Gibberish ] + [ The IV in **Plain Text** ]



### 3. The Fatal Flaw: "Running out of Numbers"
This system sounds smart, but it has two massive design failures that hackers exploit.

#### Flaw #1: The IV is "Naked" (Plain Text)
When the packet flies through the air, the **Data** is encrypted (hidden), but the **IV** is NOT encrypted (visible).
* **Hacker:** "I cannot read the message, but I can see exactly which random number you used to lock it."

#### Flaw #2: The IV is too small (24-bit)
The IV is a 24-bit number. This means there are only about **16 million** possible combinations.
* **The Problem:** Computers work very fast. On a busy network (like a customized download or streaming video), the router sends thousands of packets per second.
* **The Consequence:** Eventually, the router runs out of unique IVs. **It has to start repeating them.**

### 4. The Attack: "The Statistical Puzzle"
This is how `aircrack-ng` breaks the password. It is a game of "Spot the Difference."

1.  **Capture:** You use your wireless adapter (in Monitor Mode) to collect thousands of packets.
2.  **Filter:** You look at the "Naked" IVs attached to the packets.
3.  **Wait for Repeats:** You wait until you see two different packets that were locked using the **exact same IV**.
4.  **The Math:**
    * We have Packet A (Locked with IV #500 + Password).
    * We have Packet B (Locked with IV #500 + Password).
    * Since the IV is the same, and the Password is the same, the mathematical difference between the two packets reveals clues about the Password.

**Analogy:**
Imagine the Password is a picture hidden behind a wall. Every time the router repeats an IV, a brick falls out of the wall.
* If you capture 5,000 packets, you might see a tiny corner of the picture.
* If you capture **50,000 packets**, enough bricks have fallen out that `aircrack-ng` can clearly see the whole picture (The Password).

---

### <span style = "color: #569cd6">The Practical Application</span>

The "Best Case Scenario" for a hacker: A WEP network that is **busy** (lots of people using it).

### 1. The Pre-requisite: The "Busy" Network
The instructor repeats a very important point: **"This network is being used constantly."**

* **Why does this matter?** Remember the previous lecture? We need **IVs** (the random numbers).
* **How do we get IVs?** The router *only* sends an IV when it sends a packet of data (an email, a video chunk, a website load).
* **The Rule:**
    * **Busy Network** = Lots of Data = Lots of IVs = **Fast Crack.**
    * **Idle Network** = No Data = No IVs = **Impossible to Crack** (unless we force it, which is the *next* lecture).

### 2. Phase 1: The Recording (`airodump-ng`)
First, we need to capture the packets into a file.

**The Command:**
`airodump-ng --bssid [Target MAC] --channel [Channel] --write basic_wep mon0`

**The "Magic Number" to watch:**
Look at the column labeled **`#Data`** in the airodump screen.
* In previous lectures, this number was usually `0` or `5`.
* In this lecture, because the network is busy, you see the number shooting up: `100`... `500`... `2000`... `5000`.
* **This is your progress bar.** Each number in the `#Data` column is a captured IV. You typically need between **20,000 to 50,000** data packets to crack WEP.

### 3. Phase 2: The Math Wizard (`aircrack-ng`)
While `airodump-ng` is still running in Terminal 1 (collecting the data), you open **Terminal 2** to run the cracking tool.

**The Command:**
`aircrack-ng basic_wep-01.cap`

* **What happens:** This tool opens the file you are currently recording. It reads all the IVs you have collected so far and tries to solve the mathematical puzzle.
* **The Result:** If you have enough data packets (IVs), the screen will suddenly say:
    **`KEY FOUND! [ 11:22:33:44:55 ]`**

### 4. Phase 3: The Cleanup (Formatting the Key)
When `aircrack-ng` gives you the key, it usually gives it to you in **Hexadecimal** format (numbers and letters A-F).

* **The Output:** `11:22:33:44:55`
* **The Problem:** Most computers (Windows/Mac/Android) don't want the colons (`:`) when you type a password.
* **The Fix:** You simply copy the key and delete the colons.
    * *Result:* `1122334455`

**Note on ASCII:**
Sometimes, next to the hex code, you will see a normal word in brackets, like `(PASSWORD)`.
* WEP passwords can be entered as either the **Hex Code** OR the **ASCII word**. Both will work. But the Hex code *always* works, so the instructor focuses on that.

### 5. Why switch to Windows/Mac to test it?
At the end, we try connect to the Wi-Fi using host computer (Mac), not his hacking machine (Kali).

**Why?**
Remember when we ran `airmon-ng check kill`? We killed the Network Manager on Kali to enable Monitor Mode.
* This broke Kali's ability to connect to Wi-Fi normally.
* To connect with Kali, you would need to restart the Network Manager or reboot the machine.
* It is faster and easier to just verify the password works by typing it into your main computer or phone.

### Summary Checklist for this Attack
1.  **Target:** Ensure the WEP network has active clients (check the `#Data` column).
2.  **Capture:** Run `airodump-ng` and save to a file. Wait for `#Data` to go above 20,000.
3.  **Crack:** Run `aircrack-ng [filename.cap]`.
4.  **Login:** Remove the colons from the result and use it as the Wi-Fi password.

---

