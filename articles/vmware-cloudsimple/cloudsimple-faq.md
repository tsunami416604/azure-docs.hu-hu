---
title: GYIK – Azure VMware-megoldás a CloudSimple-től
description: Gyakori kérdések a CloudSimple Azure VMware-megoldásával kapcsolatban
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025061"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Gyakori kérdések a CloudSimple VMware-megoldásával kapcsolatban

## <a name="cloudsimple-service"></a>CloudSimple szolgáltatás

**Mi az Azure VMware-megoldás a CloudSimple-től?**

Az Azure VMware Solution by CloudSimple percek alatt átalakítja és kiterjeszti a VMware-számítási feladatokat az Azure-beli privát, dedikált felhőkre. A CloudSimple gondoskodik az infrastruktúra kiépítéséről, az infrastruktúra kezeléséről és a helyszíni és az Azure közötti számítási feladatok vezényléséről. Mivel az alkalmazások pontosan ugyanazt a helyszíni és az Azure-beli futtatást futnak, a felhő rugalmasságának és szolgáltatásainak előnyeit élvezheti anélkül, hogy az alkalmazások újratervezése bonyolulttá válna. A CloudSimple csökkenti a teljes tulajdonlási költséget egy felhőfelhasználási modellel, amely igény szerinti kiépítést, díjfizetés,és kapacitásoptimalizálást biztosít.  Tekintse meg [a Mi a VMware-megoldás az Azure-ban a CloudSimple](cloudsimple-vmware-solutions-overview.md) által a funkciók, előnyök és forgatókönyvek.

**Mi az a CloudSimple privát felhő?**

A CloudSimple private cloud egy privát, dedikált felhő, amely egy nagy teljesítményű számítási, tárolási és hálózati környezetből áll, amelyet a Microsoft Azure infrastruktúráján (hardver- és adatközpont-tárhelyen) telepítenek az Azure-beli helyeken.  A Private Cloud natív VMware "platformot, mint szolgáltatást" biztosít. A VMware szempontjából minden private cloud tartalmazza pontosan egy példányát a vCenter Server. A vCenter-kiszolgáló több ESXi-csomópontot kezel egy vagy több vSphere-fürtben, valamint a megfelelő virtuális SAN (vSAN) tárolót. A CloudSimple szolgáltatás több privát felhőt is tartalmazhat az Azure-előfizetésében.  További részletek: [Private Cloud overview](cloudsimple-private-cloud.md).

**Hol érhető el a CloudSimple szolgáltatás?**

A CloudSimple az USA keleti régiójában, az USA nyugati régiójában és Nyugat-Európában érhető el, és hamarosan további régiók is elérhetők.

**Hogyan engedélyezhetem az előfizetésemet a CloudSimple-re?**

A CloudSimple szolgáltatásra [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) vonatkozó előfizetés engedélyezéséhez lépjen kapcsolatba a Microsoft-fiók képviselőjével. Adja meg az előfizetés-azonosítóját abban az e-mailben, amelyhez engedélyezni szeretné a CloudSimple szolgáltatást.  

**Hogyan érhetem el a CloudSimple portált?**

A CloudSimple portált az Azure Portalról érheti el.  További információt [a VMware-megoldás a CloudSimple portálon keresztül az Azure Portalról című témakörben talál.](access-cloudsimple-portal.md)

**Hogyan növelhetem a privát felhő kapacitását?**

A kapacitás növelése érdekében vásároljon további csomópontokat az Azure Portalon, majd a csomópontok segítségével bővítse ki a privát felhőa a CloudSimple portálról.  További csomópontokat adhat hozzá egy meglévő vSphere-fürthöz, vagy hozzáadhatja őket egy új vSphere-fürthöz.  További információt a [CloudSimple private cloud kibővítése című témakörben talál.](expand-private-cloud.md)

**Mi történik a privát felhőmmel a karbantartás során?**

A CloudSimple néhány nappal az ütemezett karbantartási időköz előtt értesítést küld.  A karbantartás nem zavaró módon történik, hogy biztosítsa a privát felhő rendelkezésre állását.  A karbantartás a következő típusú lehet:

