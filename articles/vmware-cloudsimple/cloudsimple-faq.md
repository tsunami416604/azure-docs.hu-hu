---
title: Gyakori kérdések – VMware-megoldások CloudSimple
description: Gyakori kérdések az Azure VMware megoldás CloudSimple szerint
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e187a4a75a27e206a632388f1e20a94da032eb08
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595349"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>VMware megoldást, CloudSimple kapcsolatos gyakori kérdések

Gyakori kérdések és válaszok Azure VMware-megoldás által CloudSimple, amelyek segítenek megérteni a szolgáltatás, és hogyan használható a. A kérdések és válaszok a következő kategóriákban elrendezésére:

* CloudSimple service
* Kapcsolat
* Hálózat
* Biztonság
* Compute
* Storage
* VMware
* Az Azure-integráció
 
## <a name="cloudsimple-service"></a>CloudSimple service

**Mi az Azure VMware megoldás által CloudSimple?**

Azure VMware-megoldás által CloudSimple alakítja át, és a VMware számítási feladatainak Azure-beli privát, dedikált felhőkhöz kiterjesztheti percek alatt. A megoldás látja el az infrastruktúrát felügyeli és koordinálja a helyszíni és az Azure között. Mivel az alkalmazások pontosan ugyanazt a helyi Futtatás és az Azure-ban, kihasználhatják a rugalmasság és -szolgáltatások a felhőben, a bonyolultságát, hogy az alkalmazások átszervezése nélkül. CloudSimple csökkenti a teljes tulajdonosi költség, és a egy igény szerinti üzembe helyezési, használatalapú-as--növekedés biztosító felhőalapú fogyasztási modell, és a kapacitás optimalizálása. Szolgáltatásokat, előnyöket és forgatókönyvek, lásd: [Mi az Azure VMware megoldás által CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Mit jelent a magánfelhő CloudSimple?**

Üzembe helyez egy dedikált, privát felhő, amely egy nagy teljesítményű számítási, tárolási és hálózati környezetet az Azure-helyen telepített Microsoft Azure-infrastruktúra (hardver- és adatközpont-terület) áll. A magánfelhő natív VMware platformot biztosít szolgáltatásként. A VMware-feltételeket az egyes magánfelhőkhöz pontosan egy példányát a vCenter-kiszolgáló tartalmazza. A vCenter Server kezeli a több ESXi csomópont egy vagy több vSphere fürtökben, és a megfelelő vSAN-tároló tartalmazza. CloudSimple szolgáltatásként az Azure-előfizetésében több magánfelhő is tartalmazhat. Magánfelhők kapcsolatos további információkért lásd: [áttekintés magánfelhő](cloudsimple-private-cloud.md).

**Hol érhető el a CloudSimple szolgáltatás?**

CloudSimple az USA keleti RÉGIÓJA és USA nyugati régióban érhető el.

**Hogyan engedélyezhetem CloudSimple-előfizetés?**

Forduljon a Microsoft-ügyfélmenedzserhez, [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) CloudSimple szolgáltatás az előfizetés engedélyezése érdekében. Adja meg az e-mailben, amelyek esetében szeretne CloudSimple szolgáltatás engedélyezve van az előfizetés-Azonosítóját. 

**Hogyan férhetek hozzá a CloudSimple portálon?**

A CloudSimple portálon az Azure Portalról érhető el. Hogyan CloudSimple portál eléréséhez további információkért lásd: [férhetnek hozzá a VMware megoldás, az Azure Portalról CloudSimple portál](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Hogyan növelhetem a magánfelhő kapacitása?**

Az Azure Portalról csomópontok kiépítésére, és bontsa ki a magánfelhőben, a CloudSimple portálról. A magánfelhő bővítheti meglévő vSphere-fürtre csomópontok hozzáadásával vagy egy új vSphere-fürtöt. Az eljárást a további információkért lásd: [bontsa ki a magánfelhő CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Mi történik a magánfelhő karbantartás során?**

CloudSimple biztosít az időszakos értesítések nappal az ütemezett karbantartás előtt. Karbantartási leállás úgy, hogy a magánfelhő rendelkezésre állásának biztosításához történik. Karbantartási típusai a következők lehetnek:

- **CloudSimple infrastruktúra**: A CloudSimple infrastruktúráját úgy tervezték, hogy magas rendelkezésre állású legyen. Karbantartás közben kapcsolatot és a saját magánfelhő-alapú rendelkezésre állását biztosítják a redundáns összetevőket egyesével frissíti egyszerre nem érinti. A magánfelhő vCenter, minden virtuális gépek, a saját magánfelhő-alapú internetkapcsolat és kapcsolatok a helyszíni vagy Azure elérhető lesz.
- **CloudSimple portál**: Karbantartás közben néhány szolgáltatása a CloudSimple portálon nem érhető el, vagy előfordulhat, hogy le van tiltva. A karbantartási értesítés tartalmaz információkat, mi teheti meg a portálon.

## <a name="connectivity"></a>Kapcsolat

**Mik azok a saját kapcsolati lehetőségek CloudSimple régió hálózatomhoz?**

CloudSimple biztosít három különböző kapcsolati lehetőségek a CloudSimple régió hálózathoz való csatlakozáshoz. Mindhárom együtt is használható:

- Az Azure ExpressRoute-kapcsolat a helyszíni adatközpontból CloudSimple régió hálózathoz: Nagy sebességű közel valós idejű biztonságos privát kapcsolat, amely áthidalja a helyszíni ExpressRoute-kapcsolatcsoport az CloudSimple ExpressRoute-kapcsolatcsoport a globális elérhetőségű használatával. A kapcsolat beállításához, tekintse meg a [CloudSimple ExpressRoute használatával csatlakozhat a helyszíni](https://docs.azure.cloudsimple.com/on-premises-connection).
- Az ExpressRoute az Azure virtuális hálózat a hálózattal létesített kapcsolat CloudSimple régió: Nagy sebességű, alacsony késleltetésű biztonságos privát kapcsolat, amely áthidalja a virtuális hálózaton az CloudSimple ExpressRoute-kapcsolatcsoport Azure-beli virtuális hálózati átjárók segítségével. A kapcsolat beállításához, tekintse meg a [CloudSimple magánfelhő környezetében csatlakozhat az Azure virtuális hálózat ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Site-to-site VPN-kapcsolatot a helyszíni adatközpontját a CloudSimple régió hálózathoz: Biztonságos virtuális magánhálózati, a helyszíni VPN-eszköz a CloudSimple magánfelhő régióhoz. A kapcsolat beállításához, tekintse meg a [állítsa be a VPN-kapcsolat a helyszíni hálózata és a magánfelhő CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Hogyan létesíthetek kapcsolatot a magánfelhő számára?**

A CloudSimple portálon megtekintheti a részleteket a magánfelhő. Csatlakozik a vcenter-kiszolgáló, amely megfelel a magánfelhőben, győződjön meg róla, hogy a hálózati kapcsolat site-to-site, pont – hely vagy az ExpressRoute használatával hozták létre. Ezután indítsa el a CloudSimple portálon az Azure Portalról. Válassza ki **indítsa el a vSphere Client** a kezdőlapon vagy a magánfelhő részleteit megjelenítő oldalon.

**Mi az az előnye, hogy egy ExpressRoute-kapcsolatcsoportot?**

Az Azure ExpressRoute-Kapcsolatcsoportok nagy sebességű, alacsony késleltetésű biztonságos kapcsolatot biztosít. CloudSimple biztosít egy dedikált ExpressRoute-kapcsolatcsoport ügyfelenként régiónként. A kapcsolatcsoport használatával, biztonságos kapcsolatot a helyszíni és az Azure-előfizetése is létrehozhat.

**Mik azok a hálózati költségeket és onnan CloudSimple csatlakozni? Vannak-e a kimenő adatforgalmi díjakat és az Azure-bA CloudSimple? Vannak-e a kimenő adatforgalmi díjakat régióban?**

Nem jár költséggel a kimenő hálózati forgalomért. A virtuális hálózat vagy a helyi ExpressRoute-kapcsolatcsoport Azure standard díjszabása alapján minden kimenő forgalom a alkalmazni.

## <a name="networking"></a>Hálózat

**Milyen hálózati szolgáltatások saját magánfelhő érhetők el?**

Telepíthet virtuális helyi hálózatok és alhálózatok, illetve tűzfal táblák. Rendeljen nyilvános IP-címeket, és a térkép-magánfelhőben futó virtuális géphez. További információkért lásd: [virtuális helyi hálózatok és alhálózatok áttekintése](cloudsimple-vlans-subnets.md), [tűzfal táblák áttekintése](cloudsimple-firewall-tables.md), és [nyilvános IP-cím áttekintése](cloudsimple-public-ip-address.md).

**Hogyan állíthatok be különböző alhálózatokon saját alkalmazások számára a saját privát felhőben?**

A magánfelhőben VLAN-okat a CloudSimple portálon hozhat létre. Miután létrehozta a VLAN, hozzon létre egy elosztott port csoportot a magánfelhő vcenter a VLAN használatával, és az elosztott port csoporthoz csatlakoztatott virtuális gépek létrehozása. Tűzfal tábla engedélyezése a VLAN-t vagy alhálózat számára, és a hálózati forgalmának biztonságossá tétele a tűzfalszabályok meghatározásához.

**A privát felhők milyen tűzfal beállításai érhetők el?**

Észak – dél- és kelet – Nyugat irányú forgalom szabályokat konfigurálhat. A szabályok egy tűzfal táblában vannak definiálva. A tűzfal tábla VLAN-okat a magánfelhőben csatolhatók. A telepítő eljárás: [tűzfal táblák és a magánfelhők szabályok beállítása](https://docs.azure.cloudsimple.com/firewall).

**Is hozzárendelhetők nyilvános IP-címek a virtuális gépek saját magánfelhő-alapú környezetben?**

Az CloudSimple Portalon egyszerűen egy új nyilvános IP-címet lefoglalni és társítsa a virtuális gép vagy készülék magánhálózati IP-cím. Emellett új tűzfalszabályok létrehozása vagy meglévő tűzfalszabályok engedélyezése a forgalmat az adott portokhoz és egyedi IP-címek a portálon beállítja a alkalmazni. A telepítő eljárás: [nyilvános IP-címeket lefoglalni a magánfelhő-környezethez az](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Biztonság

**Milyen a CloudSimple biztonsági lehetőségeim?**

CloudSimple magánfelhő a következő biztonsági funkciókat biztosít a magánfelhő-környezet biztonságossá tétele:

- **Adatok inaktív adatok titkosítása:** Titkosíthatja az inaktív vSAN tárolás a privát felhőben lévő adatok. vsan-hoz egy külső kulcs felügyeleti kiszolgálóra, amely is üzembe helyezhetők az Azure virtuális hálózat vagy a helyszíni környezetben támogatja. További információkért lásd: [konfigurálja a vsan-hoz a CloudSimple magánfelhőben titkosítást](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Hálózati biztonság:** Hálózati forgalom átvitelvezérlés a kezdő és a saját magánfelhő-alapú az internetről, a helyszínen, és a magánfelhő tűzfalszabályok használatával alhálózatok belül.
- **Biztonságos, privát kapcsolat:** Biztonságos, privát kapcsolat a helyszíni hálózat és az Azure-előfizetés között.

## <a name="compute"></a>Compute

**Milyen típusú gazdagépek érhetők el?**

CloudSimple két gazdagép típust kínál:

* **CS28 csomópont**: CPU:2 2.2 GHz-es teljes 28 mag, 48 x HT. RAM: 256 GB. Storage: 1600 GB-os NVMe gyorsítótár, 5760 GB méretű adatok (teljes mértékben Flash). Hálózat: 2x25Gbe NIC.
* **CS36 csomópont**: CPU 2 x 2.3 GHz-es összesen 36 mag, 72 HT. RAM: 512 GB. Storage: 3200 GB-os NVMe gyorsítótár 11,520 GB méretű adatok (teljes mértékben Flash). Hálózat: 2x25Gbe NIC.

**Hogyan kezelje a hardveres hibák esetén?**

Minden CloudSimple infrastruktúra folyamatosan figyeli a CloudSimple platform és a szolgáltatás üzemeltetői csapatokat. Hardverhiba észlelése esetén egy új csomópontot ad a magánfelhő számára. A magánfelhő a magas rendelkezésre állás biztosítása érdekében el a sikertelen csomópontot.

## <a name="storage"></a>Storage

**Milyen típusú tárolás a privát felhő támogatott?**

CloudSimple kínál **teljes mértékben flash VMware vSAN tárolási** minden privát felhő. Minden egyes vSphere jön létre a saját vSAN-adattárhoz. További információkért lásd: [magánfelhő VMware-összetevők – vsan-hoz, storage](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Az adatok támogatott titkosítási?**
Igen. Állíthat be a vSAN-tárolót egy kulcskezelés kiszolgálói (KMS), amely üzembe helyezett helyszíni használata a magánfelhőben vagy Azure-beli vsan-hoz a tárolt adatok titkosításához.

**Hogyan kezeli a hibás lemezeket?**

A magánfelhő minden hardverösszetevő folyamatosan CloudSimple figyelési figyeli. Észleli a lemezhiba vagy lemez sikertelen heurisztika alapján azonosítja, ha új csomópontot automatikusan hozzáadódik a magánfelhő számára. A csomópont egy hibás lemezt távolítja el a magánfelhő.

## <a name="vmware"></a>VMware

**Hogyan hajthatom végre nagy méretű feltöltése és az alkalmazások és adatok áttelepítését a helyszíni?**

CloudSimple natív VMware vSphere megoldást kínál. Minden olyan eszköz, amellyel tömeges adatáttelepítés CloudSimple magánfelhőbe is használható. Az elérhető lehetőségek a következők:

- VMware HCX adatok tömeges az áttelepítéshez.
- Offline áttelepítés adatok CloudSimple a helyszínről a Storage vMotion használatával.

**Minden olyan VMware-eszközöket is telepíthet?**

CloudSimple natív VMware vSphere megoldást kínál. Bármely eszköz egy vSphere-környezet helyszíni CloudSimple lehet használni. CloudSimple bring-your-saját licenc (használata BYOL) modell támogatja a VMware-eszközök telepítése.

**Hogyan frissítések és verziófrissítések felügyelt?**

CloudSimple kezeli, és frissíti a magánfelhő infrastrukturális összetevők leállás gördülékennyé. Minden olyan VMware vagy infrastruktúra-szolgáltató által kiadott frissítés vagy javítást ütemezett frissítést, amint azt a CloudSimple minősíteni.

A magánfelhőbe telepített alkalmazások frissítések CloudSimple végre.

## <a name="azure-integration"></a>Az Azure-integráció

**Milyen Azure-szolgáltatások támogatottak?**

CloudSimple biztosít az előfizetéshez az Azure ExpressRoute-kapcsolat az Azure-ban. Az előfizetésében futó minden olyan szolgáltatások hálózati kapcsolattal rendelkezik a magánfelhő számára, és csatlakozhat a magánfelhő. Példák:

- **Azure Active Directory**: Az Azure Active Directory használata a CloudSimple vCenter-identitás forrásaként.
- **Az Azure Storage**: Tárolás biztonsági mentések, képek és egyéb adatait a magánfelhőben használni.
- **Hibrid alkalmazások**: Alkalmazásarchitektúra nyilvános és magánfelhőkben is létrehozhat. Például létrehozhat webkiszolgálók az Azure-ban, hogy hozzáférési alkalmazás- és adatbázis-kiszolgálók a CloudSimple magánfelhőbe.
- **Az Azure Monitor** és **az Azure Security Center**: Naplózás, teljesítmény-mérőszámok és biztonsági felügyeleti VMware-en futó számítási feladatok figyelése és a Security Center használható.

**Hogyan rendelhetek VMware a bérlő Azure-bA?**

CloudSimple teszi lehetővé egyedi magánfelhő VMware virtuális gépek kezelése az Azure Portalról. VCenter erőforráskészlet konfigurálni szeretné, hogy a globális rendszergazda az előfizetés megfeleltethetők erőforrás-korlátozások. 

**Milyen licencelési kedvezmények jelenik meg az Azure-ral?**

CloudSimple az Azure Hybrid Benefit előnyeit, és a licencek eszközölt befektetéseit a Microsoft licencek, valamint csökkentheti a teljes birtoklási más felhőkben képest akár 90 százalékkal mentése. Biztonsági frissítések a Windows Server 2008 és a Microsoft SQL Server 2008 első terjeszteni. A költségek alacsony BYOL-tartsa a felhőbe, mint a Veeam, a Zerto és egyéb gyakori alkalmazások. 
