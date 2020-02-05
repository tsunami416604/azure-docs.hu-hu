---
title: Gyakori kérdések – Azure VMware-megoldások (AVS)
description: Gyakori kérdések az Azure VMware Solutions (AVS) szolgáltatásról
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025061"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>Gyakori kérdések az AVS által nyújtott VMware-megoldásról

## <a name="avs-service"></a>AVS-szolgáltatás

**Mi az Azure VMware Solutions (AVS)?**

Az Azure VMware Solutions (AVS) az Azure-ban percek alatt átalakítja és kiterjeszti a VMware számítási feladatokat a saját, dedikált felhőre. Az AVS gondoskodik a kiépítésről, az infrastruktúra kezeléséről, valamint a helyszíni és az Azure közötti számítási feladatok előkészítéséről. Mivel az alkalmazásai pontosan ugyanúgy futnak a helyszínen és az Azure-ban, a felhő rugalmasságát és szolgáltatásait is kihasználhatja az alkalmazások újratervezésének bonyolultsága nélkül. Az AVS csökkenti a teljes tulajdonlási költségeket egy Felhőbeli fogyasztási modellel, amely igény szerinti üzembe helyezést, utólagos elszámolást és kapacitás-optimalizálást biztosít. Ismerje [meg, mi a VMware-megoldás az Azure](cloudsimple-vmware-solutions-overview.md) -ban a funkciók, előnyök és forgatókönyvek esetében.

**Mi az az AVS Private Cloud?**

Az AVS Private Cloud egy privát, dedikált felhő, amely egy nagy teljesítményű számítási, tárolási és hálózatkezelési környezetből áll, amely az Azure-helyek Microsoft Azure infrastruktúráján (hardver-és adatközpont-terület) van üzembe helyezve. Az AVS Private Cloud egy natív VMware platformot biztosít szolgáltatásként. A VMware kifejezésekben minden egyes AVS Private-felhő pontosan egy példányt tartalmaz a vCenter Server. A vCenter Server felügyel több ESXi-csomópontot egy vagy több vSphere-fürtben, a hozzá tartozó virtuális SAN-(vSAN-) tárolóval együtt. Az AVS szolgáltatás több AVS privát felhőt is tartalmazhat az Azure-előfizetésében. További részletek: az [AVS Private Cloud áttekintése](cloudsimple-private-cloud.md).

**Hol érhető el az AVS szolgáltatás?**

Az AVS az USA keleti régiójában, az USA nyugati régiójában és a Nyugat-európai régióban is elérhető, és hamarosan további régiókban érhető el.

**Hogyan engedélyezni az AVS-előfizetést?**

Az AVS-szolgáltatás előfizetésének engedélyezéséhez vegye fel a kapcsolatot a [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) Microsoft-fiók képviselőjével. Adja meg annak az e-mail-címének az előfizetési AZONOSÍTÓját, amelyhez engedélyezni szeretné az AVS-szolgáltatást. 

**Hogyan hozzáférni az AVS-portálhoz?**

Az AVS-portálhoz a Azure Portal férhet hozzá. Részletekért lásd: [hozzáférés a VMware Solutions (AVS) portálhoz a Azure Portal](access-cloudsimple-portal.md).

**Hogyan az AVS Private Cloud kapacitásának növelését?**

A kapacitás növeléséhez vásároljon további csomópontokat a Azure Portal, majd a csomópontok segítségével bontsa ki az AVS-alapú privát felhőt az AVS-portálról. Hozzáadhat további csomópontokat egy meglévő vSphere-fürthöz, vagy hozzáadhat egy új vSphere-fürthöz. Részletekért lásd: [az AVS Private Cloud kibontása](expand-private-cloud.md).

**Mi történik az AVS Private Cloud szolgáltatással a karbantartás során?**

Az AVS több nappal az ütemezett karbantartási időköz előtt nyújt értesítést. A karbantartás nem zavaró módon történik, így biztosítható az AVS privát felhő rendelkezésre állása. A karbantartás a következő típusú lehet:

* **AVS-infrastruktúra**. Az AVS-infrastruktúra kiválóan elérhető. Az ilyen típusú karbantartási időszak során a redundáns összetevők egyszerre frissülnek, hogy elkerülje a szolgáltatás megszakadását. Ön gondoskodik az AVS Private Cloud vCenter, az összes virtuális géphez, az AVS Private Cloud-beli internetkapcsolatról, valamint a helyszíni vagy az Azure-hoz való csatlakozásról.
* **AVS-portál**. Az ilyen típusú karbantartási időszak során előfordulhat, hogy az AVS-portál néhány funkciója le van tiltva vagy nem érhető el. A karbantartási időközt megelőzően az értesítés a szolgáltatásra vonatkozó korlátozásokkal kapcsolatos részleteket tartalmaz, miközben karbantartás zajlik.

