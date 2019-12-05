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
ms.openlocfilehash: fff92057bc9812a5ef1488a46ed469382ad3ace3
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806881"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Privát végpontok használata az Azure Storage-hoz (előzetes verzió)

Az Azure Storage-fiókokhoz [privát végpontokat](../../private-link/private-endpoint-overview.md) is használhat, amelyek lehetővé teszik a virtuális hálózat (VNet) ügyfelei számára, hogy biztonságosan hozzáférjenek az adataihoz egy [privát kapcsolaton](../../private-link/private-link-overview.md)keresztül. A privát végpont IP-címet használ a Storage-fiók szolgáltatás VNet. A VNet és a Storage-fiók ügyfelei közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül halad, ami kiküszöböli a nyilvános internetről való kitettséget.

Privát végpontok használata a Storage-fiókhoz a következőket teszi lehetővé:
- A Storage-fiók biztonságossá tételéhez konfigurálja a Storage-tűzfalat úgy, hogy az a tárolási szolgáltatás nyilvános végpontján lévő összes kapcsolatot letiltsa.
- A virtuális hálózat (VNet) biztonságának növelésével letilthatja a VNet kiszűrése adatait.
- Biztonságosan csatlakozhat a Storage-fiókokhoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../../expressroute/expressroute-locations.md) [-](../../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a VNet.

## <a name="conceptual-overview"></a>Fogalmi áttekintés
![Privát végpontok az Azure Storage-hoz – áttekintés](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

A privát végpontok egy speciális hálózati adapterek egy Azure-szolgáltatáshoz a [Virtual Networkban](../../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot a Storage-fiókjához, biztonságos kapcsolatot biztosít a VNet található ügyfelek és a tároló között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a tárolási szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások zökkenőmentesen kapcsolódhatnak a tárolási szolgáltatáshoz a magánhálózati végponton keresztül, **ugyanazokkal a kapcsolati karakterláncokkal és engedélyezési mechanizmusokkal, amelyeket egyébként használni**fognak. A magánhálózati végpontok a Storage-fiók által támogatott összes protokollal használhatók, beleértve a REST és az SMB protokollt is.

A magánhálózati végpontok olyan alhálózatokban hozhatók létre, amelyek [szolgáltatási végpontokat](../../virtual-network/virtual-network-service-endpoints-overview.md)használnak. Az alhálózaton lévő ügyfelek így egy privát végpont használatával csatlakozhatnak egy Storage-fiókhoz, míg más szolgáltatás-végpontok használatával is hozzáférhetnek.

Ha létrehoz egy privát végpontot a virtuális hálózaton található egyik tárolási szolgáltatáshoz, a rendszer egy hozzájárulási kérést küld a tárfiók tulajdonosának jóváhagyás céljából. Ha a privát végpont létrehozását kérő felhasználó a Storage-fiók tulajdonosa is, akkor a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja.

A Storage-fiók tulajdonosai a [Azure Portal](https://portal.azure.com)a Storage-fiókhoz tartozó*privát végpontok*lapján kezelhetik a belefoglalt kérelmeket és a privát végpontokat.

> [!TIP]
> Ha csak a privát végponton keresztül szeretné korlátozni a Storage-fiók elérését, konfigurálja a tárolási tűzfalat a nyilvános végponton keresztüli hozzáférés megtagadásához vagy vezérléséhez.

A Storage-fiók úgy is biztosítható, hogy csak a VNet érkező kapcsolatokat fogadja el, ha úgy [konfigurálja a tárolási tűzfalat](storage-network-security.md#change-the-default-network-access-rule) , hogy alapértelmezés szerint megtagadja a hozzáférést a nyilvános végponton keresztül. Nincs szükség olyan tűzfalszabályre, amely engedélyezi a forgalmat egy privát végponttal rendelkező VNet, mivel a tárolási tűzfal csak a nyilvános végponton keresztüli hozzáférést vezérli. A magánhálózati végpontok Ehelyett az alhálózatokhoz való hozzáférést biztosító engedélyezési folyamaton alapulnak.

### <a name="private-endpoints-for-storage-service"></a>Privát végpontok tárolási szolgáltatáshoz

A magánhálózati végpont létrehozásakor meg kell adnia a Storage-fiókot és a tárolási szolgáltatást, amelyhez csatlakozik. Külön privát végpontra van szükség az egyes tárolási szolgáltatásokhoz egy olyan Storage-fiókban, amelyhez hozzá kell férnie, azaz [blobokhoz](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2okhoz](../blobs/data-lake-storage-introduction.md), [fájlokhoz](../files/storage-files-introduction.md), [várólistákhoz](../queues/storage-queues-introduction.md), [táblákhoz](../tables/table-storage-overview.md)vagy [statikus webhelyekhez](../blobs/storage-blob-static-website.md).

> [!TIP]
> Hozzon létre egy külön privát végpontot a Storage szolgáltatás másodlagos példányához az RA-GRS-fiókok jobb olvasási teljesítményéhez.

Ha olvasási hozzáférésre van szüksége egy [írásvédett földrajzi redundáns Storage-fiókhoz](storage-redundancy-grs.md#read-access-geo-redundant-storage), külön magánhálózati végpontokra van szükség a szolgáltatás elsődleges és másodlagos példányai esetében is. A **feladatátvételhez**nem kell létrehoznia privát végpontot a másodlagos példányhoz. A magánhálózati végpont automatikusan csatlakozni fog az új elsődleges példányhoz a feladatátvétel után.

#### <a name="resources"></a>Segédanyagok és eszközök

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

| Név                                                  | Type (Típus)  | Value (Díj)                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<Storage szolgáltatás nyilvános végpontja\>                   |
| \<Storage szolgáltatás nyilvános végpontja\>                   | A     | \<Storage szolgáltatás nyilvános IP-címe\>                 |

Amint azt korábban említettük, a VNet kívüli ügyfelek számára a nyilvános végponton keresztül megtagadhatja vagy vezérelheti a hozzáférést a Storage tűzfal használatával.

A StorageAccountA tartozó DNS-erőforrásrekordok, amikor a privát végpontot üzemeltető VNet-ügyfél feloldotta a következőt:

| Név                                                  | Type (Típus)  | Value (Díj)                                                 |
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
| Blob service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Fájlszolgáltatás           | `privatelink.file.core.windows.net`  |
| Queue szolgáltatás          | `privatelink.queue.core.windows.net` |
| Table service          | `privatelink.table.core.windows.net` |
| Statikus webhelyek        | `privatelink.web.core.windows.net`   |

#### <a name="resources"></a>Segédanyagok és eszközök

A saját DNS-kiszolgáló magánhálózati végpontok támogatására való konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Azure virtuális hálózatokon található erőforrások névfeloldása](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [A magánhálózati végpontok DNS-konfigurációja](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Ismert problémák

### <a name="copy-blob-support"></a>BLOB-támogatás másolása

Az előzetes verzió ideje alatt nem támogatott a privát végpontokon keresztül elért Storage-fiókok számára kiadott blob-parancsok [másolása](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) , ha a forrásként szolgáló Storage-fiókot tűzfal védi.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Tároló-hozzáférési megkötések a virtuális hálózatok-beli ügyfelek számára privát végpontokkal

A meglévő privát végpontokkal rendelkező virtuális hálózatok-ügyfelek megkötést kaptak a privát végpontokkal rendelkező más Storage-fiókok elérésekor. Tegyük fel például, hogy egy VNet N1-hez saját végpont tartozik az A1-es Storage-fiókhoz, azaz a blob szolgáltatáshoz. Ha az A2-es Storage-fiók privát végponttal rendelkezik a blob szolgáltatáshoz tartozó VNet N2-ben, akkor az VNet N1-ben lévő ügyfeleknek a privát végpont használatával is el kell érniük az A2-es fiók blob szolgáltatását. Ha az A2-es Storage-fiók nem rendelkezik privát végpontokkal a blob szolgáltatáshoz, akkor a VNet N1-ben lévő ügyfelek privát végpont nélkül is hozzáférhetnek a blob szolgáltatáshoz.

Ez a megkötés a DNS-módosítások eredményeként történt, amikor az A2-es fiók létrehoz egy magánhálózati végpontot.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Hálózati biztonsági csoportra vonatkozó szabályok privát végpontokkal rendelkező alhálózatokhoz

Jelenleg nem lehet konfigurálni a [hálózati biztonsági csoport](../../virtual-network/security-overview.md) (NSG) szabályait privát végpontokkal rendelkező alhálózatokhoz. A probléma korlátozott megkerülő megoldásként implementálja a privát végpontok hozzáférési szabályait a forrás alhálózatokon, bár ennél a megközelítésnél magasabb szintű felügyeleti terhelésre lehet szükség.
