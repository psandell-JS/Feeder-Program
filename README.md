# Feeder-Program
**Encoder-Synchronized Card Feeder System for Omron CP1H-X40DT-D**

## 🚀 NEW USER? START HERE: [START_HERE.md](START_HERE.md)

## Project Overview
Complete PLC program for an automated card feeder that precisely places cards onto moving paper using encoder-based position control. The feeder prepares a card at its tip, then feeds it onto paper at the exact moment based on encoder position tracking.

## System Specifications

### Hardware
- **PLC**: Omron CP1H-X40DT-D (24 inputs, 16 transistor outputs)
- **Encoder**: Accu-Coder 3032091 (paper position tracking via high-speed counter)
- **Start Sensor**: Banner SME312D (detects incoming paper)
- **Stop Sensor**: Optex 15PL (detects card ready at feeder tip)
- **Programming**: CX-Programmer 9.0 or later

### Key Features
- **Encoder Synchronization**: Position-based feeding using high-speed counter (100kHz capable)
- **Automatic Card Preparation**: Feeds card to tip and waits for paper
- **Precise Positioning**: Calculates exact feed point based on encoder counts
- **Single Card Enforcement**: Logic prevents multiple cards feeding per cycle
- **Timeout Protection**: Watchdog timers prevent indefinite operation
- **Comprehensive Diagnostics**: Cycle counter, fault tracking, position monitoring

## Documentation Files

### Core Program Files

#### 1. [HARDWARE_SPECS.md](HARDWARE_SPECS.md) ⭐ START HERE
Complete hardware specifications and setup guide:
- CP1H-X40DT-D PLC configuration
- Accu-Coder 3032091 encoder setup and wiring
- Banner SME312D and Optex 15PL sensor specifications
- I/O address mapping (inputs, outputs, data registers)
- High-speed counter configuration
- Position calculation formulas
- Calibration procedures

#### 2. [LADDER_LOGIC_ENCODER.txt](LADDER_LOGIC_ENCODER.txt) ⭐ MAIN PROGRAM
Complete ladder logic program (100 rungs):
- **Section 0**: High-speed counter configuration (encoder setup)
- **Section 1**: Fault detection
- **Section 2**: Start/stop control logic
- **Section 3**: Automatic card preparation to tip
- **Section 4**: Paper detection and position calculation
- **Section 5**: Encoder-synchronized card feeding
- **Section 6**: Timeout watchdog protection
- **Section 7**: Outputs and status indicators
- **Section 8**: Diagnostics and data management

#### 3. [WIRING_DIAGRAM.md](WIRING_DIAGRAM.md)
Detailed wiring instructions:
- Encoder connection (shielded cable, grounding)
- Sensor wiring (Banner SME312D, Optex 15PL)
- Input/output terminal assignments
- Power supply connections
- Shield termination and noise prevention
- Installation checklist and testing procedures

#### 4. [CX_IMPORT_GUIDE.md](CX_IMPORT_GUIDE.md)
Step-by-step CX-Programmer usage:
- Creating new project for CP1H-X40DT-D
- Configuring high-speed counter mode
- Entering ladder logic manually
- Symbol table setup
- Simulator testing
- Downloading to PLC

#### 5. [COMMISSIONING_CHECKLIST.md](COMMISSIONING_CHECKLIST.md) ⭐ USE DURING SETUP
Complete installation and commissioning checklist:
- Pre-installation preparation
- Mechanical installation steps
- Electrical wiring verification
- Software installation and configuration
- Power-up testing procedures
- Encoder functionality testing
- Calibration steps with calculations
- Production speed validation
- Sign-off and acceptance criteria

#### 6. [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
Comprehensive troubleshooting guide:
- Quick diagnostic checklist
- Status indicator meanings
- Encoder issues (not counting, backwards, erratic)
- Sensor issues (Banner SME312D, Optex 15PL)
- Position/timing issues (too early, too late, inconsistent)
- Feed mechanism issues (jams, multiple cards)
- Fault recovery procedures
- Preventive maintenance schedule

### Legacy Files (Original Generic Template)
- [PROGRAM_STRUCTURE.md](PROGRAM_STRUCTURE.md) - Original generic design
- [LADDER_LOGIC.txt](LADDER_LOGIC.txt) - Original time-based version (replaced by encoder version)

**Note**: Use the encoder-specific files (HARDWARE_SPECS.md, LADDER_LOGIC_ENCODER.txt, WIRING_DIAGRAM.md) for this application.

## How It Works

### Operation Sequence

