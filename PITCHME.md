---?image=assets/images/gitpitch-audience.jpg
@title[EDK_II_Porting_Projects_pres]
<br><br><br><br><br>
## <span class="gold"   >UEFI & EDK II Training</span>

#### Porting Beyond the Shell 

<br>
<span style="font-size:0.75em" ><a href='http://www.tianocore.org'>tianocore.org</a></span>
Note:
  PITCHME.md for UEFI / EDK II Training  Porting Beyond the Shell 

  Copyright (c) 2018, Intel Corporation. All rights reserved.<BR>

  Redistribution and use in source (original document form) and 'compiled'
  forms (converted to PDF, epub, HTML and other formats) with or without
  modification, are permitted provided that the following conditions are met:

  1) Redistributions of source code (original document form) must retain the
     above copyright notice, this list of conditions and the following
     disclaimer as the first lines of this file unmodified.

  2) Redistributions in compiled form (transformed to other DTDs, converted to
     PDF, epub, HTML and other formats) must reproduce the above copyright
     notice, this list of conditions and the following disclaimer in the
     documentation and/or other materials provided with the distribution.

  THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
  IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
  MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
  EVENT SHALL TIANOCORE PROJECT  BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
  PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
  OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
  ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.



---  
@title[Lesson Objective]
<BR>
### <p align="center"<span class="gold"   >Lesson Objective </span></p><br>

<!---  Add bullets using https://fontawesome.com/cheatsheet certificate
-->
<ul style="list-style-type:none">
 <li>@fa[certificate gp-bullet-green]<span style="font-size:0.9em">&nbsp;&nbsp;Locate driver locations for  porting EDK II modules<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; beyond the UEFI Shell for the New Project Platform </span> </li>
 <li>@fa[certificate gp-bullet-cyan]<span style="font-size:0.9em">&nbsp;&nbsp;Determine the protocols then the UEFI Driver<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; with Devices on a platform</span></li>
 <li>@fa[certificate gp-bullet-yellow]<span style="font-size:0.9em">&nbsp;&nbsp;The goal is to boot to the OS</span></li>
</ul>



---
@title[Features Needed to Access OS]
<p align="right"><span class="gold" ><b>Features Needed to Access OS</b></span></p>
@snap[north-west span-90 fragment]
<br>
<p style="line-height:50%" ><br>&nbsp;</p>
<p style="line-height:75%" align="left"><span style="font-size:0.9em; font-weight: bold;">&nbsp;&nbsp;Add-in Card/ UEFI Driver Related</span></p>
@box[bg-royal text-white rounded my-box-pad2  ](<p style="line-height:70%" ><span style="font-size:0.8em; font-weight: bold;" >&nbsp;&nbsp;USB&nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;LAN <br> &nbsp;&nbsp;IDE/SATA &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Graphics <br> &nbsp;&nbsp;Integrated PCI Devices <br>&nbsp;</span></p>)
<br>
@snapend

@snap[north-west span-90 fragment]
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<p style="line-height:50%" ><br><br><br>&nbsp;</p>
<p style="line-height:75%" align="left"><span style="font-size:0.9em; font-weight: bold;">&nbsp;&nbsp;Platform Related DXE Driver Related </span></p>
@box[bg-purple-pp text-white rounded my-box-pad2  ](<p style="line-height:70%" ><span style="font-size:0.8em; font-weight: bold;" >SMM &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ACPI <br>ACPI S3&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;BDS&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;&nbsp;CSM &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SMBIOS <br>&nbsp;</span></p>)
<br>
@snapend

Note:

- Add-in Card/ UEFI Driver Related
  - USB
  - LAN
  - IDE/SATA
  - Graphics 
  - Integrated PCI Devices 

- Platform Related DXE Driver Related
  - SMM
  - ACPI
  - ACPI S3
  - BDS
  - CSM
  - SMBIOS




---?image=assets/images/binary-strings-black2.jpg
@title[Add-in Card Section]
<br><br><br><br><br><br><br>
### <span class="gold"  >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Add-in Card</span>
<span style="font-size:0.9em" >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Locate UEFI Drivers related to Add-in Cards</span>
 


