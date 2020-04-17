---
title: Az Azure Files telepítésének megtervezése | Microsoft dokumentumok
description: Ismerje meg, mit kell figyelembe venni az Azure Files központi telepítésének tervezésekor.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d5bf3a6df9d7292c18a93737fb7dea5d8c91f984
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536487"
---
# <a name="planning-for-an-azure-files-deployment"></a>Az Azure Files üzembe helyezésének megtervezése
[Az Azure Files](storage-files-introduction.md) kétféleképpen telepíthető: a kiszolgáló nélküli Azure-fájlmegosztások közvetlen csatlakoztatásával vagy az Azure-fájlmegosztások helyszíni gyorsítótárazásával az Azure File Sync használatával. Melyik telepítési lehetőséget választja, módosítja azokat a dolgokat, amelyeket figyelembe kell vennie a központi telepítés megtervezésekor. 

- **Az Azure-fájlmegosztás közvetlen csatlakoztatása:** Mivel az Azure Files SMB-hozzáférést biztosít, a Windows, macOS és Linux rendszerben elérhető szabványos SMB-ügyfél használatával csatlakoztathatja az Azure-fájlmegosztásokat a helyszínen vagy a felhőben. Mivel az Azure-fájlmegosztások kiszolgáló nélküliek, az éles környezetben történő üzembe helyezéshez nincs szükség fájlkiszolgáló vagy NAS-eszköz kezelésére. Ez azt jelenti, hogy nem kell szoftverjavításokat alkalmaznia, vagy fizikai lemezeket cserélnie. 

- **Gyorsítótár az Azure-fájlmegosztás helyszíni Azure File Sync:** Azure File Sync lehetővé teszi, hogy központosítsa a szervezet fájlmegosztások az Azure Files, miközben a rugalmasság, a teljesítmény és a kompatibilitás egy helyszíni fájlkiszolgáló. Az Azure File Sync átalakítja a helyszíni (vagy felhőbeli) Windows Server egy gyors gyorsítótár az Azure-fájlmegosztás. 

Ez a cikk elsősorban a helyszíni vagy felhőbeli ügyfél által közvetlenül csatlakoztatandó Azure-fájlmegosztás üzembe helyezésének szempontjaival foglalkozik. Az Azure File Sync központi telepítésének megtervezéséhez olvassa el [az Azure File Sync telepítésének megtervezése.](storage-sync-files-planning.md)

## <a name="management-concepts"></a>Kezelési koncepciók
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Az Azure-fájlmegosztások tárfiókokba történő telepítésekor a következőket javasoljuk:

- Az Azure-fájlmegosztások üzembe helyezése más Azure-fájlmegosztásokkal rendelkező tárfiókokban. Bár a GPv2-tárfiókok lehetővé teszik vegyes célú tárfiókok, mivel a tárolási erőforrások, például az Azure-fájlmegosztások és a blob tárolók osztoznak a tárfiók korlátait, az erőforrások összekeverése megnehezítheti a teljesítményproblémák elhárítását később. 

- Az Azure-fájlmegosztások üzembe helyezésekor a tárfiók IOPS-korlátaira figyelve. Ideális esetben a fájlmegosztások 1:1 tárfiókkal, de ez nem mindig lehetséges, mivel a különböző korlátok és korlátozások, mind a szervezet és az Azure-ból. Ha nem lehetséges, hogy csak egy fájlmegosztás takarásban van telepítve, fontolja meg, hogy mely megosztások lesznek nagyon aktívak, és mely megosztások lesznek kevésbé aktívak annak érdekében, hogy a legforróbb fájlmegosztások ne kerüljön ugyanabba a tárfiókba.

- Csak gpv2- és FileStorage-fiókokat telepítsen, és frissítse a GPv1- és klasszikus tárfiókokat, ha megtalálja őket a környezetben. 

