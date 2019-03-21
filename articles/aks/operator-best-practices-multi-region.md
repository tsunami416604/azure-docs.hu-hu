---
title: Operátor ajánlott eljárások – magas rendelkezésre állás és vészhelyreállítás az Azure Kubernetes-szolgáltatások (AKS)
description: A fürt operátor ajánlott eljárásokat, a rendelkezésre állás az alkalmazások magas rendelkezésre állást biztosít, és készítse elő a vész-helyreállítási helyzetekben az Azure Kubernetes-szolgáltatások (AKS)
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 926f470b8a4dbdb6d6cbfe09ee61349a819600e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098627"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások az üzleti folytonossági és vészhelyreállítási recovery az Azure Kubernetes Service (AKS)

Kezelheti a fürtöket az Azure Kubernetes Service (AKS), az alkalmazások hasznos üzemideje lesz fontos. Az AKS egy rendelkezésre állási csoportban több csomópont a magas rendelkezésre állást biztosít. Ezek a csomópontok több nem egy régió meghibásodása elleni. A hasznos üzemidő maximalizálása néhány üzletmenet-folytonossági és a vész-helyreállítási funkciók megvalósításához.

A gyakorlati tanácsok cikk összpontosít, amelyek segítségével szempontok tervezése az üzleti folytonossági és vészhelyreállítási helyreállítási az aks-ben. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Több régióban az AKS-fürtök tervezése
> * Az Azure Traffic Managerrel több fürt közötti
> * A kép tárolójegyzékek georeplikációs használata
> * Alkalmazás állapota, között több fürt tervezése
> * Tároló replikálása több régióban

## <a name="plan-for-multi-region-deployment"></a>Többrégiós üzembe helyezés tervezése

**Ajánlott eljárásokkal kapcsolatos útmutatás** – több AKS-fürt központi telepítésekor válassza ki a régióban, ahol az AKS érhető el, és a párosított régiókat használnak.

Egy AKS-fürtöt helyezünk üzembe egy adott régióban. Saját kezűleg régió hibákkal szembeni, különböző régióban üzembe helyezheti az alkalmazást több AKS-fürt. Ha azt tervezi, hogy mely régiókban az AKS-fürt üzembe helyezéséhez, a következő szempontokat kell figyelembe venni:

