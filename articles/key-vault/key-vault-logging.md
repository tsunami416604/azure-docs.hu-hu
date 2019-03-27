---
title: Azure Key Vault naplózása |} A Microsoft Docs
description: Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 25ebd72c512eb92c5d9a464a4b4d74f9e41ae389
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484112"
---
# <a name="azure-key-vault-logging"></a>Az Azure Key Vault naplózása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Miután létrehozott egy vagy több kulcstároló naplófájljainak, valószínűleg érdemes figyelni hogyan és mikor a kulcstartók-e a használt, és ki használja őket. Ez menti az adatokat az Ön által megadott Azure storage-fiókot az Azure Key vault naplózásának engedélyezésével teheti meg. Egy új tárolót **insights-logs-auditevent** automatikusan jön létre a megadott tárfiók számára. Ez ugyanazt a tárfiókot is használhat több kulcstároló naplófájljainak naplók gyűjtésére.

Elérheti a naplóinformációkat 10 perc (legfeljebb) a key vaulttal művelet után. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával. Fogja hozzon létre egy tárfiókot, naplózás engedélyezése és értelmezni a gyűjtött adatait.  

> [!NOTE]
> Az oktatóanyag nem tartalmazza a kulcstárolók, a kulcsok és titkos kulcsok létrehozásának lépéseit. Ez az információ: [Mi az Azure Key Vault?](key-vault-overview.md). Vagy a platformfüggetlen Azure CLI-vel utasításokért lásd: [ebben az oktatóanyagban](key-vault-manage-with-cli2.md).
>
> Ez a cikk az Azure PowerShell-utasításokat a diagnosztikai naplózás frissítése. Diagnosztikai naplózás az Azure Monitor használatával is frissítheti a **diagnosztikai naplók** szakaszában az Azure Portalon. 
>

A Key Vault – áttekintés kapcsolatban lásd: [Mi az Azure Key Vault?](key-vault-whatis.md). Hol érhető el a Key Vault kapcsolatos információkért tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy meglévő kulcstároló.  
* Az Azure PowerShell, 1.0.0-s minimális verzióját. Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat. Ha már telepítette az Azure PowerShell és a verzió nem ismeri az Azure PowerShell-konzolról, adja meg a `$PSVersionTable.PSVersion`.  
* A Key Vault naplóihoz elegendő tárhely az Azure-ban.

## <a id="connect"></a>Csatlakozás a key vault-előfizetés

Az első lépés a fő naplózási beállítása, hogy a kulcstartóhoz, amely a naplózni kívánt pont Azure PowerShell-lel.

Indítsa el az Azure PowerShell-munkamenetet, és jelentkezzen be Azure-fiókjába a következő parancs használatával:  

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Azure PowerShell megkeresi az ehhez a fiókhoz társított összes előfizetés. Alapértelmezés szerint a PowerShell kiválasztja az elsőt.

Előfordulhat, hogy a kulcstartó létrehozásához használt előfizetés megadásához. Adja meg a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

```powershell
Get-AzSubscription
```

Ezután a key vault fogja naplózás tartozó előfizetés megadásához, írja be:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Mutatva PowerShell a megfelelő előfizetést, egy fontos lépés, különösen akkor, ha a fiókjához társított több előfizetéssel rendelkezik. További információ az Azure PowerShell konfigurálásáról: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a id="storage"></a>Hozzon létre egy tárfiókot a naplók

Bár használhatja egy meglévő tárfiókot a naplók, hozunk létre egy tárfiókot, amelyet a Key Vault naplóinak dedikált lesz. Kényelmi célokat szolgál, később meg kell, hogy fogja tárolni a részletek nevű változóba **sa**.

Az egyszerű a mint a key vault tartalmazza is használunk ugyanabban az erőforráscsoportban. Az a [kezdeti lépéseket ismertető oktatóanyag](key-vault-get-started.md), ez az erőforráscsoport neve **ContosoResourceGroup**, és továbbra is Kelet-Ázsia a helyet használja. Cserélje le ezeket az értékeket a sajátjainak megfelelőkkel:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Ha úgy dönt, hogy egy meglévő tárfiókot használja, azt ugyanahhoz az előfizetéshez kell használnia, mint a key vaultban. És azt az Azure Resource Manager üzemi modell, nem pedig a klasszikus üzemi modellt kell használnia.
>
>

## <a id="identify"></a>A naplók kulcstárolójának azonosítása

Az a [kezdeti lépéseket ismertető oktatóanyag](key-vault-get-started.md), a kulcstartó neve **ContosoKeyVault**. Ezt a nevet, és a részletek tárolható egy változóban nevű továbbra is **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Naplózás engedélyezése

