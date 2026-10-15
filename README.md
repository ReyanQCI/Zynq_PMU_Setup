# Zynq_PMU_Setup

**If you have not already follow this link https://www.hackster.io/adam-taylor/getting-started-with-the-zuboard-vivado-and-vitis-13483e to learn how to set up Vivado and Vitis before starting this**
# __________________________________________________________________________

# To start open Vivado and create your block diagram and double click on the block
**Go to I/O Configuration --> Low Speed --> I/O Peripherals --> PMU**

**You can click on all of the GPI and GPO blocks and use the board manual to see what MIO they are connected to**

**for this we only need GPI5 which is SW4_4 and GPO1 which is LED4**

<img width="1154" height="743" alt="image" src="https://github.com/user-attachments/assets/fcdb17d1-c006-4ace-859a-bfb990bfb3bb" />

****
**Continue as you normally would in Vivado, Wrappper --> Bitstream --> Export Hardware --> Open Vitis**

# Now you should have Vitis open with your Vivado file

**Now after you create an application and get to the first screen where you have to pick your file_wrapper.xsa**

**Select your wrapper block design and for boot components you can select psu_cortexr5_0**

**Technically you can select either, but 5_0 is RPU and 53_0 is APU, for this project we are only using the PMU**

<img width="975" height="765" alt="image" src="https://github.com/user-attachments/assets/eabde86e-f529-45cd-afad-91ea97a5aee0" />

