---
title: Azure Storage-fiók áthelyezése másik régióba | Microsoft dokumentumok
description: Bemutatja, hogyan helyezhet át egy Azure Storage-fiókot egy másik régióba.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: c8578c518ac45bea147790028c2904c7ce36fffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459032"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Azure Storage-fiók áthelyezése másik régióba

Tárfiók áthelyezéséhez hozzon létre egy másolatot a tárfiókegy másik régióban. Ezután helyezze át az adatokat a fiókba az AzCopy vagy más választott eszköz használatával.

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> 
> * Sablon exportálása.
> * Módosítsa a sablont a célterület és a tárfiók nevének hozzáadásával.
> * Telepítse a sablont az új tárfiók létrehozásához.
> * Konfigurálja az új tárfiókot.
> * Adatok áthelyezése az új tárfiókba.
> * Törölje az erőforrásokat a forrásrégióban.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a fiók által használt szolgáltatások és funkciók támogatottak a célrégióban.

- Az előzetes verziójú funkciók hoz győződjön meg arról, hogy az előfizetés a célrégióban szerepel.

<a id="prepare" />

## <a name="prepare"></a>Előkészítés

Első lépésekhez exportáljon, majd módosítsa az Erőforrás-kezelő sablonját. 

### <a name="export-a-template"></a>Sablon exportálása

Ez a sablon a tárfiókot leíró beállításokat tartalmazza. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Sablon exportálása az Azure Portal használatával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **Összes erőforrás lehetőséget,** majd válassza ki a tárfiókot.

3. Válassza > **Beállítások** > **exportálása sablont.**

4. Válassza a **Letöltés gombot** az **Exportálás sablon** panelen.

5. Keresse meg a portálról letöltött .zip fájlt, és csomagolja ki a fájlt egy ön által választott mappába.

   Ez a zip fájl tartalmazza a sablont és a sablon t.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sablon exportálása a PowerShell használatával:

1. Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Ha az identitás egynél több előfizetéshez van társítva, állítsa be az aktív előfizetést az áthelyezni kívánt tárfiók előfizetésére.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportálja a forrástárfiók sablonját. Ezek a parancsok egy jsonsablont mentenek az aktuális könyvtárba.

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

Módosítsa a sablont a tárfiók nevének és régiójának módosításával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A sablon üzembe helyezése az Azure Portal használatával:

1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.

