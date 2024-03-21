# [HDL bit](https://hdlbits.01xz.net/wiki/Main_Page)做题记录

## Getting Started

### Getting Started

```verilog
module top_module( output one );
	
	assign one = 1'b1;
	
endmodule
```

### Output Zero

```verilog
module top_module ( output zero );
	
	assign zero = 1'b0;
	
endmodule
```

## Verilog Language

### Basics

#### Simple Wire 

```verilog
module top_module( input in, output out );
    assign out = in;
endmodule
```

#### Four wires

```verilog
module top_module (
	input a,
	input b,
	input c,
	output w,
	output x,
	output y,
	output z  );
	
	assign w = a;
	assign x = b;
	assign y = b;
	assign z = c;

	// If we're certain about the width of each signal, using 
	// the concatenation operator is equivalent and shorter:
	// assign {w,x,y,z} = {a,b,b,c};
	
endmodule
```

#### Inverter

```verilog
module top_module( input in, output out );
    assign out = ~in;
endmodule
```

#### AND gate

```verilog
module top_module( 
    input a, 
    input b, 
    output out );
    assign out = a & b;
endmodule
```

#### NOR gate

```verilog
module top_module( 
    input a, 
    input b, 
    output out );
    assign out = ~(a|b);
endmodule
```

#### XNOR gate

```verilog
module top_module( 
    input a, 
    input b, 
    output out );
    assign out = ~(a^b);
endmodule
```

#### Declaring wire

```verilog
`default_nettype none
module top_module(
    input a,
    input b,
    input c,
    input d,
    output out,
    output out_n   ); 
    assign out = (a&b)|(c&d);
    assign out_n = ~out;
endmodule
```

#### 7458 chip

```verilog
module top_module ( 
    input p1a, p1b, p1c, p1d, p1e, p1f,
    output p1y,
    input p2a, p2b, p2c, p2d,
    output p2y );
    assign p1y = (p1a&p1b&p1c)|(p1d&p1e&p1f);
    assign p2y = (p2a&p2b)|(p2c&p2d);
endmodule
```

### Vectors

#### Vectors

```verilog
module top_module(
	input [2:0] vec, 
	output [2:0] outv,
	output o2,
	output o1,
	output o0
);
	
	assign outv = vec;

	// This is ok too: assign {o2, o1, o0} = vec;
	assign o0 = vec[0];
	assign o1 = vec[1];
	assign o2 = vec[2];
	
endmodule
```

#### Vectors in more detail

```verilog
module top_module( 
    input wire [15:0] in,
    output wire [7:0] out_hi,
    output wire [7:0] out_lo );
    assign out_hi = in[15:8];
    assign out_lo = in[7:0];
endmodule
```

#### Vectors part select

```verilog
module top_module (
	input [31:0] in,
	output [31:0] out
);
	assign out[31:24] = in[ 7: 0];	
	assign out[23:16] = in[15: 8];	
	assign out[15: 8] = in[23:16];	
	assign out[ 7: 0] = in[31:24];		
endmodule
```

#### Bitwise operators

```verilog
module top_module( 
    input [2:0] a,
    input [2:0] b,
    output [2:0] out_or_bitwise,
    output out_or_logical,
    output [5:0] out_not
);
    assign out_or_bitwise = a | b;
	assign out_or_logical = a || b;

	//assign out_not[2:0] = ~a;	
	//assign out_not[5:3] = ~b;	
    assign out_not = ~{b,a};
endmodule
```

#### Four-input gates

```verilog
module top_module( 
    input [3:0] in,
    output out_and,
    output out_or,
    output out_xor
);
    assign out_xor = ^in;
    assign out_or = | in;
    assign out_and = & in;
