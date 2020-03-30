---
title: Az Azure PowerShell használata az Azure Storage szolgáltatással | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure PowerShell-parancsmagokat az Azure Storage-hoz.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d2404ee58f5f44fbe5625f267e6d1c504d0bd237
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465090"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)

Az Azure PowerShell segítségével azure-erőforrásokat hozhat létre és kezelhet a PowerShell parancssorból vagy parancsfájlokban. Az Azure Storage esetében ezek a parancsmagok két kategóriába sorolhatók: a vezérlősíkés az adatsík. A vezérlősík-parancsmagok a tárfiók kezelésére szolgálnak - tárfiókok létrehozásához, tulajdonságok beállításához, tárfiókok törléséhez, a hozzáférési kulcsok elforgatásához és így tovább. Az adatsík-parancsmagok a *tárfiókban* tárolt adatok kezelésére szolgálnak. Például blobok feltöltése, fájlmegosztások létrehozása és üzenetek hozzáadása egy várólistához.

Ez az útmutató cikk a felügyeleti sík parancsmagokkal a tárfiókok kezeléséhez használt gyakori műveleteket ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Tárfiókok listázása
> * Hivatkozás egy meglévő tárfiókra
> * Create a storage account
> * Tárfiók tulajdonságainak beállítása
> * A hozzáférési kulcsok beolvasása és újragenerálása
> * A tárfiókhoz való hozzáférés védelme
> * Storage Analytics engedélyezése

Ez a cikk számos más PowerShell-cikkre mutató hivatkozást tartalmaz a Storage Analytics engedélyezéséhez és eléréséhez, az adatsík-parancsmagok használatához, valamint az Azure független felhőinek eléréséhez, például a China Cloud, a German Cloud és a Government Felhő.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ehhez a gyakorlathoz az Azure PowerShell-modul, az Az 0.7-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

Ebben a gyakorlatban beírhatja a parancsokat egy normál PowerShell-ablakba, vagy használhatja a [Windows PowerShell integrált parancsfájl-alapú környezetet (ISE),](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) és írja be a parancsokat egy szerkesztőbe, majd egyszerre egy vagy több parancsot teszteljen a példák végighaladása közben. Kiemelheti a végrehajtani kívánt sorokat, és a Parancsok futtatásához kattintson a Kijelölt futtatás gombra.

A tárfiókokról további információt a [Bevezetés a tárolásba](storage-introduction.md) és [az Azure storage-fiókok – bemutatkozás](storage-create-storage-account.md)című témakörben talál.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>A tárfiókok listázása az előfizetésben

Futtassa a [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) parancsmag beolvasásához a tárfiókok listáját az aktuális előfizetésben.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Hivatkozás egy tárfiókra

Ezután egy tárfiókra mutató hivatkozásra van szüksége. Létrehozhat egy új tárfiókot, vagy kaphat egy hivatkozást egy meglévő tárfiókra. A következő szakasz mindkét módszert bemutatja.

### <a name="use-an-existing-storage-account"></a>Meglévő tárfiók használata

Meglévő tárfiók lekéréséhez szüksége van az erőforráscsoport nevére és a tárfiók nevére. Állítsa be a két mező változóit, majd használja a [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) parancsmagát.

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Most már $storageAccount, amely egy meglévő tárfiókra mutat.

### <a name="create-a-storage-account"></a>Create a storage account

A következő parancsfájl bemutatja, hogyan hozhat létre általános célú tárfiókot a [New-AzStorageAccount használatával.](/powershell/module/az.storage/New-azStorageAccount) A fiók létrehozása után olvassa be a környezetét, amely a későbbi parancsokban használható ahelyett, hogy minden egyes hívásnál megnevezné a hitelesítést.

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location

