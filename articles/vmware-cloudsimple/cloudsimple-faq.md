---
title: Gyakori kérdések – VMware-megoldás CloudSimple szerint
description: Gyakori kérdések az Azure VMware-megoldásról a CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816225"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Gyakori kérdések a VMware-megoldásról a CloudSimple

Gyakori kérdések és válaszok az Azure VMware megoldásról a CloudSimple, amely segít a szolgáltatás megismerésében és használatában. A kérdések és válaszok a következő kategóriákba vannak rendezve:

* CloudSimple szolgáltatás
* Kapcsolat
* Hálózat
* Biztonság
* Compute
* Storage
* VMware
* Azure-integráció
 
## <a name="cloudsimple-service"></a>CloudSimple szolgáltatás

**Mi az az Azure VMware-megoldás a CloudSimple?**

A CloudSimple által nyújtott Azure VMware-megoldás átalakítja és kibővíti a VMware számítási feladatokat az Azure-ban, percek alatt, dedikált felhőben. A megoldás kiépíti, kezeli az infrastruktúrát, és összehangolja a helyszíni és az Azure közötti számítási feladatokat. Mivel az alkalmazásai pontosan ugyanúgy futnak a helyszínen és az Azure-ban, a felhő rugalmasságát és szolgáltatásait is kihasználhatja az alkalmazások újratervezésének bonyolultsága nélkül. A CloudSimple csökkenti a teljes tulajdonlási költségeket egy Felhőbeli fogyasztási modellel, amely igény szerinti kiosztást, az elszámolást és a kapacitás optimalizálását biztosítja. A funkciók, az előnyök és a forgatókönyvek esetében tekintse meg a [Mi az az Azure VMware Solution by CloudSimple?](cloudsimple-vmware-solutions-overview.md)című témakört.

**Mi a CloudSimple privát felhő?**

Olyan privát, dedikált felhőt épít ki, amely egy nagy teljesítményű számítási, tárolási és hálózatkezelési környezetből áll, amelyet az Azure-helyek Microsoft Azure infrastruktúráján (hardver-és adatközpont-terület) helyeznek üzembe. A privát felhő natív VMware platformot biztosít szolgáltatásként. A VMware kifejezésekben minden egyes privát felhő pontosan egy példányt tartalmaz a vCenter Server. A vCenter Server egy vagy több vSphere-fürtben található több ESXi-csomópontot felügyel, a megfelelő vSAN-tárolóval együtt. A CloudSimple szolgáltatás több privát felhőt is tartalmazhat az Azure-előfizetésében. További információ a privát felhőkről: a [privát felhő áttekintése](cloudsimple-private-cloud.md).

**Hol érhető el a CloudSimple szolgáltatás?**

A CloudSimple az USA keleti régiójában és az USA nyugati régiójában érhető el.

**Hogyan engedélyezni a CloudSimple-előfizetést?**

A CloudSimple szolgáltatás előfizetésének engedélyezéséhez lépjen kapcsolatba Microsoft-fiók képviselőjével [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) . Adja meg az előfizetés-AZONOSÍTÓját az e-mailben, amelyhez engedélyezni szeretné a CloudSimple szolgáltatást. 

**Hogyan hozzáférni a CloudSimple-portálhoz?**

