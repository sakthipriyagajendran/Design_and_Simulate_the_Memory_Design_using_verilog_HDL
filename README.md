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
**// write verilog code for ROM using $random
**
module memory_4KB (
    input        clk,
    input        we,          // Write Enable
    input        re,          // Read Enable
    input  [11:0] address,    // 12-bit address: 0 to 4095
    input  [7:0]  data_in,    // 8-bit input data
    output reg [7:0] data_out // 8-bit output data
);

    // 4 KB memory = 4096 locations × 8 bits
    reg [7:0] memory [0:4095];

    // Write operation
    always @(posedge clk) begin
        if (we)
            memory[address] <= data_in;
    end
    // Read operation
    always @(*) begin
        if (re)
            data_out = memory[address];
        else
            data_out = 8'bz;
    end

endmodule

**// Test bench**

`timescale 1ns/1ps

module tb_memory_4KB;

    reg        clk;
    reg        we;
    reg        re;
    reg [11:0] address;
    reg [7:0]  data_in;

    wire [7:0] data_out;

    // Instantiate DUT
    memory_4KB DUT (
        .clk      (clk),
        .we       (we),
        .re       (re),
        .address  (address),
        .data_in  (data_in),
        .data_out (data_out)
    );
    // Clock generation
    initial begin
        clk = 0;
        forever #5 clk = ~clk;
    end

    // Test procedure
    initial begin

        // Initialize signals
        we      = 0;
        re      = 0;
        address = 12'd0;
        data_in = 8'd0;
         // -------------------------------
        // WRITE OPERATION
        // -------------------------------

        // Write 55H into address 100
        #10;
        we      = 1;
        address = 12'd100;
        data_in = 8'h55;

        #10;
        we = 0;

        // Write AAH into address 200
        #10;
        we      = 1;
        address = 12'd200;
        data_in = 8'hAA;

        #10;
        we = 0;
        // Write FFH into last address
        #10;
        we      = 1;
        address = 12'd4095;
        data_in = 8'hFF;
        #10;
        we = 0;
        // -------------------------------
        // READ OPERATION
        // -------------------------------
        // Read address 100
        #10;
        re      = 1;
        address = 12'd100;
        #5;
        $display("Address = %d, Data = %h", address, data_out);
        // Disable read
        #10;
        re = 0;
        #10;
        $finish;
    end
    endmodule
   
**// output Waveform**

<img width="1600" height="880" alt="image" src="https://github.com/user-attachments/assets/75e0cf5a-797b-4da0-903b-a33c4bcfe013" />

# FIFO
// **write verilog code for FIFO**
`timescale 1ns/1ps

module FIFO(
    input clk,
    input rst,
    input wr,
    input rd,
    input [7:0] data_in,
    output reg [7:0] data_out
);

reg [7:0] mem[0:3];
reg [1:0] wptr, rptr;

always @(posedge clk)
begin

    if(rst)
    begin
    wptr <= 0;
        rptr <= 0;
    end
    else
    begin
        // Write Operation
        if(wr)
        begin
            mem[wptr] <= data_in;
            wptr <= wptr + 1;
        end
        // Read Operation
        if(rd)
        begin
            data_out <= mem[rptr];
            rptr <= rptr + 1;
        end
    end
end
endmodule
**// Test bench**

`timescale 1ns/1ps

module tb_FIFO;

reg clk;
reg rst;
reg wr;
reg rd;
reg [7:0] data_in;

wire [7:0] data_out;

FIFO uut(

.clk(clk),
.rst(rst),
.wr(wr),
.rd(rd),
.data_in(data_in),
.data_out(data_out)

);
// Clock Generation
always #5 clk = ~clk;

initial
begin

clk = 0;
rst = 1;
wr = 0;
rd = 0;
data_in = 0;

#10;
rst = 0;

//----------------------
// Write Data
//----------------------

#10;
wr = 1;
data_in = 8'd10;
#10;
data_in = 8'd20;

#10;
data_in = 8'd30;

#10;
data_in = 8'd40;

#10;
wr = 0;

//----------------------
// Read Data
//----------------------

#10;
rd = 1;

#40;
rd = 0;
#20;

$finish;

end

initial
begin

$monitor("Time=%0t wr=%b rd=%b data_in=%d data_out=%d wptr=%d rptr=%d",

          $time,
          wr,
          rd,
          data_in,
          data_out,
          uut.wptr,
          uut.rptr);

end

endmodule

/**/ output Waveform**

<img width="1558" height="790" alt="image" src="https://github.com/user-attachments/assets/b5573c42-acc2-41e5-8b69-89379280d7cc" />

# Conclusion
The RAM, ROM, FIFO memory with read and write operations was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the memory operations and observing the output waveforms. The experiment demonstrates how to implement memory operations in Verilog, effectively modeling both the reading and writing processes.
