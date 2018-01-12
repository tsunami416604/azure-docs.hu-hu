---
title: "Teszt futtatása Azure verem |} Microsoft Docs"
description: "Azure-készletben diagnosztikai naplófájlok gyűjtéséről"
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: mabrigg
ms.openlocfilehash: 53ef19628b40c4a008143c867c9e7867ac91854d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Azure verem teszt futtatása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*
 
Az Azure-verem állapotának ellenőrzéséhez. Ha a probléma, forduljon a szolgáltatások a Microsoft ügyfélszolgálatának segítségét. Támogatási arra kéri, hogy a teszt-AzureStack futtassa a csomópontot. Az ellenőrző teszt elkülöníti a hibát. Támogatási majd elemezheti a részletes naplókat, a terület, ahol a hiba történt az koncentrálhat, és felhasználhatja a probléma megoldását Önnel.

## <a name="run-test-azurestack"></a>Futtassa a Test-AzureStack

Ha a probléma, forduljon a Microsoft ügyfélszolgálata szolgáltatások, és futtassa **futtatása teszt-AzureStack**.

1. Probléma lehet.
2. Forduljon a Microsoft ügyfélszolgálati támogatás.
3. Futtatás **teszt-AzureStack** a rendszerjogosultságú végpontját.
    1. A privilegizált végpont a hozzáférést. Útmutatásért lásd: [használatával a privilegizált végpont Azure verem](azure-stack-privileged-endpoint.md). 
    2. Jelentkezzen be **AzureStack\CloudAdmin** a felügyeleti gazdagépen.
    3. Nyissa meg a PowerShellt rendszergazdaként.
    4. Futtatás:`Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. Futtatás:`Test-AzureStack`
4. Ha bármely tesztek jelentés sikertelen lesz, futtassa: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` a parancsmag a naplókat gyűjt a Test-AzureStack. Diagnosztikai naplók kapcsolatos további információkért lásd: [Azure verem diagnosztikai eszközök](azure-stack-diagnostics.md).
5. Küldjön a **SeedRing** bejegyzéseit, amelyek a szolgáltatások a Microsoft ügyfélszolgálatának segítségét. A Microsoft ügyfélszolgálata szolgáltatások együttműködve biztosítja, hogy a probléma megoldásához.

## <a name="reference-for-test-azurestack"></a>Útmutató a Test-AzureStack

Ez a szakasz áttekintést a Test-AzureStack parancsmag és az ellenőrzési jelentésből összegzését tartalmazza.

### <a name="test-azurestack"></a>Teszt-AzureStack

Az Azure-verem állapotát ellenőrzi. A parancsmag a jelentés az Azure-verem hardver- és állapotát. Támogatási csapatát a jelentés segítségével felgyorsításához megoldásához Azure verem támogatási eseteket.

> [!Note]  
> Teszt-AzureStack előfordulhat, hogy észleli a, hogy a felhő kimaradások esetén, amelyek nem hibáit, például egyetlen lemez vagy egy egyetlen fizikai gazdagépen Csomóponthiba nem sikerült.

#### <a name="syntax"></a>Szintaxis

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Paraméterek

| Paraméter               | Érték           | Szükséges | Alapértelmezett |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | Nem       | HAMIS   |
| DoNotDeployTenantVm     | SwitchParameter | Nem       | HAMIS   |
| AdminCredential         | PSCredential    | Nem       | NA      |
| StorageConnectionString | Karakterlánc          | Nem       | NA      |
| Lista                    | SwitchParameter | Nem       | HAMIS   |
| Kihagyás                  | Karakterlánc          | Nem       | NA      |
| Belefoglalás                 | Karakterlánc          | Nem       | NA      |

