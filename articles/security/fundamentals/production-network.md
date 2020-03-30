---
title: Azure éles hálózat
description: Ez a cikk az Azure éles hálózat általános leírását tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7c0748e4ff1531649274834cb1e602c228f102e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726692"
---
# <a name="the-azure-production-network"></a>Az Azure éles hálózata
Az Azure éles hálózat felhasználói közé tartoznak a külső ügyfelek, akik hozzáférnek a saját Azure-alkalmazások és a belső Azure-támogatási személyzet, aki kezeli az éles hálózat. Ez a cikk ismerteti a biztonsági hozzáférési módszerek és védelmi mechanizmusok az Azure éles hálózathoz való kapcsolatok létrehozásához.

## <a name="internet-routing-and-fault-tolerance"></a>Internetes útválasztás és hibatűrés
A globálisan redundáns belső és külső Azure domainnév-szolgáltatás (DNS) infrastruktúra, több elsődleges és másodlagos DNS-kiszolgálófürttel kombinálva hibatűrést biztosít. Ezzel egy időben további Azure hálózati biztonsági vezérlők, például a NetScaler, az elosztott szolgáltatásmegtagadási (DDoS) támadások megelőzésére és az Azure DNS-szolgáltatások integritásának védelmére szolgálnak.

Az Azure DNS-kiszolgálók több adatközpont-létesítményben találhatók. Az Azure DNS-implementáció másodlagos és elsődleges DNS-kiszolgálók hierarchiáját tartalmazza az Azure ügyféltartomány-nevek nyilvános feloldásához. A tartománynevek általában egy CloudApp.net-címre oldódnak fel, amely az ügyfél szolgáltatásának virtuális IP-címét (VIP) tördeli. Az Azure-ban egyedülálló, a bérlői fordítás belső dedikált IP-címének (DIP) megfelelő VIRTUÁLIS IP-címet a Microsoft az adott VIRTUÁLIS IP-címért felelős Microsoft terheléselosztók végzik.

Az Azure az Egyesült Államokon belül földrajzilag elosztott Azure-adatközpontokban található, és a legkorszerűbb útválasztási platformokra épül, amelyek robusztus, méretezhető architekturális szabványokat valósítanak meg. Között a figyelemre méltó jellemzői a következők:

- Többprotokollos címkeváltás (MPLS)-alapú forgalomtervezés, amely hatékony kapcsolatkihasználtságot és a szolgáltatás kecses romlását biztosítja, ha kimaradás van.
- Hálózatok vannak megvalósítva a "need plus one" (N +1) redundancia architektúrák vagy jobb.
- Külsőleg az adatközpontokat dedikált, nagy sávszélességű hálózati áramkörök szolgálják ki, amelyek redundánsan több mint 1200 internetszolgáltatóval kötnek össze tulajdonságokat több társviszony-létesítési ponton. Ez a kapcsolat több mint 2000 gigabájt/másodperc (GBps) per másodperces per gbps kapacitást biztosít.

Mivel a Microsoft saját hálózati kapcsolatokkal rendelkezik az adatközpontok között, ezek az attribútumok segítik az Azure-ajánlatot a hálózat 99,9+ %-os rendelkezésre állásának elérésében anélkül, hogy hagyományos külső internetszolgáltatókra lenne szükség.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Csatlakozás az éles hálózathoz és a kapcsolódó tűzfalakhoz
Az Azure hálózati internetes forgalomáramlási szabályzat irányítja a forgalmat az Azure éles hálózat, amely az Egyesült Államok legközelebbi regionális adatközpontjában található. Mivel az Azure éles adatközpontok konzisztens hálózati architektúrát és hardvert tartanak fenn, az alábbi forgalmi folyamat leírása következetesen vonatkozik az összes adatközpontra.