## <a name="identity"></a>Identitás
Az Azure-fájlmegosztás eléréséhez a fájlmegosztás felhasználójának hitelesítenie kell magát, és engedéllyel kell rendelkeznie a megosztás eléréséhez. Ez a fájlmegosztáshoz hozzáférő felhasználó identitásán alapul. Az Azure Files három fő identitásszolgáltatóval integrálható:
- **Helyszíni Active Directory tartományi szolgáltatások (AD DS vagy helyszíni Active DS)** (előzetes verzió): Az Azure storage-fiókok is csatlakoztathatók az ügyfél tulajdonában lévő, Active Directory tartományi szolgáltatásokhoz, akárcsak egy Windows Server fájlkiszolgálóhoz vagy NAS-eszközhöz. Üzembe helyezhet egy tartományvezérlőt a helyszínen, egy Azure virtuális gépben, vagy akár egy másik felhőszolgáltatóban lévő virtuális gépként; Az Azure Files független a tartományvezérlő helyéhez képest. Miután egy tárfiók tartományhoz csatlakozott, a végfelhasználó csatlakoztathatja a fájlmegosztást azzal a felhasználói fiókkal, amelyen bejelentkezett a számítógépére. Az AD-alapú hitelesítés a Kerberos hitelesítési protokollt használja.
- **Azure Active Directory tartományi szolgáltatások (Azure AD DS)**: Az Azure AD DS egy Microsoft által felügyelt tartományvezérlőt biztosít, amely használható az Azure-erőforrásokhoz. A tárfiókhoz az Azure AD DS-hez való csatlakozás a tartományhoz való csatlakozáshoz hasonló előnyöket biztosít, mint az ügyfél tulajdonában lévő Active Directoryhoz való csatlakozás. Ez a telepítési lehetőség az AD-alapú engedélyeket igénylő alkalmazás-lift és-shift esetek esetén a leghasznosabb. Mivel az Azure AD DS AD-alapú hitelesítést biztosít, ez a beállítás a Kerberos hitelesítési protokollt is használja.
- **Azure storage-fiók kulcs:** Az Azure-fájlmegosztások is csatlakoztathatók egy Azure storage-fiók kulcs. A fájlmegosztás ily módon történő csatlakoztatásához a tárfiók nevét használja a felhasználónév, a tárfiók kulcsa pedig jelszóként. A tárfiók kulcs ának csatlakoztatása az Azure fájlmegosztás hatékonyan rendszergazdai művelet, mivel a csatlakoztatott fájlmegosztás teljes engedélyekkel rendelkezik a megosztáson lévő összes fájlhoz és mappához, még akkor is, ha rendelkezik ACL-okkal. Ha a tárfiók kulcsát használja az SMB csatlakoztatásához, az NTLMv2 hitelesítési protokollt használja a rendszer.

A helyszíni fájlkiszolgálókról átkelő vagy a Windows fájlkiszolgálókként vagy NAS-berendezésekként való használatra szánt Azure Files új fájlmegosztások létrehozása esetén ajánlott az ajánlott tartomány, amely a tárfiókot az **Ügyfél tulajdonában lévő Active Directoryhoz** csatlakozik. Ha többet szeretne tudni arról, hogy mi ként csatlakozik a tárfiókhoz egy ügyfél tulajdonában lévő Active Directoryhoz, olvassa el az [Azure Files Active Directory áttekintéscímű témakört.](storage-files-active-directory-overview.md)

