---
title: Azure Storage-fiók áthelyezése másik régióba | Microsoft Docs
description: Bemutatja, hogyan helyezhető át egy Azure Storage-fiók egy másik régióba.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 76648428e6adcaed579b0e4f1896fdf83e11a8b6
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348860"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Azure Storage-fiók áthelyezése másik régióba

Egy Storage-fiók áthelyezéséhez hozzon létre egy másolatot a Storage-fiókról egy másik régióban. Ezután helyezze át az adatait a fiókba a AzCopy használatával vagy más választott eszközzel.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> 
> * Sablon exportálása.
> * Módosítsa a sablont úgy, hogy hozzáadja a célként megadott régiót és a Storage-fiók nevét.
> * A sablon üzembe helyezésével hozza létre az új Storage-fiókot.
> * Konfigurálja az új Storage-fiókot.
> * Helyezze át az adatátvitelt az új Storage-fiókba.
> * Törölje az erőforrásokat a forrás régióban.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a fiók által használt szolgáltatások és szolgáltatások támogatottak a célként megadott régióban.

- Az előzetes verziójú funkciók esetében győződjön meg arról, hogy az előfizetése engedélyezett a célként megadott régióban.

<a id="prepare" />

## <a name="prepare"></a>Előkészítés

Első lépésként exportálja, majd módosítsa a Resource Manager-sablont. 

### <a name="export-a-template"></a>Sablon exportálása

Ez a sablon a Storage-fiókot leíró beállításokat tartalmaz. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Sablon exportálása Azure Portal használatával:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Storage-fiókját.

3. Válassza > **Beállítások** > **Exportálás sablon**lehetőséget.

4. Válassza a **Letöltés** lehetőséget a **sablon exportálása** panelen.

5. Keresse meg a portálról letöltött. zip fájlt, és bontsa ki a fájlt egy tetszőleges mappába.

   Ez a zip-fájl tartalmazza azokat a. JSON fájlokat, amelyek tartalmazzák a sablont és a parancsfájlokat a sablon telepítéséhez.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Sablon exportálása a PowerShell használatával:

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Ha az identitás egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést az áthelyezni kívánt Storage-fiók előfizetésére.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportálja a forrásként szolgáló Storage-fiók sablonját. Ezek a parancsok egy JSON-sablont mentenek az aktuális könyvtárba.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>A sablon módosítása 

Módosítsa a sablont úgy, hogy megváltoztatja a Storage-fiók nevét és a régióját.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

A sablon üzembe helyezése Azure Portal használatával:

1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.

