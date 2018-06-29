---
title: Az Azure éles hálózati környezetben
description: A cikkben a Microsoft Azure éles hálózati általános leírása.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102286"
---
# <a name="azure-production-network"></a>Az Azure éles hálózati környezetben
A felhasználók az éles Azure-hálózat elérése a saját Microsoft Azure-alkalmazások, valamint a belső Microsoft Azure technikai támogatási csoporthoz, amely a termelési hálózat kezeléséhez külső ügyfeleket tartalmazza. A biztonsági hozzáférési módszerek és a védelmi mechanizmus létrehozásához Azure üzemi hálózathoz való csatlakozást Ez a cikk ismerteti.

## <a name="internet-routing-and-fault-tolerance"></a>Internet útválasztást és a hibatűrés
Adja meg egy globális redundáns belső és külső Microsoft Azure tartomány nevét (WADNS) szolgáltatás infrastruktúrát együtt több elsődleges és másodlagos tartománynév-szolgáltatás (DNS) fürt közben további Microsoft Azure hálózati hibatűrés biztonsági intézkedések, például a NetScaler segítségével elosztott szolgáltatásmegtagadásos (DDoS-) támadások megelőzése érdekében, valamint a Microsoft Azure DNS-szolgáltatások integritását.

A WADNS-kiszolgálók több adatközpont-létesítményekben találhatók. A WADNS végrehajtása magában foglalja a másodlagos/elsődleges DNS-kiszolgálók hierarchiájában a nyilvánosan Azure felhasználói tartománynevek. A tartománynevek általában CloudApp.net címre, amely a virtuális IP-cím (VIP) cím az az ügyfélszolgálat becsomagolja oldható fel. Egyedi az Azure-ba, a belső, a bérlő fordítás dedikált IP-címet (DIP)-címhez tartozó VIP végezhető el a Microsoft terheléselosztók adott VIP felelős.

Azure földrajzilag elosztott Azure adatközpontjaiban az Egyesült Államok belül üzemel, és a hatékony és méretezhető architektúra szabványok végrehajtási állapota-a-modern útválasztási platformok épül. A fontos funkciók a következők:

- Többprotokollos címke váltás (MPLS) alapú forgalom mérnöki hatékony kapcsolatok használatát és a szolgáltatás szabályos teljesítménycsökkenése megadásával kimaradás esetén
- Hálózatok vannak megvalósítva "szükség plusz egy" (N + 1) redundancia architektúrák vagy nagyobb frekvenciával.
- Külsőleg az adatközpontok dedikált, nagy sávszélességű hálózati kapcsolatok redundantly kapcsolódó tulajdonságok a több mint 1200 globálisan pontokon több társviszony-létesítési biztosító 2000 gigabájt (GB) / amelyek átlépik ezt az internetszolgáltatók szolgálja ki peremhálózati kapacitás.

Microsoft tulajdonában van, a saját hálózati kapcsolatok adatközpont között, mivel ezek az attribútumok súgó elérése 99.9 + az Azure elérhető % a hálózat rendelkezésre állásának hagyományos külső internetszolgáltatók szükségessége nélkül.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Éles hálózati környezetben és a kapcsolódó tűzfalak kapcsolat
Az Azure-hálózat internetes forgalom adatfolyam házirend irányítja a forgalmat az Azure éles hálózati környezetben található, a legközelebbi regionális adatközpontok az Amerikai Egyesült Államokból. Mivel az Azure éles adatközpontokban karbantartását konzisztens hálózati architektúra és hardver, a forgalom áramlását alatt leírás vonatkozik következetesen összes adatközpontok.

