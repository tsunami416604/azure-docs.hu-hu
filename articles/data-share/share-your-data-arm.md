---
title: 'Rövid útmutató: megosztás a szervezeten kívül – Azure-adatmegosztás'
description: Rövid útmutató – az Azure adatmegosztási és Resource Manager-sablont használó ügyfelekkel és partnerekkel való adatmegosztás
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 87b91d353b6d9f64f28892feed92c44ceafc2598
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640922"
---
# <a name="quickstart-share-data-using-azure-data-share-and-resource-manager-templates"></a>Gyors útmutató: adatmegosztás az Azure adatmegosztási és Resource Manager-sablonok használatával

Megtudhatja, hogyan állíthat be új Azure-beli adatmegosztást egy Azure Storage-fiókból Azure Resource Manager sablon használatával, és megkezdheti az adatok megosztását az Azure-szervezeten kívüli ügyfelekkel és partnerekkel. A támogatott adattárak listáját lásd: [támogatott adattárak az Azure-beli adatmegosztásban](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/) közül származik.

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

A következő erőforrások vannak definiálva a sablonban:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft. Storage/storageAccounts/blobServices/tárolók](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft. Storage/storageAccounts/Providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft. DataShare/fiókok](/rest/api/datashare/accounts/create)
* [Microsoft. DataShare/fiókok/megosztások](/rest/api/datashare/shares/create)
* [Microsoft. DataShare/fiókok/megosztások/adatkészletek](/rest/api/datashare/datasets/create)
* [Microsoft. DataShare/fiókok/megosztások/meghívások](/rest/api/datashare/invitations/create)
* [Microsoft. DataShare/fiókok/megosztások/synchronizationSettings](/rest/api/datashare/synchronizationsettings/create)

A sablon a következő feladatokat hajtja végre:

* Hozzon létre egy Storage-fiókot és egy adatmegosztási adatforrásként használt tárolót.
* Hozzon létre egy adatmegosztási fiókot és egy adatmegosztást.
* Hozzon létre egy szerepkör-hozzárendelést, amely megadja a Storage blob adatolvasói szerepkört a forrás adatmegosztási erőforrásnak. Lásd: [szerepkörök és követelmények az Azure-adatmegosztáshoz](./concepts-roles-permissions.md).
* Adjon hozzá egy adatkészletet a dátum megosztásához.
* Adja hozzá a címzetteket az adatmegosztáshoz.
* Pillanatkép-ütemterv engedélyezése az adatmegosztáshoz.

Ez a sablon tanulási célokra készült. A gyakorlatban általában valamilyen adattal rendelkezik egy meglévő Storage-fiókban. Az adatkészlet létrehozásához létre kell hoznia a szerepkör-hozzárendelést egy sablon vagy parancsfájl futtatása előtt. Időnként előfordulhat, hogy a következő hibaüzenet jelenik meg a sablon telepítésekor:

```error message
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Ennek az az oka, hogy a központi telepítés megkísérli létrehozni az adatkészletet, mielőtt a RBAC-hozzárendelés véglegesítve lesz. A hibaüzenet ellenére a telepítés sikeres lehet.  Az [üzembe helyezett erőforrások áttekintése](#review-deployed-resources)továbbra is lehetséges.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez és a sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Válassza ki vagy adja meg a következő értékeket:

    * **Előfizetés**: válassza ki az adatmegosztás és a többi erőforrás létrehozásához használt Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához vagy egy meglévő erőforráscsoport kiválasztásához.
    * **Hely**: válasszon egy helyet az erőforráscsoportnak.
    * **Projekt neve**: adja meg a projekt nevét.  A projekt neve erőforrás-nevek generálására szolgál.  Tekintse meg az előző sablonban szereplő változók definícióit.
    * **hely**: válassza ki az erőforrások helyét.  Használhatja ugyanazt a helyet az erőforráscsoporthoz.
    * **Meghívó e-mail**: adja meg az adatmegosztási címzett Azure-beli bejelentkezési e-mail-címét.  Az e-mail alias nem működik.

    Használja az alapértelmezett értéket a többi beállításhoz.
1. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket**, valamint a **vásárlás**lehetőséget.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg a létrehozott adatmegosztási fiókot.
1. A bal oldali menüben válassza a **megosztások küldése**lehetőséget.  Ekkor megjelenik a felsorolt Storage-fiók.
1. Válassza ki a Storage-fiókot.  A **részletek**területen a szinkronizálási beállítást kell látnia a sablonban konfigurált módon.

    ![Azure-adatmegosztás Storage-fiókjának szinkronizálási beállításai](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Válassza a felül a **meghívókat** . Ekkor meg kell tekintenie a sablon telepítésekor megadott e-mail-címet. Az **állapot** **függőben**van.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure-beli adatmegosztást, és hogyan hívhat meg címzetteket. Ha többet szeretne megtudni arról, hogy az adatfogyasztók hogyan fogadhatnak és fogadhatnak adatmegosztást, folytassa az [elfogadás és az Adatfogadás](subscribe-to-data-share.md) című oktatóanyagot.
