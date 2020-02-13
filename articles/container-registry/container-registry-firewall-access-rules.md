---
title: Tűzfal-hozzáférési szabályok
description: Konfigurálja a szabályokat az Azure Container Registry tűzfal mögötti eléréséhez, mivel lehetővé teszi a hozzáférését ("Whitelisting") REST API és a tárolási végpont tartománynevét vagy a szolgáltatás-specifikus IP-címtartományt.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168019"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez

Ez a cikk bemutatja, hogyan konfigurálhat szabályokat a tűzfalon az Azure Container Registry elérésének engedélyezéséhez. Előfordulhat például, hogy egy tűzfal mögötti Azure IoT Edge eszköznek hozzá kell férnie egy tároló-beállításjegyzékhez egy tároló rendszerképének lekéréséhez. Emellett előfordulhat, hogy egy helyszíni hálózaton lévő zárolt kiszolgálónak hozzáférésre van szüksége a rendszerkép leküldéséhez.

Ha ehelyett csak egy Azure-beli virtuális hálózaton vagy nyilvános IP-címtartományból szeretné konfigurálni a bejövő hálózati hozzáférési szabályokat egy tároló-beállításjegyzékben, tekintse meg az [Azure Container Registry elérésének korlátozása virtuális hálózatról](container-registry-vnet.md)című témakört.

## <a name="about-registry-endpoints"></a>Tudnivalók a beállításjegyzékbeli végpontokról

Képek vagy egyéb összetevők Azure Container registrybe való lekéréséhez vagy leküldéséhez egy ügyfélnek, például egy Docker-démonnak a HTTPS-kapcsolaton keresztül kell kommunikálnia két különálló végponttal.

* A beállításjegyzék **REST API a végpontok** hitelesítése és a beállításjegyzék-kezelési műveletek a beállításjegyzék nyilvános REST API végpontján keresztül kezelhetők. Ez a végpont a beállításjegyzék bejelentkezési kiszolgálójának neve, vagy egy társított IP-címtartomány. 

* **Tárolási végpont** – az Azure [blob Storage](container-registry-storage.md) -tárolókat rendel az Azure Storage-fiókokhoz az egyes beállításjegyzékek nevében, hogy kezelje a tároló-lemezképek és egyéb összetevők adatait. Amikor egy ügyfél egy Azure Container registryben fér hozzá a képrétegekhez, a a beállításjegyzék által biztosított Storage-fiók végpontján keresztül kéri a kérelmeket.

Ha a beállításjegyzék [földrajzilag replikálódik](container-registry-geo-replication.md), előfordulhat, hogy az ügyfélnek egy adott régióban vagy több replikált régióban lévő REST-és tárolási végpontokkal kell kommunikálnia.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>A REST-és a tárolási tartománynevek elérésének engedélyezése

* **Rest-végpont** – engedélyezi a teljes beállításjegyzékbeli bejelentkezési kiszolgálónév elérését, például `myregistry.azurecr.io`
* **Tárolási (adat) végpont** – engedélyezi az összes Azure Blob Storage-fiókhoz való hozzáférést a helyettesítő karakterrel `*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Hozzáférés engedélyezése IP-címtartomány alapján

Ha a szervezet rendelkezik olyan házirendekkel, amelyek csak adott IP-címekhez vagy címtartományok elérését engedélyezik, töltse le az [Azure IP-címtartományok és a szolgáltatási címkék – nyilvános felhő szolgáltatást](https://www.microsoft.com/download/details.aspx?id=56519).

Azon ACR REST-végpont IP-címtartományok megkereséséhez, amelyekhez engedélyezni szeretné a hozzáférést, keresse meg a **AzureContainerRegistry** a JSON-fájlban.

> [!IMPORTANT]
> Az Azure-szolgáltatások IP-címtartományok változhatnak, és a frissítések hetente lesznek közzétéve. Töltse le rendszeresen a JSON-fájlt, és végezze el a szükséges frissítéseket a hozzáférési szabályokban. Ha a forgatókönyv magában foglalja a hálózati biztonsági csoportok szabályainak konfigurálását egy Azure-beli virtuális hálózaton a Azure Container Registry eléréséhez, használja helyette a **AzureContainerRegistry** [szolgáltatás címkéjét](#allow-access-by-service-tag) .
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-címek az összes régióban

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Adott régió REST IP-címei

Keresse meg az adott régiót, például a **AzureContainerRegistry. AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Az összes régió tárolási IP-címei

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Adott régiók tárolási IP-címei

Keresse meg az adott régiót, például a **Storage. AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Hozzáférés engedélyezése a Service tag számára

Egy Azure-beli virtuális hálózatban a hálózati biztonsági szabályok használatával szűrheti a forgalmat egy erőforrásból, például egy virtuális gépről egy tároló-beállításjegyzékbe. Az Azure-beli hálózati szabályok létrehozásának egyszerűbbé tételéhez használja a **AzureContainerRegistry** [szolgáltatás címkéjét](../virtual-network/security-overview.md#service-tags). A szolgáltatási címke az IP-címek egy csoportját jelöli, amely egy Azure-szolgáltatás globális vagy Azure-régióhoz való elérésére szolgál. A rendszer automatikusan frissíti a címkét a címek változásakor. 

Hozzon létre például egy kimenő hálózati biztonsági csoportra vonatkozó szabályt a cél **AzureContainerRegistry** , hogy engedélyezze a forgalmat egy Azure Container registrybe. Ha csak egy adott régióban szeretné engedélyezni a szolgáltatás címkéhez való hozzáférést, a következő formátumban kell megadnia a régiót: **AzureContainerRegistry**. [*régió neve*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Az MCR tartozó ügyfél-tűzfalszabályok konfigurálása

Ha tűzfal mögött szeretné elérni a Microsoft Container Registryt (MCR), tekintse meg a [MCR-ügyfél tűzfalszabályok](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)konfigurálására vonatkozó útmutatót. A MCR az összes Microsoft által közzétett Docker-rendszerkép elsődleges beállításjegyzéke, például Windows Server-lemezképek.

## <a name="next-steps"></a>Következő lépések

* A [hálózati biztonsággal kapcsolatos Azure ajánlott eljárások](../security/fundamentals/network-best-practices.md) ismertetése

* További információ az Azure Virtual Network [biztonsági csoportjairól](/azure/virtual-network/security-overview)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

