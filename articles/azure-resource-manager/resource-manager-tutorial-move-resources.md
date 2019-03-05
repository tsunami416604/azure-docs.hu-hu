---
title: Azure-erőforrások áthelyezése másik erőforráscsoportba |} A Microsoft Docs
description: Ismerje meg az Azure-erőforrások áthelyezése egy erőforráscsoport, egy másik erőforráscsoportba vagy egy előfizetés másik előfizetésre.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 62e35a361f2fcf9d6f484b699084a78f66121957
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310097"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group"></a>Oktatóanyag: Azure-erőforrások áthelyezése másik erőforráscsoportba

Ismerje meg az Azure-erőforrások áthelyezése egyik erőforráscsoportból egy másik erőforráscsoportba. Egy másik Azure-előfizetéshez egy Azure-előfizetésből is áthelyezheti az Azure-erőforrásokat. Ebben az oktatóanyagban a resource manager-sablon üzembe helyezéséhez a két erőforráscsoport és a egy storage-fiók használhatja. Ezután helyez át a storage-fiók az egyik erőforráscsoportból a másik.

![Az Azure Resource Manager áthelyezés erőforrások diagramja](./media/resource-manager-tutorial-move-resources/resource-manager-template-move-resources.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Készítse elő az erőforrásokat.
> * Ellenőrizze, hogy az erőforrás helyezhető.
> * Ellenőrzőlista az erőforrás áthelyezése előtt.
> * Az áthelyezési művelet ellenőrzéséhez.
> * Helyezze át az erőforrást.
> * Erőforrások törlése.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prepare-the-resources"></a>A-erőforrások előkészítése

A sablon létre és helyezett egy [tárfiók megosztott](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json). A sablon meghatározza a két erőforráscsoport és a egy tárfiókot. A sablon telepítésekor meg kell adnia a projekt nevét. A projekt neve egyedi erőforrásnevek létrehozására szolgál.  A következő JSON kivonjuk a sablonból:

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

Figyelje meg, hogy a helyek, a json-fájlban definiált, a két erőforrás csoport az USA keleti RÉGIÓJA és USA nyugati Régiójában található. A storage-fiókot az USA keleti régiójában található. Ha egy erőforrást helyez át egy másik erőforráscsoportot, a másik helyet, az áthelyezési művelet az erőforrás helye nem változik.

Válassza ki **kipróbálás** a Cloud shellt, és a PowerShell-parancsfájlt a Cloud shellben belül végre:

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

Várjon, amíg a parancsfájl futása sikeresen befejeződött, majd nyissa meg a [az Azure portal](https://portal.azure.com), és ellenőrizze az erőforráscsoportok és a storage-fiók elvárt módon vannak telepítve.

> [!NOTE]
> A sablon meghatározza a két erőforráscsoport, mert a központi telepítés az előfizetés-szintű telepítés minősül. A portál sablon telepítése előfizetési szintű üzemelő példányok nem támogatja. Így az Azure PowerShell a jelen oktatóanyagban használt. Az Azure CLI az előfizetés-szintű telepítések is támogatja. Lásd: [erőforráscsoportokat és erőforrásokat az Azure-előfizetés létrehozása](./deploy-to-subscription.md).

## <a name="verify-the-resource-can-be-moved"></a>Ellenőrizze az erőforrás helyezhető

Az erőforrások nem helyezhetők. Az ebben az oktatóanyagban használt resource egy storage-fiókot, amely lehessen áthelyezni. Annak ellenőrzéséhez, hogy áthelyezhető-e egy erőforrást, tekintse meg [áthelyezhető szolgáltatások](./resource-group-move-resources.md#services-that-can-be-moved).

## <a name="checklist-before-moving-resources"></a>Erőforrások áthelyezése előtti ellenőrzőlistát

Ez a lépés nem kötelező használni, ebben az oktatóanyagban, mivel nem történt.

Nincsenek erőforrások áthelyezése előtt néhány fontos lépést. Lásd: [erőforrások áthelyezése előtti ellenőrzőlistát](./resource-group-move-resources.md#checklist-before-moving-resources).

## <a name="validate-the-move"></a>Az áthelyezés érvényesítése

Az áthelyezés érvényesítése nem kötelező ebben az oktatóanyagban, mivel nem történt.

Az érvényesítés áthelyezési művelet lehetővé teszi az áthelyezési forgatókönyv teszteléséhez ténylegesen az erőforrások áthelyezése nélkül. Ez a művelet segítségével határozhatja meg, ha az áthelyezés sikeres lesz. További információkért lásd: [az áthelyezés érvényesítése](./resource-group-move-resources.md#validate-move).

## <a name="move-the-resource"></a>Helyezze át az erőforrást

A tárfiók a forrásként szolgáló erőforráscsoportot (rg1) belül, futtassa a következő PowerShell parancsfájlt helyezze át az erőforrást a cél erőforráscsoport (rg2). Ellenőrizze, hogy ugyanazt a projekt nevet használja, akkor használhatja, amikor az erőforrásokat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

Nyissa meg a [az Azure portal](https://portal.azure.com), ellenőrizze a tárfiók került, az erőforráscsoport és is ellenőrizheti a tárfiók helye még mindig az USA keleti RÉGIÓJA.

Erőforrások áthelyezésekor mind a forrás és a cél csoport zárolva vannak a művelet során. Írási és törlési műveletek az áthelyezés befejezéséig az erőforráscsoportok elakad. A zárolás azt jelenti, hogy a nem hozzáadása, frissítése vagy törlése az erőforráscsoportok erőforrásaihoz, de ez nem jelenti azt, az erőforrások szüneteltetve legyenek. Ha például egy SQL Server és az adatbázis áthelyezése egy új erőforráscsoportot, ha nem az adatbázist használó alkalmazások teljesen állásidő nélkül. Továbbra is olvasni és írni az adatbázisba.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki a kiindulási erőforráscsoport nevével.  
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.
5. Válassza ki a cél erőforráscsoport-név.  
6. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan egy erőforráscsoportot egy storage-fiók áthelyezése másik erőforráscsoportba. Az eddigi, rendelkezik lett foglalkoznak, egy storage-fiók vagy a storage-fiók több példányt. A következő oktatóanyagban egy több erőforrást és több erőforrástípust tartalmazó sablont fog fejleszteni. Egyes erőforrások függő erőforrásokkal rendelkeznek.

> [!div class="nextstepaction"]
> [Függő erőforrások létrehozása](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