* **CloudSimple infrastruktúra**.  A CloudSimple infrastruktúra magas rendelkezésre állású.  Az ilyen típusú karbantartási időköz alatt a redundáns összetevők egyenként frissülnek, hogy elkerüljék a szolgáltatás megszakadását. Fenntartja a hozzáférést a privát felhő vCenter, az összes virtuális gépek, az internet-kapcsolat a privát felhőből, és a helyszíni vagy az Azure-kapcsolat.
* **CloudSimple portál**. Az ilyen típusú karbantartási időköz során előfordulhat, hogy a CloudSimple portál egyes funkciói le vannak tiltva vagy nem érhetők el.  A karbantartási időköz előtti értesítés részletesen ismerteti a szolgáltatáskorlátozásokat a karbantartás ideje alatt.

## <a name="connectivity"></a>Kapcsolatok

**Milyen csatlakozási lehetőségeim vannak a CloudSimple régióhálózattal?**

A CloudSimple a következő csatlakozási lehetőségeket biztosítja a CloudSimple régióhálózathoz való csatlakozáshoz. Egyszerre több lehetőség is használható.

* **ExpressRoute-kapcsolat a helyszíni adatközpontból a CloudSimple régióhálózatához.** Ez egy nagy sebességű, alacsony késésű, biztonságos privát kapcsolat, amely a Global Reach segítségével hidalja át a helyszíni ExpressRoute-áramkört a CloudSimple ExpressRoute-kapcsolattal. A kapcsolat beállításával kapcsolatos tudnivalókért olvassa el a [Csatlakozás a helyszíni környezetből a CloudSimple szolgáltatásba az ExpressRoute használatával című témakört.](on-premises-connection.md)
* **ExpressRoute-kapcsolat az Azure virtuális hálózatáról a CloudSimple régióhálózatával.** Ez egy nagy sebességű, alacsony késleltetésű, biztonságos privát kapcsolat, amely virtuális hálózati átjárók segítségével hidalja át a virtuális hálózatot az Azure-ban a CloudSimple ExpressRoute-kapcsolaton keresztül. A kapcsolat beállításával kapcsolatos tudnivalókat a [CloudSimple private cloud környezet csatlakoztatása az Azure virtuális hálózathoz az ExpressRoute használatával című](azure-expressroute-connection.md)témakörben találja.
* **Helyek közötti VPN-kapcsolat a helyszíni adatközpontból a CloudSimple régióhálózatához.** Ez egy biztonságos virtuális magánhálózat a helyszíni VPN-eszközről a CloudSimple private cloud régióba.  További információt a [VPN-átjárók beállítása a CloudSimple hálózaton](vpn-gateway.md)című témakörben talál.

**Hogyan csatlakozhatok egy privát felhőhöz?**

Megtekintheti a privát felhő részleteit a CloudSimple portálon. A magánfelhőnek megfelelő virtuális központhoz való csatlakozáshoz először ellenőrizze, hogy a helyek közötti VPN, a pont-hely VPN vagy az ExpressRoute használatával létrejött-e a hálózati kapcsolat. Ezután indítsa el a CloudSimple portált az Azure Portalon, és kattintson a **vSphere-ügyfél indítása** a kezdőlapon vagy a magánfelhő részletei lapon.

**Mi az ExpressRoute-áramkörök előnye?**

Az Azure ExpressRoute-áramkör egy nagy sebességű, kis késésű, biztonságos kapcsolat.  A CloudSimple régiónként és ügyfélenként dedikált ExpressRoute-áramkört biztosít.  Ezzel az áramkörrel biztonságos kapcsolatot hozhat létre a helyszíni vagy az Azure-előfizetésből.

**Milyen hálózati költségekkel kell csatlakozni a CloudSimple szolgáltatáshoz?  A CloudSimple és az Azure, illetve a régiók között vonatkoznak a kimenő forgalom díjai?**

A hálózati kimenő forgalomért nincs CloudSimple díj.  Az Azure-díjszabások a virtuális hálózatról vagy a helyszíni ExpressRoute-kapcsolatból érkező kimenő forgalomra vonatkoznak.

## <a name="networking"></a>Hálózat

**Milyen hálózati funkciók érhetők el a privát felhőhöz?**

VlAN-okat (és azok alhálózatait) és tűzfaltáblákat létesíthet, és nyilvános IP-címeket rendelhet hozzá, amelyek leképezik a magánfelhőben futó virtuális géphez. A hálózati szolgáltatásokról a [VLAN-ok és alhálózatok áttekintése](cloudsimple-vlans-subnets.md), [a tűzfaltáblák áttekintése](cloudsimple-firewall-tables.md)és a [Nyilvános IP-cím áttekintése](cloudsimple-public-ip-address.md)című témakörben olvashat.

