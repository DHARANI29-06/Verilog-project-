## Verilog-project-
## Digital compass decoder
## Aim

To design and simulate a digital compass decoder that converts sensor direction signals into readable compass outputs using vivado software.
## Apparatus required 
Vivado software 
## Block diagram 


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
Endmodule
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


## Result 
The digital compass decoder was successfully designed and simulated in Vivado, and it correctly converted the input sensor signals into the corresponding compass directions.













