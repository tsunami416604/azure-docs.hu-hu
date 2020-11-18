---
title: Azure Monitor – ügyfél által kezelt kulcs
description: Az Log Analytics-munkaterületek Azure Key Vault kulcs használatával történő titkosításához szükséges információk és lépések Customer-Managed kulcs konfigurálásához.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/18/2020
ms.openlocfilehash: 7bfd951d7cec27e0b8264aaabf9bc3a17875256a
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873522"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor – ügyfél által kezelt kulcs 

Ez a cikk háttér-információkat és lépéseket tartalmaz az ügyfél által felügyelt kulcsok Log Analytics-munkaterületekhez való konfigurálásához. A konfigurálást követően a munkaterületekre eljuttatott összes adatfájl titkosítva van a Azure Key Vault kulccsal.

Javasoljuk, hogy a konfiguráció előtt tekintse át [az alábbi korlátozásokat és korlátozásokat](#limitationsandconstraints) .

## <a name="customer-managed-key-overview"></a>Ügyfél által felügyelt kulcs áttekintése

A inaktív adatok [titkosítása](../../security/fundamentals/encryption-atrest.md) a szervezetek közös adatvédelmi és biztonsági követelménye. Lehetővé teheti, hogy az Azure teljes mértékben kezelhesse a titkosítást, míg számos különböző lehetőség áll rendelkezésre a titkosítási és titkosítási kulcsok szoros kezeléséhez.

Azure Monitor biztosítja, hogy az összes adatok és mentett lekérdezések a Microsoft által felügyelt kulcsok (MMK-EK) használatával titkosítva legyenek. A Azure Monitor a saját [Azure Key Vault](../../key-vault/general/overview.md) tárolt saját kulcs használatával is lehetővé teszi a titkosítást, és lehetővé teszi a vezérlő számára, hogy bármikor visszavonja az adataihoz való hozzáférést. Azure Monitor a titkosítás használata azonos az [Azure Storage-titkosítás](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) működésének módjával.

Customer-Managed kulcsot olyan dedikált Log Analytics-fürtökön továbbítják, amelyek magasabb szintű védelmi szintet és irányítást biztosítanak. A dedikált fürtökbe betöltött adat kétszer, a Microsoft által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal, illetve az infrastruktúra szintjén egyszer, két különböző titkosítási algoritmus és két különböző kulcs használatával titkosítva van. A [kettős titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) védelmet biztosít olyan esetekben, amikor a titkosítási algoritmusok vagy kulcsok egyike sérül. Ebben az esetben a további titkosítási réteg továbbra is védi az adatait. A dedikált fürt lehetővé teszi, hogy az adatai a [Kulcstároló](#customer-lockbox-preview) -vezérlővel is védve legyenek.

Az elmúlt 14 napban betöltött adatok a hatékony lekérdezési motor működéséhez a gyors gyorsítótárban (SSD-alapú) is megmaradnak. Ezek az [adat a Microsoft](#key-revocation)kulcsaival is titkosítva van, függetlenül az ügyfél által felügyelt kulcs konfigurációjától Dolgozunk arra, hogy a 2021-es első felében Customer-Managed kulccsal titkosított SSD-adatmennyiséget lehessen titkosítani.

A [log Analytics-fürtök díjszabási modellje](./manage-cost-storage.md#log-analytics-dedicated-clusters) a 1000 GB/nap szinten kezdődő kapacitási foglalásokat használja.

> [!IMPORTANT]
> Az ideiglenes kapacitás megkötése miatt a fürt létrehozása előtt előzetes regisztrációra van szükség. A névjegyek a Microsofthoz, vagy a támogatási kérelem megnyitása a feliratkozási azonosítók regisztrálásához.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Customer-Managed kulcs működése Azure Monitor

Azure Monitor a rendszer által hozzárendelt felügyelt identitást használja a Azure Key Vault elérésének biztosításához. A rendszer által hozzárendelt felügyelt identitás csak egyetlen Azure-erőforráshoz társítható, míg a Log Analytics-fürt identitása a fürt szintjén támogatott – ez azt diktálja, hogy a képesség egy dedikált Log Analytics-fürtön lesz továbbítva. Ha több munkaterületen szeretné támogatni Customer-Managed kulcsot, egy új Log Analytics *fürterőforrás* közbenső identitás-kapcsolatként működik a Key Vault és a log Analytics munkaterületek között. A Log Analytics fürt tárterülete a fürt erőforrásához társított felügyelt identitást használja a \' Azure Key Vault Azure Active Directory használatával történő hitelesítéséhez. *Cluster* 

A konfigurálás után a dedikált fürthöz társított munkaterületekre betöltött összes adat titkosítva lesz a kulcsával Key Vaultban. A munkaterületeket bármikor leválaszthatja a fürtből. A rendszer betölti az új adatait, és a Microsoft-kulccsal titkosítja Log Analytics a tárterületet, míg az új és a régi adatait zökkenőmentesen kérdezheti le.


![Customer-Managed kulcs áttekintése](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics a Key Vault számára engedélyekkel rendelkező, felügyelt identitással rendelkező *fürterőforrás* – az identitást a dedikált log Analytics fürt tárterületére propagálja a rendszer.
3. Dedikált Log Analytics-fürt
4. *Fürterőforrás* -kapcsolattal társított munkaterületek 

## <a name="encryption-keys-operation"></a>Titkosítási kulcsok művelete

A Storage adattitkosítása 3 típusú kulcsot vesz fel:

- **KEK** – kulcs titkosítási kulcsa (Customer-Managed kulcs)
- **AEK** – fiók titkosítási kulcsa
- **Adattitkosítási kulcsot** – adattitkosítási kulcs

A következő szabályok érvényesek:

- A Log Analytics fürt Storage-fiókjai egyedi titkosítási kulcsot hoznak az összes Storage-fiókhoz, amely a "AEK" néven ismert.
- A AEK a DEKs származtatása céljából használható, amelyek a lemezre írt adatblokkok titkosításához használt kulcsok.
- Ha a kulcsot Key Vaultban konfigurálja, és a fürtben hivatkozik rá, az Azure Storage kérelmeket küld a Azure Key Vaultnak a AEK becsomagolásához és az adattitkosítási és visszafejtési műveletek elvégzéséhez.
- A KEK soha nem hagyja el a Key Vault, és HSM-kulcsok esetén soha nem hagyja el a hardvert.
- Az Azure Storage a *fürterőforrás* -hez társított felügyelt identitást használja a Azure Key Vault hitelesítésére és elérésére Azure Active Directory használatával.

## <a name="customer-managed-key-provisioning-procedure"></a>Customer-Managed kulcs kiépítési eljárása

1. Előfizetés regisztrálása a fürt létrehozásának engedélyezéséhez
1. Azure Key Vault létrehozása és a kulcs tárolása
1. Fürt létrehozása
1. Engedélyek megadása a Key Vault számára
1. Log Analytics-munkaterületek összekapcsolása

Customer-Managed kulcs konfigurálása nem támogatott a Azure Portal és a kiépítés a [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/), a [CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics) vagy a [Rest](https://docs.microsoft.com/rest/api/loganalytics/) -kérelmek használatával történik.

### <a name="asynchronous-operations-and-status-check"></a>Aszinkron műveletek és állapot-ellenőrzések

Néhány konfigurációs lépés aszinkron módon fut, mert nem hajthatók végre gyorsan. A REST használatakor a válasz kezdetben egy 200-as HTTP-állapotkódot (OK) és egy, az *Azure-AsyncOperation* tulajdonsággal rendelkező fejlécet ad vissza, ha elfogadják:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Az aszinkron művelet állapotát úgy tekintheti meg, hogy egy GET kérelmet küld az *Azure-AsyncOperation* fejléc értékére:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

A `status` válaszban a következők egyike lehet: "Inprogress", "frissítés", "Törlés", "sikeres" vagy "sikertelen", beleértve a hibakódot.

### <a name="allowing-subscription"></a>Előfizetés engedélyezése

> [!IMPORTANT]
> Customer-Managed kulcsfontosságú képesség a regionális. A Azure Key Vault, a fürtnek és a csatolt Log Analytics-munkaterületnek ugyanabban a régióban kell lennie, de különböző előfizetésekben lehet.

### <a name="storing-encryption-key-kek"></a>Titkosítási kulcs (KEK) tárolása

Hozzon létre vagy használjon olyan Azure Key Vault, amelyet már létre kell hoznia, vagy importálnia kell az adattitkosításhoz használni kívánt kulcsot. A Azure Key Vaultt helyreállítható kell konfigurálni, hogy megvédje a kulcsot és az adataihoz való hozzáférést Azure monitorban. Ezt a konfigurációt a Key Vault tulajdonságok területén ellenőrizheti, ha engedélyezni szeretné a *Soft delete* és a *Purge Protection* szolgáltatást.

![A védelem törlésére és eltávolítására vonatkozó védelmi beállítások](media/customer-managed-keys/soft-purge-protection.png)

Ezek a beállítások a CLI-n és a PowerShellen keresztül Key Vault frissíthetők:

- [Helyreállítható törlés](../../key-vault/general/soft-delete-overview.md)
- A [védelem kiürítése](../../key-vault/general/soft-delete-overview.md#purge-protection) a titkos vagy a tár kényszerített törlésével, a törlést követően is

### <a name="create-cluster"></a>Fürt létrehozása

Kövesse a [dedikált fürtök című cikkben](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster)bemutatott eljárást. 

> [!IMPORTANT]
> Másolja ki és mentse a választ, mivel a következő lépésekben szükség lesz a részletekre.

### <a name="grant-key-vault-permissions"></a>Key Vault engedélyek megadása

Hozzáférési szabályzat létrehozása Key Vaultban, hogy engedélyeket biztosítson a fürtnek. Ezeket az engedélyeket az alátét Azure Monitor tárolója használja az adattitkosításhoz. Nyissa meg a Key Vault a Azure Portalban, és kattintson a "hozzáférési szabályzatok", majd a "hozzáférési házirend hozzáadása" lehetőségre a szabályzat létrehozásához a következő beállításokkal:

- Kulcs engedélyei: válassza a beolvasás, a betakart kulcs és a kicsomagolási kulcs engedélyeket.
- Rendszerbiztonsági tag kiválasztása: adja meg az előző lépésben a válaszban visszaadott fürt nevét vagy a rendszerbiztonsági azonosító értékét.

![Key Vault engedélyek megadása](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A *Get* engedély szükséges annak ellenőrzéséhez, hogy a Key Vault helyreállítható-e a kulcs védelme érdekében, valamint a Azure monitor adataihoz való hozzáféréshez.

### <a name="update-cluster-with-key-identifier-details"></a>Fürt frissítése a kulcs-azonosító részleteivel

A fürtön lévő összes művelethez a Microsoft. OperationalInsights/fürtök/írási művelet engedély szükséges. Ez az engedély a/Write műveletet tartalmazó tulajdonos vagy közreműködő vagy a *Microsoft. OperationalInsights/műveletet tartalmazó log Analytics közreműködő szerepkörön* keresztül adható meg.

Ez a lépés frissíti Azure Monitor tárterületet az adattitkosításhoz használandó kulccsal és verzióval. A frissítés során az új kulcs a Storage-kulcs (AEK) becsomagolására és kicsomagolására szolgál.

Válassza ki a kulcs aktuális verzióját Azure Key Vault a kulcs azonosítójának részleteinek beszerzéséhez.

![Key Vault engedélyek megadása](media/customer-managed-keys/key-identifier-8bit.png)

Frissítse a KeyVaultProperties a fürtben a kulcs azonosítójának részleteivel.

A művelet aszinkron, és hosszabb időt is igénybe vehet.

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name"?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Válasz**

A kulcs azonosítójának elvégzése néhány percet vesz igénybe. A frissítési állapotot kétféleképpen is megtekintheti:
1. Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
2. Küldjön egy GET kérelmet a fürtre, és tekintse meg a *KeyVaultProperties* tulajdonságait. A válaszban a legutóbb frissített kulcs-azonosító részleteit kell visszaadnia.

A kérésre adott válasznak a következőhöz hasonlóan kell kinéznie, amikor a kulcs azonosítójának frissítése befejeződött: 200 OK és fejléc
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="link-workspace-to-cluster"></a>Munkaterület csatolása a fürthöz

A művelet végrehajtásához "írási" engedélyekkel kell rendelkeznie a munkaterület és a fürt számára, beleértve a következő műveleteket:

- A munkaterületen: Microsoft. OperationalInsights/munkaterületek/írás
- A fürtben: Microsoft. OperationalInsights/fürtök/írás

> [!IMPORTANT]
> Ezt a lépést csak a Log Analytics-fürt üzembe helyezésének befejezése után kell végrehajtani. Ha a munkaterületeket a kiépítés előtt rendeli hozzá, és az adatot betölti, a rendszer elveti a betöltött adatmennyiséget, és nem lesz helyreállítható.

Ez a művelet aszinkron, és egy darabig elvégezhető.

Kövesse a [dedikált fürtök című cikkben](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#link-a-workspace-to-the-cluster)bemutatott eljárást.

## <a name="key-revocation"></a>Kulcs visszavonása

Az adataihoz való hozzáférés visszavonásához tiltsa le a kulcsot, vagy törölje a fürt hozzáférési házirendjét a Key Vault. A Log Analytics fürt tárterülete mindig egy órán belül megtartja a kulcsfontosságú engedélyek változásait, a tárterület pedig elérhetetlenné válik. A fürthöz kapcsolódó munkaterületekre betöltött összes új adat eldobásra kerül, és nem állítható vissza, az adat nem érhető el, és a munkaterületekre irányuló lekérdezések sikertelenek lesznek. A korábban betöltött adatmennyiség mindaddig a tárolóban marad, amíg a fürt és a munkaterületek nem törlődnek. A nem elérhető adatokra az adatmegőrzési szabályzat vonatkozik, és a rendszer törli az adatmegőrzési időtartamot. 

Az elmúlt 14 napban betöltött adatok gyors gyorsítótárban (SSD-vel) is megmaradnak a hatékony lekérdezési motor működéséhez. Ez törölve lesz a kulcs-visszavonási művelet során, és elérhetetlenné válik.

A Storage rendszeres időközönként lekérdezi a Key Vault a titkosítási kulcs kicsomagolásának és a hozzájuk való hozzáférésnek, valamint az adatfeldolgozás és a lekérdezés 30 percen belüli folytatásának megkísérlése érdekében.

## <a name="key-rotation"></a>Kulcsrotálás

Customer-Managed a kulcs elforgatásához explicit frissítést kell megadnia a fürthöz a Azure Key Vault új kulcsának verziójával. Kövesse a "fürt frissítése a kulcs-azonosító részleteivel" lépést. Ha nem frissíti az új kulcs-azonosító részleteit a fürtben, a Log Analytics fürt tárterülete továbbra is a korábbi titkosítási kulcsot használja. Ha letiltja vagy törli a régi kulcsot, mielőtt frissíti az új kulcsot a fürtben, a [visszavonási](#key-revocation) állapotba kerül.

Az összes adatai elérhetők maradnak a kulcs elforgatási művelete után, mivel az adatai mindig titkosítva vannak a fiók titkosítási kulcsával (AEK), míg a AEK mostantól titkosítva van az új kulcs titkosítási kulcs (KEK) verziójával Key Vaultban.

## <a name="customer-managed-key-for-queries"></a>Lekérdezések Customer-Managed kulcsa

A Log Analyticsben használt lekérdezési nyelv kifejező, és bizalmas információkat tartalmazhat a lekérdezésekben vagy a lekérdezési szintaxisban hozzáadott megjegyzésekben. Egyes szervezetek megkövetelik, hogy az ilyen információk védelme Customer-Managed kulcsfontosságú házirend keretében történjen, és a kulcsával titkosított lekérdezéseket kell mentenie. A Azure Monitor lehetővé teszi, hogy a munkaterülethez való csatlakozáskor a saját kulcsával titkosított *mentett kereséseket* és *napló-riasztásokat* tartalmazó lekérdezéseket tárolja. 

> [!NOTE]
> Log Analytics lekérdezések a használt forgatókönyvtől függően különböző áruházakban menthetők. A lekérdezések a következő helyzetekben titkosítva maradnak a Microsoft Key (MMK) szolgáltatással, függetlenül a Customer-Managed legfontosabb konfigurációtól: munkafüzetek Azure Monitor, Azure-irányítópultok, Azure Logic app, Azure Notebooks és Automation Runbookok.

Ha saját tárolót (BYOS) használ, és összekapcsolja azt a munkaterülettel, a szolgáltatás feltölti a *mentett-kereséseket* és a *log-riasztási* lekérdezéseket a Storage-fiókjába. Ez azt jelenti, hogy a Storage-fiókot és a [titkosítás-nyugalmi szabályzatot](../../storage/common/customer-managed-keys-overview.md) a log Analytics fürtben lévő adatok titkosításához használt kulcs vagy egy másik kulcs használatával szabályozhatja. A Storage-fiókkal kapcsolatos költségekért azonban felelősnek kell lennie. 

**A lekérdezések Customer-Managed kulcsának beállítása előtt megfontolandó szempontok**
* A munkaterület és a Storage-fiók "Write" engedélyekkel kell rendelkeznie
* Győződjön meg arról, hogy a Storage-fiókot ugyanabban a régióban hozza létre, mint ahol a Log Analytics munkaterület található.
* A tárolóban végzett *keresések* szolgáltatásbeli összetevőknek számítanak, és a formátumuk változhat
* A meglévő *mentett keresések* el lesznek távolítva a munkaterületről. A konfiguráció előtt másolja és mentse a szükséges *kereséseket* . A *mentett keresések* megtekinthetők a [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) használatával
* A lekérdezési előzmények nem támogatottak, és nem láthatja a futtatott lekérdezéseket
* A lekérdezések mentése céljából egyetlen Storage-fiókot kapcsolhat a munkaterülethez, de a *mentett keresések* és a *log-riasztások* lekérdezései között is használható.
* A rögzítés az irányítópulton nem támogatott

**BYOS konfigurálása mentett keresési lekérdezésekhez**

Storage-fiók összekapcsolása *a* munkaterülethez – a *mentett keresési* lekérdezések a Storage-fiókba lesznek mentve. 

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

A konfiguráció után a rendszer minden új *mentett keresési* lekérdezést ment a tárolóba.

**BYOS konfigurálása a log-riasztási lekérdezésekhez**

Storage-fiók csatolása a munkaterülethez tartozó *riasztásokhoz* – a *log-riasztási* lekérdezések mentése a Storage-fiókba történik. 

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

A konfiguráció után a rendszer minden új riasztási lekérdezést ment a tárolóba.

## <a name="customer-lockbox-preview"></a>Ügyfélszéf (előzetes verzió)
A kulcstároló segítségével engedélyezheti vagy elutasíthatja a Microsoft mérnök kérelmét, hogy támogatási kérelem során hozzáférjen az adataihoz.

A Azure Monitor a Log Analytics dedikált fürthöz társított munkaterületeken található adathoz irányítja ezt a vezérlőt. A kulcstároló-vezérlő a Log Analytics dedikált fürtben tárolt adatra vonatkozik, ahol a rendszer elkülönítetten tárolja a fürt Storage-fiókjaiban a kulcstároló által védett előfizetését.  

További információ a [Microsoft Azure Ügyfélszéfről](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed kulcsfontosságú műveletek

- **Erőforráscsoport összes fürtjének beolvasása**
  
  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Válasz**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- **Egy előfizetésben lévő összes fürt beolvasása**

  ```azurecli
  az monitor log-analytics cluster list
  ```

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Válasz**
    
  Ugyanaz a válasz, mint a "fürterőforrás fürtje", de az előfizetés hatókörében.

- ***Kapacitás foglalásának* frissítése a fürtben**

  Ha a csatolt munkaterületekhez tartozó adatmennyiség idővel módosul, és a kapacitás foglalási szintjét megfelelően szeretné frissíteni. Kövesse a [fürt frissítése](#update-cluster-with-key-identifier-details) című témakört, és adja meg az új kapacitás értékét. A 1000 és 3000 GB közötti tartományban, illetve a 100-as lépéseknél lehet. A napi 3000 GB-nál nagyobb szint esetén a Microsoft-kapcsolattartóval engedélyezheti. Vegye figyelembe, hogy nem kell megadnia a teljes REST-kérelem törzsét, de tartalmaznia kell az SKU-t:

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

- ***BillingType* frissítése a fürtben**

  A *billingType* tulajdonság határozza meg a fürt és a hozzá tartozó információk számlázási hozzárendelését:
  - *fürt* (alapértelmezett) – a számlázás a fürterőforrás üzemeltetéséhez használt előfizetéshez van hozzárendelve.
  - *munkaterületek* – a számlázás a munkaterületek arányosan üzemelő előfizetésekhez van hozzárendelve.
  
  Hajtsa végre a [frissítési fürtöt](#update-cluster-with-key-identifier-details) , és adja meg az új billingType értéket. Vegye figyelembe, hogy nem kell megadnia a teljes REST-kérelem törzsét, és tartalmaznia kell a *billingType*:

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Munkaterület leválasztása**

  A művelet végrehajtásához a munkaterületen és a fürtön a "Write" engedélyek szükségesek. Bármikor leválaszthat egy munkaterületet a fürtből. Az új betöltött adatmennyiséget a leválasztási művelet Log Analytics tárolóban és a Microsoft-kulccsal titkosított formában tárolja. Lekérdezheti a munkaterületre betöltött adatmennyiséget a kapcsolat zökkenőmentes leválasztása előtt és után, ha a fürt kiépítve és érvényes Key Vault kulccsal van konfigurálva.

  Ez a művelet aszinkron, és egy darabig elvégezhető.

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  - **Munkaterület-hivatkozás állapotának megtekintése**
  
  Hajtsa végre a Get műveletet a munkaterületen, és figyelje meg, hogy a *clusterResourceId* tulajdonság szerepel-e a válaszban a *szolgáltatások* területen. Egy csatolt munkaterület a *clusterResourceId* tulajdonsággal fog rendelkezni.

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Fürt törlése**

  A művelet végrehajtásához írási engedéllyel kell rendelkeznie a fürtön. A rendszer helyreállított törlési műveletet hajt végre, amely lehetővé teszi, hogy a fürt az adatait 14 napon belül, például véletlen vagy szándékos törléssel engedélyezze. A fürt neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A Soft-delete időszak után a rendszer felszabadítja a fürt nevét, és a fürt és az adatai véglegesen törlődnek, és nem helyreállítható. Bármely csatolt munkaterület leválasztása a fürtről törlési művelettel történik. Az új betöltött adatot Log Analytics tárolóban tároljuk, és a Microsoft kulccsal titkosítjuk. 
  
  A leválasztási művelet aszinkron, és akár 90 percet is igénybe vehet.

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```
 
  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
  
- **A fürt és az adatok helyreállítása** 
  
  Az elmúlt 14 napban törölt fürt törlési állapotba kerül, és az adatforrással állítható vissza. Mivel az összes munkaterület le lett választva a fürt törlésével, újra kell kapcsolni a munkaterületeket a fürt helyreállítása után. A helyreállítási műveletet jelenleg a termékcsoport manuálisan végzi el. Használja a Microsoft-csatornát, vagy nyissa meg a törölt fürt helyreállítására vonatkozó támogatási kérelmet.

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

- A Customer-Managed kulcs támogatott a dedikált Log Analytics-fürtön, és alkalmas az olyan ügyfelek számára, akik naponta 1 TB-ot küldenek.

- A fürt maximális száma régiónként és az előfizetés 2

- A fürthöz csatolt munkaterületek maximális száma 1000

- A munkaterületet összekapcsolhatja a fürttel, majd leválaszthatja azt. Az adott munkaterületen a munkaterület-csatolási műveletek száma legfeljebb 2 lehet, 30 napon belül.

- A fürtre mutató hivatkozás csak akkor hajtható végre, ha meggyőződött arról, hogy a Log Analytics-fürt üzembe helyezése befejeződött. A rendszer eldobta a munkaterületre a befejezés előtt elküldett adatait, és nem lesz helyreállítható.

- Customer-Managed a kulcs titkosítása a konfigurációs idő után az újonnan betöltött adatmennyiségre vonatkozik. A konfiguráció előtt betöltött adatmennyiség továbbra is titkosítva marad a Microsoft-kulccsal. A Customer-Managed kulcs konfigurálása zökkenőmentesen és után is lekérdezhető az adatfeldolgozás során.

- A Azure Key Vault helyreállítható kell konfigurálni. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, és a CLI vagy a PowerShell használatával kell konfigurálni:<br>
  - [Helyreállítható törlés](../../key-vault/general/soft-delete-overview.md)
  - A [védelem kiürítését](../../key-vault/general/soft-delete-overview.md#purge-protection) be kell kapcsolni ahhoz, hogy védelmet biztosítson a titkos/tár kényszerített törlése után is.

- A fürt más erőforráscsoporthoz vagy előfizetéshez való áthelyezése jelenleg nem támogatott.

- A Azure Key Vault, a fürtnek és a csatolt munkaterületnek ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet.

- A fürtre mutató hivatkozás sikertelen lesz, ha egy másik fürthöz van csatolva.

## <a name="troubleshooting"></a>Hibaelhárítás

- Működés Key Vault rendelkezésre állással
  - Normál működés esetén – a tárolási gyorsítótárak rövid időre visszamenőleges gyorsítótárazást biztosítanak, és visszakerül a Key Vaultra a rendszeres kicsomagoláshoz.
    
  - Átmeneti kapcsolódási hibák – a tároló átmeneti hibákat (időtúllépések, kapcsolódási hibák, DNS-problémák) biztosít, mivel a kulcsok rövid ideig nem maradhatnak a gyorsítótárban, és ez a rendelkezésre állásban lévő kisméretű rendszerállapot-visszaírásokat eredményezi. A lekérdezési és a betöltési képességek megszakítás nélkül folytatódnak.
    
  - Élő webhely – a körülbelül 30 perces leállása miatt a Storage-fiók elérhetetlenné válik. A lekérdezési képesség nem érhető el, és a rendszer az adatvesztés elkerülése érdekében a Microsoft Key használatával több órán keresztül gyorsítótárazza az adatmennyiséget. Ha a rendszer visszaállítja a Key Vault, a lekérdezés elérhetővé válik, és az ideiglenes gyorsítótárazott adatot a rendszer betölti az adattárba, és titkosítja Customer-Managed kulccsal.

  - Key Vault hozzáférési arány – a Azure Monitor Storage Key Vault a becsomagolási és a kicsomagolási műveletekhez való hozzáférésének gyakorisága 6 – 60 másodperc.

- Ha létrehoz egy fürtöt, és azonnal megadja a KeyVaultProperties, a művelet sikertelen lehet, mivel a hozzáférési házirend nem definiálható, amíg a rendszer identitása hozzá nem rendeli a fürthöz.

- Ha a meglévő fürtöt frissíti a KeyVaultProperties, és a "Get" kulcs-hozzáférési szabályzat hiányzik a Key Vault, a művelet sikertelen lesz.

- Ha a fürt létrehozásakor ütközési hiba lép fel, akkor előfordulhat, hogy az elmúlt 14 napban törölte a fürtöt, és ez egy nem kötelező törlési időszak. A fürt neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A név akkor jelenik meg, ha a rendszer véglegesen törli a fürtöt.

- Ha egy művelet végrehajtása közben frissíti a fürtöt, a művelet sikertelen lesz.

- Ha nem sikerül telepíteni a fürtöt, ellenőrizze, hogy a Azure Key Vault, a fürt és a csatolt Log Analytics-munkaterületek ugyanabban a régióban találhatók-e. A lehet különböző előfizetésekben.

- Ha Key Vaultban frissíti a kulcs verzióját, és nem frissíti az új kulcs-azonosító részleteit a fürtben, a Log Analytics-fürt továbbra is az előző kulcsot fogja használni, és az adatai elérhetetlenné válnak. Frissítse az új kulcs-azonosító részleteit a fürtben az adatok feldolgozásának folytatásához és az adatok lekérdezési képességéhez.

- Bizonyos műveletek hosszúak, és eltarthat egy ideig – ezek a fürtök létrehozása, a fürt kulcsának frissítése és a fürt törlése. A művelet állapotát kétféleképpen tekintheti meg:
  1. a REST használatakor másolja az Azure-AsyncOperation URL értéket a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
  2. GET kérelem küldése a fürtnek vagy a munkaterületnek, és figyelje meg a választ. A nem összekapcsolt munkaterület például nem rendelkezik a *szolgáltatások* *clusterResourceId* .

- A [kettős titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) automatikusan konfigurálva van az október 2020-ből létrehozott fürtök esetében, ha a dupla titkosítás a régióban volt. Ha létrehoz egy fürtöt, és "<régió neve> nem támogatja a fürtök kettős titkosítását", akkor továbbra is létrehozhatja a fürtöt, de a kettős titkosítás le van tiltva. A fürt létrehozása után nem engedélyezhető vagy tiltható le. Fürt létrehozásához, ha a dupla titkosítás nem támogatott a régióban, adja hozzá `"properties": {"isDoubleEncryptionEnabled": false}` a REST-kérelem törzsét.

- Hibaüzenetek
  
  Fürt létrehozása:
  -  400 – a fürt neve érvénytelen. A fürt neve az a-z, A-Z, 0-9 és a 3-63 hosszúságú karaktereket tartalmazhat.
  -  400 – a kérelem törzse null értékű vagy helytelen formátumú.
  -  400 – az SKU neve érvénytelen. Adja meg az SKU nevét a capacityReservation értékre.
  -  400 – a kapacitás meg lett biztosítva, de az SKU nem capacityReservation. Adja meg az SKU nevét a capacityReservation értékre.
  -  400 – hiányzó kapacitás az SKU-ban. Állítsa a kapacitás értékét 1000-re vagy magasabbra a 100 (GB) lépésekben.
  -  400 – a kapacitás az SKU-ban nincs a tartományon belül. A minimálisan 1000-as és a maximálisan engedélyezett kapacitásnak kell lennie, amely a munkaterület "használat és becsült költségek" területén érhető el.
  -  400 – a kapacitás 30 napig zárolva van. A csökkenő kapacitás 30 nappal a frissítés után engedélyezett.
  -  400 – nincs beállítva SKU. Állítsa az SKU nevét a capacityReservation és a kapacitás értékének 1000-re vagy magasabbra a 100 (GB) lépésekben.
  -  400 – az identitás null értékű vagy üres. Identitás beállítása systemAssigned típussal.
  -  400 – a KeyVaultProperties beállítása a létrehozáskor történik. A KeyVaultProperties frissítése a fürt létrehozása után.
  -  400 – a művelet most nem hajtható végre. Az aszinkron művelet a sikeres állapottól eltérő állapotban van. A fürtnek a frissítési művelet végrehajtása előtt el kell végeznie a műveletet.

  Fürt frissítése
  -  400 – a fürt törlési állapotban van. Az aszinkron művelet folyamatban van. A fürtnek a frissítési művelet végrehajtása előtt el kell végeznie a műveletet.
  -  400 – a KeyVaultProperties nem üres, de helytelen formátumú. Lásd a [kulcs-azonosító frissítését](#update-cluster-with-key-identifier-details).
  -  400 – nem sikerült érvényesíteni a kulcsot a Key Vaultban. Oka lehet az engedélyek hiánya, vagy ha a kulcs nem létezik. Ellenőrizze, hogy a [kulcs-és hozzáférési szabályzatot](#grant-key-vault-permissions) Key Vaultban állította-e be.
  -  400 – a kulcs nem helyreállítható. A Key Vaultt a Soft-delete és a Purge-Protection értékre kell beállítani. Lásd: [Key Vault dokumentáció](../../key-vault/general/soft-delete-overview.md)
  -  400 – a művelet most nem hajtható végre. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.
  -  400 – a fürt törlési állapotban van. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.

  Fürt beolvasása:
    -  404 – a fürt nem található, lehet, hogy törölték a fürtöt. Ha ezzel a névvel próbál létrehozni egy fürtöt, és ütközésbe ütközik, a fürt 14 napig helyreállítható törlést végez. A támogatási szolgálattal helyreállíthatja, vagy egy másik név használatával új fürtöt hozhat létre. 

  Fürt törlése
    -  409 – a fürt nem törölhető üzembe helyezési állapotban. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.

  Munkaterület hivatkozása:
  -  404 – a munkaterület nem található. A megadott munkaterület nem létezik vagy törölték.
  -  409 – a munkaterület hivatkozása vagy a művelet leválasztása folyamatban.
  -  400 – a fürt nem található, a megadott fürt nem létezik vagy törölték. Ha ezzel a névvel próbál létrehozni egy fürtöt, és ütközésbe ütközik, a fürt 14 napig helyreállítható törlést végez. A támogatási szolgálattal való helyreállításhoz forduljon az ügyfélszolgálathoz.

  Munkaterület leválasztása:
  -  404 – a munkaterület nem található. A megadott munkaterület nem létezik vagy törölték.
  -  409 – a munkaterület hivatkozása vagy a művelet leválasztása folyamatban.
