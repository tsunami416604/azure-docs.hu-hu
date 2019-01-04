---
title: Egy megosztott saját üzemeltetésű integrációs modul létrehozása az Azure Data Factoryban a PowerShell-lel |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy megosztott saját üzemeltetésű integrációs modul az Azure Data Factoryben, hogy több adat-előállítók hozzáférhessen az integrációs modul.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: 2f132bc14e62636a3ddd269323497733de1007b7
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021152"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Az Azure Data Factoryban a PowerShell-lel megosztott saját üzemeltetésű integrációs modul létrehozása

Ez az útmutató lépésről lépésre bemutatja, hogyan hozhat létre egy megosztott saját üzemeltetésű integrációs modul az Azure Data Factoryban az Azure PowerShell használatával. Majd egy másik data factoryben a megosztott saját üzemeltetésű integrációs modult is használhatja. Ebben az oktatóanyagban a következő lépéseket hajtja végre: 

1. Adat-előállító létrehozása 
1. Hozzon létre egy saját üzemeltetésű integrációs modult.
1. A saját üzemeltetésű integrációs modul megosztása más adat-előállítók.
1. Hozzon létre egy kapcsolt integrációs modult.
1. Visszavonja a megosztást.

## <a name="prerequisites"></a>Előfeltételek 

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt. 

- **Azure PowerShell**. Kövesse a [Azure PowerShell telepítése a Windows a Powershellgettel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.11.0). PowerShell-parancsprogrammal hozzon létre egy saját üzemeltetésű integrációs modult, amelyek megoszthatók a többi adat-előállítók használhatja. 

> [!NOTE]  
> Azure-régióban, amelyben a Data Factory jelenleg listája, válassza ki az Önt érdeklő a régiók [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE).

1. Változók létrehozása. Másolja és illessze be az alábbi parancsfájlt. Cserélje le a változókat, például **SubscriptionName** és **ResourceGroupName**, tényleges értékekre: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Jelentkezzen be, és válasszon ki egy előfizetést. Adja meg a következő kódot a szkripthez jelentkezik be, és válassza ki az Azure-előfizetés:

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. Hozzon létre egy erőforráscsoportot és a egy adat-előállítót.

    > [!NOTE]  
    > Ez a lépés nem kötelező. Ha már rendelkezik egy adat-előállítót, kihagyhatja ezt a lépést. 

    Hozzon létre egy [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) használatával a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.11.0) parancsot. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy erőforráscsoportot, nevű `myResourceGroup` WestEurope a helyen: 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Futtassa a következő parancsot egy adat-előállító létrehozásához: 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime létrehozása

> [!NOTE]  
> Ez a lépés nem kötelező. Ha már rendelkezik a saját üzemeltetésű integrációs modul többi adat-előállítók megosztani kívánt, kihagyhatja ezt a lépést.

Futtassa a következő parancsot egy saját üzemeltetésű integrációs modul létrehozásához:

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Az integration runtime hitelesítési kulcs beszerzése és a egy csomópont regisztrálása

Futtassa a következő parancsot a saját üzemeltetésű integration runtime hitelesítési kulcsa beolvasásához:

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A válasz tartalmazza a saját üzemeltetésű integration runtime hitelesítési kulcsa. Ha regisztrálja az integration runtime csomópontjának használhatja ezt a kulcsot.

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Telepítse és regisztrálja a saját üzemeltetésű integrációs modul

1. Töltse le a saját üzemeltetésű integration runtime-telepítőt a [Azure Data Factory integrációs modul](https://aka.ms/dmg).

2. Futtassa a telepítőt a saját üzemeltetésű integrációs telepítése helyi számítógépre.

3. Az új saját üzemeltetésű integrációs regisztrálja az előző lépésben lekért hitelesítési kulcs.

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>A saját üzemeltetésű integrációs modul megosztása egy másik data factoryval

### <a name="create-another-data-factory"></a>Egy másik adat-előállító létrehozása

> [!NOTE]  
> Ez a lépés nem kötelező. Ha már rendelkezik az adat-előállítót, amely a megosztani kívánt, kihagyhatja ezt a lépést.

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Engedély megadása

Az adat-előállítót, amelyek a saját üzemeltetésű integrációs modul létrehozása, és regisztrálva hozzáférésre van szüksége a engedélyt.

> [!IMPORTANT]  
> Ebben a lépésben se hagyja ki!

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>A csatolt saját üzemeltetésű integrációs modul létrehozása

Futtassa a következő parancsot egy társított saját üzemeltetésű integrációs modul létrehozásához:

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Most már használhatja a társított integrációs modul bármely társított szolgáltatásban. A társított integrációs modul használja a megosztott integrációs modul tevékenységek futtatásához.

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Az integration runtime az adat-előállító megosztása visszavonása

Adat-előállító, a megosztott integrációs modul a hozzáférés visszavonásához futtassa a következő parancsot:

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Távolítsa el a meglévő kapcsolt integrációs modult, a következő parancsot a megosztott integrációs modul szemben:

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [az Azure Data Factory integrációs modul fogalmak](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Ismerje meg, hogyan [egy saját üzemeltetésű integrációs modul létrehozása az Azure Portalon](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).
