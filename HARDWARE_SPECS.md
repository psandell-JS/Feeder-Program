# Card Feeder PLC Program - Hardware Specifications
## Omron CP1H-X40DT-D Configuration

### PLC Specifications
**Model**: CP1H-X40DT-D
- **CPU**: CP1H Series
- **I/O Configuration**: 24 DC inputs, 16 transistor outputs
- **High-Speed Counters**: 4 channels (up to 100kHz)
- **Power Supply**: 24VDC
- **Program Memory**: 20K steps
- **Data Memory**: 32K words

### System Overview
This is an **encoder-synchronized registration system** that feeds cards onto moving paper with precise positioning. The encoder tracks paper position, and the card is fed at the exact moment to achieve accurate placement.

---

## Hardware Components

### Input Devices

#### High-Speed Counter Input (Encoder)
| Terminal | Symbol | Device | Description |
|----------|--------|--------|-------------|
| 0.00 (A0) | ENCODER_A | Accu-Coder 3032091 | Encoder Phase A (quadrature) |
| 0.01 (B0) | ENCODER_B | Accu-Coder 3032091 | Encoder Phase B (quadrature) |
| 0.02 (Z0) | ENCODER_Z | Accu-Coder 3032091 | Encoder Index/Zero pulse (optional) |

**Encoder Specifications (Accu-Coder 3032091)**:
- Type: Incremental rotary encoder
- Output: Push-pull (NPN/PNP compatible)
- Pulses per revolution: 2000 PPR (check your specific model)
- Max frequency: Up to 100kHz
- Wiring: A, B, Z phases + Power/Ground

#### Standard Digital Inputs
| Address | Symbol | Device | Description |
|---------|--------|--------|-------------|
| 0.03 | START_SENSOR | Banner SME312D | Paper detection (triggers feed sequence) |
| 0.04 | STOP_SENSOR | Optex 15PL | Card at feeder tip (ready position) |
| 0.07 | FEEDER_HOME | Proximity Switch | Feeder in home position |
| 0.08 | START_BTN | Push Button | Manual start button |
| 0.09 | STOP_BTN | Push Button | Stop button |
| 0.10 | MODE_AUTO | Selector Switch | Auto mode (ON) / Manual mode (OFF) |
| 0.11 | RESET_BTN | Push Button | Fault reset button |

**Banner SME312D Specifications**:
- Type: Through-beam sensor
- Detection range: Up to 12 feet
- Output: NPN/PNP solid-state
- Response time: <1ms

**Optex 15PL Specifications**:
- Type: Photoelectric sensor
- Detection range: Depends on target reflectivity
- Output: NPN/PNP relay output
- Used to detect card at feeder tip

### Output Devices

| Address | Symbol | Device Type | Description |
|---------|--------|-------------|-------------|
| 100.00 | FEEDER_MOTOR | 24VDC Motor Driver | Feeder motor enable |
| 100.01 | FEEDER_FWD | Solenoid Valve | Feed card forward |
| 100.02 | FEEDER_BACK | Solenoid Valve | Retract feeder (if needed) |
| 100.03 | VACUUM_ON | Solenoid Valve | Card vacuum/hold (if used) |
| 100.04 | RUN_INDICATOR | Green LED/Light | System running indicator |
| 100.05 | FAULT_INDICATOR | Red LED/Light | Fault/alarm indicator |
| 100.06 | READY_INDICATOR | Yellow LED/Light | Ready to feed indicator |
| 100.07 | CARD_FEED_ACTIVE | LED/Indicator | Card feeding in progress |

### Internal Memory Allocation

#### Work Bits (Relay Area)
| Address | Symbol | Description |
|---------|--------|-------------|
| W0.00 | SYSTEM_RUN | System running flag |
| W0.02 | FAULT_ACTIVE | Fault condition active |
| W0.03 | CARD_READY | Card at tip, ready to feed |
| W0.04 | PAPER_DETECTED | Paper detected by start sensor |
| W0.05 | FEED_TRIGGER | Trigger to feed card now |
| W0.06 | FEED_IN_PROGRESS | Card feeding cycle active |
| W0.07 | ENCODER_RESET_REQ | Request to reset encoder |
| W0.08 | POSITION_REACHED | Target position reached |
| W0.09 | PREP_CARD_CYCLE | Prepare next card cycle |