## <a name="connectivity"></a>Kapcsolódás

**Milyen csatlakozási lehetőségeim vannak az AVS-régió hálózatának?**

Az AVS a következő kapcsolódási lehetőségeket biztosítja az AVS-régió hálózatához való csatlakozáshoz. Egyszerre több lehetőség is használható.

* **ExpressRoute a helyszíni adatközpontból az AVS-régió hálózatához való kapcsolódást**. Ez egy nagy sebességű, kis késleltetésű, biztonságos magánhálózati kapcsolat, amely Global Reacht használ a helyszíni ExpressRoute áramkör az AVS ExpressRoute-áramkörhöz való áthidalása érdekében. A kapcsolat beállításával kapcsolatos útmutatásért lásd: [Kapcsolódás a helyszínről az AVS-hez a ExpressRoute használatával](on-premises-connection.md).
* **ExpressRoute a kapcsolódást az Azure-beli virtuális hálózatról az AVS-régió hálózatára**. Ez egy nagy sebességű, kis késleltetésű, biztonságos magánhálózati kapcsolat, amely virtuális hálózati átjárókat használ az Azure-beli virtuális hálózat az AVS ExpressRoute áramkörbe való áthidalása érdekében. A kapcsolat beállításával kapcsolatos útmutatásért lásd: az [AVS Private Cloud Environment csatlakoztatása az Azure Virtual networkhez a ExpressRoute használatával](azure-expressroute-connection.md).
* **Helyek közötti VPN-kapcsolat a helyszíni adatközpontból az AVS-régió hálózatára**. Ez egy biztonságos virtuális magánhálózat a helyszíni VPN-eszközről az AVS Private Cloud régiójába. Részletekért lásd: [VPN-átjárók beállítása AVS-hálózaton](vpn-gateway.md).

**Hogyan csatlakozhat egy AVS Private-felhőhöz?**

Az AVS-portálon megtekintheti az AVS-beli saját felhő részleteit. Az AVS Private Cloud-nak megfelelő vCenter való csatlakozáshoz először ellenőrizze, hogy a hálózati kapcsolat létrejött-e a helyek közötti VPN, pont – hely típusú VPN vagy ExpressRoute használatával. Ezután indítsa el az AVS-portált a Azure Portal, majd kattintson a kezdőlapon vagy a **vSphere-ügyfél elindítása** lehetőségre a kezdőlapon vagy az AVS Private Cloud részleteit tartalmazó oldalon.

**Mi a ExpressRoute-áramkörök előnye?**

Az Azure ExpressRoute áramkör nagy sebességű, kis késleltetésű és biztonságos kapcsolat. Az AVS dedikált ExpressRoute-áramkört biztosít régiónként/ügyfélként. Ezen áramkör használatával biztonságos kapcsolatot létesíthet a helyszínen vagy az Azure-előfizetésében.

**Mik a hálózati költségek az AVS-hez való kapcsolódáshoz? A kimenő forgalomra vonatkozó díjak az AVS és az Azure, illetve az egyes régiók között is érvényesek?**

A hálózati kimenő forgalomért nem kell AVS-díjat fizetni. Az Azure standard díjszabása a virtuális hálózatról vagy a helyszíni ExpressRoute áramkörről érkező kimenő forgalomra vonatkozik.

## <a name="networking"></a>Hálózatkezelés

**Milyen hálózatkezelési funkciók érhetők el az AVS Private Cloud szolgáltatáshoz?**

Kiépítheti a VLAN-okat (és az alhálózatokat) és a tűzfalakat, valamint olyan nyilvános IP-címeket rendelhet hozzá, amelyek az AVS Private-felhőben futó virtuális gépekre képezhetők le. A hálózatkezelési funkciókkal kapcsolatos részletekért lásd: a [VLAN-ok és az alhálózatok áttekintése](cloudsimple-vlans-subnets.md), a [tűzfalak áttekintése](cloudsimple-firewall-tables.md)és a [nyilvános IP-cím áttekintése](cloudsimple-public-ip-address.md).

