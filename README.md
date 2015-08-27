## How to prepare the component framework infrastructure for components 
1. Clone the installer starter kit

	 	git clone --recursive ssh://git@stash.csw.l-3com.com:7999/~pnewbold/installer_starter_kit.git

2. Change the project name, component name, and version in [INSTALLER\_BASE]/project.cmake

	 	set(PROJECT_NAME EBSHF CACHE STRING "")
	 	set(COMPONENT_NAME shf_installer CACHE STRING "")
	 	set(VERSION_MAJOR 0 CACHE STRING "")	
	 	set(VERSION_MINOR 1 CACHE STRING "")	
	 	set(VERSION_PATCH 0 CACHE STRING "")
3. Update the base directory for installation in [INSTALLER_BASE]/CMakeLists.txt 
	1. Go to the line that starts with set(CPACK_PACKAGING_INSTALL_PREFIX . . . )
	2. Set the remainder of the line to the base location of the install. Currently it is /opt/${DIVISION_DOMAIN}/${PROJECT_NAME}/${APPLICATION}/${PROJECT_VERSION} which resolves to /opt/com.l3com.csw/EBSHF/shf-installer/0.1.0.0
4. Update the pre and post install scripts 
	1. Comment out the echo in the pre_install script. Install is misspelled
	1. Comment out the echo in the post_install script. Install is misspelled
	`Change the echo referencing the CNES server to E-4B` 
5. Update the RPM metadata - the metadata is shown when the command rpm -qi EBSHF-shf_installer-0.1.0.0 is run 
	1. RPM defaults

		 	[root@redhatvm Desktop]# rpm -qi starter_project-starter_component-0.0.0.0
		 	Name : starter_project-starter_component Relocations: /opt/com.l-3com.csw/starter_project//0.0.0.0
		 	Version : 0.0.0.0 Vendor: L-3 Communications CSW
		 	Release : 1 Build Date: Tue 25 Aug 2015 02:12:20 PM MDT
		 	Install Date: Tue 25 Aug 2015 02:14:38 PM MDT Build Host: kodiak
		 	Group : unknown Source RPM: starter_project-starter_component-0.0.0.0-1.src.rpm
		 	Size : 17320747 License: unknown
		 	Signature : (none)
		 	Summary : starter_project-starter_component
		 	Description :
		 	DESCRIPTION
		 	===========
		 	This is an installer created using CPack (http://www.cmake.org). No additional installation instructions provided.
	1. These can be changed in CMakeLists.txt by changing the set( . . . ) commands where the first argument starts with CPACK_RPM.
	1. Set the RPM license with the line "set(CPACK_RPM_PACKAGE_LICENSE "License Description here")" inside the elseif(LINUX) section of the CPack options
	1. Set the RPM description with the text "set(CPACK_RPM_PACKAGE_DESCRIPTION "E-4B SHF RPM Installer)" inside the elseif(LINUX) section of the CPack options
	1. When complete, the RPM metadata modifications should be reflected by the "rpm -qip [RPM file path]" command

		 	Name : ebshf-shf_installer Relocations: /opt/com.l-3com.csw/ebshf//1.0.0.0
		 	Version : 1.0.0.0 Vendor: L-3 Communications CSW
		 	Release : 1 Build Date: Tue Aug 25 15:14:24 2015
		 	Install Date: (not installed) Build Host: kodiak
		 	Group : unknown Source RPM: ebshf-shf_installer-1.0.0.0-1.src.rpm
		 	Size : 17320747 License: L-3 License
		 	Signature : (none)
		 	Summary : ebshf-shf_installer
		 	Description : E-4B SHF RPM Installer
1. Add uninstall script
	1. In [INSTALLER_BASE]/parts/scripts add the script post_uninstall.
	1. Inside the script add the following lines:

		 	#!/bin/bash
		 	rm -f $RPM_INSTALL_PREFIX/database.db
		 	rm -rf $RPM_INSTALL_PREFIX/start.sh
		 	rm -rf $RPM_INSTALL_PREFIX/../LATEST
	1. Add the following line to [INSTALLER_BASE]/CMakeLists.txt

		 	set(CPACK_RPM_POST_UNINSTALL_SCRIPT_FILE ${CMAKE_SOURCE_DIR}/parts/scripts/post_uninstall) 
1. Create the RPM 
	1. cd [INSTALLER_BASE] 
	1. make clean; make arch
	1. The last CPack status line tells where the RPM was created. A sample line is CPack: - package: /home/advtech1/rlwells/git/installer_starter_kit/artifacts/com.l-3com.csw/ebshf-shf_installer/0.1.0.0/Linux-2.6-gcc4.4.5-x86_64/release/ebshf-shf_installer-0.1.0.0-Linux-2.6-gcc4.4.5-x86_64-release.rpm generated.
