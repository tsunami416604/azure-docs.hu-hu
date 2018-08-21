---
title: A PowerShell-lel az Azure Stack kódérvényesítés automatizálása |} A Microsoft Docs
description: Azure Stack érvényesítési a PowerShell használatával automatizálható.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235346"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>A PowerShell-lel az Azure Stack kódérvényesítés automatizálása 

Ellenőrzés (VaaS) szolgáltatás lehetővé teszi a indítása tesztek segítségével automatizálhatja a **LaunchVaaSTests.ps1** parancsfájlt.

PowerShell a következő munkafolyamatot használhatja:

- Tesztmenetek munkafolyamat

Ez a szkript magában foglalja a munkafolyamat négy elemei:

- Előfeltételek telepítése.
- Telepíti és a helyi ügynök elindítása.
- Elindítja a tesztet hajt végre, ilyen az integráció, működési, megbízhatóság egy kategóriát.
- Minden teszt eredménye figyelési adja át, és jelentést a jelentések fájl generálása.

## <a name="launch-the-test-pass-workflow"></a>Indítsa el a tesztelési fázisban munkafolyamat

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssorban.

2. Futtassa a következő szkriptet az automation-szkript letöltése:

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. Futtassa a következő szkriptet az értékekkel:

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **Paraméterek**

    | Paraméter | Leírás |
    | --- | --- |
    | VaaSUserld | VaaS felhasználói azonosítóját. | 
    | VaaSUserPassword | A VaaS jelszavát. |
    | ServiceAdminUser | Az Azure Stack szolgáltatás-rendszergazdai fiókot.  |
    | ServiceAdminPassword | Az Azure Stack szolgáltatás jelszava.  |
    | TenantAdminUser | A rendszergazda az elsődleges bérlő számára.  |
    | TenantAdminPassword | Az elsődleges bérlő jelszava.  |
    | FunctionalCategory| Integráció, működési, vagy a. Megbízhatóságát. Ha több értéket használja, külön az egyes értékek vesszővel elválasztva.  |

4. Tekintse át a vizsgálat eredményeit. További információ a vizsgálati eredmények beolvasása: [tesztek figyelése](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>További lépések

 - Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
 - Az Azure Stack PowerShell kapcsolatos további információkért tekintse meg a [Azure Stack-modul](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) hivatkozást.