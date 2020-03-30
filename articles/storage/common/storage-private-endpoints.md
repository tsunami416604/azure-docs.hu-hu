---
title: Magánjellegű végpontok használata
titleSuffix: Azure Storage
description: A virtuális hálózatokból származó tárfiókokhoz való biztonságos hozzáféréshez szolgáló privát végpontok áttekintése.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299056"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Privát végpontok használata az Azure Storage-hoz

Az Azure Storage-fiókok [privát végpontjaisegítségével](../../private-link/private-endpoint-overview.md) engedélyezheti a virtuális hálózaton (VNet) lévő ügyfelek számára az adatok biztonságos [elérését egy privát kapcsolaton](../../private-link/private-link-overview.md)keresztül. A privát végpont egy IP-címet használ a virtuális hálózat címterületa a tárfiók szolgáltatás. A virtuális hálózaton lévő ügyfelek és a tárfiók közötti hálózati forgalom áthalad a virtuális hálózaton és egy privát kapcsolaton a Microsoft gerinchálózaton, így kiküszöbölve a nyilvános internetről származó expozíciót.

A tárfiók privát végpontjainak használata lehetővé teszi a következőket:

- A tárfiókot úgy védheti meg, hogy a tárolótűzfalat úgy állítja be, hogy a storage-szolgáltatás nyilvános végpontján lévő összes kapcsolatot letiltsa.
- Növelje a virtuális hálózat (VNet) biztonságát azáltal, hogy lehetővé teszi az adatok kiszivárgásának letiltását a virtuális hálózatról.
- Biztonságosan csatlakozhat a helyszíni hálózatokból származó tárfiókokhoz, amelyek [VPN vagy](../../vpn-gateway/vpn-gateway-about-vpngateways.md) [ExpressRoutes](../../expressroute/expressroute-locations.md) használatával csatlakoznak a virtuális hálózathoz, privát társviszony-létesítéssel.

## <a name="conceptual-overview"></a>Fogalmi áttekintés

