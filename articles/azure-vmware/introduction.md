---
title: Bevezetés
description: Ismerje meg az Azure VMware-megoldás (AVS) funkcióit és előnyeit a VMware-alapú számítási feladatok Azure-beli üzembe helyezéséhez és kezeléséhez.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: e227b6c7c2f25fbf62f11c17dc62293e4fbd5267
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740331"
---
# <a name="what-is-azure-vmware-solution-avs-preview"></a>Mi az az Azure VMware Solution (AVS) előzetes verzió?

Az Azure VMware-megoldás (AVS) saját felhőket biztosít az Azure-ban. A privát felhők olyan vSphere-fürtöket tartalmaznak, amelyek dedikált operációs rendszer nélküli Azure-infrastruktúrából épülnek fel. A privát felhőalapú fürtöket 3 – 16 gazdagépre méretezheti, így több fürt is rendelkezhet egyetlen privát felhőben. Az összes privát felhők vCenter Server, VSAN, vSphere és NSX-T-vel vannak kiépítve. Áttelepítheti a munkaterheléseket a helyszíni környezetekről, új virtuális gépeket hozhat létre vagy helyezhet üzembe, és az Azure-szolgáltatásokat a privát felhőkből is felhasználhatja.

Az AVS egy VMware-alapú ellenőrzött megoldás, amely a fejlesztés és a frissítés folyamatos ellenőrzésével és tesztelésével foglalkozik. A privát felhőalapú infrastruktúrát és szoftvereket a Microsoft felügyeli és tartja karban, így a saját Felhőbeli számítási feladatok fejlesztésére és futtatására koncentrálhat.

Az alábbi ábra az Azure, az Azure-szolgáltatások és a helyszíni környezetek privát felhők és virtuális hálózatok közötti érintkezést mutatja be. A privát felhőktől az Azure-szolgáltatásokhoz vagy virtuális hálózatok való hálózati hozzáférés az Azure szolgáltatási végpontok SLA-alapú integrálását teszi lehetővé. A helyszíni környezetek privát Felhőbeli hozzáférése ExpressRoute Global Reach biztosít a privát és biztonságos kapcsolathoz.

![Az Azure-beli és a helyszíni AVS Private Cloud-környezet képe](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Gazdagépek, fürtök és privát felhők

Az AVS privát felhők és fürtök egy operációs rendszer nélküli, Hyper-konvergens Azure-infrastruktúra-gazdagépről készültek. A magas szintű gazdagépek 576 GB RAM-mal és kettős Intel 18 mag, 2,3 GHz-es processzorokkal rendelkeznek. A két vSAN diskgroups rendelkezik, összesen 15,36 TB-os (SSD) nyers vSAN kapacitási réteggel, valamint egy 3,2 TB (NVMe) vSAN gyorsítótár-réteggel.

Az új privát felhők üzembe helyezése a Azure Portal vagy az Azure CLI használatával történik.

## <a name="networking"></a>Hálózat

Privát felhő telepítésekor a rendszer létrehoz egy magánhálózati hálózatot a felügyelethez, a létesítéshez és a vMotion. Ezek a magánhálózatok a vCenter és a NSX-T kezelőhöz, valamint a virtuális gépek vMotion vagy üzembe helyezéséhez használhatók. Az összes magánhálózat elérhető egy Azure-beli VNet vagy helyszíni környezetből. A ExpressRoute Global Reach a privát felhőknek a helyszíni környezetekhez való csatlakoztatására szolgál, és ehhez a kapcsolathoz az előfizetésben ExpressRoute áramkört igénylő VNet van szükség.

Az internethez és az Azure-szolgáltatásokhoz való hozzáférés kiépítése a privát felhő telepítésekor történik. A hozzáférés úgy van megadva, hogy az éles munkaterhelésű hálózatokon lévő virtuális gépek képesek legyenek az Azure-vagy Internet-alapú szolgáltatások használatára. Az Internet-hozzáférés alapértelmezés szerint le van tiltva az új privát felhők esetében, és bármikor engedélyezhető vagy letiltható.

A hálózatkezeléssel és az összekapcsolással kapcsolatos további információkért tekintse meg a [hálózatkezelési fogalmakat](concepts-networking.md) ismertető cikket.

## <a name="access-and-security"></a>Hozzáférés és biztonság

A fokozott biztonság érdekében az AVS privát felhők a vSphere szerepköralapú hozzáférés-vezérlést használják. a vSphere SSO LDAP-képességekkel integrálható a Azure Active Directory. Az identitással és a jogosultságokkal kapcsolatos további információkért tekintse meg a [hozzáférési és identitási fogalmakat](concepts-identity.md) ismertető cikket.

a vSAN inaktív adatok titkosítása alapértelmezés szerint engedélyezve van, és a vSAN adattár biztonságának biztosítására szolgál. A [tárolási fogalmakról](concepts-storage.md) szóló cikkben részletesebben ismertetjük.

## <a name="host-and-software-lifecycle-maintenance"></a>A gazdagép és a szoftver életciklusának karbantartása

Az AVS Private Cloud és a VMware szoftver rendszeres frissítése biztosítja, hogy a legújabb biztonsági, stabilitási és szolgáltatási készletek a privát felhőkben fussanak. A platform karbantartásával és frissítéseivel kapcsolatos további részletek a [frissítési fogalmakat](concepts-upgrades.md) ismertető cikkben találhatók.

## <a name="monitoring-your-private-cloud"></a>Saját felhő figyelése

A naplók a [Azure monitorban](../azure-monitor/overview.md) az AVS Private-felhőben futó virtuális gépek naplófájljainak gyűjtésére használhatók. Az [MMA-ügynököt letöltheti és telepítheti](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) az AVS Private-felhőkben futó Linux-és Windows-alapú virtuális gépeken, és ugyanazokat a lekérdezéseket használhatja, mint amelyeket a helyszíni virtuális gépeken futtat. Ugyanazokat a lekérdezéseket futtathatja, amelyeket általában a virtuális gépeken is futtatnak. További információ a lekérdezések létrehozásáról: [lekérdezések írása](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries).

## <a name="next-steps"></a>További lépések

A következő lépés a [saját felhő és a fürtök legfontosabb alapfogalmai](concepts-private-clouds-clusters.md)megismerése.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