#### Data Memory Words (DM Area)
| Address | Symbol | Description | Default |
|---------|--------|-------------|---------|
| D0 | ENCODER_CURRENT | Current encoder count | 0 |
| D1 | TARGET_POSITION | Target position to feed card | 1000 |
| D2 | FEED_DELAY_COUNTS | Encoder counts from start sensor to feed point | 500 |
| D3 | CYCLE_COUNTER | Total cycles completed | 0 |
| D4 | FAULT_COUNTER | Total faults occurred | 0 |
| D5 | ENCODER_PPR | Encoder pulses per revolution | 2000 |
| D6 | FEED_POSITION_CALC | Calculated feed position | 0 |
| D7 | LAST_FEED_POSITION | Last feed position for diagnostics | 0 |

#### High-Speed Counter
| Counter | Symbol | Mode | Description |
|---------|--------|------|-------------|
| C000 | ENCODER_COUNTER | 2-Phase (Mode 2) | Tracks encoder position |

**HSC Configuration**:
- Mode: 2-phase x4 (quadrature with 4x multiplication)
- Range: -2,147,483,648 to +2,147,483,647 (32-bit signed)
- Inputs: 0.00 (A phase), 0.01 (B phase), 0.02 (Z phase optional)
- Reset: Software controlled via ladder logic

#### Timers
| Timer | Symbol | Preset | Description |
|-------|--------|--------|-------------|
| TIM000 | FEED_ADVANCE_TIMER | 2.0s | Time to feed card forward |
| TIM001 | CARD_PREP_TIMER | 3.0s | Time to prepare next card to tip |
| TIM002 | TIMEOUT_TIMER | 10.0s | Cycle timeout watchdog |
| TIM003 | FAULT_RESET_DELAY | 3.0s | Fault reset hold time |
| TIM004 | DEBOUNCE_TIMER | 0.1s | Sensor debounce |

---

## Encoder Setup in CX-Programmer

### Step 1: Configure High-Speed Counter Mode
1. Open **PLC Settings** in CX-Programmer
2. Navigate to **High-Speed Counter Settings**
3. Configure Counter 0 (C000):
   - **Mode**: Mode 2 (2-phase bi-directional)
   - **Input A**: 0.00
   - **Input B**: 0.01
   - **Z-Reset**: 0.02 (optional - for index pulse reset)
   - **Count Direction**: Up/Down based on rotation
   - **Maximum Frequency**: 100kHz

### Step 2: Input Filter Settings
- Set input filter for 0.00, 0.01, 0.02 to **minimum** (or OFF) for high-speed counting
- Standard inputs (0.03-0.11) can use default filter (typically 8ms)

### Step 3: Encoder Wiring
```
Accu-Coder 3032091 to CP1H-X40DT-D:
  Encoder A Phase  →  Terminal 0.00 (A0)
  Encoder B Phase  →  Terminal 0.01 (B0)
  Encoder Z Phase  →  Terminal 0.02 (Z0) [optional]
  Encoder +V       →  24VDC Power Supply
  Encoder Ground   →  Common Ground
  Shield           →  Earth Ground
```

**Important**: Use shielded twisted-pair cable for encoder signals to minimize noise.

---

## Operation Sequence

### Phase 1: Card Preparation (Automatic)
1. System checks if card is at tip (STOP_SENSOR = ON)
2. If no card detected, system feeds card forward
3. When STOP_SENSOR detects card, feeding stops
4. CARD_READY flag is set
5. Green "Ready" light illuminates

### Phase 2: Paper Detection and Position Tracking
1. Encoder continuously counts as paper/base moves
2. START_SENSOR (Banner SME312D) monitors for incoming paper
3. When paper detected:
   - Log current encoder position
   - Calculate target feed position (current + FEED_DELAY_COUNTS)
   - Set PAPER_DETECTED flag

### Phase 3: Position-Based Feeding
1. Encoder continues counting
2. Compare ENCODER_CURRENT to TARGET_POSITION
3. When ENCODER_CURRENT ≥ TARGET_POSITION:
   - Activate FEED_TRIGGER
   - Start feed sequence
4. Card feeds forward onto paper
5. FEED_ADVANCE_TIMER ensures card fully feeds (2 seconds)

### Phase 4: Cycle Completion
1. Feed complete, FEED_IN_PROGRESS resets
2. Increment CYCLE_COUNTER
3. System returns to Phase 1 (prepare next card)
4. Encoder continues counting for next cycle

---

## Position Calculation

### Formula for Feed Position:
```
TARGET_POSITION = START_SENSOR_POSITION + FEED_DELAY_COUNTS
```

