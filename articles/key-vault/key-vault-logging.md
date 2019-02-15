---
title: Az Azure Key Vault-naplózás – az Azure Key Vault |} A Microsoft Docs
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
ms.openlocfilehash: 61f277eda721c1490d9f4b354442718558830fe7
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300199"
---
# <a name="azure-key-vault-logging"></a>Az Azure Key Vault naplózása

Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Bevezetés

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy vagy több kulcstároló létrehozása után célszerű figyelni, hogy hogyan, mikor és kik férnek hozzá a kulcstárolókhoz. Ehhez engedélyezze a Key Vault naplózását, amely egy Ön által megadott Azure-tárfiókba menti az adatokat. A megadott tárfiókhoz automatikusan létrehozunk egy **insights-logs-auditevent** nevű tárolót, amelyet több kulcstároló naplófájljainak tárolására is használhat.

A kulcstároló műveletei után legfeljebb 10 perccel már elérheti a naplóinformációkat. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával, a tárfiók létrehozásával, a naplózás engedélyezésével és az összegyűjtött naplóinformációk értelmezésével.  

> [!NOTE]
> Az oktatóanyag nem tartalmazza a kulcstárolók, a kulcsok és titkos kulcsok létrehozásának lépéseit. Ez az információ: [Mi az Azure Key Vault?](key-vault-overview.md). A platformfüggetlen parancssori felületre vonatkozó utasításokat megtekintheti [ebben a megfelelő oktatóanyagban](key-vault-manage-with-cli2.md).
>
> Ez a cikk az Azure PowerShell-utasításokat a diagnosztikai naplózás frissítéséhez. Azonban azonos engedélyezhető az Azure Portalon az Azure Monitor használatával a **diagnosztikai naplók** szakaszban. 
>
>

Áttekintést az Azure Key Vaultról a [What is Azure Key Vault?](key-vault-whatis.md) (Mi az Azure Key Vault?) című cikkben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy meglévő kulcstároló.  
* Az Azure PowerShell **1.0.0-s minimális verziója**. Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat. Ha már telepítette az Azure PowerShellt, de nem tudja, melyik verziót, írja be az Azure PowerShell-konzolon az alábbi parancsot: `$PSVersionTable.PSVersion`.  
* A Key Vault naplóihoz elegendő tárhely az Azure-ban.

## <a id="connect"></a>Csatlakozás az előfizetésekhez

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```PowerShell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes olyan előfizetést, amely ehhez a fiókhoz van rendelve, és alapértelmezés szerint kiválasztja az elsőt.

Ha több előfizetése van, előfordulhat, hogy meg kell adnia azt, amelyikkel az Azure Key Vault tárolóját létrehozta. Írja be az alábbi parancsot a fiókhoz tartozó előfizetések megjelenítéséhez:

```PowerShell
    Get-AzSubscription
```

Ezt követően a naplózandó kulcstárolóhoz tartozó előfizetés megadásához írja be a következő parancsot:

```PowerShell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ez egy nagyon fontos lépés, és különösen hasznosnak bizonyulhat, ha több előfizetés tartozik a fiókjához. Egy, a Microsoft.Insights-regisztrációról szóló hibaüzenetet kaphat, ha kihagyja ezt a lépést.
>   
>

További információ az Azure PowerShell konfigurálásáról: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a id="storage"></a>Új tárfiók létrehozása a naplóknak

Bár meglévő tárfiókot is használhat a naplózáshoz, mi létrehozunk egy újat, amely kimondottan a Key Vault naplóinak tárolására szolgál majd. Ezt később kell csak megadni, így addig a vonatkozó részleteket egy **sa** nevű változóban tároljuk.

Az egyszerű használat érdekében ugyanazt az erőforráscsoportot használjuk, amelyben a kulcstároló is található. Az [oktatóanyagból](key-vault-overview.md) kiindulva az erőforráscsoport neve **ContosoResourceGroup** lesz, a helye pedig továbbra is Kelet-Ázsia. Az alábbi értékeket helyettesítse a sajátjainak megfelelőkkel:

```PowerShell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Ha egy meglévő tárfiók használata mellett dönt, azt ugyanazzal az előfizetéssel kell használnia, mint a kulcstárolót, és a Resource Manager-alapú üzemi modellt kell alkalmaznia, nem a klasszikust.
>
>

## <a id="identify"></a>A naplók kulcstárolójának azonosítása

Az oktatóanyagban a kulcstároló neve **ContosoKeyVault** volt, így a továbbiakban is azt használjuk, a részleteket pedig egy **kv** nevű változóban tároljuk:

```PowerShell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Naplózás engedélyezése

Ahhoz, hogy a Key Vault naplózását, a Set-AzDiagnosticSetting parancsmagot, és az új tárfiók és kulcstároló létrehozott fogjuk használni. Emellett értékre állítjuk a **-engedélyezve** jelzőt **$true** , a kategóriát pedig auditevent (a Key Vault naplózásának egyetlen kategóriája):

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A kimenet a következőképpen jelenik meg:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Ezzel megerősíti, hogy a naplózás be lett kapcsolva a kulcstárolóban, így az információk a tárfiókba lesznek mentve.