2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **Template deployment** lehetőséget.

    ![Azure Resource Manager-sablonkönyvtár](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Kattintson a **Létrehozás** gombra.

5. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.

6. Válassza **a Fájl betöltése**lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött **template.json** fájl betöltéséhez.

7. A **template.json** fájlban nevezze el a céltárfiókot a tárfiók nevének alapértelmezett értékének beállításával. Ez a példa a tárfiók nevének alapértelmezett értékét állítja a értékre. `mytargetaccount`
    
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
    A régióhely-kódok beszerzéséhez olvassa el az Azure Locations ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) [(A régió hely](https://azure.microsoft.com/global-infrastructure/locations/)  A régió kódja a terület neve szóközök nélkül, **az USA** = középső**központja**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A sablon központi telepítése a PowerShell használatával:

1. A **template.json** fájlban nevezze el a céltárfiókot a tárfiók nevének alapértelmezett értékének beállításával. Ez a példa a tárfiók nevének alapértelmezett értékét állítja a értékre. `mytargetaccount`
    
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

2. A **template.json** fájlban lévő **helytulajdonság** szerkesztése a célterületre. Ez a példa a `eastus`célrégiót állítja a értékre.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    A régiókódokat a [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) parancs futtatásával szerezheti be.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Áthelyezés

Telepítse a sablont egy új tárfiók létrehozásához a célrégióban. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Mentse a **template.json** fájlt.

2. Adja meg vagy jelölje ki a tulajdonságértékeket:

- **Előfizetés**: Válasszon egy Azure-előfizetést.

- **Erőforráscsoport**: Válassza az **Új létrehozása** lehetőséget, majd adjon nevet az erőforráscsoportnak.

- **Hely**: Válasszon egy Azure-helyet.

3. Kattintson az **Elfogadom a fenti feltételeket** jelölőnégyzetre, majd kattintson a **Vásárlás kiválasztása** gombra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Szerezze be azt az előfizetés-azonosítót, ahol a célnyilvános [IP-címet a Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)segítségével szeretné telepíteni:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. A sablon üzembe helyezéséhez használja az alábbi parancsokat:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Az új tárfiók konfigurálása

Egyes funkciók nem exportálják a sablont, így hozzá kell adnia őket az új tárfiókhoz. 

Az alábbi táblázat ezeket a funkciókat, valamint útmutatást nyújt az új tárfiókhoz való hozzáadásához.

| Szolgáltatás    | Útmutatás    |
|--------|-----------|
| **Életciklus-kezelési irányelvek** | [Az Azure Blob Storage-életciklus felügyelete](../blobs/storage-lifecycle-management-concepts.md) |
| **Statikus webhelyek** | [Statikus webhely üzemeltetése az Azure Storage-ban](../blobs/storage-blob-static-website-how-to.md) |
| **Esemény-előfizetések** | [Reagálás Blob Storage-eseményekre](../blobs/storage-blob-event-overview.md) |
| **Riasztások** | [Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával](../../azure-monitor/platform/alerts-activity-log.md) |
| **Tartalomkézbesítési hálózat (CDN)** | [Az Azure CDN használata az egyéni tartományokkal HTTPS-kapcsolaton keresztüli blobok eléréséhez](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Ha cdn-t állít be a forrástárfiókhoz, csak módosítsa a meglévő CDN eredetét az új fiók elsődleges blobszolgáltatás-végpontjára (vagy az elsődleges statikus webhely végpontjára). 

### <a name="move-data-to-the-new-storage-account"></a>Adatok áthelyezése az új tárfiókba

Az alábbiakban az adatok áthelyezését is lehatja.

:heavy_check_mark: **Azure Storage Explorer**

  Könnyen használható, és kis adatkészletek hez használható. Másolhatja a tárolókat és a fájlmegosztásokat, majd beillesztheti őket a célfiókba.

  Lásd: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/);

:heavy_check_mark: **AzCopy**

  Ez az előnyben részesített megközelítés. Teljesítményre van optimalizálva.  Az egyik módja, hogy ez gyorsabb, hogy az adatok másolása közvetlenül a tárolókiszolgálók között, így az AzCopy nem használja a hálózati sávszélességet a számítógép. Használja az AzCopy programot a parancssorból vagy egy egyéni parancsfájl részeként.

  Lásd: [Az AzCopy – Első lépések](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Azure Data Factory** 

  Ezt az eszközt csak akkor használja, ha olyan funkciókra van szüksége, amelyeket az AzCopy jelenlegi kiadása nem támogat. Az AzCopy jelenlegi kiadásában például nem másolhatja a blobokat a hierarchikus névtérrel rendelkező fiókok között. Az AzCopy nem őrzi meg a fájlhozzáférés-vezérlési listákat vagy a fájl időbélyegeit (például időbélyegzők létrehozása és módosítása). 

  Lásd az alábbi linkeket:
  - [Adatok másolása az Azure Blob storage-ba vagy onnan az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Adatok másolása az Azure Data Lake Storage Gen2 szolgáltatásba vagy onnan az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Adatok másolása az Azure Data Storage szolgáltatásból vagy az Azure Data Storage szolgáltatásba az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Adatok másolása az Azure Table storage-ba és onnan az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Selejtezés vagy tisztítás

A telepítés után, ha szeretné újrakezdeni, törölheti a céltárfiókot, és ismételje meg a cikk [Előkészítés](#prepare) és [áthelyezés](#move) szakaszában ismertetett lépéseket.

A módosítások véglegesítéséhez és a tárfiók áthelyezésének befejezéséhez törölje a forrástárfiókot.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Tárfiók eltávolítása az Azure Portal használatával:

1. Az Azure Portalon bontsa ki a menüt a bal oldalon a szolgáltatások menüjének megnyitásához, és válassza **a Storage-fiókok** a tárfiókok listájának megjelenítéséhez.

2. Keresse meg a törölni kívánt céltárfiókot, és kattintson a jobb gombbal a lista jobb oldalán található **Egyebek** gombra (**...**).

3. Válassza **a Törlés**lehetőséget, és erősítse meg.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához, beleértve az új tárfiókot is, használja az [Eltávolítás-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) parancsot:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure storage-fiókot az egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
