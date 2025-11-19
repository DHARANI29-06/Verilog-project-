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
input signed [15:0] x,
input signed [15:0] y,
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
reg signed [15:0] x_tb;
reg signed [15:0] y_tb;
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

<img width="1632" height="869" alt="Screenshot 2025-11-18 181402" src="https://github.com/user-attachments/assets/7568b270-c8b9-42db-90f2-24745ecb5ca0" />



## Result 
The digital compass decoder was successfully designed and simulated in Vivado, and it correctly converted the input sensor signals into the corresponding compass directions.
