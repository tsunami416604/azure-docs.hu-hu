---
title: "Azure Table storage műveleteket a PowerShell-lel |} Microsoft Docs"
description: "Azure Table storage műveleteket a PowerShell használatával"
services: cosmos-db
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: robinsh
ms.openlocfilehash: 15a4ed2370598cb98565c48b4563bee3a4445827
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Az Azure PowerShell Azure Table storage műveleteket 

>[!NOTE]
>Az Azure Cosmos DB tábla API prémium szolgáltatások biztosít a table storage például kulcsrakész globális terjesztési kis késleltetésű olvasások és írások, másodlagos indexelő vagy dedikált átviteli sebesség. A legtöbb esetben ez a cikk a munkahelyi Azure Cosmos DB tábla API, mind az Azure Table storage, de ez a cikk a PowerShell-parancsok csak az Azure Table storage. Ha Azure Cosmos DB tábla API-t használ, tekintse meg [Azure Cosmos DB tábla API műveletek az Azure PowerShell](table-powershell.md).
>

Azure Table storage a NoSQL-adattár, amely segítségével tárolja, és hatalmas strukturált, nem relációs adatok lekérdezése egy. A szolgáltatás fő összetevőit táblák, entitásokat és tulajdonságok. A tábla az entitások gyűjteményét. Egy entitás tulajdonságait. Minden entitás legfeljebb 252 tulajdonságot, amely minden név-érték pár is rendelkezik. Ez a cikk feltételezi, hogy Ön már ismeri az Azure Table Storage szolgáltatással kapcsolatos fogalmak. Részletes információkért lásd: [ismertetése a Table szolgáltatás adatmodell](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) és [Ismerkedés az Azure Table storage használatának .NET](table-storage-how-to-use-dotnet.md).

Útmutató cikkben közös Azure Table storage műveleteket ismerteti. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Egy tábla beolvasása
> * Adja hozzá a táblaentitásokat
> * Egy táblából
> * Tábla entitások törlése
> * Tábla törlése

A cikkben található útmutató bemutatja, hogyan eltávolításához, amikor elkészült, hozzon létre egy új tárfiókot egy új erőforráscsoportot. Ha inkább használhatja egy meglévő tárfiókot használ, azt teheti meg helyette.

A példák szükség Azure PowerShell modul verziója 4.4.0 vagy újabb. A PowerShell-ablakban futtassa `Get-Module -ListAvailable AzureRM` verzió található. Ha semmi nem jelenik meg, vagy szeretné frissíteni, lásd: [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps). 

Miután az Azure PowerShell telepítése vagy frissítése, telepítenie kell a modul **AzureRmStorageTable**, az entitások kezelésére szolgáló parancsokat tartalmaz. Ez a modul telepítéséhez futtassa PowerShell rendszergazdaként, használja a **Install-modul** parancsot.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Helyek listájának beolvasása

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ezekben a példákban **eastus**. Ez az érték tárolása a változó **hely** későbbi használatra.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport neve tárolható egy változóban későbbi használatra. Ebben a példában az erőforráscsoport neve *pshtablesrg* jön létre a *eastus* régióban.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Hozzon létre egy szabványos általános célú tárfiók helyileg redundáns tárolás (LRS) használatával [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). A tárfiók környezetét, amely definiálja a tárfiókot használni beolvasása. A tárfiók eljárva hivatkozik a környezet helyett ismételten adja meg a hitelesítő adatokat.

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

A tábla létrehozásához használja a [New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable) parancsmag. Az ebben a példában a táblázat neve `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>A tárfiók a táblázatok listájának beolvasása

A tárolási fiók használata a táblázatok listájának beolvasása [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Egy adott táblához hivatkozást lekérni

Egy táblázat a műveletek végrehajtásához szüksége van az adott tábla hivatkozik. A hivatkozás segítségével beolvasása [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Tábla törlése

Egy tábla törléséhez használja [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Ez a parancsmag eltávolítja a tábla, például az összes hozzá kapcsolódó adatokat.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ez az útmutató elején hozott létre egy új erőforrás csoport és a tárolási fiók, eltávolíthatja az összes létrehozott ebben a gyakorlatban az erőforráscsoport eltávolításával eszközök. A csoport, valamint az erőforráscsoport maga belül található összes erőforrást törli.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Útmutató cikkben megtanulta, Azure Table storage közös műveleteire vonatkozó a PowerShell-lel, beleértve a hogyan: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Egy tábla beolvasása
> * Adja hozzá a táblaentitásokat
> * Egy táblából
> * Tábla entitások törlése
> * Tábla törlése

További információkért tekintse meg a következő cikkek

* [Tárolási PowerShell-parancsmagok](/powershell/module/azurerm.storage#storage)

* [Az Azure Storage-táblázatok a PowerShell használata](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.