2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **Template deployment** lehetőséget.

    ![Azure Resource Manager-sablonkönyvtár](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Kattintson a **Létrehozás** gombra.

5. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.

6. Válassza a **fájl betöltése**lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött **template. JSON** fájl betöltéséhez.

7. A **template. JSON** fájlban nevezze el a cél Storage-fiókot a Storage-fiók nevének alapértelmezett értékének megadásával. Ez a példa a Storage-fiók nevének alapértelmezett értékét állítja be `mytargetaccount` értékre.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA** = **CentralUS**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

A sablon üzembe helyezése a PowerShell használatával:

1. A **template. JSON** fájlban nevezze el a cél Storage-fiókot a Storage-fiók nevének alapértelmezett értékének megadásával. Ez a példa a Storage-fiók nevének alapértelmezett értékét állítja be `mytargetaccount` értékre.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Szerkessze a **Location (hely** ) tulajdonságot a **template. JSON** fájlban a célként megadott régióban. Ez a példa a célként megadott régiót `eastus` értékre állítja be.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    A [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) parancs futtatásával lekérheti a régiókódokat.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Áthelyezés

A sablon üzembe helyezésével hozzon létre egy új Storage-fiókot a célként megadott régióban. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

1. Mentse a **template. JSON** fájlt.

2. Adja meg vagy válassza ki a tulajdonságértékek értékét:

- **Előfizetés**: Válasszon ki egy Azure-előfizetést.

- **Erőforráscsoport**: Válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét.

- **Hely**: Válasszon egy Azure-beli helyet.

3. Jelölje **be az Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet, majd kattintson a **Vásárlás kiválasztása** gombra.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Szerezze be azt az előfizetés-azonosítót, amelyben a cél nyilvános IP-címet a [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)használatával szeretné telepíteni:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. A következő parancsok használatával telepítheti a sablont:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Az új Storage-fiók konfigurálása

Néhány funkció nem exportál sablonba, ezért hozzá kell adnia őket az új Storage-fiókhoz. 

A következő táblázat felsorolja ezeket a funkciókat, valamint útmutatást nyújt azok hozzáadásához az új Storage-fiókhoz.

| Funkció    | Útmutatás    |
|--------|-----------|
| **Életciklus-kezelési szabályzatok** | [Azure Blob Storage-életciklus felügyelete](../blobs/storage-lifecycle-management-concepts.md) |
| **Statikus webhelyek** | [Statikus webhely üzemeltetése az Azure Storage-ban](../blobs/storage-blob-static-website-how-to.md) |
| **Esemény-előfizetések** | [Reagálás Blob Storage-eseményekre](../blobs/storage-blob-event-overview.md) |
| **Riasztások** | [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Az egyéni tartományokkal rendelkező Blobok elérése a Azure CDN használatával HTTPS-kapcsolaton keresztül](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Ha a forrásként szolgáló Storage-fiókhoz állít be egy CDN-t, csak módosítsa a meglévő CDN eredetét az új fiók elsődleges blob Service-végpontján (vagy az elsődleges statikus webhely végpontján). 

### <a name="move-data-to-the-new-storage-account"></a>Adatáthelyezés az új Storage-fiókba

Az alábbi módokon helyezheti át az adatait.

:heavy_check_mark: **Azure Storage Explorer**

  Könnyen használható, és kisebb adatkészletekhez is alkalmas. Másolja a tárolókat és a fájlmegosztást, majd illessze be őket a célként megadott fiókba.

  Lásd: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/);

:heavy_check_mark: **AzCopy**

  Ez az előnyben részesített megközelítés. Teljesítményre optimalizált.  Az egyik módszer, hogy gyorsabb, az Adatmásolás közvetlenül a Storage-kiszolgálók között történik, így a AzCopy nem használja a számítógép hálózati sávszélességét. Használjon AzCopy a parancssorban vagy egy egyéni parancsfájl részeként.

  Lásd: Ismerkedés [a AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Azure Data Factory** 

  Ezt az eszközt csak akkor használja, ha olyan funkciókra van szüksége, amelyek nem támogatottak a AzCopy jelenlegi kiadásában. Például a AzCopy jelenlegi kiadásában a Blobok nem másolhatók olyan fiókok között, amelyek hierarchikus névtérrel rendelkeznek. Emellett a AzCopy nem őrzi meg a fájlok hozzáférés-vezérlési listáját vagy a fájl időbélyegét (például: létrehozás és módosítás időbélyegek). 

  Tekintse meg az alábbi hivatkozásokat:
  - [Adatok másolása az Azure Blob Storage-ba vagy onnan az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Adatok másolása Azure Data Lake Storage Gen2 a Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Adatok másolása az Azure File Storageba vagy az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Adatok másolása az Azure Table Storage-ba és onnan az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Elvetés vagy tisztítás

Ha az üzembe helyezést követően el szeretné indítani a műveletet, törölheti a cél Storage-fiókot, és megismételheti a cikk [előkészítés](#prepare) és [Áthelyezés](#move) szakaszában ismertetett lépéseket.

A módosítások elvégzéséhez és a Storage-fiók áthelyezésének befejezéséhez törölje a forrásként szolgáló Storage-fiókot.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Storage-fiók eltávolítása a Azure Portal használatával:

1. A Azure Portalban bontsa ki a bal oldalon található menüt a szolgáltatások menüjének megnyitásához, majd válassza a **Storage-fiókok** lehetőséget a Storage-fiókok listájának megjelenítéséhez.

2. Keresse meg a törölni kívánt cél Storage-fiókot, és kattintson a jobb gombbal a lista jobb oldalán található **további** gombra ( **...** ).

3. Válassza a **Törlés**lehetőséget, és erősítse meg.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához, beleértve az új Storage-fiókot is, használja a [Remove-AzStorageAccount](/powershell/module/az.resources/remove-azstorageaccount) parancsot:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Storage-fiókot helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
