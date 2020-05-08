---
title: Azure Files központi telepítésének tervezése | Microsoft Docs
description: Megtudhatja, mit érdemes figyelembe venni Azure Files központi telepítés tervezésekor.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5356ff0ac165deefc5053cf4faa40c1159e98678
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856895"
---
# <a name="planning-for-an-azure-files-deployment"></a>Az Azure Files üzembe helyezésének megtervezése
[Azure Files](storage-files-introduction.md) kétféleképpen helyezhető üzembe: a kiszolgáló nélküli Azure-fájlmegosztás közvetlen csatlakoztatásával vagy az Azure-fájlmegosztás helyszíni gyorsítótárazásával Azure file Sync használatával. Az üzembe helyezési lehetőségek közül válassza ki azokat a beállításokat, amelyeket figyelembe kell vennie az üzemelő példány tervezésekor. 

- **Azure-fájlmegosztás közvetlen csatlakoztatása**: mivel Azure Files az SMB-hozzáférést biztosít, a Windows, MacOS és Linux rendszeren elérhető szabványos SMB-ügyféllel csatlakoztathatja az Azure-fájlmegosztást a helyszínen vagy a felhőben. Mivel az Azure-fájlmegosztás kiszolgáló nélküli, az éles környezetekben való üzembe helyezéshez nem szükséges fájlkiszolgáló vagy NAS-eszköz kezelése. Ez azt jelenti, hogy nem kell szoftverfrissítéseket alkalmaznia vagy fizikai lemezeket cserélnie. 

- **A helyszíni Azure-fájlmegosztás gyorsítótárazása a Azure file Sync**használatával: a Azure file Sync lehetővé teszi a szervezete fájlmegosztás megszervezését Azure Filesban, miközben megtartja a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Azure File Sync átalakítja a helyszíni (vagy Felhőbeli) Windows Servert az Azure-fájlmegosztás gyors gyorsítótárba. 

Ez a cikk elsősorban a helyszíni vagy a Felhőbeli ügyfelek által közvetlenül csatlakoztatott Azure-fájlmegosztás üzembe helyezési szempontjait tárgyalja. Azure File Sync központi telepítésének megtervezéséhez tekintse meg a [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md)című témakört.

## <a name="management-concepts"></a>Felügyeleti fogalmak
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Az Azure-fájlmegosztás Storage-fiókokban való telepítésekor a következőket javasoljuk:

- Az Azure file-megosztásokat csak a Storage-fiókokba helyezheti üzembe más Azure-fájlmegosztás esetén. Bár a GPv2 Storage-fiókok lehetővé teszik vegyes célú tárolási fiókok használatát, mivel a tárolási erőforrások, például az Azure-fájlmegosztás és a blob-tárolók megosztják a tárolási fiók korlátait, az erőforrások összekeverése pedig nehezebbé teszi a teljesítménnyel kapcsolatos problémák későbbi hibaelhárítását. 

- Ügyeljen arra, hogy a Storage-fiók IOPS korlátozásait az Azure-fájlmegosztás telepítésekor. Ideális esetben a Storage-fiókokkal rendelkező 1:1-es fájlmegosztás leképezhető, azonban a különböző korlátok és korlátozások miatt a szervezettől és az Azure-tól is nem mindig lehetséges. Ha egyetlen Storage-fiókban nem lehet egyetlen fájlmegosztást telepíteni, gondolja át, hogy mely megosztások lesznek aktívak, és hogy mely megosztások kevésbé lesznek aktívak, hogy a legforróbb fájlmegosztás ne legyen ugyanazon a Storage-fiókban.

- Csak a GPv2-és FileStorage-fiókok telepítése, valamint a GPv1 és a klasszikus tárolási fiókok frissítése a környezetben való kereséskor. 

