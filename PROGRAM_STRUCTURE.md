# Card Feeder PLC Program Structure
## For Omron CX-Programmer

### Program Overview
This automated card feeder program controls a system that feeds cards onto mail pieces. The program includes safety interlocks, sequencing logic, and error handling.

### System Components

#### Inputs
| Address | Symbol | Description |
|---------|--------|-------------|
| 0.00 | START_BTN | Manual start button |
| 0.01 | STOP_BTN | Stop button |
| 0.02 | E_STOP | Emergency stop |
| 0.03 | SAFETY_GUARD | Safety guard closed sensor |
| 0.04 | CARD_EMPTY_SENSOR | Card hopper empty sensor |
| 0.05 | CARD_DETECT_SENSOR | Card presence at feed position |
| 0.06 | MAIL_PRESENT_SENSOR | Mail piece present and ready |
| 0.07 | FEEDER_HOME_SENSOR | Feeder in home position |
| 0.08 | MOTOR_OVERLOAD | Feeder motor overload signal |
| 0.09 | MODE_AUTO | Auto mode selector (ON=Auto, OFF=Manual) |

#### Outputs
| Address | Symbol | Description |
|---------|--------|-------------|
| 100.00 | FEEDER_MOTOR | Feeder motor contactor |
| 100.01 | CARD_VACUUM | Card vacuum/gripper solenoid |
| 100.02 | FEEDER_FWD | Feeder forward solenoid |
| 100.03 | FEEDER_BACK | Feeder return to home solenoid |
| 100.04 | RUN_INDICATOR | Green run indicator light |
| 100.05 | FAULT_INDICATOR | Red fault indicator light |
| 100.06 | READY_INDICATOR | Yellow ready indicator light |
| 100.07 | CARD_LOW_ALARM | Card low alarm output |

#### Internal Relays (Work Bits)
| Address | Symbol | Description |
|---------|--------|-------------|
| W0.00 | SYSTEM_RUN | System running flag |
| W0.01 | CYCLE_ACTIVE | Feed cycle in progress |
| W0.02 | SAFETY_OK | All safety conditions met |
| W0.03 | FAULT_ACTIVE | Fault condition active |
| W0.04 | CARD_LOW | Card supply low warning |
| W0.05 | STEP_1 | Sequence step 1: Wait for mail |
| W0.06 | STEP_2 | Sequence step 2: Pick card |
| W0.07 | STEP_3 | Sequence step 3: Move forward |
| W0.08 | STEP_4 | Sequence step 4: Place card |
| W0.09 | STEP_5 | Sequence step 5: Return home |

#### Timers
| Timer | Symbol | Preset | Description |
|-------|--------|--------|-------------|
| TIM000 | CARD_PICK_TIMER | 1.5s | Time to pick up card |
| TIM001 | FEED_FWD_TIMER | 2.0s | Time to move forward |
| TIM002 | CARD_PLACE_TIMER | 1.0s | Time to place card |
| TIM003 | RETURN_HOME_TIMER | 2.5s | Time to return home |
| TIM004 | CYCLE_COMPLETE_TIMER | 0.5s | Cycle complete delay |
| TIM005 | FAULT_RESET_TIMER | 3.0s | Fault reset hold time |

#### Counters
| Counter | Symbol | Preset | Description |
|---------|--------|--------|-------------|
| CNT000 | CYCLE_COUNTER | 9999 | Total cycles completed |
| CNT001 | FAULT_COUNTER | 9999 | Total fault occurrences |

### Program Sections

#### Section 1: Safety Interlocks (Rungs 0-10)
- Check emergency stop
- Verify safety guard closed
- Monitor motor overload
- Calculate overall safety status

#### Section 2: System Start/Stop Logic (Rungs 11-20)
- Process start button with safety checks
- Handle stop button and e-stop
- Set/reset system run flag
- Mode selection (auto/manual)

#### Section 3: Fault Detection and Handling (Rungs 21-35)
- Card empty detection
- Motor overload handling
- Timeout detection
- Fault alarm and reset logic

#### Section 4: Feed Sequence Control (Rungs 36-80)
- Step 1: Wait for mail piece
- Step 2: Pick card from hopper
- Step 3: Move feeder forward
- Step 4: Place card on mail
- Step 5: Return to home position
- Cycle completion and reset

#### Section 5: Outputs and Indicators (Rungs 81-95)
- Control motor and solenoids based on steps
- Update status indicators
- Set alarm outputs

#### Section 6: Diagnostics and Counters (Rungs 96-100)
- Count completed cycles
- Count fault occurrences
- Logging and diagnostics

### Sequence Flow

```
START (Manual/Auto)
    ↓
Check Safety Interlocks
    ↓
[STEP 1] Wait for Mail Present
    ↓
[STEP 2] Activate Vacuum, Pick Card (1.5s)
    ↓
[STEP 3] Move Feeder Forward (2.0s)
    ↓
[STEP 4] Release Card onto Mail (1.0s)
    ↓
[STEP 5] Return to Home (2.5s)
    ↓
Cycle Complete → Ready for Next
```

### Safety Features
1. **Emergency Stop**: Immediate shutdown of all motion
2. **Safety Guard Interlock**: Must be closed for operation
3. **Motor Overload Protection**: Automatic fault detection
4. **Card Empty Detection**: Warning before complete depletion
5. **Timeout Protection**: Prevents indefinite operation

### Fault Handling
- All faults trigger immediate stop
- Fault indicator illuminated
- Manual reset required after fault clearance
- Fault counter incremented for tracking

### Operation Modes

#### Manual Mode
- Single cycle per start button press
- Operator must press start for each card
- Used for setup and testing

#### Auto Mode
- Continuous operation
- Cycles automatically when mail present
- Operator presses stop to end

### Startup Procedure
1. Verify safety guard closed
2. Check card hopper loaded
3. Ensure feeder at home position
4. Press start button
5. System performs cycle

### Shutdown Procedure
1. Press stop button (normal stop)
2. Wait for current cycle to complete
3. System returns to home position
4. All outputs de-energized

### Maintenance Notes
- Regular inspection of card sensors
- Verify vacuum pressure
- Check feeder alignment
- Clean card path regularly
