<properties
    pageTitle="Az Azure Key Vault naplózása | Microsoft Azure"
    description="Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/31/2016"
    ms.author="cabailey"/>


# Az Azure Key Vault naplózása #
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

## Bevezetés  
Egy vagy több kulcstároló létrehozása után célszerű figyelni, hogy hogyan, mikor és kik férnek hozzá a kulcstárolókhoz. Ehhez engedélyezze a Key Vault naplózását, amely egy Ön által megadott Azure-tárfiókba menti az adatokat. A megadott tárfiókhoz automatikusan létrehozunk egy **insights-logs-auditevent** nevű tárolót, amelyet több kulcstároló naplófájljainak tárolására is használhat.

A kulcstároló műveletei után legfeljebb 10 perccel már elérheti a naplóinformációkat. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A tárfiók naplófájljait Önnek kell kezelnie:

- Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
- Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával, a tárfiók létrehozásával, a naplózás engedélyezésével és az összegyűjtött naplóinformációk értelmezésével.  


>[AZURE.NOTE]  Az oktatóanyag nem tartalmazza a kulcstárolók, a kulcsok és titkos kulcsok létrehozásának lépéseit. Ezekről a [Get started with Azure Key Vault](key-vault-get-started.md) (Bevezetés az Azure Key Vault használatába) című cikkben találhat információt. A platformfüggetlen parancssori felületre vonatkozó utasításokat megtekintheti [ebben a megfelelő oktatóanyagban](key-vault-manage-with-cli.md).
>
>Az Azure Key Vault jelenleg nem konfigurálható az Azure-portálon. Ehelyett használja az itt szereplő Azure PowerShell-utasításokat.

Az összegyűjtött naplók az Operations Management Suite csomag Log Analytics eszközével jeleníthetők meg. További információ: [Azure Key Vault (Preview) solution in Log Analytics](../log-analytics/log-analytics-azure-key-vault.md) (Az Azure Key Vault (előzetes verzió) megoldás a Log Analyticsben).

Áttekintést az Azure Key Vaultról a [What is Azure Key Vault?](key-vault-whatis.md) (Mi az Azure Key Vault?) című cikkben talál.

## Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Egy meglévő kulcstároló.  
- Az Azure PowerShell **legalább 1.0.1-es verziója**. Az Azure PowerShell telepítéséről és az Azure-előfizetéssel való társításáról további információt a [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat. Ha már telepítette az Azure PowerShellt, de nem tudja, melyik verziót, írja be az Azure PowerShell-konzolon az alábbi parancsot: `(Get-Module azure -ListAvailable).Version`.  
- A Key Vault naplóihoz elegendő tárhely az Azure-ban.


## <a id="connect"></a>Csatlakozás az előfizetésekhez ##

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

    Login-AzureRmAccount

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes olyan előfizetést, amely ehhez a fiókhoz van rendelve, és alapértelmezés szerint kiválasztja az elsőt.

Ha több előfizetése van, előfordulhat, hogy meg kell adnia azt, amelyikkel az Azure Key Vault tárolóját létrehozta. Írja be az alábbi parancsot a fiókhoz tartozó előfizetések megjelenítéséhez:

    Get-AzureRmSubscription

Ezt követően a naplózandó kulcstárolóhoz tartozó előfizetés megadásához írja be a következő parancsot:

    Set-AzureRmContext -SubscriptionId <subscription ID>

További információ az Azure PowerShell konfigurálásáról: [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása).


## <a id="storage"></a>Új tárfiók létrehozása a naplóknak ##

Bár meglévő tárfiókot is használhat a naplózáshoz, mi létrehozunk egy újat, amely kimondottan a Key Vault naplóinak tárolására szolgál majd. Ezt később kell csak megadni, így addig a vonatkozó részleteket egy **sa** nevű változóban tároljuk.

Az egyszerű használat érdekében ugyanazt az erőforráscsoportot használjuk, amelyben a kulcstároló is található. Az [oktatóanyagból](key-vault-get-started.md) kiindulva az erőforráscsoport neve **ContosoResourceGroup** lesz, a helye pedig továbbra is Kelet-Ázsia. Az alábbi értékeket helyettesítse a sajátjainak megfelelőkkel:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Ha egy meglévő tárfiók használata mellett dönt, azt ugyanazzal az előfizetéssel kell használnia, mint a kulcstárolót, és a Resource Manager-alapú üzemi modellt kell alkalmaznia, nem a klasszikust.

## <a id="identify"></a>A naplók kulcstárolójának azonosítása ##

Az oktatóanyagban a kulcstároló neve **ContosoKeyVault** volt, így a továbbiakban is azt használjuk, a részleteket pedig egy **kv** nevű változóban tároljuk:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Naplózás engedélyezése ##

A Key Vault naplózását a Set-AzureRmDiagnosticSetting parancsmaggal és az újonnan létrehozott tárfiók és kulcstároló változóival engedélyezheti. Az **-Enabled** jelzőt emellett **$true**, a kategóriát pedig AuditEvent (a Key Vault naplózásának egyetlen kategóriája) értékre állítjuk:


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

Ennek a kimenete az alábbiakat tartalmazza:

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

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Mi kerül naplózásra?

- Minden hitelesített REST API-kérés naplózásra kerül, beleértve a hozzáférési engedélyekből, rendszerhibákból vagy hibás kérésekből adótó sikertelen kérelmeket is.
- A magán a kulcstárolón elvégzett műveletek, beleértve a létrehozást, az eltávolítást, a kulcstároló hozzáférési szabályzatainak beállítását és a kulcstároló tulajdonságainak (például címkéinek) frissítését.
- A kulcstároló kulcsain és titkos kulcsain elvégzett műveletek, beleértve a létrehozást, a módosítást és a kulcsok vagy titkos kulcsok törlését; az olyan műveletek, mint az aláírás, az ellenőrzés, a titkosítás, a visszafejtés, a kulcsok be- és kicsomagolása, a titkos kulcsok lekérése, valamint a kulcsok és titkos kulcsok és azok verzióinak listázása.
- A 401-es választ eredményező, nem hitelesített kérelmek. Ilyenek például azok a kérelmek, amelyek nem rendelkeznek tulajdonosi jogkivonattal, helytelen formátumúak vagy lejártak, vagy érvénytelen a jogkivonatuk.  


## <a id="access"></a>A naplók elérése ##

A kulcstároló naplóit a rendszer a megadott tárfiók **insights-logs-auditevent** nevű tárolójában tárolja. A tároló összes blobjának megjelenítéséhez írja be az alábbi parancsot:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

A kimenet ehhez hasonló lesz:

**Tároló URI-ja: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Név**

**----**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****


Ahogyan a kimenetből látható, a blobok az alábbi elnevezési módszert alkalmazzák: **erőforrás-azonosító=<ARM resource ID>/é=<year>/h=<month>/n=<day of month>/ó=<hour>/p=<minute>/fájlnév.json**

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Mivel ugyanazt a tárfiókot több erőforrásból gyűjtött naplók tárolására is használhatja, a blob nevének teljes erőforrás-azonosítója segít abban, hogy csak a szükséges blobokat töltse le, illetve csak azokhoz férjen hozzá. Előtte azonban nézzük meg, hogyan tölthető le az összes blob.

Elsőként hozzon létre egy mappát, amelybe letölti a blobokat. Példa:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Majd kérje le az összes blob listáját:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

A listát a „Get-AzureStorageBlobContent” paranccsal töltse le a mappába:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

A második parancs futtatásakor a blob nevének **/** elválasztója egy teljes mapparendszert létrehoz a célmappában, és a program ebben a rendszerben tárolja majd fájlokként a letöltött blobokat.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Példa:

- Ha több kulcstárolóval rendelkezik, de csak a CONTOSOKEYVAULT3 nevűhöz szeretne naplókat letölteni:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Ha több erőforráscsoporttal rendelkezik, de csak egyhez szeretne naplókat letölteni, használja a `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` parancsot:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Ha 2016 januárjának összes naplóját szeretné letölteni, használja a `-Blob '*/year=2016/m=01/*'` parancsot:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Most már készen áll a naplók tartalmának megtekintésére. Mielőtt azonban belekezdenénk, érdemes feljegyeznie ezt a két paramétert a Get-AzureRmDiagnosticSetting parancshoz:

- A kulcstároló erőforrásához tartozó diagnosztikai beállítások állapotának lekérése: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- A kulcstároló erőforrása naplózásának letiltása: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>A Key Vault naplóinak értelmezése ##

Az egyes blobok JSON-blobként, szöveges formában vannak tárolva. Íme egy példa a `Get-AzureRmKeyVault -VaultName 'contosokeyvault'` parancsot futtató naplóbejegyzésre:

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


Az alábbi táblázat a mezők neveit és leírásait sorolja fel.


| Mező neve        | Leírás |
| ------------- |-------------|
| time      | Dátum és idő (UTC).|
| resourceId      | Az Azure Resource Manager szerinti erőforrás-azonosító. A Key Vault-naplók esetében mindig ez a Key Vault erőforrás-azonosítója.|
| operationName      | A művelet neve, ahogy a következő táblázat is mutatja.|
| operationVersion      | Az ügyfél által kért REST API verziója.|
| category      | A Key Vault naplóihoz az AuditEvent az egyetlen elérhető érték.|
| resultType      | A REST API-kérelem eredménye.|
| resultSignature      | A HTTP-állapot.|
| resultDescription     | Az eredmény további leírása, amennyiben elérhető.|
| durationMs      | A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet.|
| callerIpAddress      | A kérelmet leadó ügyfél IP-címe.|
| correlationId      | Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez.|
| identity      | A REST API-kérelemhez megadott tokenben szereplő identitás. Ez általában egy „felhasználó”, „egyszerű szolgáltatásnév” vagy „felhasználó + alkalmazás-azonosító”, az Azure PowerShell-parancsmagok által eredményezett kérelmekhez hasonlóan.|
| properties      | Ez a mező a művelettől (operationName) függően más-más adatokat tartalmaz. A legtöbb esetben ügyféladatokat (az ügyfél által használt felhasználói ügynök sztringjét), a REST API-kérelem pontos URI-ját és a HTTP-állapot kódját tartalmazza. Ezenkívül ha egy objektumot a rendszer egy kérelem (például a KeyCreate vagy a VaultGet) eredményeként ad vissza, a kulcs URI-ját („id”), a tároló URI-ját vagy a titkos kulcs URI-ját is tartalmazza.|




Az **operationName** mező értékei ObjectVerb formátumúak. Példa:

- Minden kulcstárolón elvégzett művelet „Vault`<action>`” formátumú, például `VaultGet` és `VaultCreate`.

- Minden kulcson elvégzett művelet „Key`<action>`” formátumú, például `KeySign` és `KeyList`.

- Minden titkos kulcson elvégzett művelet „Secret`<action>`” formátumú, például `SecretGet` és `SecretListVersions`.

Az alábbi táblázat az operationName műveleteket és a megfelelő REST API-parancsokat listázza.

| operationName        | REST API-parancs |
| ------------- |-------------|
| Authentication      | Az Azure Active Directory végpontján keresztül|
| VaultGet      | [Kulcstároló adatainak lekérése](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Kulcstároló létrehozása vagy frissítése](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Kulcstároló törlése](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Kulcstároló frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Az erőforráscsoport összes kulcstárolójának listázása](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Kulcs létrehozása](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Kulcs adatainak lekérése](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Kulcs importálása egy tárolóba](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Kulcs biztonsági mentése](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Kulcs törlése](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Kulcs helyreállítása](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Aláírás kulccsal](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Ellenőrzés kulccsal](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Kulcs becsomagolása](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Kulcs kicsomagolása](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Titkosítás kulccsal](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Visszafejtés kulccsal](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Kulcs frissítése](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [Egy tároló kulcsainak listázása](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [Kulcs verzióinak listázása](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Titkos kulcs létrehozása](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Titkos kulcs lekérése](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Titkos kulcs frissítése](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Titkos kulcs törlése](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Egy tároló titkos kulcsainak listázása](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Titkos kulcs verzióinak listázása](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Következő lépések ##

Az Azure Key Vault webalkalmazásban való használatáról a [Use Azure Key Vault from a Web Application](key-vault-use-from-web-application.md) (Az Azure Key Vault webalkalmazással való használata) című témakörben találhat útmutatást.

Programozási hivatkozások: [Azure Key Vault developer’s guide](key-vault-developers-guide.md) (Az Azure Key Vault fejlesztői útmutatója).

Az Azure Key Vaultra vonatkozó Azure PowerShell 1.0-parancsmagok listáját az [Azure Key Vault Cmdlets](https://msdn.microsoft.com/library/azure/dn868052.aspx) (Az Azure Key Vault parancsmagjai) című témakörben találja.

A kulcsrotálással és a naplózással kapcsolatos oktatóanyag: [How to setup Key Vault with end to end key rotation and auditing](key-vault-key-rotation-log-monitoring.md) (A Key Vault beállítása átfogó kulcsrotálással és naplózással).



<!--HONumber=Sep16_HO4-->


