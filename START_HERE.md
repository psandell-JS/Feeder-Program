# 🚀 GETTING STARTED - READ ME FIRST!

## Welcome to Your Card Feeder PLC Program

This is a **complete, ready-to-use** PLC program for your encoder-synchronized card feeder system. Everything you need is here!

---

## ⭐ Your System Configuration

**What You Told Me:**
- **PLC**: Omron CP1H-X40DT-D
- **Encoder**: Accu-Coder 3032091 (tracks paper position)
- **Start Sensor**: Banner SME312D (detects incoming paper)
- **Stop Sensor**: Optex 15PL (detects card at feeder tip)

**What It Does:**
1. Automatically feeds card to tip and waits
2. Detects paper with start sensor
3. Uses encoder to track paper position
4. Feeds card at exact calculated position onto paper
5. One card per paper, precisely placed

---

## 📚 Your Documents (In Order of Use)

### 🔧 For Installation and Wiring:
1. **Start Here:** [HARDWARE_SPECS.md](HARDWARE_SPECS.md)
   - All hardware details
   - I/O address mapping
   - Encoder configuration

2. **Wiring:** [WIRING_DIAGRAM.md](WIRING_DIAGRAM.md)
   - Detailed wiring instructions
   - Encoder cable routing (MUST use shielded cable!)
   - All sensor connections
   - Power supply connections

### 💻 For PLC Programming:
3. **Setup CX-Programmer:** [CX_IMPORT_GUIDE.md](CX_IMPORT_GUIDE.md)
   - How to create new project
   - Configure high-speed counter for encoder
   - Enter ladder logic

4. **The Actual Program:** [LADDER_LOGIC_ENCODER.txt](LADDER_LOGIC_ENCODER.txt)
   - Complete 100-rung ladder logic
   - Enter this into CX-Programmer
   - All logic for encoder synchronization

### ✅ For Commissioning:
5. **Installation Checklist:** [COMMISSIONING_CHECKLIST.md](COMMISSIONING_CHECKLIST.md)
   - Step-by-step installation guide
   - Testing procedures
   - Calibration instructions
   - Sign-off section

