# Troubleshooting Guide
## CP1H-X40DT-D Encoder-Synchronized Card Feeder

Quick reference for diagnosing and fixing common issues.

---

## Quick Diagnostic Checklist

**Before troubleshooting, check these first:**
- [ ] Is power on? (24VDC supply and PLC powered)
- [ ] Is card loaded? (Stop Sensor should see card at tip)
- [ ] Is PLC in RUN mode? (not PROGRAM or STOP)
- [ ] Check indicator lights for status

---

## Status Indicator Meanings

| Light Color | Output | Meaning | Action |
|------------|--------|---------|--------|
| Green | 100.04 | System running, no faults | Normal operation |
| Red | 100.05 | Fault condition active | Check fault, press Reset |
| Yellow | 100.06 | Card ready at tip, waiting | Normal - ready to feed |
| White | 100.07 | Card feeding in progress | Normal - feeding active |

---

## Problem Categories

### [1. Encoder Issues](#1-encoder-issues)
### [2. Sensor Issues](#2-sensor-issues)
### [3. Position/Timing Issues](#3-positiontiming-issues)
### [4. Feed Mechanism Issues](#4-feed-mechanism-issues)
### [5. Fault Issues](#5-fault-issues)
### [6. PLC Communication Issues](#6-plc-communication-issues)

---

## 1. Encoder Issues

### Problem: Encoder not counting (D0 stays at 0)

**Symptoms:**
- D0 (ENCODER_CURRENT) doesn't change
- Paper moves but no counts
- System may timeout (F05 fault)

**Possible Causes & Solutions:**

1. **No power to encoder**
   - Check: Measure +V and 0V at encoder terminals
   - Should read: 24VDC ±10%
   - Fix: Check 24V power supply, verify wiring

2. **Encoder wiring disconnected**
   - Check: Continuity from encoder to PLC terminals 0.00, 0.01, 0.02
   - Visual: Look for broken wires or loose terminals
   - Fix: Re-terminate or replace cable

3. **High-speed counter not configured**
   - Check: PLC Settings → High-Speed Counter
   - Should be: Mode 2, inputs 0.00, 0.01, 0.02
   - Fix: Reconfigure and download settings to PLC

4. **Encoder cable shield causing noise**
   - Check: Shield should be grounded at PLC end ONLY
   - Look for: Ground loops if grounded at both ends
   - Fix: Disconnect shield at encoder end, ground at PLC only

5. **Encoder mechanical failure**
   - Check: Rotate encoder shaft by hand, should turn freely
   - Listen: For grinding or clicking sounds
   - Fix: Replace encoder

**Diagnostic Steps:**
```
1. Connect to PLC with CX-Programmer
2. Go online and monitor D0 (ENCODER_CURRENT)
3. Manually rotate encoder shaft slowly
4. D0 should increment/decrement
5. If not, check high-speed counter configuration
6. Force inputs 0.00 and 0.01 alternately - does counter change?
```

---

### Problem: Encoder counting backwards

**Symptoms:**
- D0 decreases when paper moves forward
- D0 increases when paper moves backward
- Feeding occurs at wrong position

**Cause:** A and B phases are swapped

**Solution:**
1. Power off system
2. Swap wires on terminals 0.00 and 0.01
3. A phase should be on 0.00, B phase on 0.01
4. Power on and test again

---

### Problem: Encoder counts are erratic or jumping

**Symptoms:**
- D0 jumps by large amounts randomly
- Counts are not smooth
- May cause false feed triggers

**Possible Causes & Solutions:**