Ha a tárfiók kulcs használatával szeretné elérni az Azure-fájlmegosztásokat, javasoljuk, hogy a [hálózatkezelés](#networking) szakaszban leírtak szerint használja a szolgáltatásvégpontokat.

## <a name="networking"></a>Hálózat
Az Azure-fájlmegosztások bárhonnan elérhetők a tárfiók nyilvános végpontján keresztül. Ez azt jelenti, hogy a hitelesített kérelmek, például a felhasználó bejelentkezési identitása által engedélyezett kérelmek biztonságosan származhatnak az Azure-on belülről vagy kívülről. Számos ügyfélkörnyezetben az Azure-fájlmegosztás kezdeti csatlakoztatása a helyszíni munkaállomáson sikertelen lesz, még akkor is, ha az Azure-beli virtuális gépek csatlakoztatása sikeres lesz. Ennek az az oka, hogy számos szervezet és internetszolgáltató (ISP) blokkolja az SMB által a kommunikációhoz használt portot, a 445-ös portot. A [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) összefoglalja, hogy mely internetszolgáltatók engedélyezik vagy tiltják a 445-ös porton keresztüli hozzáférést.

Az Azure-fájlmegosztáshoz való hozzáférés feloldásához két fő lehetősége van:

- A szervezet helyszíni hálózatának 445-ös portjának feloldása. Az Azure-fájlmegosztások csak a nyilvános végponton keresztül érhetők el külsőleg az internetbiztonságos protokollok, például az SMB 3.0 és a FileREST API használatával. Ez a legegyszerűbb módja az Azure-fájlmegosztás helyszíni elérésének, mivel a szervezet kimenő portszabályainak módosítása intette meg a speciális hálózati konfigurációt, azonban azt javasoljuk, hogy távolítsa el az SMB protokoll örökölt és elavult verzióit, nevezetesen az SMB 1.0-s verzióját. Ennek módjáról a [Windows/Windows Server biztonságossá tétele](storage-how-to-use-files-windows.md#securing-windowswindows-server) és [a Linux védelme című](storage-how-to-use-files-linux.md#securing-linux)témakörben olvashat.

- Access Azure-fájlmegosztások ExpressRoute- vagy VPN-kapcsolaton keresztül. Ha az Azure-fájlmegosztást egy hálózati alagúton keresztül éri el, csatlakoztathatja az Azure-fájlmegosztást, például egy helyszíni fájlmegosztást, mivel az SMB-forgalom nem haladja meg a szervezeti határt.   

Bár technikai szempontból jelentősen könnyebb az Azure-fájlmegosztások nyilvános végponton keresztül történő csatlakoztatása, arra számítunk, hogy a legtöbb ügyfél úgy dönt, hogy ExpressRoute- vagy VPN-kapcsolaton keresztül csatlakoztatja az Azure-fájlmegosztásokat. Ehhez a következőket kell konfigurálnia a környezetéhez:  

- **Az ExpressRoute, a helyek**közötti vagy a helyek közötti VPN használatával történő hálózati bújtatás: A virtuális hálózatba való bújtatás lehetővé teszi az Azure-fájlmegosztások helyszíni elérését, még akkor is, ha a 445-ös port le van tiltva.
- **Privát végpontok:** A privát végpontok dedikált IP-címet adnak a tárfióknak a virtuális hálózat címteréből. Ez lehetővé teszi a hálózati bújtatás nélkül kell megnyitni a helyszíni hálózatok akár az Azure storage-fürtök tulajdonában lévő összes IP-címtartományban. 
- **DNS-továbbítás:** Konfigurálja a helyszíni DNS-t úgy, hogy a tárfiók (azaz `storageaccount.file.core.windows.net` a nyilvános felhőrégiók) nevét a privát végpontok IP-címére oldja fel.

Az Azure-fájlmegosztás üzembe helyezéséhez társított hálózati tervezésmegtervezéséhez olvassa el az [Azure Files hálózati szempontok című témakört.](storage-files-networking-overview.md)

## <a name="encryption"></a>Titkosítás
Az Azure Files két különböző típusú titkosítást támogat: az átvitel során történő titkosítást, amely az Azure-fájlmegosztás csatlakoztatása/elérése során használt titkosításhoz kapcsolódik, és az inaktív titkosítást, amely az adatok titkosításának módját használja a lemezen való tároláskor. 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Alapértelmezés szerint az összes Azure storage-fiókok titkosítás i átvitel e titkosítás engedélyezve van. Ez azt jelenti, hogy ha egy fájlmegosztást csatlakoztat SMB-n keresztül, vagy a FileREST protokollon keresztül (például az Azure Portalon, a PowerShell/CLI-n vagy az Azure SDK-n keresztül) keresztül fér hozzá, az Azure Files csak akkor engedélyezi a kapcsolatot, ha smb 3.0+ titkosítással vagy HTTPS-lel készült. Azok az ügyfelek, amelyek nem támogatják az SMB 3.0-s vagy az SMB 3.0-s titkosítást támogató, de az SMB-titkosítást nem támogató ügyfelek nem tudják csatlakoztatni az Azure fájlmegosztást, ha az átvitel alatt titkosítás engedélyezve van. Arról, hogy mely operációs rendszerek támogatják az SMB 3.0 titkosítását, tekintse meg a [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)és Linux részletes [dokumentációját.](storage-how-to-use-files-linux.md) A PowerShell, a CLI és az SDK-k összes jelenlegi verziója támogatja a HTTPS protokollt.  

Letilthatja a titkosítást az átvitel során egy Azure-tárfiók. Ha a titkosítás le van tiltva, az Azure Files is lehetővé teszi az SMB 2.1, SMB 3.0 titkosítás nélkül, és titkosítatlan FileREST API-hívások HTTP-n keresztül. Az átvitel során a titkosítás letiltásának elsődleges oka egy régebbi operációs rendszeren, például Windows Server 2008 R2 vagy régebbi Linux-disztribúción futtatandó örökölt alkalmazás támogatása. Az Azure Files csak az Azure 2.1-es kapcsolatokat engedélyezi ugyanabban az Azure-régióban, mint az Azure fájlmegosztás; az Azure-fájlmegosztás Azure-régióján kívüli SMB 2.1-es ügyfél, például a helyszíni vagy egy másik Azure-régióban, nem fogja tudni elérni a fájlmegosztást.

Javasoljuk, hogy biztosítsa az adatok titkosításának engedélyezését az átvitel közbeni átvitel alatt.

Az átvitel közbeni titkosításról további információt az [Azure storage-beli biztonságos átvitel megkövetelése című témakörben talál.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Tárolási rétegek
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Általánosságban elmondható, hogy az Azure Files szolgáltatásai és a más szolgáltatásokkal való együttműködés azonosak a prémium szintű fájlmegosztások és a szabványos fájlmegosztások között, azonban van néhány fontos különbség:
- **Számlázási modell**
    - A prémium szintű fájlmegosztások számlázása egy kiépített számlázási modell használatával kerül számlázásra, ami azt jelenti, hogy a ténylegesen kért tárterület helyett a leépített tárterületért kell fizetnie. 
    - A szabványos fájlmegosztások számlázása használatalapú fizetéses modell használatával történik, amely tartalmazza a ténylegesen felhasznált tárterület alapköltségét, majd a megosztás használatának módjától függő további tranzakciós költségeket. A szabványos fájlmegosztások, a számla növekedni fog, ha az Azure-fájlmegosztás (olvasás/írás/csatlakoztatás) használata.
- **Redundancia-beállítások**
    - Prémium szintű fájlmegosztások csak helyileg redundáns (LRS) és zónaredundáns (ZRS) tárolók esetén érhetők el. 
    - A normál fájlmegosztások helyileg redundáns, zónaredundáns, georedundáns (GRS) és geozóna redundáns (GZRS) tároláshoz érhetők el.
- **A fájlmegosztás maximális mérete**
    - Prémium fájlmegosztások akár 100 TiB-ig is kiépíthetők további munka nélkül.
    - Alapértelmezés szerint a normál fájlmegosztások legfeljebb 5 TiB-re terjedhetnek ki, bár a megosztási korlát 100 TiB-re növelhető a *nagy fájlmegosztási* tárfiók szolgáltatásjelzőjének beállításával. A normál fájlmegosztások csak 100 TiB-ig terjedhetnek helyileg redundáns vagy zónaredundáns tárfiókok esetén. A fájlmegosztások méretének növeléséről a [Nagy fájlmegosztások engedélyezése és létrehozása](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)című témakörben talál további információt.
- **Régiónkénti rendelkezésre állás**
    - Prémium szintű fájlmegosztások nem érhetők el minden régióban, és zóna redundáns támogatás érhető el a régiók kisebb része. Ha meg szeretné tudni, hogy a prémium fájlmegosztások jelenleg elérhetők-e az Ön régiójában, tekintse meg az Azure [régiónként elérhető termékeit.](https://azure.microsoft.com/global-infrastructure/services/?products=storage) Ha meg szeretné tudni, hogy milyen régiók támogatják a ZRS-t, tekintse meg az [Azure availability zone támogatását régiónként.](../../availability-zones/az-overview.md#services-support-by-region) Annak érdekében, hogy az új régiók és a prémium szintű funkciók rangsorolásban segítsünk, kérjük, töltse ki ezt a [felmérést.](https://aka.ms/pfsfeedback)
    - A standard fájlmegosztások minden Azure-régióban elérhetők.
- Az Azure Kubernetes Service (AKS) támogatja a prémium fájlmegosztások 1.13-as és újabb verzióban.

Ha egy fájlmegosztás prémium vagy normál fájlmegosztásként jön létre, nem konvertálhatja automatikusan a másik szintre. Ha át szeretne váltani a másik szintre, létre kell hoznia egy új fájlmegosztást a rétegben, és manuálisan kell átmásolnia az adatokat az eredeti megosztásból a létrehozott új megosztásra. Azt javasoljuk, hogy a `robocopy` Windows vagy `rsync` a macOS és a Linux, hogy végre ezt a példányt.

### <a name="understanding-provisioning-for-premium-file-shares"></a>A prémium fájlmegosztások kiépítése
A prémium szintű fájlmegosztások rögzített GiB/IOPS/átviteli sebességarány alapján vannak kiépítve. Minden egyes kiépített GiB esetében a megosztás egy IOPS-t és 0,1 MiB/s átviteli értéket kap, az egy részvényre jutó maximális korlátig. A minimálisan engedélyezett kiépítés 100 GiB min IOPS/átviteli keresztülórával.

A legjobb erőfeszítés alapján az összes megosztás oka legfeljebb három IOPS/GiB a kiosztott tárterület 60 percig vagy hosszabb, méretétől függően a megosztás. Az új részvények a kiosztott kapacitás on alapuló teljes burst hitellel kezdődnek.

A részvényeket 1 GiB-növekményben kell kiépíteni. Minimális méret 100 GiB, a következő méret 101 GiB és így tovább.

> [!TIP]
> Kiindulási IOPS = 1 * kiépített GiB. (Legfeljebb 100 000 IOPS).(Up to a max of 100,000 IOPS).
>
> Burst Limit = 3 * Kiindulási IOPS. (Legfeljebb 100 000 IOPS).(Up to a max of 100,000 IOPS).
>
> kimenő forgalom = 60 MiB/s + 0,06 * kiépített GiB
>
> be- és bejuttatási arány = 40 MiB/s + 0,04 * kiépített GiB

A kiépített megosztási méretet a megosztási kvóta határozza meg. A megosztási kvóta bármikor növelhető, de csak az utolsó növekedés óta eltelt 24 óra után csökkenthető. Miután 24 órát várt kvótanövelés nélkül, annyiszor csökkentheti a megosztási kvótát, ahányszor csak szeretné, amíg újra növelni nem fogja. Az IOPS/átviteli átviteli skálán végrehajtott módosítások a méretváltozás után néhány percen belül lépnek hatályba.

Lehetőség van a kiosztott megosztás méretének csökkentésére a használt GiB alatt. Ha ezt teszi, nem veszíti el az adatokat, de továbbra is a használt méretért kell fizetnie, és a kiosztott megosztás teljesítményét (alapkonfigurációI IOPS, átviteli és burst IOPS) kapja meg, nem a használt méretet.

Az alábbi táblázat néhány példát mutat be ezekre a képletekre a kiosztott megosztásméretekesetében:

|Kapacitás (GiB) | Kiindulási IOPS | Burst IOPS | Kimenő (MiB/s) | Be- és be- és ab.-be |
|---------|---------|---------|---------|---------|
|100         | 100     | Akár 300     | 66   | 44   |
|500         | 500     | Akár 1500   | 90   | 60   |
|1,024       | 1,024   | Akár 3 072   | 122   | 81   |
|5,120       | 5,120   | Akár 15 360  | 368   | 245   |
|10,240      | 10,240  | Akár 30 720  | 675 | 450   |
|33,792      | 33,792  | Akár 100 000 | 2,088 | 1,392   |
|51,200      | 51,200  | Akár 100 000 | 3,132 | 2,088   |
|102,400     | 100 000 | Akár 100 000 | 6,204 | 4,136   |

> [!NOTE]
> Fájl megosztások teljesítmény függ gép hálózati korlátok, a rendelkezésre álló hálózati sávszélesség, Io méretek, párhuzamosság, sok más tényező. Például a 8 KiB olvasási/írási IO-mérettel rendelkező belső tesztelés alapján egyetlen Windows virtuális gép, *a Standard F16s_v2*, amely az SMB-n keresztüli prémium szintű fájlmegosztáshoz csatlakozik, 20K olvasási IOPS-t és 15K írási IOPS-t érhet el. Az 512 MiB olvasási/írási IO-méretekkel ugyanaz a virtuális gép 1,1 GiB/s kimenő és 370 MiB/s kimenő átviteli szintet érhet el. A maximális teljesítményskála elérése érdekében a terhelést több virtuális gépközött is elosztva. Kérjük, olvassa el a [hibaelhárítási útmutatót](storage-troubleshooting-files-performance.md) néhány gyakori teljesítménybeli problémáról és kerülő megoldásról.

#### <a name="bursting"></a>Tele
Prémium fájlmegosztások is tört az IOPS-ig háromszorosára. A bursting automatizált, és egy kreditrendszeren alapul. A bursting a legjobb erőfeszítés alapján működik, és a burst limit nem garancia, a fájlmegosztások *a határértékig felrobbanhatnak.*

A kreditek egy burst gyűjtőben halmozódnak fel, ha a fájlmegosztás forgalma az alapkonfigurációi IOPS alatt van. Egy 100 GiB-megosztás például 100 eredeti IOPS-érték. Ha a megosztás tényleges forgalma 40 IOPS volt egy adott 1 másodperces időközhöz, majd a 60 fel nem használt IOPS egy burst gyűjtőben kerül jóváírásra. Ezek a jóváírások később kerülnek használatba, amikor a műveletek meghaladnák az eredeti IPP-ket.

> [!TIP]
> A burst gyűjtő mérete = Kiindulási IOPS * 2 * 3600.

Ha egy megosztás meghaladja az alapszintű IOPS-t, és egy burst gyűjtőben rendelkezik kreditekkel, akkor felrobban. A részvények továbbra is felrobbanhatnak, amíg a kreditek megmaradnak, bár az 50 TiB-nél kisebb részvények csak egy órán keresztül maradnak a burst limiten. Az 50 TiB-nél nagyobb részvények technikailag meghaladhatják ezt az egy órás korlátot, legfeljebb két órát, de ez a felhalmozott burst kreditek számán alapul. Minden i/o-t a kiindulási IOPS-on túl egy kreditet használ fel, és az összes kredit felhasználása után a megosztás visszatér a kiindulási IOPS-hoz.

A részvénykreditek három állammal rendelkeznek:

- Felhalmozódó, ha a fájlmegosztás kevesebb, mint az alaptervIOPS.Accruing, when the file share is using less than the baseline IOPS.
- Csökkenő, a fájlmegosztás felszakadásakor.
- A fennmaradó állandó, ha nincsenek kreditek, vagy az alaptervi IOPS használatban van.

Az új fájlmegosztások a sorozatgyűjtőben lévő kreditek teljes számával kezdődnek. A burst kreditek nem halmozódnak fel, ha a megosztási IOPS a kiszolgáló általi szabályozás miatt az alapvonali IOPS alá csökken.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>A szabványos fájlmegosztások akár 100 TiB-ig való lehetővé tétele
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Korlátozások
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundancia
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrálás
Sok esetben nem fog létrehozni egy nettó új fájlmegosztást a szervezet számára, hanem egy meglévő fájlmegosztás áttelepítése egy helyszíni fájlkiszolgálóról vagy NAS-eszközről az Azure Files-ba. A Microsoft és a harmadik felek számos eszközt biztosítanak a fájlmegosztásra való áttéréshez, de ezek nagyjából két kategóriába sorolhatók:

- **A fájlrendszer attribútumait karóeszközök, például a a c-k és az időbélyegek:**
    - **[Azure File Sync:](storage-sync-files-planning.md)** Az Azure File Sync használható az adatok Azure-fájlmegosztásba történő betöltésére, még akkor is, ha a kívánt végső üzembe helyezés nem a helyszíni jelenlét fenntartása. Az Azure File Sync a meglévő Windows Server 2012 R2, Windows Server 2016 és Windows Server 2019 telepítéseken telepíthető. Az Azure File Sync betöltési mechanizmusként való használatának előnye, hogy a végfelhasználók továbbra is használhatják a meglévő fájlmegosztást; az Azure-fájlmegosztásra való átvágás akkor fordulhat elő, ha az összes adat feltöltése befejeződött a háttérben.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy egy jól ismert másolási eszköz, hogy a hajók a Windows és a Windows Server. A Robocopy segítségével adatokat vihet át az Azure Files-ba a fájlmegosztás helyi csatlakoztatásával, majd a robocopy parancs ban a csatlakoztatott hely használatával.

- **A fájlrendszer attribútumait nem fenntartó eszközök**:
    - **Adatmező:** A Data Box offline adatátviteli mechanizmust biztosít az adatok Azure-ba történő fizikai szállításához. Ez a módszer az átviteli sávszélesség növelésére és a sávszélesség megtakarítására szolgál, de jelenleg nem támogatja a fájlrendszer attribútumait, például az időbélyegeket és az ACL-eket.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: AzCopy egy parancssori segédprogram, amelyet az Azure Files-ba és az Azure Files-ból, valamint az Azure Blob storage-ba történő másolásra terveztek, egyszerű parancsokat használva optimális teljesítménnyel.

## <a name="next-steps"></a>További lépések
* [Az Azure-fájlszinkronizálás telepítésének megtervezése](storage-sync-files-planning.md)
* [Az Azure-fájlok telepítése](storage-files-deployment-guide.md)
* [Az Azure-fájlszinkronizálás telepítése](storage-sync-files-deployment-guide.md)
