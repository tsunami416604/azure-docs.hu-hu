---
title: Teszt futtatása az Azure Stackben |} A Microsoft Docs
description: Tudnivalók az Azure Stackben diagnosztikai naplófájlok összegyűjtése.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 10/31/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 0720edeab7577f41ddbaa67e4643f1dfe57ebac0
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740314"
---
# <a name="run-a-validation-test-for-azure-stack"></a>Teszt futtatása az Azure Stackhez

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*
 
Az Azure Stack állapotát ellenőrizheti. Ha a probléma, forduljon a Microsoft ügyfél-támogatási szolgáltatások. Támogatási arra kéri, hogy futtassa **Test-AzureStack** a felügyeleti csomópontból. Az ellenőrző teszt különíti el a hibát. Támogatási is ezután a részletes naplók elemzése, összpontosíthat a terület, ahol a hiba történt, és működik együtt a megoldásban.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

Ha a probléma, forduljon a Microsoft ügyfél-támogatási szolgáltatások, majd futtassa **futtatása Test-AzureStack**.

1. Probléma lehet.
2. Forduljon a Microsoft az ügyfél-támogatási szolgáltatások.
3. Futtatás **Test-AzureStack** az emelt szintű végpontról.
    1. Hozzáférés a kiemelt végponthoz. Útmutatásért lásd: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md). 
    2. Az a ASDK jelentkezzen be a felügyeleti gazdagéphez, másként **AzureStack\CloudAdmin**.  
    Az integrált rendszereken szüksége lesz a kiemelt-end-pont hardvergyártójához OEM által biztosított felügyeleti IP-címet használja.
    3. Nyissa meg a PowerShellt rendszergazdaként.
    4. Futtassa: `Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. Futtassa: `Test-AzureStack`
4. Ha bármelyik teszteli, hogy a jelentés **sikertelen**futtassa: `Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath “<path>” -OutputShareCredential $cred` a parancsmag a naplókat gyűjt a Test-AzureStack. Diagnosztikai naplók kapcsolatos további információkért lásd: [Azure Stack-diagnosztikai eszközök](azure-stack-diagnostics.md). Nem kell gyűjtését, vagy forduljon a Microsoft támogatási (CSS) Ha a jelentés tesztek **WARN**.
5. Küldés a **SeedRing** Microsoft ügyfél-támogatási naplók. A Microsoft ügyfélszolgálata szolgáltatások működik együtt, hogy a probléma megoldásához.

## <a name="reference-for-test-azurestack"></a>Test-AzureStack referenciája

Ez a szakasz áttekintést nyújt a Test-AzureStack parancsmag és az ellenőrzési jelentésből összegzését.

### <a name="test-azurestack"></a>Test-AzureStack

Az Azure Stack állapotát ellenőrzi. A parancsmag az Azure Stack hardver- és állapotát jelzi. Támogatási csapatát a jelentés segítségével az Azure Stack támogatási esetek megoldásához idő csökkentése érdekében.

> [!Note]  
> **Test-AzureStack** hibák, amelyek a rendszer nem eredményez felhőalapú valamilyen okból kimaradás lép, előfordulhat, hogy észlelni, például egyetlen lemez vagy egy egyetlen fizikai gazdagépen Csomóponthiba sikertelen.

#### <a name="syntax"></a>Szintaxis

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Paraméterek

| Paraméter               | Érték           | Szükséges | Alapértelmezett |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | Karakterlánc    | Nem       | FALSE (HAMIS)   |
| DoNotDeployTenantVm     | SwitchParameter | Nem       | FALSE (HAMIS)   |
| AdminCredential         | PSCredential    | Nem       | NA      |
| Lista                    | SwitchParameter | Nem       | FALSE (HAMIS)   |
| Kihagyás                  | Karakterlánc          | Nem       | NA      |
| Belefoglalás                 | Karakterlánc          | Nem       | NA      |
| BackupSharePath         | Karakterlánc          | Nem       | NA      |
| BackupShareCredential   | PSCredential    | Nem       | NA      |


A Test-AzureStack parancsmag a következő általános paramétereket támogatja: részletes, hibakeresési, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable és OutVariable. További információkért lásd: [általános paraméterek](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Példák a Test-AzureStack

Az alábbi példák azt feltételezik, hogy be van jelentkezve **CloudAdmin** és a kiemelt végponthoz (EGP) eléréséhez. Útmutatásért lásd: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Test-AzureStack interaktívan felhőbeli forgatókönyvek nélkül

A EGP-munkamenetben futtassa:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Futtassa a Test-AzureStack felhőbeli forgatókönyvek

Használhat **Test-AzureStack** felhőbeli forgatókönyvek az Azure Stack futtatásához. Ezek a forgatókönyvek a következőket biztosítják:

 - Erőforráscsoportok létrehozása
 - Csomagok létrehozása
 - Ajánlat létrehozása
 - Storage-fiókok létrehozása
 - Virtuális gép létrehozása
 - A tesztkörnyezet a létrehozott tárfiókot használ a blob-műveletek végrehajtása
 - A tesztelési forgatókönyvhöz létrehozott storage-fiók használatával üzenetsor-műveletek végrehajtása
 - A tesztkörnyezet a létrehozott tárfiókot használó tábla műveletek végrehajtása

A felhőbeli forgatókönyvek felhőalapú rendszergazdai hitelesítő adatok szükségesek. 
> [!Note]  
> Az Active Directory összevonási szolgáltatásokban (AD FS) hitelesítő adatok használatával felhőbeli forgatókönyvek nem futtatható. A **Test-AzureStack** parancsmag csak az EGP-n keresztül érhető el. Azonban az EGP nem támogatja az Active Directory összevonási szolgáltatások hitelesítő adatait.

Egyszerű felhasználónév formátumban írja be a felhő rendszergazdájának felhasználóneve serviceadmin@contoso.onmicrosoft.com (Azure AD). Amikor a rendszer kéri, írja be a felhőalapú rendszergazdai fiók jelszavát.

A EGP-munkamenetben futtassa:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Futtassa a Test-AzureStack felhőbeli forgatókönyvek nélkül

A EGP-munkamenetben futtassa:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Rendelkezésre álló Tesztelési forgatókönyvek listája:

A EGP-munkamenetben futtassa:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Egy adott teszt futtatása

A EGP-munkamenetben futtassa:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Meghatározott vizsgálatok kizárása:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>Futtassa a Test-AzureStack infrastruktúra biztonsági mentési beállítások tesztelése

Mielőtt konfigurálná a infrastruktúra biztonsági mentését, tesztelje a biztonsági mentési megosztás elérési útja és a hitelesítő adatok használatával a **AzsBackupShareAccessibility** teszteléséhez.

A EGP-munkamenetben futtassa:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
Biztonsági mentés konfigurálása után futtatható a megosztás ellenőrzése AzsBackupShareAccessibility érhető el a ERCS egy EGP-munkamenetben futtassa:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

Egy EGP-munkamenetben futtassa a konfigurált biztonsági másolatok megosztásáról az új hitelesítő adatok tesztelése:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>Ellenőrző teszt

A következő táblázat összefoglalja az érvényesítési tesztek futtatásához **Test-AzureStack**.

| Name (Név)                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Az Azure Stack-felhőben futtató infrastruktúra összegzése                                                                                  |
| Az Azure Stack tárolási szolgáltatások – összefoglalás                                                                                              |
| Az Azure Stack infrastruktúra szerepkör példány összegzése                                                                                  |
| Üzemeltetési infrastruktúrájának kihasználtságát az Azure Stack-felhőben                                                                              |
| Az Azure Stack-infrastruktúra-kapacitás                                                                                               |
| Az Azure Stack-portál és API-összefoglalót                                                                                                |
| Az Azure Stack az Azure Resource Manager-tanúsítvány adatai                                                                                               |
| Infrastruktúra felügyeletvezérlő, a hálózati vezérlő, a tárolási szolgáltatások és a kiemelt végponthoz infrastruktúra-szerepkörök          |
| Infrastruktúra felügyeletvezérlő, a hálózati vezérlő, a tárolási szolgáltatások és a kiemelt végponthoz infrastruktúra-szerepkör példányai |
| Az Azure Stack-infrastruktúra-szerepkör összefoglalása                                                                                           |
| Az Azure Stack Cloud Service Fabric-szolgáltatások                                                                                         |
| Az Azure Stack infrastruktúra szerepkör példányok teljesítménye                                                                              |
| Az Azure Stack felhő gazdagép teljesítmény összegzése                                                                                        |
| Az Azure Stack szolgáltatás erőforrás használat összegzése                                                                                  |
| Az Azure Stack skálázási egység kritikus események (elmúlt 8 óra)                                                                             |
| Az Azure Stack tárolási szolgáltatások – összefoglalás fizikai lemezek                                                                               |
|Az Azure Stack biztonsági másolatok megosztásáról kisegítő összegzése                                                                                     |

## <a name="next-steps"></a>További lépések

 - Azure Stack-diagnosztikai eszközök és a probléma naplózása kapcsolatos további információkért lásd: [ Azure Stack-diagnosztikai eszközök](azure-stack-diagnostics.md).
 - Hibaelhárítással kapcsolatos további tudnivalókért lásd: [a Microsoft Azure Stack hibaelhárítása](azure-stack-troubleshooting.md)
