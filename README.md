ROOT_ACCESS // Developer Documentation

Version: Prototype v0.9
Tech Stack: Single-File HTML5, Vanilla JavaScript, Tailwind CSS (CDN), Web Audio API
Save Data: localStorage key: 'root_access_save'

1. Game Overview

ROOT_ACCESS is a terminal-based roguelike horror simulation. The player acts as a contractor for a mysterious corporation, tasked with remotely connecting to servers to purge "Corruption" data while managing connection stability, avoiding detection by "Watchdog" daemons, and paying off a massive debt.

Core Loop

Connect: Player uses connect [level] to link to a procedural server.

Explore: Use unix-like commands (ls, cd, cat) to navigate a generated directory tree.

Deduce: Use scan to distinguish between Safe Files, Traps, and Corruption (Mimics).

Action: purge the corruption or decrypt locked files (via minigame).

Upgrade: Earn credits to buy tools and hardware upgrades to handle higher difficulties.

2. Architecture & Systems

2.1 State Management

The game state is managed by the GameState class and persisted to localStorage.

stability: The player's health (0-100). Drains passively and via actions.

credits: Currency for the shop.

debt: The primary goal (starts at 5000).

inventory: Array of strings (e.g., ['decryption_module', 'scanner_v2']).

hardware: Object tracking upgrade levels { cpu: 0, ram: 0, net: 0 }.

fileSystem: The current active server structure (nested objects).

2.2 The File System

Files and directories are JSON objects.

Structure:

"folder_name": {
    type: "dir",
    locked: boolean,      // Requires password/unlock
    encrypted: boolean,   // Requires decryption_module
    hasWatchdog: boolean, // Triggers alarm on entry
    content: { ... }      // Nested files/dirs
}
"file_name": {
    type: "file" | "exec",
    isCorrupt: boolean,   // The target (if scanned)
    isTrap: boolean,      // Damages player (if scanned)
    scanned: boolean      // True = reveals properties
}


Generation: generateDeepStructure() creates recursive folders based on difficulty depth. Targets (Corruption/Traps) are injected randomly using injectTargetIntoRandomFolder.

2.3 The Action System

To add tension, powerful commands (scan, purge, decrypt) use startAction().

Mechanic: Displays a progress bar [====....].

Blocking: Input is disabled during actions unless abort is typed.

Modifiers: Duration is reduced by the CPU Hardware stat (15% per level).

2.4 Threat Systems

Stability Drain: Constant background drain + discrete cost per command.

Passive Drain: 0.4 * Difficulty * TraitMod.

Active Cost: 1.0 * Difficulty (Reduced by NET Hardware).

Watchdog Daemon: * Spawns in specific folders on Lvl 2+ servers.

Trigger: Entering the folder starts a 6-second panic timer.

Fail State: Staying too long sets Stability to 0 (Instant Game Over).

Counter: cd .. immediately to escape.

Traps: Mimic files that look like corruption. If purge is used without scanning, they explode (-50 Stability).

3. Key Features (v0.9)

Minigames

Decryption: A Hex-code typing game. The player must type a sequence (e.g., A4 8B) within 12 seconds to unlock encrypted files.

Visuals

Themes: Changes CSS variables (Color/Phosphor glow) based on server type.

RESEARCH (Cyan), MILITARY (Red), FINANCE (Gold).

Feedback: Screen flashes red on damage. Text glitches on low stability.

The Shop & Progression

Tools:

decryption_module: Unlocks decrypt command.

scanner_v2: Auto-detects/marks traps when entering a folder.

root_kit: Auto-runs ls upon directory entry.

Hardware (Stackable):

CPU: Speeds up progress bars.

RAM: Increases Max Stability and adds passive regen.

NET: Reduces stability cost of typing commands.

4. Data Dictionaries

Server Traits (TRAITS)

Random modifiers applied to servers on connect.

STANDARD: No modifiers.

HARDENED: Double purge cost.

FRAGILE: Double stability drain.

LEAKY: Instant scan times.

GHOSTED: High Watchdog spawn chance.

Commands

Command

Usage

Description

ls

ls

List current directory contents.

cd

cd [dir]

Navigate directories (.. to go back).

scan

scan [file]

Reveal if a file is Safe, Trap, or Corruption.

purge

purge [file]

Destroy the target file (Win condition).

decrypt

decrypt [file]

Start minigame to unlock encrypted file.

connect

connect [lvl]

Start a job (Lvl 1-3).

shop

shop [buy]

View or purchase upgrades.

pay

pay [amount]

Reduce debt.

5. Roadmap & Next Steps (v1.0+)

Narrative System (The "Why"):

Implement mail command.

Trigger emails based on Debt milestones (e.g., <4000, <1000).

Story branches: Corporate loyalist vs. Hacktivist.

Advanced Server Mechanics:

Firewalls: Nodes that must be hacked to access sub-folders.

Data Exfiltration: Optional side-objectives (downloading files for bonus credits).

Endgame:

What happens when Debt == 0?

A final "Root Server" raid.

6. How to Run

Save the code as index.html.

Open in any modern browser (Chrome/Firefox recommended for AudioContext).

Click anywhere on the page to initialize the Audio Engine.