* [AKS-régiók rendelkezésre állása](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * Válassza ki a felhasználók közel régióban. Az AKS folyamatosan bővíti új régiókba.
* [Az Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * A földrajzi területen válassza a két régióban, amely a rendszer megfeleltetni egymással. Ezekben a régiókban platform frissítéseket koordinálja, és priorizálhatja helyreállítási erőfeszítések ahol szükséges.
* Rendelkezésre állási szolgáltatásszintet (forró/ritkáról gyakori elérésű, gyakori/meleg, forró/hideg)
  * Egy időben, az egyik régió mindkét régióban futtatni kívánt *készen* forgalom, vagy egy régióban, amely kész forgalmat kell elindítani.

AKS-régiók rendelkezésre állása és a társított két régió bármelyikén közös veszi figyelembe. A párosított régiók, amelyek együtt kezelheti a vészhelyreállítási régióban az AKS-fürtök üzembe helyezése. Ha például az AKS érhető el *USA keleti Régiójában* és *USA nyugati RÉGIÓJA*. Ezekben a régiókban is van párosítva. A két régióra szeretné azt javasolja, az AKS BC/Vészhelyreállítási stratégia létrehozásakor.

Az alkalmazás központi telepítése esetén hozzá kell adnia is egy újabb lépést a CI/CD-folyamat ezek több AKS-fürt központi telepítése. Ha nem frissíti a központi telepítési folyamatok, alkalmazás-KözpontiTelepítés csak is üzembe helyezhetők, a régiók és az AKS-fürtök. Ügyfél adatforgalom átirányításához egy másodlagos régióba nem kap a legújabb kódot.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Forgalom irányítása az Azure Traffic Manager használatával

**Ajánlott eljárásokkal kapcsolatos útmutatás** – az Azure Traffic Manager ügyfél a legközelebbi AKS-fürt és az alkalmazás-példányra irányíthatók. A legjobb teljesítményt és a redundancia közvetlen majd lépjen az AKS-fürt összes alkalmazás forgalom Traffic Manageren keresztül.

A különböző régiókban lévő több AKS fürthöz kell vezérelheti, hogyan átirányítja a forgalmat az egyes fürtök környezetben futó alkalmazásokhoz. [Az Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) egy DNS-alapú forgalom terheléselosztó, amely képes elosztani a hálózati forgalmat a régiók között elosztva. Akkor is irányíthatja a felhasználókat a fürt válaszidő alapján, vagy földrajzi hely alapján.

![Az AKS az Azure Traffic Managerrel](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Egy AKS-fürtöt, az ügyfelek általában csatlakozni a *szolgáltatás IP* vagy egy adott alkalmazás DNS-nevét. Több fürt telepítésben lévő ügyfelek csatlakozzon egy Traffic Manager DNS-nevet, amely a szolgáltatásokat a minden egyes AKS-fürtöt mutat. Ezek a szolgáltatások vannak meghatározva a Traffic Manager-végpontok használata. Minden végpont a *szolgáltatás terheléselosztó IP*. Ez a konfiguráció lehetővé teszi a hálózati forgalmat egy adott régióban a Traffic Manager-végpont az a végpont egy másik régióban.

![Földrajzi útválasztási az Azure Traffic Managerrel](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

A TRAFFIC Manager segítségével hajtsa végre a DNS kereséseket, és adja vissza egy felhasználó számára a leginkább megfelelő végpontra. Beágyazott profilok is használható, az elsőbbséget adva egy elsődleges helyen. A felhasználó például elsősorban csatlakozzon a legközelebbi földrajzi régiót. Ha az adott régióban működik, a Traffic Manager inkább szólítja fel őket egy másodlagos régióba. Ez a megközelítés biztosítja, hogy az ügyfél bármikor csatlakozhat egy alkalmazáspéldány akkor is, ha a legközelebbi földrajzi régióban nem érhető el.

A végpontok és útválasztási, lásd: útmutatást [a földrajzi forgalom-útválasztási módszer használatával a Traffic Manager konfigurálása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door"></a>7. rétegbeli alkalmazás útválasztás az Azure bejárati ajtó

Az Azure Traffic Manager DNS (3. réteg) alakzat forgalom használ. [(Jelenleg előzetes verzióban érhető el) az Azure bejárati ajtajának](https://docs.microsoft.com/azure/frontdoor/front-door-overview) HTTP/HTTPS (7. réteg) útválasztási lehetőséget biztosít. További bejárati ajtó jellemzők SSL megszüntetése, az egyéni tartomány, webalkalmazás-tűzfal, URL-újraíró és munkamenet-affinitás.

Tekintse át, melyik megoldás a legmegfelelőbb van az alkalmazás forgalmának igényeit.

## <a name="enable-geo-replication-for-container-images"></a>A tárolórendszerképek georeplikáció engedélyezéséhez

**Ajánlott eljárásokkal kapcsolatos útmutatás** -Store a tárolórendszerképek az Azure Container Registry és georeplikáció a beállításjegyzék egyes AKS régióba.

Üzembe helyezéséhez, és az alkalmazások futtatásához az aks-ben, kell tárolni, és kérni a tárolórendszerképeket. Az Azure Container Registry (ACR) integrálható az aks-sel való biztonságos tárolása a tárolórendszerképek vagy a Helm-diagramok. ACR automatikusan replikálhatók a rendszerképek az Azure-régiók világszerte több főkiszolgálós georeplikációs támogatja. A jobb teljesítmény és rendelkezésre állás érdekében ACR georeplikáció használatával hozzon létre egy beállításjegyzéket az egyes régiókban, amelyekben egy AKS-fürtöt. AKS-fürt minden egyes majd lekéri tárolórendszerképek ugyanabban a régióban a helyi ACR-beállításjegyzékbe:

![Az Azure Container Registry georeplikációs tárolórendszerképekhez](media/operator-best-practices-bc-dr/acr-geo-replication.png)

ACR georeplikáció használatának előnyei a következők:

* **Gyorsabb stahují se Image. az ugyanabban a régióban.** Lekéri a nagy sebességű, alacsony késleltetésű képeket hálózati kapcsolatokat ugyanazon Azure-régióban.
* **További megbízható stahují se Image. az ugyanabban a régióban.** Ha egy régió elérhetetlenné válik, az AKS-fürt lekéri a kép egy másik ACR-beállításjegyzékbe, amely továbbra is elérhető.
* **Olcsóbb stahují se Image. az ugyanabban a régióban.** Nem jár hálózati kimenő forgalom adatközpontok között.

Georeplikációs funkciója *prémium* beállításjegyzékekhez ACR Termékváltozat. A replikáció konfigurálása lépéseiért lásd: [Azure Container Registry georeplikációja](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>Távolítsa el a szolgáltatási állapot, a tárolókon belül

**Ajánlott eljárásokkal kapcsolatos útmutatás** – ahol lehetséges, ne tároljon a szolgáltatás állapotát a tárolóban. Ehelyett használjon, amelyek támogatják a többrégiós replikáció az Azure PaaS-szolgáltatások.

Szolgáltatás állapota a memóriában vagy a lemezen lévő adatok függvény feltétele, hogy egy szolgáltatás hivatkozik. Állapot a datové struktury és a tagváltozók, amely a szolgáltatás olvasási és írási tartalmazza. Attól függően, hogy a szolgáltatás lett tervezve az állapot is tartalmazhat, fájlokat vagy más erőforrásokat, amelyek tárolása a lemezen. Például a fájlok egy adatbázist használja adatok és a tranzakciós naplók tárolására.

Állam vagy externalized vagy védelmicsoport-készletek a kóddal, amely az az állapot kezelésére. Állapot externalizációja általában adatbázis vagy más adattárolóbeli tárolására, a hálózaton keresztül, vagy ugyanazon a gépen folyamaton kívül különböző gépeken futó használatával történik.

Tárolók és mikroszolgáltatások akkor leginkább rugalmas, ha a bennük lévő futó folyamatok nem őriz meg az állapotát. Az alkalmazások szinte mindig tartalmaz néhány állapotát, használja Platform Szolgáltatottszoftver-megoldás, például az Azure Database for MySQL/Postgres vagy az Azure SQL.

A, hogy hogyan hozhat létre alkalmazásokat, amelyek könnyebben hordozható, lásd az alábbi irányelveket:

* [A tizenkét tényezős alkalmazás módszertan](https://12factor.net/).
* [Webalkalmazás futtatása több Azure-régióban](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Hozzon létre egy tárolási migrálási tervet.

**Ajánlott eljárásokkal kapcsolatos útmutatás** – Ha az Azure tárolás használata előkészítéséhez és az elsődleges kiszolgálóról a tároló áttelepítése a biztonsági mentési régió teszteléséhez.

Az alkalmazások felhasználhatja az Azure Storage számára, amelyek adataihoz. Mivel az alkalmazások vannak elosztva a különböző régiókban lévő több AKS-fürt, kell a tároló szinkronizálva tartsa. Két gyakori módon történő replikálásához használt tárolási többek között a következő módszerek:

* Alkalmazás-alapú aszinkron replikáció
* Az aszinkron replikáció infrastruktúra-alapú

### <a name="infrastructure-based-asynchronous-replication"></a>Az aszinkron replikáció infrastruktúra-alapú

Az alkalmazások egy pod törlése után is szükség lehet adattárolásra. A Kubernetes köteteket is használhat állandó adattároló megőrzéséhez. Ezek állandó kötetek csomópontra virtuális gép csatlakoztatva van, és majd kitéve a podokat. Állandó kötetek podok, hajtsa végre, akkor is, ha egy másik csomópont ugyanazon a fürtön belüli áthelyezése a pod.

A tárolási megoldás használatát replikálási gyakorlata eltérő is lehet. Általános tárolási megoldások, például [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [bástya](https://rook.io/docs/rook/master/disaster-recovery.html), és [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) minden rendelkezik saját útmutatást.

A központi módszer az alkalmazások számára az adatok írása egy közös tároló pont. Ezek az adatok régiók közötti replikálására, hogy és majd a helyileg elérhető.

![Az aszinkron replikáció infrastruktúra-alapú](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Az Azure Managed Disks szolgáltatást, ha elérhető replikáció és DR megoldások közé tartozik a következő módszerek egyikével:

* [Az Azure-ban Ark](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Alkalmazás-alapú aszinkron replikáció

Jelenleg nem nem Kubernetes natív végrehajtására az aszinkron replikáció alkalmazásalapú. A tárolók és a Kubernetes lazán kapcsolódó jellegét bármilyen alkalmazás vagy nyelvi hagyományos megközelítés működnie kell. A központi módszer van maguk az alkalmazások a tárolási kérelmeket, majd minden egyes fürt írt replikálni a mögöttes adattárolási.

![Alkalmazás-alapú aszinkron replikáció](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>További lépések

Ez a cikk összpontosított üzletmenet-folytonossági és vészhelyreállítási kapcsolatos szempontok az AKS-fürtben. Az AKS-fürt műveletekkel kapcsolatos további információkért tekintse meg az alábbi gyakorlati tanácsokat:

* [Több-bérlős és a fürt elkülönítés][aks-best-practices-cluster-isolation]
* [Kubernetes-ütemezési alapszintű funkciók][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
