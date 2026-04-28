# sideros
Sideros Hybrid C2 (v5.0)Sideros Hybrid is a minimalist, stable Command and Control (C2) framework designed for red-team labs and security research. It utilizes a Single-Key XOR Stream Cipher for communication, ensuring high reliability across different operating systems without the padding issues common in standard AES implementations.⚙️ How It Works: The "LotL" ApproachSideros uses a Living-off-the-Land strategy. Instead of relying on a pre-installed Python environment on the target, the framework bundles a portable, minimal Python interpreter.Transport: All JSON-based beacons are XOR-encrypted using a 32-byte key generated during the build phase.Persistence-Ready: The agent resides in the %LOCALAPPDATA%\Microsoft\Vault directory, mimicking a standard Windows credential storage path.Bootstrapping: It utilizes the sitecustomize.py feature of Python. When the portable python.exe starts, it automatically executes sitecustomize.py, launching the implant in-memory.
🚀 Installation & Setup (Kali Linux)This framework is designed to run using only native Kali and Python repositories.

1. PrerequisitesEnsure your Kali environment is up to date. The C2 requires the standard cryptography library (often pre-installed, but available in the python3-cryptography Kali repo).Bashsudo apt update && sudo apt install python3 python3-pip python3-cryptography -y
2. Prepare the Environment
3. Bash# Clone the repository (or create the directory) mkdir sideros-hybrid && cd sideros-hybrid

# Ensure your script files are named:
# sideros_builder.py
# sideros_core.py
# sideros_c2.py

chmod +x *.py
🛠️ Operational Workflow
Step 1: Generate the BuildRun the builder. It will detect your current Kali IP, generate a fresh 32-byte XOR key, and save that key to secret.key.Bashpython3 sideros_builder.py
Artifacts Created: dist/sideros_payload.zip (The portable implant)Key Created: secret.key (Used by the C2 to decrypt traffic)

Step 2: Host the PayloadHost the generated ZIP so the target can fetch it via PowerShell.Bashcd dist
python3 -m http.server 80

Step 3: Launch the C2 ListenerOpen a new terminal window, navigate to the root sideros-hybrid folder, and start the interactive listener. It will automatically load the key from secret.key.Bashsudo python3 sideros_c2.py

Step 4: Deploy the DropperCopy the PowerShell dropper printed by the builder and run it on the target Windows machine. The dropper is "Re-run Proof"—it will automatically kill old sessions and clean up previous files before starting the new agent.🎮 C2 Interaction MenuThe C2 provides a focused interactive shell. Use the following commands:CommandDescriptionhelpDisplays the help menu.listLists all active beacons by their unique ID.interact <ID>Switches focus to a specific agent for tasking.backReturns to the main menu.exitSafely shuts down the C2 server.Any StringWhile interacting, any text entered is sent as a shell command to the target.🛡️ Security FeaturesKey Syncing: The C2 and Agent are decoupled. The C2 reads the key from a file, allowing you to update the agent without re-writing C2 code.Silent Execution: No error logs are written to the target disk.Console Hiding: The agent uses ShowWindow native calls to remain invisible to the user.Auto-Cleanup: The dropper cleans the deployment directory to avoid forensic "ghosting" of old builds.⚖️ 

**DisclaimerEducational purposes only. Sideros Hybrid is intended for authorized penetration testing and security research within controlled lab environments. Unauthorized use is strictly prohibited.**