---?image=/assets/images/slides/Slide7.JPG
@title[Protocol Stack: IDE/SATA]
<p align="right"><span class="gold" ><b>Protocol Stack: IDE/SATA</b></span></p>


Note:
 
- This is the protocol stack for the IDE/Sata interface. 

- First thing is to initialize the IDE/Sata controller. The driver checks to see if it supports the controller by verifying the Device/Vendor ID.

- An IDE/Sata Bus driver will then scan each channel of the controller using the published interface and if the media device is supported, then the driver will install a raw or Block I/O protocol on to device handle This allows the entire media device to be accessed with this protocol. Once that is installed the Disk I/O driver will install a Disk I/O protocol on top of the Block I/O handle. The Disk I/O protocol provides an interface that is not block/sector aligned for the media device.

- The partition driver will look at each block I/O device to see if a recognizable partition is on the device. If one is found, it will then attach a logical Block I/O protocol for the raw access of the partition on the device. A Disk I/O protocol is then installed on the new logical Block I/O protocol. Currently supported partition types are MBR, GPT and El-Torito.

- Finally a file system protocol is installed if the partition contains a FAT file system. This provides file operations to the media device. 



---?image=/assets/images/slides/Slide9.JPG
@title[Driver Stack: IDE/SATA]
<p align="right"><span class="gold" ><b>Driver Stack: IDE/SATA</b></span></p>


Note:
- Color indicates the likelihood of change requirement based on choosing a good platform to port

- This is the block diagram of the files required for porting. 

- As you see the only necessary drivers to port are the ones that deal directly with the IDE/Sata controller and the PCI interface. 
Currently our reference drivers support both PATA and SATA.



---?image=/assets/images/slides/Slide11.JPG
@title[Protocol Stack: USB]
<p align="right"><span class="gold" ><b>Protocol Stack: USB</b></span></p>


Note:

Notice that these protocols are Industry standard


---?image=/assets/images/slides/Slide13.JPG
@title[Driver Stack: USB]
<p align="right"><span class="gold" ><b>Driver Stack: USB</b></span></p>


Note:

Notice that these drivers are Industry standard and probably will not need porting




---?image=/assets/images/slides/Slide15.JPG
<!-- .slide: data-transition="none" -->
@title[Protocol Stack: Network]
<p align="right"><span class="gold" ><b>Protocol Stack: Network<b></span></p>


Note:
- These are all protocols associated with the Network and mananging the data to/from the network.
- As you can see these are industry standard
- The intercept depends on the NIC card manufacturer on the platform
- Intel NIC cards are the stack on the Left



+++?image=/assets/images/slides/Slide16.JPG
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="none" -->
@title[Protocol Stack: Network 02]
<p align="right"><span class="gold" ><b>Protocol Stack: Network</b></span></p>


Note:

- These are all protocols associated with the Network and mananging the data to/from the network.
- As you can see these are industry standard
- The intercept depends on the NIC card manufacturer
- Intel NIC cards are the stack on the Left


---?image=/assets/images/slides/Slide18.JPG
@title[Driver Stack: Network]
<p align="right"><span class="gold" ><b>Driver Stack: Network</b></span></p>


Note:

- Again for the industry standard driver, they are already supported from the core EDK II 
- The platform port would be to pick the appropriate driver(s) specific for the NIC card on the platform. Typically this is an addin Binary (EFI) or open source code
- For Intel it is the UndiRuntimDxe driver located in the OptionRomPkg

