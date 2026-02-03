# Commissioning Checklist
## CP1H-X40DT-D Card Feeder System

Use this checklist during installation and commissioning of the encoder-synchronized card feeder.

---

## Pre-Installation Checklist

### Documentation Review
- [ ] Read HARDWARE_SPECS.md completely
- [ ] Review LADDER_LOGIC_ENCODER.txt program structure
- [ ] Study WIRING_DIAGRAM.md for all connections
- [ ] Understand position calculation formula
- [ ] Review safety requirements

### Hardware Verification
- [ ] CP1H-X40DT-D PLC received and inspected
- [ ] Accu-Coder 3032091 encoder available
- [ ] Banner SME312D start sensor available
- [ ] Optex 15PL stop sensor available
- [ ] 24VDC power supply (5A minimum) available
- [ ] All cables and wiring materials ready
- [ ] Shielded twisted-pair cable for encoder
- [ ] Control panel with E-stop, Start, Stop, Reset buttons
- [ ] Motor driver and solenoid valves ready
- [ ] Indicator lights (Green, Red, Yellow, White)

---

## Installation Phase

### Mechanical Installation
- [ ] PLC mounted in clean, ventilated enclosure
- [ ] Encoder mounted on paper drive mechanism (base)
- [ ] Encoder coupling aligned and secure
- [ ] Start sensor (Banner SME312D) mounted at detection point
- [ ] Stop sensor (Optex 15PL) aligned to detect card at tip
- [ ] Feeder mechanism assembled and tested manually
- [ ] Motor and solenoids mounted
- [ ] All mechanical guards installed

### Electrical Installation
- [ ] AC power wiring completed (L, N, Ground)
- [ ] 24VDC power supply connected
- [ ] Encoder wired with SHIELDED cable:
  - [ ] A phase → 0.00
  - [ ] B phase → 0.01
  - [ ] Z phase → 0.02 (optional)
  - [ ] +V → 24VDC
  - [ ] 0V → Common
  - [ ] Shield grounded at PLC end only
- [ ] Start sensor (Banner) wired to 0.03
- [ ] Stop sensor (Optex) wired to 0.04
- [ ] E-stop button wired to 0.05 (NC - normally closed)
- [ ] Safety guard switch wired to 0.06
- [ ] Home sensor wired to 0.07
- [ ] Start button wired to 0.08
- [ ] Stop button wired to 0.09
- [ ] Mode selector wired to 0.10
- [ ] Reset button wired to 0.11
- [ ] Motor driver connected to 100.00
- [ ] Forward solenoid connected to 100.01
- [ ] Return solenoid connected to 100.02 (if used)
- [ ] Vacuum solenoid connected to 100.03 (if used)
- [ ] Green LED connected to 100.04
- [ ] Red LED connected to 100.05
- [ ] Yellow LED connected to 100.06
- [ ] White LED connected to 100.07
- [ ] All terminals tightened (use torque specification)
- [ ] Wire labels applied per wiring diagram

### Pre-Power Testing
- [ ] Visual inspection - all connections look good
- [ ] Continuity test on all wires
- [ ] No shorts between +24V and 0V (measure with multimeter)
- [ ] Ground resistance < 1 ohm
- [ ] E-stop circuit verified as NC (normally closed)

---

## Software Installation Phase

### CX-Programmer Setup
- [ ] CX-Programmer installed (version 9.0+)
- [ ] USB/serial cable ready for PLC connection
- [ ] New project created in CX-Programmer
- [ ] PLC model selected: CP1H-X40DT-D
- [ ] I/O configuration set correctly (24 in / 16 out)

### High-Speed Counter Configuration
- [ ] PLC Settings → High-Speed Counter Settings opened
- [ ] Counter 0 (C000) configured:
  - [ ] Mode: Mode 2 (2-phase bi-directional)
  - [ ] Input A: 0.00
  - [ ] Input B: 0.01
  - [ ] Z-Reset: 0.02 (optional)
  - [ ] Max Frequency: 100kHz
