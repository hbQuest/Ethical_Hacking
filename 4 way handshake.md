
Client <-----------------------> Access Point (AP)

Để có mạng WiFi thì trước hết cần cấu hình một AP (một điểm phát wifi, có thể nhưng không nhất thiết là router). Để bảo mật cho AP, ta set Password cho AP, chẳng hạn hehehe123.

**Password AP** sẽ được chuyển thành **PMK AP** (256 bits), ví dụ 3f2a8c92b1e4d7f0c6a1dd29fe7b34c89d74a1e3c5f8b2d0e14a7c6d38f9a550

PMK AP = PBKDF2(Password Access Point, SSID, 4096, 256)

- **PMK**: Pairwise Master Key
- **PBKDF2**: Password-Based Key Derivation Function 2
- **SSID**: Service Set Identifier (đây chính là WiFi network name)

Khi ta cần kết nối từ Client vào mạng Wi-Fi, thì đầu tiên là từ Client, chọn AP (mạng Wi-Fi) tương ứng, nhập **Password Client**, ví dụ hahaha123 (lưu ý là chỉ trong trường hợp Password của Client ta nhập trùng với Password của AP thì kết nối mới được thiết lập). Việc xác minh có trùng hay không trùng dựa vào quy trình 4-way handshake.

**Password Client** sẽ được chuyển thành **PMK Client** (256 bits)

PMK Client = PBKDF2(Password Client, SSID, 4096, 256)

- **PMK**: Pairwise Master Key
- **PBKDF2**: Password-Based Key Derivation Function 2
- **SSID**: Service Set Identifier (đây chính là name của WiFi network)

Sau đó 4-way handshake diễn ra giữa Client và AP. Thông tin được truyền đi trong 4-way handshake dựa trên EAPOL Frame - đây là một frame layer 2 (hình dung đây là bao thư, hoặc chiếc xe vận chuyển thông tin).

Cấu trúc của một EAPOL có dạng:

| Ethernet Header	| 14 Bytes	| Source MAC (Sender) & Destination MAC (Receiver). |
|---------|---------|---------|
| Protocol Version | 1 Byte | Usually 1 or 2.|
| Packet Type |	1 Byte |	For the Handshake, this is Type 3 (Key).| 
|Body (EAPOL-Key) |	Variable |	The actual WPA2 data (tuỳ vào mỗi step bên dưới) |

- **EAPOL**: Extensible Authentication Protocol Over LAN

Trong Body (The EAPOL-Key Field) có chứa:
- Key Information (Flags): A set of switches (0 or 1) that identify "Is this Message 1?", "Is there a MIC?", "Is it encrypted?".
- Replay Counter: A sequence number (1, 2, 3...) to stop hackers from re-sending old packets.
- Key Nonce: The slot for the Random Number (ANonce or SNonce).
- Key MIC: The slot for the Signature.
- Key Data: The slot for extra secrets (like the GTK).

# Step 1: AP -> Client

AP tạo một số random **ANonce**, sau đó gửi tới Client ở dạng EAPOL Message 1.

- **ANonce**: Authenticator Nonce // AP Nonce