Ahhoz, hogy a Key Vault naplózását, fogjuk használni a **Set-AzDiagnosticSetting** parancsmagot, és az új tárfiókot, és a key vaultban létrehozott változókat. Emellett értékre állítjuk a **-engedélyezve** jelzőt **$true** és a kategóriát **AuditEvent** (a Key Vault naplózásának egyetlen kategóriája):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A kimenet a következőhöz hasonló:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Ez a kimenet megerősíti, hogy a naplózás engedélyezve van a key vaultban, és adatokat a tárfiókba menti.

Igény szerint beállíthatja adatmegőrzési a naplók úgy, hogy a régebbi naplófájlok automatikusan törlődnek. Például állítsa be a megőrzési házirend beállításával a **- RetentionEnabled** jelzőt **$true**, és állítsa be a **- RetentionInDays** paramétert **90**úgy, hogy a 90 napnál régebbi naplófájlok automatikusan törlődnek.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Mi kerül naplózásra?

* Minden hitelesített REST API-kérelmek, beleértve a hozzáférési engedélyek, rendszerhibákból vagy hibás kérésekből adótó sikertelen kérelmeket.
* Műveletek a kulcsot a tár magát, beleértve a létrehozása, törlés, a kulcstartó hozzáférési szabályzatok beállítása, valamint a kulcstartó-attribútumokat, például címkék frissítése.
* Műveletek a kulcsok és titkos kulcsok a key vaultban, többek között:
  * Létrehozása, módosítása vagy törlése, ezek a kulcsok vagy titkos kulcsok.
  * Aláírása, ellenőrzése, titkosítása, visszafejtése, alkalmazásburkoló és kicsomagolási kulcsok, titkos adatait, és a listaelem kulcsok és titkos kulcsok (és azok verzióinak).
* A 401-es választ eredményező, nem hitelesített kérelmek. Például a kérelmek, amelyek nem rendelkeznek tulajdonosi jogkivonattal, helytelen formátumúak vagy lejártak, vagy, amelyek érvénytelen jogkivonatot.  

## <a id="access"></a>A naplók elérése

Key Vault-naplók vannak tárolva a **insights-logs-auditevent** megadott storage-fiókban lévő tárolóba. A naplók megtekintéséhez, akkor tölthet le blobokat.

Először hozzon létre egy változót a tároló nevéhez. Ez a változó az útmutató többi részében fogja használni.

```powershell
$container = 'insights-logs-auditevent'
```

Ebben a tárolóban lévő összes BLOB listáját, írja be:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A kimeneti ehhez hasonlóan néz ki:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Ahogy a kimenetből látható, a blobokat egy elnevezési konvenciót követi: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Használhatja ugyanazt a tárfiókot a naplók több erőforrás, mert az a blob nevének teljes erőforrás-azonosítója hasznos eléréséhez, vagy csak a szükséges blobokat töltse le. Előtte azonban nézzük meg, hogyan tölthető le az összes blob.

Hozzon létre egy mappát, amelybe letölti a blobokat. Példa:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Majd kérje le az összes blob listáját:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

A listát a keresztül **Get-AzStorageBlobContent** töltse le a célmappába:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