### Determining FEED_DELAY_COUNTS:
1. **Measure physical distance** from start sensor to card placement point
2. **Calculate encoder counts needed**:
   ```
   FEED_DELAY_COUNTS = (Distance in mm) × (Encoder PPR) / (Wheel Circumference in mm)
   ```
3. **Example**:
   - Distance from sensor to feed point: 250mm
   - Encoder mounted on 100mm diameter wheel (circumference = 314mm)
   - Encoder PPR: 2000
   - FEED_DELAY_COUNTS = 250 × 2000 / 314 = **1592 counts**

### Fine-Tuning:
- Adjust FEED_DELAY_COUNTS during commissioning
- Use data register D2 to easily modify without reprogramming
- Test at different speeds to ensure accuracy

---

## System Protection Features

1. **Timeout Protection**: 10-second watchdog on each cycle
2. **Encoder Monitoring**: Detect encoder failure (no counts when expected)
3. **Card Jam Detection**: STOP_SENSOR timeout during card prep
4. **Single Card Enforcement**: Only one feed per trigger

---

## Fault Conditions

| Fault Code | Description | Recovery |
|------------|-------------|----------|
| F03 | Cycle timeout | Check mechanism, press Reset |
| F04 | Card not reaching tip | Check card supply, press Reset |
| F05 | Encoder error | Check encoder wiring, press Reset |
| F06 | Start sensor fault | Check sensor alignment, press Reset |

---

## Operation Modes

### Manual Mode (MODE_AUTO = OFF)
- Single cycle per START_BTN press
- Operator must press start for each card
- Used for testing and setup

### Auto Mode (MODE_AUTO = ON)
- Continuous operation
- Automatically prepares next card after each cycle
- Feeds card each time paper detected
- Operator presses STOP_BTN to end

---

## Calibration Procedure

### Initial Setup:
1. Mount encoder on base/paper drive mechanism
2. Verify encoder direction (should count up as paper moves forward)
3. Position start sensor at desired detection point
4. Position stop sensor at card tip location
5. Measure distance from start sensor to card placement point

### Calibration Steps:
1. **Test Encoder**:
   - Run base/paper mechanism
   - Monitor encoder count (D0)
   - Verify counts increase smoothly
   - Verify no count errors at speed

2. **Test Start Sensor**:
   - Run paper through
   - Verify START_SENSOR triggers reliably
   - Check for false triggers

3. **Calibrate Feed Delay**:
   - Set FEED_DELAY_COUNTS to calculated value
   - Run test cycles at slow speed
   - Measure actual card placement position
   - Adjust D2 (FEED_DELAY_COUNTS) as needed
   - Repeat until accurate

4. **Test at Production Speed**:
   - Run multiple cycles at full speed
   - Verify consistent placement
   - Check for timing errors
   - Fine-tune if needed

---

## Maintenance Notes

- **Daily**: Check encoder cable connections, verify clean sensor lenses
- **Weekly**: Verify card placement accuracy, check cycle counter
- **Monthly**: Inspect mechanical alignment, verify encoder counts
- **Quarterly**: Full system calibration check

---

## Troubleshooting

**Problem**: Card feeds too early or too late
- **Solution**: Adjust D2 (FEED_DELAY_COUNTS) value

**Problem**: Encoder count not changing
- **Solution**: Check encoder power, verify A/B phase wiring, check cable shield

**Problem**: Multiple cards feeding
- **Solution**: Verify FEED_IN_PROGRESS logic prevents re-trigger, check stop sensor

**Problem**: Start sensor not detecting paper
- **Solution**: Check sensor alignment, clean sensor lens, verify power supply

**Problem**: Inconsistent feeding at high speed
- **Solution**: Verify encoder frequency within 100kHz limit, check for electrical noise

---

## Wiring Diagram Notes

### Power Supply
- 24VDC regulated supply for PLC and sensors
- Separate supply for motor/solenoids (if high current)
- Proper grounding essential for encoder signals

### Sensor Outputs
- Banner SME312D: Configure as PNP if PLC inputs are source
- Optex 15PL: Verify output type matches PLC inputs
- Use separate common for encoder signals

### Output Loads
- Transistor outputs rated 0.3A each
- Use external relay for loads >0.3A
- Snubber diodes for inductive loads

---

**Document Version**: 2.0 - Customized for CP1H-X40DT-D
**Date**: 2026-02-03
**Next Review**: After commissioning tests