**Hogyan különböző alhálózatokat beállítani saját AVS Private-Felhőbeli alkalmazásaihoz?**

Az AVS-portálról VLAN-okat hozhat létre a saját AVS-felhőben. Miután létrehozta a virtuális helyi hálózatot, létrehozhat egy elosztott portot az AVS Private Cloud vCenter a VLAN használatával, és létrehozhatja az elosztott port csoporthoz csatlakozó virtuális gépeket. Engedélyezheti a tűzfal-táblákat a VLAN/alhálózat számára, és megadhatja a tűzfalszabályok a hálózati forgalom biztonságossá tételéhez.

**Milyen tűzfal-beállítások érhetők el az AVS Private-Felhőkhöz?**

Az észak-déli és a kelet-nyugati forgalomra vonatkozó szabályokat is konfigurálhat. A szabályok definiálva vannak egy tűzfal táblában. A tűzfal tábla az AVS Private-felhőben lévő VLAN-okra is csatlakoztatható. Részletekért lásd: [Tűzfalszabályok beállítása az AVS privát felhőkhöz](firewall.md).

**Kioszthatok nyilvános IP-címeket a saját AVS Private Cloud Environment-beli virtuális gépekhez?**

Az AVS-portálon hozzárendelhet egy új nyilvános IP-címet, és társíthatja azt egy virtuális gép vagy berendezés magánhálózati IP-címével. Új tűzfalszabályok is létrehozhatók, vagy a meglévő tűzfalszabályok alkalmazása lehetővé teszi, hogy a portálon az adott portokból és IP-címekről érkező forgalom is elérhető legyen. Részletekért lásd: [nyilvános IP-címek lefoglalása az AVS Private Cloud Environment számára](public-ips.md).

## <a name="security"></a>Biztonság

**Milyen biztonsági beállítások vannak az AVS-ben?**

Az AVS a következő biztonsági funkciókat biztosítja az AVS Private Cloud Environment biztonságossá tételéhez:

* Inaktív **adatok titkosítása**. Az AVS Private Cloud vSAN-tárolójában lévő REST-adatok titkosítva lehetnek. a vSAN támogatja a külső kulcsokat kezelő kiszolgálókat, amelyek üzembe helyezhetők az Azure vNet vagy a helyszíni környezetben. Részletekért lásd: [vSAN-titkosítás konfigurálása az AVS Private Cloud szolgáltatáshoz](vsan-encryption.md).
* **Hálózati biztonság**. A hálózati forgalom szabályozása az AVS Private Cloud és az Internet, az AVS Private Cloud és a helyszíni környezet, illetve az AVS Private Cloud alhálózatai között érvényes tűzfalszabályok használatával.
* **Biztonságos, magánhálózati kapcsolatok**. Biztonságos, magánhálózati kapcsolat áll fenn a helyszíni hálózat és az Azure-előfizetés között.

## <a name="compute"></a>Számítási szolgáltatások

**Milyen típusú gazdagépek érhetők el?**

Az AVS a következő típusú gazdagépeket kínálja:

* **CS28 csomópont:** CPU: 2x 2,2 GHz, összesen 28 mag, 48 HT.  RAM: 256 GB.  Tárterület: 1600 GB NVMe cache, 5760 GB adat (all-Flash). Hálózat: 4x25Gbe hálózati adapter
* **CS36 csomópont:** CPU 2x 2,3 GHz, összesen 36 mag, 72 HT.  RAM: 512 GB.  Storage: 3200 GB NVMe cache 11520 GB adat (teljes Flash).  Hálózat: 4x25Gbe hálózati adapter
* **CS36m csomópont:** CPU 2x 2,3 GHz, összesen 36 mag, 72 HT.  RAM: 576 GB.  Storage: 3200 GB NVMe cache 13360 GB adat (teljes Flash).  Hálózat: 4x25Gbe hálózati adapter

**Hogyan történik a hardveres hibák kezelése?**

Az AVS-platform és a szolgáltatási műveleti csapatok folyamatosan figyelik az összes AVS-infrastruktúrát. Ha hardverhiba észlelhető, a rendszer hozzáad egy új csomópontot az AVS Private-felhőhöz, és eltávolítja a meghibásodott csomópontot.

## <a name="storage"></a>Adattárolás

**Milyen típusú tárterületet támogat az AVS Private Cloud?**

