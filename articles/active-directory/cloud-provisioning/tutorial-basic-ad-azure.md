---
title: Oktatóanyag – Alapvető Active Directory helyszíni és Azure AD-környezetben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356a05d4d92f17ceb66ff0208153ec3eac736757
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793897"
---
# <a name="tutorial-basic-active-directory-environment"></a>Oktatóanyag: Alapszintű Active Directory-környezet

Ez az oktatóanyag bemutatja az alapvető Active Directory-környezet létrehozását. 

![Létrehozás](media/tutorial-single-forest/diagram1.png)

Használhatja a környezetet hoz létre az oktatóanyag ban, hogy tesztelje a hibrid identitás forgatókönyvek különböző aspektusait, és lesz előfeltétele néhány oktató.  Ha már rendelkezik meglévő Active Directory-környezettel, azt használhatja helyettesítőként.  Ez az információ az egyének, akik az én kezdve a semmiből.

Ez az oktatóanyag a következőkből áll:
## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag befejezéséhez az alábbiakban az alábbi előfeltételek szükségesek
- Egy számítógép, amelyen telepítve van a [Hyper-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview)  Javasoljuk, hogy ezt Windows [10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) vagy [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) rendszerű számítógépen tegye meg.
- [Külső hálózati adapter,](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) amely lehetővé teszi, hogy a virtuális gép kommunikáljon az internettel.
- [Azure-előfizetés](https://azure.microsoft.com/free)
- A Windows Server 2016 másolata
- [Microsoft .](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Ez az oktatóanyag PowerShell-parancsfájlokat használ, így a leggyorsabb idő alatt hozhatja létre az oktatóanyag-környezetet.  A parancsfájlok mindegyike olyan változót használ, amely a parancsfájlok elején deklarálva van deklarálva.  Módosíthatja és meg is kell változtatnia a változókat, hogy azok tükrözzék a környezetet.
>
>A használt parancsfájlok egy általános Active Directory-környezetet hoznak létre az Azure AD Connect felhőkiépítési ügynök telepítése előtt.  Ezek relevánsak az összes oktató.
>
> Az oktatóanyagban használt PowerShell-parancsfájlok másolatai [itt](https://github.com/billmath/tutorial-phs)érhetők el a GitHubon.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Az első dolog, amit meg kell tennie, annak érdekében, hogy a hibrid identitáskörnyezet, és fut, hogy hozzon létre egy virtuális gépet, amely a helyszíni Active Directory-kiszolgálóként lesz használva.  Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>Az operációs rendszer telepítésének befejezése
Annak érdekében, hogy befejezze az épület a virtuális gép, be kell fejeznie az operációs rendszer telepítését.

1. Hyper-V Manager, kattintson duplán a virtuális gépre
2. Kattintson a Start gombra.
3. A program kéri, hogy "Nyomjon meg egy billentyűt a CD-ről vagy DVD-ről történő rendszerindításhoz". Rajta, és tegye meg.
4. A Windows Server indítási képernyőjén válassza ki a kívánt nyelvet, és kattintson a **Tovább**gombra.
5. Kattintson **a Telepítés gombra.**
6. Írja be a licenckulcsot, és kattintson a **Tovább**gombra.
7. Ellenőrizze **Elfogadom a licencfeltételeket, és kattintson a **Tovább**gombra.
8. Egyéni beállítás **kiválasztása: Csak a Windows telepítése (speciális)**
9. Kattintson a **Tovább gombra**
10. A telepítés befejezése után indítsa újra a virtuális gépet, jelentkezzen be, és futtassa a Windows-frissítéseket annak érdekében, hogy a virtuális gép a legfrissebb legyen.  Telepítse a legújabb frissítéseket.

## <a name="install-active-directory-prerequisites"></a>Az Active Directory előfeltételeinek telepítése
Most, hogy egy virtuális gép fel, meg kell tennie néhány dolgot telepítése előtt Az Active Directory telepítése előtt.  Ez azt illeti, át kell neveznie a virtuális gépet, statikus IP-címet és DNS-adatokat kell beállítania, és telepítenie kell a Távoli kiszolgáló felügyeleti eszközeit.   Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Windows Server AD környezet létrehozása
Most, hogy létrehozta a virtuális gép, és átnevezték, és statikus IP-címmel rendelkezik, telepítheti és konfigurálhatja az Active Directory tartományi szolgáltatásokat.  Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Windows Server AD-felhasználó létrehozása
Most, hogy rendelkezik az Active Directory-környezettel, tesztfiókra van szüksége.  Ez a fiók a helyszíni AD-környezetben jön létre, majd szinkronizálva lesz az Azure AD-vel.  Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Azure AD-bérlő létrehozása
Most létre kell hoznia egy Azure AD-bérlőt, hogy szinkronizálhassa a felhasználókat a felhővel.  Új Azure AD-bérlő létrehozásához tegye a következőket.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. Válassza ki a **plusz ikont (+)** és keresse meg az **Azure Active Directoryt**.
3. Válassza ki az **Azure Active Directoryt** a keresési eredmények közül.
4. Kattintson a **Létrehozás** gombra.</br>
![Létrehozás](media/tutorial-single-forest/create1.png)</br>
5. Adja meg a **szervezet nevét** a **kezdeti tartománynevet**. Ezután válassza **a Létrehozás lehetőséget.** Ezzel létrejön a címtár.
6. Miután ez befejeződött, kattintson az **itt** linkre, a könyvtár kezeléséhez.

## <a name="create-a-global-administrator-in-azure-ad"></a>Globális rendszergazda létrehozása az Azure AD-ben
Most, hogy rendelkezik egy Azure AD-bérlővel, létrehoz egy globális rendszergazdai fiókot.  A globális rendszergazdai fiók létrehozásához tegye a következőket.

1.  A **Kezelés** alatt válassza a **Felhasználókat**.</br>
![Létrehozás](media/tutorial-single-forest/administrator1.png)</br>
2.  Válassza a **Minden felhasználó**, majd az **+ Új felhasználó** lehetőséget.
3.  Adjon meg egy nevet és egy felhasználónevet ennek a felhasználónak. Ez lesz a bérlő globális rendszergazdája. A **Címtár szerepkört** globális **rendszergazdára** is módosítani szeretné. Megjelenítheti az ideiglenes jelszót is. Ha elkészült, kattintson a **Létrehozás** gombra.</br>
![Létrehozás](media/tutorial-single-forest/administrator2.png)</br>
4. Miután ez befejeződött, nyisson meg egy új webböngészőt, és jelentkezzen be myapps.microsoft.com az új globális rendszergazdai fiók és az ideiglenes jelszó használatával.
5. Módosítsa a globális rendszergazda jelszavát olyanra, amire emlékezni fog.

## <a name="optional--additional-server-and-forest"></a>Nem kötelező: További kiszolgáló és erdő
Az alábbi szakasz nem kötelező, amely további kiszolgáló és erdő létrehozásának lépéseit ismerteti.  Ez használható néhány fejlettebb oktatóanyagok, mint például [a Pilot for Azure AD Connect a felhőkiépítés.](tutorial-pilot-aadc-aadccp.md)

Ha csak egy további kiszolgálóra van szüksége, leállíthatja a **- A virtuálisgép-lépés létrehozása** és a kiszolgáló csatlakoztatása a fent létrehozott meglévő tartományhoz.  

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>Az operációs rendszer telepítésének befejezése
Annak érdekében, hogy befejezze az épület a virtuális gép, be kell fejeznie az operációs rendszer telepítését.

1. Hyper-V Manager, kattintson duplán a virtuális gépre
2. Kattintson a Start gombra.
3. A program kéri, hogy "Nyomjon meg egy billentyűt a CD-ről vagy DVD-ről történő rendszerindításhoz". Rajta, és tegye meg.
4. A Windows Server indítási képernyőjén válassza ki a kívánt nyelvet, és kattintson a **Tovább**gombra.
5. Kattintson **a Telepítés gombra.**
6. Írja be a licenckulcsot, és kattintson a **Tovább**gombra.
7. Ellenőrizze **Elfogadom a licencfeltételeket, és kattintson a **Tovább**gombra.
8. Egyéni beállítás **kiválasztása: Csak a Windows telepítése (speciális)**
9. Kattintson a **Tovább gombra**
10. A telepítés befejezése után indítsa újra a virtuális gépet, jelentkezzen be, és futtassa a Windows-frissítéseket annak érdekében, hogy a virtuális gép a legfrissebb legyen.  Telepítse a legújabb frissítéseket.

### <a name="install-active-directory-prerequisites"></a>Az Active Directory előfeltételeinek telepítése
Most, hogy egy virtuális gép fel, meg kell tennie néhány dolgot telepítése előtt Az Active Directory telepítése előtt.  Ez azt illeti, át kell neveznie a virtuális gépet, statikus IP-címet és DNS-adatokat kell beállítania, és telepítenie kell a Távoli kiszolgáló felügyeleti eszközeit.   Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Windows Server AD környezet létrehozása
Most, hogy létrehozta a virtuális gép, és átnevezték, és statikus IP-címmel rendelkezik, telepítheti és konfigurálhatja az Active Directory tartományi szolgáltatásokat.  Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD envrionment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Windows Server AD-felhasználó létrehozása
Most, hogy rendelkezik az Active Directory-környezettel, tesztfiókra van szüksége.  Ez a fiók a helyszíni AD-környezetben jön létre, majd szinkronizálva lesz az Azure AD-vel.  Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Összegzés
Most már rendelkezik egy olyan környezettel, amely használható a meglévő oktatóanyagokhoz, és további funkciók tesztelésére felhőkiépítés biztosít.

## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