Opcionálisan beállíthat egy megtartási házirendet a naplóihoz, így a régebbi naplófájlok automatikusan törlődni fognak. Például állítsa be a megtartási házirendet a **-RetentionEnabled** jelző használatával a **$true** értékre, majd állítsa a **-RetentionInDays** paramétert **90** értékre, így a 90 napnál régebbi naplófájlok automatikusan törlődnek.

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Mi kerül naplózásra?

* Minden hitelesített REST API-kérés naplózásra kerül, beleértve a hozzáférési engedélyekből, rendszerhibákból vagy hibás kérésekből adótó sikertelen kérelmeket is.
* A magán a kulcstárolón elvégzett műveletek, beleértve a létrehozást, az eltávolítást, a kulcstároló hozzáférési szabályzatainak beállítását és a kulcstároló tulajdonságainak (például címkéinek) frissítését.
* A kulcstároló kulcsain és titkos kulcsain elvégzett műveletek, beleértve a létrehozást, a módosítást és a kulcsok vagy titkos kulcsok törlését; az olyan műveletek, mint az aláírás, az ellenőrzés, a titkosítás, a visszafejtés, a kulcsok be- és kicsomagolása, a titkos kulcsok lekérése, valamint a kulcsok és titkos kulcsok és azok verzióinak listázása.
* A 401-es választ eredményező, nem hitelesített kérelmek. Ilyenek például azok a kérelmek, amelyek nem rendelkeznek tulajdonosi jogkivonattal, helytelen formátumúak vagy lejártak, vagy érvénytelen a jogkivonatuk.  

## <a id="access"></a>A naplók elérése

A kulcstároló naplóit a rendszer a megadott tárfiók **insights-logs-auditevent** nevű tárolójában tárolja. A tároló összes blobjának megjelenítéséhez írja be az alábbi parancsot:

Először hozzon létre egy változót a tároló nevéhez. Ez az útmutató többi részében lesz használható.

```PowerShell
$container = 'insights-logs-auditevent'
```

A tároló összes blobjának megjelenítéséhez írja be az alábbi parancsot:

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A kimenet ehhez hasonlóan néz ki:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Ahogyan a kimenetből látható, a blobok az alábbi elnevezési módszert alkalmazzák: **erőforrás-azonosító=<ARM resource ID>/é=<year>/h=<month>/n=<day of month>/ó=<hour>/p=<minute>/fájlnév.json**

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Mivel ugyanazt a tárfiókot a naplók több erőforrás is használható, a blob nevének teljes erőforrás-azonosítója hasznos eléréséhez, vagy csak a szükséges blobokat töltse le. Előtte azonban nézzük meg, hogyan tölthető le az összes blob.

Elsőként hozzon létre egy mappát, amelybe letölti a blobokat. Példa:

```PowerShell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Majd kérje le az összes blob listáját:  

```PowerShell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Ebben a listában "Get-AzStorageBlobContent" töltse le a mappába keresztül szolgáltatása:

```PowerShell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

A második parancs futtatásakor a blob nevének **/** elválasztója egy teljes mapparendszert létrehoz a célmappában, és a program ebben a rendszerben tárolja majd fájlokként a letöltött blobokat.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Példa:

* Ha több kulcstárolóval rendelkezik, de csak a CONTOSOKEYVAULT3 nevűhöz szeretne naplókat letölteni:

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
```

* Ha több erőforráscsoporttal rendelkezik, de csak egyhez szeretne naplókat letölteni, használja a `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` parancsot:

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
```

* Ha 2016 januárjának összes naplóját szeretné letölteni, használja a `-Blob '*/year=2016/m=01/*'` parancsot:

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
```

Most már készen áll a naplók tartalmának megtekintésére. Azonban mielőtt ezt a két paramétert a Get-AzDiagnosticSetting, ismernie kell:

* A kulcstároló erőforrásához tartozó diagnosztikai beállítások állapotának lekérdezése:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* A kulcstároló erőforrása naplózásának letiltása:`Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>A Key Vault naplóinak értelmezése

Az egyes blobok JSON-blobként, szöveges formában vannak tárolva. Fut

```PowerShell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Visszaadja egy naplóbejegyzés hasonlít az alábbi képen látható:

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

Az alábbi táblázat a mezők neveit és leírásait sorolja fel.