- [ ] Input filter set to minimum for 0.00, 0.01, 0.02
- [ ] Configuration saved

### Symbol Table Creation
- [ ] Symbol table opened in CX-Programmer
- [ ] All input symbols entered (ENCODER_A, ENCODER_B, START_SENSOR, etc.)
- [ ] All output symbols entered (FEEDER_MOTOR, FEEDER_FWD, etc.)
- [ ] All work bit symbols entered (SYSTEM_RUN, SAFETY_OK, etc.)
- [ ] All data register symbols entered (ENCODER_CURRENT, TARGET_POSITION, etc.)
- [ ] Symbol table saved

### Ladder Logic Entry
- [ ] Section 0: High-speed counter config (Rungs 0-5) entered
- [ ] Section 1: Safety interlocks (Rungs 6-12) entered
- [ ] Section 2: Start/stop logic (Rungs 13-20) entered
- [ ] Section 3: Card preparation (Rungs 21-35) entered
- [ ] Section 4: Paper detection & position calc (Rungs 36-50) entered
- [ ] Section 5: Card feeding cycle (Rungs 51-70) entered
- [ ] Section 6: Timeout watchdog (Rungs 71-75) entered
- [ ] Section 7: Outputs and indicators (Rungs 76-90) entered
- [ ] Section 8: Diagnostics (Rungs 91-100) entered
- [ ] All rungs have comments added
- [ ] Program compiled successfully (no errors)
- [ ] Program saved as "Card_Feeder_v2.0.cxp"
- [ ] Backup copy saved

---

## Power-Up Testing Phase

### Initial Power-Up (PLC in STOP Mode)
- [ ] 24VDC power supply turned on
- [ ] Voltage measured: 23-25VDC ✓
- [ ] PLC powered on (check RUN/ERR LEDs)
- [ ] No fault LEDs on PLC
- [ ] Encoder powered (verify at encoder terminals)
- [ ] All sensors powered (check sensor indicator LEDs)

### Input Testing (PLC in STOP, Monitor Mode)
- [ ] Connect to PLC with CX-Programmer
- [ ] Enter Monitor Mode
- [ ] Manually activate each input and verify:
  - [ ] 0.00, 0.01 - Rotate encoder manually, see inputs toggle
  - [ ] 0.03 - Block/unblock Banner sensor, verify input
  - [ ] 0.04 - Block/unblock Optex sensor, verify input
  - [ ] 0.05 - Press E-stop, verify input goes OFF (NC)
  - [ ] 0.06 - Open/close guard, verify input
  - [ ] 0.07 - Trigger home sensor, verify input
  - [ ] 0.08 - Press start button, verify input
  - [ ] 0.09 - Press stop button, verify input
  - [ ] 0.10 - Toggle mode selector, verify input
  - [ ] 0.11 - Press reset button, verify input

### Output Testing (PLC in PROGRAM Mode)
- [ ] Force each output individually and verify operation:
  - [ ] 100.00 - Motor driver activates (listen for motor)
  - [ ] 100.01 - Forward solenoid activates (listen for click)
  - [ ] 100.02 - Return solenoid activates (if used)
  - [ ] 100.03 - Vacuum solenoid activates (if used)
  - [ ] 100.04 - Green LED lights
  - [ ] 100.05 - Red LED lights
  - [ ] 100.06 - Yellow LED lights
  - [ ] 100.07 - White LED lights
- [ ] Un-force all outputs

---

## Program Testing Phase

### Encoder Functionality Test (PLC in RUN Mode)
- [ ] Download program to PLC
- [ ] PLC set to RUN mode
- [ ] Open Data Trace window
- [ ] Monitor D0 (ENCODER_CURRENT)
- [ ] Manually rotate encoder forward
- [ ] Verify D0 value INCREASES
- [ ] Manually rotate encoder backward
- [ ] Verify D0 value DECREASES
- [ ] **If backwards, power off and swap A/B phase wires**
- [ ] Run paper/base at slow speed
- [ ] Verify D0 increments smoothly
- [ ] No count errors or skips observed

