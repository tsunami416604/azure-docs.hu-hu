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
ms.openlocfilehash: aec12cee7466e59389b28742bf66247751a0d22b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949468"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Privát végpontok használata az Azure Storage-hoz (előzetes verzió)

Az Azure Storage lehetővé teszi [privát végpontok](../../private-link/private-endpoint-overview.md) használatát egy virtuális hálózaton (VNet) lévő ügyfelek számára, hogy biztonságosan hozzáférjenek a Storage-fiókban lévő, [privát kapcsolaton](../../private-link/private-link-overview.md)keresztül. A privát végpont IP-címet használ a Storage-fiók szolgáltatás VNet. A VNet és a Storage-fiók ügyfelei közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül halad, ami kiküszöböli a nyilvános internetről való kitettséget.

Privát végpontok használata a Storage-fiókhoz a következőket teszi lehetővé:
- A Storage-fiók biztonságossá tételéhez konfigurálja a Storage-tűzfalat úgy, hogy az a tárolási szolgáltatás nyilvános végpontján lévő összes kapcsolatot letiltsa.
- A virtuális hálózat (VNet) biztonságának növelése azáltal, hogy letiltja a VNet kiszűrése adatait.
- Biztonságosan csatlakozhat a Storage-fiókokhoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../../expressroute/expressroute-locations.md) [-](../../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a VNet.

## <a name="conceptual-overview"></a>Fogalmi áttekintés
![Privát végpontok az Azure Storage-hoz – áttekintés](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

A privát végpont az Azure-szolgáltatásokhoz tartozó [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNet) speciális hálózati adaptere. Biztonságos kapcsolatot biztosít az ügyfelek között a VNet és a Storage-fiókban. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a tárolási szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások zökkenőmentesen kapcsolódhatnak a tárolási szolgáltatáshoz a magánhálózati végponton keresztül, ugyanazokkal a kapcsolati karakterláncokkal és engedélyezési mechanizmusokkal, amelyeket egyébként használni fognak. A magánhálózati végpontok a Storage-fiók által támogatott összes protokollal használhatók, beleértve a REST és az SMB protokollt is.

Amikor saját VNet hoz létre egy tárolási szolgáltatáshoz egy magánhálózati végpontot, a rendszer beleegyezési kérelmet küld jóváhagyásra a Storage-fiók tulajdonosának. Ha a privát végpont létrehozását kérő felhasználó a Storage-fiók tulajdonosa is, akkor a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja.

A Storage-fiók tulajdonosai elfogadják vagy elutasítja a jóváhagyási kérelmeket, valamint megtekinthetik vagy kezelhetik a privát végpontokat a [Azure Portal](https://portal.azure.com)lévő Storage-fiókhoz tartozó magánhálózati végpontok lapján.

A Storage-fiók úgy is biztosítható, hogy csak a VNet érkező kapcsolatokat fogadja el, ha úgy [konfigurálja a tárolási tűzfalat](storage-network-security.md#change-the-default-network-access-rule) , hogy alapértelmezés szerint megtagadja a hozzáférést a nyilvános végponton keresztül. Nincs szükség olyan tárolási tűzfalszabályre, amely engedélyezi a forgalmat egy privát végponttal rendelkező VNet, mivel a tárolási tűzfalszabályok csak a nyilvános végpontra vonatkoznak. A magánhálózati végpontok Ehelyett az alhálózatokhoz való hozzáférést biztosító engedélyezési folyamaton alapulnak.

### <a name="private-endpoints-for-storage-service"></a>Privát végpontok tárolási szolgáltatáshoz

A magánhálózati végpont létrehozásakor meg kell adnia a Storage-fiókot és a tárolási szolgáltatást, amelyhez csatlakozik. Minden tárolási szolgáltatáshoz szükség van egy magánhálózati végpontra egy olyan Storage-fiókban, amelyhez hozzáférésre van szükség, azaz [Blobok](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [fájlok](../files/storage-files-introduction.md), [várólisták](../queues/storage-queues-introduction.md), [táblák](../tables/table-storage-overview.md)vagy [statikus webhelyek](../blobs/storage-blob-static-website.md).

Az [olvasási hozzáférésű földrajzi redundáns Storage-fiók](storage-redundancy-grs.md#read-access-geo-redundant-storage)olvasási elérhetőségének biztosításához külön magánhálózati végpontokra van szükség a szolgáltatás elsődleges és másodlagos példányai esetében is.

#### <a name="resources"></a>Segédanyagok és eszközök

A privát végpontok Storage-fiókhoz való létrehozásával kapcsolatos részletes információkért tekintse meg a következő cikkeket:

- [Magánhálózati kapcsolat létrehozása a Azure Portal Storage-fiókjának felhasználói felületén](../../private-link/create-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása a Azure Portal privát kapcsolati központjának használatával](../../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása az Azure CLI-vel](../../private-link/create-private-endpoint-cli.md)
- [Privát végpont létrehozása Azure PowerShell használatával](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai

Amikor létrehoz egy magánhálózati végpontot a tárolási szolgáltatáshoz, a tárolási végponthoz tartozó DNS CNAME erőforrásrekordot a "*privatelink*" előtaggal rendelkező altartományban található aliasra frissíti. Alapértelmezés szerint a VNet csatolt [privát DNS-zónát](../../dns/private-dns-overview.md) is létrehozunk. Ez a saját DNS-zóna megfelel a (z)*privatelink*előtaggal rendelkező altartománynak, és tartalmazza A DNS a magánhálózati végpontokhoz tartozó erőforrásrekordokat.

Ha a tárolási végpont URL-címét azon a VNet kívülről oldja fel, amelyben a magánhálózati végpont létrejött, a rendszer továbbra is feloldja a tárolási szolgáltatás nyilvános végpontját. A magánhálózati végpontot futtató VNet feloldva a tárolási végpont URL-címe feloldja a magánhálózati végpont IP-címét.

A fenti ábrán látható példában a "StorageAccountA" Storage-fiókhoz tartozó DNS-erőforrásrekordok a privát végpontot üzemeltető VNet kívülről történő feloldáskor a következők:

| Név                                                  | Type (Típus)  | Value (Díj)                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<nyilvános végpont\>                                   |
| \<nyilvános végpont\>                                   | A     | \<Storage szolgáltatás nyilvános IP-címe\>                 |

Amint azt korábban említettük, a tárolási tűzfal használatával megtagadhatja az összes hozzáférést a nyilvános végponton keresztül.

A StorageAccountA tartozó DNS-erőforrásrekordok, amikor a privát végpontot üzemeltető VNet-ügyfél feloldotta a következőt:

| Név                                                  | Type (Típus)  | Value (Díj)                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Ez a megközelítés lehetővé teszi a Storage-fiók elérését a privát végpontokat üzemeltető VNet és a VNet kívüli ügyfelekkel azonos kapcsolati sztring használatával. A tárolási tűzfal használatával megtagadhatja a hozzáférést a VNet kívüli összes ügyfélhez.

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Ismert problémák

### <a name="copy-blob-failures"></a>BLOB-hibák másolása

Jelenleg a privát végpontokon keresztül elért Storage-fiókok számára kiállított blob-parancsok [másolása](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) meghiúsul, ha a forrás Storage-fiókot tűzfal védi.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Tároló-hozzáférési megkötések a virtuális hálózatok-beli ügyfelek számára privát végpontokkal

Azok a virtuális hálózatok-ügyfelek, amelyek meglévő Storage Private-végponttal rendelkeznek, megkötést képeznek a privát végpontokkal rendelkező más Storage-fiókok eléréséhez. Tegyük fel például, hogy egy VNet N1-hez saját végpont tartozik az A1-es Storage-fiókhoz, azaz a blob szolgáltatáshoz. Ha az A2-es Storage-fiók privát végponttal rendelkezik a blob szolgáltatáshoz tartozó VNet N2-ben, akkor a VNet N1-ben lévő ügyfeleknek egy privát végpont használatával is el kell érniük a blob szolgáltatást az A2-es fiókban. Ha az A2-es Storage-fiók nem rendelkezik privát végpontokkal a blob szolgáltatáshoz, akkor a VNet N1-ben lévő ügyfelek privát végpont nélkül is hozzáférhetnek a blob szolgáltatáshoz.

Ez a megkötés a DNS-módosítások eredményeként történt, amikor az A2-es fiók létrehoz egy magánhálózati végpontot.

### <a name="nsg-rules-on-subnets-with-private-endpoints"></a>NSG szabályok az alhálózatokon privát végpontokkal

A [hálózati biztonsági csoport](../../virtual-network/security-overview.md) (NSG) szabályai jelenleg nem konfigurálhatók privát végpontokkal rendelkező alhálózatokhoz. A probléma korlátozott megkerülő megoldásként implementálja a privát végpontok hozzáférési szabályait a forrás alhálózatokon, bár ennél a megközelítésnél magasabb szintű felügyeleti terhelésre lehet szükség.
