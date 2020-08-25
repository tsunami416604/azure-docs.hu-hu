---
title: Fogalmak – privát felhők és fürtök
description: Ismerje meg az Azure VMware szoftver által meghatározott adatközpontok és vSphere-fürtök főbb lehetőségeit az Azure-beli VMware-megoldásokban.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 06161d2ce95415ae3309d58ad18ad0d40b3782fb
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752290"
---
# <a name="azure-vmware-solution-preview-private-cloud-and-cluster-concepts"></a>Azure VMware-megoldás – előzetes verzió – saját felhő és fürt – fogalmak

Az Azure VMware-megoldás VMware-alapú privát felhőket biztosít az Azure-ban. A privát felhők dedikált operációs rendszer nélküli gazdagépekről épülnek, és a Azure Portalon keresztül telepítik és felügyelik azokat. A privát felhőkben lévő fürtök VMware vSphere, vCenter, vSAN és NSX szoftverrel vannak kiépítve. Az Azure VMware megoldás saját Felhőbeli hardveres és szoftveres üzembe helyezése teljesen integrált és automatizált az Azure-ban.

Logikai kapcsolat áll fenn az Azure-előfizetések, az Azure VMware megoldás privát felhők, a vSAN-fürtök és a gazdagépek között. A diagramon két privát felhők jelennek meg egyetlen Azure-előfizetésben. A privát felhők fejlesztést és éles környezetet jelentenek, amelyek mindegyike saját privát felhővel rendelkezik. Ezekben a privát felhőkben két fürt található. A fejlesztési környezet alacsonyabb lehetséges igényeinek megjelenítéséhez az alacsonyabb kapacitású gazdagépekkel rendelkező kisebb fürtöket használja a rendszer. A fogalmakat az alábbi szakasz ismerteti.

![Az ügyfél-előfizetés két privát felhők képe](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Magánfelhők

A privát felhők dedikált, operációs rendszer nélküli Azure-gazdagépekkel létrehozott vSAN-fürtöket tartalmaznak. Mindegyik privát felhő több fürttel is rendelkezhet, amelyek mindegyike ugyanazzal a vCenter-kiszolgálóval és NSX-T kezelővel felügyelhető. Saját felhőket telepíthet és kezelhet a portálon, a parancssori felületen vagy a PowerShell használatával. Más erőforrásokhoz hasonlóan a privát felhők telepítése és felügyelete egy Azure-előfizetésen belül történik.

Az előfizetésen belüli privát felhők száma méretezhető. Kezdetben egy privát felhő korlátja van egy előfizetéshez.

## <a name="clusters"></a>Fürtök

Mindegyik privát felhőben létre kell hoznia legalább egy vSAN-fürtöt. Ha létrehoz egy privát felhőt, a rendszer alapértelmezés szerint egy fürtöt hoz létre. A Azure Portal vagy az API segítségével további fürtöket adhat hozzá egy privát felhőhöz. Minden fürt alapértelmezett mérete három gazdagép, és 3 – 16 gazdagépre méretezhető. A fürtben használt gazdagépek típusának azonos típusúnak kell lennie. A gazdagépek típusait a következő szakaszban ismertetjük.

A próbaverziós fürtök kiértékelésre vannak kiértékelve, és privát felhőben legfeljebb három gazdagépre és egyetlen próbaidőszakos fürtre korlátozódnak. A próbaidőszak alatt egyetlen gazdagépen is méretezheti a próbaverziós fürtöt.

A fürtöket a portálon vagy az API-n keresztül hozhatja létre, törölheti és méretezheti. Továbbra is a vSphere és a NSX-T kezelővel kezelheti a fürtkonfiguráció vagy a művelet más aspektusait. A fürt minden gazdagépének helyi tárterülete a vSAN felügyelete alatt áll.

## <a name="hosts"></a>Hosts

A Hyper-konvergens, operációs rendszer nélküli infrastruktúra-csomópontok az Azure VMware megoldás saját felhőalapú fürtjében használatosak. A gazdagép RAM-, CPU-és lemezterület-kapacitása az alábbi táblázatban található. 

| Állomás típusa              |             CPU             |   Memória (GB)   |  vSAN NVMe gyorsítótárazási szintje (TB, nyers)  |  vSAN SSD kapacitási szintje (TB, nyers)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| Magas vég (IT)          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

A fürtök létrehozásához vagy méretezéséhez használt gazdagépek a gazdagépek elkülönített készletéből szerezhetők be. Ezek a gazdagépek hardveres teszteket kaptak, és a Flash-lemezekről minden adattal biztonságosan törölve lettek. Ha eltávolít egy gazdagépet egy fürtből, a belső lemezek biztonságosan törlődnek, és a gazdagépek a gazdagépek elkülönített készletére kerülnek. Amikor egy gazdagépet ad hozzá egy fürthöz, a rendszer az elkülönített készletből megtisztított gazdagépet használ.

## <a name="vmware-software-versions"></a>VMware-szoftverek verziói

Az Azure VMware-megoldás saját felhőalapú fürtjében használt VMware-szoftver aktuális szoftververzió a következők:

| Szoftverek              |    Verzió   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U2    | 
| ESXi                  |    6,7 U2    | 
| vSAN                  |    6,7 U2    |
| NSX – T                 |      2,5     |

A privát felhőben lévő összes új fürt esetében a szoftver verziója egyezik a privát felhőben jelenleg futókkal. Az ügyfél-előfizetésben lévő összes új privát felhő esetén a szoftveres verem legújabb verziója van telepítve.

Az Azure VMware Solution platform szoftverre vonatkozó általános frissítési szabályzatokat és folyamatokat a frissítési fogalmakat ismertető dokumentum ismerteti.

## <a name="host-maintenance-and-lifecycle-management"></a>Gazdagép-karbantartás és életciklus-kezelés

A gazdagép-karbantartási és életciklus-felügyelet a privát felhőalapú fürtök kapacitására vagy teljesítményére gyakorolt hatás nélkül történik. Az automatizált gazdagépek karbantartására például a belső vezérlőprogram frissítése és a hardver javítása vagy cseréje tartozik.

A Microsoft felelős a NSX-T készülékek, például a NSX-T Manager és a NSX-T Edge-eszközök életciklus-felügyeletével. A Microsoft feladata továbbá a hálózati konfiguráció beindításának elvégzése, például a 0. rétegbeli átjáró létrehozása és az észak-déli útválasztás engedélyezése. Rendszergazdaként az Azure VMware-megoldás saját felhője számára a NSX-T SDN-konfiguráció, például a hálózati szegmensek, az elosztott tűzfalszabályok, az 1. rétegbeli átjárók és a terheléselosztó felelősek.

> [!IMPORTANT]
> Az Azure VMware-megoldás rendszergazdája nem módosíthatja az NSX-T Edge vagy a 0. rétegbeli átjáró konfigurációját. Ez a szolgáltatás elvesztését eredményezheti.

## <a name="backup-and-restoration"></a>Biztonsági mentés és helyreállítás

A Private Cloud vCenter és a NSX-T konfigurációkról óránként biztonsági mentés készül. A biztonsági mentések három napig tartanak. A biztonsági másolatból történő visszaállítást a Azure Portal egy szolgáltatási kérelme kéri.

## <a name="next-steps"></a>További lépések

A következő lépés a [Hálózatkezelés és a kapcsolatok közötti fogalmak](concepts-networking.md)megismerése.

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

