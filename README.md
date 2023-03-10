# wiscv-sp23

RISCV based inorder 5 stage processor + cache design project. 

The project has the following hierarchy
             
             1.nopipe/src -> Implement unpipelined design here
             2.pipe/src -> Implement 5 stage pipelined design here
             3.withcache/src -> Implement cache system and integrate pipelined processor designed above + cache system
             
Each of the above design folders has the following hierarchy. 

            1. proc.sv -> Top Level Design Module. Contains instruction, data memory instances and the core.
            2. core.sv -> Implement the processor design in this file
  
  
Environment :
          Add RISCV toolchain, Modelsim to your PATH variable. Add the following commands in .bashrc/.bashrc.local
          
                    export PATH=$PATH:/p/vertical/projects/552RISCV/riscv_toolchain_install/bin
                    export MGC_HOME=/s/mentor-2018/@sys/V10.0BSXE/MGC_HOME.ixl
                    export MGLS_LICENSE_FILE=/s/mentor-2018/etc/cust/mgls/mgc.licenses
                    export PATH=$PATH:/s/mentor-2018/@sys/bin:/s/mentor-2018/@sys/bin.pclinux:/s/mentor-2018/@sys/V10.0BSXE/MGC_HOME.ixl/bin:/s/mentor-2018/@sys/modelsim_dlx/bin

TB Environment is located in ./tb directory. To run any testcase.

             1. cd tb
             2. make all PROG=<program name> MODE=<pipe,nopipe,nodut> MEM_MODE=<fixed,variable> RUN_DIR=<directory where simulation run files are created>
             
             MODE = nopipe -> picks design files from nopipe/src directory
             MODE = pipe -> picks design files from pipe/src directory
             MODE = nodut -> Runs in NO DUT mode. That is instructions are executed every cycle by the test bench simulator and Reg/Mem Trace is created
             
             MEM_MODE = fixed -> Runs the design in 1 cycle latency memory model. Reads happens immediately. Writes take 1 cycle
             MEM_MODE = variable -> Runs the design in variable latency memory model. Both Reads, Writes are of variable latency. 
             The design has to look in */src/core.sv at i_icache_done signal for Instruction Memory, i_dcache_done signal for Data Memory acknowlegments.
 
 TRACES : 
          Each of the test run creates traces for the designer to assist in debug.
          
          1. MODE=pipe -> Creates REF.pTrace, DUT.pTrace (p stand for pipelining)
          2. MODE=nopipe -> Creates REF.Trace, DUT.Trace
          3. MODE=nodut -> Created REF.Trace
          
          REF Trace : Reference/Golden Trace from the testbench simulator. 
          DUT Trace : Trace Captured from the design using probes in tb/dut_probes.sv.
          
 ERRORS : 
         Apart from generating reference traces, TB does register file comparisons between TB register file and design register file on every architectural commit. If there are any errors in your design, these comparisons give "ERROR" messages in ${RUN_DIR}/${PROG}_run.log (Note these variables RUN_DIR, PROG are given as arguments to "make all" command as described above)
         
        Example ERROR Message :
        
        # @               14500 cycle_count =         10 ERROR : Reg values mismatch at index 5 ; Expected : 0, Actual : 1 current_pc : 18 old_pc : 14


                          timestamp = 14500 units
                          cycle_count = 10 (you can use timestamp and wiscv_tb.cycle_count variables to arrive at the failure point in timestamp)
                          cycle_count is a free running 32 bit counter increments by 1 on every clock cycle posedge. 
                          index = 5 (Register Index is 5 . That is x5 has incorrect value) 
                          Expected = 0 (Expected value of x5 as per TB simulator is 0)
                          Actual = 1 (Observed value of x5 in DUT register is 1)
                          current_pc = 18 (current pc of the instruction in write back stage : Yet to be committed)
                          old_pc = 14 (PC value of instruction which got committed)


Waves Dump :
           add "WAVES=1" to the make command used to dump waves in ${RUN_DIR}. "vsim.wlf" is the name of waveform created.
          
          Command to open Waves : 
                                
                                vsim vsim.wlf&
          
 
         
 
             
