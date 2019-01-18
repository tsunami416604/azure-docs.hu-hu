---
title: A PowerShell-lel az Azure Stack Kódérvényesítés automatizálása |} A Microsoft Docs
description: Az Azure Stack érvényesítési a PowerShell használatával automatizálható.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 53d74e9979b9f82d7a76d21517f2fd62ac95787a
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54387965"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>A PowerShell-lel az Azure Stack Kódérvényesítés automatizálása

Ellenőrzés (VaaS) szolgáltatás lehetővé teszi a indítása tesztek segítségével automatizálhatja a **LaunchVaaSTests.ps1** parancsfájlt.

PowerShell a következő munkafolyamatot használhatja:

- Tesztelési fázis

Ebben az oktatóanyagban megismerheti, hogyan hozhat létre olyan parancsfájlt, amely:

> [!div class="checklist"]
> * Előfeltételek telepítése
> * Telepíti és elindítja a helyi ügynök
> * Elindítja a tesztet hajt végre, például integráció, működési, megbízhatóság kategória
> * A vizsgálati eredmények jelentések

## <a name="launch-the-test-pass-workflow"></a>Indítsa el a Tesztmenetek munkafolyamat

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssorban.

2. Futtassa a következő szkriptet az automation-szkript letöltése:

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Futtassa a következő parancsfájlt a megfelelő paraméter értékét:

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Paraméterek**

    | Paraméter | Leírás |
    | --- | --- |
    | VaaSUserId | VaaS felhasználói azonosítóját. |
    | VaaSUserPassword | A VaaS jelszavát. |
    | VaaSAccountTenantId | A VaaS bérlő GUID azonosítója. |
    | VaaSSolutionName | Neve a VaaS megoldás, amely alatt a teszt át fog futni. |
    | VaaSTestPassName | A VaaS teszt neve munkafolyamat létrehozásához adja át. |
    | VaaSTestCategories | `Integration`, `Functional`, vagy a. `Reliability`. Ha több értéket használja, külön az egyes értékek vesszővel elválasztva.  |
    | ServiceAdminUserName | Az Azure Stack szolgáltatás-rendszergazdai fiókot.  |
    | ServiceAdminPassword | Az Azure Stack szolgáltatás jelszava.  |
    | TenantAdminUserName | A rendszergazda az elsődleges bérlő számára.  |
    | TenantAdminPassword | Az elsődleges bérlő jelszava.  |
    | CloudAdminUserName | A felhő-rendszergazda felhasználóneve  |
    | CloudAdminPassword | A felhő rendszergazdájának jelszava.  |

4. Tekintse át a vizsgálat eredményeit. További beállításokért lásd: [figyelése és kezelése a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>További lépések

Az Azure Stack PowerShell kapcsolatos további információkért tekintse át a legújabb modulokat.

> [!div class="nextstepaction"]
> [Az Azure Stack-modul](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
