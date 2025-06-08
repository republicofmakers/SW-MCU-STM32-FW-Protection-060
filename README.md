# SW-MCU-STM32-FW-Protection-060

## Clone Firmware 

If you program your device without RDP or any other security options, your firmware can be cloned so easily.

Here is example:

Connect the device that you want to clone, read its memory and save as .bin file.

<img width="1431" alt="get firmware" src="https://github.com/user-attachments/assets/6d816dc9-9cbd-4128-9662-538ad58c0191" />

Upload .bin file to new microcontroller

<img width="1434" alt="upload firmware" src="https://github.com/user-attachments/assets/86ec04ea-83f9-4170-9288-b5ac636ad084" />

Here is the microcontrollers that I tested.

<img width="860" alt="clone" src="https://github.com/user-attachments/assets/1f7ff60b-ce20-4632-b025-4a74c2990da5" />

(cloned device / original device)

## Protect Firmware 

Here is a simple show case for RDP (Read-Out Protection) with STM32F103C8T6.

Program your microcontroller, with .hex or .bin file.

<img width="1433" alt="upload your hex" src="https://github.com/user-attachments/assets/bed51dfa-b82b-420b-a979-dc09e5ed4fdf" />

Activate Read-Out Protection

<img width="1432" alt="active RDP" src="https://github.com/user-attachments/assets/f03a3304-33aa-41e3-99ea-0d2e97f2cd52" />

Now your device cant be read on cube programmer.

<img width="1434" alt="now you are protected" src="https://github.com/user-attachments/assets/7f378a7f-519c-4af3-ab21-92ed93f0a31a" />

Of cource if you deactive , you cant stil read the previous firmware that was one the MCU.

<img width="1433" alt="you cant read" src="https://github.com/user-attachments/assets/0ad978ad-f5c6-4484-b815-2bb639579ebb" />

Here is the microcontroller with RDP protection

<img width="668" alt="protect" src="https://github.com/user-attachments/assets/5b190e0e-7cff-4167-a9a4-d1635e6aa8de" />

(RDP Protected)

Note: You can deactive read-out protection and upload another firmware.



## Other Options

I used RDP (Read-Out Protection) in this example. While it's sufficient for many applications and provides a basic level of protection against external code extraction, it is still potentially vulnerable to physical attacks or invasive techniques.

⚠️ If your application demands higher security—such as protection against firmware theft, tampering, or unauthorized firmware updates—you should consider enabling additional STM32 security features.

The table below provides a comprehensive overview of STM32’s advanced security options, including secure boot mechanisms, TrustZone isolation, debug protection, and irreversible lock features. These can help ensure that your firmware remains trusted and secure, especially in production or IoT deployments.


| Category           | Feature               | Protection Levels / Modes                                 | Purpose / Description                                                                                     | STM32 Support                   | Reversible?            |
|--------------------|------------------------|------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------|------------------------|
| **Basic Protection** | RDP (Read-Out Protection) | RDP0 = No Protection<br>RDP1 = Flash locked<br>RDP2 = Permanent lock | Prevents external flash read/debug access. RDP2 is **permanent** and blocks even mass erase.              | ✅ All STM32 families            | RDP0/1 = ✅<br>RDP2 = ❌ |
|                    | WRP (Write Protection)   | Per-sector write lock                                      | Prevents overwrite of flash regions (like bootloaders or critical code).                                  | ✅ All STM32 families            | ✅ (via reprogramming) |
|                    | PCROP                   | Per-sector read lock (even at RDP1)                        | Hides code from external access. Ideal for IP protection.                                                  | ✅ L0/L1/L4/L5, U5, H7+          | ✅ (via reprogramming) |
| **Boot Security**   | SBSFU (Secure Bootloader) | AES encryption + RSA/ECDSA + rollback protection           | ST’s secure bootloader & firmware update system. Protects firmware integrity & authenticity.               | ✅ L4, L5, U5, H5                | ✅ (firmware replaceable) |
|                    | Custom Secure Boot       | SHA/ECDSA verification, optional AES                       | User-defined bootloader to validate firmware. Depends on your design.                                      | ✅ All with enough flash/RAM     | ✅ (user-defined)       |
| **TrustZone**       | Secure / Non-Secure Split | Secure code, memory & peripheral isolation                 | Separates sensitive code from user app. Secures boot, keys, crypto, etc.                                   | ✅ L5, U5, H5                   | ✅ (unless BOOT_LOCK = ❌) |
|                    | TZEN Option Byte         | 0 = Disabled<br>1 = Enabled (permanent)                    | Enables TrustZone hardware isolation. Once set = **permanent**.                                            | ✅ L5, U5, H5                   | ❌ (irreversible)       |
| **Root of Trust**   | ST-iRoT                  | ST factory-provisioned secure boot                         | Simplifies secure boot via preloaded trusted root.                                                         | ✅ STM32H5 only                 | ❌ (factory set)        |
|                    | OEM-iRoT                 | Developer-injected root key                               | You define keys & boot policies using ST tools. Usually one-time fuse.                                     | ✅ STM32H5, some U5             | ❌ (permanent fuse)     |
| **Runtime Safety**  | Watchdog (IWDG/WWDG)     | Timeout-based reset                                        | Detects and recovers from system hang or faults.                                                           | ✅ All STM32 families           | ✅ (configurable)       |
|                    | CRC / ECC / RAM Parity   | CRC = data check<br>ECC = 1-bit correct, 2-bit detect      | Detects memory corruption or data errors in RAM/Flash.                                                     | ✅ L4+, H7, U5                  | ✅                     |
|                    | Tamper Pins              | Physical tamper detection input                           | Detects external hardware attacks or intrusion. Useful for anti-tamper measures.                           | ✅ L0/L1/L4/L5, U5, H5          | ✅                     |
| **Debug Control**   | Secure Debug Lock        | Signed access via Debug Authentication (DA)               | Certificate-based control of debug port. Prevents access post-deployment.                                  | ✅ STM32H5 only                | ✅ with certificate<br>❌ without |
|                    | JTAG Lock / Password Lock| Option Byte or Fuse                                       | Disables or restricts SWD/JTAG. Fuse-based locks are permanent.                                            | ✅ Lx, F3, F4, F7, H7, H5       | Option Byte = ✅<br>Fuse = ❌ |
| **Boot Area Lock**  | BOOT_LOCK / SECBOOTADDx  | Locks boot region and secure boot address                 | Prevents changing TrustZone config or boot jump address. BOOT_LOCK = permanent.                            | ✅ L5, U5, H5                   | BOOT_LOCK = ❌<br>SECBOOTADDx = ✅ (before lock) |


STM32 Firmware Protection

Ceyhun Pempeci


