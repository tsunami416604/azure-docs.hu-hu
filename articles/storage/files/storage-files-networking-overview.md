---
title: Az Azure Files hálózati szempontjai | Microsoft dokumentumok
description: Az Azure Files hálózati lehetőségeinek áttekintése.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 383ad5e5063a0a207320a517c34f3b41cc57804a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067147"
---
# <a name="azure-files-networking-considerations"></a>Az Azure Files hálózati szempontjai 
Kétféleképpen csatlakozhat egy Azure-fájlmegosztáshoz:

- A megosztás elérése közvetlenül az SMB vagy FileREST protokollokon keresztül. Ez a hozzáférési minta elsősorban akkor alkalmazható, ha a lehető legtöbb helyszíni kiszolgálót kell kiiktatni.
- Az Azure-fájlmegosztás gyorsítótárának létrehozása egy helyszíni kiszolgálón az Azure File Sync segítségével, valamint a fájlmegosztás adatainak elérése a helyszíni kiszolgálóról a választott protokollal (SMB, NFS, FTPS stb.) a használati esethez. Ez a hozzáférési minta azért hasznos, mert egyesíti a helyszíni teljesítmény és a felhőalapú méretezési és kiszolgáló nélküli csatlakoztatható szolgáltatások, például az Azure Backup legjobb.

Ez a cikk arra összpontosít, hogyan konfigurálhatja a hálózatépítést, ha a használati eset közvetlenül az Azure-fájlmegosztás elérésére szólít meg, nem pedig az Azure File Sync használatával. Az Azure File Sync központi telepítésével kapcsolatos hálózati szempontokról az [Azure File Sync proxy és a tűzfal beállításainak konfigurálása](storage-sync-files-firewall-and-proxy.md)című témakörben olvashat bővebben.

Az Azure-fájlmegosztások hálózati konfigurációja az Azure storage-fiókon történik. A tárfiók egy felügyeleti konstrukció, amely egy megosztott tárolókészletet képvisel, amelyben több fájlmegosztást, valamint egyéb tárolási erőforrásokat, például blobtárolókat vagy várólistákat telepíthet. A tárfiókok több beállítást is elérhetővé teszik, amelyek segítségével biztosíthatja a hálózati hozzáférést a fájlmegosztásokhoz: hálózati végpontok, tárfiók tűzfalbeállításai és titkosítás az átvitel során. 

Javasoljuk, hogy olvassa el [az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md) című útmutató elolvasása előtt.

## <a name="accessing-your-azure-file-shares"></a>Az Azure-fájlmegosztások elérése
Ha egy tárfiókon belül telepít egy Azure-fájlmegosztást, a fájlmegosztás azonnal elérhető a tárfiók nyilvános végpontján keresztül. Ez azt jelenti, hogy a hitelesített kérelmek, például a felhasználó bejelentkezési identitása által engedélyezett kérelmek biztonságosan származhatnak az Azure-on belülről vagy kívülről. 

Számos ügyfélkörnyezetben az Azure-fájlmegosztás kezdeti csatlakoztatása a helyszíni munkaállomáson sikertelen lesz, még akkor is, ha az Azure-beli virtuális gépek csatlakoztatása sikeres lesz. Ennek az az oka, hogy számos szervezet és internetszolgáltató (ISP) blokkolja az SMB által a kommunikációhoz használt portot, a 445-ös portot. Ez a gyakorlat az SMB protokoll örökölt és elavult verzióira vonatkozó biztonsági útmutatásból származik. Bár az SMB 3.0 egy internet-biztos protokoll, az SMB régebbi verziói, különösen az SMB 1.0 nem. Az Azure-fájlmegosztások csak az SMB 3.0-s és a FileREST protokollon (amely egyben egy biztonságos internetes protokollon) keresztül érhetők el külsőleg.

Mivel az Azure-fájlmegosztás helyszíni elérésének legegyszerűbb módja a helyszíni hálózat megnyitása a 445-ös portra, a Microsoft a következő lépéseket javasolja az SMB 1.0 eltávolításához a környezetből:

