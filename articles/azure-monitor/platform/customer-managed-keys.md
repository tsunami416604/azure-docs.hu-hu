---
title: Azure Monitor – ügyfél által kezelt kulcs
description: Az Customer-Managed Key (CMK) konfigurálásához szükséges információk és lépések a Log Analytics-munkaterületen lévő adatok Azure Key Vault kulcs használatával történő titkosításához.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 09/09/2020
ms.openlocfilehash: 532d96163e2ec66730dc3fdf87f10904fd584224
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107997"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor – ügyfél által kezelt kulcs 

Ez a cikk háttér-információkat és lépéseket tartalmaz az ügyfél által felügyelt kulcsok (CMK) az Log Analytics-munkaterületekhez való konfigurálásához. A konfigurálást követően a munkaterületekre eljuttatott összes adatfájl titkosítva van a Azure Key Vault kulccsal.

Javasoljuk, hogy a konfiguráció előtt tekintse át [az alábbi korlátozásokat és korlátozásokat](#limitationsandconstraints) .

## <a name="customer-managed-key-cmk-overview"></a>Ügyfél által felügyelt kulcs (CMK) áttekintése

A inaktív adatok [titkosítása](../../security/fundamentals/encryption-atrest.md) a szervezetek közös adatvédelmi és biztonsági követelménye. Lehetővé teheti, hogy az Azure teljes mértékben kezelhesse a titkosítást, míg számos különböző lehetőség áll rendelkezésre a titkosítási vagy titkosítási kulcsok szoros kezeléséhez.

Azure Monitor biztosítja, hogy az összes adatok és mentett lekérdezések a Microsoft által felügyelt kulcsok (MMK-EK) használatával titkosítva legyenek. A Azure Monitor a saját [Azure Key Vault](../../key-vault/general/overview.md) tárolt és a rendszer által hozzárendelt [felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) -hitelesítéssel hozzáférő saját kulcs használatával is lehetőséget nyújt a titkosításra. Ez a kulcs (CMK) lehet [szoftveres vagy hardveres HSM-védelemmel ellátott](../../key-vault/general/overview.md). Azure Monitor a titkosítás használata azonos az [Azure Storage-titkosítás](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) működésének módjával.

A CMK képesség dedikált Log Analytics-fürtökön érhető el, és lehetővé teszi a vezérlést, hogy bármikor visszavonja az adataihoz való hozzáférést, és a [Kulcstároló](#customer-lockbox-preview) -vezérlővel megvédje azt. Annak ellenőrzéséhez, hogy a régiójában van-e a dedikált fürthöz szükséges kapacitás, előzetesen engedélyezni kell az előfizetést. A CMK konfigurálásának megkezdése előtt használja a Microsoft-névjegyét az előfizetés engedélyezéséhez.

A [log Analytics-fürtök díjszabási modellje](./manage-cost-storage.md#log-analytics-dedicated-clusters) a 1000 GB/nap szinten kezdődő kapacitási foglalásokat használja.

Az elmúlt 14 napban betöltött adatok a hatékony lekérdezési motor működéséhez a gyors gyorsítótárban (SSD-alapú) is megmaradnak. Ezek az adatforgalom a Microsoft kulcsaival együtt titkosítva marad, függetlenül a CMK-konfigurációtól, az SSD-adatforgalom pedig a [kulcs visszavonását](#cmk-kek-revocation)követi. Dolgozunk, hogy a CMK-mel titkosított SSD-adatforgalom a 2020-as második fele legyen.

## <a name="how-cmk-works-in-azure-monitor"></a>Hogyan működik a CMK Azure Monitor

Azure Monitor a rendszer által hozzárendelt felügyelt identitást használja a Azure Key Vault elérésének biztosításához. A rendszer által hozzárendelt felügyelt identitás csak egyetlen Azure-erőforráshoz társítható, miközben a Log Analytics-fürt identitása a fürt szintjén támogatott – ez azt diktálja, hogy a CMK képesség egy dedikált Log Analytics-fürtön van továbbítva. Ha több munkaterületen is támogatni szeretné a CMK, egy új Log Analytics *fürterőforrás* közbenső identitás-kapcsolatként működik a Key Vault és a log Analytics munkaterületek között. A Log Analytics fürt tárterülete a fürt erőforrásához társított felügyelt identitást használja a \' Azure Key Vault Azure Active Directory használatával történő hitelesítéséhez. *Cluster* 

A CMK konfigurálása után a dedikált fürthöz kapcsolódó munkaterületekre betöltött összes adat titkosítva van a kulcsával Key Vaultban. A munkaterületeket bármikor leválaszthatja a fürtből. Az új adatai bekerülnek a Log Analytics tárterületre, és titkosítva vannak a Microsoft-kulccsal, míg az új és a régi adatait zökkenőmentesen le lehet kérdezni.


![A CMK áttekintése](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics a Key Vault számára engedélyekkel rendelkező, felügyelt identitással rendelkező *fürterőforrás* – az identitást a dedikált log Analytics fürt tárterületére propagálja a rendszer.
3. Dedikált Log Analytics-fürt
4. A CMK-titkosításhoz kapcsolódó *fürterőforrás* -alapú munkaterületek

## <a name="encryption-keys-operation"></a>Titkosítási kulcsok művelete

A Storage adattitkosítása 3 típusú kulcsot vesz fel:

- **KEK** – kulcs titkosítási kulcsa (CMK)
- **AEK** – fiók titkosítási kulcsa
- **Adattitkosítási kulcsot** – adattitkosítási kulcs

A következő szabályok érvényesek:

- A Log Analytics fürt Storage-fiókjai egyedi titkosítási kulcsot hoznak az összes Storage-fiókhoz, amely a "AEK" néven ismert.

- A AEK a DEKs származtatása céljából használható, amelyek a lemezre írt adatblokkok titkosításához használt kulcsok.

- Ha a kulcsot Key Vaultban konfigurálja, és a fürtben hivatkozik rá, az Azure Storage kérelmeket küld a Azure Key Vaultnak a AEK becsomagolásához és az adattitkosítási és visszafejtési műveletek elvégzéséhez.

- A KEK soha nem hagyja el a Key Vault, és HSM-kulcsok esetén soha nem hagyja el a hardvert.

- Az Azure Storage a *fürterőforrás* -hez társított felügyelt identitást használja a Azure Key Vault hitelesítésére és elérésére Azure Active Directory használatával.

## <a name="cmk-provisioning-procedure"></a>CMK-létesítési eljárás

1. Előfizetés engedélyezése – a CMK képesség dedikált Log Analytics-fürtökön van továbbítva. Annak ellenőrzéséhez, hogy rendelkezik-e a szükséges kapacitással a régiójában, az előfizetést előzetesen engedélyezni kell. Az előfizetését a Microsoft-névjegy használatával kérheti le.
2. Azure Key Vault létrehozása és a kulcs tárolása
3. Fürt létrehozása
4. Engedélyek megadása a Key Vault számára
5. Log Analytics-munkaterületek összekapcsolása

A CMK konfigurációja nem támogatott Azure Portalban, és a kiépítés [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/)-, [CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics) -vagy [Rest](https://docs.microsoft.com/rest/api/loganalytics/) -kérelmeken keresztül történik.

### <a name="asynchronous-operations-and-status-check"></a>Aszinkron műveletek és állapot-ellenőrzések

Néhány konfigurációs lépés aszinkron módon fut, mert nem hajthatók végre gyorsan. Ha REST-kérelmeket használ a konfigurációban, a válasz kezdetben a 200-es HTTP-állapotkódot (OK) és az *Azure-AsyncOperation* tulajdonsággal rendelkező fejlécet ad vissza, ha elfogadják:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Ezután az aszinkron művelet állapotát úgy tekintheti meg, hogy elküld egy GET kérelmet az *Azure-AsyncOperation* fejléc értékére:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

A válasz a művelettel és annak *állapotával*kapcsolatos információkat tartalmaz. A következők egyike lehet:

A művelet folyamatban van
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

A kulcs-azonosító frissítési művelete folyamatban van
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

A fürt törlése folyamatban van – ha olyan fürtöt töröl, amely csatolt munkaterülettel rendelkezik, az egyes munkaterületekhez aszinkron módon történik a leválasztási művelet, és a művelet eltarthat egy ideig.
Ez nem vonatkozik arra az esetre, ha csatolt munkaterületet nem tartalmazó fürtöt töröl – ebben az esetben a fürtöt azonnal törli a rendszer.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

A művelet befejeződött
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Sikertelen művelet
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>Előfizetés engedélyezése a CMK üzembe helyezéséhez

A CMK képesség dedikált Log Analytics-fürtökön van továbbítva.Annak ellenőrzéséhez, hogy rendelkezik-e a szükséges kapacitással a régiójában, az előfizetést előzetesen engedélyezni kell. Az előfizetések azonosítóinak megadásához használja a Microsoft névjegyeit.

> [!IMPORTANT]
> A CMK-képesség regionális. A Azure Key Vault, a fürtnek és a csatolt Log Analytics-munkaterületnek ugyanabban a régióban kell lennie, de különböző előfizetésekben lehet.

### <a name="storing-encryption-key-kek"></a>Titkosítási kulcs (KEK) tárolása

Hozzon létre vagy használjon olyan Azure Key Vault, amelyet már létre kell hoznia, vagy importálnia kell az adattitkosításhoz használni kívánt kulcsot. A Azure Key Vaultt helyreállítható kell konfigurálni, hogy megvédje a kulcsot és az adataihoz való hozzáférést Azure monitorban. Ezt a konfigurációt a Key Vault tulajdonságok területén ellenőrizheti, ha engedélyezni szeretné a *Soft delete* és a *Purge Protection* szolgáltatást.

![A védelem törlésére és eltávolítására vonatkozó védelmi beállítások](media/customer-managed-keys/soft-purge-protection.png)

Ezek a beállítások a CLI-vel és a PowerShell-lel is frissíthetők:

- [Helyreállítható törlés](../../key-vault/general/soft-delete-overview.md)
- A [védelem kiürítése](../../key-vault/general/soft-delete-overview.md#purge-protection) a titkos vagy a tár kényszerített törlésével, a törlést követően is

### <a name="create-cluster"></a>Fürt létrehozása

Kövesse a [dedikált fürtök című cikkben](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster)bemutatott eljárást. 

> [!IMPORTANT]
> Másolja ki és mentse a választ, mivel a következő lépésekben szükség lesz a részletekre.

### <a name="grant-key-vault-permissions"></a>Key Vault engedélyek megadása

Frissítse a Key Vault egy új hozzáférési házirenddel, hogy engedélyeket biztosítson a fürt számára. Ezeket az engedélyeket az alátét Azure Monitor tárolója használja az adattitkosításhoz. Nyissa meg a Key Vault a Azure Portalban, és kattintson a "hozzáférési szabályzatok", majd a "hozzáférési házirend hozzáadása" lehetőségre a szabályzat létrehozásához a következő beállításokkal:

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

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> A fürt *SKU*-jának, *keyVaultProperties* vagy *billingType* frissítése a patch használatával végezhető el.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

**Válasz**

200 OK és fejléc.
A kulcs azonosítójának elvégzése néhány percet vesz igénybe. A frissítési állapotot kétféleképpen is megtekintheti:
1. Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
2. Küldjön egy GET kérelmet a fürtre, és tekintse meg a *KeyVaultProperties* tulajdonságait. A válaszban a legutóbb frissített kulcs-azonosító részleteit kell visszaadnia.

A kérésre adott válasznak a következőhöz hasonlóan kell kinéznie, amikor a kulcs azonosítójának frissítése befejeződött:

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

## <a name="cmk-kek-revocation"></a>CMK (KEK) visszavonás

Az adataihoz való hozzáférés visszavonásához tiltsa le a kulcsot, vagy törölje a fürt hozzáférési házirendjét a Key Vault. A Log Analytics fürt tárterülete mindig egy órán belül megtartja a kulcsfontosságú engedélyek változásait, a tárterület pedig elérhetetlenné válik. A fürthöz kapcsolódó munkaterületekre betöltött összes új adat eldobásra kerül, és nem állítható vissza, az adat nem érhető el, és a munkaterületekre irányuló lekérdezések sikertelenek lesznek. A korábban betöltött adatmennyiség mindaddig a tárolóban marad, amíg a fürt és a munkaterületek nem törlődnek. A nem elérhető adatokra az adatmegőrzési szabályzat vonatkozik, és a rendszer törli az adatmegőrzési időtartamot. 

Az elmúlt 14 napban betöltött adatok gyors gyorsítótárban (SSD-vel) is megmaradnak a hatékony lekérdezési motor működéséhez. Ez törölve lesz a kulcs-visszavonási művelet során, és elérhetetlenné válik.

A Storage rendszeres időközönként lekérdezi a Key Vault a titkosítási kulcs kicsomagolásának és a hozzájuk való hozzáférésnek, valamint az adatfeldolgozás és a lekérdezés 30 percen belüli folytatásának megkísérlése érdekében.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotáció

A CMK forgása megköveteli a fürt explicit frissítését a Azure Key Vault új kulcsának verziójával. Kövesse a "fürt frissítése a kulcs-azonosító részleteivel" lépést. Ha nem frissíti az új kulcs-azonosító részleteit a fürtben, a Log Analytics fürt tárterülete továbbra is a korábbi titkosítási kulcsot használja. Ha letiltja vagy törli a régi kulcsot, mielőtt frissíti az új kulcsot a fürtben, a [visszavonási](#cmk-kek-revocation) állapotba kerül.

Az összes adatai elérhetők maradnak a kulcs elforgatási művelete után, mivel az adatai mindig titkosítva vannak a fiók titkosítási kulcsával (AEK), míg a AEK mostantól titkosítva van az új kulcs titkosítási kulcs (KEK) verziójával Key Vaultban.

## <a name="cmk-for-queries"></a>Lekérdezések CMK

A Log Analyticsben használt lekérdezési nyelv kifejező, és bizalmas információkat tartalmazhat a lekérdezésekben vagy a lekérdezési szintaxisban hozzáadott megjegyzésekben. Egyes szervezetek megkövetelik, hogy az ilyen információk védelme a CMK szabályzat részeként történjen, és a kulcsával titkosított lekérdezéseket kell mentenie. A Azure Monitor lehetővé teszi, hogy a munkaterülethez való csatlakozáskor a saját kulcsával titkosított *mentett kereséseket* és *napló-riasztásokat* tartalmazó lekérdezéseket tárolja. 

> [!NOTE]
> Log Analytics lekérdezések a használt forgatókönyvtől függően különböző áruházakban menthetők. A lekérdezések a következő helyzetekben titkosítva maradnak a Microsoft Key (MMK) szolgáltatással: Azure Monitor, Azure-irányítópultok, Azure Logic app, Azure Notebooks és Automation Runbookok-munkafüzetek.

Ha saját tárolót (BYOS) használ, és összekapcsolja azt a munkaterülettel, a szolgáltatás feltölti a *mentett-kereséseket* és a *log-riasztási* lekérdezéseket a Storage-fiókjába. Ez azt jelenti, hogy a Storage-fiókot és a [titkosítás-nyugalmi szabályzatot](../../storage/common/customer-managed-keys-overview.md) a log Analytics fürtben lévő adatok titkosításához használt kulcs vagy egy másik kulcs használatával szabályozhatja. A Storage-fiókkal kapcsolatos költségekért azonban felelősnek kell lennie. 

**Szempontok a lekérdezések CMK beállítása előtt**
* A munkaterület és a Storage-fiók "Write" engedélyekkel kell rendelkeznie
* Győződjön meg arról, hogy a Storage-fiókot ugyanabban a régióban hozza létre, mint ahol a Log Analytics munkaterület található.
* A tárolóban végzett *keresések* szolgáltatásbeli összetevőknek számítanak, és a formátumuk változhat
* A meglévő *mentett keresések* el lesznek távolítva a munkaterületről. A konfiguráció előtt másolja és mentse a szükséges *kereséseket* . A *mentett keresések* megtekinthetők a [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) használatával
* A lekérdezési előzmények nem támogatottak, és nem láthatja a futtatott lekérdezéseket
* A lekérdezések mentése céljából egyetlen Storage-fiókot kapcsolhat a munkaterülethez, de a *mentett keresések* és a *log-riasztások* lekérdezései között is használható.
* A rögzítés az irányítópulton nem támogatott

**BYOS konfigurálása mentett keresési lekérdezésekhez**

Storage-fiók összekapcsolása *a* munkaterülethez – a *mentett keresési* lekérdezések a Storage-fiókba lesznek mentve. 

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

## <a name="cmk-management"></a>CMK-kezelés

- **Erőforráscsoport összes fürtjének beolvasása**
  
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

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
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

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Válasz**

  200 OK és fejléc.

  A leválasztási művelet Log Analytics tárolóban való tárolása után betöltött adatmennyiség 90 percet is igénybe vehet. A munkaterület leválasztási állapotát kétféleképpen tekintheti meg:

  1. Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
  2. Munkaterületek elküldése – kérelem kérése és a válaszra való [Rákérdezés](/rest/api/loganalytics/workspaces/get) : a nem összekapcsolt munkaterület nem lesz a *szolgáltatások* *clusterResourceId* .

- **Munkaterület-hivatkozás állapotának megtekintése**
  
  Hajtsa végre a Get műveletet a munkaterületen, és figyelje meg, hogy a *clusterResourceId* tulajdonság szerepel-e a válaszban a *szolgáltatások*területen. Egy csatolt munkaterület a *clusterResourceId* tulajdonsággal fog rendelkezni.

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Fürt törlése**

  A művelet végrehajtásához írási engedéllyel kell rendelkeznie a fürtön. A rendszer helyreállított törlési műveletet hajt végre, amely lehetővé teszi, hogy a fürt az adatait 14 napon belül, például véletlen vagy szándékos törléssel engedélyezze. A fürt neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A Soft-delete időszak után a rendszer felszabadítja a fürt nevét, és a fürt és az adatai véglegesen törlődnek, és nem helyreállítható. Bármely csatolt munkaterület leválasztása a fürtről törlési művelettel történik. Az új betöltött adatot Log Analytics tárolóban tároljuk, és a Microsoft kulccsal titkosítjuk. 
  
  A leválasztási művelet aszinkron, és akár 90 percet is igénybe vehet.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Válasz**

  200 OK

- **A fürt és az adatok helyreállítása** 
  
  Az elmúlt 14 napban törölt fürt törlési állapotba kerül, és az adatforrással állítható vissza. Mivel az összes munkaterület le lett kapcsolva a fürtből a törléskor, újra kell kapcsolni a munkaterületeket a CMK titkosítás helyreállítása után. A helyreállítási műveletet a termékcsoport jelenleg manuálisan hajtja végre. A Microsoft-csatornát a helyreállítási kérelmekhez használhatja.

## <a name="limitationsandconstraints"></a>Korlátozások és megkötések

- A CMK dedikált Log Analytics-fürtön támogatott, és alkalmas az olyan ügyfelek számára, akik napi 1 TB-ot küldenek.

- A fürt maximális száma régiónként és az előfizetés 2

A fürthöz csatolt munkaterületek maximális száma 100

- A munkaterületet összekapcsolhatja a fürttel, majd leválaszthatja azt, ha a CMK nincs szükség a munkaterületre. Az adott munkaterületen a munkaterület-csatolási műveletek száma legfeljebb 2 lehet, 30 napon belül.

- A fürtre mutató hivatkozás csak akkor hajtható végre, ha meggyőződött arról, hogy a Log Analytics-fürt üzembe helyezése befejeződött. A rendszer eldobta a munkaterületre a befejezés előtt elküldett adatait, és nem lesz helyreállítható.

- A CMK titkosítás a CMK-konfiguráció után az újonnan betöltött adatmennyiségre vonatkozik. A CMK-konfiguráció előtt betöltött adatmennyiség továbbra is titkosítva marad a Microsoft-kulccsal. A CMK konfigurálása zökkenőmentesen és után is lekérdezheti az adatfeldolgozást.

- A Azure Key Vault helyreállítható kell konfigurálni. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, és a CLI vagy a PowerShell használatával kell konfigurálni:<br>
  - [Helyreállítható törlés](../../key-vault/general/soft-delete-overview.md)
  - A [védelem kiürítését](../../key-vault/general/soft-delete-overview.md#purge-protection) be kell kapcsolni ahhoz, hogy védelmet biztosítson a titkos/tár kényszerített törlése után is.

- A fürt más erőforráscsoporthoz vagy előfizetéshez való áthelyezése jelenleg nem támogatott.

- A Azure Key Vault, a fürtnek és a csatolt munkaterületnek ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet.

- A fürtre mutató hivatkozás sikertelen lesz, ha egy másik fürthöz van csatolva

## <a name="troubleshooting"></a>Hibaelhárítás

- Működés Key Vault rendelkezésre állással
  - Normál működés esetén – a tárolási gyorsítótárak rövid időre visszamenőleges gyorsítótárazást biztosítanak, és visszakerül a Key Vaultra a rendszeres kicsomagoláshoz.
    
  - Átmeneti kapcsolódási hibák – a tároló átmeneti hibákat (időtúllépések, kapcsolódási hibák, DNS-problémák) biztosít, mivel a kulcsok rövid ideig nem maradhatnak a gyorsítótárban, és ez a rendelkezésre állásban lévő kisméretű rendszerállapot-visszaírásokat eredményezi. A lekérdezési és a betöltési képességek megszakítás nélkül folytatódnak.
    
  - Élő webhely – a körülbelül 30 perces leállása miatt a Storage-fiók elérhetetlenné válik. A lekérdezési képesség nem érhető el, és a rendszer az adatvesztés elkerülése érdekében a Microsoft Key használatával több órán keresztül gyorsítótárazza az adatmennyiséget. Ha a rendszer visszaállítja a Key Vault, a lekérdezés elérhetővé válik, és az ideiglenes gyorsítótárazott adatot a rendszer betölti az adattárba, és a CMK titkosítja.

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

- Az ügyfél által felügyelt kulccsal kapcsolatos támogatásért és segítségért használja a Microsoft-partnereit.

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
