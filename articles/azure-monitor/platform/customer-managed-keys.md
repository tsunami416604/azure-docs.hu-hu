---
title: Azure Monitor ügyfél által felügyelt kulcs konfigurálása
description: Információk és lépések az ügyfél által felügyelt kulcs (CMK) konfigurálásához a Log Analytics-munkaterületeken lévő adatok Azure Key Vault kulcs használatával történő titkosításához.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/24/2020
ms.openlocfilehash: 0cb33f55acacfd3635d19719265a46b566765a64
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592102"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor ügyfél által felügyelt kulcs konfigurálása 

Ez a cikk háttér-információkat és lépéseket tartalmaz a Log Analytics-munkaterületekhez és a Application Insights-összetevőkhöz tartozó ügyfél által felügyelt kulcsok (CMK) konfigurálásához. A konfigurálást követően a munkaterületekre vagy összetevőkre eljuttatott összes adatfájl titkosítva van a Azure Key Vault kulccsal.

Javasoljuk, hogy a konfiguráció előtt tekintse át [az alábbi korlátozásokat és korlátozásokat](#limitations-and-constraints) .

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- Azure Monitor CMK egy korai hozzáférési szolgáltatás, és engedélyezve van a regisztrált előfizetések esetében.

- Az ebben a cikkben ismertetett CMK üzembe helyezés éles környezetben történik, és ez a funkció a korai hozzáférési szolgáltatásként is támogatott.

- A CMK képesség egy dedikált adattár-fürtön érhető el, amely egy Azure Adatkezelő (ADX) fürt, és alkalmas az olyan ügyfelek számára, akik naponta 1 TB-ot küldenek. 

- A CMK díjszabási modell jelenleg nem érhető el, és nem szerepel ebben a cikkben. A dedikált ADX-fürt díjszabási modellje a naptári év második negyedévében (CY) 2020, és minden meglévő CMK-telepítésre érvényes lesz.

- Ez a cikk Log Analytics munkaterületek CMK-konfigurációját ismerteti. A Application Insights-összetevők CMK is támogatott ebben a cikkben, míg a különbségek a függelékben vannak felsorolva.

> [!NOTE]
> Log Analytics és Application Insights ugyanazt az adattárolási platformot és lekérdezési motort használják.
> Ezt a két áruházat összekapcsoljuk a Application Insights integrálásával a Log Analyticsba, hogy egyetlen egységesített naplót hozzunk létre a Azure Monitor alatt. Ezt a változást a 2020-es naptári év második negyedévére tervezték. Ha ezt követően nem kell központilag telepítenie a Application Insightsi adatait, javasoljuk, hogy várjon a konszolidáció befejezésére, mert az ilyen központi telepítések a konszolidáció során megszakadnak, és a CMK újra be kell állítania a CMK a bejelentkezés után. Elemzési munkaterület. Az 1 TB/nap minimum a fürt szintjén érvényes, amíg a konszolidáció befejeződik a második negyedévben, Application Insights és Log Analytics külön fürtöket igényelnek.

## <a name="customer-managed-key-cmk-overview"></a>Ügyfél által felügyelt kulcs (CMK) áttekintése

A inaktív adatok [titkosítása](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) a szervezetek közös adatvédelmi és biztonsági követelménye. Lehetővé teheti, hogy az Azure teljes mértékben kezelhesse a titkosítást, míg számos különböző lehetőség áll rendelkezésre a titkosítási vagy titkosítási kulcsok szoros kezeléséhez.

A Azure Monitor adattár biztosítja, hogy az Azure által felügyelt kulcsokkal titkosított összes inaktív adatok az Azure Storage szolgáltatásban tárolódnak. A Azure Monitor az adattitkosítást is lehetővé teszi az [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)tárolt saját kulcs használatával, amely a rendszer által hozzárendelt [felügyelt identitásos](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) hitelesítés használatával érhető el. Ez a kulcs lehet [szoftveres vagy hardveres HSM-védelemmel ellátott](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
A titkosítás Azure Monitor használata azonos az [Azure Storage-titkosítás](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) működésének módjával.

A Azure Monitor Storage Key Vault a becsomagolási és a kicsomagolási műveletek esetében 6 – 60 másodperc közötti gyakoriságot biztosít. Azure Monitor a tároló egy órán belül mindig tiszteletben tartja a legfontosabb engedélyek változásait.

Az elmúlt 14 napban betöltött adatok gyors gyorsítótárban (SSD-vel) is megmaradnak a hatékony lekérdezési motor működéséhez. Ezek az adatforgalom az CMK-konfigurációtól függetlenül titkosítva marad a Microsoft-kulcsoknál, de dolgozunk azon, hogy az SSD-t a CMK korai 2020-mel titkosítsa.

## <a name="how-cmk-works-in-azure-monitor"></a>Hogyan működik a CMK Azure Monitor

Azure Monitor a rendszer által hozzárendelt felügyelt identitást használja a Azure Key Vault elérésének biztosításához. A rendszer által hozzárendelt felügyelt identitás csak egyetlen Azure-erőforráshoz társítható. A fürt szintjén a Azure Monitor adattár (ADX-fürt) identitása támogatott, és ez azt határozza meg, hogy a CMK képesség egy dedikált ADX-fürtön van-e továbbítva. A CMK több munkaterületen való támogatásához egy új Log Analytics erőforrás (*fürt*) közbenső identitás-kapcsolatként működik a Key Vault és a log Analytics munkaterületek között. Ez a fogalom megfelel a rendszer által hozzárendelt identitási megkötésnek, és az identitást a ADX-fürt és a Log Analytics *fürterőforrás* között tartja fenn, míg az összes társított munkaterület adatai védettek a Key Vault kulccsal. Az alátét ADX fürt tárterülete a felügyelt identitást használja, amelyet\'s társít a *fürterőforrás* -hez, hogy a Azure Key Vault a Azure Active Directory használatával hitelesítse és hozzáférhessen.

![A CMK áttekintése](media/customer-managed-keys/cmk-overview.png)
1.  Az ügyfél Key Vault.
2.  Az ügyfél Log Analytics fürterőforrás, amelynek a felügyelt identitása Key Vault engedélyekkel rendelkezik – az identitást az adattár (ADX-fürt) szintjén támogatja a rendszer.
3.  Azure Monitor dedikált ADX-fürt.
4.  Az ügyfél CMK-titkosításhoz társított munkaterületei.

## <a name="encryption-keys-management"></a>Titkosítási kulcsok kezelése

A Storage adattitkosítása 3 típusú kulcsot vesz fel:

- **KEK** – kulcs titkosítási kulcsa Key Vault (CMK)
- **AEK** – fiók titkosítási kulcsa
- **Adattitkosítási kulcsot** – adattitkosítási kulcs

A következő szabályok érvényesek:

- A ADX Storage-fiók létrehoz egy egyedi titkosítási kulcsot minden Storage-fiókhoz, amely a AEK néven ismert.

- A AEK a DEKs származtatása céljából használható, amelyek a lemezre írt adatblokkok titkosításához használt kulcsok.

- Ha Key Vaultban konfigurálja a kulcsot, és hivatkozik rá a *fürterőforrás* -ben, az Azure Storage becsomagolja a AEK-t a KEK-be Azure Key Vault.

- A KEK soha nem hagyja el a Key Vault, és HSM-kulcsok esetén soha nem hagyja el a hardvert.

- Az Azure Storage a *fürterőforrás* -hez társított felügyelt identitást használja a Azure Key Vault hitelesítésére és elérésére Azure Active Directory használatával.

- Az olvasási/írási műveletek esetében az Azure Storage kéréseket küld a Azure Key Vaultnak a titkosítási és visszafejtési műveletek elvégzéséhez.

## <a name="cmk-provisioning-procedure"></a>CMK-létesítési eljárás

Application Insights CMK konfigurálásához kövesse a 3. és a 6. lépésekhez tartozó függelék tartalmát.

1. Előfizetés-engedélyezési lista – ez a korai hozzáférési szolgáltatáshoz szükséges
2. Azure Key Vault létrehozása és a kulcs tárolása
3. *Fürterőforrás* létrehozása
4. Azure Monitor adattár (ADX-fürt) üzembe helyezése
5. Engedélyek megadása a Key Vault számára
6. Log Analytics munkaterületek társítása

Az eljárás jelenleg nem támogatott a felhasználói felületen, a kiépítési folyamat pedig REST APIon keresztül történik.

> [!IMPORTANT]
> Minden API-kérésnek tartalmaznia kell egy tulajdonosi engedélyezési jogkivonatot a kérelem fejlécében.

Például:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Ahol a *eyJ0eXAiO....* a teljes engedélyezési jogkivonatot jelöli. 

A jogkivonatot a következő módszerek egyikével is beszerezheti:

1. [Alkalmazásregisztrációk](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) metódus használata.

2. A Azure Portal
    1. Navigáljon a Azure Portal a "fejlesztői eszköz (F12)
    1. Keresse meg az engedélyezési karakterláncot az "igénylési fejlécek" alatt a "batch? API-version" példányok egyikében. A következőhöz hasonló: "Authorization: tulajdonos \<token\>". 
    1. Másolja ki és adja hozzá az API-híváshoz az alábbi példákban.

3. Navigáljon az Azure REST dokumentációs webhelyére. Kattintson a "kipróbálás" elemre bármely API-ban, és másolja a tulajdonosi jogkivonatot.

### <a name="subscription-whitelisting"></a>Előfizetés-engedélyezési lista

A CMK képesség egy korai hozzáférési szolgáltatás. Azokat az előfizetéseket, amelyekhez *fürterőforrás* -létrehozási tervet kíván létrehozni, az Azure-termékcsoport előzetes engedélyezési listának kell lennie. Az előfizetések azonosítóinak megadásához használja a Microsoft névjegyeit.

> [!IMPORTANT]
> A CMK-képesség regionális. A Azure Key Vault, a Storage-fióknak, a *fürterőforrás* -nek és a társított log Analytics-munkaterületnek ugyanabban a régióban kell lennie, de különböző előfizetésekben lehet.

### <a name="storing-encryption-key-kek"></a>Titkosítási kulcs (KEK) tárolása

Hozzon létre egy Azure Key Vault erőforrást, majd hozza létre vagy importálja az adattitkosításhoz használandó kulcsot.

A Azure Key Vaultt helyreállítható kell konfigurálni a kulcs védelme érdekében, valamint a Azure monitor adataihoz való hozzáféréshez.

Ezek a beállítások a CLI-n és a PowerShellen keresztül érhetők el:
- A helyreállítható [törlést](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) be kell kapcsolni
- A [védelem kiürítését](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) be kell kapcsolni ahhoz, hogy védelmet biztosítson a titok/tár kényszerített törlése után is.

### <a name="create-cluster-resource"></a>*Fürterőforrás* létrehozása

Ez az erőforrás köztes identitás-kapcsolatként használatos a Key Vault és a munkaterületek között. Miután megkapta a megerősítést, hogy az előfizetések engedélyezve lettek, hozzon létre egy Log Analytics *fürterőforrás* azon a régión, ahol a munkaterületek találhatók. Application Insights és Log Analytics külön fürterőforrás szükséges. A *fürterőforrás* típusát a létrehozás ideje határozza meg, ha a "clusterType" tulajdonságot "LogAnalytics" vagy "ApplicationInsights" értékre állítja. A fürterőforrás típusa nem módosítható.

Application Insights CMK konfigurálásához kövesse a jelen lépés függelékének tartalmát.

**Létrehozás**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
Az identitást a rendszer a *fürt* erőforrásához rendeli hozzá a létrehozáskor.
a "clusterType" értéke "ApplicationInsights" a következőhöz: Application Insights CMK.

**Válasz**

202 elfogadva. Ez egy szabványos Resource Manager-válasz az aszinkron műveletekhez.

Ha bármilyen okból törli a *fürterőforrás* -t, például egy másik névvel vagy clusterType hozza létre, használja ezt a REST API:

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure Monitor adattár (ADX-fürt) üzembe helyezése

A szolgáltatás korai hozzáférési időszaka során a ADX-fürtöt manuálisan kell kiépíteni a termék csapata az előző lépések befejezését követően. Használja a Microsofttal együtt használt csatornát a *fürterőforrás* részleteinek megadásához. A JSON-válasz a GET REST API használatával kérhető le:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**Válasz**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-Id"
    },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

a "principalId" a kezelt Identity szolgáltatás által a *fürterőforrás* számára generált GUID.

> [!IMPORTANT]
> Másolja és tartsa meg a "cluster-id" értéket, mivel a következő lépésekben szüksége lesz rá.


### <a name="grant-key-vault-permissions"></a>Key Vault engedélyek megadása

> [!IMPORTANT]
> Ezt a lépést akkor kell végrehajtani, ha a termékcsoport megerősítését a Microsoft-csatornán keresztül kapta meg, hogy a Azure Monitor adattár (ADX-fürt) kiépítés teljesült. A kiépítés előtt a Key Vault hozzáférési szabályzat frissítése sikertelen lehet.

Frissítse a Key Vault egy új hozzáférési házirenddel, amely engedélyeket biztosít a *fürterőforrás* számára. Ezeket az engedélyeket a rendszer az adattitkosításhoz tartozó Azure Monitor tárolóban használja.
Nyissa meg a Key Vault a Azure Portalban, és kattintson a "hozzáférési szabályzatok", majd a "hozzáférési házirend hozzáadása" lehetőségre egy új szabályzat létrehozásához a következő beállításokkal:

- Kulcs engedélyei: válassza a beolvasás, a betakart kulcs és a kicsomagolási kulcs engedélyeket.
- Rendszerbiztonsági tag kiválasztása: adja meg az előző lépésben a válaszban visszaadott fürt-azonosító értéket.

![Key Vault engedélyek megadása](media/customer-managed-keys/grant-key-vault-permissions.png)

A *Get* engedély szükséges annak ellenőrzéséhez, hogy a Key Vault helyreállítható-e a kulcs védelme érdekében, valamint a Azure monitor adataihoz való hozzáféréshez.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Fürterőforrás frissítése a kulcs-azonosító részleteivel

Ez a lépés a Key Vault későbbi verziójának frissítéseire vonatkozik. Frissítse a *fürterőforrás* Key Vault kulcs- *azonosító* részleteit, hogy a Azure monitor Storage használhassa az új kulcs verzióját. Válassza ki a kulcs aktuális verzióját Azure Key Vault a kulcs azonosítójának részleteinek beszerzéséhez.

![Key Vault engedélyek megadása](media/customer-managed-keys/key-identifier-8bit.png)

Frissítse a *fürterőforrás* KeyVaultProperties a kulcs-azonosító részleteivel.

**Update**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>",
   "identity": { 
     "type": "systemAssigned" 
     }
}
```
A "KeyVaultProperties" a Key Vault kulcs azonosítójának részleteit tartalmazza.

**Válasz**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
       "KeyVaultProperties": {
            KeyVaultUri: "https://key-vault-name.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Munkaterület társítása a *fürt* erőforrásához

> [!NOTE]
> Ezt a lépést **csak** azután kell végrehajtani, hogy a Microsoft-csatornán keresztül megkapta az **Azure monitor adattár (ADX-fürt) kiépítés** után a termékcsoport megerősítését. Ha munkaterületeket rendel hozzá, és a **kiépítés**előtt betölti az adatmennyiséget, a rendszer elveti az adatvesztést, és nem lesz helyreállítható.

Application Insights CMK konfigurálásához kövesse a jelen lépés függelékének tartalmát.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```
A *clusterDefinitionId* az előző lépés válaszában megadott *clusterId* érték.