1. Győződjön meg arról, hogy az SMB 1.0 eltávolításra vagy letiltásra kerül a szervezet eszközein. A Windows és a Windows Server összes jelenleg támogatott verziója támogatja az SMB 1.0 eltávolítását vagy letiltását, és a Windows 10 1709-es verziójától kezdve az SMB 1.0 alapértelmezés szerint nincs telepítve a Windows rendszerre. Ha többet szeretne megtudni az SMB 1.0 letiltásáról, tekintse meg az operációs rendszerre vonatkozó oldalainkat:
    - [A Windows/Windows Server védelme](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux biztonságossá tétele](storage-how-to-use-files-linux.md#securing-linux)
2. Győződjön meg arról, hogy a szervezeten belül egyetlen termék sem igényel SMB 1.0-s terméket, és távolítsa el azokat, amelyek igen. [SMB1 termékelszámolóházat](https://aka.ms/stillneedssmb1)tartunk fenn, amely tartalmazza a Microsoft által az SMB 1.0-ra vonatkozóan ismert összes első és harmadik féltől származó terméket. 
3. (Nem kötelező) Használjon külső tűzfalat a szervezet helyszíni hálózatával, hogy megakadályozza az SMB 1.0-s forgalom elhagyását a szervezeti határból.

Ha a szervezet megköveteli, hogy a 445-ös port házirendenként vagy rendeletenként le legyen tiltva, vagy a szervezetnek egy determinisztikus útvonalat kell követnie az Azure-ba irányuló forgalom, az Azure VPN Gateway vagy az ExpressRoute használatával bújtathatja a forgalmat az Azure-fájlmegosztásokhoz.

> [!Important]  
> Még akkor is, ha úgy dönt, hogy egy másik módszert használ az Azure-fájlmegosztások eléréséhez, a Microsoft továbbra is azt javasolja, hogy távolítsa el az SMB 1.0-t a környezetéből.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Forgalom bújtatása virtuális magánhálózaton vagy ExpressRoute-hálózaton keresztül
Amikor hálózati bújtatja a helyszíni hálózatot és az Azure-t, a helyszíni hálózatot egy vagy több azure-beli virtuális hálózattal létesíti. A [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)vagy virtuális hálózat hasonló a hagyományos hálózat, amely a helyszínen működne. Az Azure storage-fiókhoz vagy az Azure virtuális géphez hasonlóan a virtuális hálózat is egy erőforráscsoportban üzembe helyezett Azure-erőforrás. 

Az Azure Files a következő mechanizmusokat támogatja a helyszíni munkaállomások és kiszolgálók és az Azure közötti forgalom bújtatásához:

- [Azure VPN-átjáró:](../../vpn-gateway/vpn-gateway-about-vpngateways.md)A VPN-átjáró egy adott típusú virtuális hálózati átjáró, amely az Azure virtuális hálózat és egy másik hely (például a helyszíni) közötti titkosított forgalom küldésére szolgál az interneten keresztül. Az Azure VPN-átjáró egy Azure-erőforrás, amely egy erőforráscsoportban telepíthető egy tárfiók vagy más Azure-erőforrások mellett. A VPN-átjárók két különböző típusú kapcsolatot adnak ki:
    - [Pont-hely (P2S) VPN-átjárókapcsolatok,](../../vpn-gateway/point-to-site-about.md) amelyek az Azure és az egyes ügyfelek közötti VPN-kapcsolatok. Ez a megoldás elsősorban olyan eszközök esetében hasznos, amelyek nem részei a szervezet helyszíni hálózatának, például a távmunkát végzők számára, akik szeretnék csatlakoztatni az Azure-fájlmegosztást otthonról, egy kávézóból vagy szállodából útközben. P2S VPN-kapcsolat használatához az Azure Files, a P2S VPN-kapcsolat kell konfigurálni minden ügyfél, amely csatlakozni kíván. A P2S VPN-kapcsolat telepítésének egyszerűsítése érdekében olvassa el a [Point-to-Site (P2S) VPN konfigurálása Windows rendszeren az Azure Files-szal való használatra](storage-files-configure-p2s-vpn-windows.md) című témakört, valamint [egy pont-hely (P2S) VPN konfigurálása Linuxon az Azure Files-szal való használatra című témakört.](storage-files-configure-p2s-vpn-linux.md)
    - [Helyek közötti (S2S) VPN,](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)amelyek az Azure és a szervezet hálózata közötti VPN-kapcsolatok. Az S2S VPN-kapcsolat lehetővé teszi, hogy a VPN-kapcsolat konfigurálása egyszer, egy VPN-kiszolgáló vagy a szervezet hálózatán üzemeltetett eszköz, ahelyett, hogy minden olyan ügyféleszközön, amelynek szüksége van az Azure-fájlmegosztás eléréséhez. Az S2S VPN-kapcsolat telepítésének egyszerűsítése érdekében olvassa el a Helyek közötti [(S2S) VPN konfigurálása az Azure Files szolgáltatáshoz című témakört.](storage-files-configure-s2s-vpn.md)
- [ExpressRoute](../../expressroute/expressroute-introduction.md), amely lehetővé teszi, hogy hozzon létre egy meghatározott útvonalat az Azure és a helyszíni hálózat, amely nem halad át az interneten. Mivel az ExpressRoute dedikált elérési utat biztosít a helyszíni adatközpont és az Azure között, az ExpressRoute hasznos lehet, ha a hálózati teljesítmény megfontolástárgyát teszi. ExpressRoute is jó választás, ha a szervezet szabályzati vagy szabályozási követelmények hez determinisztikus elérési utat az erőforrások a felhőben.

Függetlenül attól, hogy melyik bújtatási módszert használja az Azure-fájlmegosztások eléréséhez, szüksége van egy mechanizmusra annak biztosítására, hogy a tárfiókba irányuló forgalom a hagyományos internetkapcsolat helyett az alagúton keresztül haladjon. Technikailag lehetséges a tárfiók nyilvános végpontjára való átirányítás, azonban ehhez az Azure storage-fürtök összes IP-címének teljes kódolására van szükség egy régióban, mivel a tárfiókok bármikor áthelyezhetők a tárolófürtök között. Ehhez folyamatosan frissíteni kell az IP-címleképezéseket is, mivel folyamatosan új fürtöket ad hozzá.

Ahelyett, hogy a tárfiókok IP-címét a VPN-útválasztási szabályokba kódolná, azt javasoljuk, hogy használjon privát végpontokat, amelyek egy Azure virtuális hálózat címteréből származó IP-címet adnak a tárfióknak. Mivel az Azure-ba egy bújtatás létrehozása létesít a helyszíni hálózat és egy vagy több virtuális hálózat közötti társviszony-létesítést, ez lehetővé teszi a megfelelő útválasztást tartós módon.

### <a name="private-endpoints"></a>Privát végpontok
A tárfiók alapértelmezett nyilvános végpontja mellett az Azure Files egy vagy több privát végpontot is biztosít. A privát végpont egy végpont, amely csak egy Azure virtuális hálózaton belül érhető el. Amikor privát végpontot hoz létre a tárfiókhoz, a tárfiók egy privát IP-címet kap a virtuális hálózat címteréből, hasonlóan ahhoz, ahogyan egy helyszíni fájlkiszolgáló vagy NAS-eszköz kap egy IP-címet a dedikált címen belül a helyszíni hálózat területén. 

Egy adott privát végpont egy adott Azure virtuális hálózati alhálózathoz van társítva. A tárfiók több virtuális hálózatban is rendelkezhet privát végpontokkal.

A privát végpontok használata az Azure Files segítségével a következőket teszi lehetővé:
- Biztonságosan csatlakozhat az Azure-fájlmegosztásokhoz helyszíni hálózatokból VPN- vagy ExpressRoute-kapcsolaton keresztül, privát társviszony-létesítéssel.
- Az Azure-fájlmegosztások biztonságossá tétele a tárfiók tűzfalának konfigurálásával blokkolja a nyilvános végponton lévő összes kapcsolatot. Alapértelmezés szerint a privát végpont létrehozása nem blokkolja a nyilvános végponthoz való kapcsolatokat.
- Növelje a virtuális hálózat biztonságát azáltal, hogy lehetővé teszi az adatok kiszivárgásának letiltását a virtuális hálózatról (és a társviszony-létesítési határokról).

Privát végpont létrehozásáról az [Azure Files privát végpontjainak konfigurálása](storage-files-networking-endpoints.md)című témakörben található.

### <a name="private-endpoints-and-dns"></a>Privát végpontok és DNS
Privát végpont létrehozásakor alapértelmezés szerint létrehozunk egy (vagy egy meglévő) magánDNS-zónát is, amely megfelel az `privatelink` altartománynak. Szigorúan véve, egy privát DNS-zóna létrehozása nem szükséges, hogy egy privát végpont ot a tárfiók, de ez általában ajánlott, és kifejezetten szükséges, ha az Azure fájlmegosztást egy Active Directory felhasználói egyszerű vagy elérése a FileREST API-ból.

> [!Note]  
> Ez a cikk a tárfiók DNS-utótagja `core.windows.net`az Azure nyilvános régiók, . Ez a kommentár az Azure Sovereign felhőkre is vonatkozik, például az Azure US Government felhőre és az Azure China felhőre – csak helyettesítse a megfelelő utótagokat a környezetéhez. 

A privát DNS-zónában létrehozunk `storageaccount.privatelink.file.core.windows.net` egy A rekordot és egy CNAME rekordot a `storageaccount.file.core.windows.net`tárfiók normál nevéhez, amely a mintát követi. Mivel az Azure-beli privát DNS-zóna a privát végpontot tartalmazó virtuális hálózathoz csatlakozik, megfigyelheti a DNS-konfigurációt, amikor a `Resolve-DnsName` PowerShell parancsmát hívja meg egy Azure virtuális gépben (felváltva `nslookup` Windows és Linux rendszerben):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Ebben a példában `storageaccount.file.core.windows.net` a tárfiók feloldódik a privát végpont privát `192.168.0.4`IP-címére, amely történetesen a.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Ha ugyanazt a parancsot a helyszíni, látni fogja, hogy ugyanazt a tárfiók nevét feloldja a nyilvános IP-cím a tárfiók helyett; `storageaccount.file.core.windows.net` a cname `storageaccount.privatelink.file.core.windows.net`rekord, amely viszont a tárfiókot tároló Azure storage-fürt CNAME rekordja:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Ez tükrözi azt a tényt, hogy a tárfiók a nyilvános végpontot és egy vagy több privát végpontot is elérhetővé teheti. Annak érdekében, hogy a tárfiók neve feloldódjon a privát végpont privát IP-címére, módosítania kell a konfigurációt a helyszíni DNS-kiszolgálókon. Ez többféleképpen valósítható meg:

- Az ügyfelek gazdagépfájljának `storageaccount.file.core.windows.net` módosítása a kívánt privát végpont privát IP-címének feloldásához. Ez erősen ajánlott az éles környezetekben, mivel ezeket a módosításokat minden ügyfél, amely szeretné csatlakoztatni az Azure-fájlmegosztások és a tárfiók vagy a magán-végpont módosításai nem lesz automatikusan kezelni.
- A rekord létrehozása `storageaccount.file.core.windows.net` a helyszíni DNS-kiszolgálókon. Ennek az az előnye, hogy a helyszíni környezetben lévő ügyfelek automatikusan fel tudják oldani a tárfiókot anélkül, hogy minden ügyfelet konfigurálniuk kellene, de ez a megoldás hasonlóképpen törékeny a hosts fájl módosításához, mivel a módosítások nem Tükrözi. Bár ez a megoldás törékeny, lehet, hogy a legjobb választás egyes környezetekben.
- Továbbítsa a `core.windows.net` zónát a helyszíni DNS-kiszolgálókról az Azure privát DNS-zónájába. Az Azure-beli privát DNS-szolgáltató egy`168.63.129.16`speciális IP-címen ( ) keresztül érhető el, amely csak az Azure privát DNS-zónához kapcsolódó virtuális hálózatokon belül érhető el. A korlátozás megkerülése érdekében további DNS-kiszolgálókat `core.windows.net` futtathat a virtuális hálózaton belül, amelyek továbbítanak az Azure privát DNS-zónájába. A beállítás egyszerűsítése érdekében olyan PowerShell-parancsmagokat biztosítottunk, amelyek automatikusan telepítik a DNS-kiszolgálókat az Azure virtuális hálózatában, és igény szerint konfigurálják őket. A DNS-továbbítás beállításáról a [DNS konfigurálása az Azure Files szolgáltatással](storage-files-networking-dns.md)című témakörben olvashat.

## <a name="storage-account-firewall-settings"></a>A tárfiók tűzfalbeállításai
A tűzfal olyan hálózati házirend, amely szabályozza, hogy mely kérelmek férhetnek hozzá a tárfiók nyilvános végpontjához. A tárfiók tűzfala használatával korlátozhatja a hozzáférést a tárfiók nyilvános végpontjához bizonyos IP-címekre vagy tartományokra vagy egy virtuális hálózatra. A tárfiók legtöbb tűzfalházirendje általában korlátozza a hálózati hozzáférést egy vagy több virtuális hálózathoz. 

A tárfiókhoz való hozzáférés virtuális hálózathoz való korlátozásának két megközelítése van:
- Hozzon létre egy vagy több privát végpontot a tárfiókhoz, és korlátozza a nyilvános végponthoz való összes hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokból származó forgalom férhet hozzá az Azure-fájlmegosztások a tárfiókon belül.
- Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra. Ez a *szolgáltatásvégpontok*nevű virtuális hálózat képességének használatával működik. Ha korlátozza a forgalmat egy szolgáltatásvégponton keresztül egy tárfiókra, továbbra is a nyilvános IP-címen keresztül éri el a tárfiókot.

A tárfiók tűzfalának konfigurálásáról az [Azure storage tűzfalainak és virtuális hálózatainak konfigurálása](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)című témakörben olvashat bővebben.

## <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Alapértelmezés szerint az összes Azure storage-fiókok titkosítás i átvitel e titkosítás engedélyezve van. Ez azt jelenti, hogy ha egy fájlmegosztást csatlakoztat SMB-n keresztül, vagy a FileREST protokollon keresztül (például az Azure Portalon, a PowerShell/CLI-n vagy az Azure SDK-n keresztül) keresztül fér hozzá, az Azure Files csak akkor engedélyezi a kapcsolatot, ha smb 3.0+ titkosítással vagy HTTPS-lel készült. Azok az ügyfelek, amelyek nem támogatják az SMB 3.0-s vagy az SMB 3.0-s titkosítást támogató, de az SMB-titkosítást nem támogató ügyfelek nem tudják csatlakoztatni az Azure fájlmegosztást, ha az átvitel alatt titkosítás engedélyezve van. Arról, hogy mely operációs rendszerek támogatják az SMB 3.0 titkosítását, tekintse meg a [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)és Linux részletes [dokumentációját.](storage-how-to-use-files-linux.md) A PowerShell, a CLI és az SDK-k összes jelenlegi verziója támogatja a HTTPS protokollt.  

Letilthatja a titkosítást az átvitel során egy Azure-tárfiók. Ha a titkosítás le van tiltva, az Azure Files is lehetővé teszi az SMB 2.1, SMB 3.0 titkosítás nélkül, és a nem titkosított FileREST API-hívások HTTP-n keresztül. Az átvitel során a titkosítás letiltásának elsődleges oka egy régebbi operációs rendszeren, például Windows Server 2008 R2 vagy régebbi Linux-disztribúción futtatandó örökölt alkalmazás támogatása. Az Azure Files csak az Azure 2.1-es kapcsolatokat engedélyezi ugyanabban az Azure-régióban, mint az Azure fájlmegosztás; az Azure-fájlmegosztás Azure-régióján kívüli SMB 2.1-es ügyfél, például a helyszíni vagy egy másik Azure-régióban, nem fogja tudni elérni a fájlmegosztást.

Az átvitel közbeni titkosításról további információt az [Azure storage-beli biztonságos átvitel megkövetelése című témakörben talál.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="see-also"></a>Lásd még
- [Az Azure Files áttekintése](storage-files-introduction.md)
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)