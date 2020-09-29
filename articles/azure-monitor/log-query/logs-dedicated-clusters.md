---
title: Azure Monitor a dedikált fürtöket naplózza
description: Azok az ügyfelek, akik több mint 1 TB-nál több megfigyelési adatot használnak, a megosztott fürtök helyett dedikáltak
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 4ad3aa7169fcf7eeda6e56a2eab6669b8783d77d
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461461"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor a dedikált fürtöket naplózza

Azure Monitor a dedikált fürtök a nagy mennyiségű ügyfelek jobb kiszolgálására szolgáló központi telepítési lehetőség. Azok az ügyfelek, akik naponta több mint 4 TB-nyi adatot töltenek fel, dedikált fürtöket fognak használni. A dedikált fürtökkel rendelkező ügyfelek kiválaszthatják a fürtökön üzemeltetett munkaterületeket.

A nagy mennyiségű támogatáson kívül más előnyökkel is jár a dedikált fürtök használata:

- **Díjszabási korlát** – az ügyfelek magasabb betöltési arányra vonatkozó korlátját csak dedikált fürtön lehet megszabni.
- **Szolgáltatások** – bizonyos vállalati funkciók csak dedikált fürtökön érhetők el – különösen az ügyfél által felügyelt kulcsokra (CMK) és a kulcstároló-támogatásra. 
- **Konzisztencia** – az ügyfelek saját dedikált erőforrásaikkal rendelkeznek, így az ugyanazon a megosztott infrastruktúrán futó többi ügyféltől sincs befolyása.
- **Költséghatékonyság** – a dedikált fürt használata költséghatékonyabb lehet, mivel a hozzárendelt kapacitás foglalási szintjei figyelembe veszik az összes fürt betöltését, és az összes munkaterületére érvényesek, még akkor is, ha ezek némelyike kicsi, és nem jogosult a kapacitás foglalási kedvezményére.
- A **több munkaterületet tartalmazó** lekérdezések gyorsabban futnak, ha az összes munkaterület ugyanazon a fürtön található.

A dedikált fürtök esetében az ügyfeleknek legalább 1 TB-os adatfeldolgozási kapacitással kell véglegesíteni. A dedikált fürtre történő áttelepítés egyszerű. Nincs adatvesztés vagy-szolgáltatás megszakítása. 

> [!IMPORTANT]
> A dedikált fürtök jóváhagyva és teljes mértékben támogatottak az éles környezetben üzemelő példányok esetében. Az ideiglenes kapacitás megkötése miatt azonban a szolgáltatás használatához előzetes regisztráció szükséges. Az előfizetések azonosítóinak megadásához használja a Microsoft névjegyeit.

## <a name="management"></a>Kezelés 

A dedikált fürtök kezelése egy Azure-erőforráson keresztül történik, amely Azure Monitor-naplózási fürtöket jelöl. Az erőforrás minden művelete a PowerShell vagy a REST API használatával történik.

A fürt létrehozása után konfigurálható és hozzárendelhető munkaterületek is. Ha egy munkaterület egy fürthöz van társítva, a munkaterületre érkező új adat a fürtön található. Csak a fürttel azonos régióban lévő munkaterületek kapcsolódhatnak a fürthöz. A munkaterületek bizonyos korlátozásokkal nem használhatók a fürtben. A korlátozásokról a jelen cikk további részleteket tartalmaz. 

A fürt szintjén lévő összes művelethez a `Microsoft.OperationalInsights/clusters/write` művelet engedély szükséges a fürtön. Ez az engedély a műveletet tartalmazó tulajdonos vagy közreműködő vagy `*/write` a műveletet tartalmazó log Analytics közreműködő szerepkör használatával adható meg `Microsoft.OperationalInsights/*` . Log Analytics engedélyekkel kapcsolatos további információkért lásd: [hozzáférés kezelése a naplózási adatokhoz és munkaterületekhez Azure monitor](../platform/manage-access.md). 

## <a name="billing"></a>Számlázás