### 🔍 For Operation and Maintenance:
6. **When Something Goes Wrong:** [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
   - Diagnostic procedures
   - Common problems and solutions
   - Emergency procedures

7. **Quick Reference:** [README.md](README.md)
   - System overview
   - Quick start guide
   - All document links

---

## 🎯 Quick Start Path

**Never done this before? Follow this exact order:**

### Phase 1: Planning (1 hour)
```
☐ Read HARDWARE_SPECS.md completely
☐ Understand the encoder position calculation
☐ Measure your physical distances:
   - Distance from start sensor to card placement point: _____ mm
   - Encoder wheel diameter: _____ mm
☐ Calculate your FEED_DELAY_COUNTS (formula in HARDWARE_SPECS.md)
```

### Phase 2: Wiring (2-4 hours)
```
☐ Open WIRING_DIAGRAM.md
☐ Wire encoder with SHIELDED CABLE (critical!)
☐ Wire Banner start sensor
☐ Wire Optex stop sensor
☐ Wire all buttons (Start, Stop, E-stop, Reset)
☐ Wire motor and solenoids
☐ Wire indicator lights
☐ Double-check all connections against diagram
```

### Phase 3: Programming (2-3 hours)
```
☐ Install CX-Programmer (version 9.0+)
☐ Open CX_IMPORT_GUIDE.md
☐ Create new project for CP1H-X40DT-D
☐ Configure high-speed counter (Mode 2, inputs 0.00, 0.01, 0.02)
☐ Enter all 100 rungs from LADDER_LOGIC_ENCODER.txt
☐ Add symbols from HARDWARE_SPECS.md
☐ Compile program (fix any errors)
☐ Save as "Card_Feeder_v2.0.cxp"
```

### Phase 4: Testing (2-4 hours)
```
☐ Open COMMISSIONING_CHECKLIST.md
☐ Follow checklist section by section
☐ Test encoder counting (D0 should increment)
☐ Test sensors (Banner and Optex)
☐ Test safety interlocks (E-stop, guard)
☐ Test card prep cycle (feed to tip)
☐ Test position-based feeding
```

### Phase 5: Calibration (1-2 hours)
```
☐ Set initial FEED_DELAY_COUNTS (D2) from calculation
☐ Run at slow speed
☐ Measure actual card placement
☐ Adjust D2 value:
   - Too early? INCREASE D2 by 100
   - Too late? DECREASE D2 by 100
☐ Repeat until accurate
☐ Test at production speed
```

### Phase 6: Production (Ongoing)
```
☐ Train operators
☐ Run production
☐ Use TROUBLESHOOTING.md if issues arise
☐ Follow maintenance schedule
```

---

## ⚠️ Critical Items - DON'T SKIP!

### 🔴 MUST DO #1: Use Shielded Cable for Encoder
- The encoder MUST use shielded twisted-pair cable
- Ground shield at PLC end ONLY (not both ends)
- Keep encoder cable away from power cables (minimum 6 inches)
- **If you don't do this, encoder will not work reliably!**

### 🔴 MUST DO #2: Configure High-Speed Counter
- In CX-Programmer: PLC Settings → High-Speed Counter
- Set Counter 0 (C000) to Mode 2 (2-phase bi-directional)
- Assign inputs: A=0.00, B=0.01, Z=0.02
- Set input filter to minimum for these inputs
- **Without this, encoder won't count!**

### 🔴 MUST DO #3: Verify Encoder Direction
- After installation, monitor D0 (current encoder count)
- Run paper forward - D0 should INCREASE
- If D0 decreases, power off and swap A/B phase wires
- **Wrong direction = feeding at wrong position!**

### 🔴 MUST DO #4: Calculate and Calibrate FEED_DELAY
- Formula: `FEED_DELAY_COUNTS = (Distance × PPR) / Circumference`
- Example: 250mm distance, 2000 PPR, 314mm circumference = 1592 counts
- Set D2 register to this calculated value
- Fine-tune during testing
- **Wrong D2 value = cards in wrong position!**

### 🔴 MUST DO #5: Test Safety Systems
- E-stop MUST stop all motion immediately
- Safety guard MUST prevent operation when open
- Test these EVERY day before production
- **Safety first - always!**

---

## 💡 Pro Tips

### Tip #1: Start Slow
- Test everything at slow speed first (25% of production)
- Only increase speed after everything works perfectly
- Calibrate at slow speed, then verify at full speed

### Tip #2: Monitor These Values
- D0 = Current encoder count (should increment smoothly)
- D2 = Feed delay counts (adjust this for calibration)
- D6 = Target position (where card will feed)
- D3 = Cycle counter (how many cards fed)
- D4 = Fault counter (how many faults occurred)

### Tip #3: Use Simulator Mode
- Before downloading to PLC, test in simulator
- Force inputs to see if logic works
- Saves time and prevents mistakes

### Tip #4: Document Everything
- Write down your final D2 value after calibration
- Mark wires with labels
- Take photos of your installation
- Fill out the commissioning checklist completely

### Tip #5: Keep Backups
- Save your final program as "Card_Feeder_v2.0_FINAL.cxp"
- Save a backup copy to USB drive
- Print the ladder logic as PDF for reference

---

## 🆘 Common First-Time Issues

### "My encoder isn't counting!"
**Check these:**
1. Is encoder powered? (24VDC at encoder terminals)
2. High-speed counter configured in PLC settings?
3. Is shielded cable being used?
4. Are A, B phases wired to 0.00, 0.01?

**Solution:** See TROUBLESHOOTING.md → Section 1: Encoder Issues

### "Card feeds too early/late"
**This is normal during initial setup!**
1. Measure how far off (in mm)
2. Calculate correction needed (see COMMISSIONING_CHECKLIST.md)
3. Adjust D2 value
4. Test again

**Solution:** See TROUBLESHOOTING.md → Section 3: Position/Timing Issues

### "System won't start"
**Check these:**
1. Is E-stop released? (should NOT be pressed)
2. Is safety guard closed?
3. Is red fault light ON? (press Reset for 3 seconds)
4. Is PLC in RUN mode? (not STOP or PROGRAM)

**Solution:** See TROUBLESHOOTING.md → Section 5: Safety and Fault Issues

### "I can't connect to PLC"
**Check these:**
1. Is PLC powered on?
2. Is USB/serial cable connected?
3. Is correct COM port selected in CX-Programmer?
4. Is CP1H-X40DT-D selected as PLC model?

**Solution:** See TROUBLESHOOTING.md → Section 6: PLC Communication Issues

---

## 📞 Need Help?

### If you're stuck:
1. **First:** Check TROUBLESHOOTING.md for your specific issue
2. **Second:** Review the relevant document:
   - Wiring problem? → WIRING_DIAGRAM.md
   - Program problem? → LADDER_LOGIC_ENCODER.txt
   - Setup problem? → CX_IMPORT_GUIDE.md
   - Don't understand something? → HARDWARE_SPECS.md
3. **Third:** Contact support:
   - Omron: 1-800-55-OMRON (US)
   - Accu-Coder: encoder.com/support
   - Banner: bannerengineering.com/support

### What to tell support:
- "I have an encoder-synchronized card feeder"
- "Using CP1H-X40DT-D PLC"
- "The problem is: [describe specific symptom]"
- "I've tried: [list what you've done]"
- Provide values: D0=____, D2=____, D6=____

---

## ✅ Ready to Start?

**Your Next Step:**
1. Print or open [COMMISSIONING_CHECKLIST.md](COMMISSIONING_CHECKLIST.md)
2. Start with the "Pre-Installation Checklist" section
3. Work through each checkbox in order
4. Don't skip any steps!

**You've got this!** Everything is documented. Just follow the steps.

---

## 📋 What's Included in This Package

| Document | Purpose | When to Use |
|----------|---------|-------------|
| **THIS FILE** | Getting started guide | Start here! |
| HARDWARE_SPECS.md | Technical specifications | Installation planning |
| WIRING_DIAGRAM.md | All wiring details | During wiring |
| LADDER_LOGIC_ENCODER.txt | The actual PLC program | Programming phase |
| CX_IMPORT_GUIDE.md | How to use CX-Programmer | Programming phase |
| COMMISSIONING_CHECKLIST.md | Step-by-step setup | Installation & testing |
| TROUBLESHOOTING.md | Problem solving | When issues occur |
| README.md | Overview and reference | Quick reference |

---

## 🎓 Understanding How It Works

**Simple Explanation:**

1. **Setup Phase:**
   - Card feeds forward until stop sensor sees it
   - Now card is ready at the tip, waiting

2. **Detection Phase:**
   - Paper moves on base (encoder counting)
   - Start sensor detects paper approaching
   - System logs encoder position: "Paper detected at count 5000"

3. **Calculation Phase:**
   - System calculates: "Feed card when encoder reaches 5000 + 1500 = 6500"
   - (The 1500 is your FEED_DELAY_COUNTS setting)

4. **Feeding Phase:**
   - Encoder keeps counting: 5001, 5002, 5003... 6498, 6499, 6500!
   - **At 6500, card feeds!**
   - Card lands precisely on paper

5. **Reset Phase:**
   - Card feeding complete
   - System prepares next card automatically
   - Ready for next paper

**That's it!** Encoder-synchronized means feeding happens at an exact encoder count, not based on time. This is more accurate.

---

## 🎯 Your Mission, If You Choose to Accept It

**Goal:** Get your card feeder running with precise, encoder-synchronized card placement.

**What Success Looks Like:**
- Cards consistently land in the exact same spot on paper
- No double-feeding
- No missed cards
- Works reliably at production speed
- Operators trained and confident

**You have everything you need!** All the documentation, all the code, all the instructions. Just follow the steps.

---

## 🚀 Let's Do This!

**Your first action:** Open [COMMISSIONING_CHECKLIST.md](COMMISSIONING_CHECKLIST.md) and start checking boxes!

Good luck! 🍀

---

**Document Version**: 2.0  
**Last Updated**: 2026-02-03  
**System**: CP1H-X40DT-D Encoder-Synchronized Card Feeder  
**Status**: Ready for implementation