### Safety Interlock Test
- [ ] System in STOP mode (all outputs off)
- [ ] Press START button
- [ ] Verify system does NOT start (safety guard might be open)
- [ ] Close safety guard (0.06 = ON)
- [ ] Release E-stop (0.05 = OFF → becomes ON due to NC)
- [ ] Yellow READY light should illuminate
- [ ] Press START button again
- [ ] Green RUN light should illuminate
- [ ] Press E-STOP
- [ ] All motion should stop immediately ✓
- [ ] Red FAULT light should illuminate
- [ ] Press and hold RESET button for 3 seconds
- [ ] Fault should clear (Red light off)
- [ ] Open safety guard while running
- [ ] System should stop and fault ✓

### Card Preparation Test (Without Paper)
- [ ] Ensure card NOT at tip (Stop Sensor = OFF)
- [ ] System in Manual mode (0.10 = OFF)
- [ ] Safety OK, press START button
- [ ] Feeder motor should activate
- [ ] Card should feed forward toward tip
- [ ] When Stop Sensor detects card (0.04 = ON):
  - [ ] Feeder should stop
  - [ ] Yellow READY light should be ON
  - [ ] System prepared for feeding
- [ ] If card doesn't stop at sensor:
  - [ ] Check Stop Sensor alignment
  - [ ] Verify sensor wiring and operation

---

## Calibration Phase

### Distance Measurement
- [ ] Measure distance from Start Sensor to card placement point
- [ ] Distance measured: __________ mm (write down)
- [ ] Measure encoder wheel diameter: __________ mm
- [ ] Calculate circumference: Diameter × 3.14159 = __________ mm

### Feed Delay Calculation
```
FEED_DELAY_COUNTS = (Distance × Encoder_PPR) / Circumference

Example:
Distance: 250 mm
Encoder PPR: 2000 (Accu-Coder 3032091)
Wheel circumference: 314 mm
FEED_DELAY_COUNTS = (250 × 2000) / 314 = 1592 counts
```

- [ ] Calculated FEED_DELAY_COUNTS: __________ counts
- [ ] Update D2 register in program with this value
- [ ] Or modify Rung 96 constant to this value
- [ ] Re-compile and download program

### Low-Speed Testing
- [ ] Set paper/base to SLOW speed (25% of production speed)
- [ ] Place test paper on base
- [ ] System in Manual mode
- [ ] Card ready at tip (Yellow light ON)
- [ ] Start base/paper movement
- [ ] Paper passes Start Sensor (0.03 triggers)
- [ ] Verify D100 logs position when sensor triggers
- [ ] Verify D6 calculates target position (D6 = D100 + D2)
- [ ] Monitor D0 (current encoder count)
- [ ] When D0 ≥ D6, card should feed
- [ ] Measure where card lands on paper
- [ ] Compare to intended position
- [ ] If too early: Increase D2 by 100 counts
- [ ] If too late: Decrease D2 by 100 counts
- [ ] Repeat test until accurate

### Fine-Tuning
- [ ] Run 10 test cycles at slow speed
- [ ] Verify consistent placement (measure each)
- [ ] Adjust D2 in increments of 50 counts as needed
- [ ] Document final D2 value: __________ counts
- [ ] Update program with final value
- [ ] Save program as "Card_Feeder_v2.0_Calibrated.cxp"

### Medium-Speed Testing
- [ ] Increase base speed to 50% of production
- [ ] Run 10 test cycles
- [ ] Verify placement still accurate
- [ ] If drift observed, fine-tune D2 again
- [ ] Note any speed-dependent variations

### Production-Speed Testing
- [ ] Increase to full production speed
- [ ] Run 10 test cycles
- [ ] Verify placement accuracy maintained
- [ ] Check for timing errors or faults
- [ ] Verify encoder keeping up (no count errors)
- [ ] If inconsistent, reduce max speed or check for electrical noise

---

## Auto Mode Testing

