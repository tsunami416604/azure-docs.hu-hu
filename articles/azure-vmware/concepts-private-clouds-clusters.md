---
title: Fogalmak – privát felhők és fürtök
description: Ismerje meg az Azure VMware Solution szoftver által meghatározott adatközpontok és vSphere-fürtök főbb képességeit.
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: daa712a722ca3252a49e4f54e0cc9e42de4ffc73
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337321"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware-megoldás – saját felhő és fürt – fogalmak

Az Azure VMware-megoldás VMware-alapú privát felhőket biztosít az Azure-ban. A privát felhők dedikált, operációs rendszer nélküli Azure-gazdagépekkel létrehozott fürtöket tartalmaznak. Ezeket a Azure Portal, a CLI vagy a PowerShell segítségével telepítik és kezelik.  A privát felhőkben kiépített fürtök közé tartozik a VMware vSphere, a vCenter, a vSAN és a NSX szoftver. Az Azure VMware megoldás saját Felhőbeli hardveres és szoftveres üzembe helyezése teljesen integrált és automatizált az Azure-ban.

Logikai kapcsolat áll fenn az Azure-előfizetések, az Azure VMware megoldás privát felhők, a vSAN-fürtök és a gazdagépek között. A diagram egyetlen Azure-előfizetést mutat be két privát felhőből, amelyek a fejlesztési és a termelési környezetet képviselik.  Ezekben a privát felhőkben két fürt található. 

Ez a cikk az alábbi fogalmakat ismerteti.

![Az ügyfél-előfizetés két privát felhők képe](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>A fejlesztési környezet alacsonyabb lehetséges igényei miatt alacsonyabb kapacitású gazdagépeket használó kisebb fürtöket használjon. 

## <a name="private-clouds"></a>Magánfelhők

A privát felhők dedikált, operációs rendszer nélküli Azure-gazdagépekkel létrehozott vSAN-fürtöket tartalmaznak. Mindegyik privát felhő több, azonos vCenter-kiszolgálóval és NSX-T kezelővel felügyelt fürttel is rendelkezhet. A privát felhők üzembe helyezése és kezelése a portálon, a CLI-ben vagy a PowerShellben végezhető el. 

Más erőforrásokhoz hasonlóan a privát felhők telepítése és felügyelete egy Azure-előfizetésen belül történik. Az előfizetésen belüli privát felhők száma méretezhető. Kezdetben egy privát felhő korlátja van egy előfizetéshez.

## <a name="clusters"></a>Fürtök
Minden létrehozott privát felhőhöz alapértelmezés szerint egy vSAN-fürt van. A fürtöket a Azure Portal vagy az API segítségével adhatja hozzá, törölheti és méretezheti.  Az összes fürt alapértelmezett mérete három gazdagép, és akár 16 gazdagép is méretezhető.  A fürtben használt gazdagépeknek azonos típusúnak kell lenniük.

A próbaverziós fürtök kiértékelésre és három gazdagépre korlátozódnak. Privát felhőben egyetlen próbaverziós fürt van. A próbaidőszak alatt egyetlen gazdagépen is méretezheti a próbaverziós fürtöt.

A vSphere és a NSX-T Manager használatával kezelheti a fürtkonfiguráció vagy a műveletnek a legtöbb egyéb aspektusát. A fürt minden gazdagépének helyi tárterülete a vSAN felügyelete alatt áll.

## <a name="hosts"></a>Hosts

Az Azure VMware-megoldás saját felhőalapú fürtök Hyper-konvergens, operációs rendszer nélküli infrastruktúra-csomópontokat használnak. A következő táblázat a gazdagép RAM-, CPU-és lemezterület-kapacitását mutatja be. 

| Állomás típusa              |             CPU             |   Memória (GB)   |  vSAN NVMe gyorsítótárazási szintje (TB, nyers)  |  vSAN SSD kapacitási szintje (TB, nyers)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End ()          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

A fürtök létrehozásához vagy méretezéséhez használt gazdagépek a gazdagépek elkülönített készletéből származnak. Ezek a gazdagépek hardveres teszteket adtak át, és minden adattal biztonságosan törölve lettek. 

## <a name="vmware-software-versions"></a>VMware-szoftverek verziói

Az Azure VMware-megoldás saját felhőalapú fürtjében használt VMware-szoftver aktuális szoftververzió a következők:

| Szoftverek              |    Verzió   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| vSAN                  |    6,7 U3    |
| NSX – T                 |      2.5     |

A privát felhőben lévő összes új fürt esetében a szoftver verziója megegyezik a jelenleg futó verzióval. Egy előfizetéshez tartozó új privát felhő esetén a szoftveres verem legújabb verziója lesz telepítve.

A frissítéssel kapcsolatos [fogalmakat](concepts-upgrades.md) ismertető cikkben ismertetett, az Azure VMware Solution platform szoftverre vonatkozó általános frissítési szabályzatok és folyamatok találhatók.

## <a name="host-maintenance-and-lifecycle-management"></a>Gazdagép-karbantartás és életciklus-kezelés

A gazdagép-karbantartási és életciklus-kezelés nincs hatással a saját Felhőbeli fürtök kapacitására vagy teljesítményére.  Az automatizált gazdagépek karbantartására például a belső vezérlőprogram frissítése és a hardver javítása vagy cseréje tartozik.

A Microsoft felelős a NSX-T készülékek életciklus-kezelésért, például a NSX-T Manager és a NSX-T Edge. Emellett a hálózati konfiguráció rendszerindításához is felelősek, például a 0. rétegbeli átjáró létrehozása és a North-South útválasztás engedélyezése. Ön felelős a NSX-T SDN-konfigurációért. Ilyenek például a hálózati szegmensek, az elosztott tűzfalszabályok, az 1. rétegbeli átjárók és a terheléselosztó.

> [!IMPORTANT]
> Ne módosítsa a NSX-T Edge vagy a 0. rétegbeli átjáró konfigurációját, mivel ez a szolgáltatás elvesztését eredményezheti.

## <a name="backup-and-restoration"></a>Biztonsági mentés és helyreállítás

A Private Cloud vCenter és a NSX-T konfigurációk óránkénti biztonsági mentési ütemtervtel rendelkeznek.  A biztonsági mentések három napig tartanak. Ha biztonsági másolatból kell visszaállítani, nyisson meg egy [támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support) a Azure Portal a visszaállítás kéréséhez.

## <a name="next-steps"></a>További lépések

A következő lépés a [Hálózatkezelés és az összekapcsolási fogalmak](concepts-networking.md)megismerése.

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