## <a name="identity"></a>Identitás
Egy Azure-fájlmegosztás eléréséhez a fájlmegosztás felhasználójának hitelesítenie kell, és engedéllyel kell rendelkeznie a megosztás eléréséhez. Ez a fájlmegosztást elérő felhasználó identitása alapján történik. A Azure Files három fő identitás-szolgáltatóval integrálódik:
- Helyszíni **Active Directory tartományi szolgáltatások (AD DS vagy helyszíni AD DS)** (előzetes verzió): az Azure Storage-fiókok tartományhoz az ügyfél, a Active Directory tartományi szolgáltatások, a Windows Server fájlkiszolgáló vagy a NAS-eszközhöz hasonlóan lehet csatlakozni. Üzembe helyezhet egy tartományvezérlőt a helyszínen, egy Azure-beli virtuális gépen, vagy akár egy másik felhőalapú szolgáltató virtuális gépén is. Azure Files a tartományvezérlő üzemeltetéséhez szükséges agnosztikus. Miután a Storage-fiók tartományhoz csatlakozik, a végfelhasználó csatlakoztathat egy fájlmegosztást a számítógéphez bejelentkezett felhasználói fiókhoz. Az AD-alapú hitelesítés a Kerberos hitelesítési protokollt használja.
- **Azure Active Directory Domain Services (azure AD DS)**: az Azure AD DS egy Microsoft által felügyelt tartományvezérlőt biztosít, amely Azure-erőforrásokhoz használható. A Storage-fiók Azure AD DShoz való csatlakoztatása hasonló előnyökkel jár a tartományhoz való csatlakozáshoz az ügyfél tulajdonában lévő Active Directoryhoz. Ez az üzembe helyezési lehetőség az olyan alkalmazások esetében hasznos, amelyek AD-alapú engedélyeket igényelnek. Mivel az Azure AD DS AD-alapú hitelesítést biztosít, ez a beállítás a Kerberos hitelesítési protokollt is használja.
- **Azure Storage-fiók kulcsa**: az Azure-fájlmegosztás egy Azure Storage-fiók kulcsával is csatlakoztatható. Fájlmegosztás ily módon történő csatlakoztatásához a rendszer a Storage-fiók nevét használja felhasználónévként, és a Storage-fiók kulcsa jelszóként van használatban. Ha a Storage-fiók kulcsát használja az Azure-fájlmegosztás csatlakoztatására, akkor a felügyeleti művelet hatékony, mivel a csatlakoztatott fájlmegosztás teljes jogosultsággal rendelkezik a megosztás összes fájljához és mappájához, még akkor is, ha ACL-ekkel rendelkezik. Ha a Storage-fiók kulcsát használja az SMB protokollon keresztül történő csatlakoztatásra, a rendszer az NTLMv2 hitelesítési protokollt használja.

A helyszíni fájlkiszolgálók áttelepítésére vagy a Azure Files a Windows-fájlkiszolgálók vagy a NAS-készülékek működéséhez szükséges új fájlmegosztás létrehozásához az ajánlott lehetőség a Storage-fiókhoz való csatlakozás az **ügyfél tulajdonában lévő Active Directory** . Ha többet szeretne megtudni a Storage-fiók ügyfél által birtokolt Active Directoryhoz való csatlakoztatásáról, tekintse meg a [Azure Files Active Directory áttekintése](storage-files-active-directory-overview.md)című témakört.