### Continuous Operation Test
- [ ] System in AUTO mode (0.10 = ON)
- [ ] Card ready at tip
- [ ] Press START button
- [ ] Green RUN light ON
- [ ] Run continuous paper feed
- [ ] System should:
  - [ ] Feed card when paper detected
  - [ ] Automatically prepare next card
  - [ ] Continue feeding each paper
- [ ] Run for 50 cycles minimum
- [ ] Verify cycle counter (D3) increments correctly
- [ ] Monitor for any faults or errors
- [ ] Press STOP button
- [ ] System should complete current cycle and stop gracefully

### Multiple Card Prevention Test
- [ ] Manually trigger Start Sensor (0.03) rapidly
- [ ] Verify only ONE card feeds per trigger
- [ ] Verify W0.06 flag prevents re-trigger during feed
- [ ] Confirm single card enforcement working

---

## Final Validation

### Performance Verification
- [ ] Cycle time measured: __________ seconds
- [ ] Placement accuracy: within ±__________ mm
- [ ] 100 consecutive cycles completed without fault
- [ ] Production speed achieved: __________ cards/minute
- [ ] Encoder count stable (no drift after 100 cycles)

### Safety Verification
- [ ] E-stop stops all motion within __________ seconds
- [ ] Safety guard interlock prevents operation when open
- [ ] Fault recovery procedure tested and working
- [ ] All indicator lights functioning correctly
- [ ] Emergency procedures documented and posted

### Documentation
- [ ] Final D2 value documented: __________ counts
- [ ] Wiring diagram marked with actual wire numbers
- [ ] Sensor positions photographed and documented
- [ ] Calibration data recorded in maintenance log
- [ ] Operator training materials prepared
- [ ] Backup of final program saved (multiple copies)

---

## Operator Training

### Operator Trained On:
- [ ] Normal startup procedure
- [ ] Loading card supply
- [ ] Manual vs Auto mode operation
- [ ] Start/Stop button operation
- [ ] Recognizing status lights (Green/Red/Yellow/White)
- [ ] Emergency stop procedure
- [ ] Basic troubleshooting (card jam, sensor fault)
- [ ] When to call maintenance

### Maintenance Staff Trained On:
- [ ] Safety lockout/tagout procedure
- [ ] PLC programming basics (CX-Programmer)
- [ ] How to adjust D2 for calibration
- [ ] Encoder troubleshooting
- [ ] Sensor alignment and cleaning
- [ ] Wiring diagram interpretation
- [ ] Fault code meanings and recovery
- [ ] Backup/restore program procedure

---

## Acceptance Criteria

### System Accepted When:
- [ ] All checklist items above completed
- [ ] 100 consecutive cycles at production speed without fault
- [ ] Placement accuracy within specification: ±5mm (or your spec)
- [ ] Safety systems fully functional and tested
- [ ] Operators trained and signed off
- [ ] Documentation complete and organized
- [ ] Client/supervisor approval obtained

---

## Sign-Off

### Commissioning Engineer:
- Name: ________________________________
- Signature: ____________________________
- Date: ________________________________

### Client/Supervisor:
- Name: ________________________________
- Signature: ____________________________
- Date: ________________________________

### Notes:
Use this space to record any deviations from standard setup, special adjustments, or issues encountered:

________________________________________________________________

________________________________________________________________

________________________________________________________________

________________________________________________________________

---

## Post-Commissioning

### First Week Monitoring
- [ ] Daily check of cycle counter and fault counter
- [ ] Daily verification of placement accuracy
- [ ] Log any issues or adjustments needed
- [ ] Fine-tune D2 if drift observed

### One Month Review
- [ ] Review D3 (total cycles) and D4 (total faults)
- [ ] Calculate fault rate: (D4/D3) × 100 = __________ %
- [ ] Inspect encoder cable for wear
- [ ] Verify all connections still tight
- [ ] Update maintenance log

---

**Commissioning Checklist Version**: 2.0  
**Date Created**: 2026-02-03  
**System**: CP1H-X40DT-D Encoder-Synchronized Card Feeder  
**Program Version**: 2.0
