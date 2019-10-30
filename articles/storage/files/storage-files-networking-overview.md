---
title: Azure Files hálózatkezelési megfontolások | Microsoft Docs
description: A Azure Files hálózati beállításainak áttekintése.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141793"
---
# <a name="azure-files-networking-considerations"></a>Azure Files hálózati megfontolások 
Az Azure-fájlmegosztás két módon is kapcsolódhat:

- A megosztás elérése közvetlenül az SMB-vagy a kiosztott protokollon keresztül. Ez a hozzáférési minta elsősorban akkor alkalmazható, ha a lehető legtöbb helyszíni kiszolgálót el szeretné távolítani.
- Az Azure-fájlmegosztás gyorsítótárának létrehozása egy helyszíni kiszolgálón Azure File Sync használatával, valamint a fájlmegosztás adatainak elérése a helyszíni kiszolgálóról az Ön által választott protokollal (SMB, NFS, FTPS stb.) a használati esethez. Ez a hozzáférési minta hasznos, mivel az a legjobbat ötvözi a helyszíni teljesítmény és a Felhőbeli skálázás, valamint a kiszolgáló nélküli (például Azure Backup) szolgáltatások számára.

Ez a cikk azt ismerteti, hogyan kell konfigurálni a hálózatkezelést, ha a használati eset a Azure File Sync használata helyett közvetlenül az Azure-fájlmegosztás elérését kéri. A Azure File Sync központi telepítés hálózatkezelési szempontjaival kapcsolatos további információkért lásd: [Azure file Sync proxy-és tűzfalbeállítások konfigurálása](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Storage-fiók beállításai
A Storage-fiók egy olyan felügyeleti szerkezet, amely egy megosztott tárolót jelöl, amelyben több fájlmegosztást is üzembe helyezhet, valamint más tárolási erőforrásokat, például blob-tárolókat vagy várólistákat. Az Azure Storage-fiókok két alapszintű beállítást biztosítanak a hálózat biztonságossá tételéhez: az átvitel és a tűzfalak, valamint a virtuális hálózatok (virtuális hálózatok) titkosítása.

### <a name="encryption-in-transit"></a>Titkosítás átvitel közben
Alapértelmezés szerint az összes Azure Storage-fióknál engedélyezve van az átvitel titkosítása. Ez azt jelenti, hogy amikor az SMB-n keresztül csatlakoztat egy fájlmegosztást, vagy a kiosztott protokollon keresztül éri el (például a Azure Portal, a PowerShell/CLI vagy az Azure SDK-k használatával), Azure Files csak akkor engedélyezi a kapcsolatot, ha az SMB 3.0 + titkosítással vagy HTTPS-vel van ellátva. Azok az ügyfelek, amelyek nem támogatják az SMB 3,0-et vagy az SMB 3,0-et támogató ügyfeleket, de az SMB-titkosítást nem, nem fogják tudni csatlakoztatni az Azure-fájlmegosztást, ha engedélyezve van az átvitel titkosítása. Ha további információt szeretne arról, hogy mely operációs rendszerek támogatják az SMB 3,0 titkosítást, tekintse meg a [Windows](storage-how-to-use-files-windows.md), a [MacOS](storage-how-to-use-files-mac.md)és a [Linux](storage-how-to-use-files-linux.md)részletes dokumentációját. A PowerShell, CLI és SDK összes jelenlegi verziója támogatja a HTTPS-t.  

Egy Azure Storage-fiók esetében letilthatja a titkosítást az átvitel során. Ha a titkosítás le van tiltva, akkor a Azure Files az SMB 2,1, az SMB 3,0 titkosítás nélkül, és nem titkosított, nem titkosított API-hívásokat tesz lehetővé HTTP-n keresztül. Az átvitel közbeni titkosítás letiltásának elsődleges oka az olyan örökölt alkalmazások támogatása, amelyeknek régebbi operációs rendszeren kell futniuk, például Windows Server 2008 R2 vagy régebbi Linux-disztribúció. Azure Files csak az Azure-fájlmegosztás azonos Azure-régiójában lévő SMB 2,1-kapcsolatokat engedélyezi. Az Azure-fájlmegosztás (például a helyszíni vagy egy másik Azure-régió) Azure-régióján kívüli SMB 2,1-ügyfél nem fog tudni hozzáférni a fájlmegosztás eléréséhez.

További információ az átvitel közbeni titkosításról: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Tűzfalak és virtuális hálózatok 
A tűzfal olyan hálózati házirend, amely számára a kérések hozzáférhetnek az Azure-fájlmegosztás és a Storage-fiók egyéb tárolási erőforrásaihoz. Ha a Storage-fiókot az alapértelmezett hálózati beállításokkal hozza létre, az nem korlátozódik egy adott hálózatra, ezért az Internet elérhető. Ez nem jelenti azt, hogy az interneten bárki hozzáférhet a Storage-fiókban üzemeltetett Azure-fájlmegosztás adataihoz, hanem úgy, hogy a Storage-fiók bármilyen hálózatról fogadja a jogosult kérelmeket. A kérelmeket a Storage-fiók kulcsa, a közös hozzáférésű aláírás (SAS) jogkivonata (csak a legtöbbet), vagy egy Active Directory felhasználói tag számára lehet engedélyezni. 

A Storage-fiókhoz tartozó tűzfalszabályok az egyes IP-címekre, tartományokra vagy virtuális hálózatokra való hozzáférés korlátozására használhatók. Általánosságban elmondható, hogy egy Storage-fiókhoz tartozó tűzfalszabályok többsége korlátozza a virtuális hálózathoz való hálózati hozzáférést. 

Egy [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)vagy VNet hasonló egy hagyományos hálózathoz, amelyet a saját adatközpontjában szeretne használni. Lehetővé teszi, hogy biztonságos kommunikációs csatornát hozzon létre az Azure-erőforrások, például az Azure-fájlmegosztás, a virtuális gépek, az SQL-adatbázisok stb. számára az egymással való kommunikációhoz. Az Azure Storage-fiókokhoz vagy az Azure-beli virtuális gépekhez hasonlóan a VNet egy erőforráscsoport-ban üzembe helyezett Azure-erőforrás. A további hálózatkezelési beállításokkal az Azure virtuális hálózatok is kapcsolódhat a helyszíni hálózatokhoz.

Ha egy Azure-beli virtuális gépet egy virtuális hálózathoz adnak hozzá, a virtuális géphez csatolt virtuális hálózati adapter (NIC) kifejezetten a VNet korlátozódik. Ez azért lehetséges, mert az Azure-beli virtuális gépek virtualizált számítógépek, amelyek természetesen rendelkeznek hálózati adapterekkel. A virtuális gépeket az Azure infrastruktúra-szolgáltatásként vagy IaaS, a termék felállása részeként kínáljuk. Mivel az Azure-fájlmegosztás kiszolgáló nélküli fájlmegosztás, nem rendelkezik hálózati adapterrel a VNet való hozzáadáshoz. Más módon, Azure Files az Azure szolgáltatásként nyújtott platformként, vagy a "Péter" termék felállásának részeként érhető el. Ahhoz, hogy egy Storage-fiók egy VNet részévé váljon, az Azure támogatja a szolgáltatás-végpontoknak nevezett Pásti-szolgáltatásokra vonatkozó koncepciót. A szolgáltatás-végpont lehetővé teszi a Pásti-szolgáltatások számára a virtuális hálózatok részét. További információ a szolgáltatási végpontokról: [Virtual Network szolgáltatás végpontjai](../../virtual-network/virtual-network-service-endpoints-overview.md).

Egy Storage-fiók egy vagy több virtuális hálózathoz is felvehető. További információ a Storage-fiók virtuális hálózathoz való hozzáadásáról vagy más tűzfalbeállítások konfigurálásáról: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Azure-hálózatkezelés
Alapértelmezés szerint az Azure-szolgáltatások, például a Azure Files az interneten keresztül érhetők el. Mivel a Storage-fiókhoz való alapértelmezett forgalom titkosítva van (és az SMB 2,1-csatlakoztatások soha nem engedélyezettek az Azure-régión kívül), az Azure-fájlmegosztás interneten keresztüli elérésével kapcsolatban semmi nem biztonságos. A szervezet házirendje vagy egyedi szabályozási követelményei alapján az Azure-vel való szigorúbb kommunikációra lehet szükség, így az Azure számos módot biztosít arra, hogy az Azure-on kívülről érkező adatforgalom hogyan Azure Files. Az Azure-fájlmegosztás a következő szolgáltatási ajánlatokkal való elérésekor tovább biztonságossá teheti a hálózatkezelést:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): a VPN Gateway egy adott típusú virtuális hálózati átjáró, amely egy Azure-beli virtuális hálózat és egy másik hely (például a helyszíni) közötti titkosított forgalom küldésére szolgál az interneten keresztül. Az Azure VPN Gateway egy olyan Azure-erőforrás, amely egy, a Storage-fiók vagy más Azure-erőforrás melletti erőforráscsoporthoz is telepíthető. A VPN-átjárók két különböző típusú kapcsolatot tesznek elérhetővé:
    - [Pont – hely (P2S) VPN](../../vpn-gateway/point-to-site-about.md) Gateway-kapcsolatok, amelyek az Azure és az egyes ügyfelek közötti VPN-kapcsolatok. Ez a megoldás elsősorban olyan eszközök esetében hasznos, amelyek nem részei a szervezete helyszíni hálózatának, például a távmunkát, akik az Azure-fájlmegosztás otthonról, kávézóból vagy hotelből való csatlakoztatására szeretnének. Ha a P2S VPN-kapcsolatot Azure Files használatával szeretné használni, konfigurálnia kell egy P2S VPN-kapcsolatot minden olyan ügyfél számára, amelyhez csatlakozni szeretne. 
    - [Helyek közötti (S2S) VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), amely az Azure és a szervezet hálózata közötti VPN-kapcsolat. A S2S VPN-kapcsolat lehetővé teszi, hogy egy VPN-kapcsolatot egyszer, a szervezet hálózatán tárolt VPN-kiszolgálóhoz vagy eszközhöz konfigurálja, és nem minden olyan eszközt, amely az Azure-fájlmegosztás eléréséhez szükséges.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), amely lehetővé teszi egy meghatározott útvonal létrehozását az Azure és a helyszíni hálózat között, amely nem haladja meg az internetet. Mivel a ExpressRoute egy dedikált útvonalat biztosít a helyszíni adatközpont és az Azure között, a ExpressRoute hasznos lehet, ha a hálózati teljesítmény megfontolásra kerül. A ExpressRoute akkor is jó megoldás, ha a szervezet házirend-vagy szabályozási követelményei determinisztikus elérési utat igényelnek a felhőben lévő erőforrásokhoz.

