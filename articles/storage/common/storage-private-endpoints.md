---
title: Privát végpontok használata az Azure Storage szolgáltatással | Microsoft Docs
description: A privát végpontok áttekintése a virtuális hálózatok Storage-fiókjainak biztonságos eléréséhez.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 00de95f3b3e6eddd1f45be830202ba3ec8772bfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176163"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Privát végpontok használata az Azure Storage-hoz (előzetes verzió)

Az Azure Storage-fiókokhoz [privát végpontokat](../../private-link/private-endpoint-overview.md) is használhat, amelyek lehetővé teszik a virtuális hálózat (VNet) ügyfelei számára, hogy biztonságosan hozzáférjenek az adataihoz egy [privát kapcsolaton](../../private-link/private-link-overview.md)keresztül. A privát végpont IP-címet használ a Storage-fiók szolgáltatás VNet. A VNet és a Storage-fiók ügyfelei közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül halad, ami kiküszöböli a nyilvános internetről való kitettséget.

Privát végpontok használata a Storage-fiókhoz a következőket teszi lehetővé:
- A Storage-fiók biztonságossá tételéhez konfigurálja a Storage-tűzfalat úgy, hogy az a tárolási szolgáltatás nyilvános végpontján lévő összes kapcsolatot letiltsa.
- A virtuális hálózat (VNet) biztonságának növelése azáltal, hogy letiltja a VNet kiszűrése adatait.
- Biztonságosan csatlakozhat a Storage-fiókokhoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../../expressroute/expressroute-locations.md) [-](../../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a VNet.

## <a name="conceptual-overview"></a>Fogalmi áttekintés
![Privát végpontok az Azure Storage-hoz – áttekintés](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

A privát végpontok egy speciális hálózati adapterek egy Azure-szolgáltatáshoz a [Virtual Networkban](../../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot a Storage-fiókjához, biztonságos kapcsolatot biztosít a VNet található ügyfelek és a tároló között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a tárolási szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások zökkenőmentesen kapcsolódhatnak a tárolási szolgáltatáshoz a magánhálózati végponton keresztül, ugyanazokkal a kapcsolati karakterláncokkal és engedélyezési mechanizmusokkal, amelyeket egyébként használni fognak. A magánhálózati végpontok a Storage-fiók által támogatott összes protokollal használhatók, beleértve a REST és az SMB protokollt is.

Amikor saját VNet hoz létre egy tárolási szolgáltatáshoz egy magánhálózati végpontot, a rendszer beleegyezési kérelmet küld jóváhagyásra a Storage-fiók tulajdonosának. Ha a privát végpont létrehozását kérő felhasználó a Storage-fiók tulajdonosa is, akkor a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja.

A Storage-fiók tulajdonosai a [Azure Portal](https://portal.azure.com)a Storage-fiókhoz tartozó privát végpontok lapján kezelhetik a belefoglalt kérelmeket és a privát végpontokat.

> [!TIP]
> Ha csak a privát végponton keresztül szeretné korlátozni a Storage-fiókhoz való hozzáférést, konfigurálja úgy a tárolási tűzfalat, hogy az összes hozzáférését megtagadja a nyilvános végponton keresztül.

A Storage-fiók úgy is biztosítható, hogy csak a VNet érkező kapcsolatokat fogadja el, ha úgy [konfigurálja a tárolási tűzfalat](storage-network-security.md#change-the-default-network-access-rule) , hogy alapértelmezés szerint megtagadja a hozzáférést a nyilvános végponton keresztül. Nincs szükség olyan tűzfalszabályre, amely engedélyezi a forgalmat egy privát végponttal rendelkező VNet, mivel a tárolási tűzfal csak a nyilvános végponton keresztüli hozzáférést vezérli. A magánhálózati végpontok Ehelyett az alhálózatokhoz való hozzáférést biztosító engedélyezési folyamaton alapulnak.

### <a name="private-endpoints-for-storage-service"></a>Privát végpontok tárolási szolgáltatáshoz

A magánhálózati végpont létrehozásakor meg kell adnia a Storage-fiókot és a tárolási szolgáltatást, amelyhez csatlakozik. Külön privát végpontra van szükség az egyes tárolási szolgáltatásokhoz egy olyan Storage-fiókban, amelyhez hozzá kell férnie, azaz [blobokhoz](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2okhoz](../blobs/data-lake-storage-introduction.md), [fájlokhoz](../files/storage-files-introduction.md), [várólistákhoz](../queues/storage-queues-introduction.md), [táblákhoz](../tables/table-storage-overview.md)vagy [statikus webhelyekhez](../blobs/storage-blob-static-website.md).

> [!TIP]
> Hozzon létre egy külön privát végpontot a Storage szolgáltatás másodlagos példányához az RA-GRS-fiókok jobb olvasási teljesítményéhez.

Ha olvasási hozzáférésre van szüksége egy [írásvédett földrajzi redundáns Storage-fiókhoz](storage-redundancy-grs.md#read-access-geo-redundant-storage), külön magánhálózati végpontokra van szükség a szolgáltatás elsődleges és másodlagos példányai esetében is. A **feladatátvételhez**nem kell létrehoznia privát végpontot a másodlagos példányhoz. A magánhálózati végpont automatikusan csatlakozni fog az új elsődleges példányhoz a feladatátvétel után. git 

#### <a name="resources"></a>Segédanyagok és eszközök

A privát végpontok Storage-fiókhoz való létrehozásával kapcsolatos részletes információkért tekintse meg a következő cikkeket:

- [Magánhálózati kapcsolat létrehozása a Azure Portal Storage-fiókjának felhasználói felületén](../../private-link/create-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása a Azure Portal privát kapcsolati központjának használatával](../../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása az Azure CLI-vel](../../private-link/create-private-endpoint-cli.md)
- [Privát végpont létrehozása Azure PowerShell használatával](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai

A VNet lévő ügyfelek a Storage-fiókhoz ugyanazt a kapcsolódási karakterláncot használhatják, még akkor is, ha privát végpontot használ.

Privát végpont létrehozásakor a tárolási végponthoz tartozó DNS CNAME erőforrásrekordot a "*privatelink*" előtaggal rendelkező altartományban található aliasra frissíti a rendszer. Alapértelmezés szerint a VNet csatolt [privát DNS-zónát](../../dns/private-dns-overview.md) is létrehozunk. Ez a saját DNS-zóna megfelel a (z)*privatelink*előtaggal rendelkező altartománynak, és tartalmazza A DNS a magánhálózati végpontokhoz tartozó erőforrásrekordokat.

Ha a VNet kívülről oldja fel a tárolási végpont URL-címét a privát végponttal, a rendszer a Storage szolgáltatás nyilvános végpontját oldja fel. A magánhálózati végpontot futtató VNet feloldva a tárolási végpont URL-címe feloldja a magánhálózati végpont IP-címét.

A fenti ábrán látható példában a "StorageAccountA" Storage-fiókhoz tartozó DNS-erőforrásrekordok a privát végpontot üzemeltető VNet kívülről történő feloldáskor a következők:

| Név                                                  | Type (Típus)  | Value (Díj)                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<Storage szolgáltatás nyilvános végpontja\>                   |
| \<Storage szolgáltatás nyilvános végpontja\>                   | A     | \<Storage szolgáltatás nyilvános IP-címe\>                 |

Amint azt korábban említettük, a tárolási tűzfal használatával megtagadhatja az összes hozzáférést a nyilvános végponton keresztül.

A StorageAccountA tartozó DNS-erőforrásrekordok, amikor a privát végpontot üzemeltető VNet-ügyfél feloldotta a következőt:

| Név                                                  | Type (Típus)  | Value (Díj)                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Ez a megközelítés lehetővé teszi a Storage-fiók elérését a privát végpontokat üzemeltető VNet és a VNet kívüli ügyfelekkel azonos kapcsolati sztring használatával. A tárolási tűzfal használatával megtagadhatja a hozzáférést a VNet kívüli összes ügyfélhez.

> [!TIP]
> Ha egyéni vagy helyszíni DNS-kiszolgálót használ, a tárolási szolgáltatás "privatelink" altartományát kell használnia a magánhálózati végpontok DNS-erőforrásrekordjait a konfigurálásához.

A tárolási szolgáltatásokhoz tartozó magánhálózati végpontok ajánlott DNS-zónák neve a következő:

| Tárolási szolgáltatás        | Zóna neve                            |
| :--------------------- | :----------------------------------- |
| Blob service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Fájlszolgáltatások           | `privatelink.file.core.windows.net`  |
| Queue szolgáltatás          | `privatelink.queue.core.windows.net` |
| Table service          | `privatelink.table.core.windows.net` |
| Statikus webhelyek        | `privatelink.web.core.windows.net`   |

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Ismert problémák

### <a name="copy-blob-failures"></a>BLOB-hibák másolása

Jelenleg a privát végpontokon keresztül elért Storage-fiókok számára kiállított blob-parancsok [másolása](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) meghiúsul, ha a forrás Storage-fiókot tűzfal védi.

### <a name="subnets-with-service-endpoints"></a>Alhálózatok szolgáltatási végpontokkal
Az előzetes verzió ideje alatt nem hozhat létre olyan magánhálózati végpontot olyan alhálózatban, amely szolgáltatási végpontokkal rendelkezik. Különálló alhálózatokat is létrehozhat ugyanabban a VNet a szolgáltatási végpontok és a magánhálózati végpontok számára.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Tároló-hozzáférési megkötések a virtuális hálózatok-beli ügyfelek számára privát végpontokkal

A meglévő privát végpontokkal rendelkező virtuális hálózatok-ügyfelek megkötést kaptak a privát végpontokkal rendelkező más Storage-fiókok elérésekor. Tegyük fel például, hogy egy VNet N1-hez saját végpont tartozik az A1-es Storage-fiókhoz, azaz a blob szolgáltatáshoz. Ha az A2-es Storage-fiók privát végponttal rendelkezik a blob szolgáltatáshoz tartozó VNet N2-ben, akkor az VNet N1-ben lévő ügyfeleknek a privát végpont használatával is el kell érniük az A2-es fiók blob szolgáltatását. Ha az A2-es Storage-fiók nem rendelkezik privát végpontokkal a blob szolgáltatáshoz, akkor a VNet N1-ben lévő ügyfelek privát végpont nélkül is hozzáférhetnek a blob szolgáltatáshoz.

Ez a megkötés a DNS-módosítások eredményeként történt, amikor az A2-es fiók létrehoz egy magánhálózati végpontot.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Hálózati biztonsági csoportra vonatkozó szabályok privát végpontokkal rendelkező alhálózatokhoz

Jelenleg nem lehet konfigurálni a [hálózati biztonsági csoport](../../virtual-network/security-overview.md) (NSG) szabályait privát végpontokkal rendelkező alhálózatokhoz. A probléma korlátozott megkerülő megoldásként implementálja a privát végpontok hozzáférési szabályait a forrás alhálózatokon, bár ennél a megközelítésnél magasabb szintű felügyeleti terhelésre lehet szükség.