Amennyiben az Internet Azure továbbítódik a legközelebbi adatközpontba, kapcsolatot létesít a hozzáférés-útválasztóhoz. A hozzáférés útválasztók az Azure-csomópontok és ügyfél-példányként létrehozott virtuális gépek közötti forgalom elkülönítésére szolgálnak. Hálózati infrastruktúra eszközök a hozzáférés és a peremhálózati helyeken a határ pontokat, ahol érkező és/vagy a kilépési alkalmazza a rendszer. Az útválasztókat úgy vannak konfigurálva, nem kívánt hálózati forgalom szűrésére és szűkítheti forgalom sebességét, szükség esetén egy rétegzett ACL keresztül. Hozzáférés-vezérlési lista által engedélyezett a terheléselosztók van átirányítva. Terjesztési útválasztók engedélyezése csak a Microsoft által jóváhagyott IP-címeket, adja meg az ACL-ekkel hamisításszűrés, és a meghatározott TCP-kapcsolatok tervezték.

Külső terheléselosztási eszközök az access útválasztók végezzen el a hálózati címfordítás (NAT) a internetről elérhető IP-címek az Azure belső IP-cím mögött találhatók. Ezek is csomagok továbbításához érvényes éles belső IP-címek és portok, és korlátozza az ilyen belső éles hálózati környezetben címterület egy védelmi mechanizmust összekötőként.

Alapértelmezés szerint a Microsoft érvényesíti titkosítatlanul jelentkezzen be a böngésző, így az ügyfél webszolgáltatás és az összes forgalom azt követően minden forgalom Hypertext Transfer Protocol Secure (HTTPS). A TLS 1.2-es verzió használatát lehetővé teszi, hogy egy biztonságos csatornán keresztül forgalom. ACL-ek hozzáférés és core útválasztók győződjön meg arról, mi az elvárás konzisztens a forgalom forrása.

Ebben az architektúrában hagyományos biztonsági architektúrája képest fontos különbség az, hogy nincsenek-e nincs kijelölt hardveres tűzfalak, speciális behatolás észlelési/eszközök vagy más biztonsági készülékek általában várt előtt kapcsolatok hozhatók létre az Azure éles környezetbe. Általában elvárt ezek hardvereszközök tűzfal az Azure-hálózat; van azonban nincs Azure-ban alkalmazott. Ezeket a funkciókat szinte kizárólag beépülnek a tűzfal tervezéskor robusztus többrétegű biztonsági mechanizmust biztosít az Azure környezetben futó szoftver. Emellett a határ, és a kritikus fontosságú biztonsági eszközök társított sprawl hatóköre könnyebben kezelheti és leltárt, mert azt a szoftvert futtató Azure felügyeli a fenti ábrán látható módon.

## <a name="core-security-and-firewall-features"></a>Biztonság és a tűzfalon alapszolgáltatások
Azure valósítja meg a nagy teljesítményű biztonsági és a tűzfalon szolgáltatások kényszeríteni a biztonsági funkciók általában az érzékelők hagyományos környezetben az alapvető biztonsági engedélyezési határ védelme különböző szinteken.

### <a name="azure-security-features"></a>Az Azure biztonsági funkciói
Azure valósítja meg a szoftver állomásalapú tűzfal az éles hálózaton belül. Több alapvető biztonsági és a szolgáltatások tűzfal található, az alapszintű Azure-alapú környezetben. Ezek a biztonsági szolgáltatások egy védelmi-stratégia az Azure környezetben tükrözi. Az ügyfél a Microsoft Azure-ban védelmét, hogy a következő tűzfalak:

**Hipervizor tűzfal (csomagszűrők)**: A tűzfal valósult meg a hipervizor és az FC-ügynök konfigurálva van. A tűzfal védi a bérlő fut a virtuális Gépen belül az illetéktelen hozzáféréstől. Alapértelmezés szerint egy virtuális gép létrehozásakor az összes forgalom le van tiltva, és az FC-ügynök hozzáadja szabályok/kivételek engedélyezett forgalom engedélyezése a szűrő.

Az itt programozott szabályok két kategóriába sorolhatók:

- Számítógép-konfiguráció vagy infrastruktúra-szabályok: alapértelmezés szerint minden kommunikációja blokkolva van. Engedélyezi a virtuális gépek küldeni és fogadni a Dynamic Host Configuration Protocol (DHCP) kommunikációs, a DNS-adatok, forgalmat küldeni a "nyilvános", a Szálcsatornás fürt és az operációs rendszer aktiválási kiszolgálón más virtuális gépek kimenő Internet kivételek vannak. Mivel a virtuális gépek engedélyezettek listájához, a kimenő célok nem tartalmazza a Microsoft Azure útválasztó alhálózatokat és más Microsoft-tulajdonságok, a szabályok számukra összekötőként védelmi réteget.
- Szerepkör konfigurációs fájl: A bejövő hozzáférés-vezérlési listákat a bérlők modell alapján határozza meg. Például ha egy bérlőt egy előtér-webkiszolgáló 80-as porton egy adott virtuális gépen, akkor az 80-as port minden IP-cím van megnyitva. Ha a virtuális gépen futó feldolgozói szerepkörök, majd a feldolgozói szerepkör meg van nyitva csak a virtuális Gépen belül ugyanannak a bérlőnek.

**Natív gazdagép tűzfalának**: Microsoft Azure Fabric- és tárolási futtassa egy natív operációs rendszer, amelyek nem hipervizor rendelkezik, és ezért a Windows tűzfal konfigurációja a fenti két szabálykészlet.

**Tűzfal gazdagép**: az állomás tűzfal védi a gazdagép partíciójához, amely futtatja a hipervizor. A szabályok csak a FC engedélyezése és jump felvegye egy adott portot a gazdagép partíciójához be van programozott. A kivételek közé tartozik még a DHCP- és DNS-válaszok engedélyezése. Azure használja a gép konfigurációs fájlt, amely rendelkezik a sablon a gazdagép partíciójához tűzfalszabály-konfiguráció. Is van, amely lehetővé teszi a virtuális gépek állomás összetevők, vezetékes kiszolgáló & metaadat-kiszolgáló adott protokoll-porton keresztül kommunikáljon a gazdagép tűzfalához kivételt.

**Vendég tűzfal**: a Windows tűzfal adat, a vendég operációs rendszer, az ügyfél virtuális gépek és a tároló az ügyfél által konfigurálható.

További biztonsági lehetőségek építve az Azure-képességek:

- Infrastruktúra-összetevőihez rendelt, dedikált IP (immerzióban) az IP-címeket. Az interneten a támadó nem-forgalmat az ezekre a címekre, mert nem érné el azokat a Microsoft. Internetes átjáró útválasztók végez csomagszűrést címzett kizárólag belső címeket, így azokat nem kell beírnia a éles hálózati környezetben. A csak fogadja el a virtuális IP-címek irányított forgalom összetevői terheléselosztók.
- A tűzfalak implementálva az összes belső csomópontok rendelkezik három elsődleges biztonsági architektúra szempontjai bármely adott esethez:

   - Ezek mögött a Load Balancer (LB) kerülnek, és fogadja el a csomagok bárhonnan. Ezek a célja, hogy külsőleg elérhetővé tehető, és a hagyományos szegélyhálózati tűzfal megnyitott portok korlátozódott.
   - Csak fogadnia címek korlátozott számú-csomagokat. Ez az szolgáltatásmegtagadási támadások elleni védelmet részletes stratégiájának a részét. Az ilyen kapcsolatok kriptográfiai hitelesítése.
   - Tűzfalak csak select belső csomópontjáról érhető el, ebben az esetben elfogadják a csomagok csak egy forrás IP-címek, amelyek mindegyike immerzióban belül az Azure-hálózatot felsorolt listája. Például a vállalati hálózaton a támadás irányíthatják az ezekre a címekre kérelmeket, de le lesz tiltva, kivéve, ha a csomag forrás címe volt a felsorolt listán belül az Azure-hálózatot.
   - A hozzáférés-útválasztó a szervezet határain blokkolja a kimenő csomagok egy olyan címre, hogy az Azure-hálózatot a konfigurált statikus útvonalak miatt.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)