| Mező neve | Leírás |
| --- | --- |
| time |Dátum és idő (UTC). |
| resourceId |Az Azure Resource Manager szerinti erőforrás-azonosító. A Key Vault-naplók esetében mindig ez a Key Vault erőforrás-azonosítója. |
| operationName |A művelet neve, ahogy a következő táblázat is mutatja. |
| operationVersion |Az ügyfél által kért REST API verziója. |
| category |A Key Vault naplóihoz az AuditEvent az egyetlen elérhető érték. |
| resultType |A REST API-kérelem eredménye. |
| resultSignature |A HTTP-állapot. |
| resultDescription |Az eredmény további leírása, amennyiben elérhető. |
| durationMs |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet. |
| callerIpAddress |A kérelmet leadó ügyfél IP-címe. |
| correlationId |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| identity |A REST API-kérelemhez megadott tokenben szereplő identitás. Ez általában egy „felhasználó”, „egyszerű szolgáltatásnév” vagy „felhasználó + alkalmazás-azonosító”, az Azure PowerShell-parancsmagok által eredményezett kérelmekhez hasonlóan. |
| properties |Ez a mező a művelettől (operationName) függően más-más adatokat tartalmaz. A legtöbb esetben tartalmazza az ügyfél-információ (a felhasználói ügynök átadott karakterlánc-ügyfél által), a pontos REST API-kérelem URI-t és HTTP-állapotkódot. Ezenkívül ha egy objektumot a rendszer egy kérelem (például a KeyCreate vagy a VaultGet) eredményeként ad vissza, a kulcs URI-ját („id”), a tároló URI-ját vagy a titkos kulcs URI-ját is tartalmazza. |

Az **operationName** mező értékei ObjectVerb formátumúak. Példa:

* Minden kulcstárolón elvégzett művelet „Vault`<action>`” formátumú, például `VaultGet` és `VaultCreate`.
* Minden kulcson elvégzett művelet „Key`<action>`” formátumú, például `KeySign` és `KeyList`.
* Minden titkos kulcson elvégzett művelet „Secret`<action>`” formátumú, például `SecretGet` és `SecretListVersions`.

Az alábbi táblázat az operationName műveleteket és a megfelelő REST API-parancsokat listázza.

| operationName | REST API-parancs |
| --- | --- |
| Authentication |Az Azure Active Directory végpontján keresztül |
| VaultGet |[Kulcstároló adatainak lekérése](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| VaultPut |[Kulcstároló létrehozása vagy frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultDelete |[Kulcstároló törlése](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| VaultPatch |[Kulcstároló frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[Az erőforráscsoport összes kulcstárolójának listázása](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| KeyCreate |[Kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| KeyGet |[Kulcs adatainak lekérése](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| KeyImport |[Kulcs importálása egy tárolóba](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| KeyBackup |[Kulcs biztonsági mentése](https://msdn.microsoft.com/library/azure/dn878058.aspx). |
| KeyDelete |[Kulcs törlése](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| KeyRestore |[Kulcs helyreállítása](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| KeySign |[Aláírás kulccsal](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| KeyVerify |[Ellenőrzés kulccsal](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| KeyWrap |[Kulcs becsomagolása](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| KeyUnwrap |[Kulcs kicsomagolása](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| KeyEncrypt |[Titkosítás kulccsal](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| KeyDecrypt |[Visszafejtés kulccsal](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| KeyUpdate |[Kulcs frissítése](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| KeyList |[Egy tároló kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| KeyListVersions |[Kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| SecretSet |[Titkos kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| SecretGet |[Titkos kulcs lekérése](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| SecretUpdate |[Titkos kulcs frissítése](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| SecretDelete |[Titkos kulcs törlése](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| SecretList |[Egy tároló titkos kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| SecretListVersions |[Titkos kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>A Log Analytics használata

A Log Analyticsben az Azure Key Vault megoldással áttekintheti az Azure Key Vault AuditEvent-naplókat. További információért és a beállítás módjának ismertetéséért olvassa el [az Azure Key Vault megoldás a Log Analyticsben](../azure-monitor/insights/azure-key-vault.md) történő használatát ismertető cikket. Ez a cikk útmutatást biztosít a Log Analytics előzetes verziójában elérhető, korábbi Key Vault megoldásról történő migráláshoz is, amelynek során először a naplókat kellett átirányítani egy Azure Storage-fiókra, majd konfigurálni a Log Analyticset, hogy onnan lehessen olvasni.

## <a id="next"></a>Következő lépések

Ez az oktatóanyag egy .NET-webalkalmazás az Azure Key Vault használja, lásd: [használata az Azure Key Vault-webalkalmazások](tutorial-net-create-vault-azure-web-app.md).

Programozási hivatkozások: [Az Azure Key Vault fejlesztői útmutatója](key-vault-developers-guide.md).

Az Azure Key Vaultra vonatkozó Azure PowerShell 1.0-parancsmagok listáját az [Azure Key Vault Cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Az Azure Key Vault parancsmagjai) című témakörben találja.

A kulcsrotálással és a naplózással kapcsolatos oktatóanyag: [How to setup Key Vault with end to end key rotation and auditing](key-vault-key-rotation-log-monitoring.md) (A Key Vault beállítása átfogó kulcsrotálással és naplózással).
