---
title: 'Oktatóanyag: Egyetlen AD-erdő integrálása az Azure-ba phs használatával'
description: Bemutatja, hogyan lehet beállítani a hibrid identitáskörnyezet jelszókivonat-szinkronizálás használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17300fa69b61c7713c860e2a35e63fcb6584bc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "66474014"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Oktatóanyag: Egyetlen AD-erdő integrálása jelszókivonat-szinkronizálás (PHS) használatával

![Létrehozás](media/tutorial-password-hash-sync/diagram.png)

A következő oktatóanyag végigvezeti a hibrid identitáskörnyezet létrehozásán a jelszókivonat-szinkronizálás használatával.  Ez a környezet ezután tesztelésre vagy a hibrid identitás működésének megismerésére használható.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag befejezéséhez az alábbiakban az alábbi előfeltételek szükségesek
- Egy számítógép, amelyen telepítve van a [Hyper-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview)  Javasoljuk, hogy ezt Windows [10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) vagy [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) rendszerű számítógépen tegye meg.
- [Külső hálózati adapter,](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) amely lehetővé teszi, hogy a virtuális gép kommunikáljon az internettel.
- [Azure-előfizetés](https://azure.microsoft.com/free)
- A Windows Server 2016 másolata

> [!NOTE]
> Ez az oktatóanyag PowerShell-parancsfájlokat használ, így a leggyorsabb idő alatt hozhatja létre az oktatóanyag-környezetet.  A parancsfájlok mindegyike olyan változót használ, amely a parancsfájlok elején deklarálva van deklarálva.  Módosíthatja és meg is kell változtatnia a változókat, hogy azok tükrözzék a környezetet.
>
>A használt parancsfájlok az Azure AD Connect telepítése előtt általános Active Directory-környezetet hoznak létre.  Ezek relevánsak az összes oktató.
>
> Az oktatóanyagban használt PowerShell-parancsfájlok másolatai [itt](https://github.com/billmath/tutorial-phs)érhetők el a GitHubon.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Az első dolog, amit meg kell tennünk, annak érdekében, hogy a hibrid identitás környezet ben, és fut, hogy hozzon létre egy virtuális gépet, amely a helyszíni Active Directory-kiszolgáló.  Tegye a következőket:

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
Most, hogy van egy virtuális gép, meg kell tennünk néhány dolgot az Active Directory telepítése előtt.  Ez azt illeti, át kell neveznünk a virtuális gépet, statikus IP-címet és DNS-adatokat kell beállítani, és telepíteni kell a Távoli kiszolgáló felügyeleti eszközeit.   Tegye a következőket:

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
Most, hogy létrehoztuk a virtuális gép, és átnevezték, és egy statikus IP-címet, akkor megy előre, és telepítse és konfigurálja az Active Directory tartományi szolgáltatások.  Tegye a következőket:

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
Most, hogy rendelkezünk az Active Directory környezet, szükségünk van egy tesztfiók.  Ez a fiók a helyszíni AD-környezetben jön létre, majd szinkronizálva lesz az Azure AD-vel.  Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

```powershell 
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
Most létre kell hoznunk egy Azure AD-bérlőt, hogy szinkronizálhassuk a felhasználókat a felhővel.  Új Azure AD-bérlő létrehozásához tegye a következőket.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. Válassza ki a **plusz ikont (+)** és keresse meg az **Azure Active Directoryt**.
3. Válassza ki az **Azure Active Directoryt** a keresési eredmények közül.
4. Kattintson a **Létrehozás** gombra.</br>
![Létrehozás](media/tutorial-password-hash-sync/create1.png)</br>
5. Adja meg a **szervezet nevét** a **kezdeti tartománynevet**. Ezután válassza **a Létrehozás lehetőséget.** Ezzel létrejön a címtár.
6. Miután ez befejeződött, kattintson az **itt** linkre, a könyvtár kezeléséhez.

## <a name="create-a-global-administrator-in-azure-ad"></a>Globális rendszergazda létrehozása az Azure AD-ben
Most, hogy rendelkezünk egy Azure AD-bérlővel, létrehozunk egy globális rendszergazdai fiókot.  Ez a fiók az Azure AD Connect-fiók létrehozásához használható az Azure AD Connect telepítése során.  Az Azure AD-összekötő fiók az Azure AD-be történő adatok írásához használható.   A globális rendszergazdai fiók létrehozásához tegye a következőket.

1.  A **Kezelés** alatt válassza a **Felhasználókat**.</br>
![Létrehozás](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Válassza a **Minden felhasználó**, majd az **+ Új felhasználó** lehetőséget.
3.  Adjon meg egy nevet és egy felhasználónevet ennek a felhasználónak. Ez lesz a bérlő globális rendszergazdája. A **Címtár szerepkört** globális **rendszergazdára** is módosítani szeretné. Megjelenítheti az ideiglenes jelszót is. Ha elkészült, kattintson a **Létrehozás** gombra.</br>
![Létrehozás](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Miután ez befejeződött, nyisson meg egy új webböngészőt, és jelentkezzen be myapps.microsoft.com az új globális rendszergazdai fiók és az ideiglenes jelszó használatával.
5. Módosítsa a globális rendszergazda jelszavát olyanra, amire emlékezni fog.

## <a name="download-and-install-azure-ad-connect"></a>Az Azure AD Connect letöltése és telepítése
Most itt az ideje, hogy töltse le és telepítse az Azure AD Connect.  Miután telepítettük, végigfutunk az expressz telepítésen.  Tegye a következőket:

1. [Az Azure AD Connect letöltése](https://www.microsoft.com/download/details.aspx?id=47594)
2. Keresse meg az **AzureADConnect.msi** fájlt, és kattintson rá duplán.
3. Az üdvözlőképernyőn jelölje be a licencfeltételek elfogadását jelző mezőt, és kattintson a **Continue** (Folytatás) gombra.  
4. Az Express settings (Gyorsbeállítások) képernyőn kattintson a **Use express settings** (Gyorsbeállítások használata) lehetőségre.</br>  
![Létrehozás](media/tutorial-password-hash-sync/express1.png)</br>
5. A Csatlakozás az Azure AD-hez képernyőn adja meg a felhasználónevet és a jelszót az Azure AD globális rendszergazdájának. Kattintson a **Tovább** gombra.  
6. A Connect to AD DS (Csatlakozás az AD DS szolgáltatáshoz) képernyőn adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Kattintson a **Tovább** gombra.  
7. A Ready to configure (Konfigurálásra kész) oldalon kattintson az **Install** (Telepítés) lehetőségre.
8. A telepítés befejezése után kattintson az **Exit** (Kilépés) gombra.
9. A telepítés befejezése után jelentkezzen ki, majd jelentkezzen be újra, mielőtt használná a Szinkronizálási szolgáltatáskezelőt vagy a Szinkronizálási szabályszerkesztőt.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>A felhasználók létrehozásának és szinkronizálásának ellenőrzése
Most ellenőrizni fogjuk, hogy a helyszíni címtárban lévő felhasználók szinkronizálva lettek-e, és most már léteznek az Azure AD-bérlőben.  Ne feledje, hogy ez eltarthat néhány óráig.  A felhasználók szinkronizálásának ellenőrzéséhez tegye a következőket.


1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. A bal oldalon válassza az **Azure Active Directory lehetőséget**
3. A **Kezelés** alatt válassza a **Felhasználókat**.
4. Ellenőrizze, hogy látja-e az új felhasználókat a bérlőben</br>
![Szinkronizál](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés tesztelése az egyik felhasználónkkal

1. Tallózás a[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Bejelentkezés az új bérlőben létrehozott felhasználói fiókkal.  A következő formátumban kell bejelentkeznie: (user@domain.onmicrosoft.com). Használja ugyanazt a jelszót, amelyet a felhasználó a helyszíni bejelentkezéshez használ.</br>
   ![Ellenőrzés](media/tutorial-password-hash-sync/verify1.png)</br>

Most már sikeresen beállított egy hibrid identitáskörnyezetet, amely segítségével tesztelheti és megismerkedhet az Azure ajánlatával.

## <a name="next-steps"></a>Következő lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md)|