Ha a Storage-fiók kulcsát szeretné használni az Azure-fájlmegosztás eléréséhez, javasoljuk, hogy használja a szolgáltatás-végpontokat a [hálózatkezelés](#networking) szakaszban leírtak szerint.

## <a name="networking"></a>Hálózat
Az Azure-fájlmegosztás bárhonnan elérhető a Storage-fiók nyilvános végpontján keresztül. Ez azt jelenti, hogy a hitelesített kérések, például a felhasználó bejelentkezési identitása által engedélyezett kérések, biztonságosan származhatnak az Azure-on belül vagy kívül is. Számos felhasználói környezetben a helyszíni munkaállomás Azure-fájlmegosztás kezdeti csatlakoztatása sikertelen lesz, noha az Azure-beli virtuális gépekről való csatlakoztatások sikeresek lesznek. Ennek az az oka, hogy számos szervezet és internetszolgáltató (ISP) blokkolja az SMB által a kommunikációhoz használt portot, a 445-es portot. A [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) összefoglalja, hogy mely internetszolgáltatók engedélyezik vagy tiltják a 445-ös porton keresztüli hozzáférést.

Az Azure-fájlmegosztás elérésének feloldásához két fő lehetőség közül választhat:

- A szervezet helyszíni hálózatának 445-as portjának feloldása. Az Azure-fájlmegosztás csak az internetes biztonságos protokollok, például az SMB 3,0 és a kiszolgált API használatával lehet külsőleg hozzáférni a nyilvános végponton keresztül. Ez a legegyszerűbb módszer az Azure-fájlmegosztás helyszíni elérésére, mivel nem igényel speciális hálózatkezelési konfigurációt a szervezet kimenő portokra vonatkozó szabályainak módosítása után azonban javasoljuk, hogy távolítsa el az SMB protokoll örökölt és elavult verzióit, azaz az SMB 1,0-et. Ennek megismeréséhez tekintse meg a [Windows/Windows Server biztonságossá tétele](storage-how-to-use-files-windows.md#securing-windowswindows-server) és a [Linux biztonságossá tétele](storage-how-to-use-files-linux.md#securing-linux)című témakört.

- Azure-fájlmegosztás elérése ExpressRoute vagy VPN-kapcsolaton keresztül. Amikor hálózati alagúton keresztül éri el az Azure-fájlmegosztást, csatlakoztathatja az Azure-fájlmegosztást, például egy helyszíni fájlmegosztást, mivel az SMB-forgalom nem haladja meg a szervezeti határt.   

Bár a technikai szempontból jóval egyszerűbb az Azure-fájlmegosztás nyilvános végponton keresztüli csatlakoztatása, a legtöbb ügyfelünk úgy dönt, hogy az Azure-fájlmegosztást egy ExpressRoute vagy VPN-kapcsolaton keresztül csatlakoztatja. Ehhez a következőt kell konfigurálnia a környezetéhez:  

- **Hálózati bújtatás ExpressRoute, helyek közötti vagy pont – hely típusú VPN használatával**: a virtuális hálózatba való bújtatás lehetővé teszi az Azure-fájlmegosztás helyszíni elérését, még akkor is, ha az 445-es port le van tiltva.
- **Privát végpontok**: a magánhálózati végpontok dedikált IP-címet biztosítanak a Storage-fióknak a virtuális hálózat címterület területén. Ez lehetővé teszi a hálózati bújtatást anélkül, hogy az Azure Storage-fürtök által birtokolt összes IP-címtartományt meg kellene nyitni a helyszíni hálózatokat. 
- **DNS-továbbítás**: konfigurálja a helyszíni DNS-t úgy, hogy feloldja a Storage-fiók ( `storageaccount.file.core.windows.net` azaz a nyilvános felhő régiói) nevét a privát végpontok IP-címére való feloldáshoz.

Az Azure-fájlmegosztás üzembe helyezéséhez kapcsolódó hálózatkezelés megtervezéséhez tekintse meg [Azure Files hálózatkezelési megfontolásokat](storage-files-networking-overview.md).

## <a name="encryption"></a>Titkosítás
A Azure Files két különböző titkosítási típust támogat: az átvitel közbeni titkosítást, amely az Azure-fájlmegosztás csatlakoztatása/elérése és a nyugalmi állapotban lévő titkosítás használatakor használt titkosításhoz kapcsolódik, amely az adatok lemezen történő tárolásának módjára vonatkozik. 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Alapértelmezés szerint az összes Azure Storage-fióknál engedélyezve van az átvitel titkosítása. Ez azt jelenti, hogy amikor az SMB-n keresztül csatlakoztat egy fájlmegosztást, vagy a kiosztott protokollon keresztül éri el (például a Azure Portal, a PowerShell/CLI vagy az Azure SDK-k használatával), Azure Files csak akkor engedélyezi a kapcsolatot, ha az SMB 3.0 + titkosítással vagy HTTPS-vel van ellátva. Azok az ügyfelek, amelyek nem támogatják az SMB 3,0-et vagy az SMB 3,0-et támogató ügyfeleket, de az SMB-titkosítást nem, nem fogják tudni csatlakoztatni az Azure-fájlmegosztást, ha engedélyezve van az átvitel titkosítása. Ha további információt szeretne arról, hogy mely operációs rendszerek támogatják az SMB 3,0 titkosítást, tekintse meg a [Windows](storage-how-to-use-files-windows.md), a [MacOS](storage-how-to-use-files-mac.md)és a [Linux](storage-how-to-use-files-linux.md)részletes dokumentációját. A PowerShell, CLI és SDK összes jelenlegi verziója támogatja a HTTPS-t.  

Egy Azure Storage-fiók esetében letilthatja a titkosítást az átvitel során. Ha a titkosítás le van tiltva, a Azure Files az SMB 2,1, a titkosítás nélküli SMB 3,0 és a titkosítatlan, nem titkosított API-hívások HTTP-n keresztüli használatát is lehetővé teszi. Az átvitel közbeni titkosítás letiltásának elsődleges oka az olyan örökölt alkalmazások támogatása, amelyeknek régebbi operációs rendszeren kell futniuk, például Windows Server 2008 R2 vagy régebbi Linux-disztribúció. Azure Files csak az Azure-fájlmegosztás azonos Azure-régiójában lévő SMB 2,1-kapcsolatokat engedélyezi. Az Azure-fájlmegosztás (például a helyszíni vagy egy másik Azure-régió) Azure-régióján kívüli SMB 2,1-ügyfél nem fog tudni hozzáférni a fájlmegosztás eléréséhez.

Javasoljuk, hogy engedélyezze a továbbítást a forgalomban lévő adatátvitel titkosításának biztosításához.

További információ az átvitel közbeni titkosításról: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Tárolási rétegek
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Általánosságban elmondható, hogy Azure Files szolgáltatások és a más szolgáltatásokkal való együttműködés azonos a prémium fájlmegosztás és a szabványos fájlmegosztás között, azonban néhány fontos különbség:
- **Számlázási modell**
    - A prémium szintű fájlmegosztás számlázása egy kiépített számlázási modellel történik, ami azt jelenti, hogy a kiépített tárterület mennyiségét kell fizetnie, és nem a ténylegesen kért tárterületet. 
    - A standard fájlmegosztás számlázása az utólagos elszámolású modell használatával történik, amely a tárterület alapdíjait tartalmazza a ténylegesen felhasznált tárterülethez, majd a megosztás használati módjától függően további tranzakciós költségeket is tartalmaz. A standard fájlmegosztás esetén a számla akkor is növekedni fog, ha az Azure-fájlmegosztás használatát (olvasás/írás/csatlakoztatás) is növeli.
- **Redundancia-beállítások**
    - A prémium fájlmegosztás csak a helyileg redundáns (LRS) és a Zone redundáns (ZRS) tárolók esetében érhető el.
    - A standard fájlmegosztás a helyileg redundáns, a zónák redundáns, a Geo-redundáns (GRS) és a Geo-zóna redundáns (GZRS) tárolók számára érhető el.
- **Fájlmegosztás maximális mérete**
    - A prémium fájlmegosztást akár 100 TiB-ra is kiépítheti további munka nélkül.
    - Alapértelmezés szerint a standard fájlmegosztás legfeljebb 5 TiB-ra terjedhet ki, bár a megosztási korlát a 100 TiB-ra is növelhető, ha a *nagyméretű fájlmegosztás* Storage-fiók funkciójának jelzőjét választotta. A standard fájlmegosztás csak a 100 TiB-ra terjedhet a helyileg redundáns vagy zónában lévő redundáns Storage-fiókok esetében. További információ a fájlmegosztás méretének növeléséről: [nagyméretű fájlmegosztás engedélyezése és létrehozása](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share).
- **Régiónkénti rendelkezésre állás**
    - A prémium fájlmegosztás minden régióban nem érhető el, és a zóna redundáns támogatása a régiók kisebb részhalmazában érhető el. Annak megállapításához, hogy a prémium fájlmegosztás jelenleg elérhető-e az Ön régiójában, tekintse meg az Azure-ban [elérhető termékek területét](https://azure.microsoft.com/global-infrastructure/services/?products=storage) . Ha szeretné megtudni, hogy mely régiók támogatják a ZRS, tekintse meg az [Azure rendelkezésre állási zónák támogatása régiónként](../../availability-zones/az-region.md)című témakört. Kérjük, töltse ki ezt a [kérdőívet](https://aka.ms/pfsfeedback)az új régiók és prémium szintű funkciók rangsorolásához.
    - A standard fájlmegosztás minden Azure-régióban elérhető.
- Az Azure Kubernetes Service (ak) prémium szintű fájlmegosztás használatát támogatja a 1,13-es és újabb verziókban.

Ha egy fájlmegosztás prémium vagy standard fájlmegosztásként lett létrehozva, akkor nem alakíthatja át automatikusan a másik szintre. Ha a másik szintjére szeretne váltani, új fájlmegosztást kell létrehoznia az adott szinten, és manuálisan át kell másolnia az eredeti megosztás adatait az újonnan létrehozott megosztásra. Azt javasoljuk, `robocopy` hogy a Windowshoz vagy `rsync` MacOS és Linux rendszerhez használja a másolást.

### <a name="understanding-provisioning-for-premium-file-shares"></a>A prémium fájlmegosztás kiépítés ismertetése
A prémium fájlmegosztást rögzített GiB/IOPS/átviteli sebesség alapján kell kiépíteni. Minden egyes GiB-kiosztás esetén a megosztás egy IOPS és 0,1 MiB/s átviteli sebességgel fog kiadni, a maximálisan megengedett határértékek száma szerint. A minimálisan engedélyezett kiépítés a 100 GiB és a min IOPS/átviteli sebesség.

A legjobb lehetőség az, hogy minden megosztás legfeljebb három IOPS-t tud kiosztani a kiépített tárterület 60 percen belül, a megosztás méretétől függően. Az új megosztások a kiosztott kapacitás alapján kezdődnek a teljes burst Kredittel.

A megosztásokat 1 GiB-onként kell kiépíteni. A minimális méret 100 GiB, a következő méret 101 GiB és így tovább.

> [!TIP]
> Alapterv IOPS = 1 * kiépített GiB. (Max. 100 000 IOPS).
>
> Burst határérték = 3 * alapterv IOPS. (Max. 100 000 IOPS).
>
> kimenő forgalom aránya = 60 MiB/s + 0,06 * kiépített GiB
>
> bejövő forgalom sebessége = 40 MiB/s + 0,04 * kiépített GiB

A kiosztott megosztási méretet a megosztási kvóta határozza meg. A megosztási kvóta bármikor megnövelhető, de csak a legutóbbi növekedés óta 24 óra elteltével csökkenthető. Ha a várakozás után 24 órán keresztül nem növekszik a kvóta, a megosztási kvótát tetszőleges számú időpontra csökkentheti, amíg újra nem növelné. A IOPS/átviteli sebesség változásai a méret megváltozása után néhány percen belül érvénybe lépnek.

Lehetséges, hogy csökkentse a kiosztott megosztás méretét a használt GiB alatt. Ha ezt teszi, nem veszíti el az adatvesztést, de továbbra is a felhasznált méret után kell fizetnie, és meg kell kapnia a kiépített megosztás teljesítményét (alapkonfiguráció IOPS, átviteli sebességét és burst IOPS), nem a felhasznált méretet.

Az alábbi táblázat néhány példát mutat be a kiosztott megosztási méretekre:

|Kapacitás (GiB) | Alapterv IOPS | Burst IOPS | Kimenő forgalom (MiB/s) | Bejövő forgalom (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Akár 300     | 66   | 44   |
|500         | 500     | Akár 1 500   | 90   | 60   |
|1 024       | 1 024   | Akár 3 072   | 122   | 81   |
|5 120       | 5 120   | Akár 15 360  | 368   | 245   |
|10 240      | 10 240  | Akár 30 720  | 675 | 450   |
|33 792      | 33 792  | Akár 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Akár 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Akár 100 000 | 6 204 | 4 136   |

> [!NOTE]
> A fájlmegosztás teljesítményére a számítógép hálózati korlátai, a rendelkezésre álló hálózati sávszélesség, az IO-méretek, a párhuzamosságok számos más tényező vonatkozik. Például a 8 KiB írási/olvasási IO-méretekkel rendelkező belső tesztelésen alapuló, egyetlen Windowsos virtuális gép, *Standard F16s_v2*, a prémium szintű fájlmegosztás SMB-hez való csatlakoztatása 20000 olvasási IOPS és 15 000 FORDULAT írási IOPS. Az 512 MiB-írási/írási IO-méretekkel ugyanez a virtuális gép elérheti a 1,1 GiB/s kimenő forgalmat és a 370 MiB/s adatátviteli sebességet. A maximális teljesítmény elérése érdekében a terhelést több virtuális gép között kell elosztani. A gyakori teljesítménnyel kapcsolatos problémák és a megkerülő megoldások [hibaelhárítási útmutatójában](storage-troubleshooting-files-performance.md) olvashat.

#### <a name="bursting"></a>Tele
A prémium fájlmegosztás a IOPS akár három tényezővel is feltörte. A bursás automatizált, és kreditrendszer alapján működik. Az adatbontás a legjobb megoldási szinten működik, és a burst korlát nem garantálható, és a fájlmegosztás a korlátra is *feltört.*

A kreditek felhalmozódnak egy burst gyűjtőben, amikor a fájlmegosztás forgalma az alapszintű IOPS alatt van. Egy 100 GiB-megosztás például 100 alapterv-IOPS rendelkezik. Ha a megosztás tényleges forgalma 40 IOPS volt egy adott 1 másodperces intervallumhoz, akkor a 60 fel nem használt IOPS jóváírásra kerül egy burst gyűjtőn. Ezeket a krediteket később akkor fogjuk használni, amikor a műveletek túllépik az alapkonfiguráció IOPs.

> [!TIP]
> A burst vödör mérete = alapterv IOPS * 2 * 3600.

Ha egy megosztás túllépi az alapkonfiguráció IOPS, és a kreditek egy burst gyűjtőben vannak, akkor a rendszer kitört. A megosztások továbbra is feltörtek, amíg a kreditek megmaradnak, de az 50 TiB-nál kisebb megosztások csak a burst-korláton belül maradnak. A 50 TiB-nál nagyobb megosztások műszakilag meghaladják ezt az óránkénti korlátot, akár két óráig is, de ez a felhalmozott burst-kreditek számától függ. Az alapkonfiguráció IOPS túli minden egyes IO egy kreditet használ, és ha az összes kreditet felhasználja, a megosztás visszatér az alapkonfiguráció IOPS.

A megosztási kreditek három állapottal rendelkeznek:

- Ha a fájlmegosztás az alapkonfigurációnál kisebb IOPS használja.
- Csökkenő, ha a fájlmegosztás kitört.
- Hátralévő állandó, ha nincsenek kreditek vagy alapkonfiguráció IOPS használatban.

Az új fájlmegosztás a teljes számú Kredittel kezdődik a burst gyűjtőben. A burst kreditek nem lesznek felhalmozva, ha a megosztás IOPS az alapszintű IOPS alá esik a kiszolgáló általi szabályozás miatt.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>A standard fájlmegosztás engedélyezése akár 100 TiB-re is terjedhet
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Korlátozások
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundancia
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrálás
Sok esetben nem fog létrehozni nettó új fájlmegosztást a szervezet számára, hanem inkább egy meglévő fájlmegosztást telepít át egy helyszíni fájlkiszolgálón vagy NAS-eszközről a Azure Filesra. A Microsoft és a harmadik felek egyaránt számos eszközt biztosítanak a fájlmegosztás áttelepítésére, de nagyjából két kategóriába oszthatók:

- **A fájlrendszer attribútumait, például ACL-eket és időbélyegeket karbantartó eszközök**:
    - **[Azure file Sync](storage-sync-files-planning.md)**: a Azure file Sync használható az Azure-fájlmegosztásba való betöltéshez, még akkor is, ha a kívánt végponti telepítés nem tart fenn helyszíni jelenlétet. Azure File Sync a meglévő Windows Server 2012 R2, a Windows Server 2016 és a Windows Server 2019 rendszerű központi telepítések esetén telepíthető. A Azure File Sync betöltési mechanizmusként való használatának előnye, hogy a végfelhasználók továbbra is használhatják a meglévő fájlmegosztást. Az Azure-fájlmegosztás kivágása akkor fordulhat elő, ha az összes adat feltöltése befejeződött a háttérben.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: a Robocopy egy jól ismert másolási eszköz, amely a Windows és a Windows Server rendszerű kiszolgálókon található. A Robocopy felhasználható az adatok Azure Filesba történő átvitelére a fájlmegosztás helyi csatlakoztatásával, majd a csatlakoztatott hely célként való használatával a Robocopy parancsban.

- A **fájlrendszer attribútumait nem fenntartó eszközök**:
    - **Data Box**: a Data Box offline adatátviteli mechanizmust biztosít az adatok Azure-ba való fizikai szállításához. Ez a módszer az átviteli sebesség növelésére és a sávszélesség megtakarítására szolgál, de jelenleg nem támogatja a fájlrendszer-attribútumokat, például az időbélyegeket és az ACL-eket.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: a AzCopy egy parancssori segédprogram, amely az adatok átmásolását Azure Files, valamint az Azure Blob Storage-t használja az optimális teljesítményű egyszerű parancsok használatával.

## <a name="next-steps"></a>További lépések
* [Azure File Sync központi telepítésének tervezése](storage-sync-files-planning.md)
* [Azure Files üzembe helyezése](storage-files-deployment-guide.md)
* [Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