## <a name="securing-access-from-on-premises"></a>A helyszíni hozzáférés biztonságossá tétele 
Az általános célú fájlmegosztás (például Office-dokumentumok, PDF-fájlok, CAD-dokumentumok stb. Azure Files) áttelepítésekor a felhasználóknak jellemzően továbbra is el kell érniük a fájljaikat a helyszíni eszközökről, például a munkaállomásokról, a laptopokról és a tablettákról. Az általános célú fájlmegosztás fő szempontja, hogy a helyszíni felhasználók hogyan érhetik el biztonságosan a fájlmegosztást az interneten vagy a WAN-on keresztül.

Az Azure-fájlmegosztás a helyszínen való elérésének legegyszerűbb módja a helyszíni hálózat megnyitása a 445-es portra, az SMB által használt portra, valamint a Azure Portal által megadott UNC elérési út csatlakoztatására. Ehhez nincs szükség speciális hálózatkezelésre. Sok ügyfél vonakodik az 445-es port megnyitásával az SMB 1,0-es elavult biztonsági útmutatás miatt, amelyet a Microsoft nem tekint az internet biztonságos protokolljának. A Azure Files nem implementálja az SMB 1,0-t. 

Az SMB 3,0 az internet biztonságos fájlmegosztás protokollok explicit követelményének megfelelően lett tervezve. Ezért az SMB 3.0 + használatakor a számítógép hálózatkezelés szempontjából a 445-es port megnyitása nem különbözik a 443-es porttól, a HTTPS-kapcsolatokhoz használt porttól. A 445-es port nem blokkolja az SMB 1,0-forgalom inbiztonságossá tételét, a Microsoft a következő lépéseket javasolja:

