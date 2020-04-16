---
title: Azure Key Vault naplózása | Microsoft dokumentumok
description: Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: e9198892f95635add27bcfe9e479d0dd6fe3f08d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422591"
---
# <a name="azure-key-vault-logging"></a>Az Azure Key Vault naplózása

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egy vagy több kulcstartó létrehozása után valószínűleg figyelni szeretné, hogyan és mikor érhetők el a kulcstartók, és ki által. Ezt úgy teheti meg, hogy engedélyezi az Azure Key Vault naplózását, amely adatokat ment egy Azure storage-fiókban, amelyet ön biztosít. Egy új tároló nevű **insights-logs-auditevent** automatikusan létre jön a megadott tárfiókhoz. Használhatja ugyanazt a tárfiókot naplók gyűjtésére több key vaults.

A naplózási adatok at 10 perccel (legutoljára) a key vault-művelet után érheti el. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával. Hozzon létre egy tárfiókot, engedélyezze a naplózást, és értelmezze az összegyűjtött naplóadatokat.  

> [!NOTE]
> Az oktatóanyag nem tartalmazza a kulcstárolók, a kulcsok és titkos kulcsok létrehozásának lépéseit. Ezt a tájékoztatást lásd: [Mi az Azure Key Vault?](overview.md)). Vagy a platformfüggetlen Azure CLI-utasításokért tekintse meg [ezt az egyenértékű oktatóanyagot).](manage-with-cli2.md)
>
> Ez a cikk az Azure PowerShell-utasításokat a diagnosztikai naplózás frissítéséhez. A diagnosztikai naplózás t is frissítheti az Azure Monitor használatával az Azure Portal **diagnosztikai naplók** szakaszában. 
>

