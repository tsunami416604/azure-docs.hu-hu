---
title: "Az Azure Storage Azure PowerShell használatával |} Microsoft Docs"
description: "Útmutató az Azure PowerShell-parancsmagok használata az Azure Storage."
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: robinsh
ms.openlocfilehash: 1046e407bb4e9d07e91014384e9eba7b0c7020a8
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="using-azure-powershell-with-azure-storage"></a>Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)

Az Azure PowerShell létrehozása és kezelése az Azure-erőforrások, a PowerShell-parancssorból vagy a parancsfájlok segítségével. Az Azure Storage ezek a parancsmagok két kategóriába--a vezérlő vezérlősík és az adatok vezérlősík. A vezérlő vezérlősík parancsmagok segítségével kezelheti a tárfiók – storage-fiókok létrehozása, állítsa be a tulajdonságokat, storage-fiókok törlése, a hívóbetűk elforgatása és stb. Az adatok vezérlősík parancsmagok segítségével kezelhetők a tárolt adatok *a* a tárfiók. Például blobok feltöltése, fájlmegosztások létrehozása és üzenetek ad hozzá egy üzenetsort.

A cikkben található útmutató storage-fiókok kezelése a vezérlősík parancsmagokat gyakori műveletek ismerteti. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Listában tárfiókok
> * Meglévő tárfiók hivatkozás
> * Create a storage account 
> * Állítsa be a tárfiók tulajdonságai
> * Kérje le, majd a tárelérési kulcsok újragenerálása
> * A tárfiók eléréséhez védelme 
> * Tárolási analitika engedélyezése

Ez a cikk mutató hivatkozásokat biztosít több más PowerShell cikkek tárolási, például engedélyezéséről és a tárolási analitika hozzáférni, az adatok vezérlősík parancsmagok használatával és hogyan érhetők el az Azure független felhők például Kína felhő német felhő vagy kormányzati Felhő.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ebben a gyakorlatban az Azure PowerShell 4.4. vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. 