![Az Azure Storage privát végpontjainak áttekintése](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

A privát végpont egy speciális hálózati felület egy Azure-szolgáltatás a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot a tárfiókhoz, biztonságos kapcsolatot biztosít a virtuális hálózaton lévő ügyfelek és a tárban lévő ügyfelek között. A privát végpont hoz egy IP-címet a virtuális hálózat IP-címtartományából. A privát végpont és a tárolószolgáltatás közötti kapcsolat biztonságos privát kapcsolatot használ.

A virtuális hálózatban lévő alkalmazások zökkenőmentesen csatlakozhatnak a tárolószolgáltatáshoz a magánvégponton keresztül, **ugyanazokat a kapcsolati karakterláncokat és engedélyezési mechanizmusokat használva, amelyeket egyébként használnának.** A magánvégpontok a tárfiók által támogatott összes protokollhoz használhatók, beleértve a REST-et és az SMB-t is.

A szolgáltatásvégpontokat használó alhálózatokban privát [végpontok hozhatók](../../virtual-network/virtual-network-service-endpoints-overview.md)létre. Az alhálózatban lévő ügyfelek így csatlakozhatnak egy tárfiókhoz a privát végpont használatával, miközben szolgáltatásvégpontokat használnak mások eléréséhez.

Ha létrehoz egy privát végpontot a virtuális hálózaton található egyik tárolási szolgáltatáshoz, a rendszer egy hozzájárulási kérést küld a tárfiók tulajdonosának jóváhagyás céljából. Ha a privát végpont létrehozását kérő felhasználó is a tárfiók tulajdonosa, ez a hozzájárulási kérelem automatikusan jóváhagyásra kerül.

A tárfiók-tulajdonosok az [Azure Portalon](https://portal.azure.com)található tárfiók "*privát végpontok*' lapon kezelhetik a hozzájárulási kérelmeket és a privát végpontokat.

> [!TIP]
> Ha csak a magánvégponton keresztül szeretné korlátozni a tárfiókhoz való hozzáférést, konfigurálja a tárolótűzfalat úgy, hogy megtagadja vagy szabályozza a hozzáférést a nyilvános végponton keresztül.

A tárfiókot csak a virtuális hálózatból érkező kapcsolatok fogadására biztosíthatja, ha [a tárolótűzfal alapértelmezés](storage-network-security.md#change-the-default-network-access-rule) szerint megtagadja a hozzáférést a nyilvános végponton keresztül. Nincs szükség tűzfalszabályra ahhoz, hogy privát végponttal rendelkező virtuális hálózatról lehessen forgalmat használni, mivel a tárolótűzfal csak a nyilvános végponton keresztül szabályozza a hozzáférést. A privát végpontok ehelyett a hozzájárulási folyamatra támaszkodnak a storage-szolgáltatáshoz való hozzáférés biztosításához.

### <a name="private-endpoints-for-azure-storage"></a>Privát végpontok az Azure Storage-hoz

A privát végpont létrehozásakor meg kell adnia a tárfiókot és a storage-szolgáltatás, amelyhez csatlakozik. Külön saját végpontra van szüksége minden olyan tárfiókban lévő tárolószolgáltatáshoz, amelyet el kell érnie, nevezetesen [a Blobs,](../blobs/storage-blobs-overview.md) [a Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [a Files,](../files/storage-files-introduction.md) [a Queues,](../queues/storage-queues-introduction.md) [a Tables](../tables/table-storage-overview.md)vagy a [Static Websites](../blobs/storage-blob-static-website.md).

> [!TIP]
> Hozzon létre egy külön privát végpontot a tárolási szolgáltatás másodlagos példányához az RA-GRS-fiókok jobb olvasási teljesítményéhez.

A másodlagos régióhoz a georedundáns tároláshoz konfigurált tárfiókkal rendelkező olvasási hozzáféréshez külön privát végpontokra van szükség a szolgáltatás elsődleges és másodlagos példányaihoz. Nem kell létrehoznia egy privát végpontot a **feladatátvétel**másodlagos példányához. A privát végpont automatikusan csatlakozik az új elsődleges példányhoz a feladatátvétel után. A tárolási redundancia beállításairól az [Azure Storage redundanciája](storage-redundancy.md)című témakörben talál további információt.

A tárfiók hozásáról az alábbi cikkekben tájékozódhat:

- [Az Azure Portalon a Tárfiók ból privát módon csatlakozhat egy tárfiókhoz](../../private-link/create-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása az Azure Portal privát kapcsolati központjával](../../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása az Azure CLI használatával](../../private-link/create-private-endpoint-cli.md)
- [Privát végpont létrehozása az Azure PowerShell használatával](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Csatlakozás privát végpontokhoz

A privát végpontot használó virtuális hálózaton lévő ügyfeleknek ugyanazt a kapcsolati karakterláncot kell használniuk a tárfiókhoz, mint a nyilvános végponthoz csatlakozó ügyfelek. A DNS-feloldásra támaszkodva automatikusan továbbítjuk a kapcsolatot a virtuális hálózatról a tárfiókba egy privát kapcsolaton keresztül.

> [!IMPORTANT]
> Használja ugyanazt a kapcsolati karakterláncot a tárfiókhoz való csatlakozáshoz a magánvégpontok használatával, ahogy egyébként használná. Kérjük, ne csatlakozzon a tárfiókhoz a "*privatelink*" altartomány URL-címével.

Létrehozunk egy [privát DNS-zónát](../../dns/private-dns-overview.md) a virtuális hálózathoz, alapértelmezés szerint a privát végpontok szükséges frissítéseivel. Ha azonban saját DNS-kiszolgálót használ, előfordulhat, hogy további módosításokat kell végrehajtania a DNS-konfiguráción. Az alábbi [DNS-módosításokról](#dns-changes-for-private-endpoints) szóló szakasz a magánvégpontokhoz szükséges frissítéseket ismerteti.

## <a name="dns-changes-for-private-endpoints"></a>DNS-módosítások a magánvégpontokhoz

Privát végpont létrehozásakor a tárfiók DNS CNAME erőforrásrekordja egy "*privatelink*" előtaggal rendelkező altartomány aliasára frissül. Alapértelmezés szerint létrehozunk egy [privát DNS-zónát](../../dns/private-dns-overview.md)is, amely a "*privatelink*" altartománynak felel meg, a magánvégpontok DNS A erőforrásrekordjaival.

Ha a tárolóvégpont URL-címét a virtuális hálózaton kívülről a magánvégpont, feloldja a storage-szolgáltatás nyilvános végpontjára. A magánvégpontot üzemeltető virtuális hálózatról feloldva a tárolóvégpont URL-címe a privát végpont IP-címére oldódik fel.

A fenti példa esetében a "StorageAccountA" tárfiók DNS-erőforrásrekordjai, ha a privát végpontot üzemeltető virtuális hálózaton kívülről oldják fel, a következők lesznek:

| Név                                                  | Típus  | Érték                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<tárolási szolgáltatás nyilvános végpontja\>                   |
| \<tárolási szolgáltatás nyilvános végpontja\>                   | A     | \<tárolási szolgáltatás nyilvános IP-címe\>                 |

Mint korábban említettük, megtagadhatja vagy szabályozhatja a virtuális hálózaton kívüli ügyfelek hozzáférését a nyilvános végponton keresztül a tároló tűzfal használatával.

A StorageAccountA DNS-erőforrásrekordjai, ha a privát végpontot üzemeltető virtuális hálózat egyik ügyfele feloldja őket, a következők lesznek:

| Név                                                  | Típus  | Érték                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Ez a megközelítés lehetővé teszi a hozzáférést a **tárfiókhoz, ugyanazt a kapcsolati karakterláncot használva** a privát végpontokat üzemeltető virtuális hálózaton lévő ügyfelek, valamint a virtuális hálózaton kívüli ügyfelek számára.

Ha egyéni DNS-kiszolgálót használ a hálózaton, az ügyfeleknek képesnek kell lenniük a tárfiók végpontjának teljes tartománynát a privát végpont IP-címére feloldani. A DNS-kiszolgálót úgy kell konfigurálnia, hogy a privát hivatkozásaltartományt delegálja a virtuális hálózat privát DNS-zónájába, vagy konfigurálja az A rekordokat a '*StorageAccountA.privatelink.blob.core.windows.net*' számára a privát végpont IP-címével.

> [!TIP]
> Egyéni vagy helyszíni DNS-kiszolgáló használataesetén konfigurálja úgy a DNS-kiszolgálót, hogy a "privatelink" altartományban lévő tárfiók nevét feloldja a privát végpont IP-címére. Ezt úgy teheti meg, hogy a "privatelink" altartományt delegálja a virtuális hálózat privát DNS-zónájára, vagy konfigurálja a DNS-zónát a DNS-kiszolgálón, és hozzáadja az A DNS-rekordokat.

A tárolási szolgáltatások privát végpontjainak ajánlott DNS-zónanevei a következők:

| Tárolási szolgáltatás        | Zóna neve                            |
| :--------------------- | :----------------------------------- |
| Blob szolgáltatás           | `privatelink.blob.core.windows.net`  |
| 2. generációs Data Lake Storage | `privatelink.dfs.core.windows.net`   |
| Fájlszolgáltatás           | `privatelink.file.core.windows.net`  |
| Várólista-szolgáltatás          | `privatelink.queue.core.windows.net` |
| Táblaszolgáltatás          | `privatelink.table.core.windows.net` |
| Statikus weboldalak        | `privatelink.web.core.windows.net`   |

A saját DNS-kiszolgáló magánvégpontok támogatására történő konfigurálásáról az alábbi cikkekben tájékozódhat:

- [Azure virtuális hálózatokon található erőforrások névfeloldása](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [DNS-konfiguráció magánvégpontokhoz](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos részletekről az [Azure Private Link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Ismert problémák

Ne feledje, hogy az azure storage-beli privát végpontokkal kapcsolatos alábbi ismert problémák.

### <a name="copy-blob-support"></a>Blob másolása – támogatás

Ha a tárfiókot tűzfal védi, és a fiók privát végpontokon keresztül érhető el, akkor ez a fiók nem szolgálhat a [Copy Blob](/rest/api/storageservices/copy-blob) művelet forrásaként.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Tárolási hozzáférési korlátozások a privát végponttal rendelkező virtuális hálózatokban lévő ügyfelek számára

A meglévő magánvégpontokkal rendelkező virtuális hálózatokban lévő ügyfelek korlátozásokkal szembesülnek más, magán-hozzáférési pontokkal rendelkező tárfiókok elérésekor. Például tegyük fel, hogy egy virtuális hálózat N1 rendelkezik egy privát végpont egy ablob storage A1 tárfiók. Ha az A2 tárfiók rendelkezik egy virtuális hálózat N2 blobstorage-ban egy privát végpont, majd a Virtuálishálózat N1 ügyfelek is el kell érnie a Blob storage-fiók Ban A2 egy privát végpont használatával. Ha az A2 tárfiók nem rendelkezik privát végpontokkal a Blob storage számára, majd az N1 virtuális hálózatban lévő ügyfelek privát végpont nélkül férhetnek hozzá a Blob storage-hoz.

Ez a megkötés az A2 fiók privát végpontlétrehozásakor végrehajtott DNS-módosítások eredménye.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Hálózat biztonsági csoportok szabályai a privát végpontokkal rendelkező alhálózatok esetében

Jelenleg nem konfigurálható [a hálózati biztonsági csoport](../../virtual-network/security-overview.md) (NSG) szabályai és a felhasználó által definiált útvonalak a magánvégpontok. A privát végpontot üzemeltető alhálózatra alkalmazott NSG-szabályok a privát végpontra vonatkoznak. A probléma korlátozott megoldása a forrásalhálózatok on-t magánhálózati végpontokra vonatkozó hozzáférési szabályok megvalósítása, bár ez a megközelítés magasabb felügyeleti többletterhelést igényelhet.

## <a name="next-steps"></a>További lépések

- [Az Azure Storage tűzfalainak és virtuális hálózatainak konfigurálása](storage-network-security.md)
- [Biztonsági javaslatok a Blob storage-hoz](../blobs/security-recommendations.md)
