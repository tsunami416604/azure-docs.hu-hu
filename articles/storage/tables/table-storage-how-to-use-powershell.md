---
title: Az Azure Table storage-műveletek végrehajtása a PowerShell használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan futtathat olyan gyakori feladatokat, mint például az Azure Table storage-fiókból származó adatok létrehozása, lekérdezése, törlése a PowerShell használatával.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.openlocfilehash: 746044aa835df52e61c234c8b5ca61164fffbbc5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545953"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Azure Table storage-műveletek végrehajtása az Azure PowerShell használatával 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Az Azure Table storage egy NoSQL-adattár, amely segítségével tárolhatja és lekérdezheti a strukturált, nem relációs adatok hatalmas készleteit. A szolgáltatás fő összetevői a táblák, entitások és tulajdonságok. A tábla entitások gyűjteménye. Az entitás tulajdonságok halmaza. Minden entitás legfeljebb 252 tulajdonsággal rendelkezhet, amelyek mind név-érték párok. Ez a cikk feltételezi, hogy már ismeri az Azure Table Storage Service fogalmait. További információt [a Table Service Data Model ismertetése](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) és az Azure Table storage [használatának első lépései a .NET használatával című témakörben talál.](../../cosmos-db/table-storage-how-to-use-dotnet.md)

Ez az útmutató cikk a gyakori Azure Table storage-műveleteket ismerteti. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Tábla beolvasása
> * Táblaentitások hozzáadása
> * Tábla lekérdezése
> * Táblaentitások törlése
> * Tábla törlése

Ez az útmutató cikk bemutatja, hogyan hozhat létre egy új Azure Storage-fiókot egy új erőforráscsoportban, így könnyedén eltávolíthatja, ha elkészült. Ha inkább egy meglévő storage-fiókot szeretne használni, ezt teheti helyette.

A példák az Az `Az.Storage (1.1.0 or greater)` `Az.Resources (1.2.0 or greater)`PowerShell-modulokat és a . Egy PowerShell-ablakban `Get-Module -ListAvailable Az*` futtassa a verzió megkereséséhez. Ha semmi sem jelenik meg, vagy frissítenie kell, olvassa el [az Azure PowerShell-modul telepítése című témakört.](/powershell/azure/install-az-ps)

> [!IMPORTANT]
> A PowerShell azure-funkciójának használatához `Az` telepítve kell lennie a modulnak. A jelenlegi `AzTable` verziója nem kompatibilis a régebbi AzureRM-modullal.
> Szükség esetén kövesse az [Az modul telepítésére vonatkozó legújabb telepítési utasításokat.](/powershell/azure/install-az-ps)

Az Azure PowerShell telepítése vagy frissítése után telepítenie kell az **AzTable**modult, amely rendelkezik az entitások kezeléséhez szükséges parancsokkal. A modul telepítéséhez futtassa a PowerShellt rendszergazdaként, és használja a **Telepítés modul** parancsot.

> [!IMPORTANT]
> A modulnév kompatibilitási okokból még mindig közzéteszi `AzureRmStorageTables` ugyanazt a modult a régi neve a PowerShell-galériában. Ez a dokumentum csak az új névre hivatkozik.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Add-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Helyek listájának lekérése

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ezek a példák **az eastus**. Tárolja ezt az értéket a változó **helyén** későbbi használatra.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport nevét egy változóban tárolhatja későbbi használatra. Ebben a példában egy *pshtablesrg* nevű erőforráscsoport jön létre az *eastus* régióban.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Hozzon létre egy általános célú szabványos tárfiókot helyileg redundáns tárral (LRS) a [New-AzStorageAccount használatával.](/powershell/module/az.storage/New-azStorageAccount) Ügyeljen arra, hogy adjon meg egy egyedi tárfiók nevét. Ezután a tárfiókot jelölő környezetbe. Amikor egy tárfiókon jár el, hivatkozhat a környezetre, ahelyett, hogy ismételten megadná a hitelesítő adatait.

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

Táblázat létrehozásához használja a [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) parancsmagát. Ebben a példában a `pshtesttable`tábla neve .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>A tárfióktábláinak listájának beolvasása

A tárfiók tábláinak listájának beolvasása a [Get-AzStorageTable segítségével.](/powershell/module/azure.storage/Get-AzureStorageTable)

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Adott táblára mutató hivatkozás beolvasása

Ahhoz, hogy műveleteket hajtson végre egy táblán, hivatkoznia kell az adott táblára. Hivatkozás beszereznie a [Get-AzStorageTable segítségével.](/powershell/module/azure.storage/Get-AzureStorageTable)

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Egy adott tábla Referencia CloudTable tulajdonsága

> [!IMPORTANT]
> A CloudTable használata kötelező az **AzTable** PowerShell modul használata során. Hívja meg a **Get-AzStorageTable** parancsot az objektumra mutató hivatkozás lehívásához. Ez a parancs akkor is létrehozza a táblát, ha még nem létezik.

Ha az **AzTable**segítségével műveleteket szeretne végrehajtani egy táblában, egy adott tábla CloudTable tulajdonságára kell hivatkoznia.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Tábla törlése

Táblázat törléséhez használja az [Remove-AzStorageTable táblát.](/powershell/module/az.storage/Remove-AzStorageTable) Ez a parancsmag eltávolítja a táblát, beleértve az összes adatot is.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az útmutató elején létrehozott egy új erőforráscsoportot és tárfiókot, az erőforráscsoport eltávolításával eltávolíthatja az ebben a gyakorlatban létrehozott összes eszközt. Ez a parancs törli a csoporton belüli összes erőforrást, valamint magát az erőforráscsoportot is.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az útmutatócikkben a PowerShell használatával végzett gyakori Azure Table storage-műveletekről szerzett tudomást, többek között a következőkről: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Tábla beolvasása
> * Táblaentitások hozzáadása
> * Tábla lekérdezése
> * Táblaentitások törlése
> * Tábla törlése

További információt az alábbi cikkekben talál.

* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage#storage)

* [Azure-táblák használata a PowerShellből – AzureRmStorageTable/AzTable PS modul 2.0-s](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