- EDK II Modules
  - MdeModulePkg/Universal/Network/*
  - NetworkPkg/* (IPV6)
  - OptionRomPkg/UndiRuntimeDxe (Intel NIC)

 

---?image=/assets/images/slides/Slide20.JPG
@title[Protocol & Driver Stack: Graphics]
<p align="right"><span class="gold" ><b>Protocol and Driver Stack: Graphics</b></span></p>


Note:
- Mostly generic except for the low level device from a 3rd party
- typically the low level GOP (graphics Output Protocol) is via a added binary driver from the manufacturer
- From Intel it is a IGD binary module
- EDK II Modules
  - MdeModulePkg/Universal/Console/ . . .
  - IntelFrameworkModulePkg/Csm/BiosThunk/VideoDxe
  - OptionRomPkg/CirrusLogic5430Dxe
  - <Misc>Pkg/GopDriver/IntelGopDriver . . . 
		  - Intel GOP Driver (IGD) 
  - MAX  gop binary  - (workspace)\silicon\Vlv2MiscBinariesPkg\GOP


---
@title[Integrated PCI Devices: Option ROMs]
<p align="right"><span class="gold" ><b>Integrated PCI Devices: Option ROMs</b></span></p>


@snap[north-west span-20  ]
<br>
<br>
<br>
<br>
@fa[certificate fa-2x gp-bullet-cyan]
<br>
<br>
<br>
<br>
@fa[certificate fa-2x gp-bullet-yellow]
<br>
@snapend

@snap[north-east span-80 fragment ]
<br>
<br>
<br>
<br>
<p style="line-height:75%" align="left"><span style="font-size:0.9em">UEFI Device Driver Model- <br>@size[0.6em](See Section 2.6.3 of UEFI Specification <br>Follow the UEFI Driver Binding Protocol)
</span></p>
<br>
@snapend


@snap[north-east span-80 fragment ]
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<p style="line-height:75%" align="left"><span style="font-size:0.9em">EDK II Modules</span><br>
<span style="font-size:0.65em; font-family:Consolas;">OptionRomPkg/UndiRuntimeDxe 
&nbsp;&nbsp;&nbsp;&nbsp; 
 </span> </p>  
<br>
@snapend



Note:
- These are onboard PCI devices
- UEFI Device Driver Model- See Section 2.6.3 of UEFI Specification Follow the UEFI Driver Binding Protocol
- EDK II Modules Example - OptionRomPkg/UndiRuntimeDxe 
    - like the Intel Nic and IGD Drivers

- Drivers can be implemented by platform firmware developers to support buses and devices in a specific platform. Drivers can also be implemented by add-in card vendors for devices that might be integrated into the platform hardware or added to a platform through an expansion sl 


---?image=assets/images/binary-strings-black2.jpg
@title[Platform DXE Drivers Section]
<br><br><br><br><br><br><br>
### <span class="gold"  >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Platform DXE Drivers  </span>
<span style="font-size:0.9em" >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>

 
---
@title[SMM]
### <p align="right"><span class="gold" >SMM Related</span></p>

@snap[north-west span-100 ]
<br>
<br>
@box[bg-royal text-white rounded my-box-pad2  ](<p style="line-height:70%" ><span style="font-size:0.9em;" >Intel typically provides SMM Driver code<br>@size[.6em](You're only require to provide platform specific SMI handlers<br>&nbsp;)</span></p>)
<br>
@snapend

<br>
<br>
<br>
<p style="line-height:85%" align="left"><span style="font-size:0.9em;" >Base Protocol <br></span>
<span style="font-size:0.65em; font-family:Consolas;" > /@color[#ff4747](IA32FamilyCpuPkg)/SmmBase/SmmBase.inf
</span></p>

<p style="line-height:85%" align="left"><span style="font-size:0.9em;" >Access Protocol (SMRAM)<br></span>
<span style="font-size:0.65em; font-family:Consolas;" > /@color[#ff4747](&lt;MemCntlX&gt;Pkg)/SmmAccessDxe/SmmAccessDxe.inf
</span></p>


<p style="line-height:85%" align="left"><span style="font-size:0.9em;" >Control Protocol (SMI)<br></span>
<span style="font-size:0.65em; font-family:Consolas;" > /@color[#ff4747](&lt;PchX&gt;Pkg)/SmmControlDxe/SmmControlDxe.inf
</span></p>

@snap[south-east span-100 ]
@box[bg-black text-white my-box-pad2  ](<p style="line-height:40%" align="left"><span style="font-size:0.450em; font-family:Consolas; " >&nbsp;&nbsp;MinnowBoard Max<br>&nbsp;&nbsp;&nbsp;&nbsp;  - &lt;Access&gt; Vlv2DeviceRefCodePkg/ValleyView2Soc/CPU<br>&nbsp;&nbsp;&nbsp;&nbsp;  - &lt;Control&gt; Vlv2DeviceRefCodePkg/ValleyView2Soc/SouthCluster<br>&nbsp;&nbsp;</span></p>)
@snapend

Note:
- Base Protocol
  - Responsible for Processor state (in SMM) initialization and allocates SMM Memory
    - /<XFamilyCpu>Pkg/SmmBase/SmmBase.inf
- Access Protocol
  - Controls the visibility of the SMRAM on the platform
    - /<MemCntlX>Pkg/SmmAccessDxe/SmmAccessDxe.inf
- Control Protocol
  - Initiates SMI/PMI activations
     - /<PchX>Pkg/SmmControlDxe/SmmControlDxe.inf

- Additional notes:
  - Get Drivers
  - CPU and Silicon code from vendors should include all required SMM drivers
  - You are required to provide platform-specific SMI handlers only
 
- MinnowBoard Max
  - <Access> Vlv2DeviceRefCodePkg/ValleyView2Soc/CPU
  - <Control> Vlv2DeviceRefCodePkg/ValleyView2Soc/SouthCluster




---?image=/assets/images/slides/Slide27.JPG
@title[ACPI]
### <p align="center"><span class="gold" >ACPI</span></p>
<br>
<br>
<br>
<ul style="list-style-type:none">
  <li><span style="font-size:01.0em" >Provides ACPI Table driver for a platform</span></li><br>
  <li><span style="font-size:01.0em" >Composed of ACPI Table Driver (generic) and ACPI Platform Driver (platform-specific)</span></li><br>
  <li><span style="font-size:01.0em" >Platform Driver runs during DXE (before BDS)</span></li><br>
</ul>

Note:

- Provides ACPI Table driver for a platform	
  - Builds FADT table from built AML tables
  - Allows platform customization (APIC, GV3, OEMID)
  - Updates the UEFI System Table with ACPI table pointers
- Composed of two drivers:
  - ACPI Table Driver (generic)
  - ACPI Platform Driver (platform-specific)
- Platform Driver runs during DXE (before BDS)


---
@title[ACPI Locations]
### <p align="right"><span class="gold" >ACPI Locations</span></p>
<ul style="list-style-type:none">  
  <li><span style="font-size:0.9em" >ACPI tables</span></li>
  <li><span style="font-size:0.7em" ><font color="#ff4747">`/NewPlatformPkg`</font>`/AcpiTablesDxe/AcpiTablesDxe.inf`</span></li><br>
  <li><span style="font-size:0.9em" >ASL code</span></li>
  <li><span style="font-size:0.7em" ><font color="#ff4747">`/NewPlatformPkg`</font>`/AcpiTablesDxe/Asl/*.asl`</span></li><br>
  <li><span style="font-size:0.9em" >Platform specifics are in the ACPI platform driver</span></li>
  <li><span style="font-size:0.7em" ><font color="#ff4747">`/NewPlatformPkg`</font>`/AcpiPlatformDxe/AcpiPlatformDxe.inf`</span></li><br>
</ul>
```
  MinnowBoard Max
   <Tables and ASL Code> Vlv2DeviceRefCodePkg/AcpiTablesPCAT
```

  
Note:

- ACPI porting consists of the following:

- ACPI tables and their corresponding Asl code is used to produce the ACPI information for the platform

- Updates are typically the OEM ID, MP info, and if mobile the processor frequencies, power states and etc.

- MinnowBoard Max
  - <Tables and ASL Code> Vlv2DeviceRefCodePkg/AcpiTablesPCAT

---
@title[ACPI S3 Additional Features]
<p align="right"><span class="gold" ><b>ACPI S3 Additional Features</b></span></p>

<ul style="list-style-type:none">  
  <li>@fa[circle gp-bullet-cyan]<span style="font-size:0.9em" >&nbsp;&nbsp;Platform Independent Modules </span></li>
  <li><span style="font-size:0.7em" >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`/IntelFrameworkModulePkg/Universal/Acpi/`... </span></li><br>
  <li>@fa[circle gp-bullet-orange]<span style="font-size:0.9em" >&nbsp;&nbsp;ScriptSave driver </span></li>
  <li><span style="font-size:0.7em" >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`MdeModulePkg/Universal/Acpi/BootScriptExecutorDxe `</span></li><br>
  <li>@fa[circle gp-bullet-blue]<span style="font-size:0.9em" >&nbsp;&nbsp;Platform Dependent Modules </span></li>
</ul>

```
PEI:                            // Check for recovery paths or just an S3 resume.
 <NewProjectPkg>/PlatformInitPei/BootMode.c   // Get S3 resume information for MRC
DXE:
 <NewProjectPkg>/PlatformDxe/Platform.c       // Set memory variable for S3 resume.
SMM:
 <NewProjectPkg>/PlatformSmm/Platform.c       // Allocate reserved ACPI memory for S3 resume
BDS:
 <NewProjectPkg>/Library/PlatformBdsLib/BdsPlatform.c:    	// Prepare S3 information
 
```



Note:

- Platform Independent Modules
  - /IntelFrameworkModulePkg/Universal/Acpi/...
- Platform Dependent Modules (see below)
- ScriptSave driver
- DXE drivers call ScriptSave protocol to save the chipset and CPU configuration in normal boot path
- The boot script engine in PEIM restores the chipset and CPU configuration done in previous DXE in S3 resume boot path
  - MdeModulePkg/Universal/Acpi/BootScriptExecutorDxe


#### PLATFORM dependant modules:
- PEI:
  - <PlatformPkg>.../PlatformPei/
     -   BootMode.c:        // Check for recovery paths or just an S3 resume.
     - MemoryCallback.c:  // Early exit for S3 resume path.
     - Platform.c:        // On S3 path, clear SMI enable bits so it will not generate an SMI.

  - <PlatformPkg>.../PlatformMemoryInit/MemoryInit.c
          - // Get S3 resume information for MRC

- DXE:
   - <PlatformPkg>.../PlatformDxe/Platform.c:  // Set memory variable for S3 resume.
   - <PlatformPkg>.../PlatformDxe/PlatformChipsetUpdate.c(100):  // We must save serial IRQ settings for S3 resume.

- BDS:
  - /Library/PlatformBdsLib/BdsPlatform.c:    // Prepare S3 information
   

---?image=/assets/images/slides/Slide33.JPG
@title[SMM/ACPI/S3 Table]
<p align="right"><span class="gold" ><b>SMM, ACPI, &S3 Table</b></span></p>

Note:
   
- Platform specific will need to be ported   

---
@title[BDS]
### <p align="center"><span class="gold" >BDS</span></p>
<br>
<ul style="list-style-type:none">
  <li><span style="font-size:01.1em" ><font color="#92d050"><b> BDS & BDS Libraries</b></font></span></li>
   <ul style="list-style-type:none">
      <li><span style="font-size:0.7em" >`/MdeModulePkg/Universal/BdsDxe`</span></li>
      <li><span style="font-size:0.7em" >`/NewPlatformPkg/Library/NewPlatformBdsLib`</span></li>
   </ul>
   <br>
  <li><span style="font-size:01.1em" ><font color="#92d050"><b>Areas of Concern </b></font></span></li>
  <ul style="list-style-type:disc">
   <li><span style="font-size:0.7em" >Console Settings</span></li>
   <li><span style="font-size:0.7em" >Language strings are contained in .UNI file</span></li>
   <li><span style="font-size:0.7em" >Extended Memory Testing</span></li>
 </ul>

</ul>

Note:
- Porting is centralized in one bds file. 

- You will need to identify what the language is desired. Based on the language desired, you will want to direct the HII for different keyboard mappings and output displayed.

- Checks are made for what type of console is desired – either serial or GOP/VGA or both. These settings come typically from NVRAM or hard codings.

- Type of memory test desired. While PEI performs a small one for the amount of RAM needed for PEI/DXE, the check of all the memory found during PEI/DXE is checked here. Once again the type of memory testing done is platform specific. This test can be sparse or rigorous.


---?image=/assets/images/slides/Slide37.JPG
@title[Compatibility Support Module (CSM)]
<p align="right"><span class="gold" >Compatibility Support Module (CSM)</span></p>

Note:
- CSM 32 Component 
  - PcAtChipsetPkg/8259InterruptControllerDxe
    - Used to manipulate the standard 8259 chipset
  - IntelFrameworkModulePkg/Csm 
     - Thunk & Reverse Thunk
     - Legacy BIOS region support
     - Legacy BIOS DXE/BDS phase support
     - Plus other pieces of legacy support

- Platform Specific
  - Legacy chipset support
    - <RefCode>Pkg/<SoC>/SouthCluster/LegacyInterrupt/Dxe
  - Platform specifics (i.e. MP tables)
     - <RefCode>Pkg/<SoC>/CPU/CpuInit/Dxe/MpCommon 
  - Video ROM (may be platform specific)
    - NewPlatformPkg/PciPlatform


- MinnowBoard Max
      - <RefCode>Pkg/<SoC>:   Vlv2DeviceRefCodePkg/ValleyView2Soc
  

---?image=/assets/images/slides/Slide39.JPG
@title[SMBIOS]
### <p align="center"><span class="gold" >SMBIOS</span></p>


Note:
- Memory Information 
  - Physical memory Array (type 16), Memory Device (type 17), Memory Array Mapped Address (Type 19) and Memory Device Mapped Address (Type 20) 
  - are produced by Memory Subclass driver

- If the memory DIMMs supported in the new platform change from the CRB, those memory slot locations and numbers should be updated in porting
  - <MemCntlX>Pkg/MemorySubClassDxe 


- Processor Information
  - Typically does not require porting
  - Processor Information (type 4) and Cache Information (type 7) are produced by CPU driver 
    - <XFamilyCpu>Pkg/CpuMpDxe/SMBIOS
- Platform Miscellaneous Information
  - Other info is produced by the MiscSubClass driver
  - BIOS Information (type 0), System Information (type 1), System Enclosure (type 3), System Slots (type 9)
     - NewPlatformPkg/SmbiosMiscDxe


---?image=/assets/images/slides/Slide41.JPG
@title[BDS/CSM/SMBIOS Table]
<p align="right"><span class="gold" >BDS, CSM and SMBIOS Table</span></p>

Note:



  
---  
@title[Summary]
<BR>
### <p align="center"><span class="gold"   >Summary </span></p><br>
<ul style="list-style-type:none">
 <li>@fa[certificate gp-bullet-green]<span style="font-size:0.9em">&nbsp;&nbsp;Locate driver locations for  porting EDK II modules<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; beyond the UEFI Shell for the New Project Platform </span> </li>
 <li>@fa[certificate gp-bullet-cyan]<span style="font-size:0.9em">&nbsp;&nbsp;Determine the protocols then the UEFI Driver<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; with Devices on a platform</span></li>
 <li>@fa[certificate gp-bullet-yellow]<span style="font-size:0.9em">&nbsp;&nbsp;The goal is to boot to the OS</span></li>
</ul>


---?image=assets/images/gitpitch-audience.jpg
@title[Questions]
<br>
![Questions](/assets/images/questions.JPG) 


---?image=assets/images/gitpitch-audience.jpg
@title[Logo Slide]
<br><br><br>
![Logo Slide](/assets/images/TianocoreLogo.png =10x)


---
@title[Acknowledgements]
#### <p align="center"><span class="gold"   >Acknowledgements</span></p>

```c++
/**
Redistribution and use in source (original document form) and 'compiled' forms (converted
to PDF, epub, HTML and other formats) with or without modification, are permitted provided
that the following conditions are met:

Redistributions of source code (original document form) must retain the above copyright 
notice, this list of conditions and the following disclaimer as the first lines of this 
file unmodified.

Redistributions in compiled form (transformed to other DTDs, converted to PDF, epub, HTML
and other formats) must reproduce the above copyright notice, this list of conditions and 
the following disclaimer in the documentation and/or other materials provided with the 
distribution.

THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR IMPLIED 
WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND 
FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL TIANOCORE PROJECT BE 
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES 
(INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, 
DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, 
WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) 
ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF ADVISED OF THE POSSIBILITY 
OF SUCH DAMAGE.

Copyright (c) 2018, Intel Corporation. All rights reserved.
**/

```
