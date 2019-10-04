---
title: Azure PowerShell használata az Azure Storage szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Storage-hoz készült Azure PowerShell-parancsmagokat.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ac210a03f8b1a0a5f7fff07cbc68b4cd6bc98632
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016349"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)

Azure PowerShell az Azure-erőforrások PowerShell-parancssorból vagy parancsfájlokból való létrehozására és kezelésére szolgál. Az Azure Storage esetében ezek a parancsmagok két kategóriába sorolhatók – a vezérlési síkon és az adatsíkon. A vezérlő síkja parancsmagok a Storage-fiók kezelésére szolgálnak – a Storage-fiókok létrehozásához, a tulajdonságok beállításához, a tárolási fiókok törléséhez, a hozzáférési kulcsok elforgatásához stb. Az adatsík-parancsmagok segítségével kezelheti a Storage *-* fiókban tárolt adattárakat. Például Blobok feltöltése, fájlmegosztás létrehozása és üzenetek hozzáadása egy várólistához.

Ez a cikk a felügyeleti sík parancsmagokat használó általános műveleteket ismerteti a Storage-fiókok kezeléséhez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Storage-fiókok listázása
> * Meglévő Storage-fiókra mutató hivatkozás beszerzése
> * Tárfiók létrehozása
> * A Storage-fiók tulajdonságainak beállítása
> * Hozzáférési kulcsok beolvasása és újragenerálása
> * A Storage-fiókhoz való hozzáférés biztosítása
> * Storage Analytics engedélyezése

Ez a cikk számos más PowerShell-cikkre mutató hivatkozásokat tartalmaz, például hogyan lehet engedélyezni és elérni a Storage Analytics, hogyan használhatja az adatsík-parancsmagokat, és hogyan érheti el az Azure független felhőit, például a kínai felhőt, a német felhőt és a kormányzatot Felhő.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ehhez a gyakorlathoz az Azure PowerShell modul az 0,7-es vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

Ebben a gyakorlatban beírhatja a parancsokat egy normál PowerShell-ablakba, vagy használhatja a [Windows PowerShell integrált parancsfájlkezelési környezetét (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) , és beírhatja a parancsokat egy Szerkesztőbe, majd egy vagy több parancsot is kipróbálhat, amikor áthalad a Példák. Kiemelheti a végrehajtani kívánt sorokat, és a kijelölt futtatása gombra kattintva egyszerűen futtathatja ezeket a parancsokat.

A Storage-fiókokkal kapcsolatos további információkért lásd: [Bevezetés a Storage](storage-introduction.md) -ba és [Az Azure Storage-fiókok](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Az előfizetéshez tartozó Storage-fiókok listázása

Futtassa a [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) parancsmagot a jelenlegi előfizetésben lévő Storage-fiókok listájának lekéréséhez.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>A Storage-fiókra mutató hivatkozás beszerzése

Következő lépésként egy Storage-fiókra kell hivatkoznia. Létrehozhat egy új Storage-fiókot, vagy egy meglévő Storage-fiókra mutató hivatkozást is beszerezhet. A következő szakasz mindkét módszert mutatja be.

### <a name="use-an-existing-storage-account"></a>Meglévő Storage-fiók használata

Meglévő Storage-fiók beolvasásához szüksége lesz az erőforráscsoport nevére és a Storage-fiók nevére. Állítsa be a két mező változóit, majd használja a [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) parancsmagot.

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Most már rendelkezik $storageAccount, amely egy meglévő Storage-fiókra mutat.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az alábbi szkript bemutatja, hogyan hozhat létre egy általános célú Storage-fiókot a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)használatával. Miután létrehozta a fiókot, lekérheti a környezetét, amely a következő parancsokban is használható, nem pedig az egyes hívásokhoz való hitelesítés megadásával.

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

A szkript a következő PowerShell-parancsmagokat használja:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) – az érvényes helyszínek listáját kéri le. A példa a `eastus` helyet használja.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) – új erőforráscsoport létrehozása. Az erőforráscsoport olyan logikai tároló, amelybe az Azure-erőforrások üzembe helyezése és kezelése történik. A miénk neve `teststoragerg`.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) – létrehozza a Storage-fiókot. A példa a `testpshstorage`-t használja.