1. **Card Preparation Phase**
   - System checks if card is at tip (Stop Sensor = Optex 15PL)
   - If no card detected, feeder automatically advances card
   - When Stop Sensor detects card, feeding stops
   - "Ready" indicator illuminates

2. **Paper Detection Phase**
   - Encoder continuously tracks paper position on base
   - Start Sensor (Banner SME312D) monitors for incoming paper
   - When paper detected, system logs current encoder position
   - Target feed position calculated: `TARGET = CURRENT + DELAY_COUNTS`

3. **Position-Based Feeding Phase**
   - System continuously compares encoder count to target position
   - When encoder reaches target position, feed trigger activates
   - Card feeds forward onto paper at precise moment
   - Feed timer ensures complete card placement (2 seconds)

4. **Cycle Completion**
   - Feed cycle completes
   - Cycle counter increments
   - System automatically prepares next card
   - Ready for next paper in Auto mode

### Position Calculation

```
FEED_DELAY_COUNTS = (Distance_mm × Encoder_PPR) / Wheel_Circumference_mm

Example:
- Distance from sensor to feed point: 250mm
- Encoder: 2000 PPR on 100mm diameter wheel (314mm circumference)
- FEED_DELAY_COUNTS = (250 × 2000) / 314 = 1592 counts
```

Adjust D2 register during commissioning to fine-tune placement accuracy.

## Quick Start Guide

### For First-Time Setup:

1. **Read Hardware Documentation**
   - Start with [HARDWARE_SPECS.md](HARDWARE_SPECS.md)
   - Understand encoder, sensors, and I/O requirements

2. **Wire the System**
   - Follow [WIRING_DIAGRAM.md](WIRING_DIAGRAM.md) exactly
   - Use shielded cable for encoder (critical!)
   - Verify all connections before power-on

3. **Create CX-Programmer Project**
   - Follow [CX_IMPORT_GUIDE.md](CX_IMPORT_GUIDE.md)
   - Configure CP1H-X40DT-D as PLC model
   - Set up high-speed counter for encoder (Mode 2, inputs 0.00, 0.01, 0.02)

4. **Enter Ladder Logic**
   - Use [LADDER_LOGIC_ENCODER.txt](LADDER_LOGIC_ENCODER.txt)
   - Enter all 100 rungs in sections
   - Add comments for clarity
   - Compile and fix any errors

5. **Test in Simulator**
   - Force encoder inputs to verify counting
   - Test sequence logic

6. **Download and Commission**
   - Download program to PLC
   - Test encoder counting (monitor D0)
   - Verify sensor operations
   - Calibrate feed delay (adjust D2)
   - Run test cycles at slow speed
   - Increase to production speed

## System Requirements

### Software
- Omron CX-Programmer version 9.0 or later (part of CX-One suite)
- Windows PC for programming
- USB or serial cable for PLC connection

### Hardware
- Omron CP1H-X40DT-D PLC
- 24VDC regulated power supply (5A minimum)
- Accu-Coder 3032091 incremental encoder
- Banner SME312D through-beam sensor
- Optex 15PL photoelectric sensor
- Control panel buttons (Start, Stop, Reset)
- Motor driver (24VDC, <0.3A or with external relay)
- Solenoid valves for feeder control
- Status indicator lights (Green/Red/Yellow/White)

## Calibration

### Initial Calibration Steps:

1. **Verify Encoder Operation**
   ```
   - Mount encoder on paper drive mechanism
   - Power on system
   - Monitor D0 (current encoder count) in CX-Programmer
   - Manually rotate or run paper
   - Verify counts increase (forward direction)
   - If counting backwards, swap A and B phase wires
   ```

2. **Measure Physical Distances**
   ```
   - Measure distance from Start Sensor to card placement point
   - Example: 250mm from sensor to where card should land on paper
   - Note this measurement
   ```

3. **Calculate Feed Delay**
   ```
   Formula: FEED_DELAY_COUNTS = (Distance × PPR) / Circumference
   
   Example calculation:
   - Distance: 250mm
   - Encoder PPR: 2000 (Accu-Coder 3032091)
   - Wheel diameter: 100mm → Circumference: 314mm
   - FEED_DELAY_COUNTS = (250 × 2000) / 314 = 1592 counts
   
   Update D2 register with this value in ladder logic or via HMI
   ```

4. **Test and Fine-Tune**
   ```
   - Run test cycles at slow speed
   - Measure where card actually lands on paper
   - If too early: Increase D2 by 50-100 counts
   - If too late: Decrease D2 by 50-100 counts
   - Repeat until accurate
   - Test at production speed and adjust if needed
   ```

