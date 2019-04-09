---
title: Az Azure Table storage műveleteket a PowerShell-lel |} A Microsoft Docs
description: A PowerShell-lel az Azure Table storage műveletet hajt végre.
services: cosmos-db
author: roygara
ms.service: cosmos-db
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: cosmosdb-table
ms.openlocfilehash: 840c2793928816c6346e2039a38678585f8e0bc7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273124"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Az Azure Table storage műveleteket az Azure PowerShell használatával 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Az Azure Table storage a NoSQL-adattár, amely segítségével tárolhatja, és lekérdezheti a strukturált, nem relációs adatok hatalmas készleteinek. A szolgáltatás fő összetevői táblákat, az entitások és tulajdonságait. Egy tábla az entitások gyűjteményét. Egy entitás, tulajdonságait. Minden entitás legfeljebb 252 tulajdonságot tartalmazhat, amelyek minden név-érték párok rendelkezhet. Ez a cikk azt feltételezi, hogy már ismeri az Azure Table Storage szolgáltatás fogalmakat. Részletes információkért lásd: [a Table szolgáltatás adatmodelljének ismertetése](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) és [.NET használatával az Azure Table storage használatának első lépései](../../cosmos-db/table-storage-how-to-use-dotnet.md).

A cikkben található útmutató ismerteti a gyakori Azure Table storage-műveletek. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Egy tábla lekéréséhez
> * Tábla entitások hozzáadása
> * Tábla lekérdezése
> * Tábla entitások törlése
> * Tábla törlése

A cikkben található útmutató bemutatja, hogyan hozhat létre egy új Azure Storage-fiók az egy új erőforráscsoportot, így könnyen eltávolíthatja, ha elkészült. Ha szeretné inkább egy meglévő tárfiókot, is megteheti, helyette.

A példák megkövetelése Az PowerShell-modulok `Az.Storage (1.1.0 or greater)` és `Az.Resources (1.2.0 or greater)`. Egy PowerShell-ablakot a Futtatás `Get-Module -ListAvailable Az*` a verzió megkereséséhez. Ha semmi nem jelenik meg, vagy szeretné frissíteni, lásd: [Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Ezzel a funkcióval az Azure PowerShell megköveteli, hogy Ön a `Az` modul telepítve van. Jelenlegi verziója `AzTable` , nem kompatibilis a régebbi AzureRM-modul.
> Kövesse a [legújabb telepítési Az modul telepítésére vonatkozó utasításokat](/powershell/azure/install-az-ps) szükség esetén.

Miután az Azure PowerShell telepítése vagy frissítése, telepítenie kell a modult **AzTable**, amely rendelkezik, és az entitáskezelésről parancsokat. Ez a modul telepítéséhez futtassa a Powershellt rendszergazdaként, és használja a **Install-Module** parancsot.

> [!IMPORTANT]
> Modul neve kompatibilitási okokból továbbra is közzétesszük a ugyanazon modul a régi néven `AzureRmStorageTables` a PowerShell-galériában. Ez a dokumentum az új név csak hivatkozik.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Add-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Helyek listájának lekérése

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ezek a példák a **eastus**. Ez az érték Store a változóban **hely** későbbi használatra.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport nevének Store későbbi használat céljából egy változóban. Ebben a példában egy erőforráscsoportot nevű *pshtablesrg* jön létre a *eastus* régióban.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Helyileg redundáns tárolás (LRS) használó általános célú standard szintű storage-fiók létrehozása [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Mindenképpen adja meg a tárfiók egyedi neve. Ezután kérdezze le a környezetet, amely a tárfiók jelöli. A storage-fiók eljárva hivatkozhat többször adja meg a hitelesítő adatokat a környezetre.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Új tábla létrehozása

Hozzon létre egy táblát, használja a [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) parancsmagot. Ebben a példában a táblázat neve `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>A tárfiókban lévő táblák listájának lekéréséhez

A tárfiók tárfiókkulcsait lévő táblák listájának lekéréséhez [Get-AzStorageTable](/powershell/module/az.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Egy adott tábla egy hivatkozást lekérni

Tábla műveletek végrehajtásához, szüksége van egy, az adott tábla. Egy hivatkozást az első [Get-AzStorageTable](/powershell/module/az.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Egy adott tábla hivatkozási CloudTable tulajdonság

> [!IMPORTANT]
> CloudTable használata nem kötelező, ha **AzTable** PowerShell-modult. Hívja a **Get-AzTableTable** parancs használatával beszerezheti az ezen objektum hivatkozását. Ezzel a paranccsal emellett a táblát hoz létre, ha azt nem létezik.

Egy táblát a műveletek végrehajtásához **AzTable**, egy hivatkozás egy adott tábla CloudTable tulajdonság van szüksége.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Tábla törlése

Egy tábla törléséhez használja [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Ez a parancsmag eltávolítja a tábla, beleértve az összes adatot.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha egy új erőforrás és a tárfiókja-fiókot, ez az útmutató elején, eltávolíthatja az összes létrehozott ebben a gyakorlatban az erőforráscsoport eltávolításával az eszközök. Ez a parancs törli a csoportot, valamint a magát az erőforráscsoportot lévő összes erőforrást.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Az útmutató a cikkben megismerkedett a gyakori Azure Table storage műveleteket a PowerShell-lel, beleértve a: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Egy tábla lekéréséhez
> * Tábla entitások hozzáadása
> * Tábla lekérdezése
> * Tábla entitások törlése
> * Tábla törlése

További információkért tekintse meg a következő cikkek

* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage#storage)

* [Azure PowerShell - modul v2.0 AzureRmStorageTable/AzTable PS-táblák használata](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