Az SKU neve megadja a Storage-fiók replikálásának típusát (például LRS (helyileg redundáns tárolás). További információ a replikálásról: az [Azure Storage replikációja](storage-redundancy.md).

> [!IMPORTANT]
> A Storage-fiók nevének egyedinek kell lennie az Azure-on belül, és kisbetűnek kell lennie. Elnevezési konvenciók és korlátozások esetében lásd: [tárolók, blobok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Most már rendelkezik egy új Storage-fiókkal és annak hivatkozásával.

## <a name="manage-the-storage-account"></a>A Storage-fiók kezelése

Most, hogy egy új Storage-fiókra vagy egy meglévő Storage-fiókra hivatkozik, a következő szakasz a Storage-fiók kezeléséhez használható parancsokat mutatja be.

### <a name="storage-account-properties"></a>Storage-fiók tulajdonságai

A Storage-fiók beállításainak módosításához használja a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Habár nem lehet módosítani a Storage-fiók helyét vagy az azt tartalmazó erőforráscsoportot, a többi tulajdonságot is módosíthatja. A következő lista a PowerShell használatával módosítható tulajdonságokat tartalmazza.

* A Storage-fiókhoz rendelt **egyéni tartomány** .

* A Storage-fiókhoz rendelt **címkék** . A címkéket gyakran használják az erőforrások számlázási célokra való kategorizálására.

* Az **SKU** a Storage-fiók replikációs beállítása, például a LRS a helyileg redundáns tároláshoz. Előfordulhat például,\_\_hogy a standard szintű LRS standard GRS vagy standard\_RAGRS vált. Vegye figyelembe, hogy a standard\_szintű ZRS,\_a standard szintű\_GZRS, a standard\_szintű RAGZRS és a prémium LRS nem módosítható más SKU-ra, vagy más SKU-ket is módosíthat ezekre.

* A blob Storage-fiókok **hozzáférési szintje** . A hozzáférési szint értéke a gyakori vagy a ritka elérésű értékre van állítva, és lehetővé teszi, hogy a Storage-fiók használatával összehangolt hozzáférési szint kiválasztásával csökkentse a költségeket. További információ: gyakori, ritka elérésű [és archív tárolási szintek](../blobs/storage-blob-storage-tiers.md).

* Csak HTTPS-forgalom engedélyezése.

### <a name="manage-the-access-keys"></a>A hozzáférési kulcsok kezelése

Az Azure Storage-fiókhoz két fiók kulcsa tartozik. A kulcsok lekéréséhez használja a [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Ez a példa az első kulcsot kéri le. Ha a másikat szeretné lekérni `Value[1]` , használja `Value[0]`a helyett a következőt:.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

A kulcs újralétrehozásához használja a [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

A másik kulcs újralétrehozásához használja `key2` a nevet a `key1`helyett.

Egy kulcs újragenerálása, majd az új érték megjelenítéséhez újra lekéri.

> [!NOTE]
> Körültekintő tervezést kell végrehajtania, mielőtt újragenerálja a kulcsot egy éles Storage-fiókhoz. Egy vagy mindkét kulcs újragenerálása érvényteleníti a hozzáférését bármely alkalmazáshoz az újragenerált kulcs használatával. További információ eléréséhez lásd: [Hozzáférési kulcsok](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Tárfiók törlése

A Storage-fiók törléséhez használja a [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Egy Storage-fiók törlésekor a fiókban tárolt összes eszköz is törlődik. Ha véletlenül töröl egy fiókot, azonnal hívja meg a támogatási szolgálatot, és nyisson meg egy jegyet a Storage-fiók visszaállításához. Az adatai helyreállítása nem garantált, de időnként működik. Ne hozzon létre olyan új Storage-fiókot, amelynek a neve megegyezik a régivel, amíg a támogatási jegyet meg nem oldotta.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>A Storage-fiók virtuális hálózatok és tűzfalakkal történő ellátása

Alapértelmezés szerint az összes Storage-fiók minden olyan hálózat számára elérhető, amely hozzáféréssel rendelkezik az internethez. Azonban úgy is konfigurálhatja a hálózati szabályokat, hogy csak bizonyos virtuális hálózatokból származó alkalmazások férhessenek hozzá a Storage-fiókhoz. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md).

A cikk bemutatja, hogyan kezelheti ezeket a beállításokat a következő PowerShell-parancsmagok használatával:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>A Storage Analytics használata  

[Azure Storage Analytics](storage-analytics.md) [Storage Analytics mérőszámokból](/rest/api/storageservices/about-storage-analytics-metrics) és [Storage Analytics naplózásból](/rest/api/storageservices/about-storage-analytics-logging)áll.

**Storage Analytics metrikák** használatával gyűjtheti az Azure Storage-fiókok metrikáit, amelyeket a Storage-fiók állapotának figyelésére használhat. A metrikák a blobokhoz, fájlokhoz, táblákhoz és várólistákhoz is engedélyezhetők.

**Storage Analytics a naplózás** kiszolgálóoldali, és lehetővé teszi a sikeres és sikertelen kérések adatainak rögzítését a Storage-fiókba. Ezek a naplók lehetővé teszik az olvasási, írási és törlési műveletek részleteit a táblák, a várólisták és a Blobok, valamint a sikertelen kérések okainak megtekintéséhez. A naplózás Azure Files esetén nem érhető el.

A figyelést a [Azure Portal](https://portal.azure.com), a PowerShell vagy programozott módon konfigurálhatja a Storage ügyféloldali kódtár használatával.

> [!NOTE]
> A percek elemzését a PowerShell használatával engedélyezheti. Ez a funkció nem érhető el a portálon.
>

* Ha szeretné megtudni, hogyan engedélyezheti és tekintheti meg a tárolási metrikák adatait a PowerShell használatával, tekintse meg a [Storage Analytics mérőszámait](storage-analytics-metrics.md).

* Ha szeretné megtudni, hogyan engedélyezheti és kérheti le a tárolási naplózási adatait a PowerShell használatával, tekintse meg a tárolók [naplózásának engedélyezése a PowerShell használatával](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) és [a tárolási naplózási naplófájlok](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)megkeresése

* A tárolási mérőszámok és a tárolók naplózásának a tárolási problémák elhárításához való használatával kapcsolatos részletes információkért lásd: [figyelés, diagnosztizálás és hibaelhárítás Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>A Storage-fiókban tárolt adatkezelés

Most, hogy megértette, hogyan kezelheti a Storage-fiókját a PowerShell-lel, az alábbi cikkekből megtudhatja, hogyan érheti el az adatobjektumokat a Storage-fiókban.

* [Blobok kezelése a PowerShell-lel](../blobs/storage-how-to-use-blobs-powershell.md)
* [Fájlok kezelése a PowerShell-lel](../files/storage-how-to-use-files-powershell.md)
* [Várólisták kezelése a PowerShell-lel](../queues/storage-powershell-how-to-use-queues.md)
* [Azure Table Storage-műveletek végrehajtása a PowerShell-lel](../../storage/tables/table-storage-how-to-use-powershell.md)

A Azure Cosmos DB Table API olyan prémium szintű funkciókat biztosít a Table Storage-hoz, mint a kulcsrakész globális terjesztés, az alacsony késésű olvasások és írások, az automatikus másodlagos indexelés és a dedikált átviteli sebesség.

* További információ: [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Független felhőalapú üzemelő példányok az Azure-ban

A legtöbb felhasználó az Azure nyilvános Felhőjét használja a globális Azure-beli üzembe helyezéshez. A Microsoft Azure a szuverenitás okai és így továbbra is független központi telepítések. Ezeket a független központi telepítéseket "környezeteknek" nevezzük. Ezek a rendelkezésre álló környezetek:

* [Azure Government felhő](https://azure.microsoft.com/features/gov/)
* [A 21Vianet által Kínában üzemeltetett Azure China 21Vianet-felhő](http://www.windowsazure.cn/)
* [Azure German Cloud](../../germany/germany-welcome.md)

A felhők és a hozzájuk tartozó tárolók PowerShell-lel való elérésével kapcsolatos információkért lásd: [tároló kezelése az Azure független felhőkben a PowerShell használatával](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot és egy Storage-fiókot ehhez a gyakorlathoz, akkor a yous eltávolíthatja az összes létrehozott eszközt az erőforráscsoport eltávolításával. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott Storage-fiókot és magát az erőforráscsoportot.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>További lépések

Ez a cikk a felügyeleti sík parancsmagokat használó általános műveleteket ismerteti a Storage-fiókok kezeléséhez. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Storage-fiókok listázása
> * Meglévő Storage-fiókra mutató hivatkozás beszerzése
> * Tárfiók létrehozása
> * A Storage-fiók tulajdonságainak beállítása
> * Hozzáférési kulcsok beolvasása és újragenerálása
> * A Storage-fiókhoz való hozzáférés biztosítása
> * Storage Analytics engedélyezése

Ez a cikk több más cikkre is hivatkozik, például az adatobjektumok kezelésével, a Storage Analytics engedélyezésével, valamint az Azure-beli független felhők, például a kínai felhő, a német felhő és a kormányzati felhő elérésének módjával. Íme néhány további kapcsolódó cikk és erőforrás a következő hivatkozásokhoz:

* [Azure Storage-vezérlési sík PowerShell-parancsmagok](/powershell/module/az.storage/)
* [Azure Storage-adatsíkok PowerShell-parancsmagjai](/powershell/module/azure.storage/)
* [Windows PowerShell-dokumentáció](https://msdn.microsoft.com/library/ms714469.aspx)
