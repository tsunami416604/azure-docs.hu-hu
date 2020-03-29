---
title: Megosztott, saját üzemeltetésű integrációs futásidő létrehozása a PowerShellhasználatával
description: Ismerje meg, hogyan hozhat létre megosztott, saját üzemeltetésű integrációs futásidejűt az Azure Data Factoryban, így több adatgyár is hozzáférhet az integrációs futásidőhöz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 10/31/2018
ms.openlocfilehash: a2f24d8203ac5fb9724370cbdf4309bdc43c166a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444094"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Megosztott, saját üzemeltetésű integrációs futásidő létrehozása az Azure Data Factoryban

Ez az útmutató bemutatja, hogyan hozhat létre egy megosztott saját üzemeltetésű integrációs futásidejű az Azure Data Factoryban. Ezután használhatja a megosztott saját üzemeltetésű integrációs futásidejű egy másik adat-előállítóban.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Megosztott saját üzemeltetésű infravörös kapcsolat létrehozása az Azure Data Factory felhasználói felületén

Ha az Azure Data Factory felhasználói felületén közös üzemeltetésű infravörös szolgáltatást szeretne létrehozni, a következő lépéseket teheti:

1. A saját üzemeltetésű infravörös megosztandó, adjon engedélyt az adat-gyár, amelyben létre kívánja hozni a csatolt infravörös.
      
    ![A Megosztás lap engedélyének megadására szolgáló gomb](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![Engedélyek hozzárendelésének kijelölése](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. Vegye figyelembe a saját üzemeltetésű infravörös megosztott erőforrásazonosítóját.
      
   ![Az erőforrásazonosító helye](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. Abban az adat-előállítóban, amelyhez az engedélyeket megadták, hozzon létre egy új, saját üzemeltetésű infravörös (csatolt) kapcsolatát, és adja meg az erőforrás-azonosítót.
      
   ![Gomb csatolt, saját üzemeltetésű integrációs futásidejű létrehozásához](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![Név- és erőforrásazonosító-mezők](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Megosztott, saját üzemeltetésű infravörös kapcsolat létrehozása az Azure PowerShell használatával

Ha az Azure PowerShell használatával szeretne közös üzemeltetésű infravörös szolgáltatást létrehozni, a következő lépéseket teheti: 
1. Adat-előállító létrehozása 
1. Hozzon létre egy saját üzemeltetésű integrációs modult.
1. Ossza meg az önkiszolgáló integrációs futásidejű más adatgyárakkal.
1. Hozzon létre egy csatolt integrációs futásidejűt.
1. Vonja vissza a megosztást.

### <a name="prerequisites"></a>Előfeltételek 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. 

- **Az Azure PowerShell**. Kövesse az [Azure PowerShell telepítése Windowsra a PowerShellGet szolgáltatással](https://docs.microsoft.com/powershell/azure/install-az-ps)című témakör utasításait. A PowerShell segítségével parancsfájl futtatásával hozzon létre egy saját üzemeltetésű integrációs futásidejű, amely más adatgyárakkal osztható konkreti. 

> [!NOTE]  
> Az Azure-régiók listájához, ahol a Data Factory jelenleg elérhető, válassza ki azokat a régiókat, amelyek érdeklik a [régiónként elérhető termékeken.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)

### <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE).

1. Változók létrehozása. Másolja és illessze be a következő parancsfájlt. Cserélje le a változókat, például **a SubscriptionName** és **a ResourceGroupName**változókat a tényleges értékekre: 

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

1. Jelentkezzen be, és válasszon ki egy előfizetést. Adja hozzá a következő kódot a parancsfájlhoz a bejelentkezéshez és az Azure-előfizetés kiválasztásához:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Hozzon létre egy erőforráscsoportot és egy adat-előállítót.

    > [!NOTE]  
    > Ez a lépés nem kötelező. Ha már rendelkezik adatelmunkunkorssal, hagyja ki ezt a lépést. 

    Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példa létrehoz `myResourceGroup` egy nyugat-európai helyen elnevezett erőforráscsoportot: 

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
> Ez a lépés nem kötelező. Ha már rendelkezik a saját üzemeltetésű integrációs futásidejű, amely meg szeretné osztani más adatgyárak, hagyja ki ezt a lépést.

Saját üzemeltetésű integrációs futásidejű létrehozásához futtassa a következő parancsot:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Az integrációs futásidejű hitelesítési kulcs beszereznie és regisztrálnia egy csomópontot

Futtassa a következő parancsot az önkiszolgáló integrációs futásidejű hitelesítési kulcs ának lekérni:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A válasz tartalmazza a saját üzemeltetésű integrációs futásidejű hitelesítési kulcsot. Ezt a kulcsot használja az integrációs futásidejű csomópont regisztrálásakor.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Az önkiszolgáló integrációs futásidő telepítése és regisztrálása

1. Töltse le az Azure Data Factory Integration Runtime saját üzemeltetésű integrációs futásidejű [telepítőjét.](https://aka.ms/dmg)

2. Futtassa a telepítőt az önkiszolgáló integráció helyi számítógépre való telepítéséhez.

3. Regisztrálja az új saját üzemeltetésű integrációt az előző lépésben beolvasott hitelesítési kulccsal.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Az önkiszolgáló integrációs futásidő megosztása egy másik adatfeldolgozóval

#### <a name="create-another-data-factory"></a>Másik adatgyár létrehozása

> [!NOTE]  
> Ez a lépés nem kötelező. Ha már rendelkezik a megosztani kívánt adatelmzóval, hagyja ki ezt a lépést.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Engedély megadása

Adjon engedélyt az adat-előállítónak, amelynek hozzá kell férnie a létrehozott és regisztrált saját üzemeltetésű integrációs futásidőeléréséhez.

> [!IMPORTANT]  
> Ne hagyja ki ezt a lépést!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Csatolt, saját üzemeltetésű integrációs futásidő létrehozása

A következő parancs futtatásával hozzon létre egy csatolt, saját üzemeltetésű integrációs futási időt:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Most antól használhatja ezt a csatolt integrációs futásidőt bármely csatolt szolgáltatásban. A csatolt integrációs futásidejű a megosztott integrációs futásidejű tevékenységek futtatásához használja.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Integrációs futásidejű megosztás visszavonása adat-előállítóból

Ha vissza szeretné vonni egy adat-előállító hozzáférését a megosztott integrációs futásórából, futtassa a következő parancsot:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

A meglévő csatolt integrációs futásidejű eltávolításához futtassa a következő parancsot a megosztott integrációs futási idővel:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>További lépések

- Tekintse át [az integrációs futásidejű fogalmakat az Azure Data Factoryban.](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)

- Ismerje meg, hogyan [hozhat létre saját üzemeltetésű integrációs futásidejűt az Azure Portalon.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
