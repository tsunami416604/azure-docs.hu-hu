---
title: Magas rendelkezésre állás és vészhelyreállítás recovery az Azure Kubernetes Service (AKS)
description: Ismerje meg, hogy egy fürt operátor ajánlott eljárások az alkalmazások magas rendelkezésre állás biztosítása és vészhelyreállítás az Azure Kubernetes Service (AKS) előkészítése rendelkezésre állás elérése érdekében.
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 4afc1231e6c9fa49c04c7bf6dfe26ee5eb87cc31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475163"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások az üzleti folytonossági és vészhelyreállítási recovery az Azure Kubernetes Service (AKS)

Kezelheti a fürtöket az Azure Kubernetes Service (AKS), az alkalmazások hasznos üzemideje lesz fontos. Az AKS egy rendelkezésre állási csoportban több csomópont a magas rendelkezésre állást biztosít. De ezek több csomópont nem a rendszer egy régiót érintő hibákkal szembeni. A hasznos üzemidő maximalizálása tervez, üzleti folytonosság fenntartásához és felkészülés vészhelyreállításra.

Ez a cikk az üzletmenet-folytonosság és vészhelyreállítás az aks-ben tervezéséről összpontosít. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Tervezze meg az AKS-fürt több régióban.
> * Az Azure Traffic Manager több fürt közötti forgalom irányítása.
> * A kép tárolójegyzékek georeplikációt használja.
> * Tervezze meg a alkalmazásállapot több fürt között.
> * Tároló replikálása több régióban.

## <a name="plan-for-multiregion-deployment"></a>Többrégiós üzembe helyezés tervezése

**Ajánlott eljárás**: Több AKS-fürt központi telepítése esetén válassza ki a régióban, ahol az AKS érhető el, és a párosított régiókat használnak.

Egy AKS-fürtöt helyezünk üzembe egy adott régióban. A rendszer régió hibákkal szembeni, különböző régióban üzembe helyezheti az alkalmazást több AKS-fürt. Ha azt tervezi, hogy hol helyezi üzembe az AKS-fürt, vegye figyelembe:

* [**AKS-régiók rendelkezésre állása**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Válassza ki a felhasználók közel régióban. AKS új régiókba folyamatosan bővül.
* [**Azure – párosított régiók**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): A földrajzi területen válassza a két régióban, amely a rendszer megfeleltetni egymással. Párosított régiók platform frissítéseket koordinálja, és priorizálhatja helyreállítási erőfeszítések ahol szükséges.
* **Szolgáltatás rendelkezésre állása**: Döntse el, hogy a párosított régiókat hot/ritkáról gyakori elérésű, gyakori/közepes vagy forró/hideg legyen-e. Egy időben, az egyik régió mindkét régióban futtatni kívánt *készen* forgalmat bonyolító elindítani? Vagy az nincs ideje Felkészülés a forgalmat az egyik régió?

AKS-régiók rendelkezésre állása és a társított két régió bármelyikén rendszer közös veszi figyelembe. A párosított régiók, amelyek együtt kezelheti a vészhelyreállítási régióban az AKS-fürtök üzembe helyezése. Ha például az AKS érhető el az USA keleti RÉGIÓJA és USA nyugati RÉGIÓJA. Ezekben a régiókban vannak párosítva. Válassza ki a két régióra, az AKS BC/Vészhelyreállítási stratégia létrehozásakor.

Az alkalmazás központi telepítése esetén hozzá ezek több AKS-fürt központi telepítése a CI/CD folyamatot egy újabb lépést. Ha nem frissíti a központi telepítési folyamatok, alkalmazások előfordulhat, hogy üzembe helyezve a régiók és az AKS-fürtök közül csak egyet. Ügyfél adatforgalom átirányításához egy másodlagos régióba nem kap a legújabb kódot.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Forgalom irányítása az Azure Traffic Manager használatával

**Ajánlott eljárás**: Az Azure Traffic Manager ügyfél a legközelebbi AKS-fürt és az alkalmazás-példányra irányíthatók. A legjobb teljesítményt és a redundancia a minden alkalmazás forgalom Traffic Manageren keresztül közvetlen előtt kerül az AKS-fürt.

Ha különböző régiókban lévő több AKS-fürtöt, a Traffic Manager segítségével szabályozhatja, hogy az alkalmazások minden egyes fürt futtatására a forgalom adatfolyamait. [Az Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) egy DNS-alapú forgalom terheléselosztó, amely képes elosztani a hálózati forgalmat a régiók között elosztva. Használja a Traffic Manager útválasztási felhasználók fürt válaszidő alapján, vagy földrajzi hely alapján.

![Az AKS a Traffic Managerrel](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Általában egy AKS-fürtön rendelkező ügyfelek csatlakozni egy adott alkalmazás szolgáltatás IP- vagy DNS-neve. Multicluster telepítésben lévő ügyfelek csatlakozzon egy Traffic Manager DNS-nevet, amely a szolgáltatásokat a minden egyes AKS-fürtöt mutat. Ezek a szolgáltatások megadása a Traffic Manager-végpontok segítségével. Minden végpont a *terheléselosztó IP-szolgáltatás*. Használja ezt a konfigurációt a hálózati forgalmat az egy adott régióban Traffic Manager-végpontot a végponthoz, egy másik régióban.

![Földrajzi útválasztási Traffic Manager használatával](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

A TRAFFIC Manager DNS-keresések hajt végre, és adja vissza egy felhasználó leginkább megfelelő végpontra. Beágyazott profilok rangsorolhatók is egy elsődleges helyen. Felhasználók például általában csatlakozzon a legközelebbi földrajzi régiót. Adott régióban működik, ha a Traffic Manager inkább irányítja a felhasználóknak, hogy egy másodlagos régióba. Ez a megközelítés biztosítja az ügyfelek kapcsolódhatnak alkalmazáspéldány, még akkor is, ha a legközelebbi földrajzi régióban nem érhető el.

A végpontok és útválasztási beállításáról további információért lásd: [a földrajzi forgalom-útválasztási módszer konfigurálása a Traffic Manager használatával](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>7\. rétegbeli alkalmazás útválasztás az Azure bejárati ajtajának szolgáltatás

A TRAFFIC Manager DNS (3. réteg) alakzat forgalom használ. [Az Azure bejárati ajtajának szolgáltatás](https://docs.microsoft.com/azure/frontdoor/front-door-overview) HTTP/HTTPS (7. réteg) útválasztási lehetőséget biztosít. Az Azure bejárati ajtajának Service további szolgáltatásai többek között, SSL megszüntetése, az egyéni tartomány, webalkalmazás-tűzfal, URL-újraíró és munkamenet-affinitás. Tekintse át, melyik megoldás a legmegfelelőbb van az alkalmazás forgalmának igényeit.

## <a name="enable-geo-replication-for-container-images"></a>A tárolórendszerképek georeplikáció engedélyezéséhez

**Ajánlott eljárás**: A tárolórendszerképek Store az Azure Container Registry és georeplikáció a beállításjegyzék egyes AKS régióba.

Üzembe helyezéséhez, és az alkalmazások futtatásához az aks-ben, kell tárolni, és kérni a tárolórendszerképeket. Container Registry integrálható az aks-sel, így biztonságosan tárolhatja a tárolórendszerképek vagy a Helm-diagramok. Container Registry támogatja a több főkiszolgálós georeplikációs automatikusan replikálhatók a rendszerképek a világ különböző pontjain található Azure-régióban. 

Jobb teljesítmény és rendelkezésre állás érdekében a Container Registry georeplikációs használatával hozzon létre egy beállításjegyzéket az egyes régiókban, amelyekben egy AKS-fürtöt. Minden egyes AKS-fürt tárolórendszerképek majd lekéri a helyi tároló-beállításjegyzékből ugyanabban a régióban:

![Container Registry georeplikációja tárolórendszerképekhez](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Container Registry georeplikációs lekéréses lemezképek használja az ugyanabban a régióban, az eredmények a következők:

* **Gyorsabb**: Lekéri képeket nagy sebességű, alacsony késleltetésű hálózati kapcsolatokat ugyanazon Azure-régióban.
* **További megbízható**: Ha egy régió elérhetetlenné válik, az AKS-fürt lekéri a képek egy elérhető tárolóregisztrációs adatbázisból.
* **Olcsóbb**: Nem jár hálózati kimenő forgalom adatközpontok között.

Georeplikációs funkciója *prémium* tároló-beállításjegyzékek Termékváltozat. Georeplikáció konfigurálása az információkért lásd: [Container Registry georeplikációs](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Távolítsa el a szolgáltatási állapot, a tárolókon belül

**Ajánlott eljárás**: Ahol lehetséges, ne tároljon szolgáltatás állapotát a tárolóban. Ehelyett használja az Azure-platform (PaaS) szolgáltatás, amely támogatja a többrégiós replikáció.

*Szolgáltatás állapota* a memóriában vagy a lemezen lévő adatok függvény feltétele, hogy egy szolgáltatás hivatkozik. Állapot a datové struktury és a tagváltozók, amely a szolgáltatás olvasási és írási tartalmazza. Attól függően, hogy a szolgáltatás lett tervezve az állapot is tartalmazhatnak, fájlokat vagy más erőforrásokat, amelyek a lemezen tárolja. Például az állapot tartalmazhatnak a fájlokat egy adatbázis adatait és a tranzakciós naplók tárolására használ.

Állam vagy externalized vagy a kódot, amely kezeli az állapot a felhőátjárónak. Állapot általában adatbázis vagy más adattárolóbeli tárolására folyamaton kívül ugyanazon a gépen futó vagy különböző gépeken a hálózaton futó használatával externalize.

Tárolók és mikroszolgáltatások leginkább rugalmas, amikor a bennük lévő futó folyamatok nem is megőrzik állapotukat. Alkalmazások szinte mindig tartalmaz néhány állapot, mert használja egy PaaS-megoldások, például az Azure Database for MySQL, Azure Database for PostgreSQL, vagy az Azure SQL Database.

Hordozható alkalmazásokat hozhat létre, tekintse meg a következő irányelveket:

* [A 12 tényezős alkalmazásfejlesztési módszertan](https://12factor.net/)
* [Webalkalmazás futtatása több Azure-régióban](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Hozzon létre egy tárolási migrálási tervet.

**Ajánlott eljárás**: Ha az Azure Storage szolgáltatást használja, előkészítése, és a tároló áttelepítése a biztonsági mentési régió az elsődleges régióból történő teszteléséhez.

Az alkalmazások az Azure Storage használható az adatokat. Mivel az alkalmazások vannak elosztva a különböző régiókban lévő több AKS-fürt, a storage szinkronizálva tartsa kell. Az alábbiakban a tárolók replikálásához közös kétféleképpen:

* Az aszinkron replikáció infrastruktúra-alapú
* Alkalmazás-alapú aszinkron replikáció

### <a name="infrastructure-based-asynchronous-replication"></a>Az aszinkron replikáció infrastruktúra-alapú

Az alkalmazások egy pod törlése után is szükség lehet adattárolásra. A Kubernetes köteteket is használhat állandó adattároló megőrzéséhez. Állandó kötetek csatlakoztatva van a virtuális gép csomóponthoz és majd kitéve a podokat. Tartós kötet podok hajtsa végre, akkor is, ha a podok ugyanazon a fürtön belül egy másik csomópontra kerül át.

A replikációs stratégiát használja attól függ, hogy a tárolási megoldás. Általános tárolási megoldások, például [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [bástya](https://rook.io/docs/rook/master/disaster-recovery.html), és [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) adja meg a saját vész-helyreállítási útmutatást és a replikáció.

A tipikus stratégia, hogy egy közös tárolási pontot, ahol az alkalmazások írhat adataikat. Ezek az adatok régiók közötti replikálására, hogy és majd a helyileg elérhető.

![Az aszinkron replikáció infrastruktúra-alapú](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Ha az Azure Managed Disks szolgáltatást, választhatja a replikáció és DR megoldások, például a következő:

* [Az Azure-ban Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Alkalmazás-alapú aszinkron replikáció

Kubernetes jelenleg itt nem natív végrehajtására az aszinkron replikáció alkalmazásalapú. Mivel a tárolók és a Kubernetes lazán vannak összekapcsolva, bármilyen alkalmazás vagy nyelvi hagyományos megközelítés működnie kell. Általában a maguk az alkalmazások a tárolási kérelmek, majd minden egyes fürt mögöttes adattárolási írt replikálni.

![Alkalmazás-alapú aszinkron replikáció](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>További lépések

Ez a cikk az üzletmenet-folytonossági és vészhelyreállítási szempontok az AKS-fürtök összpontosít. Az AKS-fürt műveletekkel kapcsolatos további információkért tekintse meg az alábbi ajánlott eljárásokkal kapcsolatos cikkek:

* [Több-bérlős és a fürt elkülönítés][aks-best-practices-cluster-isolation]
* [Kubernetes-ütemezési alapszintű funkciók][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
