---
title: A helyi ügynök telepítse és tesztelje a rendszerképet a virtuális gépek az Azure Stack érvényesítési szolgáltatásként |} A Microsoft Docs
description: A helyi ügynök telepítése, és tesztelje a rendszerkép virtuális gépeket, az Azure Stack érvényesítési szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 78136ab00dcba2f8a99df36ba99d384b49995882
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159214"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>A helyi ügynök és a teszt virtuális gépek üzembe helyezése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ismerje meg, hogy az érvényesítés egy szolgáltatás (VaaS) helyi ügynök használata a hardver ellenőrzése. A helyi ügynök kell üzembe helyezni az Azure Stack megoldás ellenőrző tesztek futtatása előtt kell kiértékelni.

> [!Note]  
> Győződjön meg arról, hogy a gép, ahol a helyi ügynök fut, nem nem férhet hozzá, az interneten. A gép csak a felhasználók számára, akik jogosultak az Azure Stack VaaS használatára elérhetőnek kell lennie.

A virtuális gépek teszteléséhez:

1. A helyi ügynök telepítése
2. A rendszer a hibák behelyezése
3. A helyi ügynök futtatása

## <a name="download-and-start-the-local-agent"></a>Töltse le és indítsa el a helyi ügynök

Töltse le az ügynök olyan gépre, amely megfelel az előfeltételeknek, amely nem része az Azure Stack rendszer, de egy, amely hozzáfér az Azure Stack-végpontokra irányuló adatközpontjában.

### <a name="machine-prerequisites"></a>Gép előfeltételei

Ellenőrizze, hogy a gép megfelel-e a következő feltételeknek:

- Az összes Azure Stack-végpontra való hozzáférés
- .NET 4.6 és a PowerShell 5.0-s telepítése
- Legalább 8 GB RAM
- Legalább 8 processzormagokkal
- 200 GB-os minimális szabad lemezterület
- Állandó hálózati kapcsolat az internethez

Az Azure Stack, a rendszer a tesztelt. A gép nem lehet része az Azure Stack, vagy az Azure Stack-felhő üzemeltet.

### <a name="download-and-install-the-agent"></a>Az ügynök letöltése és telepítése

1. Nyisson meg egy rendszergazda jogú parancssort a gépen a tesztek futtatásához használhat Windows PowerShell.
2. Futtassa a következő parancsot a helyi ügynök letöltéséhez:

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. Futtassa a következő parancsot a helyi ügynök függőségek telepítéséhez:

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **Paraméterek**

    | Paraméter | Leírás |
    | --- | --- |
    | aadServiceAdminUser | A globális rendszergazda az Azure AD-bérlője számára. Elképzelhető például vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | A globális rendszergazda jelszava. |
    | AadTenantId | Az Azure AD-bérlő azonosítója az Azure-fiók regisztrálva van szolgáltatásként érvényesítése. |
    | ExternalFqdn | A konfigurációs fájlban szereplő kaphat a teljes tartománynevét. További útmutatást lásd: [teszt paramétereiben a szolgáltatás az Azure Stack érvényesítési](azure-stack-vaas-parameters-test.md). |
    | Régió | Az Azure AD-bérlő régiója. |

A parancs letölti egy nyilvános rendszerkép tárház (PIR) rendszerkép (operációs rendszer VHD), és másolja egy Azure blob storage-ból az Azure Stack storage. 

![Előfeltételek letöltése](media/installingprereqs.png)

> [!Note]  
> Ha a hálózat lassúsága tapasztal, ezek a lemezképek letöltésekor, külön-külön letölteni azokat egy helyi megosztással, és adja meg a paramétert **- LocalPackagePath** *FileShareOrLocalPath*. Annak további útmutatást a PIR letöltési szakaszában [leírók lassú hálózati kapcsolat](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) , [érvényesítési szolgáltatás hibaelhárítása](azure-stack-vaas-troubleshoot.md).

## <a name="fault-injection"></a>Hibabeszúrás

A Microsoft Azure Stack úgy, rugalmasság és kezelni kívánt meghibásodások többféle szoftver- és a hibák. A rendszer a hibák aránya hibabeszúrás növekszik. Ez segít a problémák korábban tárhat fel, hogy csökkentheti, hogy a rendszer leállásához incidensek számát.

A következő parancsokat a rendszer a hibák behelyezése.

1. Nyissa meg a Windows Powershellt rendszergazda jogú parancssorban.

2. Futtassa az alábbi parancsot:

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>Az ügynök futtatása

1. Nyissa meg a Windows Powershellt rendszergazda jogú parancssorban.

2. Futtassa az alábbi parancsot:

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **Paraméterek**  
    
    | Paraméter | Leírás |
    | --- | --- |
    | VaaSUserId | Felhasználói azonosító segítségével jelentkezzen be a VaaS portálra (például UserName@Contoso.com) |
    | VaaSTenantId | Az Azure AD-bérlő azonosítója az Azure-fiók regisztrálva van szolgáltatásként érvényesítése. |

    > [!Note]  
    > Az ügynök futtatásakor az aktuális munkakönyvtárban kell lennie a végrehajtható fájl, tevékenység motor gazdagép helyét **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Ha nem látja a jelentett hibákat, majd a helyi ügynök sikeres volt. A konzolablakban megjelenik az alábbi példa szöveget.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Az ügynök elindítva](media/startedagent.png)

Az ügynök egyedileg azonosítja a neve. Alapértelmezés szerint a teljesen minősített neve (FQDN) nevét, ahol lett elindítva a gép használja. Kell minimálisra csökkenthető az ablakban bármely véletlen kattint, az ablak elkerülése érdekében minden egyéb művelet módosítja a fókuszt szünetelteti.

## <a name="next-steps"></a>További lépések

- [Egy új Azure Stack megoldás ellenőrzése](azure-stack-vaas-validate-solution-new.md)  
- Ha lassú vagy időszakos internetkapcsolattal, letöltheti a PIR-lemezképet. További információkért lásd: [leírók lassú hálózati kapcsolat](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).
- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
