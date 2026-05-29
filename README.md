# 🖥️ MARIE CPU Architecture Simulation from NAND Gates

A complete, gate-level hardware simulation of the **MARIE** (Machine Architecture that is Really Intuitive and Easy) 16-bit computer, built entirely from fundamental **NAND gates** and designed to run inside **Sebastian Lague's Digital Logic Sim**.

This project bypasses high-level coding or abstract software, opting instead for pure, hierarchical logic gates. By leveraging the component-based system in Sebastian Lague's simulator, this repository provides the raw component files necessary to spawn, inspect, and run a fully functional MARIE processor.

---

## 📸 Component Showcase & Visual Gallery

Here is the complete visual breakdown of the custom logic components designed for this MARIE simulation, directly exported from the simulator.

### 🏛️ Main CPU & System Level (Work in Progress)
| Component | Schematic | Description |
|---|---|---|
| **Unfinished MARIE CPU** | ![Unfinished MARIE CPU](img/unfished_MARIE.png) | The top-level system chassis containing the CPU core, RAM memory, master clock generator, and basic input/output bus. |
| **Unfinished CPU Datapath** | ![Unfinished CPU Datapath](img/unfinshed_cpu.png) | The internal wiring of the CPU datapath where registers, ALU, and control unit are situated. |

### 🎛️ Control & Timing
| Component | Schematic | Description |
|---|---|---|
| **Control Unit** | ![Control Unit](img/Control_Unit.png) | The hardwired control unit that decodes opcodes and asserts control signals based on the timing cycle. |
| **5-Cycles Counter** | ![5-Cycles Counter](img/5Cycles_Counter.png) | The sequential ring counter that generates the timing pulses $T_0$ to $T_4$ for each instruction phase. |

### 🧮 Arithmetic, Memory & Bus routing
| Component | Schematic | Description |
|---|---|---|
| **16-Bit ALU** | ![16-Bit ALU](img/16Bit_ALU.png) | The Arithmetic Logic Unit supporting mathematical operations like addition and subtraction. |
| **16-Bit RAM** | ![16-Bit RAM](img/16Bit_Ram.png) | The random-access memory array used to store machine code instructions and data. |
| **16-Bit Collector** | ![16-Bit Collector](img/16Bit_Collector.png) | The main data bus system that routes signals between the registers and other components. |

### 💾 Registers & Bit Utilities
| Component | Schematic | Description |
|---|---|---|
| **12-Bit Register** | ![12-Bit Register](img/12Bit_Register.png) | Used for memory-addressing registers like the Program Counter (`PC`) and Memory Address Register (`MAR`). |
| **16-Bit Register** | ![16-Bit Register](img/16Bit_Register.png) | Used for general data registers like the Accumulator (`AC`), Memory Buffer Register (`MBR`), and Instruction Register (`IR`). |
| **8-Bit Tri-state** | ![8-Bit Tri-state](img/8Bit_Tristate.png) | Tri-state buffer component used for gating 8-bit registers onto the shared bus. |
| **16-Bit Data Block** | ![16-Bit Data Block](img/16Bit_DataBlock.png) | Multi-bit structural grouping block for data manipulation. |
| **1-Bit Data Block** | ![1-Bit Data Block](img/1Bit_DataBlock.png) | Single-bit data block for individual status tracking. |
| **16-Bit Counter** | ![16-Bit Counter](img/16Bit_Counter.png) | Sequential counter used to keep track of operations or sequences. |

### 🧱 Core Logic & Building Blocks
| Component | Schematic | Description |
|---|---|---|
| **D Flip-Flop** | ![D Flip-Flop](img/DFlipFlop.png) | Standard edge-triggered D Flip-Flop, the primary component for register cells. |
| **Latch** | ![Latch](img/Latche.png) | Basic level-sensitive storage latch. |
| **Full Adder** | ![Full Adder](img/FullAdder.png) | 1-bit full adder with Carry-in and Carry-out, chained to build the ALU. |
| **Selector** | ![Selector](img/Selector.png) | Multiplexer selector logic for routing control signals. |
| **Demux 2-to-4** | ![Demux 2-to-4](img/Demux2t4.png) | 2-to-4 line decoder/demultiplexer for addressing and routing. |
| **Module** | ![Module](img/Module.png) | Custom sub-circuit macro block. |
| **Sub-Module** | ![Sub-Module](img/SubModule.png) | Specialized nested functional logic macro. |

---

## 🛠️ What Has Been Implemented

This project successfully constructs a multi-layered hardware simulation of a complete computer starting from the lowest level:

1. **Foundational Gates & Latches:**
   * Custom basic gates and level-sensitive storage cell (`Latche.png`) and edge-triggered storage (`DFlipFlop.png`) built directly from NAND gates.
