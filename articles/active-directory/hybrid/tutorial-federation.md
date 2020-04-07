---
title: 'Oktatóanyag: Egyetlen AD-erdőkörnyezet et az Azure-ba kell betáplálni | Microsoft dokumentumok'
description: Bemutatja, hogyan lehet beállítani egy hibrid identitáskörnyezet összevonása használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3a17eb7fdde6840ce04fb0cbce13ec3f1a121e0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673701"
---
# <a name="tutorial-federate-a-single-ad-forest-environment-to-the-cloud"></a>Oktatóanyag: Egyetlen AD-erdőkörnyezet etetik a felhőbe

![Létrehozás](media/tutorial-federation/diagram.png)

A következő oktatóanyag bemutatja a hibrid identitáskörnyezet létrehozását az összevonás használatával.  Ez a környezet ezután tesztelésre vagy a hibrid identitás működésének megismerésére használható.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag befejezéséhez az alábbiakban az alábbi előfeltételek szükségesek
- Egy számítógép, amelyen telepítve van a [Hyper-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview)  Javasoljuk, hogy ezt Windows [10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) vagy [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) rendszerű számítógépen tegye meg.
- [Azure-előfizetés](https://azure.microsoft.com/free)
- - [Külső hálózati adapter,](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) amely lehetővé teszi, hogy a virtuális gép kommunikáljon az internettel.
- A Windows Server 2016 másolata
- Ellenőrizhető [egyéni tartomány](../../active-directory/fundamentals/add-custom-domain.md)

> [!NOTE]
> Ez az oktatóanyag PowerShell-parancsfájlokat használ, így a leggyorsabb idő alatt hozhatja létre az oktatóanyag-környezetet.  A parancsfájlok mindegyike olyan változót használ, amely a parancsfájlok elején deklarálva van deklarálva.  Módosíthatja és meg is kell változtatnia a változókat, hogy azok tükrözzék a környezetet.
>
>A használt parancsfájlok az Azure AD Connect telepítése előtt általános Active Directory-környezetet hoznak létre.  Ezek relevánsak az összes oktató.
>
> Az oktatóanyagban használt PowerShell-parancsfájlok másolatai [itt](https://github.com/billmath/tutorial-phs)érhetők el a GitHubon.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Az első dolog, amit meg kell tennünk, annak érdekében, hogy a hibrid identitás környezet ben, és fut, hogy hozzon létre egy virtuális gépet, amely a helyszíni Active Directory-kiszolgáló.  

>[!NOTE]
>Ha még soha nem futtatott parancsfájlt a PowerShell `Set-ExecutionPolicy remotesigned` ben a gazdagépen, akkor futtatnia kell, és igent kell mondania a PowerShellben, a parancsfájlok futtatása előtt.

Tegye a következőket:

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

## <a name="install-active-directory-pre-requisites"></a>Az Active Directory előfeltételeinek telepítése
Most, hogy van egy virtuális gép, meg kell tennünk néhány dolgot az Active Directory telepítése előtt.  Ez azt illeti, át kell neveznünk a virtuális gépet, statikus IP-címet és DNS-adatokat kell beállítani, és telepíteni kell a Távoli kiszolgáló felügyeleti eszközeit.   Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Fuss, `Set-ExecutionPolicy remotesigned` és igent mondj minden [A]  Nyomja le az Enter billentyűt.
3. Futtassa az alábbi parancsprogramot.

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
$Password = ConvertTo-SecureString "Passw0rd" -AsPlainText -Force

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

## <a name="create-a-certificate-for-ad-fs"></a>Tanúsítvány létrehozása az AD FS-hez
Most létrehozunk egy TLS/SSL tanúsítványt, amelyet az AD FS fog használni.  Ez lesz egy önaláírt tanúsítványt, és csak tesztelési célokra.  A Microsoft nem javasolja az önaláírt tanúsítványok használatát éles környezetben. Tegye a következőket:

1. Nyissa meg a PowerShell ISE rendszergazdaként.
2. Futtassa az alábbi parancsprogramot.

```powershell 
#Declare variables
$DNSname = "adfs.contoso.com"
$Location = "cert:\LocalMachine\My"

#Create certificate
New-SelfSignedCertificate -DnsName $DNSname -CertStoreLocation $Location
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

## <a name="add-the-custom-domain-name-to-your-directory"></a>Az egyéni tartománynév hozzáadása a címtárhoz
Most, hogy van egy bérlőnk és egy globális rendszergazdánk, hozzá kell adnunk az egyéni tartományunkat, hogy az Azure ellenőrizhesse azt.  Tegye a következőket:

1. Vissza az [Azure Portalon](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) győződjön meg róla, hogy zárja be a **Minden felhasználó** panel.
2. A bal oldalon válassza az **Egyéni tartománynevek** elemet.
3. Válassza **az Egyéni tartomány hozzáadása**lehetőséget.</br>
![Összevonás](media/tutorial-federation/custom1.png)</br>
4. Az **Egyéni tartománynevek mezőbe**írja be az egyéni tartomány nevét, majd kattintson a Tartomány **hozzáadása**gombra.
5. Az egyéni tartománynév képernyőn TXT vagy MX információkat kap.  Ezt az információt hozzá kell adni a tartománya alá tartozó tartományregisztráló DNS-adataihoz.  Tehát meg kell menni a tartományregisztráló, adja meg a TXT vagy MX információkat a DNS-beállításokat a tartomány.  Ez lehetővé teszi az Azure számára a tartomány igazolását.  Ez akár 24 órát is igénybe vehet, amíg az Azure ellenőrzi azt.  További információt az [Egyéni tartomány hozzáadása dokumentációban](../../active-directory/fundamentals/add-custom-domain.md) talál.</br>
![Összevonás](media/tutorial-federation/custom2.png)</br>
6. Az ellenőrzés ellenőrzéséhez kattintson az Ellenőrzés gombra.</br>
![Összevonás](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Az Azure AD Connect letöltése és telepítése
Most itt az ideje, hogy töltse le és telepítse az Azure AD Connect.  Miután telepítettük, végigfutunk az expressz telepítésen.  Tegye a következőket:

1. [Az Azure AD Connect letöltése](https://www.microsoft.com/download/details.aspx?id=47594)
2. Keresse meg az **AzureADConnect.msi** fájlt, és kattintson rá duplán.
3. Az üdvözlőképernyőn jelölje be a licencfeltételek elfogadását jelző mezőt, és kattintson a **Continue** (Folytatás) gombra.  
4. A Gyorsbeállítások képernyőn kattintson a **Testreszabás gombra.**  
5. A Szükséges összetevők telepítése képernyőn. Kattintson a **Telepítés gombra.**  
6. A Felhasználói bejelentkezés képernyőn válassza az **Összevonás a AD FS-sel** lehetőséget, majd kattintson a **Tovább**gombra.
![Összevonás](media/tutorial-federation/fed1.png)

1. A Csatlakozás az Azure AD-hez képernyőn adja meg a fent létrehozott globális rendszergazda felhasználónevét és jelszavát, majd kattintson a **Tovább**gombra.
2. A Könyvtárak csatlakoztatása képernyőn kattintson a **Könyvtár hozzáadása**gombra.  Ezután válassza **az Új AD-fiók létrehozása lehetőséget,** írja be a contoso\Rendszergazda felhasználónevet és jelszót, majd kattintson az **OK**gombra.
3. Kattintson a **Tovább** gombra.
4. Az Azure AD bejelentkezési konfigurációs képernyőn válassza a **Folytatás lehetőséget anélkül, hogy az összes UPN-utótagot ellenőrzött tartományoknak megfelelőnek megfeleltetne,** és kattintson a Tovább gombra. **Next.**
5. A Tartomány- és szervezetiegység-szűrés képernyőn kattintson a **Tovább gombra.**
6. A Felhasználók egyedi azonosítása képernyőn kattintson a **Tovább**gombra.
7. A Felhasználók és eszközök szűrése képernyőn kattintson a **Tovább**gombra.
8. A Választható szolgáltatások képernyőn kattintson a **Tovább**gombra.
9. A Tartományi rendszergazda hitelesítő adatai lapon adja meg a contoso\Rendszergazda felhasználónevet és jelszót, majd kattintson a **Tovább gombra.**
10. Az AD FS farm képernyőjén győződjön meg arról, hogy **az Új AD FS-farm konfigurálása** jelölőnégyzet be van jelölve.
11. Válassza **az Összevonási kiszolgálókon telepített tanúsítvány használata** lehetőséget, és kattintson a **Tallózás gombra.**
12. Írja be a DC1 értéket a keresőmezőbe, és jelölje ki, ha megtalálta.  Kattintson az **OK** gombra.
13. A **Tanúsítványfájl** legördülő menüben válassza **adfs.contoso.com** a fent létrehozott tanúsítványt.  Kattintson a **Tovább** gombra.
![Összevonás](media/tutorial-federation/fed2.png)

1. Az AD FS-kiszolgáló képernyőjén kattintson a **Tallózás gombra,** és írja be a DC1 értéket a keresőmezőbe, és jelölje ki, ha megtalálta.  Kattintson az **OK** gombra.  Kattintson a **Tovább** gombra.
![Összevonás](media/tutorial-federation/fed3.png)

1. A webalkalmazás proxykiszolgálói képernyőn kattintson a **Tovább**gombra.
2. Az AD FS szolgáltatásfiók képernyőjén adja meg a contoso\Rendszergazda felhasználónevet és jelszót, majd kattintson a **Tovább gombra.**
3. Az Azure AD-tartomány képernyőn válassza ki az ellenőrzött egyéni tartományt a legördülő menüből, és kattintson a **Tovább**gombra.
4. A Ready to configure (Konfigurálásra kész) oldalon kattintson az **Install** (Telepítés) lehetőségre.
5. A telepítés befejezése után kattintson az **Exit** (Kilépés) gombra.
6. A telepítés befejezése után jelentkezzen ki, majd jelentkezzen be újra, mielőtt használná a Szinkronizálási szolgáltatáskezelőt vagy a Szinkronizálási szabályszerkesztőt.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>A felhasználók létrehozásának és szinkronizálásának ellenőrzése
Most ellenőrizni fogjuk, hogy a helyszíni címtárban lévő felhasználók szinkronizálva lettek-e, és most már léteznek az Azure AD-bérlőben.  Ne feledje, hogy ez eltarthat néhány óráig.  A felhasználók szinkronizálásának ellenőrzéséhez tegye a következőket.


1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. A bal oldalon válassza az **Azure Active Directory lehetőséget**
3. A **Kezelés** alatt válassza a **Felhasználókat**.
4. Ellenőrizze, hogy látja-e az ![új felhasználókat a bérlői szinkronizálásban](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés tesztelése az egyik felhasználónkkal

1. Tallózás a[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Bejelentkezés az új bérlőben létrehozott felhasználói fiókkal.  A következő formátumban kell bejelentkeznie: (user@domain.onmicrosoft.com). Használja ugyanazt a jelszót, amelyet a felhasználó a helyszíni bejelentkezéshez használ.
   ![Ellenőrzés](media/tutorial-password-hash-sync/verify1.png)

Most már sikeresen beállított egy hibrid identitáskörnyezetet, amely segítségével tesztelheti és megismerkedhet az Azure ajánlatával.

## <a name="next-steps"></a>Következő lépések

- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)

