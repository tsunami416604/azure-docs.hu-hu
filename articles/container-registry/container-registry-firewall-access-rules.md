---
title: Tűzfal-hozzáférési szabályok
description: Szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez.
ms.topic: article
ms.date: 07/17/2019
ms.openlocfilehash: 6a0a169f7e5a7e07771cb9fee474b7f4a9391a4e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455180"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez

Ez a cikk bemutatja, hogyan konfigurálhat szabályokat a tűzfalon az Azure Container Registry elérésének engedélyezéséhez. Előfordulhat például, hogy egy tűzfal mögötti Azure IoT Edge eszköznek hozzá kell férnie egy tároló-beállításjegyzékhez egy tároló rendszerképének lekéréséhez. Emellett előfordulhat, hogy egy helyszíni hálózaton lévő zárolt kiszolgálónak hozzáférésre van szüksége a rendszerkép leküldéséhez.

Ha ehelyett úgy szeretné konfigurálni a bejövő hálózati hozzáférési szabályokat egy tároló-beállításjegyzékben, hogy csak az Azure-beli virtuális hálózaton vagy egy nyilvános IP-címtartomány elérését engedélyezze, tekintse meg az [Azure Container Registry elérésének korlátozása virtuális hálózatról](container-registry-vnet.md)című témakört.

## <a name="about-registry-endpoints"></a>Tudnivalók a beállításjegyzékbeli végpontokról

Képek vagy egyéb összetevők Azure Container registrybe való lekéréséhez vagy leküldéséhez egy ügyfélnek, például egy Docker-démonnak a HTTPS-kapcsolaton keresztül kell kommunikálnia két különálló végponttal.

* A beállításjegyzék **REST API a végpontok** hitelesítése és a beállításjegyzék-kezelési műveletek a beállításjegyzék nyilvános REST API végpontján keresztül kezelhetők. Ez a végpont a beállításjegyzék bejelentkezési kiszolgálójának URL-címe vagy egy társított IP-címtartomány. 

* **Tárolási végpont** – az Azure [blob Storage](container-registry-storage.md) -tárolókat rendel az Azure Storage-fiókokhoz az egyes beállításjegyzékek nevében a tároló-lemezképek és egyéb összetevők kezeléséhez. Amikor egy ügyfél egy Azure Container registryben fér hozzá a képrétegekhez, a a beállításjegyzék által biztosított Storage-fiók végpontján keresztül kéri a kérelmeket.

Ha a beállításjegyzék [földrajzilag replikálódik](container-registry-geo-replication.md), előfordulhat, hogy az ügyfélnek egy adott régióban vagy több replikált régióban lévő REST-és tárolási végpontokkal kell kommunikálnia.

## <a name="allow-access-to-rest-and-storage-urls"></a>A REST és a Storage URL-címek elérésének engedélyezése

* **Rest-végpont** – hozzáférés engedélyezése a beállításjegyzék-kiszolgáló URL-címéhez, például `myregistry.azurecr.io`
* **Tárolási végpont** – engedélyezi az összes Azure Blob Storage-fiókhoz való hozzáférést a helyettesítő karakterrel `*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Hozzáférés engedélyezése IP-címtartomány alapján

Ha engedélyezni szeretné a hozzáférést adott IP-címekhez, töltse le az [Azure IP-címtartományok és a szolgáltatás címkéit – nyilvános felhőt](https://www.microsoft.com/download/details.aspx?id=56519).

Az ACR REST-végpont IP-címtartományok megkereséséhez keressen rá a **AzureContainerRegistry** kifejezésre a JSON-fájlban.

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

## <a name="next-steps"></a>Következő lépések

* A [hálózati biztonsággal kapcsolatos Azure ajánlott eljárások](../security/fundamentals/network-best-practices.md) ismertetése

* További információ az Azure Virtual Network [biztonsági csoportjairól](/azure/virtual-network/security-overview)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