**Hogyan állíthatok be különböző alhálózatokat az alkalmazásaimhoz a privát felhőben?**

VlAN-okat hozhat létre a privát felhőben a CloudSimple portálon.  Miután létrehozott egy VLAN-t, létrehozhat egy elosztott portcsoportot a Private Cloud vCenteren a VLAN használatával, és létrehozhat virtuális gépeket, amelyek az elosztott portcsoporthoz kapcsolódnak.  Engedélyezheti a tűzfaltáblákat a VLAN/alhálózat számára, és tűzfalszabályokat határozhat meg a hálózati forgalom védelmére.

**Milyen tűzfalbeállítások érhetők el a privát felhőkhöz?**

Az észak-déli és kelet-nyugati forgalomra vonatkozó szabályokat konfigurálhatja.  A szabályok egy tűzfaltáblában vannak definiálva.  A tűzfaltábla csatolható a VLAN-okhoz a privát felhőben.  További információt a [Tűzfaltáblák beállítása és a privát felhők re vonatkozó szabályok (Tűzfaltáblák és szabályok beállítása) (Személyes felhők) témakörben talál.](firewall.md)

**Hozzárendelhetek nyilvános IP-címeket a virtuális gépekhez a privát felhőbeli környezetben?**

A CloudSimple portálon lefoglalhatja egy új nyilvános IP-címet, és társíthatja azt egy virtuális gép vagy egy berendezés privát IP-címéhez.  Új tűzfalszabályokat is létrehozhat, vagy meglévő tűzfalszabályokat alkalmazhat a portál adott portjairól és IP-címeiről érkező forgalom engedélyezéséhez. További információt a [Nyilvános IP-címek lefoglalása privát felhőbeli környezethez című témakörben](public-ips.md)talál.

## <a name="security"></a>Biztonság

**Milyen biztonsági lehetőségeim vannak a CloudSimple-en?**

A CloudSimple a következő biztonsági funkciókat biztosítja a privát felhőkörnyezet védelméhez:

* **Az adatok inaktív titkosítása**. A vSAN-tárolón a magánfelhőben tárolt adatok titkosítása. a vSAN támogatja a külső kulcskezelő kiszolgálókat, amelyek az Azure virtuális hálózaton vagy a helyszíni környezetben telepíthetők.  További információt a [VSAN-titkosítás konfigurálása a CloudSimple private cloud szolgáltatáshoz című témakörben talál.](vsan-encryption.md)
* **Hálózati biztonság**. A hálózati forgalom szabályozása a magánfelhő és az internet, a privát felhő és a helyszíni környezet, illetve a magánfelhő alhálózatai között érvényes tűzfalszabályokkal.
* **Biztonságos, privát kapcsolat**. A helyszíni hálózat és az Azure-előfizetés között biztonságos, privát kapcsolat jön létre.

## <a name="compute"></a>Compute

**Milyen házigazdák állnak rendelkezésre?**

A CloudSimple a következő gazdagéptípusokat kínálja:

* **CS28 csomópont:** CPU:2x 2.2 GHz, összesen 28 mag, 48 HT.  RAM: 256 GB.  Tárhely: 1600 GB NVMe gyorsítótár, 5760 GB-os adat (All-Flash). Hálózat: 4x25Gbe NIC
* **CS36 csomópont:** CPU 2x 2,3 GHz, összesen 36 mag, 72 HT.  RAM: 512 GB.  Tárhely: 3200 GB NVMe cache 11520 GB adat (All-Flash).  Hálózat: 4x25Gbe NIC
* **CS36m csomópont:** CPU 2x 2,3 GHz, összesen 36 mag, 72 HT.  RAM: 576 GB.  Tárhely: 3200 GB NVMe cache 13360 GB adat (All-Flash).  Hálózat: 4x25Gbe NIC

**Hogyan kezelik a hardverhibákat?**

A CloudSimple platform és a szolgáltatási műveleti csapataink folyamatosan figyelik az összes CloudSimple infrastruktúrát.  Ha hardverhibát észlel, a rendszer új csomópontot ad hozzá a magánfelhőhöz, és eltávolítja a meghibásodott csomópontot.

## <a name="storage"></a>Storage

**Milyen típusú tárhely et támogat egy privát felhő?**

