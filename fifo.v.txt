//Designing a 16*8 fifo. That is fifo has 16 location and each location has 8 bits of data
module fifo(
  input clk, reset,
  input [7:0] data_in,
  input we, re,
  output reg [7:0] data_out,
  output full, empty
);
  reg [3:0] wr_ptr, rd_ptr;
  reg [3:0] count;
  reg [7:0] mem[15:0]; //memory declaration
  
  integer i;
  
  //assigning of outputs full and empty
  
  assign full=(count==4'd15)?1:0;
  assign empty=(count==4'd0)?1:0;
  
  //counter logic
  always@(posedge clk) begin
    if (reset)
      count<=4'd0;
    else if (we && ~full)
      count<=count+1;
    else if (re && ~empty)
      count<=count-1;
    else
    count<=count;
  end
  
  //writing operation
  always@(posedge clk) begin
    if (reset) begin
      for(i=0; i<=15; i=i+1) begin
        mem[i]<=8'b0000_0000;
        wr_ptr<=0;
      end
    end
    
    else if (we && -full) begin
      mem [wr_ptr]<=data_in;
      wr_ptr<=wr_ptr+1;
    end
      
    else
      wr_ptr<=wr_ptr;
  end
  
  //reading operation
  always@(posedge clk) begin
    if (reset) begin
      data_out<=8'b0000_0000;
      rd_ptr<=0;
    end
      
    else if (re && ~empty) begin
      data_out<=mem [rd_ptr]
      rd_ptr<=rd_ptr+1;
    end
  else begin
    data_out<=8'b0000_0000;
    rd_ptr<=rd_ptr;
  end
  
end
    
endmodule