2. **Datapath & Register Arrays:**
   * High-capacity custom bit-width registers, including the `12-bit Register` (optimized for addressing) and the `16-bit Register` (optimized for data storage), complete with load-enable lines.
   * Chained 1-bit full adders (`FullAdder.png`) into a robust 16-bit Ripple-Carry ALU (`16Bit_ALU.png`) supporting both subtraction (via 2's complement logic) and standard addition.
3. **Data Routing System (The Bus):**
   * Built the `16Bit_Collector` to handle multiple registers competing for a single shared bus, avoiding bus collisions and coordinating clean read/write cycles.
   * Created decoder systems (`Demux2t4.png`, `Selector.png`) and `8-Bit Tri-state` gates to manage bus access.
4. **Memory Subsystems:**
   * Crafted a `16-Bit RAM` block to store program instructions and variables, fully mapped to the addressing bus.
5. **Instruction Execution Control:**
   * Developed the `5-Cycles Counter` (Ring Counter) to sequence micro-operations step-by-step ($T_0, T_1, T_2, T_3, T_4$).
   * Constructed the `Control Unit` to decode the 4-bit opcodes and trigger the corresponding register transfer signals.

---

## ⏳ What is Remaining (Work in Progress)

The CPU architecture is extremely close to running its first programs, but some top-level integration is still needed (as shown in `unfished_MARIE.png` and `unfinshed_cpu.png`):

1. **Final CPU Wiring:**
   * **Control Lines Routing:** Complete the connections from the `Control Unit` control-line outputs (e.g., `MAR_Write`, `PC_Increment`, `AC_Load`) to their target register control pins on the CPU datapath.
   * **ALU Loopback:** Ensure the output of the 16-bit ALU successfully routes back to the input multiplexer of the Accumulator (`AC`) register.
   * **Status Flag Connections:** Link the Zero, Negative, and Positive flags of the ALU to the Skip Condition decoder within the Control Unit.
2. **Master Clock Distribution:**
   * Distribute a single synchronized clock signal (`CLK`) across all internal registers and the 5-cycle timing counter to prevent clock skew or race conditions.
3. **Memory Bus Integration:**
   * Link the address out from the Memory Address Register (`MAR`) to the RAM address port, and connect the RAM data port in bi-directional mode to the Memory Buffer Register (`MBR`).
4. **Testing with Sample Code:**
   * Manually input a simple program (e.g., loading a number, adding another, and storing the result) to verify the hardwired logic executes correctly.

---

## ⚙️ How the CPU Works (Internal Mechanics)

The CPU acts as a hardware state machine, executing instructions through the **Fetch-Decode-Execute** cycle. Here is how each component interacts:

```mermaid
sequenceDiagram
    participant PC as Program Counter (12-bit)
    participant MAR as Memory Address Register
    participant RAM as 16-Bit RAM
    participant IR as Instruction Register
    participant CU as Control Unit & 5-Cycle Counter
    participant ALU as 16-Bit ALU
    participant AC as Accumulator

    Note over PC,CU: Fetch Phase (T0 - T1)
    CU->>PC: Read PC Address
    PC->>MAR: Load into MAR
    CU->>RAM: Memory Read Address in MAR
    RAM->>IR: Load Instruction into IR

    Note over IR,CU: Decode Phase (T2)
    IR->>CU: 4-bit Opcode to Decoders
    CU->>CU: Select Active Opcode Line

    Note over CU,AC: Execute Phase (T3 - T4)
    CU->>ALU: Trigger Arithmetic Signal
    ALU->>AC: Update Accumulator with ALU Output
    CU->>PC: Increment PC
```

### The Timing Sequence (Driven by `5Cycles_Counter.png`)
MARIE divides each instruction cycle into distinct micro-operations triggered on timing pulses generated by the `5-Cycles Counter`:
* **Pulse $T_0$:** The address in the `PC` is loaded into the `MAR` (`MAR <- PC`).
* **Pulse $T_1$:** The instruction at the memory location pointed to by `MAR` is read into the `IR` (`IR <- M[MAR]`), and the `PC` is incremented (`PC <- PC + 1`).
* **Pulse $T_2$:** The `Control Unit` decodes the 4-bit opcode. If the instruction has an address operand, the address portion `IR[11-0]` is copied to the `MAR` (`MAR <- IR[11-0]`).
* **Pulse $T_3$ & $T_4$:** The specific logic path for the active instruction opcode is enabled. For example, for an `Add` instruction:
  * $T_3$: Read operand from memory into the `MBR` (`MBR <- M[MAR]`).
  * $T_4$: The ALU sums the `AC` and `MBR`, saving the result back to `AC` (`AC <- AC + MBR`).

---

## 💾 Installation & Setup in "Digital Logic Sim"

Since this project consists of raw component files rather than a standalone app, you must import them directly into **Sebastian Lague's Digital Logic Sim**.

### Step 1: Download & Install the Simulator
* **GitHub Repository:** [SebLague/Digital-Logic-Sim](https://github.com/SebLague/Digital-Logic-Sim)
* **Build or Download:** Grab the latest release for your OS.

### Step 2: Locate the Save Directory
Navigate to the following path depending on your operating system:

* 💻 **Windows:**
  ```text
  %USERPROFILE%\AppData\LocalLow\Sebastian Lague\Digital Logic Sim\SaveData\Components
  ```
* 🐧 **Linux:**
  ```text
  ~/.config/unity3d/Sebastian Lague/Digital Logic Sim/SaveData/Components
  ```
* 🍎 **macOS:**
  ```text
  ~/Library/Application Support/unity3d/Sebastian Lague/Digital Logic Sim/SaveData/Components
  ```

### Step 3: Copy Component Files
1. Copy all the `.json` (or component definition) files from this project's `/components` directory.
2. Paste them directly into the **`Components`** folder identified in Step 2.
3. Restart the simulator. You will now see MARIE sub-circuits (such as `AC_16bit`, `ALU_16bit`, `ControlUnit`, and `MARIE_CPU`) listed in your custom components palette!

---

## 🛠️ MARIE Instruction Set Architecture (ISA)

The MARIE CPU decodes 16-bit instructions with the following format:
```text
┌───────────────┬──────────────────────────────────────────┐
│ Opcode (4-bit)│             Address (12-bit)             │
└───────────────┴──────────────────────────────────────────┘
15            12 11                                       0
```

The simulated hardwired control unit fully supports the following 14 instructions:

| Opcode (Hex) | Instruction | Description | RTL (Register Transfer Language) / Micro-operations |
|:---:|:---|:---|:---|
| **`0`** | `JnS X` | Jump and Store | `MBR <- PC`, `MAR <- X`, `M[MAR] <- MBR`, `AC <- X`, `AC <- AC + 1`, `PC <- AC` |
| **`1`** | `Load X` | Load X into AC | `MAR <- X`, `MBR <- M[MAR]`, `AC <- MBR` |
| **`2`** | `Store X` | Store AC into X | `MAR <- X`, `MBR <- AC`, `M[MAR] <- MBR` |
| **`3`** | `Add X` | Add X to AC | `MAR <- X`, `MBR <- M[MAR]`, `AC <- AC + MBR` |
| **`4`** | `Subt X` | Subtract X from AC | `MAR <- X`, `MBR <- M[MAR]`, `AC <- AC - MBR` |
| **`5`** | `Input` | Input value to AC | `AC <- InREG` |
| **`6`** | `Output` | Output AC value | `OutREG <- AC` |
| **`7`** | `Halt` | Terminate program | Stops the master clock generator |
| **`8`** | `SkipCond` | Skip based on AC | If condition is met: `PC <- PC + 1` (IR[11-10]: `00` for <0, `01` for =0, `10` for >0) |
| **`9`** | `Jump X` | Jump to X | `PC <- X` |
| **`A`** | `Clear` | Clear AC | `AC <- 0` |
| **`B`** | `AddI X` | Add Indirect | `MAR <- X`, `MBR <- M[MAR]`, `MAR <- MBR`, `MBR <- M[MAR]`, `AC <- AC + MBR` |
| **`C`** | `JumpI X` | Jump Indirect | `MAR <- X`, `MBR <- M[MAR]`, `PC <- MBR` |
| **`D`** | `StoreI X`| Store Indirect | `MAR <- X`, `MBR <- M[MAR]`, `MAR <- MBR`, `MBR <- AC`, `M[MAR] <- MBR` |

---

## 🏃 How to Run a Program

To execute assembly or machine code on your simulated MARIE processor inside Sebastian Lague's simulator:

1. **Write your program** in MARIE Machine Code (e.g., `1005` translates to `Load 005`).
2. **Load the program into RAM:**
   * Open the `MARIE_RAM` component inside the simulator.
   * Right-click/edit the RAM table (or write directly to DFF registers at specific memory locations, e.g., starting at address `000` in Hex).
3. **Set the Clock:**
   * Pin a standard Clock generator to the master `CLK` input of the `MARIE_CPU` component.
   * Set the clock speed (manual toggle or fast oscillation).
4. **Trigger Reset:**
   * Temporarily assert the `Reset` pin high to clear the registers and set `PC` back to `000`.
5. **Watch the Execution:**
   * Enable "Show Wire Signals" inside the simulator to watch electrons flow from memory to registers, through the ALU, and into the Output register!

---

## 💡 Troubleshooting & Notes

* **Simulation Lag:** Because Digital Logic Sim simulates every single logic gate in real-time, building a full 16-bit CPU with hardwired instruction sets requires thousands of virtual gates. If you experience performance lag:
  * Reduce the clock frequency.
  * Close nesting tabs in the simulator (e.g., do not keep the internal gate-level view of the ALU or RAM open while running the top-level CPU).
* **Clock Sync Issues:** Make sure all registers share a single unified `CLK` signal to prevent race conditions during timing cycles.