**Válasz**

```json
{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

A társítás után a rendszer a munkaterületek számára elküldett adatait titkosítja a felügyelt kulccsal.

### <a name="workspace-association-verification"></a>Munkaterület-társítás ellenőrzése
Megtekintheti, hogy egy munkaterület *Custer* -erőforráshoz van-e társítva a [munkaterületek – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) válasz lehetőségre kattintva. A társított munkaterület "clusterResourceId" tulajdonsággal fog rendelkezni a *fürterőforrás* -azonosítóval.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
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
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
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

Azure Monitor a tárterület mindig egy órán belül betartja a legfontosabb engedélyek változásait, általában hamarabb, a tárterület pedig elérhetetlenné válik. A rendszer elveti a *fürterőforrás* -munkaterületekhez kapcsolódó összes adatot, és a lekérdezések sikertelenek lesznek. A korábban betöltött adatmennyiség mindaddig nem érhető el Azure Monitor tárolóban, amíg a kulcs visszavonásra kerül, és a munkaterületek nem törlődnek. A nem elérhető adatokra az adatmegőrzési szabályzat vonatkozik, és a rendszer törli az adatmegőrzési időtartamot.

A tárterület rendszeres időközönként lekérdezi a Key Vault, hogy megpróbálja kibontani a titkosítási kulcsot, és ha a hozzáférés, az adatfeldolgozás és a lekérdezés 30 percen belül folytatódni fog.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotáció

A CMK forgása megköveteli a *fürterőforrás* explicit frissítését az új Azure Key Vault kulcs verziószámával. Ha az új kulccsal szeretné frissíteni a Azure Monitort, kövesse a " *fürterőforrás* frissítése a kulcs- *azonosító* részleteivel" lépést.

Ha Key Vault frissíti a kulcsot, és nem frissíti az új *kulcs-azonosító* részleteit a *fürterőforrás* *-ben, Azure monitor a tárterület továbbra is az előző kulcsot használja.

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

- A CMK funkció a ADX-fürt szintjén támogatott, és dedikált Azure Monitor ADX-fürtöt igényel

- A *fürt* erőforrásainak maximális száma az előfizetésben legfeljebb 5

- A *fürt* erőforrás-társítását csak azután kell végrehajtani, hogy a ADX-fürt kiépítés után megerősítte a termékcsoport megerősítését. A kiépítés előtt elküldett adatkészletek el lesznek távolítva, és nem lesznek helyreállítva.

- A CMK titkosítás a CMK-konfiguráció után az újonnan betöltött adatmennyiségre vonatkozik. A CMK-konfiguráció előtt betöltött adatmennyiség továbbra is a Microsoft-kulccsal lett titkosítva. Az adatlekérdezést a konfigurálás előtt és után zökkenőmentesen is lekérdezheti.

- Ha a munkaterület hozzá van rendelve egy *fürterőforrás* -hez, az nem rendelhető hozzá a *fürterőforrás* szolgáltatáshoz, mert az adatok titkosítva vannak a kulccsal, és nem érhető el a KEK Azure Key Vault-ben való használata nélkül.

- A Azure Key Vault helyreállítható kell konfigurálni. Ezek a tulajdonságok alapértelmezés szerint nem engedélyezettek, és a parancssori felület és a PowerShell használatával kell konfigurálni:

  - A helyreállítható [törlést](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) be kell kapcsolni
  - A [védelem kiürítését](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) be kell kapcsolni ahhoz, hogy védelmet biztosítson a titok/tár kényszerített törlése után is.

- Application Insights és Log Analytics külön *fürterőforrás* szükséges. A *fürterőforrás* típusát a létrehozás ideje határozza meg, ha a "clusterType" tulajdonságot "LogAnalytics" vagy "ApplicationInsights" értékre állítja. A *fürterőforrás* típusa nem módosítható.

- A *fürterőforrás* más erőforráscsoporthoz vagy előfizetéshez való áthelyezése jelenleg nem támogatott.

- A Azure Key Vault, a *fürterőforrás* és a társított munkaterületeknek ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure ad) bérlőben kell lenniük, de különböző előfizetésekben is lehetnek.

- Ha egy másik *fürterőforrás* -erőforráshoz van társítva, akkor sikertelen lesz a munkaterület társítása a *fürt* erőforrásaihoz

## <a name="troubleshooting-and-management"></a>Hibaelhárítás és felügyelet

- Key Vault rendelkezésre állás
    - Normál működés esetén a tárolási gyorsítótárak rövid időtartamra visszamenőlegesen visszaállnak Key Vault a kicsomagoláshoz.
    
    - Átmeneti csatlakoztatási hibák. A tárolók átmeneti hibákat (időtúllépéseket, kapcsolódási hibákat, DNS-hibákat) biztosítanak azáltal, hogy a kulcsok a gyorsítótárban maradnak egy rövid ideig, és ez a rendelkezésre állásban lévő kisméretű visszaírásokat is elvégzi. A lekérdezési és a betöltési képességek megszakítás nélkül folytatódnak.
    
    - Az élő webhely, amely körülbelül 30 percet vesz igénybe, a Storage-fiók elérhetetlenné válását fogja eredményezni. A lekérdezési képesség nem érhető el, és a rendszer az adatvesztés elkerülése érdekében a Microsoft Key használatával több órán keresztül gyorsítótárazza az adatmennyiséget. Ha a rendszer visszaállítja a Key Vault, a lekérdezés elérhetővé válik, és az ideiglenes gyorsítótárazott adatot a rendszer betölti az adattárba, és a CMK titkosítja.

- Ha létrehoz egy *fürterőforrás* -t, és azonnal megadja a KeyVaultProperties, a művelet meghiúsulhat, mivel a hozzáférési házirend nem határozható meg, amíg a rendszer identitása hozzá nem rendeli a *fürterőforrás* -erőforráshoz.

- Ha a meglévő *fürterőforrás* frissítése a KeyVaultProperties és a "Get" kulcs-hozzáférési szabályzat hiányzik a Key Vault, a művelet sikertelen lesz.

- Ha egy munkaterülethez társított *fürterőforrás* törlését kísérli meg, a törlési művelet sikertelen lesz.

- Ezzel az API-val lekérheti az erőforráscsoport összes *fürterőforrás* -erőforrását:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
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
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Ezzel az API-hívással szerezheti be az előfizetéshez tartozó összes *fürterőforrás* -erőforrást:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Válasz**
    
  Ugyanaz a válasz, mint a "*fürterőforrás* egy erőforráscsoport esetében", de az előfizetések hatókörében.
    
- Használja ezt az API-hívást a *fürterőforrás* törléséhez – a *fürterőforrás* törlése előtt törölnie kell az összes társított munkaterületet:

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Válasz**

  200 OK


## <a name="appendix"></a>Függelék

Application Insights ügyfél által felügyelt kulcs (CMK) is támogatott, de érdemes figyelembe vennie a következő változást, amely segít megtervezni az alkalmazás-betekintési összetevők CMK telepítését.

Log Analytics és Application Insights ugyanazt az adattárolási platformot és lekérdezési motort használják. Ezt a két áruházat összekapcsoljuk a Application Insights integrálásával a Log Analyticsba, hogy egyetlen egységesített naplót lehessen tárolni a Azure Monitor a második negyedévében
2020. Ez a változás az alkalmazás betekintési adatait Log Analytics munkaterületekre helyezi át, és lekérdezéseket, elemzéseket és egyéb tökéletesítéseket tesz lehetővé, miközben a munkaterület CMK konfigurációja a munkaterületen is érvényes lesz a Application Insights adataira.

> [!NOTE]
> Ha az integráció előtt nem kell CMK telepítenie az alkalmazás-betekintési adataihoz, javasoljuk, hogy várjon Application Insights CMK, mivel az ilyen központi telepítések megszakadnak az integráció során, és újra kell konfigurálnia a CMK a bejelentkezés után. Elemzési munkaterület. Az 1 TB/nap minimum a fürt szintjén érvényes, amíg a konszolidáció befejeződik a második negyedévben, Application Insights és Log Analytics külön fürtöket igényelnek.

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK-konfiguráció

Application Insights CMK konfigurációja megegyezik az ebben a cikkben ismertetett folyamattal, beleértve a korlátozásokat és a hibaelhárítást, kivéve a következő lépéseket:

- *Fürterőforrás* létrehozása

- Összetevő hozzárendelése *fürterőforrás* -erőforráshoz

A Application Insights CMK konfigurálásakor ezeket a lépéseket a fent felsorolt lépések helyett kell használni.

### <a name="create-a-cluster-resource"></a>*Fürterőforrás* létrehozása

Ez az erőforrás köztes identitás-kapcsolatként használatos a Key Vault és az összetevők között. MIUTÁN megkapta az előfizetések engedélyezési feladatainak megerősítését, hozzon létre egy Log Analytics *fürterőforrás* azon a régión, ahol az összetevők találhatók. A *fürterőforrás* típusát a létrehozás ideje határozza meg, ha a *ClusterType* tulajdonságot *LogAnalytics*vagy *ApplicationInsights*értékre állítja be. Application Insights CMK *ApplicationInsights* kell lennie. A *clusterType* beállítás a konfiguráció után nem módosítható.

**Létrehozás**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Válasz**

A rendszer a *fürt* erőforrásához a létrehozáskor rendeli hozzá az identitást.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
a "principalId" a felügyelt identitási szolgáltatás által létrehozott GUID.

> [!IMPORTANT]
> Másolja és tartsa meg a "cluster-id" értéket, mivel a következő lépésekben szüksége lesz rá.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Összetevő hozzárendelése *fürterőforrás* -erőforráshoz összetevők használatával [– Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
a "clusterDefinitionId" az előző lépés válaszában megadott "clusterId" érték.
a "Kind" példa a "web".

**Válasz**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
a "clusterDefinitionId" az ehhez az összetevőhöz társított *fürterőforrás* -azonosító.

A társítás után a rendszer a felügyelt kulccsal titkosítja az összetevőknek küldendő adatait.
