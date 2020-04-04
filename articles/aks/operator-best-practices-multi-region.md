---
title: Magas rendelkezésre állás és vészhelyreállítás az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg a fürtüzemeltető ajánlott eljárásokat az alkalmazások maximális rendelkezésre állásának eléréséhez, a magas rendelkezésre állás biztosításához és a vészhelyreállításelőkészítéséhez az Azure Kubernetes szolgáltatásban (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 6aff60cbc4a4cab557e6e202ea1181d5b20bbd20
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655868"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) üzleti folytonosságával és vész-helyreállításával kapcsolatos gyakorlati tanácsok

A fürtök kezelése az Azure Kubernetes-szolgáltatás (AKS) kezelése során fontossá válik az alkalmazások rendelkezésre állása. Alapértelmezés szerint az AKS magas rendelkezésre állást biztosít a [virtuálisgép-méretezési csoport (VMSS)](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)több csomópontjának használatával. De ezek a több csomópont nem védi meg a rendszert a régió meghibásodása. Az állásidő maximalizálása érdekében tervezze meg előre az üzletmenet folytonosságának fenntartását és a vészhelyreállításra való felkészülést.

Ez a cikk az üzletmenet folytonosságának és a vészhelyreállításnak az AKS-ben történő megtervezésére összpontosít. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az AKS-fürtök tervezése több régióban.
> * Több fürt közötti forgalmat irányítson az Azure Traffic Manager használatával.
> * Georeplikáció használata a tárolórendszerkép-jegyzékekhez.
> * Tervezze meg az alkalmazás állapotát több fürtben.
> * Több régióban replikálja a tárhelyet.

## <a name="plan-for-multiregion-deployment"></a>Több régiós telepítés megtervezése

**Ajánlott eljárás:** Ha több AKS-fürtöt telepít, válassza ki azokat a régiókat, ahol az AKS elérhető, és használjon párosított régiókat.

Egy AKS-fürt egyetlen régióban van telepítve. A régiómeghibásodás elleni védelem érdekében telepítse az alkalmazást több AKS-fürtre különböző régiókban. Ha azt tervezi, hogy hol telepíti az AKS-fürtöt, vegye figyelembe a következőket:

* [**AKS-régió elérhetősége**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Válassza ki a felhasználókhoz közeli régiókat. Az AKS folyamatosan terjeszkedik új régiókban.
* [**Azure-ban párosított régiók:**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)A földrajzi terület, válasszon ki két régiót, amelyek párosítva vannak egymással. A párosított régiók összehangolják a platformfrissítéseket, és szükség esetén rangsorolják a helyreállítási erőfeszítéseket.
* **Szolgáltatás elérhetősége**: Döntse el, hogy a párosított régiók legyenek-e forró/meleg, meleg/meleg vagy meleg/hideg. Mindkét régiót egyszerre szeretné futtatni, és egy régió *készen áll* a forgalom kiszolgálására? Vagy azt szeretné, hogy egy régiónak legyen ideje felkészülni a forgalom kiszolgálására?

Az AKS-régió rendelkezésre állása és a párosított régiók közös szempontot jelentenek. Az AKS-fürtöket olyan párosított régiókba helyezheti üzembe, amelyek a régióvész-helyreállítás együttes kezelésére szolgálnak. Az AKS például az USA keleti és az USA nyugati részén érhető el. Ezek a régiók párosítva vannak. Válassza ki ezt a két régiót, amikor AKS BC/DR stratégiát hoz létre.

Az alkalmazás üzembe helyezésekor adjon hozzá egy újabb lépést a CI/CD-folyamathoz, hogy ezekre a több AKS-fürtre telepítse. Ha nem frissíti a központi telepítési folyamatokat, előfordulhat, hogy az alkalmazások csak az egyik régióban és az AKS-fürtökben vannak telepítve. A másodlagos régióba irányított ügyfélforgalom nem kapja meg a legújabb kódfrissítéseket.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Forgalom irányítása az Azure Traffic Manager használatával

**Ajánlott eljárás:** Az Azure Traffic Manager az ügyfeleket a legközelebbi AKS-fürthöz és alkalmazáspéldányhoz irányíthatja. A legjobb teljesítmény és redundancia érdekében irányítsa az összes alkalmazásforgalmat a Traffic Manageren keresztül, mielőtt az AKS-fürthöz jutna.

Ha több AKS-fürtöt is tartalmaz különböző régiókban, a Traffic Manager segítségével szabályozhatja, hogy az egyes fürtökben futó alkalmazások hogyan áramlanak a forgalom. [Az Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) egy DNS-alapú terheléselosztó, amely képes elosztani a hálózati forgalmat a régiók között. A Traffic Manager segítségével a felhasználókat a fürt válaszideje vagy a földrajzi elhelyezkedés alapján irányítsa.

![AKS a Traffic Managerrel](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Azok az ügyfelek, akik egyetlen AKS-fürttel rendelkeznek, általában egy adott alkalmazás IP-címéhez vagy DNS-nevéhez csatlakoznak. Többfürtös telepítés esetén az ügyfeleknek csatlakozniuk kell egy Traffic Manager DNS-névhez, amely az egyes AKS-fürtök szolgáltatásaira mutat. Ezeket a szolgáltatásokat a Traffic Manager-végpontok használatával határozhatja meg. Minden végpont a *szolgáltatás terheléselosztó IP.* Ezzel a konfigurációval irányíthatja a hálózati forgalmat a Traffic Manager-végpontegy régióban a végpont egy másik régióban.

![Földrajzi útválasztás a Traffic Manageren keresztül](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

A Traffic Manager DNS-kereséseket hajt végre, és a felhasználó legmegfelelőbb végpontját adja vissza. A beágyazott profilok rangsorolhatják az elsődleges helyet. A felhasználóknak például általában a legközelebbi földrajzi régiójukhoz kell kapcsolódniuk. Ha a régió problémája van, a Traffic Manager ehelyett irányítja a felhasználókat egy másodlagos régióba. Ez a megközelítés biztosítja, hogy az ügyfelek akkor is csatlakozhatnak egy alkalmazáspéldányhoz, ha a legközelebbi földrajzi régiójuk nem érhető el.

A végpontok és az útválasztás beállításáról [a Földrajzi forgalom útválasztási módszerének konfigurálása a Traffic Manager használatával című](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)témakörben talál további információt.

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Layer 7 alkalmazásútválasztás az Azure Bejárati ajtó szolgáltatással

A Traffic Manager a DNS (3. réteg) segítségével alakítja ki a forgalmat. [Az Azure Bejárati ajtó szolgáltatás](https://docs.microsoft.com/azure/frontdoor/front-door-overview) http/HTTPS (7. réteg) útválasztási beállítást biztosít. Az Azure Front Door Service további funkciói közé tartozik a TLS-megszüntetés, az egyéni tartomány, a webalkalmazás tűzfala, az URL-újraírás és a munkamenet-affinitás. Tekintse át az alkalmazásforgalom igényeit, hogy megértse, melyik megoldás a legmegfelelőbb.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Régiók összekapcsolása a globális virtuális hálózati társviszony-létesítéssel

Ha a fürtöknek beszélniük kell egymással, a két virtuális hálózat egymáshoz való csatlakoztatása [virtuális hálózati társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)útján érhető el. Ez a technológia összeköti a virtuális hálózatokat egymással, így nagy sávszélességet biztosít a Microsoft gerinchálózatán, még a különböző földrajzi régiókközött is.

Az AKS-fürtöket futtató virtuális hálózatok társviszony-létesítésének előfeltétele a szabványos terheléselosztó használata az AKS-fürtben, hogy a Kubernetes-szolgáltatások elérhetők a virtuális hálózat társviszony-létesítésén keresztül.

## <a name="enable-geo-replication-for-container-images"></a>Tárolólemezképek georeplikációjának engedélyezése

**Ajánlott eljárás:** A tárolórendszerképeket az Azure Container Registry tárolja, és georeplikálja a beállításjegyzéket az egyes AKS-régiókban.

Az alkalmazások AKS-ben való üzembe helyezéséhez és futtatásához szüksége van a tárolórendszerképek tárolásának és lekérése. A Container Registry integrálható az AKS-sel, így biztonságosan tárolhatja a tárolórendszerképeket vagy a Helm-diagramokat. A Container Registry támogatja a többfős georeplikációt, hogy a rendszerképek automatikusan replikálhatók az Azure-régiókba világszerte. 

A teljesítmény és a rendelkezésre állás javítása érdekében a Container Registry georeplikációjával hozzon létre egy beállításjegyzéket minden olyan régióban, ahol AKS-fürttalálható. Minden AKS-fürt lekéri a tárolórendszerképeket a helyi tároló beállításjegyzékéből ugyanabban a régióban:

![Tárolóbeállítás georeplikációja tárolólemezképekhez](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Ha a Container Registry georeplikációt használja a lemezképek lekérése ugyanabból a régióból, az eredmények a következők:

* **Gyorsabb:** Képeket kell lekérések nagy sebességű, alacsony késésű hálózati kapcsolatok ugyanabban az Azure-régióban.
* **Megbízhatóbb:** Ha egy régió nem érhető el, az AKS-fürt lekéri a rendszerképeket egy elérhető tároló beállításjegyzékből.
* **Olcsóbb:** Nincs hálózati kimenő forgalom díj adatközpontok között.

A georeplikáció a *prémium szintű* termékváltozat-tárolójegyzékek egyik szolgáltatása. A georeplikáció konfigurálásáról a [Container Registry georeplikációcímű](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)témakörben talál további információt.

## <a name="remove-service-state-from-inside-containers"></a>Szolgáltatásállapot eltávolítása a belső tárolókból

**Ajánlott eljárás:** Ahol lehetséges, ne tárolja a szolgáltatás állapotát a tárolóban. Ehelyett használjon egy Azure-platformot szolgáltatásként (PaaS), amely támogatja a többrégiós replikációt.

*A szolgáltatásállapota* a szolgáltatás működéséhez szükséges memórián belüli vagy lemezen lévő adatokra utal. Az állam tartalmazza a szolgáltatás által beolvasást és írást tartalmazó adatstruktúrákat és tagváltozókat. A szolgáltatás megtervezett állapotától függően az állapot fájlokat vagy más, a lemezen tárolt erőforrásokat is tartalmazhat. Az állapot tartalmazhat például azokat a fájlokat, amelyeket az adatbázis az adatok és a tranzakciós naplók tárolására használ.

Az állapot kiszervezhető vagy együtt helyezkedhet el az állapotot kezelő kóddal. Általában egy adatbázis vagy más adattár használatával, amely fut a különböző gépeken a hálózaton keresztül, vagy hogy elfogy a folyamat ugyanazon a gépen.

A tárolók és a mikroszolgáltatások akkor a legrugalmasabbak, ha a bennük futó folyamatok nem őrzik meg az állapotot. Mivel az alkalmazások szinte mindig tartalmaznak bizonyos állapotokat, használja a PaaS-megoldás, például az Azure Database for MySQL, az Azure Database for PostgreSQL vagy az Azure SQL Database.

Hordozható alkalmazások létrehozásához olvassa el az alábbi irányelveket:

* [A 12 faktoros alkalmazás módszertana](https://12factor.net/)
* [Webalkalmazás futtatása több Azure-régióban](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Tárolóáttelepítési terv létrehozása

**Ajánlott eljárás:** Ha az Azure Storage-t használja, készítse elő és tesztelje, hogyan telepítheti át a tárhelyet az elsődleges régióból a biztonsági mentési régióba.

Előfordulhat, hogy az alkalmazások az Azure Storage-t használják az adataikhoz. Mivel az alkalmazások több AKS-fürtközött vannak elosztva különböző régiókban, a tárolót szinkronizálni kell. A tárolók replikálásának két gyakori módja:

* Infrastruktúra-alapú aszinkron replikáció
* Alkalmazásalapú aszinkron replikáció

### <a name="infrastructure-based-asynchronous-replication"></a>Infrastruktúra-alapú aszinkron replikáció

Előfordulhat, hogy az alkalmazások állandó tárolást igényelnek a pod törlése után is. A Kubernetes, az állandó kötetek adattárolás megőrzése. Az állandó kötetek egy virtuális csomóponthoz vannak csatlakoztatva, majd a podok számára elérhetővé teszik őket. Az állandó kötetek akkor is követik a podokat, ha a podok ugyanabban a fürtön belül egy másik csomópontba kerülnek.

A használt replikációs stratégia a tárolási megoldástól függ. Közös tárolási megoldások, mint a [Gluster,](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/) [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Bástya](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html), és [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) saját útmutatást nyújt a katasztrófa utáni helyreállítás és a replikáció.

A tipikus stratégia az, hogy egy közös tárolási pont, ahol az alkalmazások írhatnak az adatokat. Ezeket az adatokat ezután régiók között replikálja, majd helyileg éri el.

![Infrastruktúra-alapú aszinkron replikáció](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Ha Azure felügyelt lemezeket használ, a replikációs és a VÉSZ-megoldásokat, például az alábbiakat választhatja:

* [Velero az Azure-ban](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Alkalmazásalapú aszinkron replikáció

A Kubernetes jelenleg nem biztosít natív implementációt az alkalmazásalapú aszinkron replikációhoz. Mivel a tárolók és a Kubernetes lazán összekapcsolt, minden hagyományos alkalmazás vagy nyelvi megközelítés működnie kell. Általában maguk az alkalmazások replikálják a tárolási kérelmeket, amelyeket aztán az egyes fürtök alapjául szolgáló adattárolóba írnak.

![Alkalmazásalapú aszinkron replikáció](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>További lépések

Ez a cikk az Üzletmenet-folytonosságra és az AKS-fürtök vész-helyreállítási szempontjaira összpontosít. Az AKS fürtműveleteiről az alábbi gyakorlati tanácsokról szóló cikkekben talál további információt:

* [Több-bérlős és fürtelkülönítés][aks-best-practices-cluster-isolation]
* [Egyszerű Kubernetes ütemező funkciók][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