# Set the name of the storage account and the SKU name.
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context.
$ctx = $storageAccount.Context
```

A parancsfájl a következő PowerShell-parancsmagokat használja:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) - lekéri az érvényes helyek listáját. A példa `eastus` a helyhez használatos.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) - új erőforráscsoportot hoz létre. Az erőforráscsoport egy logikai tároló, amelybe az Azure-erőforrások üzembe helyezése és kezelése. A miénk `teststoragerg`a neve.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) - létrehozza a tárfiókot. A példa `testpshstorage`a .

A termékváltozat neve a tárfiók replikációjának típusát jelzi, például az LRS (helyileg redundáns tárolás). A replikációról további információt az [Azure Storage Replikáció című témakörben talál.](storage-redundancy.md)

> [!IMPORTANT]
> A tárfiók nevének egyedinek kell lennie az Azure-on belül, és kisbetűsnek kell lennie. Elnevezési konvenciók és korlátozások, [lásd: elnevezési és hivatkozási tárolók, blobok és metaadatok.](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
>

Most már van egy új tárfiókot, és egy hivatkozást rá.

## <a name="manage-the-storage-account"></a>A tárfiók kezelése

Most, hogy egy új tárfiókra vagy egy meglévő tárfiókra mutató hivatkozást tartalmaz, a következő szakaszban látható néhány parancs, amelyet a tárfiók kezeléséhez használhat.

### <a name="storage-account-properties"></a>Tárfiók tulajdonságai

A tárfiók beállításainak módosításához használja a [Set-AzStorageAccount eszközt.](/powershell/module/az.storage/set-azstorageaccount) Bár nem módosíthatja a tárolási fiók helyét, vagy az erőforráscsoport, amelyben található, számos más tulajdonság módosítható. Az alábbi lista néhány, a PowerShell használatával módosítható tulajdonságok at sorolfel.

* A tárfiókhoz rendelt **egyéni tartomány.**

* A tárfiókhoz rendelt **címkék.** A címkéket gyakran használják az erőforrások számlázási célokra történő kategorizálására.

* A **termékváltozat** a tárfiók replikációs beállítása, például lrs helyileg redundáns tároláshoz. Például átválthat Standard\_LRS-ről\_Standard GRS vagy Standard\_RAGRS-re. Ne feledje, hogy\_a Standard\_ZRS, standard\_GZRS,\_Standard RAGZRS vagy Prémium LRS nem módosítható más suk-ra, és nem módosíthatja ezekre a standard ZRS-eket, standard GZRS-eket, standard RAGZRS-eket vagy prémium LRS-eket.

* A Blob storage-fiókok **hozzáférési szintje.** A hozzáférési szint értéke **gyakori** vagy **ritka elérésű,** és lehetővé teszi a költségek minimalizálását a tárfiók használatának megfelelő hozzáférési szint kiválasztásával. További információ: [Gyakori, ritka elérésű és archiválási tárolási szintek.](../blobs/storage-blob-storage-tiers.md)

* Csak HTTPS-forgalmat engedélyezze.

### <a name="manage-the-access-keys"></a>A hozzáférési kulcsok kezelése

Az Azure Storage-fiók két fiókkulcsot hoz. A kulcsok beolvasásához használja a [Get-AzStorageAccountKey programot.](/powershell/module/az.Storage/Get-azStorageAccountKey) Ez a példa az első kulcsot olvassa be. A másik beolvasásához `Value[1]` használja `Value[0]`a helyett a lehetőséget.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

A kulcs újragenerálásához használja a [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey)eszközt.

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

A másik kulcs újragenerálásához használja `key2` a `key1`kulcsnévként a helyett.

Hozza létre újra az egyik kulcsot, majd olvassa be újra az új érték megtekintéséhez.

> [!NOTE]
> Gondos tervezést kell végrehajtania, mielőtt újragenerálja a kulcsot egy éles tárfiókhoz. Az egyik vagy mindkét kulcs újragenerálása érvényteleníti a hozzáférést minden olyan alkalmazáshoz, amely a kulcs újragenerálása. További információt a [Tárfiók hozzáférési kulcsainak kezelése című témakörben talál.](storage-account-keys-manage.md)


### <a name="delete-a-storage-account"></a>Tárfiók törlése

Tárfiók törléséhez használja az [Eltávolítás-AzStorageAccount eszközt.](/powershell/module/az.storage/Remove-azStorageAccount)

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Amikor töröl egy tárfiókot, a fiókban tárolt összes eszköz is törlődik. Ha véletlenül töröl egy fiókot, azonnal hívja fel a támogatási felhívást, és nyisson meg egy jegyet a tárfiók visszaállításához. Az adatok helyreállítása nem garantált, de néha működik. Ne hozzon létre új tárfiókot a réginel megegyező névvel, amíg a támogatási jegy fel nem oldódott.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>A tárfiók védelme virtuális hálózatok és tűzfalak használatával

Alapértelmezés szerint minden tárfiók elérhető bármely olyan hálózaton, amely hozzáfér az internethez. A hálózati szabályok azonban konfigurálhatók úgy, hogy csak adott virtuális hálózatokból származó alkalmazások férhessenek hozzá egy tárfiókhoz. További információt az [Azure Storage tűzfalak és virtuális hálózatok konfigurálása című témakörben talál.](storage-network-security.md)

A cikk bemutatja, hogyan kezelheti ezeket a beállításokat a következő PowerShell-parancsmagokkal:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>A tárhelyelemzés használata

[Az Azure Storage Analytics](storage-analytics.md) [a Storage Analytics-metrikákból](/rest/api/storageservices/about-storage-analytics-metrics) és a [Storage Analytics naplózásából](/rest/api/storageservices/about-storage-analytics-logging)áll.

**A Storage Analytics Metrikák** az Azure storage-fiókok metrikák gyűjtésére szolgálnak, amelyek segítségével figyelheti a tárfiók állapotát. A metrikák engedélyezhetők blobokhoz, fájlokhoz, táblákhoz és várólistákhoz.

**A Storage Analytics naplózása** kiszolgálóoldali, és lehetővé teszi a sikeres és a sikertelen kérelmek adatait a tárfiókba történő rögzítéséhez. Ezek a naplók lehetővé teszik az olvasási, írási és törlési műveletek részleteit a táblákon, várólistákon és blobokon, valamint a sikertelen kérelmek okait. A naplózás nem érhető el az Azure Files.

A figyelés konfigurálható az [Azure Portalon](https://portal.azure.com), PowerShell, vagy programozott módon a storage-ügyfélkódtár használatával.

> [!NOTE]
> A PowerShell használatával engedélyezheti a percelemzéseket. Ez a funkció nem érhető el a portálon.
>

* A Storage Metrics-adatok PowerShell használatával történő engedélyezéséről és megtekintéséről a [Storage-elemzési metrikák olvashatnak.](storage-analytics-metrics.md)

* A Tárolási naplózási adatok PowerShell használatával történő engedélyezéséről és beolvasásáról a [Tárolónaplózás engedélyezése a PowerShell használatával](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) és a [Tárolónaplózásnapló adatainak megkeresése](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)című témakörből megtudhatja.

* A tárolási metrikák és a tárolási naplózás tárolási problémák elhárításához való használatáról a [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása című](storage-monitoring-diagnosing-troubleshooting.md)témakörben talál részletes információt.

## <a name="manage-the-data-in-the-storage-account"></a>A tárfiókban lévő adatok kezelése

Most, hogy már tisztában van azzal, hogyan kezelheti a tárfiókot a PowerShell, a következő cikkek segítségével megtudhatja, hogyan érheti el az adatobjektumokat a tárfiókban.

* [Blobok kezelése a PowerShell használatával](../blobs/storage-how-to-use-blobs-powershell.md)
* [Fájlok kezelése a PowerShell használatával](../files/storage-how-to-use-files-powershell.md)
* [Várólisták kezelése a PowerShell használatával](../queues/storage-powershell-how-to-use-queues.md)
* [Azure Table storage-műveletek végrehajtása a PowerShell használatával](../../storage/tables/table-storage-how-to-use-powershell.md)

Az Azure Cosmos DB Table API prémium szintű funkciókat biztosít a table storage, például a kulcsrakész globális terjesztés, alacsony késésű olvasási és írási, automatikus másodlagos indexelés és dedikált átviteli.

* További információ: [Azure Cosmos DB Table API.](../../cosmos-db/table-introduction.md)

## <a name="independent-cloud-deployments-of-azure"></a>Az Azure független felhőalapú telepítései

A legtöbb ember az Azure Public Cloud szolgáltatást használja a globális Azure-telepítéshez. A szuverenitás és így tovább a Microsoft Azure néhány független központi telepítése is létezik. Ezeket a független központi telepítéseket "környezetnek" nevezzük. Ezek a rendelkezésre álló környezetek:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [A 21Vianet által kínában üzemeltetett Azure China 21Vianet Cloud](http://www.windowsazure.cn/)
* [Azure német felhő](../../germany/germany-welcome.md)

Afelhők és a PowerShell használatával való tárolásuk eléréséről a [Storage kezelése az Azure független felhőiben a PowerShell használatával](storage-powershell-independent-clouds.md)című témakörben talál tájékoztatást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot és egy tárfiókot ehhez a gyakorlathoz, eltávolíthatja az erőforráscsoport eltávolításával létrehozott összes eszközt. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott tárfiókot és magát az erőforráscsoportot.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>További lépések

Ez az útmutató cikk a felügyeleti sík parancsmagokkal a tárfiókok kezeléséhez használt gyakori műveleteket ismerteti. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tárfiókok listázása
> * Hivatkozás egy meglévő tárfiókra
> * Create a storage account
> * Tárfiók tulajdonságainak beállítása
> * A hozzáférési kulcsok beolvasása és újragenerálása
> * A tárfiókhoz való hozzáférés védelme
> * Storage Analytics engedélyezése

Ez a cikk számos más cikkre is tartalmaz hivatkozásokat, például az adatobjektumok kezelésére, a Storage Analytics engedélyezésére, valamint az Azure független felhőinek elérésére, például a China Cloudra, a German Cloudra és a Government Cloudra. Íme néhány további kapcsolódó cikkek és források referencia:

* [Az Azure Storage PowerShell-parancsmagjai](/powershell/module/az.storage/)
* [Az Azure Storage PowerShell-parancsmagjai](/powershell/module/azure.storage/)
* [Windows PowerShell-referencia](/powershell/scripting/overview)
