---
title: Megosztott, saját üzemeltetésű integrációs modul létrehozása a PowerShell-lel
description: Megtudhatja, hogyan hozhat létre megosztott, saját üzemeltetésű integrációs modult Azure Data Factory, így több adatfeldolgozó is hozzáférhet az integrációs modulhoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 8422d6978c21744696e3d37c34fdd867b014a19e
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655734"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Megosztott, saját üzemeltetésű integrációs modul létrehozása Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez az útmutató bemutatja, hogyan hozhat létre megosztott, saját üzemeltetésű integrációs modult Azure Data Factoryban. Ezt követően használhatja a megosztott saját üzemeltetésű integrációs modult egy másik adatgyárban.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Megosztott saját üzemeltetésű integrációs modul létrehozása Azure Data Factory felhasználói felület használatával

Ha közösen üzemeltetett IR-t szeretne létrehozni Azure Data Factory felhasználói felülettel, hajtsa végre a következő lépéseket:

1. A saját üzemeltetésű integrációs modulban válassza a hozzáférés **engedélyezése egy másik adat-előállítónak** lehetőséget, majd az "Integration Runtime Setup" lapon válassza ki azt az adat-előállítót, amelyben létre kívánja hozni a csatolt IR-t.
      
    ![Engedélyek megadására szolgáló gomb a megosztás lapon](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Jegyezze fel, és másolja a saját üzemeltetésű integrációs modul fenti "erőforrás-AZONOSÍTÓját" a megosztáshoz.
         
3. Hozzon létre egy új, saját üzemeltetésű IR-t (csatolt) az adatelőállítóban, és adja meg az erőforrás-azonosítót.
      
    ![Saját üzemeltetésű integrációs modul létrehozásának gombja](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![A társított saját üzemeltetésű integrációs modul létrehozásának gombja](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Név és erőforrás-azonosító mezői](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Megosztott saját üzemeltetésű IR létrehozása Azure PowerShell használatával

Ha Azure PowerShell használatával szeretné létrehozni a megosztott saját üzemeltetésű integrációs modult, hajtsa végre a következő lépéseket: 
1. Adat-előállító létrehozása 
1. Hozzon létre egy saját üzemeltetésű integrációs modult.
1. Ossza meg a saját üzemeltetésű integrációs modult más adatgyárakkal.
1. Hozzon létre egy társított integrációs modult.
1. A megosztás visszavonása.

### <a name="prerequisites"></a>Előfeltételek 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) . 

- **Azure PowerShell**. Kövesse a következő témakör utasításait: [Install Azure PowerShell for Windows with PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps). A PowerShell használatával parancsfájlt futtathat egy olyan saját üzemeltetésű integrációs modul létrehozásához, amely más adatgyárakkal is megosztható. 

> [!NOTE]  
> Azon Azure-régiók listáját, amelyekben a Data Factory jelenleg elérhető, válassza ki azokat a régiókat, amelyek érdeklik a [régiókban elérhető termékeken](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE).

1. Hozzon létre változókat. Másolja és illessze be az alábbi szkriptet. Cserélje le a változókat (például **SubscriptionName** és **ResourceGroupName**) a tényleges értékekkel: 

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

1. Jelentkezzen be, és válasszon egy előfizetést. Adja hozzá a következő kódot a szkripthez a bejelentkezéshez, és válassza ki az Azure-előfizetését:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Hozzon létre egy erőforráscsoportot és egy adatelőállítót.

    > [!NOTE]  
    > Ez a lépés nem kötelező. Ha már rendelkezik egy adatelőállítóval, ugorja át ezt a lépést. 

    Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancs használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy nevű erőforráscsoportot `myResourceGroup` a WestEurope helyen: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Futtassa a következő parancsot egy adat-előállító létrehozásához: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime létrehozása

> [!NOTE]  
> Ez a lépés nem kötelező. Ha már rendelkezik olyan saját üzemeltetésű integrációs futtatókörnyezettel, amelyet más adatgyárakkal szeretne megosztani, ugorja át ezt a lépést.

Futtassa a következő parancsot egy saját üzemeltetésű integrációs modul létrehozásához:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Az Integration Runtime hitelesítési kulcsának beszerzése és a csomópont regisztrálása

Futtassa a következő parancsot a saját üzemeltetésű integrációs modul hitelesítési kulcsának beszerzéséhez:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A válasz tartalmazza a saját üzemeltetésű integrációs modulhoz tartozó hitelesítési kulcsot. Ezt a kulcsot használja az Integration Runtime csomópontjának regisztrálása során.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>A saját üzemeltetésű integrációs modul telepítése és regisztrálása

1. Töltse le a saját üzemeltetésű Integration Runtime telepítőjét [Azure Data Factory Integration Runtimeról](https://aka.ms/dmg).

2. Futtassa a telepítőt a saját üzemeltetésű integráció helyi számítógépre történő telepítéséhez.

3. Regisztrálja az új saját üzemeltetésű integrációt az előző lépésben lekért hitelesítési kulccsal.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>A saját üzemeltetésű integrációs modul megosztása egy másik adatgyárral

#### <a name="create-another-data-factory"></a>Másik adatelőállító létrehozása

> [!NOTE]  
> Ez a lépés nem kötelező. Ha már rendelkezik a-vel megosztani kívánt adatelőállítóval, ugorja át ezt a lépést.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Engedély megadása

Adjon engedélyt az adat-előállítónak, amelynek hozzá kell férnie a létrehozott és regisztrált saját üzemeltetésű integrációs modulhoz.

> [!IMPORTANT]  
> Ne ugorja át ezt a lépést!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Társított saját üzemeltetésű integrációs modul létrehozása

A következő parancs futtatásával hozzon létre egy társított saját üzemeltetésű integrációs modult:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Most már használhatja a társított integrációs modult bármelyik társított szolgáltatásban. A társított Integration Runtime a megosztott integrációs modult használja a tevékenységek futtatásához.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Az integrációs modul megosztásának visszavonása egy adatgyárból

A következő parancs futtatásával vonhatja vissza az adatok előállítójának a megosztott integrációs modulból való hozzáférését:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Ha el szeretné távolítani a meglévő társított integrációs modult, futtassa a következő parancsot a megosztott integrációs futtatókörnyezeten:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Következő lépések

- Tekintse át [az Integration Runtime fogalmait a Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Ismerje meg, hogyan [hozhat létre saját üzemeltetésű integrációs modult a Azure Portal](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).