A CloudSimple minden flash VMware vSAN tárolót kínál minden magánfelhővel.  Minden vSphere saját vSAN adattalettel jön létre.  További részletek: [Private Cloud VMware components - vSAN storage](vmware-components.md#vsan-storage).

**Támogatott az adatok titkosítása?**
Igen.  Beállíthatja a vSAN-tároló a magánfelhőben, hogy egy kulcsfelügyeleti kiszolgáló (KMS), amely a helyszínen telepített, vagy az Azure-ban a vSAN-n tárolt adatok titkosításához.

**Hogyan történik a meghibásodott lemezek kezelése?**

A CloudSimple folyamatosan figyeli a magánfelhő összes hardverösszetevőjét.  Ha lemezhibát észlel, vagy a rendszer hibásként azonosítja a lemezt (heurisztika alapján), a rendszer automatikusan hozzáad egy új csomópontot a magánfelhőhöz.  A meghibásodott vagy meghibásodott lemezzel rendelkező csomópont törlődik a magánfelhőből.

## <a name="vmware"></a>VMware

**Hogyan hajthatom végre az alkalmazások és adatok nagyméretű feltöltését vagy áttelepítését a helyszíni környezetből?**

A CloudSimple natív VMware vSphere megoldást biztosít.  A tömeges adatáttelepítéshez szükséges összes VMware-eszköz használható a privát felhővel.  A lehetőségek a következők:

* VMware HCX az adatok tömeges áttelepítéséhez.
* Adatok áttelepítése a Storage vMotion használatával a helyszíni cloudsimple használatával.

**Telepíthetek bármilyen VMware eszközt?**

A CloudSimple natív VMware vSphere megoldást biztosít.  A helyszíni vSphere környezet kezeléséhez használt összes VMware-eszköz használható a CloudSimple-en.  A CloudSimple támogatja a Bring-your-own-license (BYOL) modellt a VMware-eszközök telepítéséhez.

**Hogyan történik a frissítések és a frissítések kezelése?**

A CloudSimple zökkenőmentesen kezeli és frissíti a privát felhő összes infrastruktúra-összetevőjét.  A VMware vagy az infrastruktúra-szállítók által kiadott összes frissítés és biztonsági javítás frissítésre van ütemezve, amint a CloudSimple minősíti őket.

A CloudSimple nem hajt végre frissítéseket vagy a magánfelhőre telepített alkalmazások frissítéseit.

## <a name="azure-integration"></a>Azure-integráció

**Milyen Azure-szolgáltatások támogatottak?**

A CloudSimple Azure ExpressRoute-kapcsolatot biztosít az Azure-előfizetéséhez.  Az előfizetésében futó összes szolgáltatás csatlakozhat a privát felhőhöz.  Példák erre vonatkozóan:

* **Az Azure Active Directory** a CloudSimple vCenter identitásforrásaként.
* **Azure-tárhely** a biztonsági mentések, képek és egyéb adatok tárolására a magánfelhőből.
* **Hibrid alkalmazások** olyan alkalmazásarchitektúrával, amely nyilvános és privát felhőkre is kiterjed.  Létrehozhat például webkiszolgálókat az Azure-ban, amelyek a magánfelhőben lévő alkalmazás- és adatbázis-kiszolgálókhoz férnek hozzá.
* **Az Azure Monitor** és az **Azure biztonsági központja** a VMware-en futó számítási feladatokhoz támogatja a naplózást, a teljesítménymutatókat és a biztonsági felügyeletet.

**Hogyan maskolhatom le a VMware-bérlőimet az Azure-ra?**

A CloudSimple egyedülálló lehetőséget biztosít a VMware virtuális gépek magánfelhőben történő kezelésére az Azure Portalról.  A kívánt erőforrás-megkötésekkel konfigurált vCenter erőforráskészletet a globális rendszergazda leképezheti az előfizetésre.  

**Milyen licencelési előnyöket kapok az Azure-tól?**

A CloudSimple segítségével kihasználhatja az Azure hybrid usage benefit előnyeit, és akár 90%-ot takaríthat meg a licenceken. Ez az előny megőrzi a Microsoft-licencekbe való befektetését, és csökkenti a TCO-t más felhőalapú megoldásokhoz képest. A Windows Server 2008 és a Microsoft SQL Server 2008 rendszerhez is kaphat biztonsági frissítéseket.  A bring-your-own-license (BYOL) modell segítségével alacsonyan tarthatja a költségeket az olyan gyakori alkalmazások esetében, mint a Veeam és a Zerto.  
