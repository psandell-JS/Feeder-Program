# Feeder-Program
Automated card feeder PLC program for Omron CX-Programmer.

## Project Overview
This project provides a complete PLC (Programmable Logic Controller) program for an automated feeder system that feeds cards onto mail pieces. The program is designed for use with Omron PLCs and CX-Programmer software.

## Features
- **Automated Sequencing**: 5-step feed cycle with precise timing control
- **Safety Interlocks**: Emergency stop, safety guard, and motor overload protection
- **Dual Operation Modes**: Manual (single cycle) and Auto (continuous) modes
- **Fault Detection**: Automatic detection and handling of card empty, motor overload, and timeout conditions
- **Diagnostics**: Cycle counter, fault counter, and comprehensive status indicators
- **Well-Documented**: Complete I/O mapping, sequence flow, and troubleshooting guides

## Documentation Files

### 1. [PROGRAM_STRUCTURE.md](PROGRAM_STRUCTURE.md)
Complete program architecture including:
- I/O address mapping (inputs, outputs, internal relays)
- Timer and counter definitions
- Sequence flow diagrams
- Safety features and fault handling
- Operation modes and procedures

### 2. [LADDER_LOGIC.txt](LADDER_LOGIC.txt)
Full ladder logic program listing:
- 100 rungs of ladder logic code
- 6 program sections (Safety, Start/Stop, Faults, Sequence, Outputs, Diagnostics)
- Detailed comments and annotations
- Testing and troubleshooting procedures
- CX-Programmer configuration settings

### 3. [CX_IMPORT_GUIDE.md](CX_IMPORT_GUIDE.md)
Step-by-step instructions for:
- Creating new CX-Programmer project
- Importing ladder logic into CX-Programmer
- Testing in simulator mode
- Downloading to PLC hardware
- Common issues and solutions

## Quick Start

### For CX-Programmer Users:
1. Read [PROGRAM_STRUCTURE.md](PROGRAM_STRUCTURE.md) to understand the system
2. Open CX-Programmer and create a new project
3. Follow [CX_IMPORT_GUIDE.md](CX_IMPORT_GUIDE.md) to import the ladder logic
4. Use [LADDER_LOGIC.txt](LADDER_LOGIC.txt) as reference for each rung
5. Test in simulator mode before downloading to PLC

### System Requirements:
- **Software**: Omron CX-Programmer 9.0+ (part of CX-One suite)
- **Hardware**: Omron CP1L, CP1H, or CJ series PLC
- **I/O**: 10 digital inputs, 8 digital outputs minimum

## Program Highlights

### Feed Cycle Sequence:
1. **Wait for Mail** - System ready, waiting for mail piece
2. **Pick Card** - Vacuum activates, picks card from hopper (1.5s)
3. **Move Forward** - Feeder moves card over mail piece (2.0s)
4. **Place Card** - Vacuum releases, card placed on mail (1.0s)
5. **Return Home** - Feeder returns to start position (2.5s)

### Safety Features:
- Emergency stop with immediate shutdown
- Safety guard interlock prevents operation if open
- Motor overload detection and fault handling
- Card empty detection with warning
- Timeout protection on all movements

### Operation Modes:
- **Manual Mode**: Single cycle per start button press (testing/setup)
- **Auto Mode**: Continuous operation for production

## Compatible PLC Models
- CP1L-M40DR-D (recommended)
- CP1H-X40DR-A
- CJ1M-CPU11
- CJ2M-CPU11
- Other compatible Omron PLCs with adequate I/O

## License
This program is provided as-is for use with Omron PLC systems. Modify as needed for your specific application.

## Support
For questions or issues:
1. Review the documentation files in this repository
2. Check the troubleshooting section in LADDER_LOGIC.txt
3. Consult Omron technical support for CX-Programmer specific questions

## Version History
- **v1.0** (2026-02-03): Initial release with complete ladder logic program

---
**Note**: This is a template program. You must verify and adjust timing values, sensor configurations, and safety requirements to match your specific hardware installation and application requirements.
