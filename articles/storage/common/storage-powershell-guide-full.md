---
title: Az Azure PowerShell-lel és az Azure Storage |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure PowerShell-parancsmagok az Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 78eeb986be5380d9e34a42872cbbdeee37654cc0
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317005"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)

Az Azure PowerShell segítségével hozzon létre, és az Azure-erőforrások kezelése a PowerShell-parancssorból vagy szkriptekkel. Az Azure Storage esetében ezek a parancsmagok két kategóriába--a vezérlősík és az adatsíkhoz. A vezérlő felügyeletisík-parancsmagok segítségével kezelheti a storage-fiók – tárfiókok létrehozása, tulajdonságainak, törli a storage-fiókok, a hozzáférési kulcsainak rotálása és így tovább. A tárolt adatok kezelésére használhatók az adatok felügyeletisík-parancsmagok *a* a storage-fiókot. Például blobok feltöltése, fájlmegosztások létrehozása, és üzeneteket ad hozzá egy üzenetsorba.

A cikkben található útmutató a felügyeletisík-parancsmagok használatával kezelheti a storage-fiókok olyan gyakori műveleteket ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Storage-fiókok listázása
> * Egy meglévő tárfiókot mutató hivatkozás beolvasása
> * Tárfiók létrehozása
> * Tárfiók tulajdonságainak beállítása
> * Kérje le, és a hozzáférési kulcsok újragenerálása
> * A tárfiókhoz való hozzáférés védelme
> * Enable Storage Analytics

Ez a cikk számos más PowerShell cikkekre mutató hivatkozások a Storage szolgáltatás esetében például engedélyezésével és a Storage Analytics eléréséhez, az adatok felügyeletisík-parancsmagok használata és elérése az Azure-például a kínai felhőben, a német felhőben és a Government független felhők Felhő.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ebben a gyakorlatban az Azure PowerShell-modul Az 0,7 vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

