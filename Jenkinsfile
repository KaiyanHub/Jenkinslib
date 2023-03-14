config_file = '\\\\shinst-vpatch.rnd.aspentech.com\\PatchWorkingFolder\\AMS\\V14.0\\config\\Config.json'
pipeline {
	agent none
	stages {
		stage ('Sync Staging') {
			agent {
				label 'IS2020' ///########Must comply with staging for build merge modules, prjects######
			}
			steps {
				script{
    				config = readJSON file : config_file
    				ProductFeild = "MES"
                    BuildFlagPath = config.get(ProductFeild).PrivateProperties.BuildFlagPath
                    echo BuildFlagPath
                    BuildFlagFile = "StagingSyncFlag.txt"
                    /*if(fileExists(BuildFlagPath)){
                        bat script: 'rd /S /Q "'+ BuildFlagPath +'"'
                    }*/
                    if(!fileExists(BuildFlagPath+BuildFlagFile)){
                        bat script: 'if not exist "' + BuildFlagPath +'" (mkdir "'+ BuildFlagPath +'")'
                        
                        /*SourcePath = config.get(ProductFeild).PublicProperties.DevStaging_x86
            			DestinationPath = config.get(ProductFeild).PublicProperties.BuildStaging_x86
                        for(subPath in config.get('MES').Staging_x86.SubFolder){
                            SourceStaging = SourcePath + subPath
                            DestinationStaging = DestinationPath +subPath +'\\'
                            try {
                                bat script: 'xcopy "' + SourceStaging + '" ' + '"'+ DestinationStaging + '" /e /y'
                            } catch (err) {
                                echo err.getMessage()
                            }
                        }
                        //build job: 'AutoCompareFolderAndCopy', parameters: [string(name: 'SourcePath', value:SourcePath),string(name: 'DestinationPath', value: DestinationPath),string(name: 'IfNeedCompareContent', value: "False")] 
                        */
            			powershell'''
                			$DiskInfo = Get-WmiObject -Class Win32_LogicalDisk 
                            foreach ($Drivers in $DiskInfo){
                                echo $Drivers.DeviceID $Drivers.ProviderName 
                                echo "--------------"
                            }
            			'''
            			SourceStaging = config.get(ProductFeild).PublicProperties.DevStaging_x86
            			DestinationStaging = config.get(ProductFeild).PublicProperties.BuildStaging_x86
            			echo SourceStaging
                         try {
                             bat script: 'xcopy "' + SourceStaging + '" ' + '"'+ DestinationStaging + '" /e /y /c'  
                         } catch (err) {
                            echo err.getMessage()
                         }
        			

                        SourceStaging = config.get(ProductFeild).PublicProperties.DevStaging_x64
            			DestinationStaging = config.get(ProductFeild).PublicProperties.BuildStaging_x64
                         try {
                            bat script: 'xcopy "' + SourceStaging + '" ' + '"'+ DestinationStaging + '" /e /y /c'  
                         } catch (err) {
                            echo err.getMessage()
                         }
                       
                        bat script: 'cd > '+BuildFlagPath+BuildFlagFile
                        
                    }else {
                        echo "Skip syncing staging files "
                    }
                }
			}
		}
		stage ('Build MergeModule') {
			agent {
				label 'IS2020' /////////CAN be changing/////////
			}
			steps {
				// Build InstallShield Project
				script{
				    
//	p4sync charset: 'none', credential: 'P4_zhangkai', populate: autoClean(delete: true, modtime: false, parallel: [enable: false, minbytes: '1024', minfiles: '1', threads: '4'], pin: '', quiet: true, replace: true, tidy: false), source: depotSource('//depot/idk_installation/V14.0/INT/AMS/Seattle/Projects')

				    //echo "Start to build merge modules..."
				    config = readJSON file : config_file
					ProductFeild = "MES"
    				Prj_x86_P4Path = config.get(ProductFeild).PrivateProperties.Prj_x86_P4Path + "MergeModules\\"
    				Prj_x64_P4Path = config.get(ProductFeild).PrivateProperties.Prj_x64_P4Path + "MergeModules\\"
    				CommonPreBuildPath = config.get(ProductFeild).PrivateProperties.CommonPreBuildPath
    				CommonReleasePath = config.get(ProductFeild).PrivateProperties.CommonReleasePath
    				CommonVbs = config.get(ProductFeild).PrivateProperties.CommonVbs
    				AllMSMs = ""
    				ISVersion = config.get(ProductFeild).PublicProperties.InstallShieldVersion
    				MsmRelocationPath = config.get(ProductFeild).PrivateProperties.MsmRelocationPath_AMS_x86
    				echo "MsmRelocationPath is " + MsmRelocationPath
    				IsmNameClass= config.get('MES').msm_x86.Name.getClass().toString() //class net.sf.json.JSONArray
    				//echo IsmNameClass 
    				//Next is for building x86 msm
    				if(IsmNameClass=="class java.lang.String"){
    				    IsmName= config.get('MES').msm_x86.Name
                        //echo IsmName
                        BuildFlagFile = BuildFlagPath + IsmName + ".txt"
                        if(!fileExists(BuildFlagFile)){
                           //echo BuildFlagFile + " doesn't exist"
                           echo "Start to build project: " + IsmName
    				       //build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                           build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                            
                        }
    				}
    				else{
    				    for($filename in config.get('MES').msm_x86.Name){
                            IsmName= $filename
                            //echo IsmName
                            BuildFlagFile = BuildFlagPath + IsmName + ".txt"
                            if(!fileExists(BuildFlagFile)){
                                //echo BuildFlagFile + " doesn't exist"
                                echo "Start to build project: " + IsmName
        				        //build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                                build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                            }
                        }
                    }
                    //Next is for building x64 msm
                    MsmRelocationPath = config.get(ProductFeild).PrivateProperties.MsmRelocationPath_AMS_x64
    				IsmNameClass= config.get('MES').msm_x64.Name.getClass().toString() //class net.sf.json.JSONArray
    				//class java.lang.String
    				//echo IsmNameClass 
    				if(IsmNameClass == "class java.lang.String"){
                        IsmName= config.get('MES').msm_x64.Name
                        //echo IsmName
                        BuildFlagFile = BuildFlagPath + IsmName + ".txt"
                        if(!fileExists(BuildFlagFile)){
                            //echo BuildFlagFile + " doesn't exist"
                            echo "Start to build project: " + IsmName
    				        //build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x64_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                            build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                        }  
                    }
    				else{
        				for($filename in config.get('MES').msm_x64.Name){
                            IsmName= $filename
                            //echo IsmName
                            PrjBuildFlag = BuildFlagPath + IsmName + ".txt"
                            if(!fileExists(PrjBuildFlag)){
                                //echo BuildFlagFile + " doesn't exist"
                                echo "Start to build project: " + IsmName
        				        //build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x64_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                                build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                            }    
                        } 
    				}
					
				//////Next is for building special msm
                    
    				IsmNameClass= config.get('MES').msm_special.Name.getClass().toString() //class net.sf.json.JSONArray
    				//class java.lang.String
    				//echo IsmNameClass 
    				if(IsmNameClass == "class java.lang.String"){
                        SpecialStr= config.get('MES').msm_special.Name.split(";")
                        PrjP4Path = SpecialStr[0]
						IsmName= SpecialStr[1]
						MsmRelocationPath = SpecialStr[2]
                        //echo IsmName
                        BuildFlagFile = BuildFlagPath + IsmName + ".txt"
                        if(!fileExists(BuildFlagFile)){
                            //echo BuildFlagFile + " doesn't exist"
                            echo "Start to build project: " + IsmName
    				        build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: PrjP4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                            //build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                        }  
                    }
    				else{
        				for($filename in config.get('MES').msm_special.Name){
							SpecialStr= $filename.split(";")
							PrjP4Path = SpecialStr[0]
    						IsmName= SpecialStr[1]
    						MsmRelocationPath = SpecialStr[2]
                            //echo IsmName
                            PrjBuildFlag = BuildFlagPath + IsmName + ".txt"
                            if(!fileExists(PrjBuildFlag)){
                                //echo BuildFlagFile + " doesn't exist"
                                echo "Start to build project: " + IsmName
        				        build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: PrjP4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                                //build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                            }    
                        } 
    				}
					
				}
			}
		}
		stage ('Build Project') {
			//agent none
			agent {
				label 'IS2020'
			}
			steps {
				script{
				    //echo "Start to build products..."
				    config = readJSON file : config_file
					ProductFeild = "MES"
    				Prj_x86_P4Path = config.get(ProductFeild).PrivateProperties.Prj_x86_P4Path
    				Prj_x64_P4Path = config.get(ProductFeild).PrivateProperties.Prj_x64_P4Path
    				CommonPreBuildPath = config.get(ProductFeild).PrivateProperties.CommonPreBuildPath
    				env.CommonReleasePath = config.get(ProductFeild).PrivateProperties.CommonReleasePath
    				echo CommonReleasePath
    				CommonVbs = config.get(ProductFeild).PrivateProperties.CommonVbs
    				AllMSMs = config.get(ProductFeild).PrivateProperties.AllMSMs
    				MsmRelocationPath = ""
    				ISVersion = config.get(ProductFeild).PublicProperties.InstallShieldVersion
    				env.BuildFlagPath = config.get(ProductFeild).PrivateProperties.BuildFlagPath
    				
    				/////Next is for building special project(ExcelAddin, ExcelAddin_x64, APRMExcelAddin_x64)/////////////////////////////////////////////////////
					//echo "APRMExcelAddin_x64"
					PrjP4Path = Prj_x64_P4Path 
                    BuildFlagFile = BuildFlagPath + "APRMExcelAddin_x64.txt"
                    if(!fileExists(BuildFlagFile)){
                        //build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: "APRMExcelAddin_x64"), string(name: 'PrjP4Path', value: PrjP4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
            			//build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: "APRMExcelAddin_x64"), string(name: 'PrjP4Path', value: PrjP4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
            			powershell '''
                			$DiskInfo = Get-WmiObject -Class Win32_LogicalDisk 
                            foreach ($Drivers in $DiskInfo){
                                    echo $Drivers.DeviceID $Drivers.ProviderName 
                                    echo "--------------"
                            }
            				$PrjRelease = ${env:CommonReleasePath} + "APRMExcelAddin_x64"
            				echo $PrjRelease
            				$msi = (Get-ChildItem $PrjRelease -Recurse -Filter *.msi).FullName
                            Write-host "msi is " $msi
                            If(-not (test-path "R:\\aspenONEV14.0\\MSC\\util\\APRMExcelAddinSetup\\SetupFile")){mkdir "R:\\aspenONEV14.0\\MSC\\util\\APRMExcelAddinSetup\\SetupFile"}
                            Copy-Item $msi "R:\\aspenONEV14.0\\MSC\\util\\APRMExcelAddinSetup\\SetupFile" -Force -ErrorAction Stop
            				
            				$pfwLocation = Get-ItemPropertyValue HKLM:\\SOFTWARE\\WOW6432Node\\InstallShield\\PackageForTheWeb\\current -Name PFTWexe
            				Start-Process -FilePath $pfwLocation -ArgumentList "R:\\aspenONEV14.0\\MSC\\util\\APRMExcelAddinSetup\\APRMExcelAddinSetup.pfw -a -s" 
            				
            				Start-Process -FilePath "C:\\Users\\buildpart\\Desktop\\Kaiyan\\StampVer.exe" -ArgumentList "-f `"18.2.0.0`" R:\\aspenONEV14.0\\MSC\\util\\APRMExcelAddinSetup\\APRMExcelAddinSetup.exe" -Verb RunAs
            				
                        '''
                        build job: 'DigitalSign', parameters: [string(name: 'SignRootFolder', value: "R:\\aspenONEV14.0\\MSC\\util\\APRMExcelAddinSetup"), string(name: 'SignLogFolder', value: "R:\\aspenONEV14.0\\MSC\\util\\SignLog"), string(name: 'FileExtension', value: '*.msi,*.exe')]
                        powershell '''
            				
            				Copy-Item "R:\\aspenONEV14.0\\MSC\\util\\APRMExcelAddinSetup\\APRMExcelAddinSetup.exe" "S:\\aspenONEV14.0\\AMS\\V14.0\\Web21\\ADDIN_MSI_CP" -Force -ErrorAction Stop
                       
                            If (-not (test-path ${env:BuildFlagPath})) {
                                mkdir ${env:BuildFlagPath}
                            }
                            New-Item -Path ${env:BuildFlagPath} -Name "APRMExcelAddin_x64.txt" -ItemType "file" 
                        '''   
                    }
    				
    				//echo "ExcelAddin_x64"
					PrjP4Path = Prj_x64_P4Path
                    BuildFlagFile = BuildFlagPath + "ExcelAddin_x64.txt"
                    if(!fileExists(BuildFlagFile)){
                        build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: "ExcelAddin_x64"), string(name: 'PrjP4Path', value: PrjP4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
            			powershell '''
            				$PrjRelease = ${env:CommonReleasePath} + "ExcelAddin_x64"
            				$msi = (Get-ChildItem $PrjRelease -Recurse -Filter *.msi).FullName
                            Write-host "msi is " $msi
                            If (-not (test-path "R:\\aspenONEV14.0\\MSC\\util\\ExcelAddinSetup\\SetupFile")){
                                mkdir "R:\\aspenONEV14.0\\MSC\\util\\ExcelAddinSetup\\SetupFile"
                            }
                            Copy-Item $msi "R:\\aspenONEV14.0\\MSC\\util\\ExceladdinSetup\\SetupFile" -Force -ErrorAction Stop
                        '''   
                    }
        				
    				PrjP4Path = Prj_x86_P4Path 
                    BuildFlagFile = BuildFlagPath + "ExcelAddin.txt"
                    if(!fileExists(BuildFlagFile)){
                        //build job: 'AutoBuildIsmDocker', parameters: [string(name: 'PrjName', value: "ExcelAddin"), string(name: 'PrjP4Path', value: PrjP4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
            			powershell '''
            				$PrjRelease = ${env:CommonReleasePath} + "ExcelAddin"
            				echo $PrjRelease
            				$msi = (Get-ChildItem $PrjRelease -Recurse -Filter *.msi).FullName
                            Write-host "msi is " $msi
                            If (-not (test-path "R:\\aspenONEV14.0\\MSC\\util\\ExcelAddinSetup\\SetupFile")){
                                mkdir "R:\\aspenONEV14.0\\MSC\\util\\ExcelAddinSetup\\SetupFile"
                            }
                            Copy-Item $msi "R:\\aspenONEV14.0\\MSC\\util\\ExceladdinSetup\\SetupFile" -Force -ErrorAction Stop
            				
            				$pfwLocation = Get-ItemPropertyValue HKLM:\\SOFTWARE\\WOW6432Node\\InstallShield\\PackageForTheWeb\\current -Name PFTWexe
            				Start-Process -FilePath $pfwLocation -ArgumentList "R:\\aspenONEV14.0\\MSC\\util\\ExcelAddinSetup\\ExcelAddinSetup.pfw -a -s" -wait
            				
            				Start-Process -FilePath "C:\\Users\\buildpart\\Desktop\\Kaiyan\\StampVer.exe" -ArgumentList "-f `"18.1.0.0`" R:\\aspenONEV14.0\\MSC\\util\\ExcelAddinSetup\\ExcelAddinSetup.exe" -Verb RunAs
            			'''	
                        build job: 'DigitalSign', parameters: [string(name: 'SignRootFolder', value: "R:\\aspenONEV14.0\\MSC\\util\\ExcelAddinSetup"), string(name: 'SignLogFolder', value: "R:\\aspenONEV14.0\\MSC\\util\\SignLog"), string(name: 'FileExtension', value: '*.msi,*.exe')]
                        powershell '''
            				
            				Copy-Item "R:\\aspenONEV14.0\\MSC\\util\\ExcelAddinSetup\\ExcelAddinSetup.exe" "S:\\aspenONEV14.0\\AMS\\V14.0\\Web21\\ADDIN_MSI_CP" -Force -ErrorAction Stop
                       
                            If (-not (test-path ${env:BuildFlagPath})){
                                mkdir ${env:BuildFlagPath}
                            }
                            New-Item -Path ${env:BuildFlagPath} -Name "ExcelAddin.txt" -ItemType "file" 
                        '''   
                    }
  //////////////////////end of building special projects  			
    			
    				
    				IsmNameClass= config.get('MES').prj_x86.Name.getClass().toString() //class net.sf.json.JSONArray
    				//class java.lang.String
    				//echo IsmNameClass 
    				if(IsmNameClass=="class java.lang.String"){
    				    IsmName= config.get('MES').prj_x86.Name
                        //echo IsmName
                        BuildFlagFile = BuildFlagPath + IsmName + ".txt"
                        if(!fileExists(BuildFlagFile)){
                           //echo BuildFlagFile + " doesn't exist"
                           echo "Start to build project: " + IsmName
    				       build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                        }
    				}
    				else{
    				    for($filename in config.get('MES').prj_x86.Name){
                            IsmName= $filename
                            //echo IsmName
                            BuildFlagFile = BuildFlagPath + IsmName + ".txt"
                            if(!fileExists(BuildFlagFile)){
                                //echo BuildFlagFile + " doesn't exist"
                                echo "Start to build project: " + IsmName
        				        build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x86_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                            }
                        }
                    }
                    
                    
    				IsmNameClass= config.get('MES').prj_x64.Name.getClass().toString() //class net.sf.json.JSONArray
    				//class java.lang.String
    				//echo IsmNameClass 
    				if(IsmNameClass == "class java.lang.String"){
                        IsmName= config.get('MES').prj_x64.Name
                        //echo IsmName
                        BuildFlagFile = BuildFlagPath + IsmName + ".txt"
                        if(!fileExists(BuildFlagFile)){
                            //echo BuildFlagFile + " doesn't exist"
                            echo "Start to build project: " + IsmName
    				        build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x64_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                        }  
                    }
    				else{
        				for($filename in config.get('MES').prj_x64.Name){
                            IsmName= $filename
                            //echo IsmName
                            PrjBuildFlag = BuildFlagPath + IsmName + ".txt"
                            if(!fileExists(PrjBuildFlag)){
                                //echo BuildFlagFile + " doesn't exist"
                                echo "Start to build project: " + IsmName
        				        build job: 'AutoBuildIsm', parameters: [string(name: 'PrjName', value: IsmName), string(name: 'PrjP4Path', value: Prj_x64_P4Path), string(name: 'CommonPreBuildPath', value: CommonPreBuildPath), string(name: 'CommonReleasePath', value: CommonReleasePath), string(name: 'CommonVbs', value: CommonVbs), string(name: 'AllMSMs', value: AllMSMs), string(name: 'ISVersion', value: ISVersion), string(name: 'MsmRelocationPath', value: MsmRelocationPath), string(name: 'BuildFlagPath', value: BuildFlagPath)]				                
                            }    
                        } 
    				}
    			}
			}
		}
		stage ('Build Patch'){ 
			agent {
				label 'windows'
			}
			steps {
				script{
					//config = readJSON file : config_file
					//bat script: 'if exist "' + config.APEM.Directories.Release + '" (RMDIR /S /Q "' + config.APEM.Directories.Release + '")'
				    //bat script: 'MKDIR "' + config.APEM.Directories.Release + '"'
				    ProductFeild = 'MES'
				    CommonReleasePath = config.get(ProductFeild).PrivateProperties.CommonReleasePath 
				    IsmNameClass= config.get('MES').prj_x86.Name.getClass().toString() //class net.sf.json.JSONArray
    				if(IsmNameClass == "class java.lang.String"){
                        IsmName= config.get('MES').prj_x86.Name
                        //echo IsmName
                        BuildFlagFile = BuildFlagPath + IsmName + "_Patch.txt"
                        if(!fileExists(BuildFlagFile)){
                            //echo BuildFlagFile + " doesn't exist"
                            echo "Start to build patch: " + IsmName
                            build job: 'AutoBuildPatchJobNum2', parameters: [string(name: 'ConfigurationFile', value: config_file), string(name: 'ProductFeild', value: ProductFeild), string(name: 'ProductName', value: IsmName)]  
                            }else{
                            echo "Skip building patch: " + IsmName
                        }  
                    }
    				else{
        				for(IsmName in config.get('MES').prj_x86.Name){
                            //echo IsmName
                            PrjBuildFlag = BuildFlagPath + IsmName + "_Patch.txt"
                            if(!fileExists(PrjBuildFlag)){
                                //echo BuildFlagFile + " doesn't exist"
                                //echo "Start to build patch: " + IsmName
                                build job: 'AutoBuildPatchJobNum2', parameters: [string(name: 'ConfigurationFile', value: config_file), string(name: 'ProductFeild', value: ProductFeild), string(name: 'ProductName', value: IsmName), string(name: 'CommonReleasePath', value: CommonReleasePath)] 
                            }else{
                                echo "Skip building patch: " + IsmName
                            }     
                        } 
    				}
    				
    				IsmNameClass= config.get('MES').prj_x64.Name.getClass().toString() 
    				if(IsmNameClass == "class java.lang.String"){
                        IsmName= config.get('MES').prj_x64.Name
                        //echo IsmName
                        BuildFlagFile = BuildFlagPath + IsmName + "_Patch.txt"
                        if(!fileExists(BuildFlagFile)){
                            //echo BuildFlagFile + " doesn't exist"
                            //echo "Start to build patch: " + IsmName
                            build job: 'AutoBuildPatchJobNum2', parameters: [string(name: 'ConfigurationFile', value: config_file), string(name: 'ProductFeild', value: ProductFeild), string(name: 'ProductName', value: IsmName)]
                        }else{
                            echo "Skip building patch: " + IsmName
                        }  
                    }
    				else{
        				for(IsmName in config.get('MES').prj_x64.Name){
                            echo IsmName
                            PrjBuildFlag = BuildFlagPath + IsmName + "_Patch.txt"
                            if(!fileExists(PrjBuildFlag)){
                                //echo BuildFlagFile + " doesn't exist"
                               // echo "Start to build project: " + IsmName
                                build job: 'AutoBuildPatchJobNum2', parameters: [string(name: 'ConfigurationFile', value: config_file), string(name: 'ProductFeild', value: ProductFeild), string(name: 'ProductName', value: IsmName)]
                            }else{
                                echo "Skip building " + IsmName
                            }     
                        } 
                    }
				}    
			}
        }
        stage ('Digital Sign') {
			agent none
			steps {
			    script{
			        ProductFeild = 'MES'
			        build job: 'DigitalSign', parameters: [string(name: 'SignRootFolder', value: config.get(ProductFeild).PrivateProperties.SignPath), string(name: 'SignLogFolder', value: config.get(ProductFeild).PrivateProperties.SignLogFolder), string(name: 'FileExtension', value: '*.msi,*.exe')]
			    }
			}
		}
		stage ('Package and Upload') {
			agent {
			    label 'windows'
			}
			steps {
			    script {
                    ProductFeild ="MES"
    				fullPatchName = config.get(ProductFeild).PrivateProperties.ZipFile + " RC" + config.get(ProductFeild).PublicProperties.RCNumber
    				PatchZip = config.get(ProductFeild).PrivateProperties.ZipFile + " RC" + config.get(ProductFeild).PublicProperties.RCNumber + '.zip'
    				bat script: 'if exist "' + PatchZip + '" (DEL /F "' + PatchZip + '")'
    				zip dir:  config.get(ProductFeild).PrivateProperties.SignPath, exclude: '', glob: '', zipFile: PatchZip
    				bat script: 'XCOPY.exe "' + PatchZip + '" \\\\shexablox01.corp.aspentech.com\\Public\\kaiyan /Y /I'
    				
    				MailTemplate = config.get(ProductFeild).PrivateProperties.MailTemplate
    				RCNumber = config.get(ProductFeild).PublicProperties.RCNumber
    				IDKVersion = config.get(ProductFeild).PublicProperties.IDK
    				SLMVersion = config.get(ProductFeild).PublicProperties.SLM
    				UAVersion = config.get(ProductFeild).PublicProperties.UpdateAgentVersion
    				MailSubject = config.get(ProductFeild).PrivateProperties.MailTitle
    				echo "RCNumber is " + RCNumber 
    				echo "IDKVersion is " + IDKVersion
    				echo "SLMVersion is " + SLMVersion 
    				echo "UAVersion is " + UAVersion
    				template = readFile(MailTemplate)
    			    template = template.replaceAll("%RC%", RCNumber)
    				template = template.replaceAll("%version_IDK%",IDKVersion )
    				template = template.replaceAll("%version_SLM%",SLMVersion )
    		     	template = template.replaceAll("%version_UA%", UAVersion)
    				mail body: template, from: 'kaiyan.zhang@aspentech.com', mimeType: 'text/html', subject: MailSubject, to: 'kaiyan.zhang@aspentech.com'
			        
			        config.get(ProductFeild).PublicProperties.RCNumber = (config.get(ProductFeild).PublicProperties.RCNumber.toInteger() + 1).toString()
    				writeJSON file: config_file, json: config
			    }
			}
		}
		
	  /*if(fileExists(BuildFlagPath)){
            bat script: 'rd /S /Q "'+ BuildFlagPath +'"'
        }*/	
	
	}
}
