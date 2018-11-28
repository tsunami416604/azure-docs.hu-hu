---
title: 'Oktatóanyag: Az Azure-ban a Jelszókivonat-szinkronizálás (nál) egyerdős AD integrálása |} A Microsoft Docs'
description: Bemutatja, hogyan állíthatja be az egy hibrid identitás környezet Jelszókivonat-szinkronizálás használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f830a27e9caf032443c2b27bdd2f95fa8069bcf2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247279"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Oktatóanyag: A Jelszókivonat-szinkronizálás (nál) használatával egyetlen AD-erdővel integrálása

![Létrehozás](media/tutorial-password-hash-sync/diagram.png)

A következő oktatóanyag végigvezeti a Jelszókivonat-szinkronizálás használatával hibrid identitás-környezet létrehozása.  Ebben a környezetben felhasználható, tesztelési vagy az első több megismerkedhet a hibrid identitás működésével.

## <a name="prerequisites"></a>Előfeltételek
Az alábbiakban az oktatóprogram elvégzéséhez szükséges előfeltételek
- Egy olyan számítógéppel [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) telepítve.  Ehhez két, ajánlott egy [Windows 10-es](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) vagy egy [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) számítógép.
- Egy [külső hálózati adapter](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) , hogy a virtuális gép kommunikáljon az internettel.
- [Azure-előfizetés](https://azure.microsoft.com/free)
- Windows Server 2016-példányát

> [!NOTE]
> Ebben az oktatóanyagban használja a PowerShell-szkriptek, hogy az oktatóanyag környezetben a leggyorsabb legrövidebb idő alatt hozhat létre.  A parancsfájl elején deklarált változókat a parancsfájlok mindegyike használja.  Lehet, és a változók a környezetnek megfelelően módosítani kell.
>
>A parancsfájlok használt hozzon létre egy általános Active Directory-környezetet az Azure AD Connect telepítése előtt.  Relevánsak az összes, az oktatóanyagokat.
>
> A Githubon érhetők el ebben az oktatóanyagban használt PowerShell-parancsfájlok példányait [Itt](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Először is, amely kell tennie, annak érdekében, hogy a hibrid identitás környezetünk szolgáltatásszintje és fut, hogy a helyszíni Active Directory-kiszolgálóként használt virtuális gép létrehozása.  Tegye a következőket:

1. Nyisson meg rendszergazdaként a PowerShell ISE.
2. Futtassa a következő szkriptet.

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

## <a name="complete-the-operating-system-deployment"></a>Hajtsa végre az operációs rendszer központi telepítése
A virtuális gép kiépítésének befejezéséhez, kell az operációs rendszer telepítésének befejezéséhez.

1. Hyper-V kezelőjében, kattintson duplán a virtuális gépen
2. Kattintson a Start gombra.
3.  A rendszer bekéri "Nyomja le bármelyik billentyűt CD vagy DVD-ről". Lépjen tovább, és ehhez.
4. A Windows Server indítási képernyő válassza ki a nyelvet, és kattintson a **tovább**.
5. Kattintson a **telepítse most**.
6. Adja meg a licenckulcsot, és kattintson a **tovább**.
7. Ellenőrizze ** e fogadja el a licencfeltételeket, és kattintson a **tovább**.
8. Válassza ki **egyéni: csak a Windows telepítése (speciális)**
9. Kattintson a **Tovább** gombra
10. A telepítés befejezése után indítsa újra a virtuális gépet, a bejelentkezési és a futtatási Windows frissíti annak biztosítása érdekében, a virtuális Gépet a legfrissebb.  Telepítse a legújabb frissítéseket.

## <a name="install-active-directory-prerequisites"></a>Az Active Directory-előfeltételek telepítése
Most, hogy egy virtuális gép, hajtsa végre az Active Directory telepítése előtt néhány dolgot kell.  Azt jelenti hogy kell nevezze át a virtuális gép, egy statikus IP-cím és DNS-információkat, és a Távoli kiszolgálófelügyelet eszközeinek telepítése.   Tegye a következőket:

1. Nyisson meg rendszergazdaként a PowerShell ISE.
2. Futtassa a következő szkriptet.

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

## <a name="create-a-windows-server-ad-environment"></a>A Windows Server AD-környezet létrehozása
Most, hogy rendelkezünk a létrehozott virtuális Géphez, és át lett nevezve, és a egy statikus IP-címmel rendelkezik, azt is lépjen tovább, és telepítse és konfigurálja az Active Directory Domain Services.  Tegye a következőket:

1. Nyisson meg rendszergazdaként a PowerShell ISE.
2. Futtassa a következő szkriptet.

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

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>A Windows Server AD-felhasználó létrehozása
Most, hogy az Active Directory-környezetet, hogy egy olyan fiókot kell.  Ez a fiók létrejön a helyszíni AD-környezetet, és utána lettek szinkronizálva az Azure ad-ben.  Tegye a következőket:

1. Nyisson meg rendszergazdaként a PowerShell ISE.
2. Futtassa a következő szkriptet.

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

## <a name="create-an-azure-ad-tenant"></a>Az Azure AD-bérlő létrehozása
Most meg kell, hogy azt is szinkronizálhatja a felhőbe felhasználóink Azure AD-bérlő létrehozásához.  Hozhat létre egy új Azure AD-bérlőhöz, tegye a következőket.

1. Keresse meg a [az Azure portal](https://portal.azure.com) és a egy Azure-előfizetéssel rendelkező fiókkal jelentkezzen be.
2. Válassza ki a **plusz ikont (+)** és keressen rá a **Azure Active Directory**.
3. Válassza ki **Azure Active Directory** a keresési eredmények között.
4. Kattintson a **Létrehozás** gombra.</br>
![Létrehozás](media/tutorial-password-hash-sync/create1.png)</br>
5. Adjon meg egy **szervezet nevét** együtt a **kezdeti tartománynevet**. Ezután kattintson a **Létrehozás** elemre. Ekkor létrejön a címtárban.
6. Miután ez befejeződött, kattintson a **Itt** hivatkozást, a címtár kezeléséhez.

## <a name="create-a-global-administrator-in-azure-ad"></a>Hozzon létre egy globális rendszergazda az Azure ad-ben
Most, hogy az Azure AD-bérlő, hozunk létre egy globális rendszergazdai fiókkal.  Ezt a fiókot az Azure AD-összekötő fiók létrehozása az Azure AD Connect telepítése során használatos.  Az Azure AD-összekötő fiók segítségével adatokat írni az Azure ad-ben.   Hozzon létre globális rendszergazdai fiók tegye a következőket.

1.  A **Kezelés** alatt válassza a **Felhasználókat**.</br>
![Létrehozás](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Válassza ki **minden felhasználó** majd **+ új felhasználó**.
3.  Adjon meg egy nevet és egy felhasználónevet ehhez a felhasználóhoz. Ez a bérlő globális rendszergazdája lesz. Is érdemes módosítani a **címtárbeli szerepkör** való **globális rendszergazdája.** Az ideiglenes jelszót is megjelenítheti. Amikor elkészült, válassza ki a **létrehozás**.</br>
![Létrehozás](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Miután ez befejeződött, nyisson meg egy új webböngészőt, és jelentkezzen be az új globális rendszergazdai fiókot és az ideiglenes jelszó használatával myapps.microsoft.com.
5. Módosítsa a jelszót a globális rendszergazda úgy, hogy az ne felejtse el lesz.

## <a name="download-and-install-azure-ad-connect"></a>Töltse le és telepítse az Azure AD Connect
Most, töltse le és telepítse az Azure AD Connect ideje.  Ha telepítve van a Microsoft a gyorstelepítés keresztül fog futni.  Tegye a következőket:

1. Töltse le [az Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Keresse meg az **AzureADConnect.msi** fájlt, és kattintson rá duplán.
3. Az üdvözlőképernyőn jelölje be a licencfeltételek elfogadását jelző mezőt, és kattintson a **Continue** (Folytatás) gombra.  
4. Az Express settings (Gyorsbeállítások) képernyőn kattintson a **Use express settings** (Gyorsbeállítások használata) lehetőségre.</br>  
![Létrehozás](media/tutorial-password-hash-sync/express1.png)</br>
5. A csatlakozás az Azure ad Szolgáltatáshoz képernyőn adja meg a felhasználónevet és jelszót a globális rendszergazda Azure ad-ben. Kattintson a **Tovább** gombra.  
6. A Connect to AD DS (Csatlakozás az AD DS szolgáltatáshoz) képernyőn adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Kattintson a **Tovább** gombra.  
7. A Ready to configure (Konfigurálásra kész) oldalon kattintson az **Install** (Telepítés) lehetőségre.
8. A telepítés befejezése után kattintson az **Exit** (Kilépés) gombra.
9. Miután a telepítés befejeződött, jelentkezzen ki, és jelentkezzen be újra a Synchronization Service Managert vagy a szinkronizálási Szabályszerkesztő használata előtt.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Ellenőrizze a felhasználók jönnek létre, és a szinkronizálás gyakorisága
Most ellenőrizzük, hogy a felhasználók a helyszíni címtárban szolgáltatástípusban szinkronizálta, és most már meg Azure AD-bérlő szerepel.  Vegye figyelembe, hogy ez eltarthat néhány óráig.  Felhasználók szinkronizálása ellenőrzéséhez tegye a következőket.


1. Keresse meg a [az Azure portal](https://portal.azure.com) és a egy Azure-előfizetéssel rendelkező fiókkal jelentkezzen be.
2. A bal oldalon válassza ki a **Azure Active Directory**
3. A **Kezelés** alatt válassza a **Felhasználókat**.
4. Győződjön meg arról, hogy megjelenik-e az új felhasználókat a bérlőben</br>
![Szinkronizálási](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Hogy a felhasználók bejelentkezés tesztelése

1.  Keresse meg a [http://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be az új bérlő létrehozott felhasználói fiókkal.  Kell jelentkezzen be a következő formátumban: (user@domain.onmicrosoft.com). A felhasználó által használt a bejelentkezéshez ugyanazt a jelszót a helyszínen.</br>
![Ellenőrizze](media/tutorial-password-hash-sync/verify1.png)</br>

Most már sikeresen befejeződött a telepítő egy hibrid identitás környezet, amellyel tesztelheti, és ismerje meg az Azure által biztosított lehetőségeket.

## <a name="next-steps"></a>További lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)|
