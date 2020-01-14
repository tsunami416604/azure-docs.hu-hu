---
title: Azure Monitor ügyfél által felügyelt kulcs konfigurálása
description: Információk és lépések az ügyfél által felügyelt kulcs (CMK) konfigurálásához a Log Analytics-munkaterületeken lévő adatok Azure Key Vault kulcs használatával történő titkosításához.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/11/2020
ms.openlocfilehash: 04bda5b016234f96d4bef7796799f2526296dd26
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932753"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor ügyfél által felügyelt kulcs konfigurálása 

Ez a cikk háttér-információkat és lépéseket tartalmaz a Log Analytics-munkaterületekhez és a Application Insights-összetevőkhöz tartozó ügyfél által felügyelt kulcsok (CMK) konfigurálásához. A konfigurálást követően a munkaterületekre vagy összetevőkre eljuttatott összes adatfájl titkosítva van a Azure Key Vault kulccsal.

Javasoljuk, hogy a konfiguráció előtt tekintse át [az alábbi korlátozásokat és korlátozásokat](#Limitations and constraints) .

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- Azure Monitor CMK egy korai hozzáférési szolgáltatás, és engedélyezve van a regisztrált előfizetések esetében.

- Az ebben a cikkben ismertetett CMK üzembe helyezés éles környezetben történik, és ez a funkció a korai hozzáférési szolgáltatásként is támogatott.

- A CMK képesség egy dedikált adattár-fürtön érhető el, amely egy Azure Adatkezelő (ADX) fürt, és alkalmas az olyan ügyfelek számára, akik naponta 1 TB-ot küldenek. 

- A CMK díjszabási modell jelenleg nem érhető el, és nem szerepel ebben a cikkben. A dedikált ADX-fürt díjszabási modellje a naptári év második negyedévében (CY) 2020, és minden meglévő CMK-telepítésre érvényes lesz.

- Ez a cikk Log Analytics munkaterületek CMK-konfigurációját ismerteti. A Application Insights-összetevők CMK is támogatott ebben a cikkben, míg a különbségek a függelékben vannak felsorolva.

> [!NOTE]
> Log Analytics és Application Insights ugyanazt az adattárolási platformot és lekérdezési motort használják.
> Ezt a két áruházat összekapcsoljuk a Application Insights integrálásával a Log Analyticsba, hogy egyetlen egységesített naplót hozzunk létre a Azure Monitor alatt. Ezt a változást a 2020-es naptári év második negyedévére tervezték. Ha ezt követően nem kell CMK telepítenie az alkalmazás betekintési adataihoz, javasoljuk, hogy várjon a konszolidáció befejezésére, mert az ilyen központi telepítések megszakadnak a konszolidációban, és újra kell konfigurálnia a CMK az áttelepítés után a naplóba Elemzési munkaterület. A minimális napi 1 TB a fürt szintjén érvényes, míg a konszolidáció nem fejeződik be a második negyedévben Application Insights és Log Analytics külön fürtökre van szükség.

## <a name="customer-managed-key-cmk-overview"></a>Ügyfél által felügyelt kulcs (CMK) áttekintése

A inaktív adatok [titkosítása](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) a szervezetek közös adatvédelmi és biztonsági követelménye. Lehetővé teheti, hogy az Azure teljes mértékben kezelhesse a titkosítást, míg számos különböző lehetőség áll rendelkezésre a titkosítási vagy titkosítási kulcsok szoros kezeléséhez.

A Azure Monitor adattár biztosítja, hogy az Azure által felügyelt kulcsokkal titkosított összes inaktív adatok az Azure Storage szolgáltatásban tárolódnak. A Azure Monitor a saját, az [Azure Key vaultban](https://docs.microsoft.com/azure/key-vault/key-vault-overview)tárolt kulcsával is lehetővé teszi az adatok titkosítását, amelyet a rendszerhez rendelt [felügyelt identitásos](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) hitelesítéssel érhet el. Ez a kulcs lehet [szoftveres vagy hardveres HSM-védelemmel ellátott](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
A titkosítás Azure Monitor használata azonos az [Azure Storage-titkosítás](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) működésének módjával.

A Azure Monitor Storage Key Vault a becsomagolási és a kicsomagolási műveletek esetében 6 – 60 másodperc közötti gyakoriságot biztosít. Azure Monitor Storage  
egy órán belül mindig tiszteletben tartja a legfontosabb engedélyek változásait.

Az elmúlt 14 napban betöltött adatok gyors gyorsítótárban (SSD-vel) is megmaradnak a hatékony lekérdezési motor működéséhez. Ezek az adatforgalom az CMK-konfigurációtól függetlenül titkosítva marad a Microsoft-kulcsoknál, de dolgozunk azon, hogy az SSD-t a CMK korai 2020-mel titkosítsa.

## <a name="how-cmk-works-in-azure-monitor"></a>Hogyan működik a CMK Azure Monitor

Azure Monitor a rendszer által hozzárendelt felügyelt identitást használja a Azure Key Vault elérésének biztosításához. A rendszer által hozzárendelt felügyelt identitás csak egyetlen Azure-erőforráshoz társítható. A fürt szintjén a Azure Monitor adattár (ADX-fürt) identitása támogatott, és ez azt határozza meg, hogy a CMK képesség egy dedikált ADX-fürtön van-e továbbítva. A CMK több munkaterületen való támogatásához egy új Log Analytics erőforrás (*fürt*) közbenső identitás-kapcsolatként működik a Key Vault és a log Analytics munkaterületek között. Ez a fogalom megfelel a rendszer által hozzárendelt identitási megkötésnek, és az identitást a ADX-fürt és a Log Analytics *fürterőforrás* között tartja fenn *,* míg az összes társított munkaterület adatai védettek a Key Vault kulccsal. Az alátét ADX fürt tárterülete a felügyelt identitást használja, amelyet\'s társít a *fürterőforrás* -hez, hogy a Azure Key Vault a Azure Active Directory használatával hitelesítse és hozzáférhessen.

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

- A KEK soha nem hagyja el a Key Vault és a HSM-kulcsok esetében soha nem hagy hardvert.

- Az Azure Storage a *fürterőforrás* -hez társított felügyelt identitást használja a Azure Key Vault hitelesítésére és elérésére Azure Active Directory használatával.

- Az olvasási/írási műveletek esetében az Azure Storage kéréseket küld a Azure Key Vaultnak a titkosítási és visszafejtési műveletek elvégzéséhez.

## <a name="cmk-provisioning-procedure"></a>CMK-létesítési eljárás

A kiépítési folyamat a következő lépéseket tartalmazza:

1. Előfizetés-engedélyezési lista – ez a korai hozzáférési szolgáltatáshoz szükséges
2. Azure Key Vault létrehozása és a kulcs tárolása
3. *Fürterőforrás* létrehozása
4. Engedélyek megadása a Key Vault számára
5. Azure Monitor adattár (ADX-fürt) üzembe helyezése
6. Log Analytics munkaterületek társítása

Az eljárás jelenleg nem támogatott a felhasználói felületen, a kiépítési folyamat pedig REST APIon keresztül történik.

> [!IMPORTANT]
> Minden API-kérésnek tartalmaznia kell egy tulajdonosi engedélyezési jogkivonatot a kérelem fejlécében.

Példa:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Ahol a *eyJ0eXAiO....* a teljes engedélyezési jogkivonatot jelöli. 

A jogkivonatot a következő módszerek egyikével is beszerezheti:

1. [Alkalmazásregisztrációk](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) metódus használata.

2. Az Azure Portalon
    1. Navigáljon a Azure Portal a "fejlesztői eszköz (F12)
    1. Keresse meg az engedélyezési karakterláncot az "igénylési fejlécek" alatt a "batch? API-version" példányok egyikében. A következőhöz hasonló: "Authorization: tulajdonos \<token\>". 
    1. Másolja ki és adja hozzá az API-híváshoz az alábbi példákban.

3. Navigáljon az Azure REST dokumentációs webhelyére. Kattintson a "kipróbálás" elemre bármely API-ban, és másolja a tulajdonosi jogkivonatot.

### <a name="subscription-whitelisting"></a>Előfizetés-engedélyezési lista

A CMK képesség egy korai hozzáférési szolgáltatás. Azokat az előfizetéseket, amelyekhez *fürterőforrás* -létrehozási tervet kíván létrehozni, az Azure-termékcsoport előzetes engedélyezési listának kell lennie. Az előfizetések azonosítóinak megadásához használja a Microsoft névjegyeit.

> [!WARNING]
> A CMK-képesség regionális. A Azure Key Vault, a Storage-fióknak, a *fürterőforrás* -nek és a társított log Analytics-munkaterületnek ugyanabban a régióban kell lennie, de különböző előfizetésekben lehet.

### <a name="storing-encryption-key-kek"></a>Titkosítási kulcs (KEK) tárolása

Hozzon létre egy Azure Key Vault erőforrást, majd hozza létre vagy importálja az adattitkosításhoz használandó kulcsot.

A Azure Key Vaultt helyreállítható kell konfigurálni a kulcs védelme érdekében, valamint a Azure monitor adataihoz való hozzáféréshez.

Ezek a beállítások a CLI-n és a PowerSell-on keresztül érhetők el:
- A helyreállítható [törlést](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) be kell kapcsolni
- A [védelem kiürítését](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) be kell kapcsolni ahhoz, hogy védelmet biztosítson a titok/tár kényszerített törlése után is.

### <a name="create-cluster-resource"></a>*Fürterőforrás* létrehozása

Ez az erőforrás köztes identitás-kapcsolatként használatos a Key Vault és a munkaterületek között. Csak az előfizetések engedélyezési jóváhagyása után hozzon létre egy Log Analytics *fürterőforrás* azon a régión, ahol a munkaterületek találhatók. Application Insights és Log Analytics külön fürterőforrás szükséges. A fürterőforrás típusát a létrehozás ideje határozza meg, ha a "clusterType" tulajdonságot "LogAnalytics" vagy "ApplicationInsights" értékre állítja. A fürterőforrás típusa nem módosítható.

**Létrehozás**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"   //Should be "ApplicationInsights" for Application Insights CMK
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
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"    //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```
> [!IMPORTANT]
> Másolja és tartsa meg a "cluster-id"-t, mert a következő lépésekben szüksége lesz rá.

Ha bármilyen okból törölni szeretné a *fürterőforrás* (például egy másik néven hozza létre), használja ezt az API-hívást:

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Key Vault engedélyek megadása

Frissítse a Key Vaultt, és adja hozzá a fürterőforrás hozzáférési szabályzatát. Ezután a rendszer a Key Vaulthoz tartozó engedélyeket propagálja az adattitkosításhoz használandó, Azure Monitor tárterületre.
Nyissa meg a Key Vault a Azure Portalban, és kattintson a "hozzáférési szabályzatok", majd a "hozzáférési házirend hozzáadása" lehetőségre egy új szabályzat létrehozásához a következő beállításokkal:

- Kulcs engedélyei: válassza a beolvasás, a betakart kulcs és a kicsomagolási kulcs engedélyeket.

- Rendszerbiztonsági tag kiválasztása: adja meg a fürt azonosítóját, amely az előző lépés válaszában szereplő "clusterId" érték.

![Key Vault engedélyek megadása](media/customer-managed-keys/grant-key-vault-permissions.png)

A *Get* engedély szükséges annak ellenőrzéséhez, hogy a Key Vault helyreállítható-e a kulcs védelme érdekében, valamint a Azure monitor adataihoz való hozzáféréshez.

Néhány percet vesz igénybe, amíg a *fürterőforrás* el nem terjed a Azure Resource Manager. Ha a hozzáférési házirendet közvetlenül a *fürt* erőforrásainak létrehozása után konfigurálja, átmeneti hiba léphet fel. Ebben az esetben próbálkozzon újra néhány perc múlva.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Fürterőforrás frissítése a kulcs-azonosító részleteivel

Ez az eljárás akkor is érvényes, ha a kulcs új verzióját hozza létre.

Frissítse a fürterőforrás Azure Key Vault kulcs-azonosító részleteit, hogy a Azure Monitor Storage használhassa az új kulcs verzióját. Válassza ki a kulcs aktuális verzióját Azure Key Vault a kulcs azonosítójának részleteinek beszerzéséhez:

![Key Vault engedélyek megadása](media/customer-managed-keys/key-identifier-8bit.png)

Frissítse a *fürterőforrás* KeyVaultProperties a kulcs-azonosító részleteivel.

**Update**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": {     //Key Vault key identifier details taken from Key identifier URI
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

**Válasz**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
       "KeyVaultProperties": {     // Key Vault key identifier
            KeyVaultUri: "https://key-vault-name.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"    //Example: Switzerland North
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure Monitor adattár (ADX-fürt) üzembe helyezése

A szolgáltatás korai hozzáférési időszaka során a ADX-fürtöt manuálisan kell kiépíteni a termék csapata az előző lépések befejezését követően. Használja a Microsofttal együtt használt csatornát a következő részletek megadásához:

1. Erősítse meg, hogy a fenti lépések hol befejeződtek

2. A fürterőforrás API-válasza. a Get API-hívással bármikor lekérhető.

**A *FÜRTERŐFORRÁS* azonosítójának beolvasása**

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
       "KeyVaultProperties": {    // Key Vault key identifier
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

**Munkaterület hozzárendelése a *fürt* erőforrásaihoz [munkaterületek használatával – API létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate)**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2015-11-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "source": "Azure",
    "customerId": "<workspace-id>",    //Available in Azure portal under Log Analytics workspace Overview section
    "features": {
      "clusterDefinitionId": "<cluster-id>"    //It's the "clusterId" value provided in the respond from the previous step 
    }
  },
  "id": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>",
  "name": "<workspace-name>",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "<region-name>"
}
```

**Válasz**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-id",
    "retentionInDays": value,
    "features": {
      "legacy": value,
      "searchVersion": value,
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    },
    "workspaceCapping": {
      "dailyQuotaGb": value,
      "quotaNextResetTime": "timeStamp",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

A társítás után a rendszer a munkaterületek számára elküldett adatait titkosítja a felügyelt kulccsal.

## <a name="cmk-kek-revocation"></a>CMK (KEK) visszavonás

Azure Monitor a tárterület minden esetben egy órán belül betartja a kulcsfontosságú engedélyek változásait, általában hamarabb, a tárterület pedig elérhetetlenné válik – a *fürterőforrás* -munkaterülethez kapcsolódó összes adat el lesz dobva, és a lekérdezések sikertelenek lesznek. A korábban betöltött adatmennyiség mindaddig nem érhető el Azure Monitor tárolóban, amíg a kulcs visszavonásra kerül, és a munkaterületek nem törlődnek. A nem elérhető adatokra az adatmegőrzési szabályzat vonatkozik, és a rendszer törli az adatmegőrzési időtartamot.

A tárterület rendszeres időközönként lekérdezi a Key Vault, hogy megpróbálja kibontani a titkosítási kulcsot, és ha a hozzáférés, az adatfeldolgozás és a lekérdezés 30 percen belül folytatódni fog.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotáció

A CMK forgása megköveteli a fürterőforrás explicit frissítését az új Azure Key Vault kulcs verziószámával. Ha az új kulccsal szeretné frissíteni a Azure Monitort, kövesse a " *fürterőforrás* frissítése a kulcs-azonosító részleteivel" lépést.

-   Ha a kulcsot a Key Vaultban elforgatja, és a Azure Monitor röviddel azután nem frissíti az új verziót, a kulcsot Azure Monitor Storage nem fogja tudni elérni.

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

- A CMK funkció a ADX-fürt szintjén támogatott, és dedikált Azure Monitor ADX-fürtöt igényel

- A *fürt* erőforrásainak maximális száma az előfizetésben legfeljebb 5

- A *fürt* erőforrás-társítását csak azután kell végrehajtani, hogy a ADX-fürt kiépítés után megerősítte a termékcsoport megerősítését. A kiépítés előtt elküldett adatkészletek el lesznek távolítva, és nem lesznek helyreállítva.

- A CMK titkosítás a CMK-konfiguráció után az újonnan betöltött adatmennyiségre vonatkozik. A CMK-konfiguráció előtt betöltött adatmennyiség továbbra is a Microsoft-kulccsal lett titkosítva. Az adatlekérdezést a konfigurálás előtt és után zökkenőmentesen is lekérdezheti.

- A CMK képesség regionális – a Azure Key Vault, a *fürterőforrás* és a társított munkaterületeknek ugyanabban a régióban kell lenniük, de különböző előfizetésekben is lehetnek.

- Ha a munkaterület hozzá van rendelve egy *fürterőforrás* -hez, az nem rendelhető hozzá a *fürterőforrás* szolgáltatáshoz, mert az adatok titkosítva vannak a kulccsal, és nem érhető el a KEK Azure Key Vault-ben való használata nélkül.

- A Azure Key Vault helyreállítható kell konfigurálni. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve:

  - A [Soft delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) be van kapcsolva
  - A "nem végleges törlés" beállítás bekapcsolva állapotba kerül a titkos vagy a tároló törlésének kényszerítése után is.

- Application Insights és Log Analytics külön *fürterőforrás* szükséges. A *fürterőforrás* típusát a létrehozás ideje határozza meg, ha a "clusterType" tulajdonságot "LogAnalytics" vagy "ApplicationInsights" értékre állítja. A *fürterőforrás* típusa nem módosítható.

- A *fürterőforrás* más erőforráscsoporthoz vagy előfizetéshez való áthelyezése jelenleg nem támogatott.

- Ha a *fürterőforrás* egy másik bérlőn van, a *fürt* erőforrás-társítása sikertelen lesz.

-   Ha egy másik *fürterőforrás* -erőforráshoz van társítva, akkor sikertelen lesz a munkaterület társítása a *fürt* erőforrásaihoz

## <a name="troubleshooting-and-management"></a>Hibaelhárítás és felügyelet

- Key Vault rendelkezésre állás
    - Normál működés esetén a tárolási gyorsítótárak rövid időtartamra visszamenőlegesen visszaállnak Key Vault a kicsomagoláshoz.
    
    - Átmeneti csatlakoztatási hibák. A tárolók átmeneti hibákat (időtúllépéseket, kapcsolódási hibákat, DNS-hibákat) biztosítanak azáltal, hogy rövid idő alatt lehetővé teszik a kulcsok gyorsítótárban való tartózkodását
            – A betöltés megszakítás nélkül folytatódik
    
    - Az élő hely körülbelül 30 percet vesz igénybe, mert a Storage-fiók elérhetetlenné válik.
            -   **lekérdezési** képesség nem érhető el **– a betöltés – az** adatvesztés elkerülése érdekében a Microsoft Key használatával több órán át gyorsítótárazza az adatmennyiséget. Ha a rendszer visszaállítja a Key Vault, a lekérdezés elérhetővé válik, és az ideiglenes gyorsítótárazott adatot a rendszer betölti az adattárba, és a CMK titkosítja.

- Ha létrehoz egy *fürterőforrás* -t, és azonnal megadja a KeyVaultProperties, a művelet meghiúsulhat, mivel a hozzáférési házirend nem határozható meg, amíg a rendszer identitása hozzá nem rendeli a *fürterőforrás* -erőforráshoz.

- Ha a meglévő *fürterőforrás* frissítése a KeyVaultProperties és a "Get" kulcs-hozzáférési szabályzat hiányzik a Key Vault, a művelet sikertelen lesz.

- Ha egy munkaterülethez társított *fürterőforrás* törlését kísérli meg, a törlési művelet sikertelen lesz.

- Erőforráscsoport összes *fürterőforrás* -erőforrásának beolvasása:

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
         "KeyVaultProperties": {    // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net",
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

- Az előfizetés összes *fürterőforrás* -erőforrásának beolvasása

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
**Válasz**
    
Ugyanaz a válasz, mint a "*fürterőforrás* egy erőforráscsoport esetében", de az előfizetések hatókörében.
    
- *Fürterőforrás* törlése – a *fürt* erőforrásának törlése előtt törölnie kell az összes társított munkaterületet:

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

**Válasz**

200 OK


## <a name="appendix"></a>Függelék

Ez a cikk Application Insights ügyfél által felügyelt kulcsra (CMK) vonatkozik, de érdemes figyelembe vennie a közelgő változást, amely segít megtervezni az alkalmazás betekintési összetevőihez tartozó CMK telepítését.

Log Analytics és Application Insights ugyanazt az adattárolási platformot és a lekérdezési motort használja – ezeket a két áruházat a Application Insights integrálásával együtt a Log Analyticsba helyezve egyetlen egységes naplófájl-tárolót biztosítanak Azure Monitor a második negyedév
2020. Ez a változás az alkalmazás betekintési adatait Log Analytics munkaterületekre helyezi át, és lekérdezéseket, elemzéseket és egyéb tökéletesítéseket tesz lehetővé, miközben a munkaterület CMK konfigurációja a munkaterületen is érvényes lesz a Application Insights adataira.

> [!NOTE]
> Ha ezt követően nem kell CMK telepítenie az alkalmazás betekintési adataihoz, javasoljuk, hogy várjon a konszolidáció befejezésére, mert az ilyen központi telepítések megszakadnak a konszolidációban, és újra kell konfigurálnia a CMK az áttelepítés után a naplóba Elemzési munkaterület. A minimális napi 1 TB a fürt szintjén érvényes, míg a konszolidáció nem fejeződik be a második negyedévben Application Insights és Log Analytics külön fürtökre van szükség.

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK-konfiguráció

Application Insights CMK konfigurációja megegyezik az ebben a cikkben ismertetett folyamattal, beleértve a korlátozásokat és a hibaelhárítást, kivéve a következő lépéseket:

- *Fürterőforrás* létrehozása

- Összetevő hozzárendelése *fürterőforrás* -erőforráshoz

A Application Insights CMK konfigurálásakor ezeket a lépéseket a fent felsorolt lépések helyett kell használni.

### <a name="create-a-cluster-resource"></a>*Fürterőforrás* létrehozása

Ez az erőforrás köztes identitás-kapcsolatként használatos a Key Vault és az összetevők között. MIUTÁN megkapta az előfizetések engedélyezési feladatainak megerősítését, hozzon létre egy Log Analytics fürterőforrás azon a régión, ahol az összetevők találhatók. A fürterőforrás típusát a létrehozás ideje határozza meg, ha a *clusterType* tulajdonságot *LogAnalytics*vagy *ApplicationInsights*értékre állítja be. Application Insights CMK *ApplicationInsights* kell lennie. A *clusterType* beállítás a konfiguráció után nem módosítható.

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
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster - copy it since you need it for Key Vault and components association 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="associate-a-component-to-a-cluster-resource"></a>Összetevő hozzárendelése *fürterőforrás* -erőforráshoz

```rst
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Insights/components/{component-name}?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id" //It's the "clusterId" value provided in the respond from the previous step
  },
  "location": "region-name",
  "kind": "component-type",
}
```

**Válasz**

```json
{
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.insights/components/{component-name}",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id" //The Cluster resource ID that is associated to this component
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

A társítás után a rendszer a felügyelt kulccsal titkosítja az összetevőknek küldendő adatait.
