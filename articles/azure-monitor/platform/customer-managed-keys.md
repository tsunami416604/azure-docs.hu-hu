---
title: Azure Monitor ügyfél által felügyelt kulcs
description: Információk és lépések az ügyfél által felügyelt kulcs (CMK) konfigurálásához a Log Analytics-munkaterületeken lévő adatok Azure Key Vault kulcs használatával történő titkosításához.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/07/2020
ms.openlocfilehash: c78d8d603b6686d382ec7edcccc24d5dacc4745a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982224"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor ügyfél által felügyelt kulcs 

Ez a cikk háttér-információkat és lépéseket tartalmaz az ügyfél által felügyelt kulcsok (CMK) az Log Analytics-munkaterületekhez való konfigurálásához. A konfigurálást követően a munkaterületekre eljuttatott összes adatfájl titkosítva van a Azure Key Vault kulccsal.

Javasoljuk, hogy a konfiguráció előtt tekintse át [az alábbi korlátozásokat és korlátozásokat](#limitations-and-constraints) .

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- Azure Monitor CMK egy korai hozzáférési szolgáltatás, és engedélyezve van a regisztrált előfizetések esetében.

- Az ebben a cikkben ismertetett CMK üzembe helyezés éles környezetben történik, és ez a funkció a korai hozzáférési szolgáltatásként is támogatott.

- A CMK képesség egy dedikált Log Analytics-fürtön érhető el, amely egy fizikai fürt és egy adattároló, amely akár napi 1 TB-ot küld az ügyfeleknek

- A CMK díjszabási modell jelenleg nem érhető el, és nem szerepel ebben a cikkben. A dedikált Log Analytics-fürt díjszabási modellje várható a naptári év második negyedévében (CY) 2020, és minden meglévő CMK-telepítésre érvényes lesz.

## <a name="customer-managed-key-cmk-overview"></a>Ügyfél által felügyelt kulcs (CMK) áttekintése

A inaktív adatok [titkosítása](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) a szervezetek közös adatvédelmi és biztonsági követelménye. Lehetővé teheti, hogy az Azure teljes mértékben kezelhesse a titkosítást, míg számos különböző lehetőség áll rendelkezésre a titkosítási vagy titkosítási kulcsok szoros kezeléséhez.

Azure Monitor Storage biztosítja, hogy az Azure által felügyelt kulcsokkal titkosított összes inaktív adatok az Azure Storage szolgáltatásban tárolódnak. A Azure Monitor emellett lehetőséget biztosít az adattitkosításra a saját [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)tárolt kulcsával, amely a rendszer által hozzárendelt [felügyelt identitásos](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) hitelesítés használatával érhető el. Ez a kulcs lehet [szoftveres vagy hardveres HSM-védelemmel ellátott](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
A titkosítás Azure Monitor használata azonos az [Azure Storage-titkosítás](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) működésének módjával.

A Azure Monitor Storage Key Vault a becsomagolási és a kicsomagolási műveletek esetében 6 – 60 másodperc közötti gyakoriságot biztosít.Azure Monitor a tároló egy órán belül mindig tiszteletben tartja a legfontosabb engedélyek változásait.

Az elmúlt 14 napban betöltött adatok gyors gyorsítótárban (SSD-vel) is megmaradnak a hatékony lekérdezési motor működéséhez. Ezek az adatforgalom a Microsoft kulcsaival együtt titkosítva marad, függetlenül a CMK-konfigurációtól, az SSD [-](#cmk-kek-revocation) adatforgalom azonban nem érhető el. Dolgozunk azon, hogy az SSD-adattitkosítás a CMK-mel a 2020 második felében legyen titkosítva.

## <a name="how-cmk-works-in-azure-monitor"></a>Hogyan működik a CMK Azure Monitor

Azure Monitor a rendszer által hozzárendelt felügyelt identitást használja a Azure Key Vault elérésének biztosításához.A rendszer által hozzárendelt felügyelt identitás csak egyetlen Azure-erőforráshoz társítható. A dedikált Log Analytics-fürt identitása a fürt szintjén támogatott, és ez azt határozza meg, hogy a CMK képesség dedikált Log Analytics-fürtön legyen továbbítva. Ha több munkaterületen is támogatni szeretné a CMK, egy új Log Analytics *fürterőforrás* közbenső identitás-kapcsolatként működik a Key Vault és a log Analytics munkaterületek között. Ez a fogalom megőrzi a dedikált Log Analytics-fürt és a Log Analytics *fürterőforrás* közötti identitást, míg a társított munkaterületek adatai védve vannak a Key Vault kulccsal. A dedikált Log Analytics fürt tárterülete a *fürt* erőforrásához\'társított felügyelt identitást használja a Azure Key Vault Azure Active Directory használatával történő hitelesítéséhez és eléréséhez.

![A CMK áttekintése](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Az ügyfél Key Vault.
2.    Az ügyfél Log Analytics *fürterőforrás* , amelynek a felügyelt identitása Key Vault engedélyekkel rendelkezik – az identitás a dedikált log Analytics fürt szintjén támogatott.
3.    Dedikált Log Analytics-fürt.
4.    *Az ügyfél CMK-* titkosításhoz társított munkaterületei.

## <a name="encryption-keys-operation"></a>Titkosítási kulcsok művelete

A Storage adattitkosítása 3 típusú kulcsot vesz fel:

- **KEK** – kulcs titkosítási kulcsa (CMK)
- **AEK** – fiók titkosítási kulcsa
- **Adattitkosítási kulcsot** – adattitkosítási kulcs

A következő szabályok érvényesek:

- A dedikált Log Analytics fürt Storage-fiókok egyedi titkosítási kulcsot állítanak elő minden olyan Storage-fiókhoz, amely a "AEK" néven ismert.

- A AEK a DEKs származtatása céljából használható, amelyek a lemezre írt adatblokkok titkosításához használt kulcsok.

- Ha a kulcsot Key Vaultban konfigurálja, és a *fürterőforrás* alapján hivatkozik rá, az Azure Storage kéréseket küld a Azure Key Vaultnak, hogy az adattitkosítási és-visszafejtési műveletek elvégzéséhez becsomagolja és kicsomagolja a AEK-t.

- A KEK soha nem hagyja el a Key Vault, és HSM-kulcsok esetén soha nem hagyja el a hardvert.

- Az Azure Storage a *fürterőforrás* -hez társított felügyelt identitást használja a Azure Key Vault hitelesítésére és elérésére Azure Active Directory használatával.

## <a name="cmk-provisioning-procedure"></a>CMK-létesítési eljárás

1. Előfizetés-engedélyezési lista – ez a korai hozzáférési szolgáltatáshoz szükséges
2. Azure Key Vault létrehozása és a kulcs tárolása
3. *Fürterőforrás* létrehozása
5. Engedélyek megadása a Key Vault számára
6. Log Analytics-munkaterületek társítása

Az eljárás jelenleg nem támogatott a felhasználói felületen, a kiépítési folyamat pedig REST APIon keresztül történik.

> [!IMPORTANT]
> Minden API-kérésnek tartalmaznia kell egy tulajdonosi engedélyezési jogkivonatot a kérelem fejlécében.

Például:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Ahol a *eyJ0eXAiO....* a teljes engedélyezési jogkivonatot jelöli. 

A jogkivonatot a következő módszerek egyikével is beszerezheti:

1. [Alkalmazásregisztrációk](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) metódus használata.
2. Az Azure Portalon
    1. Navigáljon Azure Portal a "fejlesztői eszköz" (F12) alatt
    1. Keresse meg az engedélyezési karakterláncot az "igénylési fejlécek" alatt a "batch? API-version" példányok egyikében. A következőképpen néz ki: "Authorization: tulajdonos eyJ0eXAiO...". 
    1. Másolja ki és adja hozzá az API-híváshoz az alábbi példákban.
3. Navigáljon az Azure REST dokumentációs webhelyére. Kattintson a "kipróbálás" elemre bármely API-ban, és másolja a tulajdonosi jogkivonatot.

### <a name="asynchronous-operations-and-status-check"></a>Aszinkron műveletek és állapot-ellenőrzések

A konfigurációs eljárás egyes műveletei aszinkron módon futnak, mert nem hajthatók végre gyorsan. Az aszinkron műveletre adott válasz kezdetben egy 200-es HTTP-állapotkódot (OK) és egy, az *Azure-AsyncOperation* tulajdonsággal rendelkező fejlécet ad vissza, ha elfogadják:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Az aszinkron művelet állapotát úgy tekintheti meg, hogy egy GET kérelmet küld az *Azure-AsyncOperation* fejléc értékére:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
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

### <a name="subscription-whitelisting"></a>Előfizetés-engedélyezési lista

A CMK képesség egy korai hozzáférési szolgáltatás. Azokat az előfizetéseket, amelyekhez *fürterőforrás* -létrehozási tervet kíván létrehozni, az Azure-termékcsoport előzetes engedélyezési listának kell lennie. Az előfizetések azonosítóinak megadásához használja a Microsoft névjegyeit.

> [!IMPORTANT]
> A CMK-képesség regionális. A Azure Key Vault, a *fürterőforrás* és a társított log Analytics-munkaterületnek ugyanabban a régióban kell lennie, de különböző előfizetésekben lehet.

### <a name="storing-encryption-key-kek"></a>Titkosítási kulcs (KEK) tárolása

Hozzon létre vagy használjon olyan Azure Key Vault, amelyet már létre kell hoznia, vagy importálnia kell az adattitkosításhoz használni kívánt kulcsot. A Azure Key Vaultt helyreállítható kell konfigurálni, hogy megvédje a kulcsot és az adataihoz való hozzáférést Azure monitorban. Ezt a konfigurációt a Key Vault tulajdonságok területén ellenőrizheti, ha engedélyezni szeretné a *Soft delete* és a *Purge Protection* szolgáltatást.

![A védelem törlésére és eltávolítására vonatkozó védelmi beállítások](media/customer-managed-keys/soft-purge-protection.png)

Ezek a beállítások a CLI-n és a PowerShellen keresztül érhetők el:
- [Helyreállítható törlés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- A [védelem kiürítése](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) a titkos vagy a tár kényszerített törlésével, a törlést követően is

### <a name="create-cluster-resource"></a>*Fürterőforrás* létrehozása

Ez az erőforrás köztes identitás-kapcsolatként használatos a Key Vault és az Log Analytics-munkaterületek között. Miután megkapta a megerősítést, hogy az előfizetések engedélyezve lettek, hozzon létre egy Log Analytics *fürterőforrás* azon a régión, ahol a munkaterületek találhatók.

*Fürterőforrás* létrehozásakor meg kell adnia a *kapacitás foglalási* szintjét (SKU). A *kapacitás foglalási* szintje napi 1 000 és 2 000 GB között lehet, és később is frissíthető 100-os lépésekben. Ha napi 2 000 GB-nál nagyobb kapacitású foglalásra van szüksége, lépjen kapcsolatba LAIngestionRate@microsoft.comvelünk a következő címen:. [További információ](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)
    
A *billingType* tulajdonság határozza meg a *fürterőforrás* és a hozzá tartozó adatforrások számlázási hozzárendelését:
- *fürt* (alapértelmezett) – a számlázás a *fürterőforrás* üzemeltetéséhez használt előfizetéshez van hozzárendelve.
- *munkaterületek* – a számlázás a munkaterületek arányosan üzemelő előfizetésekhez van hozzárendelve. 

> [!NOTE]
> A *fürterőforrás* létrehozása után frissítheti azt *SKU*-val, *KEYVAULTPROPERTIES* vagy *billingType* a patch Rest-kérelem használatával.

**Létrehozás**

Ez a Resource Manager-kérelem aszinkron művelet.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```
Az identitást a rendszer a *fürt* erőforrásához rendeli hozzá a létrehozáskor.

**Válasz**

200 OK és fejléc.

Míg a dedikált Log Analytics-fürt üzembe helyezése egy ideig tart, a kiépítési állapotot kétféleképpen is megtekintheti:

1. Másolja az Azure-AsyncOperation URL értékét a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
2. Küldjön egy GET-kérést a *fürterőforrás* számára, és tekintse meg a *provisioningState* értéket. A kiépítés és a *sikeres* Befejezés *ProvisioningAccount* .


```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Másolja ki és mentse a választ, mivel a következő lépésekben szükség lesz a részletekre.

**Válasz**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "provisioningState": "ProvisioningAccount",
    "clusterType": "LogAnalytics",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

A "principalId" GUID azonosítót a *fürterőforrás* felügyelt identitási szolgáltatása hozza létre.

### <a name="grant-key-vault-permissions"></a>Key Vault engedélyek megadása

Frissítse a Key Vault egy olyan új hozzáférési házirenddel, amely engedélyt ad a *fürterőforrás* számára. Ezeket az engedélyeket az alátét Azure Monitor tárolója használja az adattitkosításhoz. Nyissa meg a Key Vault a Azure Portalban, és kattintson a "hozzáférési szabályzatok", majd a "hozzáférési házirend hozzáadása" lehetőségre a szabályzat létrehozásához a következő beállításokkal:

- Kulcs engedélyei: válassza a beolvasás, a betakart kulcs és a kicsomagolási kulcs engedélyeket.
- Rendszerbiztonsági tag kiválasztása: adja meg az előző lépésben a válaszban visszaadott résztvevő-azonosító értéket.

![Key Vault engedélyek megadása](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A *Get* engedély szükséges annak ellenőrzéséhez, hogy a Key Vault helyreállítható-e a kulcs védelme érdekében, valamint a Azure monitor adataihoz való hozzáféréshez.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Fürterőforrás frissítése a kulcs-azonosító részleteivel

Ez a lépés a kezdeti és a későbbi verziókban a Key Vault. Tájékoztatja Azure Monitor tárolót az adattitkosításhoz használt kulcs verziószámáról. Ha frissítve van, az új kulcs a Storage Key (AEK) becsomagolására és kicsomagolására szolgál.

Ha szeretné frissíteni a *fürterőforrás* adatait a Key Vault *kulcs-azonosító* részleteit, válassza ki a kulcs aktuális verzióját a Azure Key Vault a kulcs azonosítójának részleteinek beszerzéséhez.

![Key Vault engedélyek megadása](media/customer-managed-keys/key-identifier-8bit.png)

Frissítse a *fürterőforrás* KeyVaultProperties a kulcs-azonosító részleteivel.

**Frissítés**

Ez a Resource Manager-kérelem aszinkron művelet a kulcs-azonosító részleteinek frissítésekor, miközben a kapacitás értékének frissítésekor szinkronban van.

> [!Note]
> Az *SKU*-t, a *keyVaultProperties* -t vagy a *billingType*-t egy részleges törzsben is megadhatja a *fürt* erőforrásaiban.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       }
   },
   "location":"<region-name>"
}
```
A "KeyVaultProperties" a Key Vault kulcs azonosítójának részleteit tartalmazza.

**Válasz**

200 OK és fejléc.
A kulcs azonosítójának elvégzése néhány percet vesz igénybe. A frissítési állapotot kétféleképpen is megtekintheti:
1. Másolja az Azure-AsyncOperation URL értékét a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
2. Küldjön egy GET-kérést a *fürterőforrás* számára, és tekintse meg a *KeyVaultProperties* tulajdonságait. A válaszban a legutóbb frissített kulcs-azonosító részleteit kell visszaadnia.

A *fürt* erőforrására vonatkozó kérésre adott válasznak a következőhöz hasonlóan kell kinéznie:

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
      keyVaultUri: "https://key-vault-name.vault.azure.net",
      kyName: "key-name",
      keyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Munkaterület társítása a *fürt* erőforrásához

A művelet végrehajtásához "írási" engedélyekkel kell rendelkeznie a munkaterület és a *fürterőforrás* számára, beleértve a következő műveleteket:

- A munkaterületen: Microsoft. OperationalInsights/munkaterületek/írás
- A *fürterőforrás* : Microsoft. OperationalInsights/fürtök/írás

> [!IMPORTANT]
> Ezt a lépést csak a dedikált Log Analytics-fürt kiépítés befejezése után kell végrehajtani. Ha munkaterületeket rendel hozzá, és a kiépítés előtt betölti az adatot, a rendszer elveti a betöltött adatmennyiséget, és nem lesz helyreállítható.

**Munkaterület hozzárendelése**

Ez a Resource Manager-kérelem aszinkron művelet.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Válasz**

200 OK és fejléc.

A betöltött adatait a rendszer a társítási művelet után titkosítja a felügyelt kulccsal, ami akár 90 percet is igénybe vehet. A munkaterület-társítási állapotot kétféleképpen tekintheti meg:

1. Másolja az Azure-AsyncOperation URL értékét a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
2. [Munkaterületek küldése –](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) kérelem kérése és a válasz betartása a társított munkaterületen a "szolgáltatások" clusterResourceId lesz.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
```

**Válasz**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": days,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>CMK (KEK) visszavonás

Az adataihoz való hozzáférés visszavonásához tiltsa le a kulcsot, vagy törölje a *fürt* erőforrás-hozzáférési szabályzatát a Key Vault. Azure Monitor a tárterület mindig egy órán belül betartja a legfontosabb engedélyek változásait, általában hamarabb, a tárterület pedig elérhetetlenné válik. A rendszer elveti a *fürterőforrás* -munkaterületekhez kapcsolódó összes adatot, és a lekérdezések sikertelenek lesznek. A korábban betöltött adatmennyiség nem érhető el Azure Monitor tárolóban, amíg a *fürterőforrás* , és a munkaterületek nem törlődnek. A nem elérhető adatokra az adatmegőrzési szabályzat vonatkozik, és a rendszer törli az adatmegőrzési időtartamot. 

Az elmúlt 14 napban betöltött adatok gyors gyorsítótárban (SSD-vel) is megmaradnak a hatékony lekérdezési motor működéséhez. Ezek az adategységek a Microsoft kulcsaival együtt titkosítva maradnak a CMK konfigurációjától függetlenül, de a kulcs-visszavonási művelet során törlődnek, és elérhetetlenné válnak.

A tárterület rendszeres időközönként lekérdezi a Key Vault, hogy megpróbálja kibontani a titkosítási kulcsot, és ha a hozzáférés, az adatfeldolgozás és a lekérdezés 30 percen belül folytatódni fog.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotáció

A CMK forgása megköveteli a *fürterőforrás* explicit frissítését a Azure Key Vault új kulcsának verziójával. Ha az új kulccsal szeretné frissíteni a Azure Monitort, kövesse a " *fürterőforrás* frissítése a kulcs-azonosító részleteivel" lépést. Ha Key Vaultban frissíti a kulcs verzióját, és nem frissíti az új kulcs-azonosító részleteit a *fürterőforrás* -ben, Azure monitor a tárterület továbbra is az előző kulcsot használja.
Az összes adatot elérhetővé kell tenni a kulcsfontosságú rotációs művelet után, beleértve az elforgatás előtt és után betöltött adatot is, mivel az összes adatot titkosítja a fiók titkosítási kulcsa (AEK), míg a AEK már titkosítva van az új kulcs titkosítási kulcs (KEK) verziójával.

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

- A CMK olyan dedikált Log Analytics-fürtön támogatott, amely az 1 TB-ot naponta vagy többet küldő ügyfelek számára alkalmas.

- A *fürt* erőforrásainak maximális száma régiónként és előfizetésben 2

- Hozzárendelhet egy munkaterületet a *fürt* erőforrásához, majd hozzárendelheti azt, ha az ADATAIhoz CMK már nincs szükség, vagy bármilyen más okból. A munkaterületen elvégezhető munkaterület-társítások száma 30 nap alatt legfeljebb 2 lehet

- A *fürthöz* való munkaterület-társítást csak akkor kell végrehajtani, ha meggyőződött arról, hogy a dedikált log Analytics-fürt kiépítés befejeződött. A rendszer eldobta a munkaterületre a befejezés előtt elküldett adatait, és nem lesz helyreállítható.

- A CMK titkosítás a CMK-konfiguráció után az újonnan betöltött adatmennyiségre vonatkozik. A CMK-konfiguráció előtt betöltött adatmennyiség továbbra is titkosítva marad a Microsoft-kulccsal. A CMK konfigurálása zökkenőmentesen és után is lekérdezheti az adatfeldolgozást.

- A Azure Key Vault helyreállítható kell konfigurálni. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, és a CLI vagy a PowerShell használatával kell konfigurálni:

  - A helyreállítható [törlést](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) be kell kapcsolni
  - A [védelem kiürítését](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) be kell kapcsolni ahhoz, hogy védelmet biztosítson a titkos/tár kényszerített törlése után is.

- A *fürterőforrás* más erőforráscsoporthoz vagy előfizetéshez való áthelyezése jelenleg nem támogatott.

- A Azure Key Vault, a *fürterőforrás* és a társított munkaterületeknek ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure ad) bérlőben kell lenniük, de különböző előfizetésekben is lehetnek.

- Ha egy másik *fürterőforrás* -erőforráshoz van társítva, akkor sikertelen lesz a munkaterület társítása a *fürt* erőforrásaihoz


## <a name="management"></a>Kezelés

- **Erőforráscsoport összes *fürterőforrás* -erőforrásának beolvasása**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
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
              keyVaultUri: "https://key-vault-name.vault.azure.net",
              keyName: "key-name",
              keyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- **Az előfizetés összes *fürterőforrás* -erőforrásának beolvasása**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Válasz**
    
  Ugyanaz a válasz, mint a "*fürterőforrás* egy erőforráscsoport esetében", de az előfizetések hatókörében.

- ***Kapacitás foglalásának* frissítése a *fürterőforrás* -ben**

  Ha a társított munkaterületekhez tartozó adatmennyiség idővel módosul, és a kapacitás foglalási szintjét megfelelően szeretné frissíteni. Kövesse a [ *fürt* frissítése erőforrást](#update-cluster-resource-with-key-identifier-details) , és adja meg az új kapacitás értékét. A 1 000 és 2 000 GB közötti tartományban, illetve a 100-as lépéseknél lehet. A napi 2 000 GB-nál nagyobb szint esetén a Microsoft-kapcsolattartóval engedélyezheti. Vegye figyelembe, hogy nem kell megadnia a teljes REST-kérelem törzsét, és tartalmaznia kell az SKU-t:

  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ``` 

- ***BillingType* frissítése a *fürterőforrás* -ben**

  A *billingType* tulajdonság határozza meg a *fürterőforrás* és a hozzá tartozó adatforrások számlázási hozzárendelését:
  - *fürt* (alapértelmezett) – a számlázás a fürterőforrás üzemeltetéséhez használt előfizetéshez van hozzárendelve.
  - *munkaterületek* – a számlázás a munkaterületek arányosan üzemelő előfizetésekhez van hozzárendelve.
  
  Kövesse a [ *fürt* frissítése erőforrást](#update-cluster-resource-with-key-identifier-details) , és adja meg az új billingType értéket. Vegye figyelembe, hogy nem kell megadnia a teljes REST-kérelem törzsét, és tartalmaznia kell a *billingType*:

  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Munkaterület hozzárendelése**

  A művelet végrehajtásához a munkaterületre és a *fürt* erőforrására vonatkozó "írási" engedélyekre van szükség. Bármikor elvégezheti a munkaterületek hozzárendelését a *fürterőforrás* használatával. Az új betöltött adatmennyiség a társítási művelet Log Analytics tárolóban történő tárolása és a Microsoft-kulccsal való titkosítása után történik. Lekérdezheti a munkaterületre betöltött adatmennyiséget, mielőtt a társítást zökkenőmentesen elvégezte, amíg a *fürterőforrás* kiépítve és érvényes Key Vault kulccsal van konfigurálva.

  Ez a Resource Manager-kérelem aszinkron művelet.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  ```

  **Válasz**

  200 OK és fejléc.

  A társítási művelet Log Analytics tárolóban való tárolása után betöltött adatmennyiség 90 percet is igénybe vehet. A munkaterület-társítási állapotot kétféleképpen is megtekintheti:

  1. Másolja az Azure-AsyncOperation URL értékét a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
  2. [Munkaterületek](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) elküldése – kérelem kérése és a válasz, de a társított munkaterület nem lesz a *szolgáltatások* *clusterResourceId* .


- **A *fürterőforrás* törlése**

  A művelet elvégzéséhez írási engedéllyel kell rendelkeznie a *fürt* erőforrásához. A rendszer helyreállított törlési műveletet végez, amely lehetővé teszi a *fürterőforrás* helyreállítását 14 napon belül, akár véletlen, akár szándékos törlést is. A *fürterőforrás* neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A Soft-delete időszak után a rendszer felszabadítja a *fürterőforrás* nevét, a *fürt* erőforrásait és adatait véglegesen törli, és nem helyreállítható. A társított munkaterület a törlési művelethez hozzárendeli a *fürterőforrás* -hozzárendelést. Az új betöltött adatot Log Analytics tárolóban tároljuk, és a Microsoft kulccsal titkosítjuk. A munkaterületek de-társított művelete aszinkron, és akár 90 percet is igénybe vehet.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Válasz**

  200 OK

- **A *fürterőforrás* és az adatok helyreállítása** 
  
  Az elmúlt 14 napban törölt *fürterőforrás* törlési állapotban van, és helyreállítható. Ezt a terméket jelenleg a termékcsoport manuálisan hajtja végre. A Microsoft-csatornát a helyreállítási kérelmekhez használhatja.


## <a name="troubleshooting"></a>Hibaelhárítás
- Működés Key Vault rendelkezésre állással
  - Normál működés esetén – a tárolási gyorsítótárak rövid időre visszamenőleges gyorsítótárazást biztosítanak, és visszakerül a Key Vaultra a rendszeres kicsomagoláshoz.
    
  - Átmeneti kapcsolódási hibák – a tároló átmeneti hibákat (időtúllépések, kapcsolódási hibák, DNS-problémák) biztosít, mivel a kulcsok rövid ideig nem maradhatnak a gyorsítótárban, és ez a rendelkezésre állásban lévő kisméretű rendszerállapot-visszaírásokat eredményezi. A lekérdezési és a betöltési képességek megszakítás nélkül folytatódnak.
    
  - Élő webhely – a körülbelül 30 perces leállása miatt a Storage-fiók elérhetetlenné válik. A lekérdezési képesség nem érhető el, és a rendszer az adatvesztés elkerülése érdekében a Microsoft Key használatával több órán keresztül gyorsítótárazza az adatmennyiséget. Ha a rendszer visszaállítja a Key Vault, a lekérdezés elérhetővé válik, és az ideiglenes gyorsítótárazott adatot a rendszer betölti az adattárba, és a CMK titkosítja.

- Ha létrehoz egy *fürterőforrás* -t, és azonnal megadja a KeyVaultProperties, a művelet meghiúsulhat, mivel a hozzáférési házirend nem határozható meg, amíg a rendszer identitása hozzá nem rendeli a *fürterőforrás* -erőforráshoz.

- Ha a meglévő *fürterőforrás* frissítése a KeyVaultProperties és a "Get" kulcs-hozzáférési szabályzat hiányzik a Key Vault, a művelet sikertelen lesz.

- Ha egy munkaterülethez társított *fürterőforrás* törlését kísérli meg, a törlési művelet sikertelen lesz.

- Ha a *fürterőforrás* létrehozásakor ütközési hiba lép fel, akkor előfordulhat, hogy az elmúlt 14 napban törölte a *fürterőforrás* -t, és ez egy nem megfelelő törlési időszak. A *fürterőforrás* neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A név akkor jelenik meg, ha a rendszer véglegesen törli a *fürt* erőforrását a helyreállított törlési időszak után.

- Ha egy művelet végrehajtása közben frissíti a *fürterőforrás* -erőforrást, a művelet sikertelen lesz.