Az AVS minden olyan Flash VMware vSAN-tárolót biztosít, amely minden AVS Private-felhővel rendelkezik. Minden vSphere a saját vSAN adattárával jön létre. Részletekért lásd: [AVS Private Cloud VMware Components-vSAN Storage](vmware-components.md#vsan-storage).

**Támogatott az adattitkosítás?**
Igen. A vSAN-tárolót az AVS Private-felhőben úgy állíthatja be, hogy a helyszíni vagy az Azure-ban üzembe helyezett kulcskezelő kiszolgáló (KMS) használatával Titkosítsa a vSAN tárolt adatait.

**Hogyan történik a hibás lemezek kezelése?**

Az AVS folyamatosan figyeli az AVS Private Cloud összes hardveres összetevőjét. Ha a rendszer hibát észlel, vagy ha egy lemez hibásként van azonosítva (heurisztikus alapján), a rendszer automatikusan hozzáadja az új csomópontot az AVS privát felhőhöz. A meghibásodott vagy meghibásodott lemezzel rendelkező csomópont el lesz távolítva az AVS Private Cloud-ból.

## <a name="vmware"></a>VMware

**Hogyan a helyszíni alkalmazások és adatok nagy léptékű feltöltését vagy áttelepítését?**

Az AVS natív VMware vSphere megoldást biztosít. A tömeges adatáttelepítéshez használható összes VMware-eszköz használható az AVS Private Cloud szolgáltatással. A lehetőségek a következők:

* VMware-HCX az adatfeldolgozás tömeges áttelepítéséhez.
* Az adatok hidegen áttelepíthetők a helyszíni vMotion-ből az AVS-be.

**Telepíthetek VMware-eszközöket?**

Az AVS natív VMware vSphere megoldást biztosít. A helyszíni vSphere-környezet kezeléséhez használt összes VMware-eszköz használható az AVS-en. Az AVS támogatja a VMware-eszközök telepítését a saját licencű (BYOL) modellben.

**Hogyan kezelik a frissítéseket és a frissítéseket?**

Az AVS zökkenőmentes, nem zavaró módon kezeli és frissíti az AVS Private Cloud összes infrastruktúra-összetevőjét. A VMware-vagy infrastruktúra-szolgáltatók által kiadott összes frissítés és biztonsági javítás ütemezett frissítésre van ütemezve, amint az AVS-nek megfelelő minősítéssel rendelkezik.

Az AVS nem hajtja végre az AVS Private Cloud-ra telepített alkalmazások frissítéseit és frissítéseit.

## <a name="azure-integration"></a>Azure-integráció

**Mely Azure-szolgáltatások támogatottak?**

Az AVS Azure ExpressRoute-kapcsolódást biztosít az Azure-előfizetéshez. Az előfizetésben futó összes szolgáltatás csatlakozhat az AVS Private Cloud-hoz. Példák:

* **Azure Active Directory** az AVS-vCenter tartozó identitás forrásaként.
* **Azure Storage** biztonsági másolatok, képek és egyéb adatok tárolására az AVS Private Cloud-ból.
* **Hibrid alkalmazások** a nyilvános és az AVS privát felhőkre kiterjedő alkalmazás-architektúrával. Létrehozhat például olyan webkiszolgálókat az Azure-ban, amelyek az alkalmazás-és adatbázis-kiszolgálókat érik el az AVS Private Cloud-on.
* Az **Azure monitor** és az **Azure Security Center** a VMware-támogatás naplózásával, teljesítmény-metrikákkal és biztonsági felügyelettel futó munkaterhelésekhez.

**Hogyan a VMware-bérlőket az Azure-ba?**

Az AVS lehetővé teszi, hogy a VMware-alapú virtuális gépeket az Azure Portal-ról származó AVS Private Cloud-on felügyelje. A kívánt erőforrás-korlátozásokkal konfigurált vCenter erőforráskészlet a globális rendszergazda által előfizetéshez rendelhető hozzá. 

**Milyen licencelési előnyökkel jár az Azure?**

Az AVS használatával kihasználhatja az Azure Hybrid használati juttatás előnyeit, és akár 90%-ot is megtakaríthat a licencek esetében. Ez az előny megőrzi a Microsoft-licencek beruházásait, és csökkenti a TCO-t más felhőalapú megoldásokhoz képest. A Windows Server 2008-es és a Microsoft SQL Server 2008-os bővített biztonsági frissítései is megszerezhetők. A saját licencű (BYOL) modell segítségével alacsonyan tarthatók a költségek a gyakori alkalmazások, például a Veeam és a Zerto esetében. 