Ebben a gyakorlatban egy rendszeres PowerShell-ablakot be lehet írni a parancsokat, vagy használhatja a [Windows PowerShell integrált parancsfájl-kezelési környezet (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) be egy szövegszerkesztőben írja be a parancsokat, majd egy időben egy vagy több tesztparancsokat Nyissa meg a példákon keresztül. Jelöljön ki a sorokat, hajtsa végre, és kattintson a kijelölt futtatása csupán azokat a parancsokat futtatni szeretné.

Storage-fiókokkal kapcsolatos további információkért lásd: [Storage bemutatása](storage-introduction.md) és [tudnivalók az Azure storage-fiókok](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Az előfizetés a storage-fiókok listázása

Futtassa a [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) beolvasásához a tárfiókok listája, a jelenlegi előfizetésében.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Kérje le a hivatkozását egy storage-fiókba

Ezután egy hivatkozást a tárfiókra van szüksége. Hozzon létre egy új tárfiókot, vagy egy meglévő tárfiókot hivatkozás. A következő szakasz mindkét módszert bemutatja.

### <a name="use-an-existing-storage-account"></a>Meglévő tárfiók használata

Lekérni egy meglévő tárfiókot, szüksége van az erőforráscsoport nevét és a tárfiók nevére. Állítsa be a változókat az adott két mezők, majd használja a [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) parancsmagot.

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Most már $storageAccount, amely egy meglévő tárfiókot.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az alábbi parancsfájl bemutatja, hogyan hozzon létre egy általános célú fiók [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Miután létrehozta a fiókot, olvassa be a környezetét, amely használható az ezt követő parancsok helyett adja meg a hitelesítés az egyes hívások.

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

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) – lekéri az érvényese helyek listáját. A példában `eastus` helyéhez.

*   [Új AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) – létrehoz egy új erőforráscsoportot. Egy erőforráscsoport olyan logikai tároló, amelybe az Azure-erőforrások üzembe és felügyelhet. A sajátunkhoz nevezzük `teststoragerg`.

*   [Új AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) --a storage-fiókot hoz létre. A példában `testpshstorage`.

Az SKU neve a tárfiók, például az LRS (helyileg redundáns tárolás) replikáció típusát jelzi. A replikációval kapcsolatos további információkért lásd: [Azure Tárreplikáció](storage-redundancy.md).

> [!IMPORTANT]
> Azure-on belül egyedinek kell lennie a tárfiókja nevére, és kisbetűnek kell lennie. Elnevezési konvenciók és korlátozások: [elnevezése és a hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Most már rendelkezik egy új tárfiókot, és a egy hivatkozást.

## <a name="manage-the-storage-account"></a>A tárfiók kezelése

Most, hogy egy hivatkozás egy új tárfiókot vagy egy meglévő tárfiókot, a következő szakasz bemutatja a tárfiók kezelése segítségével parancsai közül néhányat.

### <a name="storage-account-properties"></a>A tárfiók tulajdonságai

A storage-fiók beállításainak módosításához használja [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Egy storage-fiókot, vagy az erőforráscsoport, amelyben található helye nem módosítható, amíg számos, a többi tulajdonság módosítható. A következő mutatja be a tulajdonságokat, módosíthatja a PowerShell használatával.

* A **egyéni tartomány** a tárfiókhoz rendelt.

* A **címkék** a tárfiókhoz rendelt. A címkék gyakran használják kategorizálhatja az erőforrásokat a számlázás tekintetében.

* A **Termékváltozat** a replikációs beállítás a tárfiók, mint a helyileg redundáns tárolás LRS. Például érdemes lehet módosítani a Standard\_LRS, Standard\_GRS vagy standard szintű\_RAGRS. Vegye figyelembe, hogy nem tudja módosítani Standard\_zrs-t vagy a prémium szintű\_más termékváltozatokra LRS, vagy módosítsa a többi termékváltozat ezeket.

* A **hozzáférési szint** Blob storage-fiókok. Hozzáférési szint értékét értéke **gyakori elérésű** vagy **ritkán használt adatok**, és lehetővé teszi, hogy a költségek minimalizálása a hozzáférési szint, amely igazodik a hogyan használhatja a storage-fiók kiválasztásával. További információkért lásd: [gyakori, ritka és archív tárolási szintek](../blobs/storage-blob-storage-tiers.md).

* Csak engedélyezze a HTTPS-forgalmat.

### <a name="manage-the-access-keys"></a>A hozzáférési kulcsok kezelése

Azure Storage-fiók fiók két kulccsal rendelkezik. A kulcsok lekéréséhez használja [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Ebben a példában az első kulcsot kérdezi le. Lekérdezheti a másikra, `Value[1]` helyett `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Újragenerálja a kulcsot, használja a [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Hozza létre újra a másik hívóbetűt, használja a `key2` helyett a legfontosabb neveként `key1`.

Hozza létre újra a kulcsok egyikét, és majd kérje le újra az új érték a.

> [!NOTE]
> Storage-fiókok a kulcs újragenerálása előtt gondosan kell végezni. Legalább az egyik kulcs újragenerálása érvényteleníti a hozzáférést minden olyan alkalmazáshoz, a kulcs újragenerálása használatával. További információ eléréséhez lásd: [Hozzáférési kulcsok](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Tárfiók törlése

Storage-fiók törléséhez használja [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Ha töröl egy tárfiókot, az eszközök a fiókban tárolt összes is törlődnek. Ha véletlenül töröl egy fiókot, az ügyfélszolgálat azonnal, és visszaállítani a tárfiók egy jegyet. Az adatok nem garantált, de egyes esetekben működik. Ne hozzon létre egy új tárfiókot a régi kiszolgálóéval azonos nevű megoldásáig egy támogatási jegyet.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>A storage-fiók használata a virtuális hálózatok és a tűzfalak védelme

Alapértelmezés szerint az összes storage-fiókok érhetők el szerint semmilyen hálózathoz, amely hozzáfér az internethez. Azonban konfigurálhatja úgy a hálózati szabályok a kizárólag a virtuális hálózatok adott alkalmazások a tárfiók eléréséhez. További információkért lásd: [konfigurálása az Azure Storage-tűzfalak és virtuális hálózatok](storage-network-security.md).

A cikk bemutatja, hogyan kezelheti ezeket a beállításokat a következő PowerShell-parancsmagok használatával:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>A storage analytics használata  

[Az Azure Storage Analytics](storage-analytics.md) áll [Storage Analytics Metrics](/rest/api/storageservices/about-storage-analytics-metrics) és [Storage Analytics naplózási](/rest/api/storageservices/about-storage-analytics-logging).

**Storage Analytics mérőszámainak** begyűjtése az Azure storage-fiókok, amellyel egy tárfiókot állapotának figyelésére szolgál. Metrikák blobok, fájlok, táblák és üzenetsorok esetén is engedélyezhető.

**Storage Analytics naplózási** kiszolgálói oldalon történik, és lehetővé teszi a tárfiókhoz való sikeres és sikertelen kérések rekord részleteit. Ezek a naplók lehetővé teszik az olvasási, írási és törlési műveleteinek a táblák, üzenetsorok, blobok, valamint az okok, a sikertelen kérelmek részleteinek megtekintéséhez. Naplózás nem érhető el az Azure Files számára.

Alkalmazásával konfigurálható a [az Azure portal](https://portal.azure.com), PowerShell, vagy programozott módon, a storage ügyféloldali kódtára segítségével.

> [!NOTE]
> Percenkénti analytics PowerShell használatával engedélyezheti. Ez a funkció a portálon nem érhető el.
>

* Megtudhatja, hogyan engedélyezése és megtekintése a Storage Metrics data PowerShell-lel, tekintse meg [Storage analytics mérőszámainak](storage-analytics-metrics.md).

* Megtudhatja, hogyan engedélyezheti és a Storage naplózási adatok PowerShell-lel, tekintse meg a [engedélyezése a Storage-naplózás PowerShell-lel](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) és [log Storage naplózási adatok keresése](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Részletes információk a Storage-mérőszámok és a Storage-naplózás használatával tárolási hibáinak elhárítása: [figyelés felismerése és hibáinak elhárítása a Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>A storage-fiókban tárolt adatok kezelése

Most, hogy megismerkedett a PowerShell-lel a tárfiók kezelése, a következő cikkek segítségével ismerje meg a storage-fiókban lévő adatok objektumok elérése.

* [Hogyan kezelheti a blobokat a PowerShell-lel](../blobs/storage-how-to-use-blobs-powershell.md)
* [A PowerShell-lel fájlok kezelése](../files/storage-how-to-use-files-powershell.md)
* [A PowerShell-lel várólisták kezelése](../queues/storage-powershell-how-to-use-queues.md)
* [Az Azure Table storage műveleteket a PowerShell-lel](../../storage/tables/table-storage-how-to-use-powershell.md)

Az Azure Cosmos DB Table API a table storage, például a kulcsrakész globális disztribúciót, kis késleltetésű olvasásokhoz, és írási, automatikus másodlagos indexelést és dedikált átviteli sebességet biztosít a prémium szintű funkciók.

* További információkért lásd: [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Az Azure a független felhőben üzemelő példányok

A legtöbb ember használata az Azure nyilvános felhő a globális Azure üzemelő példányhoz. Vannak bizonyos független üzembe helyezések, a Microsoft Azure az adatszuverenitási miatt és így tovább. Ezek független üzembe helyezések nevezik "környezetekben." A rendelkezésre álló környezetek az alábbiak:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Kínában a 21Vianet által üzemeltetett Azure China 21Vianet felhő](http://www.windowsazure.cn/)
* [Az Azure német felhőben](../../germany/germany-welcome.md)

Ezek a felhők és azok a PowerShell-lel elérésével kapcsolatos további információkért tekintse meg [Storage kezelése PowerShell használatával az Azure független felhőkben](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot és a egy storage-fiókot ehhez a gyakorlathoz, yous távolítsa el az eszközök, az erőforráscsoport eltávolításával létrehozott összes. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott tárfiókot és magát az erőforráscsoportot.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>További lépések

A cikkben található útmutató a felügyeletisík-parancsmagok használatával kezelheti a storage-fiókok olyan gyakori műveleteket ismerteti. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Storage-fiókok listázása
> * Egy meglévő tárfiókot mutató hivatkozás beolvasása
> * Tárfiók létrehozása
> * Tárfiók tulajdonságainak beállítása
> * Kérje le, és a hozzáférési kulcsok újragenerálása
> * A tárfiókhoz való hozzáférés védelme
> * Enable Storage Analytics

Ez a cikk számos más cikkeket, például a data-objektumok kezelése, a Storage Analytics engedélyezése és elérése az Azure független felhőkben, például a kínai felhőben, a német felhőben és a Government Cloud mutató hivatkozásokat is megadja. Íme, néhány további kapcsolódó cikkek és erőforrások referenciaként:

* [Az Azure Storage vezérlési sík PowerShell-parancsmagok](/powershell/module/az.storage/)
* [Az Azure Storage adat adatsík PowerShell-parancsmagok](/powershell/module/azure.storage/)
* [Windows PowerShell-referencia](https://msdn.microsoft.com/library/ms714469.aspx)
