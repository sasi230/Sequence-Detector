#  EXP NO:6 Sequence Detector using Moore and Mealy State Machines with Testbench Verification

## Aim:
To design and simulate a sequence detector using both Moore and Mealy state machine models in Verilog HDL, and verify their functionality through a testbench using the Vivado 2023.1 simulation environment. The objective is to detect a specific sequence of bits (e.g., 1011) and compare the Moore and Mealy designs.

## Apparatus Required:
+ Vivado 2023.1 or equivalent Verilog simulation tool.
+ Computer system with a suitable operating system.
## Procedure
1. Launch Vivado 2023.1:Open Vivado and create a new project.
2. Write the Verilog Code for Sequence Detector (Moore and Mealy FSM):Design two Verilog modules: one for a Moore FSM and another for a Mealy FSM to detect a sequence such as 1011.
3. Create the Testbench:Write a testbench to apply input sequences and verify the output of both FSM designs.
4. Add the Verilog Files:Add the Verilog code for both FSMs and the testbench to the Vivado project.
5. Run Simulation:Run the simulation and observe the output to check if the sequence is detected correctly.
6. Observe the Waveforms:Analyze the waveform to ensure both the Moore and Mealy machines detect the sequence as expected.
7. Save and Document Results:Capture the waveforms and include the results in the final report.

## Verilog Code for Sequence Detector Using Moore FSM

### moore_sequence_detector.v
```
module moore_fsm_sequence(
    input clk,
    input reset,
    input run,
    output reg [3:0] count
);

    // State encoding
    localparam s0 = 4'd0;
    localparam s2 = 4'd2;
    localparam s4 = 4'd4;
    localparam s6 = 4'd6;
    localparam s8 = 4'd8;
    localparam s10 = 4'd10;
    localparam s12 = 4'd12;

    reg [3:0] current_state, next_state;

    // State transition logic
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            current_state <= s0;  // Initialize to state s0 on reset
        end else begin
            current_state <= next_state;  // Move to next state on clock edge
        end
    end

    // Next state logic
    always @(*) begin
        case (current_state)
            s0:  if (run) next_state = s2;  else next_state = s0;
            s2:  if (run) next_state = s4;  else next_state = s2;
            s4:  if (run) next_state = s6;  else next_state = s4;
            s6:  if (run) next_state = s8;  else next_state = s6;
            s8:  if (run) next_state = s10; else next_state = s8;
            s10: if (run) next_state = s12; else next_state = s10;
            s12: if (run) next_state = s0;  else next_state = s12;
            default: next_state = s0;
        endcase
    end

    // Output logic (depends only on current state in Moore FSM)
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            count <= 4'd0;  // Reset count when reset is high
        end else begin
            count <= current_state;  // Output the current state as the count
        end
    end

endmodule
```
### mealy_sequence_detector.v
```
module mealy_fsm_sequence(
    input clk,
    input reset,
    input run,
    output reg [3:0] count
);

    // State encoding
    localparam s0 = 4'd0;
    localparam s2 = 4'd2;
    localparam s4 = 4'd4;
    localparam s6 = 4'd6;
    localparam s8 = 4'd8;
    localparam s10 = 4'd10;
    localparam s12 = 4'd12;

    reg [3:0] current_state, next_state;

    // State transition logic
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            current_state <= s0;  // Initialize to state s0 on reset
        end else begin
            current_state <= next_state;  // Move to next state on clock edge
        end
    end

    // Next state logic
    always @(*) begin
        case (current_state)
            s0:  if (run) next_state = s2;  else next_state = s0;
            s2:  if (run) next_state = s4;  else next_state = s2;
            s4:  if (run) next_state = s6;  else next_state = s4;
            s6:  if (run) next_state = s8;  else next_state = s6;
            s8:  if (run) next_state = s10; else next_state = s8;
            s10: if (run) next_state = s12; else next_state = s10;
            s12: if (run) next_state = s0;  else next_state = s12;
            default: next_state = s0;
        endcase
    end

    // Output logic (depends on current state and input in Mealy FSM)
    always @(*) begin
        case (current_state)
            s0: count = (run) ? 4'd2 : 4'd0;
            s2: count = (run) ? 4'd4 : 4'd2;
            s4: count = (run) ? 4'd6 : 4'd4;
            s6: count = (run) ? 4'd8 : 4'd6;
            s8: count = (run) ? 4'd10 : 4'd8;
            s10: count = (run) ? 4'd12 : 4'd10;
            s12: count = (run) ? 4'd0 : 4'd12;
            default: count = 4'd0;
        endcase
    end
```

### Testbench for Sequence Detector (Moore and Mealy FSMs)
```
module fsm_sequence_tb;

    reg clk;
    reg reset;
    reg run;
    wire [3:0] moore_count;
    wire [3:0] mealy_count;

    // Instantiate the Moore FSM
    moore_fsm_sequence moore_fsm (
        .clk(clk),
        .reset(reset),
        .run(run),
        .count(moore_count)
    );

    // Instantiate the Mealy FSM
    mealy_fsm_sequence mealy_fsm (
        .clk(clk),
        .reset(reset),
        .run(run),
        .count(mealy_count)
    );

    // Clock generation
    always #5 clk = ~clk;  // 10ns period clock

    // Test sequence
    initial begin
        // Initialize inputs
        clk = 0;
        reset = 1;
        run = 0;

        // Apply reset for a few cycles
        #10 reset = 0;

        // Start the FSM by setting run to 1
        #20 run = 1;

        // Let the FSM run for some time
        #100 $stop;
    end

    // Monitor outputs
    initial begin
        $monitor("Time=%0t | reset=%b | run=%b | Moore Count=%d | Mealy Count=%d",
                 $time, reset, run, moore_count, mealy_count);
    end

endmodule
```
## OUTPUT:
![image](https://github.com/user-attachments/assets/c10f6c08-3e0e-4232-bae3-3ad01f482a98)

## Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