Miután az Azure internetes forgalmat a legközelebbi adatközpontba irányítja, létrejön a kapcsolat a hozzáférési útválasztókkal. Ezek a hozzáférési útválasztók az Azure-csomópontok és az ügyfél által példányosított virtuális gépek közötti forgalom elkülönítésére szolgálnak. A hálózati infrastruktúra-eszközök a hozzáférési és peremhelyeken azok a határpontok, ahol a be- és kilépési szűrőket alkalmazzák. Ezek az útválasztók egy rétegzett hozzáférés-vezérlési listán (ACL) keresztül vannak konfigurálva a nem kívánt hálózati forgalom szűrésére és szükség esetén forgalmi díjkorlátok alkalmazására. Az ACL által engedélyezett forgalmat a terheléselosztók irányítják. A terjesztési útválasztók célja, hogy csak a Microsoft által jóváhagyott IP-címeket engedélyezzenek, hamisítás elleni védelmet biztosítsanak, és ACP-kapcsolatokat hozzanak létre, amelyek ACP-ket használnak.

A külső terheléselosztási eszközök a hozzáférési útválasztók mögött találhatók, hogy az internetes irányítható IP-kről az Azure belső IP-címére hálózati címfordítást (NAT) hajthassanak végre. Az eszközök a csomagokat érvényes éles belső IP-khez és portokhoz is továbbítják, és védelmi mechanizmusként működnek a belső éles hálózati címterület kiteszik.

Alapértelmezés szerint a Microsoft kényszeríti a Hypertext Transfer Protocol Secure (HTTPS) protokollt az ügyfelek webböngészőinek továbbított összes forgalomra vonatkozóan, beleértve a bejelentkezést és az azt követő összes forgalmat. A TLS 1.2-es használata biztonságos alagutat tesz lehetővé a forgalom átáramlásához. A hozzáférés-hozzáférés-hozzáférés-vissza, valamint az alapvető útválasztók biztosítják, hogy a forgalom forrása összhangban legyen a várttal.

Fontos különbség ebben az architektúrában, ha összehasonlítjuk a hagyományos biztonsági architektúrával, az, hogy nincsenek dedikált hardveres tűzfalak, speciális behatolásészlelő vagy -megelőző eszközök vagy egyéb biztonsági eszközök, amelyek általában az Azure éles környezetéhez való csatlakozások előtt várható. Az ügyfelek általában elvárják ezeket a hardveres tűzfaleszközöket az Azure-hálózatban; azonban egyik sem az Azure-ban. Ezek a biztonsági funkciók szinte kizárólag az Azure-környezetet futtató szoftverbe vannak beépítve, hogy robusztus, többrétegű biztonsági mechanizmusokat biztosítsanak, beleértve a tűzfalfunkciókat is. Emellett a határ hatóköre és a kritikus biztonsági eszközök kapcsolódó terjeszkedése könnyebben kezelhető és leltározható, ahogy az az előző ábrán is látható, mivel az Azure-t futtató szoftver kezeli.

## <a name="core-security-and-firewall-features"></a>Alapvető biztonsági és tűzfalfunkciók
Az Azure robusztus szoftverbiztonsági és tűzfalfunkciókat valósít meg különböző szinteken, hogy kikényszerítse azokat a biztonsági funkciókat, amelyek általában egy hagyományos környezetben várhatók az alapvető biztonsági engedélyezési határ védelme érdekében.

### <a name="azure-security-features"></a>Az Azure biztonsági szolgáltatásai
Az Azure gazdagépalapú szoftveres tűzfalakat valósít meg az éles hálózaton belül. Számos alapvető biztonsági és tűzfal-szolgáltatás található az alapvető Azure-környezetben. Ezek a biztonsági funkciók az Azure-környezetben egy mélyreható védelmi stratégiát tükröznek. Az Azure-ban az ügyféladatokat a következő tűzfalak védik:

**Hypervisor tűzfal (csomagszűrő)**: Ez a tűzfal a hipervizorban van megvalósítva, és a hálóvezérlő (FC) ügynöke konfigurálja. Ez a tűzfal védi a virtuális gépen belül futó bérlőt az illetéktelen hozzáféréstől. Alapértelmezés szerint a virtuális gép létrehozásakor az összes forgalom le van tiltva, majd az FC-ügynök szabályokat és kivételeket ad hozzá a szűrőben az engedélyezett forgalom engedélyezéséhez.

Itt két szabálykategória van programozva:

- **Gépkonfigurációs vagy infrastruktúra-szabályok**: Alapértelmezés szerint minden kommunikáció blokkolva van. Vannak kivételek, amelyek lehetővé teszik a virtuális gép számára, hogy DHCP-kommunikációt és DNS-adatokat küldjön és fogadjon, és forgalmat küldjön a "nyilvános" internetes kimenő szolgáltatásoknak az FC-fürt és az operációsrendszer-aktiválási kiszolgáló más virtuális gépei számára. Mivel a virtuális gépek engedélyezett listája a kimenő célok nem tartalmazza az Azure-útválasztó alhálózatok és más Microsoft-tulajdonságok, a szabályok védelmi rétegként működnek számukra.
- **Szerepkör-konfigurációs fájlszabályok**: A bérlői szolgáltatásmodell alapján határozza meg a bejövő ACL-eket. Ha például egy bérlő egy web előtér-ablaka a 80-as porton egy bizonyos virtuális gépen, a 80-as port minden IP-címre meg nyílik. Ha a virtuális gép egy feldolgozói szerepkör fut, a feldolgozói szerepkör csak a virtuális gép ugyanazon a bérlőn belül nyílik meg.

**Natív gazdagép tűzfal:** Az Azure Service Fabric és az Azure Storage egy natív operációs rendszeren fut, amely nem rendelkezik hipervizor, és ezért a Windows tűzfal az előző két szabálykészlettel van konfigurálva.

**Gazdatűzfal**: A gazdatűzfal védi a gazdapartíciót, amely a hipervizort futtatja. A szabályok úgy vannak programozva, hogy csak az FC és a jump boxok beszéljenek a gazdapartícióval egy adott porton. A másik kivétel a DHCP-válaszok és a DNS-válaszok engedélyezése. Az Azure egy gépkonfigurációs fájlt használ, amely tűzfalszabályok sablonját tartalmazza a gazdapartícióhoz. A gazdatűzfal-kivétel is létezik, amely lehetővé teszi a virtuális gépek számára, hogy adott protokollon/portokon keresztül kommunikáljanak az állomásösszetevőkkel, a vezetékes kiszolgálóval és a metaadat-kiszolgálóval.

**Vendég tűzfal**: A vendég operációs rendszer Windows tűzfal-darabja, amelyet az ügyfelek az ügyfelek virtuális gépeken és tárolón konfigurálhatók.

Az Azure-funkciókba beépített további biztonsági funkciók a következők:

- A DIPs-ből származó IP-címekhez rendelt infrastruktúra-összetevők. Az interneten tartózkodó támadók nem tudják kezelni az ilyen címekre irányuló forgalmat, mert nem érik el a Microsoftot. Az internetes átjáróútválasztók szűrik a kizárólag belső címeknek címzett csomagokat, így nem lépnek be az éles hálózatba. A VIP-kre irányuló forgalmat csak a terheléselosztók fogadják el.
- Az összes belső csomóponton megvalósított tűzfalak három elsődleges biztonsági architektúra-szempontot fejtenek ki egy adott forgatókönyvhöz:

   - A tűzfalak a terheléselosztó mögé kerülnek, és bárhonnan elfogadják a csomagokat. Ezek a csomagok külső legkülső expozícióra szolgálnak, és a hagyományos külső tűzfal nyitott portjainak felelnek meg.
   - A tűzfalak csak korlátozott címkészletről fogadnak el csomagokat. Ez a megfontolás része a DDoS-támadások elleni védelmi mélyreható stratégiának. Az ilyen kapcsolatok kriptográfiailag hitelesítve vannak.
   - A tűzfalak csak bizonyos belső csomópontokból érhetők el. Csak a forrás IP-címek számba sorolt listájáról fogadnak el csomagokat, amelyek mindegyike Az Azure-hálózaton belüli DIPs. Például egy támadás a vállalati hálózat közvetlen kérelmeket ezekre a címekre, de a támadások blokkolva lesznek, kivéve, ha a forráscíme a csomag volt az egyik a felsorolt lista az Azure-hálózaton belül.
     - A hozzáférési útválasztó a kerületblokkolja a kimenő csomagokat, amelyek címzettje egy címet, amely az Azure-hálózaton belül a konfigurált statikus útvonalak miatt.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)