1. Installing the RPM from a Window 7 computer 
	1. Verify putty is installed, including pscp and plink at "c:\Program Files (x86)\PuTTY\"
	1. Copy E4BSatcomInstaller.bar, E4BSatcomUninstaller.bat, install_scripts.sh, and uninstall_script.sh from the Win7InstallScripts folder in the SHF repository in Stash into a folder on the desktop
	1. Update the version in the scripts to match the version of the rpm that is being installed
	1. Run the scripts inputting the IP and root password when requested

		 	C:\Users\rlwells\Desktop\E-4B Install>E4BSatcomInstaller.bat
		 	Enter GMS IP address (192.168.2.2): 192.168.101.9
		 	Enter the root password: gms4sbc
		 	Transferring files
		 	Installing files
		 	cd /opt/com.l-3com.csw/ebshf//0.1.0.0
		 	Installing E-4B SHF
		 	/opt/com.l-3com.csw/ebshf/0.1.0.0
		 	Complete

		 	C:\Users\rlwells\Desktop\E-4B Install>E4BSatcomUninstaller.bat
		 	Enter GMS IP address (192.168.2.2): 192.168.101.9
		 	Enter the root password: gms4sbc
		 	Uninstalling files
		 	Post uninstall script
		 	cd /opt/com.l-3com.csw/ebshf//0.1.0.0
		 	Complete

 
1. Running the component framework infrastructure 
	1. Currently this requires access to a terminal
	1. cd /opt/com.l-3com.csw/ebshf/0.1.0.0
	1. ./start.sh database.db

		 	Sample Output on Start-up for two test components 
		 	[root@redhatvm 1.0.0.0]# ./start.sh database.db
		 	[22:24:42.2574] (0x7f163bf94720) Information: ./start built on: Tue Mar 31 14:03:35 2015 (using database: "database.db")
		 	[22:24:42.2585] (0x7f163bf94720) Information: Launching Component Service ...
		 	[22:24:42.5404] (0x7f163bf94720) Information: Launched Richard Satsim Service in library ebshf_satsim_service.
		 	[22:24:42.5415] (0x7f163a913700) Debug: IDL::Component_framework::State_change (subscriber ID: 2) DataReader: Subscription matched
		 	[22:24:42.5416] (0x7f163a913700) Debug: IDL::Component_framework::State_change (subscriber ID: 5) DataReader: Subscription matched
		 	[22:24:42.5417] (0x7f163a913700) Debug: IDL::Component_framework::State_change (subscriber ID: 8) DataReader: Subscription matched
		 	[22:24:42.5424] (0x7f163a913700) Debug: (subscriber ID: 0) DataReader: Subscription matched
		 	[22:24:42.5424] (0x7f163a913700) Debug: (subscriber ID: 0) DataReader: Subscription matched
			[22:24:42.5425] (0x7f163a913700) Debug: (subscriber ID: 0) DataReader: Subscription matched
			[22:24:42.5425] (0x7f163a913700) Debug: (subscriber ID: 0) DataReader: Subscription matched
			[22:24:42.5436] (0x7f163a913700) Debug: (subscriber ID: 0) DataReader: Subscription matched
			[22:24:42.5436] (0x7f163a913700) Debug: (subscriber ID: 0) DataReader: Subscription matched
			[22:24:42.5437] (0x7f163a913700) Debug: (subscriber ID: 0) DataReader: Subscription matched
			[22:24:42.5452] (0x7f163a913700) Debug: IDL::BIT::BIT_message (subscriber ID: 6) DataReader: Subscription matched
			[22:24:42.5453] (0x7f163a913700) Debug: IDL::BIT::BIT_message (subscriber ID: 11) DataReader: Subscription matched
			[22:24:42.5496] (0x7f163bf94720) Information: Launched Another Satsim Service in library ebshf_satsim_service.
			[22:24:42.5499] (0x7f163a913700) Debug: IDL::Component_framework::Mode_change (subscriber ID: 14) DataReader: Subscription matched
			[22:24:42.5499] (0x7f163a913700) Debug: IDL::Component_framework::Mode_change (subscriber ID: 14) DataReader: Subscription matched
			[22:24:42.5500] (0x7f163a913700) Debug: IDL::Component_framework::Mode_change (subscriber ID: 14) DataReader: Subscription matched
			[22:24:42.5506] (0x7f163bf94720) Information: Component Service started.

---