1. **Electrical noise on encoder signals**
   - Check: Is encoder cable near power cables or motors?
   - Fix: Route encoder cable away from noise sources (minimum 6" separation)
   - Fix: Verify using SHIELDED twisted-pair cable
   - Fix: Add ferrite core to encoder cable

2. **Encoder cable too long**
   - Check: Cable length (should be <50m for 100kHz)
   - Fix: Shorten cable or use encoder with line driver output

3. **Loose encoder coupling**
   - Check: Mechanical connection between encoder and drive shaft
   - Fix: Tighten coupling, verify no slippage

4. **Input filter set too high**
   - Check: Input filter time in PLC settings
   - Should be: Minimum or OFF for 0.00, 0.01, 0.02
   - Fix: Reduce input filter time in PLC configuration

**Diagnostic Steps:**
```
1. Monitor D0 at very slow speed
2. Count should increment smoothly by 1
3. If jumping: Check cable routing and shielding
4. If inconsistent: Check mechanical coupling
5. Use oscilloscope to view A/B phase signals if available
```

---

## 2. Sensor Issues

### Problem: Start Sensor (Banner SME312D) not detecting paper

**Symptoms:**
- Input 0.03 stays OFF when paper passes
- System doesn't trigger feed
- W0.04 (PAPER_DETECTED) never sets

**Possible Causes & Solutions:**

1. **Sensor not powered**
   - Check: 24VDC at sensor terminals
   - Check: Sensor indicator LED (should be lit when powered)
   - Fix: Verify wiring, check power supply

2. **Sensor beam misaligned**
   - Check: For through-beam sensors, emitter and receiver aligned
   - Check: Paper path passes through beam
   - Fix: Realign sensor per Banner manual, test with paper

3. **Sensor output type mismatch**
   - Check: Sensor output type (NPN or PNP)
   - Check: PLC input configuration (source or sink)
   - Fix: Change sensor output setting or use appropriate wiring

4. **Dirty sensor lens**
   - Check: Clean lens with soft cloth
   - Look for: Dust, oil, paper dust buildup
   - Fix: Clean lens, establish regular cleaning schedule

5. **Paper not blocking beam**
   - Check: Paper actually passes through sensor beam
   - Check: Paper opacity sufficient for detection
   - Fix: Adjust sensor position or sensitivity

**Diagnostic Steps:**
```
1. Monitor input 0.03 in CX-Programmer
2. Manually block sensor beam with hand/card
3. Input should turn ON
4. If not, check sensor power and wiring
5. Check sensor output with multimeter (should switch 0-24V)
```

---

### Problem: Stop Sensor (Optex 15PL) not detecting card at tip

**Symptoms:**
- Input 0.04 stays OFF when card at tip
- W0.03 (CARD_READY) never sets
- Card prep cycle times out (F04 fault)

**Possible Causes & Solutions:**

1. **Sensor misaligned**
   - Check: Sensor aimed at card tip position
   - Check: Card actually reaches sensor detection zone
   - Fix: Adjust sensor angle/position, test with card

2. **Card not reflective enough**
   - Check: Card surface finish (glossy vs. matte)
   - Check: Sensor sensitivity setting
   - Fix: Adjust sensor sensitivity, teach sensor with actual card

3. **Sensor distance too far**
   - Check: Distance from sensor to card
   - Should be: Within sensor's rated detection range
   - Fix: Move sensor closer or use different sensor

4. **Sensor teach/set mode not configured**
   - Check: Sensor may need to be "taught" on target
   - Refer to: Optex 15PL manual for teach procedure
   - Fix: Perform teach procedure with actual card

**Diagnostic Steps:**
```
1. Monitor input 0.04 in CX-Programmer
2. Place card at tip position manually
3. Input should turn ON
4. Remove card, input should turn OFF
5. If not working, check sensor alignment and power
```

---

## 3. Position/Timing Issues

### Problem: Card feeds too early (before paper reaches position)

**Symptoms:**
- Card lands ahead of intended position on paper
- Placement inconsistent or too far forward

**Cause:** FEED_DELAY_COUNTS (D2) value too small

**Solution:**
1. Calculate how far early (in mm): __________ mm
2. Calculate adjustment needed:
   ```
   Additional_Counts = (Error_mm × Encoder_PPR) / Wheel_Circumference_mm
   ```
3. Increase D2 by this amount
4. Update in ladder logic Rung 96 or change D2 directly via HMI
5. Re-test at slow speed
6. Fine-tune in increments of 50-100 counts

**Example:**
```
Card is 20mm too early
Encoder: 2000 PPR on 100mm wheel (circumference 314mm)
Additional counts needed: (20 × 2000) / 314 = 127 counts
If D2 was 1500, increase to 1627
```

---

### Problem: Card feeds too late (after paper passes position)

**Symptoms:**
- Card lands behind intended position on paper
- Placement inconsistent or too far back

**Cause:** FEED_DELAY_COUNTS (D2) value too large

**Solution:**
1. Calculate how far late (in mm): __________ mm
2. Calculate adjustment needed (same formula as above)
3. Decrease D2 by this amount
4. Re-test and fine-tune

---

### Problem: Feeding inconsistent at high speed but OK at low speed

**Symptoms:**
- Placement accurate at slow speed
- Placement varies at production speed
- May see occasional timing faults

**Possible Causes & Solutions:**

1. **PLC scan time too slow**
   - Check: Scan time in PLC (should be <10ms)
   - Fix: Simplify program, remove unnecessary rungs
   - Fix: Use faster PLC model if needed

2. **Encoder frequency too high**
   - Check: Maximum encoder frequency at top speed
   - Should be: <100kHz for CP1H
   - Fix: Reduce max speed or use lower PPR encoder

3. **Mechanical slippage**
   - Check: Encoder coupling tight at high speed?
   - Check: Belt slippage on drive system
   - Fix: Tighten couplings, replace worn belts

4. **Speed-dependent timing**
   - Issue: Different belt speeds cause different delays
   - Fix: May need speed compensation algorithm
   - Advanced: Use encoder frequency to calculate dynamic delay

---

### Problem: Card doesn't feed at all (position never reached)

**Symptoms:**
- Paper detected (W0.04 = ON)
- Encoder counting (D0 increasing)
- But D0 never reaches D6 (target position)
- Eventually times out

**Possible Causes & Solutions:**

1. **Target position calculated wrong**
   - Check: Monitor D100 (start position), D2 (delay), D6 (target)
   - Check: D6 = D100 + D2 (should be addition, not subtraction)
   - Fix: Verify Rung 38 ADD instruction correct

2. **Encoder reset occurring**
   - Check: Is encoder being reset during cycle?
   - Check: Z-pulse input (0.02) triggering reset?
   - Fix: Disable Z-pulse reset or use only when stopped

3. **Paper base stopped moving**
   - Check: Is D0 still incrementing after start sensor?
   - Fix: Verify paper drive system running continuously

4. **D2 value set to 0 or negative**
   - Check: D2 should be positive value (e.g., 500-2000)
   - Fix: Set D2 to calculated value

**Diagnostic Steps:**
```
Monitor these values during cycle:
- D100: Start position when paper detected
- D2: Feed delay counts (should be 500-2000 typically)
- D6: Target position (D100 + D2)
- D0: Current encoder count (should eventually reach D6)

If D0 never reaches D6:
- Check if encoder actually counting up
- Verify D6 calculation is correct
- Ensure paper base continues moving
```

---

## 4. Feed Mechanism Issues

### Problem: Card doesn't reach tip during prep cycle

**Symptoms:**
- Motor runs but card doesn't move
- TIM001 (3 second timer) expires
- F04 fault occurs
- Stop sensor never sees card

**Possible Causes & Solutions:**

1. **Card jammed in feeder**
   - Check: Visual inspection for jammed cards
   - Check: Card path for obstructions
   - Fix: Clear jam, check card alignment

2. **Motor not running**
   - Check: Output 100.00 turning ON during prep?
   - Check: Motor driver receiving signal
   - Fix: Check motor driver wiring and enable signal

3. **Forward solenoid not activating**
   - Check: Output 100.01 turning ON during prep?
   - Check: Solenoid energizing (listen for click)
   - Fix: Check solenoid wiring, test solenoid directly

4. **Mechanical issue**
   - Check: Can you manually push card to tip?
   - Check: Feeder mechanism for binding or damage
   - Fix: Repair or lubricate mechanism

5. **No cards in hopper**
   - Check: Card supply loaded
   - Fix: Reload cards

**Diagnostic Steps:**
```
1. Monitor W0.09 (PREP_CARD_CYCLE) - should be ON
2. Monitor 100.00 (FEEDER_MOTOR) - should be ON
3. Monitor 100.01 (FEEDER_FWD) - should be ON
4. Listen for motor and solenoid operation
5. Watch card physically - is it moving?
```

---

### Problem: Multiple cards feeding

**Symptoms:**
- More than one card feeds per cycle
- Cards sticking together
- Feed mechanism grabbing multiple cards

**Possible Causes & Solutions:**

1. **Mechanical issue - cards sticking**
   - Check: Cards not separated properly in hopper
   - Check: Static electricity causing cards to cling
   - Fix: Use anti-static treatment, separate cards better
   - Fix: Add card separation mechanism

2. **Logic issue - double triggering**
   - Check: Monitor W0.06 (FEED_IN_PROGRESS)
   - Should: Block re-trigger while feeding
   - Check: Rung 98 prevents W0.05 from setting during feed
   - Fix: Verify logic prevents double-feed

3. **Stop sensor detecting multiple cards**
   - Check: Stop sensor only sees one card at a time
   - Fix: Adjust sensor sensitivity or position

---

### Problem: Card feeds but doesn't place on paper properly

**Symptoms:**
- Card feeds at right time
- But card doesn't release or lands wrong

**Possible Causes & Solutions:**

1. **Vacuum not releasing** (if using vacuum)
   - Check: Output 100.03 turning OFF after placement
   - Check: Vacuum valve de-energizing
   - Fix: Check valve operation, verify timer settings

2. **Feed advance time too short**
   - Check: TIM000 preset (should be 2.0 seconds, or 15#20)
   - Fix: Increase timer preset if card not fully placing

3. **Mechanical alignment**
   - Check: Feeder tip alignment with paper path
   - Fix: Adjust mechanical positioning

---

## 5. Fault Issues

### Problem: System won't start (stays in fault)

**Symptoms:**
- Red fault light ON
- Can't start system
- W0.02 (FAULT_ACTIVE) = ON

**Solution:**
1. Identify fault cause:
   - Previous timeout? Clear cause
   - Check for error conditions
2. Press and HOLD Reset button for 3 seconds (TIM003)
3. Wait for fault to clear (Red light OFF)
4. Verify ready (Yellow ready light should come ON)
5. Press Start button

---

### Problem: System faults immediately when started

**Possible Causes:**

1. **Encoder error at startup**
   - Check: Is encoder connected and powered?
   - Check: Does D0 respond to encoder rotation?
   - Fix: Verify encoder wiring and configuration

2. **Card not at tip**
   - System may require card ready before starting
   - Check: Input 0.04 (stop sensor)
   - Fix: Manually feed card to tip

---

### Problem: Timeout fault (F03) occurs during cycle

**Symptoms:**
- System runs for 10 seconds then faults
- TIM002 expires
- Red fault light ON

**Possible Causes:**

1. **Paper stopped moving**
   - Check: Is paper drive running?
   - Fix: Verify paper feed mechanism operating

2. **Encoder not counting**
   - Check: D0 incrementing during cycle?
   - Fix: See encoder troubleshooting section

3. **Target position unreachable**
   - Check: D6 value reasonable?
   - Check: D6 not set to impossibly high value
   - Fix: Verify D2 setting, check calculation

---

## 6. PLC Communication Issues

### Problem: Cannot connect to PLC with CX-Programmer

**Possible Causes & Solutions:**

1. **Wrong COM port selected**
   - Check: Device Manager for USB/serial port number
   - Fix: Select correct COM port in CX-Programmer

2. **Wrong cable type**
   - Check: Using Omron programming cable (USB or serial)
   - Fix: Use proper cable (CS1W-CIF31 or USB-CIF02)

3. **PLC not powered**
   - Check: PLC power LED ON
   - Fix: Check 24VDC supply

4. **Wrong PLC model selected**
   - Check: CP1H-X40DT-D selected in connection settings
   - Fix: Select correct PLC model

5. **Baud rate mismatch** (serial only)
   - Check: Baud rate in PLC settings
   - Default: 9600 bps
   - Fix: Match CX-Programmer baud rate to PLC setting

---

## Emergency Procedures

### Stopping the System
1. **Press STOP button**
2. System will complete current cycle if safe
3. Or press and hold STOP for immediate stop
4. Identify and fix any issues
5. Press Start to resume

### Clearing a Card Jam
1. Press Stop button
2. Wait for all motion to cease
3. **Lockout/Tagout per your facility procedures**
4. Manually remove jammed card
5. Check for mechanism damage
6. Press Start to resume

### Power Loss Recovery
1. When power restored, PLC will restart
2. Encoder count (D0) will be zero
3. System will be in stopped state
4. Check for cards in mechanism
5. Press Reset to clear any faults
6. Follow normal startup procedure

---

## Preventive Maintenance

### Daily
- [ ] Visual inspection of all cables
- [ ] Check card supply
- [ ] Clean sensor lenses

### Weekly
- [ ] Check encoder cable condition
- [ ] Verify grounding connections
- [ ] Review cycle counter (D3)
- [ ] Review fault counter (D4)

### Monthly
- [ ] Calibration check (measure placement)
- [ ] Tighten all terminals
- [ ] Inspect mechanical components
- [ ] Download and backup PLC program

### Quarterly
- [ ] Full system recalibration
- [ ] Replace worn components
- [ ] Update documentation
- [ ] Review and update training materials

---

## Diagnostic Data Locations

**Monitor these addresses for troubleshooting:**

| Address | Name | Normal Range | What It Tells You |
|---------|------|--------------|-------------------|
| D0 | ENCODER_CURRENT | 0 to millions | Current encoder count (should increment smoothly) |
| D2 | FEED_DELAY_COUNTS | 500-2000 typical | Feed delay setting (adjust for calibration) |
| D3 | CYCLE_COUNTER | 0-9999 | Total cycles completed |
| D4 | FAULT_COUNTER | 0-9999 | Total faults occurred |
| D6 | TARGET_POSITION | Varies | Calculated feed position |
| D100 | START_POSITION | Varies | Encoder count when paper detected |
| W0.00 | SYSTEM_RUN | ON when running | System running flag |
| W0.02 | FAULT_ACTIVE | Should be OFF | Fault condition active |
| W0.03 | CARD_READY | Should be ON | Card at tip, ready to feed |
| W0.04 | PAPER_DETECTED | Toggles | Paper detected by start sensor |
| W0.06 | FEED_IN_PROGRESS | Toggles | Currently feeding card |

---

## Getting Help

If you've tried the troubleshooting steps and still have issues:

1. **Gather Information:**
   - What is the exact symptom?
   - When did it start?
   - What changed recently?
   - Current values of D0, D2, D3, D4, D6
   - Status of W0.00 through W0.06
   - Any fault lights or codes?

2. **Check Documentation:**
   - Review HARDWARE_SPECS.md
   - Review LADDER_LOGIC_ENCODER.txt
   - Review WIRING_DIAGRAM.md

3. **Contact Support:**
   - Omron Technical Support: 1-800-55-OMRON (US)
   - Encoder Support: Accu-Coder/encoder.com
   - Sensor Support: Banner Engineering, Optex

4. **Consider Professional Service:**
   - Local Omron distributor
   - Automation system integrator
   - Industrial controls electrician

---

**Troubleshooting Guide Version**: 2.0  
**Date**: 2026-02-03  
**System**: CP1H-X40DT-D Encoder-Synchronized Card Feeder
