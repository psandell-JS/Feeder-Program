# CX-Programmer Project Import Instructions

## How to Use This Program with CX-Programmer

### Prerequisites
1. **Software**: Omron CX-Programmer version 9.0 or later (part of CX-One suite)
2. **Hardware**: Omron CP1L, CP1H, or CJ series PLC (or compatible controller)
3. **Computer**: Windows PC with USB or serial port for PLC connection

### Step-by-Step Import Process

#### Method 1: Create New Project and Enter Ladder Logic

Since the ladder logic is provided in text format, you'll need to create a new project and manually enter the rungs:

1. **Create New Project**
   - Open CX-Programmer
   - File → New
   - Select your PLC model (e.g., CP1L-M40DR-D)
   - Set device type and I/O configuration
   - Click OK

2. **Configure I/O Table**
   - Go to I/O Table view
   - Assign inputs (0.00 - 0.09) per the PROGRAM_STRUCTURE.md document
   - Assign outputs (100.00 - 100.07)
   - Save the I/O configuration

3. **Create Symbol Table**
   - Go to Edit → Symbol Table
   - Enter symbols for inputs, outputs, and work bits as defined in LADDER_LOGIC.txt
   - This makes the program more readable
   - Save the symbol table

4. **Enter Ladder Logic**
   - Switch to Ladder Editor view
   - Create new ladder section
   - Manually enter each rung from LADDER_LOGIC.txt:
     - SECTION 1: Rungs 0-10 (Safety Interlocks)
     - SECTION 2: Rungs 11-20 (Start/Stop Logic)
     - SECTION 3: Rungs 21-35 (Fault Detection)
     - SECTION 4: Rungs 36-80 (Sequence Control)
     - SECTION 5: Rungs 81-95 (Outputs)
     - SECTION 6: Rungs 96-100 (Diagnostics)
   - Add comments to each rung for documentation
   - Use the ladder instruction palette to insert contacts, coils, timers, and counters

5. **Verify Program**
   - Program → Compile
   - Check for any syntax errors
   - Review and fix any compilation issues

6. **Save Project**
   - File → Save As
   - Name: "Card_Feeder_v1.0.cxp"
   - Save in your project directory

#### Method 2: Using the Simulator

Before downloading to actual hardware, test in simulator mode:

1. **Start Simulator**
   - Simulation → Start Simulator
   - CX-Programmer will switch to simulation mode

2. **Test Sequence**
   - Right-click on input bits to force ON/OFF
   - Force 0.00 (START_BTN) to test startup
   - Force 0.06 (MAIL_PRESENT) to start cycle
   - Watch the sequence steps activate (W0.05 through W0.09)
   - Monitor outputs (100.00 through 100.07)

3. **Test Safety Functions**
   - Force 0.02 (E_STOP) to verify immediate shutdown
   - Force 0.03 (SAFETY_GUARD) OFF to test interlock
   - Force 0.08 (MOTOR_OVERLOAD) to test fault response

4. **Test Fault Recovery**
   - Create fault condition
   - Hold START button for 3 seconds (TIM005) to reset
   - Verify fault clears and system can restart

### Downloading to PLC

1. **Connect to PLC**
   - Connect USB cable or RS-232C cable
   - PLC → Work Online
   - Select communication port
   - Click Connect

2. **Download Program**
   - PLC → Transfer → To PLC
   - Select "Program" and "I/O Table"
   - Click Transfer
   - Wait for download to complete

3. **Set PLC to RUN Mode**
   - PLC → Operating Mode → Monitor Mode or Run Mode
   - Program will start executing

4. **Monitor Operation**
   - Stay in online mode to monitor
   - Watch inputs and outputs in real-time
   - Use Data Trace function to log cycle times
   - Monitor work bits to see sequence progression

### Converting Text Ladder to CX-Programmer Format

The ladder logic provided uses standard notation that maps to CX-Programmer instructions:

