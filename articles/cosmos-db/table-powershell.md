---
title: "Azure Cosmos DB tábla API-műveleteket a PowerShell-lel |} Microsoft Docs"
description: "A PowerShell-lel Azure Cosmos DB tábla API műveletek végrehajtása"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d0f835db8a9fbe3833a9c7931ad1d8b4a778f016
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Az Azure PowerShell Azure Cosmos DB tábla API műveletek végrehajtása 

>[!NOTE]
>Az Azure Cosmos DB tábla API prémium szolgáltatások biztosít a table storage például kulcsrakész globális terjesztési kis késleltetésű olvasások és írások, másodlagos indexelő vagy dedikált átviteli sebesség. A legtöbb esetben ez a cikk a munkahelyi Azure Cosmos DB tábla API, mind az Azure Table storage, de ez a cikk a PowerShell-parancsok csak az Azure Cosmos DB tábla API. Ha az Azure Table storage használ, tekintse meg [hajtsa végre az Azure Table storage műveletek az Azure PowerShell](table-storage-how-to-use-powershell.md).
>

Az Azure Cosmos DB tábla API lehetővé teszi tárolja, és hatalmas strukturált, nem relációs adatokat lekérdezni. A szolgáltatás fő összetevőit táblák, entitásokat és tulajdonságok. A tábla az entitások gyűjteményét. Egy entitás tulajdonságait. Minden entitás legfeljebb 252 tulajdonságot, amely minden név-érték pár is rendelkezik. Ez a cikk feltételezi, hogy Ön már ismeri az Azure Cosmos DB tábla API fogalmakat. Részletes információkért lásd: [Bevezetés az Azure Cosmos DB tábla API](table-introduction.md) és [tábla API használatával a .NET-alkalmazás létrehozása](create-table-dotnet.md).

A cikkben található útmutató közös tábla API-műveleteket ismerteti. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Egy tábla beolvasása
> * Adja hozzá a táblaentitásokat
> * Egy táblából
> * Tábla entitások törlése

## <a name="prerequisites"></a>Előfeltételek

A példák szükség Azure PowerShell modul verziója 4.4.0 vagy újabb. A PowerShell-ablakban futtassa `Get-Module -ListAvailable AzureRM` verzió található. Ha semmi nem jelenik meg, vagy szeretné frissíteni, lásd: [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps). 

Miután az Azure PowerShell telepítése vagy frissítése, telepítenie kell a modul **AzureRmStorageTable**, az entitások kezelésére szolgáló parancsokat tartalmaz. Ez a modul telepítéséhez futtassa PowerShell rendszergazdaként, használja a **Install-modul** parancsot.

```powershell
Install-Module AzureRmStorageTable
```

Telepítse az Azure Cosmos DB szerelvények helyi PowerShell-parancsmagok használatához. Ehhez útmutatást lásd: [Cosmos DB táblák Azure RM tárolási táblák PowerShell-modul](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/).

A következő gyakorlatokban kipróbálására, egy Azure Cosmos-adatbázis adatbázis-fiók szükséges. Ha még nem rendelkezik egy, hozzon létre egy új Azure Cosmos DB fiók használatával a [Azure-portálon](https://portal.azure.com). Egy új adatbázis-fiók létrehozásához lásd [Azure Cosmos DB: adatbázis-fiók létrehozása](create-table-dotnet.md#create-a-database-account).

Az adatbázis-fiók nevét és az erőforrás csoportot beszerzése a portálról; Ezekkel az értékekkel a parancsfájlt a táblák eléréséhez be van szüksége. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Hozzon létre egy táblát, vagy hivatkozzon egy tábla

Hozzon létre egy táblát, vagy egy hivatkozás a beolvasandó, **Get-AzureStorageTableTable**. Ha felhívja a parancsmagot egy nem létező tábla nevével, új táblázat létrehozása ezzel a névvel, és visszaad egy hivatkozást az új táblázat. Ha a tábla létezik, a meglévő táblázat egy referenciát ad eredményül.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

A PowerShell használata Azure Cosmos DB fiók lévő táblák nem szerepelhet, de jelentkezzen be a portálra, és tekintse meg a táblázatot. Most már a tábla az entitások kezelése vizsgáljuk meg.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Tábla törlése 

PowerShell Azure Cosmos DB törlése táblák nem támogatja. Tábla törlése, keresse fel a [Azure-portálon](https://azure.portal.com), keresse meg a Azure Cosmos DB-fiókot használ, található, és törölni kívánja a táblázatot. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot, és létrehozott egy új Azure Cosmos DB-fiókot az adott csoport, eltávolíthatja összes eszköz hozott létre ebben a gyakorlatban az erőforráscsoport eltávolításával. A csoport, valamint az erőforráscsoport maga belül található összes erőforrást törli.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Útmutató cikkben megismerte közös tábla API-műveleteket a PowerShell-lel, beleértve a hogyan: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Egy tábla beolvasása
> * Adja hozzá a táblaentitásokat
> * Egy táblából
> * Tábla entitások törlése

További információkért tekintse át a következő cikkeket:

* [Az Azure Storage-táblázatok a PowerShell használata](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.