# Basics
 Ideally k-stage piepelining should improve performance by a factor of K
 slowest stage determines the clock cycle
 -> we want all cycles to be balanced
  >[!info] RISC has a 5 stage pipeline but she mentioned something about others having around 15 "*deeper pipelines*"

>[!warning] Reading the Registers happens at the instruction decode not the Execution stage

>[!warning] Even though all instructions have to go through all 5 stages,  it seems Branching instruction only requires 3 cycles and Store only 4
>The instruction still goes through all the stages but It does not use them. 
>-> The instruction is considered *Done* when it finishes all stages, even the ones that don't *do useful work for it*

>[!success] The *Branch instruction* doesn't need the *Memory access and Write-back* stage. however it still goes through them. 
>*Keep in mind the writing back to the Program counter does not count as a Write-Back stage*


