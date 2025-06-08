# SW-MCU-STM32-FW-Protection-060

Here is some general information for STM32 Security Features.


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