A CloudSimple-portálhoz a Azure Portal férhet hozzá. További információ a CloudSimple-portál eléréséről: [hozzáférés a VMware-megoldáshoz a CloudSimple-portálon a Azure Portal](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Hogyan a saját felhő kapacitásának növelését?**

A csomópontokat kiépítheti a Azure Portalból, és kiterjesztheti saját felhőjét a CloudSimple portálról. Kiterjesztheti saját felhőjét, ha csomópontokat ad hozzá egy meglévő vSphere-fürthöz, vagy új vSphere-fürtöt hoz létre. Az eljárással kapcsolatos információkért lásd: [CloudSimple privát felhő](https://docs.azure.cloudsimple.com/expand-private-cloud)kibontása.

**Mi történik a saját felhővel a karbantartás során?**

A CloudSimple rendszeres értesítési napokat biztosít az ütemezett karbantartás előtt. A karbantartást nem zavaró módon végezheti el a privát felhő rendelkezésre állásának biztosítása érdekében. A karbantartás a következő típusú lehet:

- **CloudSimple-infrastruktúra**: A CloudSimple-infrastruktúra kiválóan elérhető. A karbantartás, a kapcsolat és a saját felhő elérhetőségének biztosítása érdekében a redundáns összetevők frissítése semmilyen hatással nincs. Hozzáférhet a saját felhőalapú vCenter, az összes virtuális géphez, a saját Felhőbeli internetkapcsolathoz, valamint a helyszíni vagy az Azure-hoz való csatlakozáshoz.
- **CloudSimple-portál**: A karbantartás során előfordulhat, hogy a CloudSimple-portál egyes funkciói nem érhetők el, vagy le vannak tiltva. A karbantartási értesítés a portálon elvégzendő tudnivalókról tartalmaz információkat.

## <a name="connectivity"></a>Kapcsolat

**Milyen csatlakozási lehetőségeim vannak a CloudSimple-régióm hálózatához?**

A CloudSimple három különböző csatlakozási lehetőséget biztosít a CloudSimple-régió hálózatához való csatlakozáshoz. Mindhárom lehetőség egyszerre használható:

- Azure ExpressRoute-kapcsolódás a helyszíni adatközpontból a CloudSimple region Networkbe: Nagy sebességű, kis késleltetésű, biztonságos magánhálózati kapcsolat, amely a helyszíni ExpressRoute áramkört a CloudSimple ExpressRoute-áramkörrel a Global Reach használatával áthidalja. A kapcsolat beállításához lásd: Kapcsolódás a helyszínről a [CloudSimple a ExpressRoute használatával](https://docs.azure.cloudsimple.com/on-premises-connection).
- ExpressRoute az Azure-beli virtuális hálózatról a CloudSimple-régió hálózatára: Nagy sebességű, kis késleltetésű, biztonságos magánhálózati kapcsolat, amely virtuális hálózati átjárók használatával az Azure-ban CloudSimple ExpressRoute-áramkört használó virtuális hálózatát használja. A kapcsolat beállításához lásd: [a CloudSimple saját felhőalapú környezetének csatlakoztatása az Azure Virtual networkhez a ExpressRoute használatával](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Helyek közötti VPN-kapcsolat a helyszíni adatközpontból a CloudSimple-régió hálózatára: Biztonságos virtuális magánhálózat a helyszíni VPN-eszközről a CloudSimple saját Felhőbeli régiójába. A kapcsolat beállításához tekintse [meg a VPN-kapcsolat beállítása a helyszíni hálózat és a CloudSimple privát felhő között](https://docs.azure.cloudsimple.com/set-up-vpn)című témakört.

**Hogyan csatlakozni a privát felhőhöz?**

A privát felhő részleteit a CloudSimple-portálon tekintheti meg. A privát felhőnek megfelelő vCenter való kapcsolódáshoz győződjön meg arról, hogy a helyek közötti, pont – hely vagy ExpressRoute használatával létesített hálózati kapcsolat. Ezután indítsa el az CloudSimple-portált a Azure Portal. Jelölje be a **vSphere-ügyfél elindítása** a kezdőlapon vagy a privát felhő részletei lapon.

**Mi a ExpressRoute-áramkör előnye?**

Az Azure ExpressRoute áramkör nagy sebességű, kis késleltetésű biztonságos kapcsolatot biztosít. A CloudSimple egy régiónként dedikált ExpressRoute-áramkört biztosít az ügyfelek számára. Ezen áramkör használatával biztonságos kapcsolatot hozhat létre a helyszíni és az Azure-előfizetésből.

**Milyen hálózati költségekkel kell csatlakozni a CloudSimple-hoz és a-hoz? Van-e kimenő forgalom a CloudSimple és az Azure között? Vannak-e kimenő költségek a régiók között?**

A hálózati kimenő forgalomért nem számítunk fel díjat. Az Azure standard díjszabása a virtuális hálózatról vagy egy helyszíni ExpressRoute-áramkörről érkező kimenő forgalomra vonatkozik.

## <a name="networking"></a>Hálózat

**Milyen hálózatkezelési funkciók érhetők el a saját felhőhöz?**

A VLAN-ok és az alhálózatok és a tűzfalak is kioszthatók. Nyilvános IP-címeket rendelhet hozzá, és leképezheti a saját felhőben futó virtuális gépeket. További információ: a [VLAN-ok és](cloudsimple-vlans-subnets.md)az alhálózatok áttekintése, a [tűzfalak áttekintése](cloudsimple-firewall-tables.md)és a [nyilvános IP-címek áttekintése](cloudsimple-public-ip-address.md).

**Hogyan különböző alhálózatokat beállítani az alkalmazásaim számára a saját felhőben?**

A CloudSimple-portálról VLAN-okat hozhat létre a saját felhőben. Miután létrehozta a VLAN-t, létrehozhat egy elosztott portot a saját Felhőbeli vCenter a VLAN használatával, és létrehozhatja az elosztott port csoporthoz csatlakoztatott virtuális gépeket. Engedélyezheti a virtuális helyi hálózat vagy alhálózat tűzfalának táblázatát, és megadhatja a tűzfal szabályait a hálózati forgalom biztonságossá tételéhez.

**Milyen tűzfal-beállítások érhetők el a privát felhőkhöz?**

Az észak-déli és a kelet-nyugati forgalomra vonatkozó szabályokat is konfigurálhat. A szabályok definiálva vannak egy tűzfal táblában. A tűzfal tábla a privát felhőben lévő VLAN-okra is csatlakoztatható. A telepítési eljárással kapcsolatban lásd: a [tűzfalak és a privát felhők szabályainak beállítása](https://docs.azure.cloudsimple.com/firewall).

**Rendelhetek nyilvános IP-címeket a saját felhőalapú környezetében lévő virtuális gépekhez?**

A CloudSimple-portálon egyszerűen lefoglalhat egy új nyilvános IP-címet, és társíthatja azt a virtuális gép vagy berendezés magánhálózati IP-címével. Emellett új tűzfalszabályok is létrehozhatók, vagy a meglévő tűzfalszabályok alkalmazása lehetővé teszi, hogy a portálon az adott portokon és a megadott IP-címekről érkező adatforgalmat engedélyezzék. A telepítési eljáráshoz lásd: [nyilvános IP-címek lefoglalása egy privát felhőalapú környezethez](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Biztonság

**Mik a CloudSimple biztonsági beállításai?**

A CloudSimple privát felhő a következő biztonsági funkciókat biztosítja a saját felhőalapú környezetének biztonságossá tételéhez:

- **Inaktív adatok titkosítása:** Az inaktív adatok titkosítva tárolják a vSAN-tárolót a saját felhőben. a vSAN támogatja a külső kulcskezelő kiszolgálót, amely az Azure Virtual Networkben vagy a helyszíni környezetben is üzembe helyezhető. További információ: vSAN- [titkosítás konfigurálása saját CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption)-felhőhöz.
- **Hálózati biztonság:** Szabályozhatja a hálózati forgalmat a és a rendszerből a saját felhőbe az internetről, a helyszíni környezetből és a saját felhő alhálózatán belül a tűzfalszabályok használatával.
- **Biztonságos, magánhálózati kapcsolatok:** Biztonságos, magánhálózati kapcsolat a helyszíni hálózat és az Azure-előfizetés között.

## <a name="compute"></a>Compute

**Milyen típusú gazdagépek érhetők el?**

A CloudSimple két gazdagép típusát kínálja:

* **CS28 csomópont**: CPU: 2x 2,2 GHz, összesen 28 mag, 48 HT. RAM: 256 GB. Storage: 1600 – GB NVMe cache, 5760-GB adat (all-Flash). Hálózati 2x25Gbe NIC.
* **CS36 csomópont**: CPU 2x 2,3 GHz, összesen 36 mag, 72 HT. RAM: 512 GB. Storage: 3200 – GB NVMe cache 11 520 – GB adat (all-Flash). Hálózati 2x25Gbe NIC.

**Hogyan történik a hardveres hibák kezelése?**

Az összes CloudSimple-infrastruktúrát folyamatosan figyeli a CloudSimple platform és a hozzá tartozó szolgáltatási műveleti csapatok. Ha hardverhiba észlelhető, a rendszer új csomópontot ad hozzá a saját felhőhöz. A rendszer eltávolítja a meghibásodott csomópontot, hogy biztosítsa a saját felhő magas rendelkezésre állását.

## <a name="storage"></a>Storage

**Milyen típusú tárterület támogatott a privát felhőben?**

A CloudSimple minden privát felhőben biztosít **minden Flash VMware vSAN-tárolót** . Minden vSphere a saját vSAN adattárával jön létre. További információ: [Private Cloud VMware Components-vSAN Storage](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Támogatott az adattitkosítás?**
Igen. A vSAN-tárolót a saját felhőben is beállíthatja egy kulcskezelő kiszolgáló (KMS) használatára, amelyet a helyszínen vagy az Azure-ban telepítenek a vSAN-on tárolt adatmennyiség titkosításához.

**Hogyan történik a hibás lemezek kezelése?**

A CloudSimple-figyelés folyamatosan figyeli a saját felhő összes hardveres összetevőjét. Ha a rendszer hibát észlel, vagy ha a lemez hibásként van azonosítva a heurisztikus művelet alapján, a rendszer automatikusan hozzáadja az új csomópontot a privát felhőhöz. A rendszer eltávolítja a meghibásodott vagy hibás lemezt tartalmazó csomópontot a privát felhőből.

## <a name="vmware"></a>VMware

**Hogyan a helyszíni alkalmazások és adatok nagy méretű feltöltését és áttelepítését végzik?**

A CloudSimple natív VMware vSphere megoldást biztosít. A tömeges adatáttelepítéshez használt bármely eszköz használható a CloudSimple privát felhővel. Néhány elérhető lehetőség:

- VMware-HCX az adatfeldolgozás tömeges áttelepítéséhez.
- Az adatok hidegen áttelepíthetők a helyszíni vMotion a CloudSimple-be.

**Telepíthetek VMware-eszközöket?**

A CloudSimple natív VMware vSphere megoldást biztosít. A helyszíni vSphere-környezetek kezeléséhez használt bármely eszköz használható a CloudSimple. A CloudSimple támogatja a VMware-eszközök telepítésének saját Licences (BYOL) modelljét.

**Hogyan kezelik a frissítéseket és a frissítéseket?**

A CloudSimple zökkenőmentesen zavaró módon kezeli és frissíti a saját felhő összes infrastruktúra-összetevőjét. A VMware-vagy infrastruktúra-szolgáltatók által kiadott összes frissítés vagy biztonsági javítás ütemezett frissítésre van ütemezve, amint az CloudSimple minősítéssel rendelkezik.

A CloudSimple nem hajtja végre a privát felhőbe telepített alkalmazások frissítéseit és frissítéseit.

## <a name="azure-integration"></a>Azure-integráció

**Mely Azure-szolgáltatások támogatottak?**

A CloudSimple biztosítja az Azure ExpressRoute-kapcsolódást az Azure-előfizetéshez. Az előfizetésben futó minden szolgáltatás rendelkezik hálózati kapcsolattal a saját felhőhöz, és csatlakozhat a saját felhőhöz. Példák:

- **Azure Active Directory**: A CloudSimple-vCenter használja a Azure Active Directory identitási forrásként.
- **Azure Storage**: A tárhely használatával biztonsági mentéseket, képeket és egyéb adatokat tárolhat a saját felhőből.
- **Hibrid alkalmazások**: Létrehozhat olyan alkalmazás-architektúrát, amely a nyilvános és a privát felhőkre is kiterjed. Létrehozhat például olyan webkiszolgálókat az Azure-ban, amelyek egy CloudSimple saját felhőben érik el az alkalmazás-és adatbázis-kiszolgálókat.
- **Azure monitor** és **Azure Security Center**: A VMware-en futó munkaterhelések figyelő-és Security Center használhatnak a naplózáshoz, a teljesítmény mérőszámokhoz és a biztonság kezeléséhez.

**Hogyan a VMware-bérlőket az Azure-ba?**

A CloudSimple lehetővé teszi, hogy a VMware virtuális gépeket a Azure Portal egy privát felhőben kezelje. A globális rendszergazda által az előfizetéshez hozzárendelni kívánt erőforrás-korlátozásokkal konfigurált vCenter erőforráskészlet. 

**Milyen licencelési előnyökkel jár az Azure?**

A CloudSimple segítségével kihasználhatja a Azure Hybrid Benefit előnyeit, és akár 90%-ot is megtakaríthat a licencek esetében, hogy megőrizze a Microsoft-licencek beruházásait, és csökkentse a teljes tulajdonlási költségeket a többi felhőhöz képest. A Windows Server 2008-es és a Microsoft SQL Server 2008-os bővített biztonsági frissítései is megszerezhetők. A gyakori alkalmazások, például a Veeam, a Zerto és mások számára is alacsony a BYOL a felhőben. 
