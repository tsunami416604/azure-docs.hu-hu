---
title: Gyakori kérdések – VMware-megoldások CloudSimple
description: Gyakori kérdések az Azure VMware megoldás CloudSimple szerint
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358528"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>VMware megoldást, CloudSimple kapcsolatos gyakori kérdések

Gyakori kérdések és válaszok CloudSimple VMware-megoldások tudnivalók, amelyek segítenek megérteni a szolgáltatás, és hogyan használható a.  A kérdések és válaszok a következő kategóriákba vannak rendezve.

* CloudSimple Service
* Kapcsolat
* Hálózat
* Biztonság
* Compute
* Storage
* VMware
* Az Azure-integráció
  
## <a name="cloudsimple-service"></a>CloudSimple Service

**Mi a VMware megoldás által CloudSimple?**

**VMware megoldást, CloudSimple** alakítja át, és kiterjeszti a VMware számítási feladatainak Azure-beli privát, dedikált felhőkhöz percek alatt. Mi gondoskodunk, az üzembe helyezést, az infrastruktúra kezelése és replikálásával segít a vállalatnak a helyszíni és az Azure között. Mivel az alkalmazások pontosan ugyanazt a helyi Futtatás és az Azure-ban, kihasználhatják a rugalmasság és -szolgáltatások a felhőben, a bonyolultságát, hogy az alkalmazások átszervezése nélkül. CloudSimple csökkenti a teljes tulajdonosi költség, és a egy igény szerinti üzembe helyezési, használatalapú-as--növekedés biztosító felhőalapú fogyasztási modell, és a kapacitás optimalizálása.  Lásd: [Mi az Azure-ban CloudSimple által a VMware megoldás](cloudsimple-vmware-solutions-overview.md) szolgáltatásokat, előnyöket és forgatókönyvekhez.

**Mi az CloudSimple Magánfelhő?**

Üzembe helyez egy privát, dedikált felhőalapú, nagy teljesítményű számítási, tárolási, amely, valamint a hálózati környezetben telepített Microsoft Azure-infrastruktúra (hardver- és adatközpont-terület) az Azure-helyen.  Magánfelhő "-szolgáltatásként" natív VMware platformot biztosít. Minden egyes Magánfelhő VMware-feltételeket, a vCenter-kiszolgáló pontosan egy példányát tartalmazza. A vCenter Server kezeli a több ESXi-csomópont található egy vagy több vSphere fürtök esetén a megfelelő virtuális SAN (vSAN) storage együtt. CloudSimple szolgáltatásként az Azure-előfizetésében több magánfelhő is tartalmazhat.  Magánfelhők kapcsolatos további információkért lásd: [áttekintés Magánfelhő](cloudsimple-private-cloud.md).

**Hol érhető el CloudSimple szolgáltatás?**

CloudSimple az USA keleti RÉGIÓJA és USA nyugati régióban érhető el.

**Hogyan engedélyezhetem CloudSimple-előfizetés?**

A következő Microsoft-ügyfélmenedzserhez fordulnia [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) CloudSimple szolgáltatás az előfizetés engedélyezése érdekében. Adja meg az e-mailben, amelyek esetében szeretne CloudSimple szolgáltatás engedélyezve van az előfizetés-Azonosítóját.  

**Hogyan férhetek hozzá a CloudSimple portálon?**