A második parancs futtatásakor a **/** a blob nevének elválasztó alapján hoz létre egy teljes mapparendszert a rendeltetési mappára. Ez a struktúra segítségével töltse le és tárolja a blobok, fájlok.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Példa:

* Ha több kulcstárolóval rendelkezik, de csak a CONTOSOKEYVAULT3 nevűhöz szeretne naplókat letölteni:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Ha több erőforráscsoporttal rendelkezik, de csak egyhez szeretne naplókat letölteni, használja a `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` parancsot:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Ha azt szeretné, minden a naplók letöltéséhez január 2019 hónapban, `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Most már készen áll a naplók tartalmának megtekintésére. Azonban mielőtt adott, ha tisztában van két további parancsokat:

* A kulcstároló erőforrásához tartozó diagnosztikai beállítások állapotának lekérdezése:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* A kulcstároló erőforrása naplózásának letiltása:`Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>A Key Vault naplóinak értelmezése

Az egyes blobok JSON-blobként, szöveges formában vannak tárolva. Lássunk erre egy példa naplóbejegyzés. Futtassa ezt a parancsot:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Azt adja vissza egy naplóbejegyzés ehhez hasonló:

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

Az alábbi táblázat a mezők neveit és leírásait sorolja fel:

| Mező neve | Leírás |
| --- | --- |
| **idő** |Dátuma és időpontja (UTC). |
| **resourceId** |Az Azure Resource Manager-erőforrás azonosítója. A Key Vault naplóihoz Ez a Key Vault erőforrás-azonosítója. |
| **OperationName** |A művelet neve, ahogy a következő táblázat is mutatja. |
| **operationVersion** |Az ügyfél által kért REST API-verzió. |
| **kategória** |Eredmény típusa. A Key Vault naplóihoz **AuditEvent** az egyetlen elérhető érték van. |
| **resultType** |A REST API-kérés eredményét. |
| **resultSignature** |A HTTP-állapot. |
| **resultDescription** |Az eredmény további leírása, amennyiben elérhető. |
| **durationMs** |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet. |
| **callerIpAddress** |A kérést leadó ügyfél IP-címe. |
| **correlationId** |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| **Identitás** |A jogkivonat a REST API-kérelemben szereplő identitás. Ez általában a "felhasználó" a "szolgáltatásnév" vagy "felhasználó + appId," egy kérelmet, amely egy Azure PowerShell-parancsmaggal való gazdabuszadaptereken kombinációja. |
| **Tulajdonságok** |A művelet alapján, amely információkat (**operationName**). A legtöbb esetben ez a mező tartalmazza, ügyfél-információ (a felhasználói ügynök átadott karakterlánc-ügyfél által), a pontos REST API-kérelem URI-t és HTTP-állapotkód. Emellett ha egy objektumot ad vissza egy kérelem (például **KeyCreate** vagy **VaultGet**), is tartalmaz, a kulcs URI-JÁT ("id"), a tároló URI-t vagy a titkos URI. |

A **operationName** mező értékek *ObjectVerb* formátumban. Példa:

* Minden kulcstárolón elvégzett művelet a `Vault<action>` formátumú, például `VaultGet` és `VaultCreate`.
* Minden kulcson elvégzett művelet a `Key<action>` formátumú, például `KeySign` és `KeyList`.
* Minden titkos kulcson elvégzett művelet a `Secret<action>` formátumú, például `SecretGet` és `SecretListVersions`.

A következő táblázat felsorolja a **operationName** értékeket és a megfelelő REST API-parancsokat:

| operationName | REST API-parancs |
| --- | --- |
| **Hitelesítés** |Hitelesítés Azure Active Directory végpontján keresztül |
| **VaultGet** |[Kulcstároló adatainak lekérése](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Kulcstároló létrehozása vagy frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Kulcstároló törlése](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Kulcstároló frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Az erőforráscsoport összes kulcstárolójának listázása](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Kulcs adatainak lekérése](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Kulcs importálása egy tárolóba](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[A kulcs biztonsági mentése](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Kulcs törlése](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Kulcs helyreállítása](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Aláírás kulccsal](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Ellenőrzés kulccsal](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Kulcs becsomagolása](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Kulcs kicsomagolása](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Titkosítás kulccsal](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Visszafejtés kulccsal](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Kulcs frissítése](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Egy tároló kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Titkos kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Titkos kulcs lekérése](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Titkos kulcs frissítése](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Titkos kulcs törlése](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Egy tároló titkos kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Titkos kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Az Azure Monitor-naplók

Használatával a Key Vault megoldás az Azure Monitor naplóira tekintse át a Key Vault **AuditEvent** naplókat. Az Azure Monitor naplóira a naplólekérdezések adatok elemzése és a szükséges adatok lekéréséhez használhatja. 

További információk, beleértve az beállítására,: [Azure Key Vault megoldás az Azure Monitor naplóira](../azure-monitor/insights/azure-key-vault.md). Ez a cikk utasításokat is tartalmaz, ha, át kell állniuk a régi Key Vault megoldás, amely alatt az Azure Monitor naplóira volt elérhető, előzetes verzió, ahol irányítva először a naplókat az Azure storage-fiókba, és a beállított Azure figyelő naplózza, hogy onnan lehessen olvasni.

## <a id="next"></a>Következő lépések

Ez az oktatóanyag egy .NET-webalkalmazás az Azure Key Vault használja, lásd: [használata az Azure Key Vault-webalkalmazások](tutorial-net-create-vault-azure-web-app.md).

Programozási hivatkozások: [Az Azure Key Vault fejlesztői útmutatója](key-vault-developers-guide.md).

Az Azure Key Vault az Azure PowerShell 1.0-parancsmagok listáját lásd: [Azure Key Vault-parancsmagok](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

A kulcsforgatás és a napló az Azure Key Vault naplózásának oktatóanyagért lásd: [Key Vault beállítása végpontok közötti kulcsforgatással és auditálással](key-vault-key-rotation-log-monitoring.md).
