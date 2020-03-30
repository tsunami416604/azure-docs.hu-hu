---
title: Tűzfal-hozzáférési szabályok
description: Szabályok konfigurálása az Azure-tároló beállításjegyzék-beállításjegyzék-hozzáférés a tűzfal mögül, azáltal, hogy hozzáférést biztosít ("whitelisting") REST API és a tárolási végpont tartománynevek vagy szolgáltatásspecifikus IP-címtartományok eléréséhez.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168019"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Szabályok konfigurálása egy Tűzfal mögötti Azure-tároló beállításjegyzékének eléréséhez

Ez a cikk bemutatja, hogyan konfigurálhatja a szabályokat a tűzfalon, hogy az Azure-tároló beállításjegyzékhez való hozzáférést. Például egy Azure IoT Edge-eszköz egy tűzfal vagy proxykiszolgáló mögött előfordulhat, hogy egy tároló beállításjegyzék-hozzáférés egy tároló rendszerkép lekérése. Vagy előfordulhat, hogy egy helyszíni hálózat zárolt kiszolgálójának hozzáférésre van szüksége a lemezkép lelökéséhez.

Ha ehelyett a bejövő hálózati hozzáférési szabályokat csak egy Azure virtuális hálózaton vagy nyilvános IP-címtartományban szeretné konfigurálni egy tárolóbeállítás-nyilvántartásban, olvassa el az [Azure-tároló beállításjegyzékének virtuális hálózatról való hozzáférés korlátozása című témakört.](container-registry-vnet.md)

## <a name="about-registry-endpoints"></a>Beállításjegyzék-végpontok –

A rendszerképek vagy más összetevők leküldéses vagy leküldéses egy Azure-tároló beállításjegyzékbe, egy ügyfél, például egy Docker démon kell https-en keresztül két különböző végpontok.

* **Registry REST API-végpont** – A hitelesítési és beállításjegyzék-kezelési műveletek kezelése a rendszerleíró adatbázis nyilvános REST API-végpontja. Ez a végpont a rendszerleíró adatbázis bejelentkezési kiszolgálójának vagy egy társított IP-címtartománynak a neve. 

* **Storage-végpont** – Az Azure [lefoglalja a blob storage](container-registry-storage.md) az Azure Storage-fiókok nevében minden beállításjegyzék a tárolórendszerképek és egyéb összetevők adatainak kezeléséhez. Amikor egy ügyfél hozzáfér a lemezképek rétegei egy Azure-tároló beállításjegyzékben, a beállításjegyzék által biztosított tárfiók-végpont használatával kéri.

Ha a beállításjegyzék [georeplikált,](container-registry-geo-replication.md)előfordulhat, hogy egy ügyfélnek egy adott régióban vagy több replikált régióban kell együttműködnie a REST és a tárolási végpontokkal.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Hozzáférés engedélyezése REST- és tárolótartomány-nevekhez

* **REST-végpont** – Hozzáférés engedélyezése a teljesen minősített rendszerleíró bejelentkezési kiszolgáló nevéhez, például`myregistry.azurecr.io`
* **Storage (data) endpoint** – Hozzáférés engedélyezése az összes Azure blobstorage-fiókhoz a helyettesítő karakter használatával`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Hozzáférés engedélyezése IP-címtartomány szerint

Ha a szervezet szabályzata csak bizonyos IP-címekhez vagy címtartományokhoz engedélyezi a hozzáférést, töltse le [az Azure IP-tartományokat és a szolgáltatáscímkék – nyilvános felhőt.](https://www.microsoft.com/download/details.aspx?id=56519)

Az ACR REST-végpont IP-tartományainak megkereséséhez, amelyekhez hozzáférésre van szüksége, keresse meg az **AzureContainerRegistry-t** a JSON-fájlban.

> [!IMPORTANT]
> Az Azure-szolgáltatások IP-címtartományai változhatnak, és a frissítéseket hetente teszik közzé. Töltse le rendszeresen a JSON-fájlt, és tegye meg a szükséges frissítéseket a hozzáférési szabályokban. Ha a forgatókönyv ben konfigurálása hálózati biztonsági csoport szabályok egy Azure virtuális hálózat eléréséhez Azure Container Registry, használja az **AzureContainerRegistry** [szolgáltatás címke](#allow-access-by-service-tag) helyett.
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-címek az összes régióhoz

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

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP-címek egy adott régióhoz

Keresse meg az adott régiót, például **az AzureContainerRegistry.AustraliaEast**.

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

### <a name="storage-ip-addresses-for-all-regions"></a>Tárolási IP-címek az összes régióban

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

### <a name="storage-ip-addresses-for-specific-regions"></a>Tárolási IP-címek adott régiókhoz

Keresse meg az adott régiót, például **a Storage.AustraliaCentral.**

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

## <a name="allow-access-by-service-tag"></a>Hozzáférés engedélyezése szolgáltatáscímke szerint

Egy Azure virtuális hálózatban a hálózati biztonsági szabályok segítségével szűrheti a forgalmat egy erőforrás, például egy virtuális gép egy tároló beállításjegyzékbe. Az Azure hálózati szabályok létrehozásának egyszerűsítése érdekében használja az **AzureContainerRegistry** [szolgáltatáscímkét.](../virtual-network/security-overview.md#service-tags) A szolgáltatáscímke az IP-cím előtagok egy csoportját jelöli egy Azure-szolgáltatás globális vagy Azure-régiónkénti eléréséhez. A címke automatikusan frissül, amikor a címek változnak. 

Hozzon létre például egy kimenő hálózati biztonsági csoportszabályt a cél **AzureContainerRegistry** céllal, hogy engedélyezze a forgalmat egy Azure-tároló beállításjegyzékbe. Ha csak egy adott régióban szeretné engedélyezni a hozzáférést a szolgáltatáscímkéhez, adja meg a régiót a következő formátumban: **AzureContainerRegistry**. [*régió neve*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Ügyféltűzfal-szabályok konfigurálása az MCR-hez

Ha tűzfal mögül szeretné elérni a Microsoft Container Registry (MCR) rendszert, olvassa el az [MCR-ügyféltűzfal-szabályok](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)konfigurálásához szükséges útmutatást. Az MCR az összes Microsoft által közzétett docker-lemezkép, például a Windows Server-lemezképek elsődleges beállításjegyzéke.

## <a name="next-steps"></a>További lépések

* Ismerje meg az [Azure bevált módszereit a hálózat biztonságával kapcsolatban](../security/fundamentals/network-best-practices.md)

* További információ az Azure virtuális hálózat [biztonsági csoportjairól](/azure/virtual-network/security-overview)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

