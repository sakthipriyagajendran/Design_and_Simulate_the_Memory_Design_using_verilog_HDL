# Design_and_Simulate_the_Memory_Design_using_verilog_HDL
# EXP NO:5. Design-and-Simulate the-Memory-Design-using-Verilog-HDL
# Aim 
To design and simulate a RAM,ROM,FIFO using Verilog HDL, and verify its functionality through a testbench in the Vivado 2023.1 environment. 

# Apparatus Required 
Vivado 2023.1 

# Procedure
1. Launch Vivado 2023.1 Open Vivado and create a new project.
2. Design the Verilog Code Write the Verilog code for the RAM,ROM,FIFO
3. Create the Testbench Write a testbench to simulate the memory behavior. The testbench should apply various and monitor the corresponding output.
4. Create the Verilog Files Create both the design module and the testbench in the Vivado project.
5. Run Simulation Run the behavioral simulation to verify the output.
6. Observe the Waveforms Analyze the output waveforms in the simulation window, and verify that the correct read and write operation.
7. Save and Document Results Capture screenshots of the waveform and save the simulation logs. These will be included in the lab report.

# Code
# RAM
**// Verilog code**
module RAM16KB(
    input clk,
    input we,
    input [13:0] addr,
    input [7:0] data_in,
    output reg [7:0] data_out
);

reg [7:0] mem[0:16383];

always @(posedge clk)
begin
    if(we)
        mem[addr] <= data_in;
    else
        data_out <= mem[addr];
end

endmodule
**// Test bench**
`timescale 1ns/1ps

module tb_RAM;

reg clk;
reg we;
reg [13:0] addr;
reg [7:0] data_in;

wire [7:0] data_out;

integer i;

reg [13:0] addr_mem[0:19];
reg [7:0] data_mem[0:19];

RAM16KB uut(
    .clk(clk),
    .we(we),
    .addr(addr),
    .data_in(data_in),
    .data_out(data_out)
);
always #5 clk = ~clk;

initial
begin

    clk = 0;

    //-----------------------------
    // Write
    //-----------------------------

    we = 1;

    for(i=0;i<20;i=i+1)
    begin
        @(posedge clk);

        addr_mem[i] = $random % 16384;
        data_mem[i] = $random;

        addr = addr_mem[i];
        data_in = data_mem[i];
 $display("WRITE Address=%0d Data=%h",addr,data_in);

    end

    // Read
    we = 0;

    for(i=0;i<20;i=i+1)
    begin
        @(posedge clk);

        addr = addr_mem[i];

        @(posedge clk);

        $display("READ Address=%0d Data=%h",addr,data_out);
    end

    #20;
    $finish;
end
endmodule
**// output Waveform**
<img width="1600" height="879" alt="image" src="https://github.com/user-attachments/assets/2352c067-582b-4971-a742-eaeb4689321c" />

# ROM
// write verilog code for ROM using $random

// Test bench

// output Waveform

# FIFO
// write verilog code for FIFO

// Test bench

// output Waveform

# Conclusion
The RAM, ROM, FIFO memory with read and write operations was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the memory operations and observing the output waveforms. The experiment demonstrates how to implement memory operations in Verilog, effectively modeling both the reading and writing processes.
