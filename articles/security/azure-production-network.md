---
title: Azure production network
description: Ez a cikk az Azure éles hálózati környezetben általános leírása.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: afae7cc6390ea4cd8c18c687e9d99400c8da9da4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116932"
---
# <a name="the-azure-production-network"></a>Az Azure éles hálózati környezetben
Az Azure éles hálózati környezetben, a felhasználók közé tartozik a mindkét külső ügyfelek esetében, aki a hozzáférést a saját Azure-alkalmazások és a belső Azure-támogatási személyzetnek kezelheti az éles hálózati környezetben. Ez a cikk ismerteti a biztonsági hozzáférési metódusokat és a védelmi mechanizmus az Azure éles környezetben való hálózati kapcsolatot lehessen létesíteni.

## <a name="internet-routing-and-fault-tolerance"></a>Internetes útválasztási és hibatűréssel kezelik
Egy globálisan redundáns belső és külső Azure Domain Name Service (DNS) infrastruktúra, több elsődleges és másodlagos DNS server-fürt, kombinálva hibatűrést biztosít. Egy időben a további Azure-hálózat biztonsági intézkedések, például NetScaler, elosztott szolgáltatásmegtagadásos (DDoS) támadásoktól és az Azure DNS-szolgáltatások integritásának szolgálnak.

Az Azure DNS-kiszolgálók több adatközpont-létesítményekben találhatók. Az Azure DNS megvalósítása a másodlagos és az elsődleges DNS-kiszolgálók hierarchiájában a nyilvános Azure-ügyfél tartománynevek magában foglalja. A tartománynevek általában egy CloudApp.net címével, amely az ügyfél szolgáltatás a virtuális IP-cím (VIP) cím burkolja oldható fel. Egyedi az Azure-ba, a virtuális IP-cím, amely megfelel a belső dedikált IP címét (DIP) a bérlő fordítás végzi el a Microsoft terheléselosztók felelős a virtuális IP-cím.

Azure a földrajzilag elosztott Azure adatközpontra az USA-ban üzemeltetett, és be van építve az állapot-az-a legújabb útválasztási platformon, amely nagy teherbírású, méretezhető architekturális szabványoknak megvalósítása. A jelentősebb funkciói közé tartoznak:

- Többprotokollos címke váltás mpls virtuális Magánhálózat-alapú forgalom mérnöki, így hatékony kapcsolatok használatát és a szolgáltatás szabályos teljesítménycsökkenése kimaradás esetén.
- Hálózatok vannak megvalósítva, a "szükséges plusz egy" (N + 1) a redundancia architektúrák vagy jobb.
- Külsőleg adatközpontok dedikált, nagy sávszélességű hálózati kapcsolatcsoporttal összekapcsoló redundantly tulajdonságai a globálisan időpontokban több társviszony-létesítés feletti 1200 internetszolgáltatók szolgálja. Ehhez a kapcsolathoz ajánlatunkban 2000 GB / másodperc (GBps) edge kapacitást biztosít.

A Microsoft tulajdonában lévő, a saját hálózati Kapcsolatcsoportok adatközpontok közötti, mert ezek az attribútumok segítségével hagyományos külső internetszolgáltatók nélkül 99,9 + %-os hálózati rendelkezésre állás az Azure-ajánlatát.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Kapcsolat az éles hálózati környezetben és a kapcsolódó tűzfalak
Az Azure hálózati internetes forgalom folyamat házirend irányítja a forgalmat az Azure éles hálózati környezetben, amely a legközelebbi regionális adatközpont belül az Egyesült Államokban található. Az éles Azure-adatközpontokból konzisztens hálózati architektúra és hardver kezelése, mert a forgalmat a következő folyamat leírásának következetesen minden adatközpontban vonatkozik.