A Key Vaultról a [Mi az Azure Key Vault?](overview.md)című témakörben olvashat. Arról, hogy hol érhető el a Key Vault, tekintse meg az [árképzési lapot.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy meglévő kulcstároló.  
* Az Azure PowerShell, az 1.0.0 minimális verziója. Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat. Ha már telepítette az Azure PowerShellt, és nem ismeri a `$PSVersionTable.PSVersion`verziót, az Azure PowerShell konzolon írja be a .  
* A Key Vault naplóihoz elegendő tárhely az Azure-ban.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Csatlakozás a kulcstartó-előfizetéshez

A kulcsnaplózás beállításának első lépése az Azure PowerShell rámutatása a naplózni kívánt kulcstartóhoz.

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába a következő paranccsal:  

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell leteszi a fiókhoz társított összes előfizetést. Alapértelmezés szerint a PowerShell az elsőt használja.

Előfordulhat, hogy meg kell adnia a key vault létrehozásához használt előfizetést. Írja be a következő parancsot a fiók előfizetései megtekintéséhez:

```powershell
Get-AzSubscription
```

Ezután adja meg a naplózni kívánt kulcstartóhoz társított előfizetést, írja be a következőt:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

A PowerShell megfelelő előfizetésre mutatása fontos lépés, különösen akkor, ha több előfizetés van társítva a fiókjához. További információ az Azure PowerShell konfigurálásáról: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Tárfiók létrehozása a naplókhoz

Bár a naplókhoz használhat egy meglévő tárfiókot, létrehozunk egy tárfiókot, amely a Key Vault-naplókhoz lesz hozzábontva. A kényelem, amikor meg kell adni ezt később, akkor tárolja a részleteket egy változó nevű **sa**.

A felügyelet további megkönnyítése érdekében ugyanazt az erőforráscsoportot is használjuk, mint a key vaultot tartalmazó. Az [első lépések oktatóanyagból](../secrets/quick-create-cli.md)ez az erőforráscsoport neve **ContosoResourceGroup,** és továbbra is a kelet-ázsiai helyet fogjuk használni. Cserélje le ezeket az értékeket a saját, adott esetben:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Ha úgy dönt, hogy egy meglévő tárfiókot használ, akkor ugyanazt az előfizetést kell használnia, mint a key vault. És az Azure Resource Manager üzembe helyezési modelljét kell használnia, nem pedig a klasszikus üzembe helyezési modellt.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>A naplók kulcstárolójának azonosítása

Az [első lépések oktatóanyagában](../secrets/quick-create-cli.md)a key vault neve **ContosoKeyVault**volt. Továbbra is ezt a nevet fogjuk használni, és a részleteket egy kv nevű változóban **tároljuk:**

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging"></a><a id="enable"></a>Naplózás engedélyezése

A Key Vault naplózásának engedélyezéséhez a **Set-AzDiagnosticSetting** parancsmast használjuk, az új tárfiókhoz és a key vaulthoz létrehozott változókkal együtt. Az **-Enabled** jelzőt is **$true,** és a kategóriát AuditEvent értékre **állítjuk** (a Key Vault naplózásának egyetlen kategóriája):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A kimenet így néz ki:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Ez a kimenet megerősíti, hogy a naplózás most engedélyezve van a key vault, és ez menti az adatokat a tárfiókba.

Szükség esetén beállíthatja a naplók adatmegőrzési házirendet, hogy a régebbi naplók automatikusan törlődnek. Állítsa be például az adatmegőrzési házirendet úgy, hogy a **-RetentionEnabled** jelzőt **$true**értékre állítja, és állítsa a **-RetentionInDays** paramétert **90-re,** hogy a 90 napnál régebbi naplók automatikusan törlődjenek.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Mi kerül naplózásra?

* Minden hitelesített REST API-kérelem, beleértve a hozzáférési engedélyek, rendszerhibák vagy rossz kérelmek miatt sikertelen kérelmeket.
* A key vault on maga, beleértve a létrehozása, törlése, key vault hozzáférési szabályzatok beállítása és a key vault attribútumok frissítése, például a címkék.
* A kulcstartóban lévő kulcsokés titkos kulcsok műveletei, többek között a következők:
  * Kulcsok vagy titkos kulcsok létrehozása, módosítása vagy törlése.
  * Kulcsok aláírása, ellenőrzése, titkosítása, visszafejtése, csomagolása és kicsomagolása, titkos kulcsok beolvasása, valamint kulcsok és titkos kulcsok (és azok verziói) listázása.
* A 401-es választ eredményező, nem hitelesített kérelmek. Példák olyan kérések, amelyek nem rendelkeznek tulajdonosi jogkivonatot, amelyek hibásan formázottak vagy lejárt, vagy amelyek érvénytelen jogkivonatot tartalmaznak.  

## <a name="access-your-logs"></a><a id="access"></a>A naplók elérése

Key Vault naplók a **insights-logs-auditevent** tárolóban a megadott tárfiókban tárolja. A naplók megtekintéséhez le kell töltenie a blobokat.

Először hozzon létre egy változót a tároló nevéhez. Ezt a változót fogja használni a forgatókönyv többi részében.

```powershell
$container = 'insights-logs-auditevent'
```

A tárolóban lévő összes blob listázásához írja be a következőt:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A kimeneti ehhez hasonlóan néz ki:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Amint az ebből a kimenetből is látható, a blobok egy elnevezési konvenciót követnek:`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Mivel ugyanazt a tárfiókot több erőforrás naplóinak gyűjtésére is használhatja, a blob névben lévő teljes erőforrás-azonosító hasznos lehet csak a szükséges blobok eléréséhez vagy letöltéséhez. Előtte azonban nézzük meg, hogyan tölthető le az összes blob.

Hozzon létre egy mappát a blobok letöltéséhez. Például:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Majd kérje le az összes blob listáját:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

A lista leépülése a **Get-AzStorageBlobContent szolgáltatáson** keresztül a blobok letöltéséhez a célmappába:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

A második parancs futtatásakor a **/** blobnevekben lévő határoló teljes mappastruktúrát hoz létre a célmappa alatt. Ezt a struktúrát fogja használni a blobok fájlokként való letöltéséhez és tárolásához.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Például:

* Ha több kulcstárolóval rendelkezik, de csak a CONTOSOKEYVAULT3 nevűhöz szeretne naplókat letölteni:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Ha több erőforráscsoporttal rendelkezik, de csak egyhez szeretne naplókat letölteni, használja a `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` parancsot:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Ha 2019 januárjának összes naplóját le szeretné tölteni, használja a következőket: `-Blob '*/year=2019/m=01/*'`

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Most már készen áll a naplók tartalmának megtekintésére. De mielőtt továbblépnénk, tudnod kell még két parancsot:

* A kulcstároló erőforrásához tartozó diagnosztikai beállítások állapotának lekérdezése:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* A kulcstároló erőforrása naplózásának letiltása:`Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>A Key Vault naplóinak értelmezése

Az egyes blobok JSON-blobként, szöveges formában vannak tárolva. Nézzünk meg egy példa naplóbejegyzést. Futtassa ezt a parancsot:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Ez a következőhöz hasonló naplóbejegyzést ad vissza:

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

Az alábbi táblázat a mezőneveket és leírásokat sorolja fel:

| Mező neve | Leírás |
| --- | --- |
| **Idő** |Dátum és idő UTC-ben. |
| **resourceId** |Az Azure Resource Manager erőforrásazonosítója. A Key Vault-naplók esetében ez mindig a Key Vault erőforrás-azonosítója. |
| **operationName** |A művelet neve, ahogy a következő táblázat is mutatja. |
| **operationVersion** |REST API-verzió az ügyfél által kért. |
| **Kategória** |Az eredmény típusa. A Key Vault-naplók esetében az **AuditEvent** az egyetlen, elérhető érték. |
| **resultType** |A REST API-kérelem eredménye. |
| **resultSignature** |A HTTP-állapot. |
| **resultDescription** |Az eredmény további leírása, amennyiben elérhető. |
| **durationMs** |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet. |
| **callerIpAddress** |A kérést küldő ügyfél IP-címe. |
| **korrelációazonosító** |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| **Identitás** |A REST API-kérelemben bemutatott jogkivonat identitása. Ez általában egy "felhasználó", egy "egyszerű szolgáltatás", vagy a kombináció "user+appId", mint egy Azure PowerShell-parancsmagból származó kérelem esetében. |
| **Tulajdonságok** |A művelettől függően változó információ (**operationName**). A legtöbb esetben ez a mező tartalmazza az ügyféladatait (az ügyfél által átadott felhasználói ügynök karakterláncot), a pontos REST API-kérelem URI-ját és a HTTP állapotkódot. Emellett ha egy objektum ot ad vissza egy kérés eredményeként (például **KeyCreate** vagy **VaultGet),** ez is tartalmazza a kulcs URI (id"), tároló URI vagy titkos URI. |

A **OperationName** mező értékek *ObjectVerb* formátumúak. Például:

* Minden key vault művelet `Vault<action>` formátuma, `VaultGet` `VaultCreate`például és .
* Minden kulcsfontosságú művelet `Key<action>` formátuma a `KeySign` `KeyList`( és a .
* Minden titkos művelet `Secret<action>` formátuma a `SecretGet` `SecretListVersions`( és a .

Az alábbi táblázat az **OperationName** értékeket és a megfelelő REST API-parancsokat sorolja fel:

| operationName | REST API parancs |
| --- | --- |
| **Hitelesítés** |Hitelesítés az Azure Active Directory-végponton keresztül |
| **VaultGet** |[Kulcstároló adatainak lekérése](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Kulcstároló létrehozása vagy frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Kulcstároló törlése](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Kulcstároló frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Az erőforráscsoport összes kulcstárolójának listázása](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Kulcs adatainak lekérése](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Kulcs importálása egy tárolóba](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Kulcs biztonsági és biztonsági másolatot](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
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
| **SecretGet** |[Kap egy titkot](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Titkos kulcs frissítése](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Titkos kulcs törlése](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Egy tároló titkos kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Titkos kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Az Azure Monitor-naplók használata

Használhatja a Key Vault-megoldás az Azure Monitor naplók a Key Vault **AuditEvent** naplók áttekintéséhez. Az Azure Monitor naplóiban naplólekérdezések használatával elemezheti az adatokat, és lekéri a szükséges információkat. 

További információért, többek között ennek beállításáról az [Azure Key Vault-megoldás az Azure Monitor naplóiban című témakörben talál.](../../azure-monitor/insights/azure-key-vault.md) Ez a cikk is tartalmaz utasításokat, ha át kell telepítenie a régi Key Vault-megoldás, amely az Azure Monitor naplók előzetes verzió, ahol először irányította a naplókat egy Azure-tárfiókot, és konfigurált Azure Monitor naplók olvasni onnan.

## <a name="next-steps"></a><a id="next"></a>További lépések

Az Azure Key Vault ot egy .NET webalkalmazásban használó oktatóanyagról az [Azure Key Vault használata webalkalmazásból.](tutorial-net-create-vault-azure-web-app.md)

Programozási hivatkozások: [Azure Key Vault developer’s guide](developers-guide.md) (Az Azure Key Vault fejlesztői útmutatója).

Az Azure Key Vault Azure PowerShell 1.0-s parancsmagjainak listáját az [Azure Key Vault parancsmagjai ban.](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

A kulcsrotációról és az Azure Key Vaulttal való naplózásról szóló oktatóanyagról a [Key Vault beállítása végpontok között a kulcsrotációval és a naplózással.](../secrets/key-rotation-log-monitoring.md)