Ehhez a gyakorlathoz adhatja meg a parancsokat egy rendszeres PowerShell-ablakban, vagy használhatja a [Windows PowerShell integrált parancsfájlkezelési környezet (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) a parancsok írja be egy szerkesztővel, majd egy időben egy vagy több parancsból tesztelése a példák végighaladnia. Kiemelheti hajtható végre, és kattintson, csak a kijelölt futtatása azokat a parancsokat futtatni kívánt sorokat.

Storage-fiókok kapcsolatos további információkért lásd: [Storage bemutatása](storage-introduction.md) és [tudnivalók az Azure storage-fiókok](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Az előfizetés tárfiókok listázása

Futtassa a [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) parancsmagot, hogy a jelenlegi előfizetés tárfiókjai listájának beolvasása. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>A tárfiók hivatkozás

A következő lépésben egy hivatkozást egy tárfiókot. Hozzon létre egy új tárfiókot, vagy egy meglévő tárfiók való hivatkozás. A következő szakasz mindkét módszer jeleníti meg. 

### <a name="use-an-existing-storage-account"></a>Meglévő tárfiók használata 

Meglévő tárfiók lekéréséhez van szüksége az erőforráscsoport nevét és a tárfiók nevét. A két mezőket a változókat, akkor használja a [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) parancsmag. 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Most már rendelkezik $storageAccount, amely egy meglévő tárfiók mutat.

### <a name="create-a-storage-account"></a>Create a storage account 

Az alábbi parancsfájl bemutatja, hogyan hozzon létre egy általános célú tárfiókok segítségével [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Miután létrehozta a fiókot, a környezetben, amely használható a következő parancsok beolvasása helyett adja meg a hitelesítés minden hívással.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

A parancsfájl a következő PowerShell-parancsmagokat használja: 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) --lekéri az érvényese helyek listáját. A példa `eastus` helyéhez.

*   [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) --hoz létre egy új erőforráscsoportot. Erőforráscsoport egy olyan logikai tároló, amelyben az Azure-erőforrások telepítése és kezelése. Is nevezik `teststoragerg`. 

*   [Új-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) --tényleges tárolási fiókot hoz létre. A példa `testpshstorage`.

A termékváltozat replikációs LRS (helyileg redundáns tárolás) például a tárfiók típusát jelöli. A replikációval kapcsolatos további információkért lásd: [Azure Storage replikációs](storage-redundancy.md).

> [!IMPORTANT]
> A tárfiók neve Azure belül egyedieknek kell lenniük, és kisbetűnek kell lennie. Az elnevezési konvenciókat és korlátozások, lásd: [elnevezési és hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Most, hogy egy új tárfiókot, és hivatkozni rá. 

## <a name="manage-the-storage-account"></a>A tárfiók kezelése

Most, hogy egy hivatkozást egy új tárfiókot vagy egy meglévő tárfiókot használ, a következő szakaszban néhány mutatja be a parancsok a tárfiók kezelése segítségével.

### <a name="storage-account-properties"></a>A tárfiók tulajdonságai

A storage-fiók beállításainak módosításához használja [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Egy tárfiókot, vagy azt az erőforráscsoport helye nem módosítható, amíg a többi tulajdonság számos módosíthatja. Az alábbi listában találja a Tulajdonságok módosíthatja a PowerShell használatával.

* A **egyéni tartomány** a tárfiók rendelt.

* A **címkék** a tárfiók rendelt. Címkék gyakran használják a számlázási okokból kategorizálhatja az erőforrásokat.

* A **SKU** a replikációs beállítás a tárfiók, például az LRS helyileg redundáns tárolás. Például előfordulhat, hogy módosítja a Standard\_LRS, Standard\_GRS vagy Standard\_RAGRS. Vegye figyelembe, hogy nem módosítható a ZRS Standard vagy prémium szintű LRS más termékváltozatok való, továbbá egyéb termékváltozatok módosítani ezeket a. 

* A **hozzáférési szint** Blob storage-fiókok. A hozzáférési réteg értéke **működés közbeni** vagy **lassú**, és lehetővé teszi, hogy a storage-fiók használatának igazodik a hozzáférési szint kiválasztásával a költségek minimalizálása érdekében. További információkért lásd: [közbeni, hűtsük le, és archiválja a tárolási rétegek](../blobs/storage-blob-storage-tiers.md).

* A storage szolgáltatás oszloptitkosítási beállítás a blob storage és/vagy a file storage. Az SSE kapcsolatos további információkért lásd: [Storage szolgáltatás titkosítási](storage-service-encryption.md).

* Engedélyezése csak a HTTPS-forgalmat. 

### <a name="manage-the-access-keys"></a>A elérési kulcsok kezelése

Egy Azure Storage-fiók két kulcsait tartalmaz. A kulcsok lekéréséhez használja [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). Ez a példa lekéri az első kulcsát. Egy másikra lekéréséhez használja `Value[1]` helyett `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Újragenerálja a kulcsot, használja a [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

A más kulcs újragenerálása, használja a `key2` ahelyett, hogy a kulcs neveként `key1`.

Újragenerálja a kulcsokat egyikét, és majd még egyszer tekintse meg az új érték beolvasása.

> [!NOTE] 
> Végre kell hajtania üzemi tárfiók kulcsának újragenerálása előtt gondosan meg kell tervezni. Legalább az egyik kulcs újragenerálása érvényteleníti a hozzáférési kulcs újragenerálása használatával bármely alkalmazás. További információkért lásd: [Regenate tárelérési kulcsok](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Tárfiók törlése 

A storage-fiók törléséhez használja [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Ha töröl egy tárfiókot, az eszközök a fiókban tárolt összes is törlődnek. Ha véletlenül törli a fiókot, azonnal forduljon az ügyfélszolgálathoz, és nyissa meg a tárfiók visszaállítása jegy. Az adatok helyreállítása nem garantált, de egyes esetekben működik. Ne hozzon létre egy új tárfiókot a régi kiszolgálóéval azonos nevű megoldásáig a támogatási jegy. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>A tárolási fiók használata a virtuális hálózatokat és a tűzfalon védelme

Alapértelmezés szerint az összes tárfiók található elérhető-e olyan hálózathoz, amely hozzáfér az internethez. Azonban a hálózati szabályokat, hogy csak a tárfiók eléréséhez a meghatározott virtuális hálózatok alkalmazások is beállíthatja. További információkért lásd: [konfigurálása Azure Storage tűzfalak és a virtuális hálózatok](storage-network-security.md). 

A cikk bemutatja, hogyan kezelheti ezeket a beállításokat a következő PowerShell-parancsmagok használatával:
* [Adja hozzá AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Frissítés-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="use-storage-analytics"></a>Tárolási analitika használata  

[Az Azure Storage Analytics](storage-analytics.md) áll [Storage Analytics Metrics](/rest/api/storageservices/about-storage-analytics-metrics) és [Storage Analytics naplózás](/rest/api/storageservices/about-storage-analytics-logging). 

**Storage Analytics metrikák** gyűjtéséhez az Azure storage-fiókok, amelyek segítségével a tárfiók állapotának figyelésére szolgál. Metrikák blobokat, fájlok, táblát és üzenetsort is engedélyezhető.

**Storage Analytics naplózás** kiszolgálóoldali történik, és lehetővé teszi, hogy a tárfiók sikeres és a sikertelen kérelmek adatainak rögzítését. Ezek a naplók lehetővé teszik a Részletek területen az olvasási, írási és törlési műveleteket a táblák, üzenetsorok, blobok, valamint a sikertelen kérelmek okait. Naplózás nincs Azure-fájlok esetén érhető el.

Alkalmazásával konfigurálható a [Azure-portálon](https://portal.azure.com), PowerShell-lel, vagy programozott módon, a storage ügyféloldali kódtára. 

> [!NOTE]
> A percben analytics PowerShell használatával engedélyezheti. Ez a funkció nem érhető el a portálon.
>

* Engedélyezése és megtekintése a PowerShell használatával Storage Metrics data kapcsolatban [engedélyezése Azure Storage metrikák és megtekintése a metrikai adatok](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Engedélyezése és a PowerShell használatával tároló-naplózás adatainak beolvasása további tudnivalókért lásd: [engedélyezése a tárolási-naplózás PowerShell-lel](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) és [keresése a naplózás tárolási naplóadatok](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data).

* A Storage Metrics és a naplózás tárolási tárolási problémák elhárítása részletes információkért lásd: [figyelés felismerése és hibaelhárítása a Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>A tárfiókban lévő adatokat kezelése

Most, hogy megismerte a PowerShell használatával a tárfiók kezelése, a következő cikkek segítségével megtudhatja, hogyan érhetők el a data objects a tárfiókban lévő.

* [A PowerShell-lel blobok kezelése](../blobs/storage-how-to-use-blobs-powershell.md)
* [A PowerShell-lel fájlok kezelése](../files/storage-how-to-use-files-powershell.md)
* [A PowerShell-lel várólisták kezelése](../queues/storage-powershell-how-to-use-queues.md)

## <a name="azures-independently-deployed-clouds"></a>Azure-egymástól függetlenül telepített felhők

A legtöbben Azure nyilvános Felhőjében használja a globális Azure telepítésekhez. Van még néhány független üzemelő Microsoft Azure a közös joghatóság alá okait, és így tovább. A független központi telepítéseket nevezzük "environments." A rendelkezésre álló környezetekben az alábbiak:

* [Az Azure Government felhő](https://azure.microsoft.com/features/gov/)
* [Azure Kínában a 21Vianet által működtetett Kína-felhő](http://www.windowsazure.cn/)
* [Az Azure német felhő](../../germany/germany-welcome.md)

Hogyan érhetők el a felhők és a tárolás, a PowerShell használatával kapcsolatos információkért lásd: [tárolás kezelése a PowerShell használata Azure független felhők](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot és az ebben a gyakorlatban egy tárfiókot, yous is távolítsa el az összes az eszközök eltávolítása az erőforráscsoport hozta létre. Ez törli a csoportban található összes erőforrást is. Ebben az esetben eltávolítja a létrehozott tárfiókban és az erőforráscsoport magát.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Következő lépések

A cikkben található útmutató storage-fiókok kezelése a vezérlősík parancsmagokat gyakori műveletek ismerteti. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Listában tárfiókok
> * Meglévő tárfiók hivatkozás
> * Create a storage account 
> * Állítsa be a tárfiók tulajdonságai
> * Kérje le, majd a tárelérési kulcsok újragenerálása
> * A tárfiók eléréséhez védelme 
> * Tárolási analitika engedélyezése

Ez a cikk számos további cikkeit, például az adatok objektumok kezelése, engedélyezése a tárolási analitika és hogyan érhetők el az Azure független felhők például Kína felhő német felhő vagy kormányzati felhő mutató hivatkozásokat is biztosít. Íme, néhány további kapcsolódó cikkek és referenciaként erőforrások:

* [Az Azure Storage vezérlő vezérlősík PowerShell-parancsmagok](/powershell/module/AzureRM.Storage/)
* [Az Azure Storage adatok vezérlősík PowerShell-parancsmagok](/powershell/module/azure.storage/)
* [A Windows PowerShell-referencia](https://msdn.microsoft.com/library/ms714469.aspx)