| Text Notation | CX-Programmer Instruction | Key Press / Method |
|--------------|---------------------------|-------------------|
| --[ ]-- | LD (Load) / AND / OR normally open contact | F5 (LD), F6 (AND), F7 (OR) |
| --[ NOT ]-- | LD NOT / AND NOT / OR NOT (normally closed) | F5+N, F6+N, F7+N |
| --( )-- | OUT (Output Coil) | F4 |
| --( SET )-- | SET (Set/Latch) | S, E, T |
| --( RSET )-- | RSET (Reset/Unlatch) | R, S, E, T |
| @TIM000 15#XX | TIM (Timer) | T, I, M, 0, 0, 0, [space], 1, 5, #, XX |
| @CNT000 XXXX | CNT (Counter) | C, N, T, 0, 0, 0, [space], XXXX |
| --[ @ ]-- | @ (Differential/Rising Edge) | @ symbol before address |

### Example: Creating Rung 0

Text format:
```
RUNG 0: Safety Interlock Check - E-Stop
|--[ NOT 0.02 ]----------------------------------------( W0.02 )--|
```

In CX-Programmer:
1. Click in ladder editor
2. Press F5 (LD) or click LD button
3. Type: 0.02
4. Press Shift+F6 (AND NOT) or right-click → "Convert to NOT"
5. Press F4 (OUT)
6. Type: W0.02
7. Press Enter
8. Add comment: "Safety Interlock Check - E-Stop"

### Tips for Efficient Entry

1. **Use Symbol Table First**: Define all symbols before entering ladder logic
   - Makes address entry faster
   - Improves readability
   - Reduces errors

2. **Copy-Paste Similar Rungs**: Many rungs have similar structure
   - Copy a rung and modify addresses
   - Faster than creating from scratch

3. **Use Keyboard Shortcuts**: Learn CX-Programmer shortcuts
   - F5: LD (Load)
   - F6: AND
   - F7: OR
   - F4: OUT
   - F2: Insert rung
   - Ctrl+C/V: Copy/Paste

4. **Compile Frequently**: Check for errors early
   - Compile after each section
   - Fix errors immediately
   - Prevents accumulation of issues

5. **Save Often**: Don't lose your work
   - Save after each major section
   - Use version numbers (v1.0, v1.1, etc.)

### Project Files Structure

After completing import, your project folder should contain:

```
Card_Feeder_Project/
├── Card_Feeder_v1.0.cxp          (Main project file)
├── Card_Feeder_v1.0.opt          (Options file)
├── PROGRAM_STRUCTURE.md          (Documentation - this repo)
├── LADDER_LOGIC.txt              (Ladder logic reference - this repo)
├── CX_IMPORT_GUIDE.md            (This file - this repo)
└── Backup/
    └── Card_Feeder_v1.0_backup.cxp
```

### Common Issues and Solutions

**Issue**: "Address out of range" error
- **Solution**: Check PLC model supports the address range used
- Modify addresses if needed for your specific PLC model

**Issue**: Timer format error
- **Solution**: Ensure timer format matches: TIM000 #0015 (not 15#15)
- CX-Programmer uses different format than text notation

**Issue**: Symbol not found
- **Solution**: Ensure symbol table is complete before entering ladder
- Add missing symbols to symbol table

**Issue**: Compile error on SET/RSET
- **Solution**: SET and RSET must be used with bit addresses (not word addresses)
- Check address format (should be X.XX format)

### Next Steps After Import

1. **Review Program**: Walk through each section to ensure correct entry
2. **Simulate**: Test thoroughly in simulator mode
3. **Document Changes**: Note any modifications made during import
4. **Backup**: Create backup copy of working project
5. **Version Control**: Save as new version before testing on hardware

### Support and Resources

- **Omron Support**: https://automation.omron.com/en/us/support
- **CX-Programmer Manual**: Search for "W361-E2" manual
- **Training Videos**: Omron Learning Center
- **Community Forums**: Automation Community (learn.automationcommunity.com)

### Contact

For questions about this specific program implementation:
- Review the PROGRAM_STRUCTURE.md for detailed I/O and logic description
- Review LADDER_LOGIC.txt for complete ladder logic listing
- Check troubleshooting section in LADDER_LOGIC.txt

---

**Program Version**: 1.0
**Date Created**: 2026-02-03
**Compatible With**: CX-Programmer 9.0+, CP1L/CP1H/CJ Series PLCs