A Test-AzureStack parancsmag a következő általános paramétereket támogatja: részletes, hibakeresési, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable és OutVariable. További információkért lásd: [közös paraméterek](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Teszt-AzureStack példák

Az alábbi példák azt feltételezik, hogy néven van bejelentkezve **CloudAdmin** és a kiemelt végpont (EGP) elérésekor. Útmutatásért lásd: [használatával a privilegizált végpont Azure verem](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Teszt-AzureStack nélkül felhős rendszerekben interaktívan futtatja

Egy EGP-munkamenetben futtassa:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Futtassa a Test-AzureStack felhős rendszerekben

Teszt-AzureStack felhős rendszerekben futtatni az Azure-verem használható. Ezek a forgatókönyvek a következőket biztosítják:

 - Erőforráscsoportok
 - Csomagok létrehozása
 - Ajánlatok létrehozása
 - Storage-fiókok létrehozása
 - A virtuális gép létrehozása
 - A tesztkörnyezet létrehozott tárfiókban használatával blob műveleteket
 - A tesztkörnyezet létrehozott tárfiókban használatával várólista műveleteket
 - A tesztkörnyezet létrehozott tárfiókban használatával tábla műveleteket

A felhő esetekben szükséges felhőalapú rendszergazdai hitelesítő adatokat. 
> [!Note]  
> Az Active Directory összevont szolgáltatások (ADFS) hitelesítő adatok használatával felhős rendszerekben nem futtatható. A **teszt-AzureStack** parancsmag keresztül az EGP csak érhető el. De a EGP nem támogatja az AD FS hitelesítő adatokat.

Írja be a felhő rendszergazdai felhasználónév UPN formátumban serviceadmin@contoso.onmicrosoft.com (AAD). Amikor a rendszer kéri, írja be a felhőalapú rendszergazdai fiók jelszavát.

Egy EGP-munkamenetben futtassa:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Futtassa a Test-AzureStack felhős rendszerekben nélkül

Egy EGP-munkamenetben futtassa:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Rendelkezésre álló Tesztelési forgatókönyvek listája:

Egy EGP-munkamenetben futtassa:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>A megadott teszt futtatása

Egy EGP-munkamenetben futtassa:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Meghatározott tesztek kizárása:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>Ellenőrző teszt

A következő táblázat összefoglalja az érvényesítési tesztek futtatásához teszt-AzureStack.

| Name (Név)                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Az Azure verem Felhőszolgáltatóknak infrastruktúra összegzése                                                                                  |
| Az Azure verem tárolási szolgáltatások – összefoglalás                                                                                              |
| Az Azure verem infrastruktúra szerepkör példány összegzése                                                                                  |
| Az Azure verem felhőalapú infrastruktúra kihasználtsági üzemeltetési                                                                              |
| A verem Azure infrastruktúra-kapacitás                                                                                               |
| A verem az Azure portál és API-összefoglalót                                                                                                |
| Azure verem Azure Resource Manager-tanúsítvány adatai                                                                                               |
| Infrastruktúra felügyeletvezérlő, a hálózati vezérlő, a tárolási szolgáltatások és a kiemelt végpont infrastruktúra-szerepkörök          |
| Infrastruktúra felügyeletvezérlő, a hálózati vezérlő, a tárolási szolgáltatások és a kiemelt végpont infrastruktúra Szerepkörpéldányokat |
| Az Azure infrastruktúra-szerepkör verem összegzése                                                                                           |
| Az Azure verem Felhőszolgáltatások Service Fabric                                                                                         |
| Az Azure verem infrastruktúra szerepkör példány teljesítmény                                                                              |
| Az Azure verem felhő állomás létrehozásának összegzése                                                                                        |
| Az Azure verem szolgáltatás erőforrás használat összegzése                                                                                  |
| Az Azure verem skálázási egység kritikus események (utolsó 8 óra)                                                                             |
| Az Azure verem tárolási szolgáltatások – összefoglalás fizikai lemezek                                                                               |

## <a name="next-steps"></a>További lépések

 - Azure verem diagnosztikai eszközök és a probléma naplózás kapcsolatos további információkért lásd: [ Azure verem diagnosztikai eszközök](azure-stack-diagnostics.md).
 - Hibaelhárítással kapcsolatos további tudnivalókért lásd: [Microsoft Azure verem hibaelhárítása](azure-stack-troubleshooting.md)