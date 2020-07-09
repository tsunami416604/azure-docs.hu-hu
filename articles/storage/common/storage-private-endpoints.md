---
title: Privát végpontok használata
titleSuffix: Azure Storage
description: A privát végpontok áttekintése a virtuális hálózatok Storage-fiókjainak biztonságos eléréséhez.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 7a216b9e430c10f42d48df01746e111355cf91b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513288"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Privát végpontok használata az Azure Storage-hoz

Az Azure Storage-fiókokhoz [privát végpontokat](../../private-link/private-endpoint-overview.md) is használhat, amelyek lehetővé teszik a virtuális hálózat (VNet) ügyfelei számára, hogy biztonságosan hozzáférjenek az adataihoz egy [privát kapcsolaton](../../private-link/private-link-overview.md)keresztül. A privát végpont IP-címet használ a Storage-fiók szolgáltatás VNet. A VNet és a Storage-fiók ügyfelei közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül halad, ami kiküszöböli a nyilvános internetről való kitettséget.

Privát végpontok használata a Storage-fiókhoz a következőket teszi lehetővé:

- A Storage-fiók biztonságossá tételéhez konfigurálja a Storage-tűzfalat úgy, hogy az a tárolási szolgáltatás nyilvános végpontján lévő összes kapcsolatot letiltsa.
- A virtuális hálózat (VNet) biztonságának növelésével letilthatja a VNet kiszűrése adatait.
- Biztonságosan csatlakozhat a Storage-fiókokhoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../../expressroute/expressroute-locations.md) [-](../../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a VNet.

## <a name="conceptual-overview"></a>Fogalmi áttekintés

![Az Azure Storage privát végpontjai – áttekintés](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

A privát végpontok egy speciális hálózati adapterek egy Azure-szolgáltatáshoz a [Virtual Networkban](../../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot a Storage-fiókjához, biztonságos kapcsolatot biztosít a VNet található ügyfelek és a tároló között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a tárolási szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások zökkenőmentesen kapcsolódhatnak a tárolási szolgáltatáshoz a magánhálózati végponton keresztül, **ugyanazokkal a kapcsolati karakterláncokkal és engedélyezési mechanizmusokkal, amelyeket egyébként használni**fognak. A magánhálózati végpontok a Storage-fiók által támogatott összes protokollal használhatók, beleértve a REST és az SMB protokollt is.

A magánhálózati végpontok olyan alhálózatokban hozhatók létre, amelyek [szolgáltatási végpontokat](../../virtual-network/virtual-network-service-endpoints-overview.md)használnak. Az alhálózaton lévő ügyfelek így egy privát végpont használatával csatlakozhatnak egy Storage-fiókhoz, míg más szolgáltatás-végpontok használatával is hozzáférhetnek.

Ha létrehoz egy privát végpontot a virtuális hálózaton található egyik tárolási szolgáltatáshoz, a rendszer egy hozzájárulási kérést küld a tárfiók tulajdonosának jóváhagyás céljából. Ha a privát végpont létrehozását kérő felhasználó a Storage-fiók tulajdonosa is, akkor a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja.

A Storage-fiók tulajdonosai a [Azure Portal](https://portal.azure.com)a Storage-fiókhoz tartozó*privát végpontok*lapján kezelhetik a belefoglalt kérelmeket és a privát végpontokat.

> [!TIP]
> Ha csak a privát végponton keresztül szeretné korlátozni a Storage-fiók elérését, konfigurálja a tárolási tűzfalat a nyilvános végponton keresztüli hozzáférés megtagadásához vagy vezérléséhez.

A Storage-fiók úgy is biztosítható, hogy csak a VNet érkező kapcsolatokat fogadja el, ha úgy [konfigurálja a tárolási tűzfalat](storage-network-security.md#change-the-default-network-access-rule) , hogy alapértelmezés szerint megtagadja a hozzáférést a nyilvános végponton keresztül. Nincs szükség olyan tűzfalszabályre, amely engedélyezi a forgalmat egy privát végponttal rendelkező VNet, mivel a tárolási tűzfal csak a nyilvános végponton keresztüli hozzáférést vezérli. A magánhálózati végpontok Ehelyett az alhálózatokhoz való hozzáférést biztosító engedélyezési folyamaton alapulnak.

### <a name="private-endpoints-for-azure-storage"></a>Privát végpontok az Azure Storage-hoz

A magánhálózati végpont létrehozásakor meg kell adnia a Storage-fiókot és a tárolási szolgáltatást, amelyhez csatlakozik. Külön privát végpontra van szükség az egyes tárolási szolgáltatásokhoz egy olyan Storage-fiókban, amelyhez hozzá kell férnie, azaz [blobokhoz](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2okhoz](../blobs/data-lake-storage-introduction.md), [fájlokhoz](../files/storage-files-introduction.md), [várólistákhoz](../queues/storage-queues-introduction.md), [táblákhoz](../tables/table-storage-overview.md)vagy [statikus webhelyekhez](../blobs/storage-blob-static-website.md).

> [!TIP]
> Hozzon létre egy külön privát végpontot a Storage szolgáltatás másodlagos példányához az RA-GRS-fiókok jobb olvasási teljesítményéhez.

Ha olvasási hozzáférést szeretne a másodlagos régióhoz a földrajzi redundáns tároláshoz konfigurált Storage-fiókkal, akkor a szolgáltatás elsődleges és másodlagos példányaihoz külön magánhálózati végpontokra van szükség. A **feladatátvételhez**nem kell létrehoznia privát végpontot a másodlagos példányhoz. A magánhálózati végpont automatikusan csatlakozni fog az új elsődleges példányhoz a feladatátvétel után. További információ a tárterület-redundancia lehetőségeiről: [Azure Storage redundancia](storage-redundancy.md).

A privát végpontok Storage-fiókhoz való létrehozásával kapcsolatos részletes információkért tekintse meg a következő cikkeket:

- [Magánhálózati kapcsolat létrehozása a Azure Portal Storage-fiókjának felhasználói felületén](../../private-link/create-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása a Azure Portal privát kapcsolati központjának használatával](../../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása az Azure CLI-vel](../../private-link/create-private-endpoint-cli.md)
- [Privát végpont létrehozása Azure PowerShell használatával](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Csatlakozás privát végpontokhoz

A privát végpontot használó VNet lévő ügyfeleknek ugyanazt a kapcsolati karakterláncot kell használniuk a Storage-fiókhoz, mint a nyilvános végponthoz csatlakozó ügyfelek. A DNS-feloldásra támaszkodva automatikusan átirányítja a kapcsolatokat a VNet a Storage-fiókba egy privát kapcsolaton keresztül.

> [!IMPORTANT]
> Ugyanazzal a kapcsolati karakterlánccal csatlakozhat a Storage-fiókhoz privát végpontok használatával, ahogy azt más célra is használni szeretné. Ne kapcsolódjon a Storage-fiókhoz az "*privatelink*" altartomány URL-címével.

A VNet csatolt [saját DNS-zónát](../../dns/private-dns-overview.md) hozunk létre a privát végpontokhoz szükséges frissítésekkel, alapértelmezés szerint. Ha azonban a saját DNS-kiszolgálóját használja, előfordulhat, hogy további módosításokat kell végeznie a DNS-konfigurációban. Az alábbi [DNS-változások](#dns-changes-for-private-endpoints) című szakasz a privát végpontokhoz szükséges frissítéseket ismerteti.

## <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai

Privát végpont létrehozásakor a rendszer a Storage-fiókhoz tartozó DNS CNAME erőforrásrekordot a "*privatelink*" előtaggal rendelkező altartományban lévő aliasra frissíti. Alapértelmezés szerint a "*privatelink*" altartománynak megfelelő [privát DNS-zónát](../../dns/private-dns-overview.md)is létrehozunk, a DNS a saját végpontokhoz tartozó erőforrásrekordokat.

Ha a VNet kívülről oldja fel a tárolási végpont URL-címét a privát végponttal, a rendszer a Storage szolgáltatás nyilvános végpontját oldja fel. A magánhálózati végpontot futtató VNet feloldva a tárolási végpont URL-címe feloldja a magánhálózati végpont IP-címét.

A fenti ábrán látható példában a "StorageAccountA" Storage-fiókhoz tartozó DNS-erőforrásrekordok a privát végpontot üzemeltető VNet kívülről történő feloldáskor a következők:

| Name                                                  | Típus  | Érték                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

Amint azt korábban említettük, a VNet kívüli ügyfelek számára a nyilvános végponton keresztül megtagadhatja vagy vezérelheti a hozzáférést a Storage tűzfal használatával.

A StorageAccountA tartozó DNS-erőforrásrekordok, amikor a privát végpontot üzemeltető VNet-ügyfél feloldotta a következőt:

| Name                                                  | Típus  | Érték                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Ez a megközelítés lehetővé teszi, hogy a Storage-fiókhoz **ugyanazt a kapcsolati karakterláncot használja** , mint a privát végpontokat üzemeltető VNet lévő ügyfelek, valamint a VNet kívüli ügyfelek számára.

Ha a hálózaton egyéni DNS-kiszolgálót használ, az ügyfeleknek fel kell tudniuk oldani a Storage-fiók végpontjának teljes tartománynevét a magánhálózati végpont IP-címére. A DNS-kiszolgálót úgy kell konfigurálni, hogy delegálja a magánhálózati kapcsolat altartományát a VNet tartozó magánhálózati DNS-zónához, vagy konfigurálja a "*StorageAccountA.privatelink.blob.Core.Windows.net*" rekordját a magánhálózati végpont IP-címével.

> [!TIP]
> Egyéni vagy helyszíni DNS-kiszolgáló használatakor a DNS-kiszolgálót úgy kell konfigurálni, hogy az "privatelink" altartományban lévő Storage-fiók nevét a magánhálózati végpont IP-címére oldja fel. Ezt úgy teheti meg, hogy delegálja a "privatelink" altartományt a VNet magánhálózati DNS-zónájába, vagy konfigurálja a DNS-zónát a DNS-kiszolgálón, és hozzáadja a DNS-rekordot.

A tárolási szolgáltatásokhoz tartozó magánhálózati végpontok ajánlott DNS-zónák neve a következő:

| Tárolási szolgáltatás        | Zóna neve                            |
| :--------------------- | :----------------------------------- |
| Blob szolgáltatás           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Fájlszolgáltatások           | `privatelink.file.core.windows.net`  |
| Queue szolgáltatás          | `privatelink.queue.core.windows.net` |
| Table service          | `privatelink.table.core.windows.net` |
| Statikus webhelyek        | `privatelink.web.core.windows.net`   |

A saját DNS-kiszolgáló magánhálózati végpontok támogatására való konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Azure virtuális hálózatokon található erőforrások névfeloldása](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [A magánhálózati végpontok DNS-konfigurációja](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Ismert problémák

Tartsa szem előtt az Azure Storage-hoz készült privát végpontok következő ismert problémáit.

### <a name="copy-blob-support"></a>BLOB-támogatás másolása

Ha a Storage-fiókot tűzfal védi, és a fiók privát végpontokon keresztül érhető el, akkor ez a fiók nem szolgálhat [másolási blob](/rest/api/storageservices/copy-blob) -művelet forrásaként.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Tároló-hozzáférési megkötések a virtuális hálózatok-beli ügyfelek számára privát végpontokkal

A meglévő privát végpontokkal rendelkező virtuális hálózatok-ügyfelek megkötést kaptak a privát végpontokkal rendelkező más Storage-fiókok elérésekor. Tegyük fel például, hogy egy VNet N1 rendelkezik egy saját végponttal az A1-es Storage-fiókhoz a blob Storage-hoz. Ha az A2-es Storage-fiókhoz privát végpont tartozik egy VNet N2-ben a blob Storage-hoz, akkor az VNet N1-ben lévő ügyfeleknek a privát végpont használatával is hozzá kell férniük a blob Storage-fiókhoz. Ha az A2-es Storage-fiók nem rendelkezik privát végpontokkal a blob Storage-hoz, akkor a VNet N1-ben lévő ügyfelek privát végpont nélkül férhetnek hozzá a fiókhoz a blob Storage-ban.

Ez a megkötés a DNS-módosítások eredményeként történt, amikor az A2-es fiók létrehoz egy magánhálózati végpontot.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Hálózat biztonsági csoportok szabályai a privát végpontokkal rendelkező alhálózatok esetében

Jelenleg nem konfigurálhatja a [hálózati biztonsági csoport](../../virtual-network/security-overview.md) (NSG) szabályait és a felhasználó által megadott útvonalakat a privát végpontokhoz. A privát végpontot működtető alhálózatra alkalmazott NSG-szabályok csak a privát végponton lévő többi végpontra (például hálózati adapterekre) vonatkoznak. A probléma korlátozott megkerülő megoldásként implementálja a privát végpontok hozzáférési szabályait a forrás alhálózatokon, bár ennél a megközelítésnél magasabb szintű felügyeleti terhelésre lehet szükség.

## <a name="next-steps"></a>További lépések

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md)
- [Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)