Internetes forgalmat az Azure a legközelebbi adatközponthoz továbbítódik, miután egy kapcsolatot a hozzáférést az útválasztók. A hozzáférés az útválasztók Azure-csomópontok és az ügyfél által létrehozott virtuális gépek közötti forgalom elkülönítésére szolgálnak. Hálózati infrastruktúra eszközök a hozzáférés és a peremhálózati helyeken a határ pontokat, ahol bejövő és kimenő szűrők alkalmazását. Az útválasztókat úgy vannak konfigurálva, nem kívánt hálózati forgalom szűrése és a alkalmazni a forgalom sebességhatárok, szükség esetén egy rétegzett hozzáférés-vezérlési lista (ACL) keresztül. ACL által engedélyezett forgalmat irányítja a rendszer a terheléselosztók. Terjesztési útválasztók csak a Microsoft által jóváhagyott IP-címek engedélyezése hamisításszűrés megadása és hozzáférés-vezérlési listákat használó TCP-kapcsolatok létesítéséhez lett tervezve.

Külső terheléselosztás eszközökön hajtható végre hálózati címfordítás (NAT) a internetről elérhető IP-címek az Azure belső IP-címek hozzáférés útválasztói mögött található. Az eszközök is irányíthatja a csomagok érvényes éles belső IP-címek és portok és azok szerepét egy védelmi mechanizmust, amely korlátozza a is közzéteheti a belső éles hálózat címterét.

Alapértelmezés szerint a Microsoft szigorú továbbított ügyfelei webböngészők, többek között azt követően jelentkezzen be, és minden forgalmat az összes forgalom Hypertext Transfer Protocol biztonságos (HTTPS). A TLS 1.2-es verzió használatát lehetővé teszi egy biztonságos csatornán keresztül haladjon a forgalom számára. ACL-ek hozzáférés és az alapvető útválasztók győződjön meg arról, hogy mi az elvárás összhangban az a forgalom forrását.

Ebben az architektúrában fontos különbség az architektúrában a hagyományos biztonsági a képest, hogy nincsenek-e nincs kijelölt hardveres tűzfalak, speciális behatolásérzékelési vagy megelőzési eszközök vagy más biztonsági eszközökkel, amelyek általában Mielőtt kapcsolatok az Azure éles környezetben végrehajtott várható. Ügyfeleink az Azure-hálózat; általában ezek hardvereszközök tűzfal várható azonban nincs alkalmazzák Azure-ban. Szinte kizárólag azokat a biztonsági funkciók beépített hatékony, többrétegű biztonsági mechanizmusok, többek között tűzfal képességeket biztosít az Azure környezetben futó szoftverekről. Ezenkívül a a határ, és a kritikus fontosságú eszközök társított bővítéseket hatóköre könnyebben felügyelhető és leltár-, mert azt a szoftvert, hogy fut az Azure felügyeli a fenti ábrán látható módon.

## <a name="core-security-and-firewall-features"></a>Biztonság és a tűzfal alapszolgáltatások
Az Azure nagy teljesítményű biztonsági és a különböző szinteken kikényszeríteni a biztonsági funkciókat, amelyeket az alapvető biztonsági engedélyt határ védelmére a hagyományos környezetben általában várhatóan Tűzfalszolgáltatások végeznek.

### <a name="azure-security-features"></a>Az Azure biztonsági funkciók
Az Azure szoftveres állomásalapú tűzfal az éles hálózaton belül valósítja meg. Több alapvető biztonsági és a tűzfal-funkciók az alapvető Azure-környezetben található. Ezeket a biztonsági funkciókat az Azure környezetben jellegű defense stratégia tükrözik. Vásárlói adatok Azure-ban az alábbi tűzfal védi:

**Hipervizor tűzfalat (csomagszűrőt)**: Ez a tűzfal van megvalósítva a hipervizor, és a (FC) hálóvezérlő ügynök által konfigurált. Ez a tűzfal védi a bérlő, amely a virtuális gép fut, a jogosulatlan hozzáférés ellen. Alapesetben, amikor egy virtuális gép létrehozása, minden forgalmat blokkol, majd az FC-ügynök szabályok és kivételek hozzáadja-e meg a szűrő engedélyezett forgalom engedélyezéséhez.

Itt programozott szabályok két kategóriába:

- **A gép konfigurációs vagy infrastrukturális szabályok**: Alapértelmezés szerint minden kommunikáció blokkolva van. Kivételek állnak fenn, amelyek lehetővé teszik egy virtuális Gépet, a Dynamic Host Configuration Protocol (DHCP) kommunikáció és a DNS-adatok küldése és fogadása és forgalom küldése, ha a "nyilvános" internetre kimenő az FC-fürt és az operációsrendszer-aktiválási kiszolgálóra belüli más virtuális gépekre. Mivel a virtuális gépek engedélyezett kimenő listája célok nem tartalmaz Azure útválasztó-alhálózatok és egyéb Microsoft-tulajdonságok, a szabályok azok szerepét egy védelmi réteget.
- **Szerepkör-konfigurációs fájl szabályok**: A bejövő ACL-eket a bérlők szolgáltatási modellje alapján határozza meg. Például ha egy bérlőt egy web front end egy adott virtuális gép 80-as porton, 80-as port meg van nyitva az összes IP-címek. Ha a virtuális gépen futó feldolgozói szerepkör, a feldolgozói szerepkört csak az ugyanazon a bérlőn belül a virtuális gép van nyitva.

**Natív gazdagép tűzfala**: Egy natív operációs rendszeren, amelynek nincs hipervizora futtatása az Azure Service Fabric és az Azure Storage, és ezért Windows tűzfalon konfigurálva van a fenti két szabálycsoport.

**Gazdagép tűzfala**: A gazdagép tűzfala a hipervizort futtató állomás partíció védi. A szabályok úgy vannak programozva, csak az FC engedélyezéséhez, és ugorjon be a gazdagép partíciójához egy adott porton kommunikáljon. A kivételek, hogy a DHCP- és DNS-válaszok. Az Azure használ egy gép konfigurációs fájlját, amely tartalmazza a gazdagép partíciójához vonatkozó tűzfalszabályok sablonját. A gazdagép tűzfal-kivétel is létezik, amely lehetővé teszi a virtuális gépek gazdagép-összetevők, átviteli kiszolgálót és metaadat-kiszolgáló adott protokoll-porton keresztül kommunikáljon.

**Vendég tűzfala**: A Windows tűzfalában a vendég operációs rendszer, amely ügyfél virtuális gépeken és a storage ügyfelek által konfigurálható.

Az Azure-szolgáltatások hatékony beépített biztonsági szolgáltatásai többek között:

- IP-címek, a DIP infrastruktúra-összetevők. Az internetes támadók nem-forgalom ezekre a címekre, mert akkor éri el a Microsoft. Internetes átjárót útválasztók szűrheti a csomagokat, amelyek kizárólag belső címeket, így azok nem adjon meg az éles hálózati környezetben. Fogadja el a forgalmat, amely a virtuális IP-cím van irányítva csak összetevőket terheléselosztók.
- Tűzfalak, amelyek az összes belső csomópontra van három elsődleges biztonsági architektúra szempontjai bármely adott esethez:

   - Tűzfalak kerülnek, a terheléselosztó mögött, és fogadja el a csomagok bárhonnan. Ezek a csomagok célja, hogy kívülről elérhetővé tett, és a hagyományos szegélyhálózati tűzfal nyitott portokra lenne meg.
   - Tűzfalak fogadja el a csomagok csak korlátozott számú címeket. A figyelmet a DDoS elleni védelmi vonalként részletes stratégia részét képezi. Titkosítási szempontból hitelesítése az ilyen kapcsolatok.
   - Tűzfalak csak select belső csomópontok is elérhető. Elfogadják a csomagok csak egy forrás IP-címek, amelyek mindegyike DIP az Azure hálózatán belül sorszámozott listájához. Például a vállalati hálózaton a támadás irányíthatják az ezekre a címekre kéréseket, de a támadások le lesz tiltva, kivéve, ha a csomag forrás-korábban egy, az Azure hálózatán belül a felsorolt listájában.
     - A hozzáférés-útválasztó a szervezet határain blokkolja a kimenő csomagok, amelyek egy címet, amely az Azure hálózatán belül a konfigurált statikus útvonalak miatt.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)