> [!Important]  
> Még ha úgy is dönt, hogy a 445-es portot a kimenő forgalom felé hagyja, a Microsoft továbbra is javasolja, hogy az SMB 1,0-et a környezetből távolítsa el.

1. Győződjön meg arról, hogy az SMB 1,0 el lett távolítva vagy le van tiltva a szervezet eszközein. A Windows és a Windows Server összes jelenleg támogatott verziója támogatja az SMB 1,0 eltávolítását vagy letiltását, és a Windows 10-es verziótól kezdődően a 1709-es verziójától kezdve az SMB 1,0 nincs telepítve a Windows rendszerben alapértelmezés szerint. Ha többet szeretne megtudni az SMB 1,0 letiltásáról, tekintse meg az operációs rendszerre vonatkozó oldalakat:
    - [A Windows/Windows Server biztonságossá tétele](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [A Linux biztonságossá tétele](storage-how-to-use-files-linux.md#securing-linux)
1. Győződjön meg arról, hogy a szervezeten belül egyetlen termék sem igényel SMB 1,0-t, és távolítsa el azokat. Egy SMB1- [adatközpontot](https://aka.ms/stillneedssmb1)tartunk fenn, amely tartalmazza az összes, a Microsoft számára ismert, az SMB 1,0-t megkövetelő, első és harmadik féltől származó terméket. 
1. Választható Egy külső gyártótól származó tűzfalat használhat a szervezete helyszíni hálózatán az SMB 1,0-forgalom megakadályozása érdekében.

Ha a szervezete az 445-as portot a házirend vagy a szabályozás letiltására kéri, az Azure VPN Gateway vagy a ExpressRoute az 443-es porton keresztüli forgalom bújtatására is használható. Ha többet szeretne megtudni ezekről a lépésekről, tekintse meg a lapokkal kapcsolatos konkrét lépéseket:
- [Helyek közötti (S2S) VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md)
- [Pont – hely (P2S) VPN konfigurálása Windows rendszeren a Azure Files-mel való használatra](storage-files-configure-p2s-vpn-windows.md)
- [Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Files-vel való használatra](storage-files-configure-p2s-vpn-linux.md)

Előfordulhat, hogy a szervezete számára további követelmény, hogy a helyszíni helyről kimenő forgalomnak a felhőben lévő erőforrásainak determinisztikus-elérési útját kell követnie. Ha igen, a ExpressRoute képes megfelelni ennek a követelménynek.

## <a name="securing-access-from-cloud-resources"></a>A Felhőbeli erőforrásokhoz való hozzáférés biztonságossá tétele
Általánosságban elmondható, hogy amikor a helyszíni alkalmazást felemelik és áthelyezik a felhőbe, az alkalmazás és az alkalmazás adatmozgatása egy időben történik. Ez azt jelenti, hogy a lift és a váltás áttelepítésének elsődleges szempontja, hogy az Azure-fájlmegosztás elérését az adott virtuális gépekhez vagy olyan Azure-szolgáltatásokhoz használja, amelyeknek hozzáférést kell kérniük a fájlmegosztás működéséhez. 

Előfordulhat, hogy a virtuális hálózatok segítségével szeretné korlátozni, hogy mely virtuális gépek vagy más Azure-erőforrások számára engedélyezett a hálózati kapcsolat (SMB-csatlakoztatások vagy REST API hívások az Azure-fájlmegosztás számára). Az Azure-fájlmegosztás VNet való elhelyezését mindig javasoljuk, ha engedélyezi a nem titkosított forgalmat a Storage-fiókhoz. Ellenkező esetben, függetlenül attól, hogy használja-e a virtuális hálózatok-t, az üzleti igényeknek és a szervezeti házirendnek megfelelően kell döntenie.

Az Azure-fájlmegosztás titkosításának elsődleges oka az, hogy támogassa a Windows Server 2008 R2, a Windows 7 vagy más régebbi operációs rendszert, amely az Azure-fájlmegosztást az SMB 2,1 (vagy az SMB 3,0 használatával, bizonyos Linux-disztribúciók titkosítása nélkül) éri el. Az SMB 3.0 + titkosítást támogató operációs rendszerek titkosítása nélkül nem javasoljuk az SMB 2,1 vagy az SMB 3,0 használatát.

## <a name="see-also"></a>Lásd még:
- [Az Azure Files áttekintése](storage-files-introduction.md)
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)