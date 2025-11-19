# Digital compass decoder
## Aim

To design and simulate a digital compass decoder that converts sensor direction signals into readable compass outputs using vivado software.

## Apparatus required 
Vivado 2024.1

## Block diagram 
<img width="2848" height="1600" alt="image" src="https://github.com/user-attachments/assets/601ecf43-2324-495c-9267-85c0dbfc972b" />

## Objective

  1. Read X and Y sensor values.
  2. Calculate the heading angle (0–359°).
  3. Decode the angle into N, E, S, or W.
  4. Implement the logic using Verilog.
  5. Verify direction output through simulation.

## Verilog code 
```verilog
module compassdecoder(
input signed [7:0] x,
input signed [7:0] y,
output reg [3:0] direction
);
reg [8:0] heading;
always @(*) begin
if (x == 0 && y == 0)
heading = 0;
else if (x == 0)
heading = (y > 0) ? 90 : 270;
else begin
if (x > 0 && y >= 0)
heading = (y * 90) / x;
else if (x <= 0 && y > 0)
heading = 180 - ((y * 90) / (-x));
else if (x < 0 && y <= 0)
heading = 180 + ((-y * 90) / (-x));
else if (x >= 0 && y < 0)
heading = 360 - (((-y) * 90) / x);
else
heading = 0;
end
end
always @(*) begin
if (heading < 45 || heading >= 315)
direction = 4'b1000;
else if (heading >= 45 && heading < 135)
direction = 4'b0100;
else if (heading >= 135 && heading < 225)
direction = 4'b0010;
else if (heading >= 225 && heading < 315)
direction = 4'b0001;
else
direction = 4'b0000;
end
endmodule
```
## Testbench code ##
```verilog
module compassdecoder_tb;
reg signed [7:0] x_tb;
reg signed [7:0] y_tb;
wire [3:0] direction_led_tb;
compassdecoder uut (
.x(x_tb),
.y(y_tb),
.direction_led(direction_led_tb));
initial begin
$monitor("Time=%0t | x=%0d | y=%0d | direction_led=%b", $time, x_tb, y_tb,
direction_led_tb);
x_tb = 0; y_tb = 0;
#10;
x_tb = 100; y_tb = 0;
#10;
x_tb = 0; y_tb = 100;
#10;
x_tb = -100; y_tb = 0;
#10;
x_tb = 0; y_tb = -100;
#10;
repeat (10) begin
x_tb = $random % 200 - 100; // -100 to 99
y_tb = $random % 200 - 100;
#10;
end
$finish;
end
endmodule
```
## Simulation output 

<img width="1624" height="884" alt="image" src="https://github.com/user-attachments/assets/2652d714-2bec-4d19-b79e-6ba715ef0e0c" />

## FPGA board simulation

1.  x = 00000000 & y = 00000000
2.  x = 01100100 & y = 00000000 
3.  output is NORTH (1000)

![WhatsApp Image 2025-11-19 at 12 24 26_728de212](https://github.com/user-attachments/assets/7612a3ec-d4b9-47d5-9309-efecd4e772f1)


1.  x = 00000000
2.  Y = 01100100 
3.  output is EAST (0100)

![WhatsApp Image 2025-11-19 at 12 24 26_904c3042](https://github.com/user-attachments/assets/e385a484-fa0b-4754-b544-c95e2df667ea)


1.  x = 10011100
2.  Y = 00000000
3.  output is SOUTH (0010)

![WhatsApp Image 2025-11-19 at 12 24 26_a0e93b83](https://github.com/user-attachments/assets/712b4a4f-2d5c-463e-a272-9b1e2745270b)


1.  x = 00000000
2.  Y = 10011100 
3.  output is WEST (0001)

![WhatsApp Image 2025-11-19 at 12 24 25_a3d74663](https://github.com/user-attachments/assets/67c11f09-2162-4a64-ab95-a3854c633155)

## Result 
The digital compass decoder was successfully designed and simulated in Vivado, and it correctly converted the input sensor signals into the corresponding compass directions.