Az Azure Portalról CloudSimple portál érhető el.  Lásd: [VMware megoldás elérése az Azure Portalról CloudSimple portál](https://docs.azure.cloudsimple.com/access-cloudsimple-portal) részleteivel kapcsolatban CloudSimple portál elérése. 

**Hogyan növelhetem a Magánfelhő kapacitása?**

Vásároljon csomópontok az Azure Portalról, és bontsa ki a Magánfelhő a CloudSimple portálról.  A Magánfelhő bővítheti meglévő vSphere-fürthöz további csomópontok hozzáadásával vagy a vSphere új fürtöt.  Lásd: [bontsa ki a CloudSimple Magánfelhő](https://docs.azure.cloudsimple.com/expand-private-cloud) a cikk a következő eljáráshoz.

**Mi történik a Magánfelhő karbantartás során?**

CloudSimple ütemezett karbantartás előtt nap rendszeres értesítéseket biztosít.  Karbantartás történik, zavart nem okozó úgy, hogy a Magánfelhő rendelkezésre állásának biztosításához.  Karbantartási típusai a következők lehetnek:

1. **CloudSimple infrastruktúra:**  CloudSimple infrastruktúráját úgy tervezték, hogy magas rendelkezésre állású legyen.  A karbantartás során kapcsolat és a Magánfelhő rendelkezésre állását biztosítják a redundáns összetevőket egyesével frissíti egyszerre nem érinti.  A Magánfelhő vCenter, minden virtuális gépek, a Magánfelhő internet kapcsolat és kapcsolatok a helyszíni vagy Azure hozzáférhet.
2. **CloudSimple Portal:** Karbantartás közben néhány funkció CloudSimple portálon nem lehet, elérhető-e vagy le van tiltva.  Karbantartási értesítés mi teheti meg a portál részleteit tartalmazza.

## <a name="connectivity"></a>Kapcsolat

**Milyen CloudSimple régió hálózati kapcsolat lehetőségeim?**

CloudSimple biztosít három különböző kapcsolati lehetőségek a CloudSimple régió hálózathoz való csatlakozáshoz.  Mindhárom együtt is használható.

1. Az ExpressRoute-kapcsolat a helyszíni adatközpont CloudSimple régió hálózathoz – nagy sebességű, alacsony késleltetésű biztonságos privát kapcsolat a helyszíni ExpressRoute-kapcsolatcsoport a CloudSimple ExpressRoute-kapcsolatcsoporttal globális elérhetőségű az adatközponthíd-képzés. Lásd: [CloudSimple ExpressRoute használatával csatlakozhat a helyszíni](https://docs.azure.cloudsimple.com/on-premises-connection) a cikk a kapcsolat beállításához.
2. Az ExpressRoute-kapcsolat az Azure virtuális hálózat CloudSimple régió hálózathoz – nagy sebességű, alacsony késleltetésű biztonságos privát kapcsolatot a virtuális hálózati átjárók használatával CloudSimple ExpressRoute-kapcsolatcsoport az adatközponthíd-képzés az Azure-beli virtuális hálózat.  Lásd: [CloudSimple Magánfelhő környezetében csatlakozhat az Azure virtuális hálózat ExpressRoute-tal](https://docs.azure.cloudsimple.com/azure-expressroute-connection) a cikk a kapcsolat beállításához.
3. Site-to-Site VPN-kapcsolat a helyszíni adatközpontból a CloudSimple régió hálózati – biztonságos virtuális magánhálózati a helyszíni VPN-eszköz, a Magánfelhő CloudSimple régióhoz.  [Állítsa be a VPN-kapcsolat a helyszíni hálózata és a Magánfelhő CloudSimple] lásd a cikk a VPN-kapcsolat beállítása.

**Hogyan létesíthetek kapcsolatot magánfelhőbe?**

A Magánfelhő részleteit megtekintheti a CloudSimple portálon. Szeretne csatlakozni a vcenter-kiszolgáló a Magánfelhő megfelelő, győződjön meg arról hálózati kapcsolatot létrehozott használja a Site-to-Site, pont – hely, vagy az ExpressRoute. Ezután indítsa el a CloudSimple portálon az Azure portálon, majd kattintson a *indítsa el a vSphere Client* a kezdőlapon vagy a Magánfelhő részleteit megjelenítő oldalon.

**Mi az az előnye, hogy az ExpressRoute-kapcsolatcsoportot?**

Az Azure ExpressRoute-kapcsolatcsoport nagy sebességű, alacsony késleltetésű biztonságos kapcsolatot biztosít.  CloudSimple biztosít egy dedikált ExpressRoute-kapcsolatcsoport ügyfelenként régiónként.  A kapcsolatcsoport használatával, biztonságos kapcsolatot a helyszíni és/vagy az Azure-előfizetése is létrehozhat.

**Mik azok a hálózati költségeket és-tárolókról CloudSimple csatlakozni. A kimenő adatforgalmi díjakat és-tárolókról az Azure-bA CloudSimple? Több régió?**

Nem jár semmilyen hálózati kimenő forgalomra.  Az Azure standard díjszabása vonatkozik minden kimenő forgalom a virtuális hálózat vagy a helyi ExpressRoute-kapcsolatcsoport.

## <a name="networking"></a>Hálózat

**Milyen hálózati szolgáltatások saját Magánfelhő érhetők el?**

VLAN-ok (és az alhálózatok) üzembe helyezése, tűzfal-táblákat, és rendeljen nyilvános IP-címeket és képezze le a privát felhőben futó virtuális géphez.  További információkért lásd: [VLAN-OK/alhálózatok áttekintése](cloudsimple-vlans-subnets.md), [tűzfal táblák áttekintése](cloudsimple-firewall-tables.md), és [nyilvános IP-cím áttekintése](cloudsimple-public-ip-address.md) cikkeket.

**Hogyan állíthatok be különböző alhálózatokon saját alkalmazások számára a saját privát felhő?**

A Magánfelhő a VLAN-OK CloudSimple portálból hozhat létre.  Miután létrehozta a VLAN, hozzon létre egy elosztott port csoportot a Magánfelhő vcenter a VLAN használatával, és az elosztott port csoporthoz csatlakoztatott virtuális gépek létrehozása.  Tűzfal tábla engedélyezheti a VLAN/alhálózat és hálózati forgalmának biztonságossá tétele a tűzfalszabályok meghatározásához.

**Milyen tűzfal beállításait a Magánfelhők számára érhetők el?**

Észak – dél- és kelet – Nyugat irányú forgalom szabályokat konfigurálhat.  A szabályok egy tűzfal táblában vannak definiálva.  A tűzfal tábla a privát felhőben VLAN(s) csatolhatók.  Lásd: [tűzfal táblák és -szabályok beállítása Magánfelhők](https://docs.azure.cloudsimple.com/firewall) folyamat ismertető cikket.

**Is hozzárendelhetők nyilvános IP-címek a virtuális gépek a Magánfelhő környezetemet?**

Az CloudSimple Portalon egyszerűen egy új nyilvános IP-címet lefoglalni és társítsa a virtuális gép vagy készülék magánhálózati IP-cím.  Is új tűzfalszabályok létrehozása vagy meglévő tűzfalszabályokat, hogy a forgalom a meghatározott portokon és/vagy adott állítsa be az IP-címek a portálon a alkalmazni. Lásd: [nyilvános IP-címek lefoglalása a Magánfelhő-környezethez](https://docs.azure.cloudsimple.com/public-ips) eljárás beállítására.

## <a name="security"></a>Biztonság

**Milyen a CloudSimple biztonsági lehetőségeim?**

CloudSimple Magánfelhő a következő biztonsági funkciókat biztosít a Magánfelhő-környezet biztonságossá tétele:

1. **Inaktív adatok titkosítása adatok**: A vSAN-tárolót a privát felhőben levő az inaktív adatok titkosíthatók. vSAN támogatja a külső kulcs felügyeleti kiszolgálóra, amely az Azure virtuális hálózat vagy a helyszíni környezetben is telepíthető.  Lásd: [konfigurálja a vsan-hoz a CloudSimple Magánfelhő titkosítást](https://docs.azure.cloudsimple.com/vsan-encryption) további részletekért.
2. **Hálózati biztonság**: Hálózati forgalom átvitelvezérlés Azure blobból vagy az internetről, a helyszíni és a Magánfelhő tűzfalszabályokkal alhálózatain belül a privát felhő.
3. **Biztonságos, privát kapcsolat**: Biztonságos privát kapcsolat a helyszíni hálózat és az Azure-előfizetése között.

## <a name="compute"></a>Compute

**Milyen típusú gazdagépek érhetők el?**

CloudSimple két gazdagép típust kínál:

* **CS28 csomópont:** CPU:2 2.2 GHz-es teljes 28 mag, 48 x HT.  RAM: 256 GB.  Storage: 1600 GB-os NVMe gyorsítótár, 5760 GB adat (teljes mértékben Flash). Hálózat: 2x25Gbe NIC
* **CS36 csomópont:** CPU 2 x 2.3 GHz-es összesen 36 mag, 72 HT.  RAM: 512 GB.  Storage: 3200 GB NVMe-gyorsítótár 11,520 GB adat (teljes mértékben Flash).  Hálózat: 2x25Gbe NIC

**Hogyan kezelje a szüntethessük a hardverhibák?**

Minden CloudSimple infrastruktúra folyamatosan figyeli a CloudSimple platform és a szolgáltatás üzemeltetői csapatokat.  Hardverhiba észlelése esetén egy új csomópontra kerül a Magánfelhő és a sikertelen csomópont eltávolítása a Magánfelhő magas rendelkezésre állásának biztosítása.

## <a name="storage"></a>Storage

**A Magánfelhő tároló milyen típusú támogatott?**

CloudSimple kínál **teljes mértékben flash VMware vSAN tárolási** minden privát felhő.  Minden egyes vSphere jön létre a saját vSAN-adattárhoz.  Lásd: [privát felhő VMware összetevők – vsan-hoz, storage](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage) további részleteivel.

**Az adatok támogatott titkosítási?**
Igen.  Állíthat be a vSAN-tárolót a privát felhő, amely üzembe helyezett helyszíni kulcskezelő kiszolgáló (KMS) használata vagy az Azure-ban a vSAN-on tárolt adatok titkosításához

**Hogyan kezeli a hibás lemezeket?**

A privát felhő minden hardverösszetevő folyamatosan CloudSimple figyelési figyeli.  Ha semmilyen lemezhiba észlel, vagy a lemezzel (a heurisztika alapján) sikertelen azonosítottak, egy új csomópont automatikusan hozzáadódik a Magánfelhő.  A csomópont hibás lemezt távolítja el a Magánfelhő.

## <a name="vmware"></a>VMware

**Hogyan hajthatom végre nagy méretű feltöltése/migrate alkalmazásait és adatait a helyszíni?**

CloudSimple natív VMware vSphere-megoldást kínál.  Minden olyan eszköz, amellyel tömeges adatáttelepítés CloudSimple Magánfelhő is használható.  Az elérhető lehetőségek a következők:

1. VMware HCX adatok tömeges az áttelepítéshez.
2. Ritkán használt adatok CloudSimple a helyszínről a Storage vMotion használatával áttelepítése.

**Minden olyan VMware-eszközöket is telepíthet?**

CloudSimple natív VMware vSphere-megoldást kínál.  Minden olyan eszköz, amellyel vSphere környezetben a helyszíni CloudSimple lehet használni.  CloudSimple Bring-Your-saját licenc használata (BYOL) modell támogatja a VMware-eszközök telepítése.

**Hogyan frissítések és verziófrissítések felügyelt?**

CloudSimple kezeli, és frissíti a Magánfelhő összes infrastruktúra-összetevőihez zavart nem okozó gördülékennyé.  Minden olyan VMware vagy infrastruktúra-szolgáltató által kiadott frissítés vagy javítást lesz ütemezve frissítés, amint azt a CloudSimple minősíteni.

CloudSimple frissítések, a Magánfelhő a telepített alkalmazások nem hajt végre.

## <a name="azure-integration"></a>Az Azure-integráció

**Milyen Azure-szolgáltatások támogatottak?**

CloudSimple biztosít az előfizetéshez az Azure ExpressRoute-kapcsolat az Azure-ban.  Az előfizetésében futó szolgáltatások a Magánfelhő hálózati kapcsolattal rendelkezik, és csatlakozhat a Magánfelhő.  Példák:

1. **Az Azure Active Directory** a CloudSimple vcenter-identitás forrásként
2. **Az Azure storage** biztonsági mentések, képek és egyéb adatait a Magánfelhő tárolására
3. **Hibrid alkalmazások** -alkalmazásarchitektúra nyilvános és magánfelhőkben is létrehozhat.  Például az Azure-ban elérhető alkalmazás- és adatbázis-kiszolgálók CloudSimple privát felhő webservers is létrehozhat.
4. **Az Azure monitor** és **az Azure security center** – VMware-en futó munkaterhelések használhatják, ezek a naplózás, teljesítmény-mérőszámok és biztonsági felügyeleti.

**Hogyan rendelhetek VMware a bérlő Azure-bA?**

CloudSimple teszi lehetővé egyedi a Magánfelhő VMware virtuális gépek kezelése az Azure Portalról.  VCenter erőforráskészlet (konfigurálni kívánt erőforrás-korlátozások) a globális rendszergazda is rendelhető hozzá az előfizetés.  

**Milyen licencelési kedvezmények jelenik meg az Azure-ral?**

CloudSimple is kihasználhatja az Azure Hybrid Benefit használati és legfeljebb 90 %-os megtakarítást a licenceket a Microsoft Licenses, a teljes bekerülési Költséget, más felhőkben képest csökkenti a befektetési megőrizve. Biztonsági frissítések hálózatfigyelést emellett a Windows Server 2008 és a Microsoft SQL Server 2008.  Tartsa a költségeket a használata a saját licenc használata (BYOL) alacsony például a Veeam, a Zerto és egyéb gyakori alkalmazások a felhőbe.  