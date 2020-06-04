---
title: Azure Monitor ügyfél által felügyelt kulcs
description: Információk és lépések az ügyfél által felügyelt kulcs (CMK) konfigurálásához a Log Analytics-munkaterületeken lévő adatok Azure Key Vault kulcs használatával történő titkosításához.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/20/2020
ms.openlocfilehash: 9a80adbbeda2754f9f08a4d2f16b8caf9263b087
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84340851"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor ügyfél által felügyelt kulcs 

Ez a cikk háttér-információkat és lépéseket tartalmaz az ügyfél által felügyelt kulcsok (CMK) az Log Analytics-munkaterületekhez való konfigurálásához. A konfigurálást követően a munkaterületekre eljuttatott összes adatfájl titkosítva van a Azure Key Vault kulccsal.

Javasoljuk, hogy a konfiguráció előtt tekintse át [az alábbi korlátozásokat és korlátozásokat](#limitationsandconstraints) .

## <a name="customer-managed-key-cmk-overview"></a>Ügyfél által felügyelt kulcs (CMK) áttekintése

[Titkosítás nyugalmi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)   állapotban gyakori adatvédelmi és biztonsági követelmények a szervezetekben.Lehetővé teheti, hogy az Azure teljes mértékben kezelhesse a titkosítást, míg számos különböző lehetőség áll rendelkezésre a titkosítási vagy titkosítási kulcsok szoros kezeléséhez.

Azure Monitor biztosítja, hogy az összes adatok titkosítva legyenek az Azure által felügyelt kulcsok használatával.A Azure Monitor emellett lehetőséget biztosít az adattitkosításra, amely a saját [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)tárolja a saját kulcsát,   és a tárolók segítségével a rendszer által hozzárendelt [felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)-   hitelesítéssel fér hozzá.Ez a kulcs lehet [szoftveres vagy hardveres HSM-védelemmel ellátott](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Azure Monitor a titkosítás használata azonos az [Azure Storage-titkosítás](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)   működésének módjával.

A CMK segítségével szabályozhatja az adataihoz való hozzáférést, és bármikor visszavonhatja azt. Azure Monitor a tároló egy órán belül mindig tiszteletben tartja a legfontosabb engedélyek változásait. Az elmúlt 14 napban betöltött adatok a hatékony lekérdezési motor működéséhez a gyors gyorsítótárban (SSD-alapú) is megmaradnak. Ezek az adatforgalom a Microsoft kulcsaival együtt titkosítva marad, függetlenül a CMK-konfigurációtól, az SSD-adatforgalom pedig a [kulcs visszavonását](#cmk-kek-revocation)követi. Dolgozunk, hogy a CMK-mel titkosított SSD-adatforgalom a 2020-as második fele legyen.

A CMK képesség dedikált Log Analytics-fürtökön van továbbítva. Annak ellenőrzéséhez, hogy rendelkezik-e a szükséges kapacitással a régiójában, meg kell adnia az előfizetés előzetes engedélyezési feltételeit. A CMK konfigurálásának megkezdése előtt használja a Microsoft-névjegyét az előfizetés engedélyezési listájának beszerzéséhez.

A [log Analytics-fürtök díjszabási modellje](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)a   1000 GB/nap szinten kezdődő kapacitási foglalásokat használja.

## <a name="how-cmk-works-in-azure-monitor"></a>Hogyan működik a CMK Azure Monitor

Azure Monitor a rendszer által hozzárendelt felügyelt identitást használja a Azure Key Vault elérésének biztosításához. A rendszer által hozzárendelt felügyelt identitás csak egyetlen Azure-erőforráshoz társítható, miközben a Log Analytics-fürt identitása a fürt szintjén támogatott – ez azt diktálja, hogy a CMK képesség egy dedikált Log Analytics-fürtön van továbbítva. Ha több munkaterületen is támogatni szeretné a CMK, egy új Log Analytics *fürterőforrás* közbenső identitás-kapcsolatként működik a Key Vault és a log Analytics munkaterületek között. A Log Analytics fürt tárterülete a fürt erőforrásához társított felügyelt identitást használja a \' Azure Key Vault Azure Active Directory használatával történő hitelesítéséhez. *Cluster* 

A CMK konfigurálása után a *fürt* erőforrásaihoz tartozó munkaterületekre betöltött összes adatot titkosítja Key Vault a kulcsával. Bármikor leválaszthatja a munkaterületeket a *fürt* erőforrásaiból. Az új adatai bekerülnek a Log Analytics tárterületre, és titkosítva vannak a Microsoft-kulccsal, míg az új és a régi adatait zökkenőmentesen le lehet kérdezni.


![A CMK áttekintése](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics a Key Vault számára engedélyekkel rendelkező, felügyelt identitással rendelkező *fürterőforrás* – az identitást a dedikált log Analytics fürt tárterületére propagálja a rendszer.
3. Dedikált Log Analytics-fürt
4. A CMK-titkosításhoz tartozó *fürterőforrás* -hozzárendeléshez társított munkaterületek

## <a name="encryption-keys-operation"></a>Titkosítási kulcsok művelete

A Storage adattitkosítása 3 típusú kulcsot vesz fel:

- **KEK** – kulcs titkosítási kulcsa (CMK)
- **AEK** – fiók titkosítási kulcsa
- **Adattitkosítási kulcsot** – adattitkosítási kulcs

A következő szabályok érvényesek:

- A Log Analytics fürt Storage-fiókjai egyedi titkosítási kulcsot hoznak az összes Storage-fiókhoz, amely a "AEK" néven ismert.

- A AEK a DEKs származtatása céljából használható, amelyek a lemezre írt adatblokkok titkosításához használt kulcsok.

- Ha a kulcsot Key Vaultban konfigurálja, és a *fürterőforrás* alapján hivatkozik rá, az Azure Storage kéréseket küld a Azure Key Vaultnak, hogy az adattitkosítási és-visszafejtési műveletek elvégzéséhez becsomagolja és kicsomagolja a AEK-t.

- A KEK soha nem hagyja el a Key Vault, és HSM-kulcsok esetén soha nem hagyja el a hardvert.

- Az Azure Storage a *fürterőforrás* -hez társított felügyelt identitást használja a Azure Key Vault hitelesítésére és elérésére Azure Active Directory használatával.

## <a name="cmk-provisioning-procedure"></a>CMK-létesítési eljárás

1. Előfizetés-engedélyezési lista – a CMK képesség dedikált Log Analytics-fürtökön van továbbítva. Annak ellenőrzéséhez, hogy rendelkezik-e a szükséges kapacitással a régiójában, meg kell adnia az előfizetés előzetes engedélyezési feltételeit. Az előfizetés engedélyezési listájának beszerzése a Microsoft-névjegy használatával
2. Azure Key Vault létrehozása és a kulcs tárolása
3. *Fürterőforrás* létrehozása
4. Engedélyek megadása a Key Vault számára
5. Log Analytics-munkaterületek társítása

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

A *fürterőforrás* -törlés folyamatban van – ha töröl egy olyan *fürterőforrás* -erőforrást, amely munkaterületekhez társított munkaterületeket tartalmaz, akkor az aszinkron műveletekben eltarthat egy kapcsolat bontási műveletet.
Ez nem vonatkozik arra az esetre, ha egy társított munkaterülettel nem rendelkező *fürtöt* töröl – ebben az esetben a rendszer azonnal törli a *fürterőforrás* -készletet.
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

### <a name="subscription-whitelisting"></a>Előfizetés-engedélyezési lista

A CMK képesség dedikált Log Analytics-fürtökön van továbbítva.Annak ellenőrzéséhez, hogy rendelkezik-e a szükséges kapacitással a régiójában, meg kell adnia az előfizetés előzetes engedélyezési feltételeit. Az előfizetések azonosítóinak megadásához használja a Microsoft névjegyeit.

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

*Fürterőforrás* létrehozásakor meg kell adnia a *kapacitás foglalási* szintjét (SKU). A *kapacitás foglalási* szintje napi 1 000 és 2 000 GB között lehet, és később is frissíthető 100-os lépésekben. Ha napi 2 000 GB-nál nagyobb kapacitású foglalásra van szüksége, lépjen kapcsolatba velünk a következő címen: LAIngestionRate@microsoft.com . [További információ](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

A *billingType* tulajdonság határozza meg a *fürterőforrás* és a hozzá tartozó adatforrások számlázási hozzárendelését:
- *Fürt* (alapértelmezett) – a fürt kapacitásának foglalási költségei a *fürterőforrás* számára vannak hozzárendelve.
- *Munkaterületek* – a fürt kapacitásának foglalási költségei arányosak a fürtben lévő munkaterületekhez, és a *fürt* erőforrása egy bizonyos használatot számláz, ha a napi teljes betöltött adat a kapacitás foglalása alatt áll. A fürt árképzési modelljével kapcsolatos további tudnivalókért tekintse meg [log Analytics dedikált fürtök](manage-cost-storage.md#log-analytics-dedicated-clusters) című témakört. 

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

Míg a Log Analytics-fürt üzembe helyezése egy ideig tart, a kiépítési állapotot kétféleképpen is megtekintheti:

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

> [!NOTE]
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
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
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
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
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
> Ezt a lépést csak a Log Analytics-fürt üzembe helyezésének befejezése után kell végrehajtani. Ha munkaterületeket rendel hozzá, és a kiépítés előtt betölti az adatot, a rendszer elveti a betöltött adatmennyiséget, és nem lesz helyreállítható.

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
Authorization: Bearer <token>
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
    "retentionInDays": 31,
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

Az adataihoz való hozzáférés visszavonásához tiltsa le a kulcsot, vagy törölje a *fürt* erőforrás-hozzáférési szabályzatát a Key Vault. A Log Analytics fürt tárterülete mindig egy órán belül megtartja a kulcsfontosságú engedélyek változásait, a tárterület pedig elérhetetlenné válik. Minden új, a fürterőforrás-munkaterülethez kapcsolódó adat *Cluster*   eldobásra kerül, és nem állítható vissza, az adat nem érhető el, és a munkaterületekre irányuló lekérdezések sikertelenek lesznek. A korábban betöltött adatmennyiség mindaddig a tárolóban marad, amíg a *fürterőforrás* és a munkaterületek nem törlődnek. A nem elérhető adatokra az adatmegőrzési szabályzat vonatkozik, és a rendszer törli az adatmegőrzési időtartamot. 

Az elmúlt 14 napban betöltött adatok gyors gyorsítótárban (SSD-vel) is megmaradnak a hatékony lekérdezési motor működéséhez. Ez törölve lesz a kulcs-visszavonási művelet során, és elérhetetlenné válik.

A Storage rendszeres időközönként lekérdezi a Key Vault a titkosítási kulcs kicsomagolásának és a hozzájuk való hozzáférésnek, valamint az adatfeldolgozás és a lekérdezés 30 percen belüli folytatásának megkísérlése érdekében.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotáció

A CMK forgatásához explicit frissítés szükséges a *fürterőforrás* számára a Azure Key Vault új kulcsának verziójával. Kövesse a " *fürterőforrás* frissítése a kulcs-azonosító részleteivel" lépést. Ha nem frissíti az új kulcs-azonosító részleteit a *fürterőforrás* -ben, a log Analytics fürt tárterülete továbbra is a korábbi titkosítási kulcsot használja. Ha letiltja vagy törli a régi kulcsot, mielőtt frissíti az új kulcsot a *fürterőforrás* -ben, a rendszer a [kulcs-visszavonási](#cmk-kek-revocation) állapotot fogja kapni.

Az összes adatai elérhetők maradnak a kulcs elforgatási művelete után, mivel az adatai mindig titkosítva vannak a fiók titkosítási kulcsával (AEK), míg a AEK mostantól titkosítva van az új kulcs titkosítási kulcs (KEK) verziójával Key Vaultban.

## <a name="cmk-manage"></a>CMK kezelése

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
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
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

  **törzse**
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

  **törzse**
  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Munkaterület megszüntetése**

  A művelet végrehajtásához a munkaterületre és a *fürt* erőforrására vonatkozó "írási" engedélyekre van szükség. Bármikor leválaszthat egy munkaterületet a *fürt* erőforrásáról. Az új betöltött adatmennyiség a társítási művelet Log Analytics tárolóban történő tárolása és a Microsoft-kulccsal való titkosítása után történik. Lekérdezheti a munkaterületre betöltött adatmennyiséget, mielőtt a társítást zökkenőmentesen elvégezte, amíg a *fürterőforrás* kiépítve és érvényes Key Vault kulccsal van konfigurálva.

  Ez a Resource Manager-kérelem aszinkron művelet.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Válasz**

  200 OK és fejléc.

  A leválasztott adatmennyiséget a rendszer Log Analytics tárolóban tárolja, ez a művelet 90 percet is igénybe vehet. A munkaterület-társítási állapotot kétféleképpen is megtekintheti:

  1. Másolja az Azure-AsyncOperation URL értékét a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
  2. [Munkaterületek küldése –](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) kérelem kérése és a válasz megtekintése, a nem társított munkaterület nem rendelkezik a *szolgáltatások* *clusterResourceId* .

- **A *fürterőforrás* törlése**

  A művelet elvégzéséhez írási engedéllyel kell rendelkeznie a *fürt* erőforrásához. A rendszer helyreállított törlési műveletet végez, amely lehetővé teszi a *fürterőforrás* helyreállítását 14 napon belül, akár véletlen, akár szándékos törlést is. A *fürterőforrás* neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A Soft-delete időszak után a rendszer felszabadítja a *fürterőforrás* nevét, a *fürt* erőforrásait és adatait véglegesen törli, és nem helyreállítható. A társított munkaterületek a törlési művelet során kikerülnek a *fürterőforrás* alapján. Az új betöltött adatot Log Analytics tárolóban tároljuk, és a Microsoft kulccsal titkosítjuk. A munkaterületek nem társított művelete aszinkron, és akár 90 percet is igénybe vehet.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Válasz**

  200 OK

- **A *fürterőforrás* és az adatok helyreállítása** 
  
  Az elmúlt 14 napban törölt *fürterőforrás* törlési állapotban van, és visszaállítható az adott adattal. Mivel az összes munkaterület *a fürterőforrás* törlésével van társítva a *fürterőforrás* törlésekor, újra társítania kell a munkaterületeket a CMK titkosítás helyreállítása után. A helyreállítási műveletet a termékcsoport jelenleg manuálisan hajtja végre. A Microsoft-csatornát a helyreállítási kérelmekhez használhatja.

## <a name="limitationsandconstraints"></a>Korlátozások és megkötések

– A CMK dedikált Log Analytics-fürtön támogatott, és alkalmas az olyan ügyfelek számára, akik napi 1 TB-ot küldenek.

– A *fürt*   erőforrásainak maximális száma régiónként és előfizetésben 2

– Hozzárendelheti a munkaterületet a *fürt*   erőforrásához, majd megoszthatja azt, ha a munkaterülethez nincs szükség CMK.Az adott munkaterületen egy 30 napos időszakon belül a munkaterület-társítások száma legfeljebb 2 lehet

– A munkaterület- *hozzárendelést*   csak akkor kell végrehajtani, ha meggyőződött arról, hogy a log Analytics-fürt üzembe helyezése befejeződött.A rendszer eldobta a munkaterületre a befejezés előtt elküldett adatait, és nem lesz helyreállítható.

– A CMK titkosítás a CMK-konfiguráció után az újonnan betöltött adatmennyiségre vonatkozik      .A CMK      -konfiguráció előtt betöltött adatmennyiség továbbra is titkosítva marad a Microsoft-kulccsal.    A CMK konfigurálása zökkenőmentesen és után is lekérdezheti az adatfeldolgozást.

– A Azure Key Vault helyreállítható kell konfigurálni.Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, és a parancssori felület vagy a PowerShell használatával kell konfigurálni: a törlési védelmet be kell kapcsolni, hogy a rendszer a    -  [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 
         -  [Purge protection](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)   titkos törlés után még a titok/tár kényszerített törlését is bekapcsolja.

- *Fürt*   az erőforrás jelenleg nem támogatott más erőforráscsoporthoz vagy előfizetéshez      .

– A Azure Key Vault, a *fürterőforrás*   és a társított munkaterületeknek ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure ad) bérlőben kell lenniük, de különböző előfizetésekben is lehetnek.

– A munkaterület-hozzárendelés *fürterőforrás*-társítása   sikertelen lesz, ha      egy másik *fürterőforrás*-erőforráshoz van társítva  

## <a name="troubleshooting"></a>Hibaelhárítás

- Működés Key Vault rendelkezésre állással
  - Normál működés esetén – a tárolási gyorsítótárak rövid időre visszamenőleges gyorsítótárazást biztosítanak, és visszakerül a Key Vaultra a rendszeres kicsomagoláshoz.
    
  - Átmeneti kapcsolódási hibák – a tároló átmeneti hibákat (időtúllépések, kapcsolódási hibák, DNS-problémák) biztosít, mivel a kulcsok rövid ideig nem maradhatnak a gyorsítótárban, és ez a rendelkezésre állásban lévő kisméretű rendszerállapot-visszaírásokat eredményezi. A lekérdezési és a betöltési képességek megszakítás nélkül folytatódnak.
    
  - Élő webhely – a körülbelül 30 perces leállása miatt a Storage-fiók elérhetetlenné válik. A lekérdezési képesség nem érhető el, és a rendszer az adatvesztés elkerülése érdekében a Microsoft Key használatával több órán keresztül gyorsítótárazza az adatmennyiséget. Ha a rendszer visszaállítja a Key Vault, a lekérdezés elérhetővé válik, és az ideiglenes gyorsítótárazott adatot a rendszer betölti az adattárba, és a CMK titkosítja.

  - Key Vault hozzáférési arány – a Azure Monitor Storage Key Vault a becsomagolási és a kicsomagolási műveletekhez való hozzáférésének gyakorisága 6 – 60 másodperc.

- Ha létrehoz egy *fürterőforrás* -t, és azonnal megadja a KeyVaultProperties, a művelet meghiúsulhat, mivel a hozzáférési házirend nem határozható meg, amíg a rendszer identitása hozzá nem rendeli a *fürterőforrás* -erőforráshoz.

- Ha a meglévő *fürterőforrás* frissítése a KeyVaultProperties és a "Get" kulcs-hozzáférési szabályzat hiányzik a Key Vault, a művelet sikertelen lesz.

- Ha egy munkaterülethez társított *fürterőforrás* törlését kísérli meg, a törlési művelet sikertelen lesz.

- Ha a *fürterőforrás* létrehozásakor ütközési hiba lép fel, akkor előfordulhat, hogy az elmúlt 14 napban törölte a *fürterőforrás* -t, és ez egy nem megfelelő törlési időszak. A *fürterőforrás* neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A név akkor jelenik meg, ha a rendszer véglegesen törli a *fürt* erőforrását a helyreállított törlési időszak után.

- Ha egy művelet végrehajtása közben frissíti a *fürterőforrás* -erőforrást, a művelet sikertelen lesz.

- Ha nem sikerül telepíteni a *fürterőforrás* -t, ellenőrizze, hogy a Azure Key Vault, a *fürterőforrás*   és a társított log Analytics-munkaterületek ugyanabban a régióban találhatók-e. A lehet különböző előfizetésekben.

- Ha Key Vaultban frissíti a kulcs verzióját, és nem frissíti az új kulcs-azonosító részleteit a *fürterőforrás* -ben, a log Analytics-fürt továbbra is az előző kulcsot fogja használni, és az adatai elérhetetlenné válnak. Frissítse az új kulcs-azonosító részleteit a *fürterőforrás* -ben az adatok feldolgozásának folytatásához és az adatok lekérdezési képességéhez.

- Az ügyfél által felügyelt kulccsal kapcsolatos támogatásért és segítségért használja a Microsoft-partnereit.
