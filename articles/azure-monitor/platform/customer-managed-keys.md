---
title: Az Azure Monitor ügyféláltal felügyelt kulcskonfigurációja
description: Információ és lépések az Ügyfél által felügyelt kulcs (CMK) konfigurálásához a Log Analytics-munkaterületeken lévő adatok titkosításához egy Azure Key Vault-kulcs használatával.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 03/26/2020
ms.openlocfilehash: 18c926d16319eb8a8736a51d5f10e434b94d0ebe
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582507"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Az Azure Monitor ügyféláltal felügyelt kulcskonfigurációja 

Ez a cikk háttér-információkat és lépéseket tartalmaz az Ügyfél által felügyelt kulcsok (CMK) konfigurálásához a Log Analytics-munkaterületekhez és az Application Insights-összetevőkhöz. Konfigurálás után a munkaterületekre vagy összetevőkre küldött adatok titkosítva vannak az Azure Key Vault-kulccsal.

Javasoljuk, hogy a konfiguráció előtt tekintse át az alábbi [korlátozásokat és korlátozásokat.](#limitations-and-constraints)

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- Az Azure Monitor CMK egy korai hozzáférésű funkció, amely regisztrált előfizetésekhez engedélyezett.

- A CMK-telepítés ebben a cikkben leírt szállított éles minőség és támogatott, mint ilyen, bár ez egy korai hozzáférési funkció.

- A CMK-funkció egy dedikált adattár-fürtön érhető el, amely egy Azure Data Explorer (ADX) fürt, és alkalmas a naponta vagy több 1 TB-ot küldő ügyfelek számára. 

- A CMK-díjszabási modell jelenleg nem érhető el, és ez a cikk nem foglalkozik vele. A dedikált ADX-fürt díjszabási modellje a 2020-as naptári év második negyedévében várható, és minden meglévő CMK-telepítésre vonatkozik.

- Ez a cikk a CmK-konfiguráció log analytics-munkaterületek. CmK az Application Insights-összetevők is támogatja ezt a cikket, míg a különbségek a függelékben felsorolt.

> [!NOTE]
> A Log Analytics és az Application Insights ugyanazt az adattár-platformot és lekérdezési motort használja.
> Ezt a két áruházat az Application Insights log analytics-be való integrálásával hozzuk létre az Azure Monitor egyetlen egyesített naplótárolójának létrehozásához. Ez a változás a tervek szerint a 2020-as naptári év második negyedévére történik. Ha addigra nem kell központi telepítést telepítenie az Application Insights-adatokhoz, javasoljuk, hogy várjon a konszolidáció befejezésére, mivel az ilyen központi telepítések megzavarják a konszolidációt, és újra kell konfigurálnia a CMK-t a Log Analytics munkaterületre való áttelepítés után. A napi 1 TB-os minimum a fürt szintjén érvényes, és amíg a konszolidáció a második negyedévben befejeződik, az Application Insights és a Log Analytics külön fürtöket igényel.

## <a name="customer-managed-key-cmk-overview"></a>Ügyfél által felügyelt kulcs (CMK) – áttekintés

[Az inaktív titkosítás](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) gyakori adatvédelmi és biztonsági követelmény a szervezeteknél. Lehetővé teheti, hogy az Azure teljes mértékben kezelje az inaktív titkosítást, miközben számos lehetőség közül választhat a titkosítás vagy a titkosítási kulcsok szoros kezelésére.

Az Azure Monitor adattár biztosítja, hogy az Azure által kezelt kulcsok használatával inkulált összes adat az Azure Storage-ban tárolva. Az Azure Monitor adattitkosítási lehetőséget is biztosít az [Azure Key Vaultban](https://docs.microsoft.com/azure/key-vault/key-vault-overview)tárolt saját kulcs használatával, amely a rendszer által hozzárendelt [felügyelt identitás-hitelesítés](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) használatával érhető el. Ez a kulcs lehet [szoftver vagy hardver-HSM védett](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Az Azure Monitor titkosítási használata megegyezik az [Azure Storage titkosítási](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) működik.

Az Azure Monitor Storage által a Key Vaulthoz hozzáférő gyakoriság 6 és 60 másodperc között van.Az Azure Monitor Storage mindig egy órán belül tiszteletben tartja a kulcsfontosságú engedélyek változásait.

Az elmúlt 14 napban beadott adatok at is a rendszer a hatékony lekérdezési motor működése érdekében a gyorsítótárban (SSD-vel támogatott) is tárolják. Ezek az adatok továbbra is titkosítva maradnak a Microsoft-kulcsokkal, függetlenül a CMK konfigurációjától, de azon dolgozunk, hogy az SSD-t 2020 első felében titkosítsuk a CMK-vel.

## <a name="how-cmk-works-in-azure-monitor"></a>Hogyan működik a CMK az Azure Monitorban?

Az Azure Monitor a rendszer által hozzárendelt felügyelt identitást használja az Azure Key Vault hoz való hozzáférés biztosításához.A rendszer által hozzárendelt felügyelt identitás csak egyetlen Azure-erőforráshoz társítható. Az Azure Monitor adattárának (ADX-fürt) identitása fürtszinten támogatott, és ez azt diktálja, hogy a CMK-képesség egy dedikált ADX-fürtön legyen. A CMK több munkaterületen való támogatásához egy új Log Analytics-erőforrás (*fürt*) köztes identitáskapcsolatként működik a Key Vault és a Log Analytics-munkaterületek között. Ez a koncepció megfelel a rendszer által hozzárendelt identitásmegkötésnek, és az identitás megmarad az ADX-fürt és a Log *Analytics-fürt* erőforrás között, míg az összes társított munkaterület adatait a Key Vault-kulcs védi. Az alávetítéses ADX-fürttároló a\' *fürterőforráshoz* társított felügyelt identitást használja az Azure Key Vault hitelesítéséhez és eléréséhez az Azure Active Directoryn keresztül.

![CMK – áttekintés](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Az ügyfél kulcstartója.
2.    Az ügyfél Log *Cluster* Analytics fürterőforrása, amely nek hozzáférése van a Key Vaulthoz – az identitás az adattár (ADX-fürt) szintjén támogatott.
3.    Az Azure Monitor dedikált ADX-fürt.
4.    A CMK-titkosításhoz *fürterőforráshoz* társított vevői munkaterületek.

## <a name="encryption-keys-management"></a>Titkosítási kulcsok kezelése

A storage-adattitkosításban három féle kulcs szerepel:

- **KEK** - kulcstitkosítási kulcs (CMK)
- **AEK** - Fiók titkosítási kulcsa
- **DEK** - Adattitkosítási kulcs

A következő szabályok érvényesek:

- Az ADX tárfiókok egyedi titkosítási kulcsot hoznak létre minden tárfiókhoz, amely az AEK néven ismert.

- Az AEK a DEK-k származtatására szolgál, amelyek a lemezre írt adatok minden egyes blokkjának titkosítására szolgálnak.

- Amikor konfigurálja a kulcsot a Key *Cluster* Vaultban, és hivatkozik rá a fürterőforrásban, az Azure Storage kéréseket küld az Azure Key Vaultnak az AEK becsomagolásához és kicsomagolásához az adattitkosítási és visszafejtési műveletek végrehajtásához.

- A KEK soha nem hagyja el a Key Vault ot, és HSM-kulcs esetén soha nem hagyja el a hardvert.

- Az Azure Storage a *fürterőforráshoz* társított felügyelt identitást használja az Azure Key Vault hitelesítéséhez és eléréséhez az Azure Active Directoryn keresztül.

## <a name="cmk-provisioning-procedure"></a>CMK-kiépítési eljárás

Az Application Insights CMK-konfigurációjához kövesse a függelék tartalmát a 3.

1. Előfizetésengedélyezési lista – ez szükséges ehhez a korai hozzáférési funkcióhoz
2. Az Azure Key Vault létrehozása és a kulcs tárolása
3. *Fürterőforrás* létrehozása
4. Az Azure Monitor adattárának (ADX-fürt) kiépítése
5. Engedélyek megadása a Key Vaultszámára
6. A Log Analytics-munkaterületek társítása

Az eljárás jelenleg nem támogatott a felhasználói felületen, és a létesítési folyamat REST API-n keresztül történik.

> [!IMPORTANT]
> Minden API-kérelemnek tartalmaznia kell egy tulajdonosi engedélyezési jogkivonatot a kérelem fejlécében.

Példa:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Ahol *az eyJ0eXAiO...* a teljes engedélyezési jogkivonatot jelöli. 

A jogkivonatot az alábbi módszerek egyikével szerezheti be:

1. Használja [az alkalmazásregisztrációk](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) módszert.
2. Az Azure Portalon
    1. Navigálás az Azure Portalra a "fejlesztői eszköz (F12) segítségével
    1. Keresse meg az engedélyezési karakterláncot a "Fejlécek kérése" alatt a "batch?api-version" példányok egyikében. Úgy néz ki, mint: \<\>"engedély: bemutatóra token ". 
    1. Másolja és adja hozzá az API-híváshoz az alábbi példák szerint.
3. Nyissa meg az Azure REST dokumentációs webhelyét. Nyomja meg a "Próbálja ki" gombot bármely API-n, és másolja a tulajdonosi jogkivonatot.

### <a name="subscription-whitelisting"></a>Előfizetés engedélyezési listája

A CMK-képesség egy korai hozzáférési funkció. Azok az előfizetések, *amelyeknél fürterőforrásokat* kíván létrehozni, előzetesen az Azure termékcsoportnak előzetesen szerepelniük kell. Az előfizetési azonosítók megadásához használja a Microsoftba szóló névjegyeit.

> [!IMPORTANT]
> A CMK-képesség regionális. Az Azure Key Vault, *a fürterőforrás* és a kapcsolódó Log Analytics-munkaterületek ugyanabban a régióban kell lenniük, de lehetnek különböző előfizetésekben.

### <a name="storing-encryption-key-kek"></a>Titkosítási kulcs tárolása (KEK)

Hozzon létre vagy használjon egy Azure Key Vault, amely már létre kell hoznia, vagy az adatok titkosításához használt kulcs importálása. Az Azure Key Vault kell konfigurálni, mint helyreállítható, hogy megvédje a kulcsot, és az adatokhoz való hozzáférés az Azure Monitorban. Ezt a konfigurációt a Key Vault tulajdonságai alatt ellenőrizheti, a *helyreállítható törlés* és *a kiürítési védelem* is engedélyezve kell lennie.

Ezek a beállítások a CLI-n és a PowerShellen keresztül érhetők el:
- [Helyreállítható törlés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Tisztítsuk meg](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) a védelmet a titkos / trezor erőtörlése ellen még a puha törlés után is

### <a name="create-cluster-resource"></a>*Fürterőforrás* létrehozása

Ez az erőforrás a Key Vault és a Log Analytics-munkaterületek közötti köztes identitáskapcsolatként használatos. Miután megerősítést kapott arról, hogy az előfizetések szerepelnek, hozzon létre egy Log *Analytics-fürterőforrást* a munkaterületei nek helye szerinti régióban. Az Application Insights és a Log Analytics külön *fürterőforrás-típusokat* igényel. A *fürterőforrás* típusa a létrehozás időpontjában a *clusterType* tulajdonság *loganalytics*vagy *ApplicationInsights*beállításával van definiálva. A fürt erőforrástípusa után nem módosítható.

Az Application Insights CMK konfiguráció, kövesse a függelék tartalmát.

Fürterőforrás létrehozásakor meg kell *adnia* a kapacitásfoglalási szintet (termékhalmaz). A kapacitásfoglalási szint napi 1000 és 2000 GB között lehet, és később 100 lépésben frissítheti azt. Ha napi 2000 GB-nál magasabb kapacitásfoglalási szintre van szüksége, az engedélyezéshez elérje microsoftos partnerét. Ez a tulajdonság jelenleg nincs hatással a számlázásra – a dedikált fürt díjszabási modelljének bevezetése után a számlázás minden meglévő CMK-telepítésre vonatkozik.

**Létrehozás**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
Az identitás a *létrehozás* időpontjában van hozzárendelve a fürterőforráshoz.

**Válasz**

202 Elfogadva. Ez egy szabványos Erőforrás-kezelő válasz aszinkron műveletekhez.

>[!Important]
> Az aluli ADX-fürt kiépítése egy ideig tart. Ellenőrizheti a létesítési állapot, amikor a GET REST API-hívás végrehajtása a *fürt* erőforrás, és a *provisioningState* érték vizsgálata. A *kiépítés során a kiépítés,* és *sikeres,* ha befejeződött.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Az Azure Monitor adattárának (ADX-fürt) kiépítése

A szolgáltatás korai hozzáférési időszakában az ADX-fürtöt manuálisan kiépíti a termékcsapat, amint az előző lépések befejeződtek. Használja a Microsoft-csatornát ehhez *Cluster* a lépéshez, és adja meg a fürterőforrás-választ. 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Másolja és mentse a választ, mivel szüksége lesz a részleteket a későbbi lépésekben

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
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

A "principalId" GUID-t a *fürterőforrás* felügyelt identitásszolgáltatása hozza létre.

### <a name="grant-key-vault-permissions"></a>Key Vault-engedélyek megadása

Frissítse a Key Vaultot egy új hozzáférési *Cluster* szabályzattal, amely engedélyeket ad a fürterőforrásnak. Ezeket az engedélyeket az alávetítéses Azure Monitor Storage használja az adatok titkosításához. Nyissa meg a Key Vaultot az Azure Portalon, és kattintson a "Hozzáférési szabályzatok" vagy a "+ Hozzáférési szabályzat hozzáadása" gombra az alábbi beállításokkal rendelkező szabályzat létrehozásához:

- Kulcsengedélyek: válassza a "Get", "Wrap Key" és "Unwrap Key" (Kulcs kicsomagolása) és a "Kulcs kicsomagolása" engedélyt.
- Válassza ki a fő: adja meg a rendszernéviazonosító-értéket, amely az előző lépésben a válaszban visszaadott értéket adja meg.

![Key Vault-engedélyek megadása](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A *Get* engedély szükséges annak ellenőrzéséhez, hogy a Key Vault van konfigurálva a kulcs és az Azure Monitor adataihoz való hozzáférés védelme érdekében.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Fürterőforrás frissítése a kulcsazonosító részleteivel

Ez a lépés a key vault ban a kezdeti és a jövőbeli kulcsverzió-frissítésekre vonatkozik. Tájékoztatja az Azure Monitor Storage-t az adattitkosításhoz használandó kulcsverzióról. Frissítéskor az új kulcs a Storage kulcs (AEK) tördelésére és kicsomagolására szolgál.

A *fürterőforrás* frissítéséhez a Key Vault *kulcsazonosítójának* részleteivel válassza ki a kulcs aktuális verzióját az Azure Key Vaultban a kulcsazonosító részleteinek leése érdekében.

![Key Vault-engedélyek megadása](media/customer-managed-keys/key-identifier-8bit.png)

Frissítse a *fürterőforrás* KeyVaultProperties a kulcsazonosító adatait.

**Frissítés**

>[!Warning]
> Meg kell adnia egy teljes *törzset* a fürterőforrás-frissítésben, amely tartalmazza *az identitást,* *a termékku-t*, *a KeyVaultProperties tulajdonságot* és *a helyet.* Ha hiányoznak a *KeyVaultProperties* adatai, a kulcsazonosító t eltávolítja a *fürterőforrásból,* és a kulcs [visszavonását](#cmk-kek-revocation)okozza.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
A "KeyVaultProperties" a Key Vault kulcsazonosítójának adatait tartalmazza.

**Válasz**

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

### <a name="workspace-association-to-cluster-resource"></a>Munkaterület-társítás *fürterőforráshoz*

Az Application Insights CMK-konfiguráció, kövesse a függelék tartalmát ebben a lépésben.

> [!IMPORTANT]
> Ezt a lépést csak az ADX-fürt kiépítése után kell végrehajtani. Ha a kiépítés előtt társítja a munkaterületeket és a betöltési adatokat, a bevitt adatok ellesznek dobva, és nem lesznek helyreállíthatók.
> Annak ellenőrzéséhez, hogy az ADX-fürt ki van-e építve, hajtsa végre a *FÜRTerőforrás* GET REST API-t, és ellenőrizze, hogy a *provisioningState* érték *sikeres-e.*

A művelet végrehajtásához mind a munkaterülethez, mind a *fürterőforráshoz* "írási" engedélyekkel kell rendelkeznie, amelyek a következő műveleteket tartalmazzák:

- A munkaterületen: Microsoft.OperationalInsights/workspaces/write
- *Fürterőforrás:* Microsoft.OperationalInsights/clusters/write

**Munkaterület társítása**
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Válasz**

```json
{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

A munkaterület-társítás az Erőforrás-kezelő aszinkron műveleteken keresztül történik, amelyek végrehajtása akár 90 percet is igénybe vehet. A következő lépés bemutatja, hogyan lehet ellenőrizni a munkaterület társításának állapotát. A munkaterületek társítása után a munkaterületekre bevitt adatok a felügyelt kulccsal titkosítottan tárolódnak.

### <a name="workspace-association-verification"></a>Munkaterület-társítás ellenőrzése
Ellenőrizheti, hogy egy munkaterület van-e társítva egy *fürterőforráshoz* a [Munkaterületek – Válasz beszerez és figyel.](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) A társított munkaterületek egy "clusterResourceId" tulajdonsággal *rendelkeznek, amelynek* fürterőforrás-azonosítója van.

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

## <a name="cmk-kek-revocation"></a>CMK (KEK) visszavonása

Visszavonhatja az adatokhoz való hozzáférést a kulcs letiltásával *Cluster* vagy a fürterőforrás-hozzáférési házirend törlésével a Key Vaultban. Az Azure Monitor Storage mindig tiszteletben tartja a kulcsfontosságú engedélyek változásait egy órán belül, általában hamarabb, és a Storage elérhetetlenné válik. A *fürterőforráshoz* társított munkaterületekre bevitt adatok eldobásra kerülnek, és a lekérdezések sikertelenek lesznek. A korábban bevitt adatok nem érhetők el az Azure Monitor Storage-ban mindaddig, amíg ön a *fürterőforrás,* és a munkaterületek nem törlődnek. A nem elérhető adatokat az adatmegőrzési házirend szabályozza, és az adatmegőrzés elérésekor törlődnek.

A Storage rendszeres időközönként lekérdezi a Key Vaultot, hogy megpróbálja kicsomagolni a titkosítási kulcsot, és amint hozzáfért, az adatok betöltése és a lekérdezés 30 percen belül folytatódik.

## <a name="cmk-kek-rotation"></a>CMK (KEK) elforgatás

A CMK elforgatása a *fürterőforrás* explicit frissítését igényli az Azure Key Vault új kulcsverziójával. Az Azure Monitor frissítése az új kulcsverzióval kövesse a *"Fürterőforrás* frissítése a kulcsazonosító részleteivel" című útmutató utasításait. Ha frissíti a kulcsverzióját a Key Vaultban, és nem *Cluster* frissíti az új kulcsazonosító adatait a fürterőforrásban, az Azure Monitor Storage továbbra is az előző kulcsot használja.
Az összes adat elérhető a kulcsrotációs művelet után, beleértve a rotáció előtt és utána bevitt adatokat is, mivel az összes adat titkosítva marad a fióktitkosítási kulcs (AEK) által, miközben az AEK-et az új kulcstitkosítási kulcs (KEK) verziója titkosítja.

## <a name="limitations-and-constraints"></a>Korlátozások és korlátozások

- A CMK-szolgáltatás ADX-fürt szinten támogatott, és egy dedikált Azure Monitor ADX-fürthöz szükséges, napi vagy több 1 TB-os küldését követelve.

- A *fürterőforrások* maximális száma előfizetésenként legfeljebb 2

- *A* fürterőforrás-társítást csak akkor szabad végrehajtani, ha meggyőződött arról, hogy az ADX-fürt kiépítése teljesült. A kiépítés előtt küldött adatokat a rendszer eldob, és nem lesz helyreállítható.

- A CMK-titkosítás a CMK-konfiguráció után az újonnan bevitt adatokra vonatkozik. A CMK-konfiguráció előtt bevitt adatok továbbra is titkosítva maradnak a Microsoft-kulccsal. A CMK-konfiguráció előtt és után betöltött adatok lekérdezése zökkenőmentesen.

- Miután munkaterület van társítva egy *fürterőforráshoz,* nem lehet a *fürt* erőforrásból való társítást, mivel az adatok titkosítva vannak a kulccsal, és nem érhetők el az Azure Key Vaultban a KEK nélkül.

- Az Azure Key Vault kell konfigurálni, mint helyreállítható. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, és cli és PowerShell használatával kell konfigurálni:

  - [A soft delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) gombot be kell kapcsolni
  - [A tisztítási védelmet](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) be kell kapcsolni, hogy védekezze a titkos / trezor erő törlésének ellen, még a puha törlés után is

- Az Application Insights és a Log Analytics külön *fürterőforrásokat* igényel. A *fürterőforrás* típusa a létrehozás időpontjában definiálva van definiálva a "clusterType" tulajdonság "LogAnalytics" vagy "ApplicationInsights" (Fürttípus) tulajdonság "LogAnalytics" vagy "ApplicationInsights" (Fürttípus) tulajdonságra állításával. A *Cluster* fürterőforrás-típus nem módosítható.

- *Fürterőforrás* áthelyezése egy másik erőforráscsoportba vagy előfizetésre jelenleg nem támogatott.

- Az Azure Key Vault, *a fürterőforrás* és a kapcsolódó munkaterületek ugyanabban a régióban és ugyanabban az Azure Active Directory (Azure AD) bérlőben kell lennie, de lehetnek különböző előfizetésekben.

- A *fürterőforráshoz* való munkaterület-társítás sikertelen lesz, ha egy másik *fürterőforráshoz* van társítva

## <a name="troubleshooting-and-management"></a>Hibaelhárítás és kezelés

- A Key Vault elérhetősége
    - Normál működés esetén - A storage rövid ideig gyorsítótárazza az AEK-et, és rendszeres időközönként visszatér a Key Vaultba.
    
    - Átmeneti csatlakozási hibák - A storage kezeli az átmeneti hibákat (időtúllépések, kapcsolati hibák, DNS-problémák) azáltal, hogy lehetővé teszi a kulcsok számára, hogy rövid ideig a gyorsítótárban maradjanak, és ez leküzdi a rendelkezésre álló kis blips-eket. A lekérdezési és betöltési képességek megszakítás nélkül folytatódnak.
    
    - Élő webhely – körülbelül 30 perces elérhetetlenség esetén a Storage-fiók elérhetetlenné válik. A lekérdezési funkció nem érhető el, és a bevitt adatok több órán keresztül gyorsítótárazódnak a Microsoft kulccsal az adatvesztés elkerülése érdekében. A Key Vault-hoz való hozzáférés visszaállításakor a lekérdezés elérhetővé válik, és az ideiglenes gyorsítótárazott adatok az adattárba kerül, és a CMK-vel titkosítva lesznek.

- Ha létrehoz egy *fürterőforrást,* és azonnal megadja a KeyVaultProperties tulajdonságot, a művelet sikertelen lehet, mivel a hozzáférési házirend nem határozható meg, amíg a rendszeridentitás nincs hozzárendelve a *fürterőforráshoz.*

- Ha frissíti *a* meglévő fürterőforrást a KeyVaultProperties szolgáltatással, és a "Get" kulcshozzáférési házirend hiányzik a Key Vaultból, a művelet sikertelen lesz.

- Ha egy munkaterülethez társított *fürterőforrást* próbál törölni, a törlési művelet sikertelen lesz.

- Erőforráscsoport összes *fürterőforrásának* beszereznie:

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

- Az összes *fürterőforrás* beszereznie egy előfizetéshez:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Válasz**
    
  Ugyanaz a válasz, mint a *"Fürterőforrások* egy erőforráscsoporthoz", de az előfizetés hatókörében.
    
- Törölje a *fürterőforrást* – a rendszer helyreállító törlési műveletet hajt végre, amely lehetővé teszi a fürterőforrás, az adatok és a kapcsolódó munkaterületek 14 napon belüli helyreállítását, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e. A *fürterőforrás* neve továbbra is fenntartva marad a helyreállítható törlési időszakban, és nem hozhat létre új fürtöt ezzel a névvel. A helyreállítható törlési időszak után a *fürterőforrás* és -adatok nem állíthatók helyre. A társított munkaterületek nincsenek *Cluster* társítva a fürterőforrásból, és az új adatok at a megosztott tárolóba tárolják, és a Microsoft kulccsal titkosítják.

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Válasz**

  200 OK

- A *fürterőforrás* és az adatok helyreállítása – a helyreállítható törlési időszak alatt hozzon létre egy *fürterőforrást* ugyanazzal a névvel és ugyanabban az előfizetésben, erőforráscsoportban és régióban. A *fürterőforrás* helyreállításához kövesse a ** *Fürterőforrás* létrehozása** lépést.


## <a name="appendix"></a>Függelék

Az Application Insights ügyfél által felügyelt kulcs (CMK) is támogatott, bár érdemes megfontolni a következő módosítást, amely segít megtervezni a CMK üzembe helyezését az Application Insight-összetevőkhöz.

A Log Analytics és az Application Insights ugyanazt az adattár-platformot és lekérdezési motort használja. Ezt a két áruházat az Application Insights Log Analytics-be való integrálásával hozzuk össze, hogy az Azure Monitor alatt egyetlen egyesített naplót biztosítsunk a második negyedévig.
2020. Ez a módosítás az Application Insight-adatokat a Log Analytics-munkaterületekre hozza, és lehetővé teszi a lekérdezéseket, elemzéseket és egyéb fejlesztéseket, miközben a CMK konfigurálása a munkaterületen az Application Insights-adatokra is vonatkozik.

> [!NOTE]
> Ha nem kell telepítenie a CMK-t az Application Insight-adatokhoz az integráció előtt, javasoljuk, hogy várjon az Application Insights CMK-vel, mivel az ilyen telepítések megszakadnak az integráció miatt, és újra kell konfigurálnia a CMK-t a Log Analytics munkaterületre való áttelepítés után. A napi 1 TB-os minimum a fürt szintjén érvényes, és amíg a konszolidáció a második negyedévben befejeződik, az Application Insights és a Log Analytics külön fürtöket igényel.

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK-konfigurációja

Az Application Insights CMK konfigurációja megegyezik a cikkben bemutatott folyamattal, beleértve a korlátozásokat és a hibaelhárítást, kivéve az alábbi lépéseket:

- *Fürterőforrás* létrehozása
- Összetevő hozzárendelése *fürterőforráshoz*

A CMK alkalmazáselemzési konfigurálásakor az alábbi lépéseket használja a fent felsoroltak helyett.

### <a name="create-a-cluster-resource"></a>*Fürterőforrás* létrehozása

Ez az erőforrás köztes identitáskapcsolatként használatos a Key Vault és az összetevők között. Miután megerősítést kapott arról, hogy az előfizetések szerepelnek, hozzon létre egy Log *Analytics-fürterőforrást* az összetevők et tartalmazó régióban. A *fürterőforrás* típusa a létrehozás időpontjában a *clusterType* tulajdonság *loganalytics*vagy *ApplicationInsights*beállításával van definiálva. *Az ApplicationInsights* for Application Insights CMK-nek kell lennie. A *clusterType* beállítás nem módosítható a konfiguráció után.

**Létrehozás**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Válasz**

202 Elfogadva. Ez egy szabványos Erőforrás-kezelő válasz aszinkron műveletekhez.

>[!Important]
> Az aluli ADX-fürt kiépítése néhány percet vesz igénybe. Ellenőrizheti a létesítési állapot, amikor a GET REST API-hívás végrehajtása a *fürt* erőforrás, és a *provisioningState* érték vizsgálata. A *kiépítés során a kiépítés* és a "Sikeres" kiépítés közben.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Összetevő társítása *fürterőforráshoz* összetevők használatával [– API létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)

A művelet végrehajtásához mind az összetevőre, mind a *fürterőforrásra* "írási" engedélyekkel kell rendelkeznie, amelyek a következő műveleteket foglalják magukban:

- Összetevőben: Microsoft.Insights/component/write
- *Fürterőforrás:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Ezt a lépést csak az ADX-fürt kiépítése után kell végrehajtani. Ha a kiépítés előtt társítja az összetevőket és a betöltési adatokat, a bevitt adatok ellesznek dobva, és nem lesznek helyreállíthatók.
> Annak ellenőrzéséhez, hogy az ADX-fürt ki van-e építve, hajtsa végre a *FÜRTerőforrás* GET REST API-t, és ellenőrizze, hogy a *provisioningState* érték *sikeres-e.*

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
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> Másolja és tartsa meg az "elv-id" értéket, mivel szüksége lesz rá a következő lépésekben.

**Összetevő társítása**

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
A "clusterDefinitionId" az előző lépésválaszban megadott "clusterId" érték.
"kedves" példa a "web".

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
A "clusterDefinitionId" *Cluster* az összetevőhöz társított fürterőforrás-azonosító.

A társítás után az összetevőknek küldött adatok at a kezelt kulccsal titkosítottan tárolja a rendszer.
