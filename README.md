# Zynq_PMU_Setup

**If you have not already follow this link https://www.hackster.io/adam-taylor/getting-started-with-the-zuboard-vivado-and-vitis-13483e to learn how to set up Vivado and Vitis before starting this**
# __________________________________________________________________________

# 1) To start open Vivado and create your block diagram and double click on the block
**Go to I/O Configuration --> Low Speed --> I/O Peripherals --> PMU**

**You can click on all of the GPI and GPO blocks and use the board manual to see what MIO they are connected to**

**for this we only need GPI5 which is SW4_4 and GPO1 which is LED4**

<img width="1154" height="743" alt="image" src="https://github.com/user-attachments/assets/fcdb17d1-c006-4ace-859a-bfb990bfb3bb" />

****
**Continue as you normally would in Vivado, Wrappper --> Bitstream --> Export Hardware --> Open Vitis**

# 2) Now you should have Vitis open with your Vivado file

**Now after you create an application and get to the first screen where you have to pick your file_wrapper.xsa**

**Select your wrapper block design and for boot components you can select psu_cortexr5_0**

**Technically you can select either, but 5_0 is RPU and 53_0 is APU, for this project we are only using the PMU**

<img width="975" height="765" alt="image" src="https://github.com/user-attachments/assets/eabde86e-f529-45cd-afad-91ea97a5aee0" />

# 3) Now you should be on the Application Project Details screen

**Set your application name and select psu_pmu_0 as your Processor then continue**

<img width="975" height="776" alt="image" src="https://github.com/user-attachments/assets/9bc91c43-d2d5-4f2e-8b62-6ae9dd518cc3" />

****
**On the next screen select ZynqMP PMU Firmware then click Finish**

<img width="975" height="769" alt="image" src="https://github.com/user-attachments/assets/11822579-1a31-4c26-ae02-20a5c02901f7" />

# 4) Now we have the Vitis application open, you can click on build and debug and continue

# 5) Project Properties

**Go to your project in the Explorer tab and click on Properties**

<img width="780" height="805" alt="image" src="https://github.com/user-attachments/assets/5482866e-4b01-4040-9a7e-468c22519dc9" />

****
**Now go to C/C++ Build --> Settings --> Symbols**

<img width="975" height="1010" alt="image" src="https://github.com/user-attachments/assets/58186174-e1d8-4159-92d1-135cb319a860" />

*****
**Click on the add symbol and add ENABLE_CUSTOM_MOD**

<img width="975" height="568" alt="image" src="https://github.com/user-attachments/assets/3d7203be-020c-43fa-bf2f-a6c2fae5ede1" />

# 6) Debug Configuration

**Go to the dropdown on the debugger at the top and click on Debug Configurations**

<img width="975" height="556" alt="image" src="https://github.com/user-attachments/assets/4d30f3ce-2665-4b46-9844-377f524b0db9" />

****
**In the configuration click on Single Application Debug (GDB) and uncheck "Stop and 'main'" and click apply**

<img width="975" height="803" alt="image" src="https://github.com/user-attachments/assets/c1417e0b-28f3-40ac-8a78-c8fc0bf99ebc" />

# 7) Create .h file

**In the Explorer tab open the dropdown under your project and add the .h file**

<img width="975" height="931" alt="image" src="https://github.com/user-attachments/assets/98e32cef-ad3e-49dc-afd7-4237367a0319" />

<img width="799" height="928" alt="image" src="https://github.com/user-attachments/assets/aac9f255-49d5-46c0-91da-b45e0a02d79d" />

****
**Now open the .h file and add this**
```instuction
#ifndef XPFW_MOD_CUSTOM_H
#define XPFW_MOD_CUSTOM_H

void ModCustomInit(void);

#endif
```

# 8) Create .c file

**The same way the .h was created we need a .c file too**

<img width="791" height="916" alt="image" src="https://github.com/user-attachments/assets/35146c20-36dd-4147-a841-49f4033fc35a" />

****
**In the .c file you can add this**

```instuction
#include "xpfw_mod_custom.h"
#include "xpfw_default.h"
#include "xpfw_core.h"
#include "xpfw_module.h"
#include "sleep.h"
static const XPfw_Module_t *CustomModPtr;

static void CustomTask(void)
{
	Xil_Out32(0xFFD40014, Xil_In32(0xFFD40014) | (1U << 1U)); //LED ON
	usleep(200000);
	Xil_Out32(0xFFD40014, Xil_In32(0xFFD40014) & ~(1U << 1U)); //LED OFF
}
                    // (points to the custom PMU module, points to the config data(Not used for LED), Lenght/amount of config data in CfgData)
static void CustomCfgInit(const XPfw_Module_t *ModPtr, const u32 *CfgData, u32 Len)
{
    s32 Status; // 32 bit signed int

                                //(pointer, 1000ms -> 1s, function call)
    Status = XPfw_CoreScheduleTask(ModPtr, 1000U, CustomTask); // PMU firmware for scheduling a task

    if (XST_FAILURE == Status) {
        XPfw_Printf(DEBUG_ERROR,"CUSTOM: Scheduling task failed\r\n");
    }
}

void ModCustomInit(void) //What the PMU has to run from .h file
{
    CustomModPtr = XPfw_CoreCreateMod(); // Creates a module for the PMU

    if (XST_FAILURE == XPfw_CoreSetCfgHandler(CustomModPtr, CustomCfgInit)) { //Calls Function and checks for failures too

        XPfw_Printf(DEBUG_ERROR, "CUSTOM: Set Cfg handler failed\r\n");
    }
}
```

# 9) Final step

**go to src--> xpfw_user_startup.c and add include the .h file you created**

<img width="517" height="484" alt="image" src="https://github.com/user-attachments/assets/0ea0a856-213b-4d2f-b072-5e2f6df5e2e5" />

<img width="975" height="650" alt="image" src="https://github.com/user-attachments/assets/ba10fc18-e0d8-4a3e-9c80-504bf6948954" />

# 10) Build and Run