EAPOL-Key Message 1 có nội dung
- Goal: "Here is my random number."
- Key Information (Flags): "I am the Authenticator (Access Point)."
- Replay Counter: 1
- Key Nonce: [ ANonce ] (The Access Point's Random Number).
- Key MIC: 00 00 00... (Empty. Why? Because we haven't agreed on a key yet, so we can't sign it).
- Key Data: Empty.

# Step 2: Client -> AP

Client tạo một số random **SNonce**. Client vừa nhận được ANoce từ AP ở step 1. Vậy ở Client hiện tại đang có: PMK Client, ANonce, SNonce, MAC Address của Client, MAC Address của AP.

### 2.1. Client sẽ tạo thành **PTK Client** (512 bits)

PTK Client = PRF(PMK Client, ANonce, SNonce, MAC Address của Client, MAC Address của AP)

- **PTK**: Pairwise Transient Key
- **PRF**: Pseudo-Random Function
- **ANonce**: Authenticator Nonce // AP Nonce
- **SNonce**: Supplicant Nonce
- **MAC Address**: Media Access Control Addresses

PTK Client được tạo thành, có cấu gồm 3 phần chính và extra

PTK Client (512 bits) = KCK (128 bits) + KEK (128 bits) + TK (128 bits) + Unused / padding (128 bits)

- **KCK**: Key Confirmation Key - được sử dụng để tạo MIC (The signature/integrity check).
- **KEK**: Key Encryption Key - được sử dụng để encrypt the GTK (The Group Key) trong step 3.
- **TK**: Temporal Key - được sử dụng để encrypt actual Internet Data (Netflix, Browser, etc.).

### 2.2. Client sẽ tạo EAPOL Message 2 gồm có (lưu ý lúc này MIC đang là zero vì chưa tạo MIC):
EAPOL-Key Message 2 có nội dung:
- Goal: "Here is mine. I have signed this to prove I know the password."
- Key Information (Flags): "I am the Client. The MIC is present."
- Replay Counter: 1 (Must match Message 1).
- Key Nonce: [ SNonce ].
- Key MIC: 00 00 00... (Empty. Why? Because we haven't agreed on a key yet, so we can't sign it).
- Key Data: [ RSN Info ] (Your device says: "I confirm we are using WPA2-AES").


### 2.3. Client sẽ tạo **MIC Client** (128 bits)

MIC Client = HMAC-SHA1(KCK, EAPOL Message 2 Frame with MIC Zeros)

- **MIC**: Message Integrity Code
- **HMAC-SHA1**: Hash-Based Message Authentication Code - Secure Hash Algorithm 1
- **KCK**: Key Confirmation Key
- **EAPOL**: Extensible Authentication Protocol Over LAN

Sau giai đoạn này MIC Client đã được tạo có độ dài 128 bits, và nó sẽ thay thế phần MIC Zero trong EAPOL Message 2. Lúc này EAPOL Message 2 gồm có:
- Goal: "Here is mine. I have signed this to prove I know the password."
- Key Information (Flags): "I am the Client. The MIC is present."
- Replay Counter: 1 (Must match Message 1).
- Key Nonce: [ SNonce ].
- Key MIC: **MIC Client vừa tạo ở trên**
- Key Data: [ RSN Info ] (Your device says: "I confirm we are using WPA2-AES").

### 2.4. Client sẽ gửi EAPOL Message 2 này cho AP


AP lúc này có ANonce (tạo ở step 1), MAC Address của Client, MAC Address của AP, PMK của AP và SNonce (vừa nhận được ở step 2).

### 2.5. AP sẽ tạo thành **PTK AP** (512 bits)

PTK AP = PRF(PMK AP, ANonce, SNonce, MAC Address của Client, MAC Address của AP)

- **PTK**: Pairwise Transient Key
- **PRF**: Pseudo-Random Function
- **ANonce**: Authenticator Nonce // Access Point Nonce
- **SNonce**: Supplicant Nonce
- **MAC Address**: Media Access Control Addresses

PTK AP được tạo thành, có cấu gồm 3 phần chính và extra

PTK AP (512 bits) = KCK (128 bits) + KEK (128 bits) + TK (128 bits) + Unused / padding (128 bits)

- **KCK**: Key Confirmation Key - được sử dụng để tạo MIC (The signature/integrity check).
- **KEK**: Key Encryption Key - được sử dụng để encrypt the GTK (The Group Key) trong step 3.
- **TK**: Temporal Key - được sử dụng để encrypt actual Internet Data (Netflix, Browser, etc.).

### 2.6. AP sẽ tạo **MIC AP** (128 bits)

(lưu ý là EAPOL Message 2 mà AP vừa nhận được từ Client gửi qua đã có MIC Client 128 bits, lúc này AP sẽ zero out/reset thành phần MIC trong EAPOL Message 2 thành 0, để nó có thể calculate MIC của AP)

MIC AP = HMAC-SHA1(KCK, EAPOL Message 2 Frame with MIC Zeros)

### 2.7 AP sẽ compare MIC AP và MIC Client
- Nếu khớp nhau thì chứng tỏ Client biết **đúng PMK của AP** -> continue 4-way handshake (lúc này đồng nghĩa với việc PMK Client = PMK AP, PTK Client = PTK AP, MIC Client = MIC AP)
- Nếu không khớp nhau thì chứng tỏ Client không biết Password => disconnect

# Step 3: AP -> Client

### 3.1 AP sẽ tạo GTK
Just like the PTK comes from a PMK, the GTK comes from a master secret called the GMK. What is the GMK? It is a random 256-bit number generated by the AP when it first boots up. Who knows it? ONLY the AP. No client ever sees this. It stays inside the AP's silicon brain.
- GMK: Group Master Key
- GTK: Group Transient Key

GTK = PRF(GMK, "Group key expansion", MAC Address của AP, GNonce)

### 3.2 AP sẽ tạo encrypted data

Encrypted_Data = AES-WRAP(KEK, GTK)
- **AES**: Advanced Encryption Standard
- **KEK**: Key Encryption Key
- **GTK**: Group Transient Key

### 3.3. AP sẽ tạo EAPOL Message 3 gồm có

EAPOL-Key Message 3 có nội dung:
- Goal: "Proof accepted. Here is the Group Key for broadcast."
- Key Information (Flags): "I am Router. MIC is present. Encrypted Data is present."
- Replay Counter: 2 (Incremented).
- Key Nonce: [ ANonce ].
- Key MIC: 0000000.
- Key Data: [ Encrypted GTK ] (The Group Key hidden inside the encryption wrapper).

### 3.4 AP sẽ tạo MIC:
MIC AP = HMAC-SHA1(KCK của AP, EAPOL Message 3 Frame with MIC Zeros)

sau khi tạo xong, nó thay thế vào chỗ MIC field toàn zero ở EAPOL Message 3

### 3.4 AP gửi EAPOL Message 3 to Client

### 3.5 Client xác nhận lại MIC
MIC của Client = HMAC-SHA1(KCK của Client, EAPOL Message 3 Frame with MIC Zeros)

so sánh MIC của Client và MIC của AP, thấy trùng nhau => confirm lại là **AP thực sự biết PMK của Client** (lúc này đồng nghĩa với việc PMK Client = PMK AP, PTK Client = PTK AP, MIC Client = MIC AP)

### 3.6 Client decrypts the GTK using KEK.

### 3.7 Client installs:

PTK (TK) for unicast data
GTK for broadcast/multicast
At this point, both sides have the same unicast & group keys.

# Step 4: Client - AP
### 4.1 Client tạo EAPOL Message 4

EAPOL-Key Message 4 có nội dung:
- Goal: "I got the keys. I am ready."
- Key Information (Flags): "I am Client. MIC is present."
- Replay Counter: 2 (Matches Message 3).
- Key Nonce: 00 00 00... (No need to send it again).
- Key MIC: [ Valid Signature ].
- Key Data: Empty.

### 4.2 Client tạo MIC, rồi gửi EAPOL cho AP

### 4.3 AP receive EAPOL Message 4, AP tính MIC dựa trên KCK => xác nhận trùng nhau => kết thúc 4-way handshake

Action: You send a final acknowledgment (ACK).

Result: The handshake is closed. The "Port" is opened. All future traffic (Netflix, Facebook) is encrypted using the PTK.


After this:
Both AP and Client have:
Same PTK (KCK, KEK, TK)
Same GTK
Data traffic now flows encrypted using TK (unicast) and GTK (broadcast/multicast).

AP installs PTK after verifying Message 2

Client installs PTK after verifying Message 3