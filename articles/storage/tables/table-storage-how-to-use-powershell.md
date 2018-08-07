---
title: Az Azure Table storage műveleteket a PowerShell-lel |} A Microsoft Docs
description: A PowerShell-lel az Azure Table storage műveletet hajt végre.
services: cosmos-db
author: robinsh
ms.service: cosmos-db
ms.topic: article
ms.date: 03/14/2018
ms.author: robinsh
ms.component: tables
ms.openlocfilehash: 21023dd8adcf5ba623ac1bac3c2dbea0dfe04c36
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524039"
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

Példák Azure PowerShell modul verziója 4.4.0 szükséges vagy újabb. Egy PowerShell-ablakot a Futtatás `Get-Module -ListAvailable AzureRM` a verzió megkereséséhez. Ha semmi nem jelenik meg, vagy szeretné frissíteni, lásd: [Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps). 

Miután az Azure PowerShell telepítése vagy frissítése, telepítenie kell a modult **AzureRmStorageTable**, amely rendelkezik, és az entitáskezelésről parancsokat. Ez a modul telepítéséhez futtassa a Powershellt rendszergazdaként, és használja a **Install-Module** parancsot.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Helyek listájának lekérése

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ezek a példák a **eastus**. Ez az érték Store a változóban **hely** későbbi használatra.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport nevének Store későbbi használat céljából egy változóban. Ebben a példában egy erőforráscsoportot nevű *pshtablesrg* jön létre a *eastus* régióban.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Helyileg redundáns tárolás (LRS) használó általános célú standard szintű storage-fiók létrehozása [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). A tárfiók környezetét, amely meghatározza a használandó tárfiókot beolvasása. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Új tábla létrehozása

Hozzon létre egy táblát, használja a [New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable) parancsmagot. Ebben a példában a táblázat neve `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>A tárfiókban lévő táblák listájának lekéréséhez

A tárfiók tárfiókkulcsait lévő táblák listájának lekéréséhez [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Egy adott tábla egy hivatkozást lekérni

Tábla műveletek végrehajtásához, szüksége van egy, az adott tábla. Egy hivatkozást az első [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Tábla törlése

Egy tábla törléséhez használja [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Ez a parancsmag eltávolítja a tábla, beleértve az összes adatot.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha egy új erőforrás és a tárfiókja-fiókot, ez az útmutató elején, eltávolíthatja az összes létrehozott ebben a gyakorlatban az erőforráscsoport eltávolításával az eszközök. Ez a parancs törli a csoportot, valamint a magát az erőforráscsoportot lévő összes erőforrást.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
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

* [Tárolási PowerShell-parancsmagok](/powershell/module/azurerm.storage#storage)

* [PowerShell az Azure Storage-táblák használata](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