endmodule
```

#### Vector concatenation operator

``` verilog
module top_module (
    input [4:0] a, b, c, d, e, f,
    output [7:0] w, x, y, z );//

    // assign { ... } = { ... };
    assign {w, x, y, z} = {a, b, c, d, e, f, 2'b11};
endmodule
```

#### Vertor reversal 1

```verilog
module top_module( 
    input [7:0] in,
    output [7:0] out
); 
    integer i;
    always @* begin
        for(i = 0; i <= 7; i = i + 1) begin
            out[i] = in[7-i];        
        end    
    end
endmodule
```

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/341035ac3d2df711a126a9dcaf151646.png)

- 这题我一开始误解了题目的意思，看了仿真图意识到了`Reverse`的含义

#### Replication operator

```verilog
module top_module (
    input [7:0] in,
    output [31:0] out );//

    // assign out = { replicate-sign-bit , the-input };
    assign out = {{24{in[7]}}, in};
endmodule
```

#### More replication

```verilog
module top_module (
    input a, b, c, d, e,
    output [24:0] out );//

    // The output is XNOR of two vectors created by 
    // concatenating and replicating the five inputs.
    // assign out = ~{ ... } ^ { ... };
    assign out = ~ {{5{a}},{5{b}},{5{c}},{5{d}},{5{e}}} ^ {5{a,b,c,d,e}};
endmodule
```

### Modules: Hierarchy

#### Modules

```verilog
module top_module ( input a, input b, output out );
    mod_a(a, b, out);
endmodule
```

#### Connecting ports by position

```verilog
module top_module ( 
    input a, 
    input b, 
    input c,
    input d,
    output out1,
    output out2
);
    mod_a(out1, out2, a, b, c, d);
endmodule
```

#### Connecting ports by name

```verilog
module top_module ( 
    input a, 
    input b, 
    input c,
    input d,
    output out1,
    output out2
);
    mod_a (.out1(out1), .out2(out2), .in1(a), .in2(b), .in3(c), .in4(d));
endmodule
```

- 在Verilog中，模块实例化的一般语法如下：

  ```verilog
  verilogCopy codemodule_top_name u1 (
    .port1(signal1),
    .port2(signal2),
    // ... other ports ...
  );
  ```

  在这个例子中，`module_top_name`是要实例化的模块的名称，`u1`是该实例的名称。通过为每个实例提供唯一的名称，你可以在设计中引用和操作这些实例。

  此外，模块的端口也通过名称连接。上面的例子中，`.port1(signal1)`表示将模块 `module_top_name` 的 `port1` 连接到信号 `signal1`。这样的命名和连接使得代码更加清晰、可读，并且减少了出错的可能性。

  总体来说，命名模块实例和端口连接有助于在设计中保持结构的清晰性，提高可维护性和可读性。

#### Three modules

```verilog
module top_module ( input clk, input d, output q );
    wire q1, q2;
    my_dff dff1(.clk(clk), .d(d), .q(q1)),
    dff2(.clk(clk), .d(q1), .q(q2)),
    dff3(.clk(clk), .d(q2), .q(q));
endmodule
```

#### Modules and vectors

```verilog
module top_module ( 
    input clk, 
    input [7:0] d, 
    input [1:0] sel, 
    output [7:0] q 
);
    wire [7:0] out1, out2, out3;
    my_dff8 dff1(clk, d, out1),
    dff2(clk, out1, out2),
    dff3(clk, out2, out3);
    
    always @* begin
        case(sel)
            2'b00:
                q = d;
            2'b01:
                q = out1;
            2'b10:
                q = out2;
            2'b11:
                q = out3;
        endcase
   end
endmodule
```

#### Adder1

```verilog
module top_module(
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
);
    wire [15:0] out1, out2;
    wire out;
    add16 add1(a[15:0],b[15:0],0,out1,out),
    add2(a[31:16],b[31:16],out, out2,);
    assign sum = {out2, out1};
endmodule
```

#### Adder2

```verilog
module top_module (
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
);//
    wire cout1;
    add16 adder1(a[15:0], b[15:0], 0, sum[15:0], cout1),
    adder2(a[31:16], b[31:16], cout1, sum[31:16]);
endmodule

module add1 ( input a, input b, input cin, output sum, output cout );

// Full adder module here
    assign {cout, sum} = a + b + cin;
endmodule
```

#### Carry-select adder

```verilog
module top_module(
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
); 
    wire [15:0] out0, out1;
    wire cout0;
    add16 low(a[15:0], b[15:0], 0, sum[15:0], cout0),
    high0(a[31:16], b[31:16], 0, out0, ),
    high1(a[31:16], b[31:16], 1, out1, );
    assign sum[31:16] = cout0 ? out1:out0;
endmodule
```

#### Adder-subtractor

```verilog
module top_module(
    input [31:0] a,
    input [31:0] b,
    input sub,
    output [31:0] sum
);
    wire [31: 0]b_n ;
    wire cout0;
    assign b_n = b ^ {32{sub}};
    add16 adder0(a[15:0], b_n[15:0], sub, sum[15:0], cout0),
    adder1(a[31:16], b_n[31:16], cout0, sum[31:16]);
endmodule
```

### Procedures

- Combinational: `always @(*)`
- Clocked: `always @(posedge clk)`

#### Always blocks(combinational)

``` verilog
// synthesis verilog_input_version verilog_2001
module top_module(
    input a, 
    input b,
    output wire out_assign,
    output reg out_alwaysblock
);
    assign out_assign = a & b;
    always @(*) out_alwaysblock = a & b;  
endmodule
```

#### Always blocks(clocked)

```verilog
module top_module(
    input clk,
    input a,
    input b,
    output wire out_assign,
    output reg out_always_comb,
    output reg out_always_ff   );
    assign out_assign = a ^ b;
    always @(*) out_always_comb = a ^ b;
    always @(posedge clk) out_always_ff = a ^ b;
endmodule
```

#### If statement

```verilog
module top_module(
    input a,
    input b,
    input sel_b1,
    input sel_b2,
    output wire out_assign,
    output reg out_always   ); 
    always @(*) begin
        if (sel_b1 & sel_b2) begin
        out_always = b;
    end
    else begin
        out_always = a;
    end
end
    assign out_assign = (sel_b1 & sel_b2)? b : a; 
endmodule
```

- The second edtion

```verilog
module top_module(
    input a,
    input b,
    input sel_b1,
    input sel_b2,
    output wire out_assign,
    output reg out_always
);

    always @(*) begin
        if (sel_b1 & sel_b2) begin
            out_always = b;
            out_assign = b;
        end
        else begin
            out_always = a;
            out_assign = a;
        end
    end

endmodule
```

#### If statement latches

```verilog
module top_module (
    input      cpu_overheated,
    output reg shut_off_computer,
    input      arrived,
    input      gas_tank_empty,
    output reg keep_driving  ); //

    always @(*) begin
        if (cpu_overheated)
           shut_off_computer = 1;
        else 
           shut_off_computer = 0;
    end

    always @(*) begin
        if (~arrived)
           keep_driving = ~gas_tank_empty;
        else 
           keep_driving = 0;
    end

endmodule
```

#### Case statement

```verilog
module top_module ( 
    input [2:0] sel, 
    input [3:0] data0,
    input [3:0] data1,
    input [3:0] data2,
    input [3:0] data3,
    input [3:0] data4,
    input [3:0] data5,
    output reg [3:0] out   );//

    always@(*) begin  // This is a combinational circuit
        case(sel)
            3'b000:  out = data0;
            3'b001:  out = data1;
            3'b010:  out = data2;
            3'b011:  out = data3;
            3'b100:  out = data4;
            3'b101:  out = data5;
            default: out = 0;
        endcase
    end

endmodule
```

#### Priority encoder

```verilog
module top_module (
    input [3:0] in,
    output reg [1:0] pos  );
    always @ * begin
        casex(in)
            4'bxxx1: pos = 2'b00;
            4'bxx10: pos = 2'b01;
            4'bx100: pos = 2'b10;
            4'b1000: pos = 2'b11;
            default: pos = 2'b00;
        endcase
        
    end
    
endmodule
```

#### Priority encoder with casez

```verilog
module top_module (
    input [7:0] in,
    output reg [2:0] pos );
    always @* begin
        casez(in)
            8'bzzzzzzz1: pos = 3'd0;
            8'bzzzzzz1z: pos = 3'd1;
            8'bzzzzz1zz: pos = 3'd2;
            8'bzzzz1zzz: pos = 3'd3;
            8'bzzz1zzzz: pos = 3'd4;
            8'bzz1zzzzz: pos = 3'd5;
            8'bz1zzzzzz: pos = 3'd6;
            8'b1zzzzzzz: pos = 3'd7;
            default: pos = 3'd0;
        endcase
    end
endmodule
```

#### Avoiding  latches

```verilog
module top_module (
    input [15:0] scancode,
    output reg left,
    output reg down,
    output reg right,
    output reg up  ); 
    always @ * begin
        case(scancode)
            16'he06b:   {left, down, right, up} = 4'b1000;
            16'he072:	{left, down, right, up} = 4'b0100;
            16'he074:	{left, down, right, up} = 4'b0010;
            16'he075:	{left, down, right, up} = 4'b0001;
            default: {left, down, right, up} = 4'b0000;
        endcase
    end
    
endmodule
```

### More Verilog Features

#### Conditional ternary operator

``` verilog
module top_module (
    input [7:0] a, b, c, d,
    output [7:0] min);//

    // assign intermediate_result1 = compare? true: false;
    wire [7:0] mid1, mid2;
    assign mid1 = (a < b) ? a : b;
    assign mid2 = (c < d) ? c : d;
    assign min = (mid1 < mid2) ? mid1 : mid2;
endmodule
```

#### Reduction operators

```verilog
module top_module (
    input [7:0] in,
    output parity); 
    assign parity = ^ in;
endmodule
```

#### Reduction : Even wider gates

```verilog
module top_module( 
    input [99:0] in,
    output out_and,
    output out_or,
    output out_xor 
);
    assign out_and = & in;
    assign out_or = | in;
    assign out_xor = ^ in;
endmodule
```

#### Combinational for loop Vector reversal 2

```verilog
module top_module( 
    input [99:0] in,
    output [99:0] out
);
    integer i;
    always @* begin
        for(i = 0; i<=99; i = i+1)begin
            out[i] = in[99-i];
        end
    end
    
endmodule
```

#### Combinational for-loop 255-bit population count

```verilog
module top_module( 
    input [254:0] in,
    output [7:0] out );
    integer i;
    always @(*) begin
        out = 1'b0;
        for(i = 0; i <= 254; i = i + 1)begin
            out = out + in[i];
        end
    end
endmodule
```

#### Generate for-loop: 100-bit binary adder 2

``` verilog
module top_module( 
    input [99:0] a, b,
    input cin,
    output [99:0] cout,
    output [99:0] sum );
    integer i;
    always @* begin
        sum[0] = a[0] ^ b[0] ^ cin ;
        cout[0] = a[0] & b[0] | a[0] & cin | b[0] & cin ;
        for(i = 1;i <= 99; i = i+1)begin
            sum[i] = a[i] ^ b[i] ^ cout[i-1];
            cout[i] = a[i] & b[i] | a[i] & cout[i-1] | b[i] & cout[i-1] ; 
        end
    end
    
endmodule
```

- Second Edtion

```verilog
module top_module( 
    input [99:0] a, b,
    input cin,
    output [99:0] cout,
    output [99:0] sum );
    integer i;
    always@(*)begin
        {cout[0],sum[0]} = a[0]+b[0]+cin; 
        for(i=1;i< 100;i=i+1)begin
            {cout[i],sum[i]} = a[i]+b[i]+cout[i-1];
        end
    end
endmodule
```

#### Generate for-loop: 100-digit BCD adder

```verilog
module top_module( 
    input [399:0] a, b,
    input cin,
    output cout,
    output [399:0] sum );
    wire [99:0] cout_1;
    bcd_fadd fadd0(.a(a[3:0]), .b(b[3:0]), .cin(cin), .cout(cout_1[0]), .sum(sum[3:0]));
    genvar i;
    generate
        for(i = 1; i < 100; i = i + 1)
            begin: adder1
                bcd_fadd fadd1(.a(a[i*4+3:i*4]), .b(b[4*i+3:4*i]), .cin(cout_1[i-1]), .cout(cout_1[i]), .sum(sum[3+4*i:i*4]));
            end
    endgenerate
    assign cout = cout_1[99];
endmodule
```

- `always`是`Verilog`中的一种结构，用来描述一个永久的状态机，其中的语句会一直保持执行，而`generate`是一种可以生成多个实例的结构。