A dedikált fürtök csak olyan munkaterületek esetében támogatottak, amelyek GB-os csomagokat használnak a kapacitás foglalási szintjeivel vagy anélkül. A dedikált fürtök esetében nem számítunk fel további díjat azon ügyfelek számára, akik az adott fürtnél több mint 1 TB-ot töltenek be. "Véglegesítés a betöltéshez" kifejezés azt jelenti, hogy a fürt szintjén legalább 1 TB/nap kapacitás-foglalási szintet rendeltek hozzá. Amíg a kapacitás foglalása a fürt szintjén van csatolva, az adatok tényleges kitöltésére két lehetőség áll rendelkezésre:

- *Fürt* (alapértelmezett) – a fürt kapacitásának foglalási költségei a *fürterőforrás* számára vannak hozzárendelve.
- *Munkaterületek* – a fürt kapacitásának foglalási költségei arányosak a fürt munkaterületeivel. Ha a nap teljes betöltött adata a kapacitás foglalása alatt van, akkor a *fürterőforrás* számlázása valamilyen használattal történik. A fürt árképzési modelljével kapcsolatos további tudnivalókért tekintse meg [log Analytics dedikált fürtök](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) című témakört.

A dedikált fürtök számlázásával kapcsolatos további információkért lásd: [log Analytics dedikált fürt számlázása](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="creating-a-cluster"></a>Fürt létrehozása

Először létre kell hoznia egy fürt erőforrásait a dedikált fürt létrehozásának megkezdéséhez.

A következő tulajdonságokat kell megadni:

- **ClusterName**: felügyeleti célokra használatos. A felhasználók nem jelennek meg erre a névre.
- **ResourceGroupName**: bármely Azure-erőforrás esetében a fürtök egy erőforráscsoporthoz tartoznak. Javasoljuk, hogy egy központi informatikai erőforráscsoportot használjon, mivel a fürtöket általában számos csapat osztja meg a szervezeten belül. További tervezési szempontokért tekintse át [a Azure monitor naplók üzembe helyezésének megtervezése](../platform/design-logs-deployment.md) című szakaszt.
- **Hely**: a fürt egy adott Azure-régióban található. Ehhez a fürthöz csak a régióban található munkaterületek csatolhatók.
- **SkuCapacity**: a *kapacitás foglalási* szintjét (SKU) meg kell adnia a *fürterőforrás* létrehozásakor. A *kapacitás foglalási* szintje napi 1 000 gb és 3 000 GB között lehet. Ha szükséges, a 100-es lépésekben később is frissítheti. Ha napi 3 000 GB-nál nagyobb kapacitású foglalásra van szüksége, lépjen kapcsolatba velünk a következő címen: LAIngestionRate@microsoft.com . A fürttel kapcsolatos költségekkel kapcsolatos további információkért lásd: [log Analytics-fürtök költségeinek kezelése](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

A *fürterőforrás* létrehozása után további tulajdonságokat (például *SKU*, * keyVaultProperties vagy *billingType*) is szerkeszthet. További részletekért lásd alább.

> [!WARNING]
> A fürt létrehozása elindítja az erőforrás-kiosztást és az üzembe helyezést. A művelet elvégzése akár egy órát is igénybe vehet. Azt javasoljuk, hogy aszinkron módon futtassa.

A fürtöket létrehozó felhasználói fióknak rendelkeznie kell a szabványos Azure-Erőforrás-létrehozási engedéllyel: `Microsoft.Resources/deployments/*` és a fürt írási engedélyével `(Microsoft.OperationalInsights/clusters/write)` .

### <a name="create"></a>Létrehozás 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Call* 
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

*Válasz*

200 OK és fejlécnek kell lennie.

### <a name="check-provisioning-status"></a>Az átadási állapot ellenőrzése

A Log Analytics-fürt üzembe helyezése eltarthat egy ideig. A kiépítési állapotot többféleképpen is megtekintheti:

- Futtassa a Get-AzOperationalInsightsCluster PowerShell-parancsot az erőforráscsoport nevével, és tekintse meg a ProvisioningState tulajdonságot. Az érték a kiépítés és a *sikeres* Befejezés *ProvisioningAccount* .
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Másolja az Azure-AsyncOperation URL értékét a válaszból, és kövesse az aszinkron műveletek állapotának ellenőrzését.

- Küldjön egy GET-kérést a *fürterőforrás* számára, és tekintse meg a *provisioningState* értéket. Az érték a kiépítés és a *sikeres* Befejezés *ProvisioningAccount* .

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
   Authorization: Bearer <token>
   ```

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
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

A *principalId* GUID azonosítót a *fürterőforrás* felügyelt identitási szolgáltatása hozza létre.

## <a name="change-cluster-properties"></a>Fürt tulajdonságainak módosítása

Miután létrehozta a *fürterőforrás* -erőforrást, és teljes mértékben kiépítve van, a fürt szintjén további tulajdonságokat is szerkeszthet a PowerShell vagy a REST API használatával. A fürt létrehozása során elérhető tulajdonságoktól eltérő tulajdonságok csak a fürt üzembe helyezése után adhatók meg:

- **keyVaultProperties**: a [Azure monitor ügyfél által felügyelt kulcs](../platform/customer-managed-keys.md#cmk-provisioning-procedure)kiépítéséhez használt Azure Key Vault konfigurálására szolgál. A következő paramétereket tartalmazza:  *KeyVaultUri*, *Kulcsnév*, *Version*. 
- **billingType** – a *billingType* tulajdonság határozza meg a *fürterőforrás* és a hozzá tartozó adatforrások számlázási hozzárendelését:
- **Fürt** (alapértelmezett) – a fürt kapacitásának foglalási költségei a *fürterőforrás* számára vannak hozzárendelve.
- **Munkaterületek** – a fürt kapacitásának foglalási költségei arányosak a fürtben lévő munkaterületekhez, és a *fürt* erőforrása egy bizonyos használatot számláz ki, ha a napi teljes betöltött adat a kapacitás foglalása alatt van. A fürt árképzési modelljével kapcsolatos további tudnivalókért tekintse meg [log Analytics dedikált fürtök](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) című témakört. 

> [!NOTE]
> A *billingType* tulajdonság nem támogatott a PowerShellben.
> Előfordulhat, hogy a fürt tulajdonságainak frissítései aszinkron módon futnak, és eltarthat egy ideig.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> A *fürt* erőforrás- *SKU*-t, a *KeyVaultProperties* -t vagy a *billingType* -t a patch használatával frissítheti.

Példa: 

*Call*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
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

*Válasz*

200 OK és fejléc

### <a name="check-cluster-update-status"></a>Fürt frissítési állapotának megtekintése

A kulcs-azonosító propagálásának elvégzése néhány percet vesz igénybe. A frissítési állapotot kétféleképpen is megtekintheti:

- Másolja az Azure-AsyncOperation URL értékét a válaszból, és kövesse az aszinkron műveletek állapotának ellenőrzését. 

   VAGY

- Küldjön egy GET-kérést a *fürterőforrás* számára, és tekintse meg a *KeyVaultProperties* tulajdonságait. A válaszban a legutóbb frissített kulcs-azonosító részleteit kell visszaadnia.

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
       "billingType": "cluster",
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
  }
  ```

## <a name="link-a-workspace-to-the-cluster"></a>Munkaterület összekapcsolása a fürttel

Ha egy munkaterület egy dedikált fürthöz van csatolva, a munkaterületre betöltött új adat az új fürtre lesz irányítva, miközben a meglévő adat a meglévő fürtön marad. Ha a dedikált fürtöt az ügyfél által felügyelt kulcsok (CMK-EK) használatával titkosítják, csak az új adat titkosítva lesz a kulccsal. A rendszer ezt a különbséget a felhasználóktól és a felhasználóktól csak a szokásos módon kérdezi le, miközben a rendszer a háttérben futtatja a fürtön végzett lekérdezéseket.

A fürtök akár 100 munkaterülethez is összekapcsolhatók. A csatolt munkaterületek ugyanabban a régióban találhatók, mint a fürt. A rendszerháttér védelme és az adattöredezettség elkerülése érdekében a munkaterületet nem lehet havonta kétszer több fürthöz kapcsolni.

A kapcsolati művelet végrehajtásához a munkaterülethez és a *fürt* erőforrásához "írási" engedélyekkel kell rendelkeznie:

- A munkaterületen: *Microsoft. OperationalInsights/munkaterületek/írás*
- A *fürterőforrás* : *Microsoft. OperationalInsights/fürtök/írás*

A számlázási szempontoktól eltérő módon a csatolt munkaterület megtartja a saját beállításait, például az adatmegőrzési időt.
A munkaterület és a fürt különböző előfizetésekben lehet. Lehetőség van arra, hogy a munkaterület és a fürt eltérő bérlők legyenek, ha az Azure Lighthouse mindkét példányát egyetlen bérlőhöz rendeli.

A munkaterületek összekapcsolását csak a Log Analytics-fürt kiépítésének befejezése után lehet elvégezni.

> [!WARNING]
> A munkaterület fürthöz való csatolásához több háttér-összetevő szinkronizálására és a gyorsítótár hidratációjának biztosítására van szükség. A művelet végrehajtása akár két órát is igénybe vehet. Javasoljuk, hogy aszinkron módon futtassa.


### <a name="link-operations"></a>Kapcsolódási műveletek

**PowerShell**

Fürthöz való hivatkozáshoz használja a következő PowerShell-parancsot:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Fürthöz való hivatkozáshoz használja a következő REST-hívást:

*Küldés*

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

*Válasz*

200 OK és fejléc.

### <a name="using-customer-managed-keys-with-linking"></a>Ügyfél által felügyelt kulcsok használata a csatolással

Ha ügyfél által felügyelt kulcsokat használ, a rendszer a felügyelt kulccsal titkosítja a betöltött adatot a társítási művelet után, amely akár 90 percet is igénybe vehet. 

A munkaterület-társítási állapotot kétféleképpen tekintheti meg:

- Másolja az Azure-AsyncOperation URL értékét a válaszból, és kövesse az aszinkron műveletek állapotának ellenőrzését.

- [Munkaterületek küldése –](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) kérelem kérése és a válasz betartása. A társított munkaterülethez tartozik egy clusterResourceId a "szolgáltatások" alatt.

A küldési kérelem a következőhöz hasonló:

*Küldés*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*Válasz*

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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Munkaterület leválasztása dedikált fürtből

A munkaterületet leválaszthatja egy fürtről. A munkaterület fürtből való leválasztása után az ehhez a munkaterülethez társított új adatok nem lesznek elküldve a dedikált fürthöz. Emellett a munkaterület számlázása már nem a fürtön keresztül történik. Előfordulhat, hogy a nem összekapcsolt munkaterület régi adatmennyisége a fürtön marad. Ha az adatok titkosítása az ügyfél által felügyelt kulcsokkal (CMK) történik, a rendszer megőrzi a Key Vault titkot. A rendszer elvonta ezt a változást Log Analytics felhasználótól. A felhasználók csak a szokásos módon tudják lekérdezni a munkaterületet. A rendszer szükség szerint a háttérben végzi a fürtön keresztüli lekérdezéseket a felhasználókra vonatkozóan.  

> [!WARNING] 
> Egy hónapon belül legfeljebb két összekapcsolási művelet lehet a munkaterületen. Szánjon időt arra, hogy vegye figyelembe és tervezze meg a tevékenységek leválasztását. 

## <a name="delete-a-dedicated-cluster"></a>Dedikált fürt törlése

A dedikált fürterőforrás törölhető. A törlés előtt le kell szüntetnie a fürt összes munkaterületének összekapcsolását. A fürt erőforrásának törlése után a fizikai fürt kiürítési és törlési folyamatba kerül. A fürt törlése törli a fürtön tárolt összes adatmennyiséget. Az adatok olyan munkaterületekről származnak, amelyek korábban a fürthöz voltak társítva.

## <a name="next-steps"></a>További lépések

- További információ a [log Analytics dedikált fürt számlázásáról](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- A [log Analytics-munkaterületek megfelelő kialakításának](../platform/design-logs-deployment.md) megismerése