## Troubleshooting

### Common Issues

| Problem | Cause | Solution |
|---------|-------|----------|
| Encoder not counting | Wiring, power, or config issue | Check A/B phase wiring, verify 24V power, confirm high-speed counter mode 2 |
| Counting backwards | A/B phases swapped | Swap encoder A and B phase connections |
| Card feeds too early | Feed delay too small | Increase D2 value by 50-100 counts |
| Card feeds too late | Feed delay too large | Decrease D2 value by 50-100 counts |
| Card doesn't reach tip | Mechanical issue or sensor misaligned | Check card path, verify Stop Sensor aligned, check motor operation |
| Start Sensor not detecting | Sensor misalignment or failed | Check sensor beam alignment, clean lens, verify 24V power |
| Multiple cards feeding | Logic error or sensor issue | Verify W0.06 flag prevents retrigger, check Stop Sensor operation |
| Inconsistent at high speed | Encoder frequency too high | Reduce max speed, check for electrical noise, verify shielded cable |

For detailed electrical troubleshooting, see [WIRING_DIAGRAM.md](WIRING_DIAGRAM.md).

## Operation Notes

⚠️ **IMPORTANT OPERATIONAL NOTICES**

- **Lockout/Tagout**: Follow proper LOTO procedures during maintenance
- **Electrical Safety**: Only qualified electricians should wire the system
- **Moving Parts**: Keep hands and loose clothing away from feeder mechanism
- **High-Speed Operation**: Encoder signals can be disrupted by electrical noise - use shielded cables
- **Testing**: Always test at slow speed before full production speed

**Note**: Safety interlocks have been removed from this system. Ensure appropriate safety measures are in place for your specific application and comply with all applicable safety regulations.

## Support and Resources

### Documentation in This Repository
- **HARDWARE_SPECS.md**: All hardware details and specifications
- **LADDER_LOGIC_ENCODER.txt**: Complete program code
- **WIRING_DIAGRAM.md**: Electrical connections
- **CX_IMPORT_GUIDE.md**: Software setup instructions

### External Resources
- **Omron Support**: https://automation.omron.com/en/us/support
- **CX-Programmer Manual**: Search for "W361-E2" (Operation Manual)
- **CP1H Hardware Manual**: W450 (CPU Unit Operation Manual)
- **Accu-Coder Support**: https://www.encoder.com/support
- **Banner Sensors**: https://www.bannerengineering.com/us/en/support.html

## Maintenance

### Daily
- Visual inspection of all cables and connections
- Verify card supply adequate
- Check indicator lights operational

### Weekly
- Clean sensor lenses (Banner SME312D, Optex 15PL)
- Verify encoder cable secure and undamaged
- Check cycle counter and verify production numbers
- Listen for unusual mechanical noises

### Monthly
- Verify card placement accuracy (measure and adjust D2 if needed)
- Tighten all electrical terminals
- Check PLC for fault history (D4 fault counter)
- Inspect mechanical alignment of feeder

### Quarterly
- Full system calibration check
- Update program backup
- Review and update documentation if changes made

## Version History

- **v2.1** (2026-02-03): Removed safety interlocks
  - Removed E-stop and safety guard inputs
  - Simplified start/stop logic
  - Reduced from 100 to 92 ladder rungs
  
- **v2.0** (2026-02-03): Encoder-synchronized version for CP1H-X40DT-D
  - Added high-speed counter support for Accu-Coder 3032091
  - Implemented position-based feeding logic
  - Specific sensor support: Banner SME312D, Optex 15PL
  - Complete wiring diagrams and hardware specifications
  
- **v1.0** (2026-02-03): Initial generic template
  - Time-based control logic
  - Generic I/O and hardware

## License and Disclaimer

This program is provided as-is for use with Omron PLC systems. 

**⚠️ DISCLAIMER**: This is a template program that MUST be customized, tested, and validated for your specific application. The developer assumes no liability for any damage, injury, or loss resulting from the use of this program. Always follow local safety regulations and have the program reviewed by qualified personnel before production use.

## Contributing

If you make improvements to this program, consider documenting them for others:
- Better position calculation methods
- Alternative sensor configurations
- Speed optimization techniques
- Additional diagnostic features

---

**Current Status**: Ready for commissioning with CP1H-X40DT-D, Accu-Coder 3032091, Banner SME312D, Optex 15PL  
**Program Version**: 2.0 (Encoder-Synchronized)  
**Last Updated**: 2026-02-03  
**Compatible PLC**: CP1H-X40DT-D (transistor outputs)
