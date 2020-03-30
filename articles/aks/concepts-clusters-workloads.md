---
title: Fogalmak – Kubernetes alapjai az Azure Kubernetes-szolgáltatásokhoz (AKS)
description: Ismerje meg a Kubernetes alapvető fürt- és számítási feladatok összetevőit, valamint azt, hogy ezek hogyan kapcsolódnak az Azure Kubernetes-szolgáltatás (AKS) szolgáltatásaihoz.
services: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: bcf56aa89a42d65fdb7bf03696faad13c64cbc8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259642"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes alapfogalmak az Azure Kubernetes szolgáltatás (AKS)

Ahogy az alkalmazásfejlesztés egy tárolóalapú megközelítés felé halad, fontos az erőforrások vezénylése és kezelése. A Kubernetes a vezető platform, amely lehetővé teszi a hibatűrő alkalmazás-munkaterhelések megbízható ütemezését. Az Azure Kubernetes-szolgáltatás (AKS) egy felügyelt Kubernetes-ajánlat, amely tovább egyszerűsíti a tárolóalapú alkalmazások üzembe helyezését és kezelését.

Ez a cikk bemutatja a Kubernetes alapvető infrastruktúra-összetevőit , például a *vezérlősíkot*, *a csomópontokat*és a *csomópontkészleteket*. Számítási feladatok, például *podok,* *központi telepítések*és *készletek* is bevezetésre kerülnek, valamint az erőforrások *névterekbe való csoportosítása.*

## <a name="what-is-kubernetes"></a>Mi az a Kubernetes?

A Kubernetes egy gyorsan fejlődő platform, amely a tárolóalapú alkalmazásokat és a hozzájuk kapcsolódó hálózati és tárolási összetevőket kezeli. A hangsúly az alkalmazás számítási feladatain van, nem az alapul szolgáló infrastruktúra-összetevőkön. A Kubernetes deklaratív megközelítést biztosít a központi telepítésekhez, amelyet a felügyeleti műveletekhez szükséges API-k robusztus készlete támogat.

Modern, hordozható, mikroszolgáltatásokon alapuló alkalmazásokat hozhat létre és futtathat, amelyek a Kubernetes vezénylése és az alkalmazás-összetevők rendelkezésre állásának kezelése előnyeit élvezik. A Kubernetes támogatja az állapotnélküli és az állapotalapú alkalmazásokat, mivel a csapatok a mikroszolgáltatásokon alapuló alkalmazások elfogadásával haladnak előre.

Nyílt platformként a Kubernetes lehetővé teszi, hogy az alkalmazásokat a kívánt programozási nyelvvel, operációs rendszerrel, könyvtárakkal vagy üzenetküldő busszal építse fel. A meglévő folyamatos integrációs és folyamatos kézbesítési (CI/CD) eszközök integrálhatók a Kubernetes-szel a kiadások ütemezéséhez és üzembe helyezéséhez.

Az Azure Kubernetes-szolgáltatás (AKS) felügyelt Kubernetes-szolgáltatást biztosít, amely csökkenti az üzembe helyezési és az alapvető felügyeleti feladatok összetettségét, beleértve a frissítések koordinálását is. Az AKS vezérlősíkot az Azure platform kezeli, és csak az alkalmazásokat futó AKS-csomópontokért kell fizetnie. Az AKS a nyílt forráskódú Azure Kubernetes Service Engine[(aks-engine)][aks-engine]tetejére épül.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-fürtarchitektúra

A Kubernetes-fürt két összetevőből áll:

- *A vezérlősík-csomópontok* biztosítják az alapvető Kubernetes-szolgáltatásokat és az alkalmazás-munkaterhelések vezénylését.
- *A csomópontok futtatják* az alkalmazás-munkaterheléseket.

![Kubernetes vezérlősík és csomópont-összetevők](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Vezérlősík

AKS-fürt létrehozásakor a rendszer automatikusan létrehoz és konfigurál egy vezérlősíkot. Ez a vezérlősík a felhasználótól absztrakt felügyelt Azure-erőforrásként érhető el. A vezérlősíknak nincs költsége, csak az AKS-fürt részét szolgáló csomópontok.

A vezérlősík a következő kubernetes-összetevőket tartalmazza:

- *kube-apiserver* – Az API-kiszolgáló az alapul szolgáló Kubernetes API-k expozíciójának módját. Ez az összetevő biztosítja a felügyeleti `kubectl` eszközök, például a Kubernetes irányítópult kapcsolati kapcsolatát.
- *etcd* - A Kubernetes-fürt és -konfiguráció állapotának fenntartása érdekében a magas rendelkezésre állású *etcd* kulcsfontosságú értéktároló a Kubernetes-en belül.
- *kube-scheduler* – Alkalmazások létrehozásakor vagy méretezésekor az ütemező határozza meg, hogy mely csomópontok futtathatják a számítási feladatot, és elindítja azokat.
- *kube-controller-manager* – A vezérlőkezelő számos kisebb vezérlőt felügyel, amelyek olyan műveleteket hajtanak végre, mint például a podok replikálása és a csomópontműveletek kezelése.

Az AKS egyegybérlős vezérlősíkot biztosít, dedikált API-kiszolgálóval, ütemezővel stb. Megadhatja a csomópontok számát és méretét, és az Azure platform konfigurálja a biztonságos kommunikációt a vezérlősík és a csomópontok között. A vezérlősíkkal a Kubernetes API-kon `kubectl` keresztül történik, például a Kubernetes irányítópulton keresztül.

Ez a felügyelt vezérlősík azt jelenti, hogy nem kell konfigurálnia az összetevőket, például egy magas rendelkezésre *állású etcd tárolót,* de azt is jelenti, hogy nem férhet hozzá közvetlenül a vezérlősíkhoz. A Kubernetes-re történő frissítések et az Azure CLI vagy az Azure Portal on keresztül kell hangolni, amely frissíti a vezérlősíkot, majd a csomópontokat. A lehetséges problémák elhárításához tekintse át a vezérlősík naplók az Azure Monitor naplók on keresztül.

Ha a vezérlősíkot egy adott módon kell konfigurálnia, vagy közvetlen hozzáférésre van szüksége, telepítheti saját Kubernetes-fürtjét [az aks-engine][aks-engine]használatával.

A kapcsolódó gyakorlati tanácsokról a [Fürtbiztonsággal és -frissítésekekkel kapcsolatos gyakorlati tanácsok az AKS-ben című témakörben található.][operator-best-practices-cluster-security]

## <a name="nodes-and-node-pools"></a>Csomópontok és csomópontkészletek

Az alkalmazások és a támogató szolgáltatások futtatásához kubernetes *csomópontra*van szükség. Az AKS-fürt egy vagy több csomópont, amely egy Azure virtuális gép (VM), amely a Kubernetes csomópont-összetevőket és a tároló futásidejű:

- A `kubelet` a Kubernetes ügynök, amely feldolgozza a vezénylési kérelmeket a vezérlősíkés a kért tárolók futtatásának ütemezése.
- A virtuális hálózatot a *kube-proxy* kezeli minden csomóponton. A proxy irányítja a hálózati forgalmat, és kezeli a szolgáltatások és a podok IP-címzését.
- A *tároló futási ideje* az az összetevő, amely lehetővé teszi a tárolóba telepített alkalmazások futtatását és további erőforrások, például a virtuális hálózat és a tároló futtatását. Az AKS-ben a Moby a tároló futási ideje.

![Az Azure virtuális gépe és a Kubernetes-csomópont támogató erőforrásai](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Az Azure virtuális gép mérete a csomópontok határozza meg, hogy hány processzorok, mennyi memóriát, és a rendelkezésre álló tárterület mérete és típusa (például a nagy teljesítményű SSD vagy a rendszeres HDD. Ha nagy mennyiségű processzort és memóriát vagy nagy teljesítményű tárhelyet igénylő alkalmazásokra számít, ennek megfelelően tervezze meg a csomópont méretét. Az AKS-fürtcsomópontjainak számát is felskálázhatja az igények kielégítése érdekében.

Az AKS-ben a fürt csomópontjainak virtuálisgép-lemezképe jelenleg Ubuntu Linux vagy Windows Server 2019 rendszeren alapul. Amikor létrehoz egy AKS-fürtöt, vagy felskálázhatja a csomópontok számát, az Azure platform létrehozza a kért virtuális gépek számát, és konfigurálja őket. Nincs manuális konfiguráció, amit végre kell hajtania. Ügynök csomópontok számlázása standard virtuális gépek, így a virtuális gép által használt virtuális gép méretére vonatkozó engedmények (beleértve az [Azure-foglalások)][reservation-discounts]automatikusan alkalmazva.

Ha egy másik gazdaoperációs rendszert, tárolófutási időt vagy egyéni csomagokat kell használnia, telepítheti saját Kubernetes-fürtjét [az aks-engine][aks-engine]használatával. Az upstream `aks-engine` kiadási funkciókat, és biztosítja a konfigurációs beállításokat, mielőtt azok hivatalosan támogatott AKS-fürtök. Ha például a Moby-tól eltérő tárolófutási időt `aks-engine` szeretne használni, konfigurálhatja és üzembe helyezheti a Kubernetes-fürtöt, amely megfelel az aktuális igényeknek.

### <a name="resource-reservations"></a>Erőforrás-foglalások

A csomópont-erőforrásokat az AKS használja a csomópont fürt részeként való működéséhez. Ez eltérést okozhat a csomópont teljes erőforrásai és az AKS-ben használatos erőforrások között. Ez fontos megjegyezni, ha a kérelmek és korlátozások a felhasználó által telepített podok beállításakor.

A csomópont lefoglalható erőforrásainak keresése:
```kubectl
kubectl describe node [NODE_NAME]

```

A csomópont teljesítményének és működésének fenntartása érdekében az AKS minden csomóponton erőforrásokat foglal le. Ahogy egy csomópont egyre nagyobb erőforrások, az erőforrás-foglalás nő, mivel a nagyobb mennyiségű felhasználó üzembe helyezett podok felügyeletre szoruló.

>[!NOTE]
> Az AKS-bővítmények, például a Container Insights (OMS) használatával további csomópont-erőforrásokat használ fel.

- **CPU** - fenntartott CPU függ csomópont típusa és a fürt konfigurációja, amely okozhat kevésbé lefoglalható CPU miatt futó további funkciók

| CPU magok a gazdagépen | 1 | 2 | 4 | 8 | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kube-fenntartott (millicores)|60|100|140|180|260|420|740|

- **Memória** - az AKS által használt memória két érték összegét tartalmazza.

1. A kubelet démon telepítve van az összes Kubernetes-ügynök csomóponton a tároló létrehozása és megszüntetése kezelésére. Alapértelmezés szerint az AKS-ben ez a démon a következő kilakoltatási szabállyal rendelkezik: *memory.available<750Mi*, ami azt jelenti, hogy egy csomópontnak mindig legalább 750 Mi-rel kell rendelkeznie.  Ha egy gazdagép a rendelkezésre álló memória küszöbértéke alatt van, a kubelet megszakítja az egyik futó podot, hogy memóriát szabadítson fel a gazdagépen, és védje azt. Ez egy reaktív művelet, ha a rendelkezésre álló memória meghaladja a 750Mi küszöbértéket.

2. A második érték a kubelet démon megfelelő működéséhez (kube-reserved) a memóriafoglalások fokozatos üteme.
    - Az első 4 GB memória 25%-a
    - A következő 4 GB memória 20%-a (legfeljebb 8 GB)
    - A következő 8 GB memória 10%-a (akár 16 GB)
    - A következő 112 GB memória 6%-a (akár 128 GB)
    - A 128 GB feletti memória 2%-a

A fenti szabályok a memória és a CPU-lefoglalás az ügynök csomópontok kifogástalan állapotának megőrzésére szolgálnak, beleértve néhány gazdarendszer-podokat, amelyek kritikusak a fürt állapota szempontjából. Ezek a lefoglalási szabályok azt is eredményezik, hogy a csomópont kevesebb lefoglalható memóriát és processzort jelent, mint ha nem lenne része egy Kubernetes-fürtnek. A fenti erőforrás-foglalások nem módosíthatók.

Ha például egy csomópont 7 GB-ot kínál, a 750Mi-es kemény kilakoltatási küszöbértéken felül a memória 34%-a nem allokálható.

`(0.25*4) + (0.20*3) = + 1 GB + 0.6GB = 1.6GB / 7GB = 22.86% reserved`

A Kubernetes-fenntartások mellett az alapul szolgáló csomópont operációs rendszer is fenntart egy mennyiségű PROCESSZOR- és memória-erőforrásokat az operációs rendszer funkcióinak karbantartásához.

A kapcsolódó gyakorlati tanácsokról az [AKS alapvető ütemezőszolgáltatásainak gyakorlati tanácsai című témakörben található.][operator-best-practices-scheduler]

### <a name="node-pools"></a>Csomópontkészletek

Az azonos konfigurációs csomópontok *csomópontkészletekbe*vannak csoportosítva. A Kubernetes-fürt egy vagy több csomópontkészletet tartalmaz. A csomópontok kezdeti száma és mérete az Alapértelmezett *csomópontkészletet*létrehozó AKS-fürt létrehozásakor van definiálva. Ez az alapértelmezett csomópontkészlet az AKS-ben tartalmazza az ügynökcsomópontokat futtató alapul szolgáló virtuális gépeket.

> [!NOTE]
> Annak érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópontkészletben.

AKS-fürt méretezésekor vagy frissítésekor a művelet az alapértelmezett csomópontkészlettel történik. Egy adott csomópontkészlet méretezése vagy frissítése is választható. A frissítési műveletekhez a futó tárolók ütemezése a csomópontkészlet más csomópontjaira történik, amíg az összes csomópont sikeresen nem frissül.

Az AKS több csomópontkészletének használatáról az [AKS-ben lévő fürt több csomópontkészletének létrehozása és kezelése][use-multiple-node-pools]című témakörben talál további információt.

### <a name="node-selectors"></a>Csomópontválasztók

Egy több csomópontkészletet tartalmazó AKS-fürtben előfordulhat, hogy meg kell mondania a Kubernetes-ütemezőnek, hogy melyik csomópontkészletet használja egy adott erőforráshoz. A bejövő műveletek vezérlői például nem fusannak a Windows Server-csomópontokon (jelenleg előzetes verzióban az AKS-ben). Csomópont választók segítségével definiálhat különböző paramétereket, például a csomópont operációs rendszer, amely szabályozza, ahol a pod kell ütemezni.

A következő alappélda egy NGINX-példányt ütemez egy Linux-csomópontra a *"beta.kubernetes.io/os"* csomópontválasztó használatával: linux :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

A podok ütemezésének szabályozásáról az [AKS speciális ütemező szolgáltatásainak gyakorlati tanácsai című témakörben][operator-best-practices-advanced-scheduler]talál további információt.

## <a name="pods"></a>Hüvely

A Kubernetes *podokat* használ az alkalmazás egy példányának futtatásához. A pod az alkalmazás egyetlen példányát jelöli. A podok általában 1:1-es hozzárendeléssel rendelkeznek egy tárolóval, bár vannak olyan speciális forgatókönyvek, ahol egy pod több tárolót is tartalmazhat. Ezek a többtárolós podok vannak ütemezve együtt ugyanazon a csomóponton, és lehetővé teszi a tárolók kapcsolódó erőforrások megosztását.

Pod létrehozásakor *erőforrás-kérelmeket* definiálhat egy bizonyos mennyiségű PROCESSZOR- vagy memória-erőforrás igényléséhez. A Kubernetes-ütemező megpróbálja ütemezni a podok futtatásához egy csomóponton a rendelkezésre álló erőforrásokat, hogy megfeleljen a kérésnek. Megadhatja azt is, hogy egy adott pod túl sok számítási erőforrást képes felaz alapul szolgáló csomópontból túl sok számítási erőforrást fogyasztani. Ajánlott eljárás: erőforráskorlátok at az összes podok, hogy a Kubernetes-ütemező megérteni, hogy mely erőforrások szükségesek és engedélyezett.

További információ: [Kubernetes podok][kubernetes-pods] és [Kubernetes pod életciklusa.][kubernetes-pod-lifecycle]

A pod egy logikai erőforrás, de a tároló(k) ahol az alkalmazás számítási feladatok futnak. Podok általában rövid élettartamú, eldobható erőforrások, és egyedileg ütemezett podok hiányzik néhány, a magas rendelkezésre állási és redundancia funkciók Kubernetes nyújt. Ehelyett a podok általában kubernetes *vezérlők*, például a központi telepítési vezérlő által üzembe helyezett és felügyelt.

## <a name="deployments-and-yaml-manifests"></a>Központi telepítések és YAML-jegyzékek

A *központi telepítés* egy vagy több azonos podokat jelöl, amelyeket a Kubernetes központi telepítési vezérlő kezel. A központi telepítés határozza meg a *replikák* (podok) létrehozása, és a Kubernetes-ütemező biztosítja, hogy ha a podok vagy csomópontok problémákat tapasztalnak, további podok vannak ütemezve a kifogástalan csomópontokon.

Frissítheti a központi telepítések a podok, a használt tárolórendszerkép vagy a csatlakoztatott tároló konfigurációjának módosításához. A központi telepítési vezérlő kiüríti és leállítja a replikák egy adott számát, replikákat hoz létre az új központi telepítési definícióból, és folytatja a folyamatot, amíg a központi telepítés összes replikája frissül.

Az AKS-ben a legtöbb állapotmentes alkalmazásnak az egyes podok ütemezése helyett a központi telepítési modellt kell használnia. A Kubernetes figyelheti a központi telepítések állapotát és állapotát annak érdekében, hogy a szükséges számú replikák fussanak a fürtön belül. Ha csak az egyes podok ütemezése, a podok nem indul újra, ha problémát tapasztal, és nem ütemezett át a kifogástalan csomópontok, ha a jelenlegi csomópont hibát tapasztal.

Ha egy alkalmazás megköveteli, hogy a példányok kvóruma mindig elérhető legyen a felügyeleti döntések meghozatalához, nem szeretné, hogy egy frissítési folyamat megzavarja ezt a képességet. *Pod megszakítási költségvetések* segítségével meghatározhatja, hogy hány replikák egy központi telepítésben lehet venni egy frissítés vagy csomópont frissítése során. Például ha *5* replikák a központi telepítésben, megadhat egy pod megszakítása *4* lehetővé teszi, hogy egy replika törlését/átütemezését egy időben. Pod erőforrás-korlátokhoz, ajánlott eljárás pod megszakítási költségvetések olyan alkalmazások, amelyek megkövetelik a replikák minimális számú replikák mindig jelen van.

A központi telepítések általában `kubectl create` a `kubectl apply`vagy ával jönnek létre és kezelhetők. Központi telepítés létrehozásához definiáljon egy jegyzékfájlt YAML (YAML Ain't Markup Language) formátumban. A következő példa az NGINX webkiszolgáló alapvető telepítését hozza létre. A központi telepítés *3* létrehozandó replikát határoz meg, és a *80-as* port meg van nyitva a tárolón. Az erőforrás-kérelmek és -korlátok a processzorhoz és a memóriához is definiálva vannak.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Összetettebb alkalmazások at is hozhat létre, beleértve a szolgáltatások, például a terheléselosztók a YAML-jegyzékfájlon belül.

További információ: [Kubernetes deployments][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Csomagkezelés a Helm-el

A Kubernetes-alkalmazások kezelésének közös megközelítése a [Helm][helm]. Hozhat létre, és használja a meglévő nyilvános *Helm-diagramok,* amelyek tartalmazzák az alkalmazáskód és a Kubernetes YAML-jegyzékek az erőforrások üzembe helyezéséhez. Ezek a Helm-diagramok tárolhatók helyileg, vagy gyakran egy távoli tárházban, például egy [Azure Container Registry Helm chart tárházban.][acr-helm]

A Helm használatához a Kubernetes-fürtbe egy *Tiller* nevű kiszolgálóösszetevő van telepítve. A Tiller kezeli a diagramok telepítését a fürtön belül. Maga a Helm-ügyfél helyileg van telepítve a számítógépen, vagy az [Azure Cloud Shellben][azure-cloud-shell]használható. Kereshet vagy hozhat létre Helm-diagramokat az ügyféllel, majd telepítheti őket a Kubernetes-fürtbe.

![A Helm tartalmaz egy ügyfél- és egy kiszolgálóoldali tiller-összetevőt, amely erőforrásokat hoz létre a Kubernetes-fürtön belül](media/concepts-clusters-workloads/use-helm.png)

További információ: [Alkalmazások telepítése helm az Azure Kubernetes szolgáltatás (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets és DaemonSets

A központi telepítési vezérlő a Kubernetes-ütemező segítségével futtategy adott számú replikák bármely rendelkezésre álló csomópont on rendelkezésre álló erőforrásokat. Ez a megközelítés a központi telepítések használata elegendő lehet az állapotmentes alkalmazások, de nem az alkalmazások, amelyek állandó elnevezési konvenciót vagy tárolót igényel. Olyan alkalmazások esetében, amelyek megkövetelik, hogy a replika létezzen az egyes csomópontokon, vagy a kijelölt csomópontokon, a központi telepítési vezérlő nem vizsgálja meg, hogyan replikák vannak elosztva a csomópontok között.

Két Kubernetes-erőforrás lehetővé teszi az ilyen típusú alkalmazások kezelését:

- *StatefulSets* – Az alkalmazások állapotát az egyes podok életciklusán, például a tároláson túl is fenntarthatja.
- *DaemonSets* - Biztosítegy futó példány minden csomóponton, a Kubernetes bootstrap folyamat korai szakaszában.

### <a name="statefulsets"></a>Állapotbaadott készletek

A modern alkalmazásfejlesztés gyakran állapot nélküli alkalmazásokra irányul, de *a StatefulSets* használható állapotalapú alkalmazásokhoz, például adatbázis-összetevőket tartalmazó alkalmazásokhoz. A StatefulSet hasonló egy központi telepítéshez, hogy egy vagy több azonos podok jönnek létre és kezelnek. A StatefulSet replikái kecses, egymást követő megközelítést követnek az üzembe helyezéshez, a méretezéshez, a frissítésekhez és a végződésekhez. StatefulSet esetén (a replikák átütemezése esetén) az elnevezési konvenció, a hálózatnevek és a tároló konspiráta megmarad.

Az alkalmazást YAML formátumban `kind: StatefulSet`definiálja a használatával, és a StatefulSet vezérlő ezután kezeli a szükséges replikák telepítését és felügyeletét. Az adatok az Azure felügyelt lemezei vagy az Azure Files által biztosított állandó tárolóba íródnak. A StatefulSets, az alapul szolgáló állandó tároló marad akkor is, ha a StatefulSet törlődik.

További információ: [Kubernetes StatefulSets][kubernetes-statefulsets].

A StatefulSet replikái ütemezve vannak, és az AKS-fürt bármely elérhető csomópontján futnak. Ha meg kell győződnie arról, hogy legalább egy pod a készlet fut egy csomóponton, ehelyett használhatja a DaemonSet.

### <a name="daemonsets"></a>Daemonszettek

Adott naplógyűjtési vagy figyelési igények, előfordulhat, hogy egy adott pod futtatásához az összes, vagy kiválasztott csomópontok. A *DaemonSet* ismét egy vagy több azonos pod okainak üzembe helyezésére szolgál, de a DaemonSet vezérlő biztosítja, hogy minden megadott csomópont a pod egy példányát futtatja.

A DaemonSet vezérlő ütemezheti podok csomópontok on korai a fürt indítási folyamat, mielőtt az alapértelmezett Kubernetes ütemező elindult. Ez a képesség biztosítja, hogy a Pods egy DaemonSet elindul, mielőtt a hagyományos podok egy központi telepítés vagy StatefulSet vannak ütemezve.

A StatefulSets beállításhoz hasonlóan a DaemonSet is egy `kind: DaemonSet`YAML-definíció részeként van definiálva a használatával.

További információ: [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> A [virtuális csomópontok bővítmény](virtual-nodes-cli.md#enable-virtual-nodes-addon)használata esetén a Démonkészletek nem hoz létre podokat a virtuális csomóponton.

## <a name="namespaces"></a>Névterek

A Kubernetes-erőforrások, például a podok és a központi telepítések logikailag *egy névtérbe*vannak csoportosítva. Ezek a csoportosítások lekínálják az AKS-fürtök logikai felosztását, valamint az erőforrások létrehozásához, megtekintéséhez vagy kezeléséhez való hozzáférés korlátozását. Létrehozhat névtereket például külön üzleti csoportokhoz. A felhasználók csak a hozzájuk rendelt névtereken belüli erőforrásokkal kommunikálhatnak.

![Kubernetes névterek logikailag osztja erőforrások és alkalmazások](media/concepts-clusters-workloads/namespaces.png)

AKS-fürt létrehozásakor a következő névterek érhetők el:

- *alapértelmezett* – Ez a névtér az a hely, ahol a podok és a központi telepítések alapértelmezés szerint létrejönnek, ha nincs megadva. Kisebb környezetekben az alkalmazásokat közvetlenül az alapértelmezett névtérbe helyezheti további logikai színbontások létrehozása nélkül. Amikor a Kubernetes API-val kommunikál, például a, `kubectl get pods`az alapértelmezett névtér használatos, ha nincs megadva.
- *kube-system* – Ez a névtér, ahol az alapvető erőforrások léteznek, például a hálózati szolgáltatások, például a DNS és a proxy, vagy a Kubernetes irányítópulton. Általában nem telepíti a saját alkalmazásait ebbe a névtérbe.
- *kube-public* – Ez a névtér általában nem használható, de használható az erőforrások láthatóak az egész fürtön, és bármely felhasználó által megtekinthető.

További információ: [Kubernetes névterek][kubernetes-namespaces].

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a Kubernetes alapvető összetevőit, és hogyan vonatkoznak az AKS-fürtökre. A Kubernetes és az AKS alapfogalmairól az alábbi cikkekben talál további információt:

- [Kubernetes / AKS hozzáférés és identitás][aks-concepts-identity]
- [Kubernetes / AKS biztonság][aks-concepts-security]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes / AKS tároló][aks-concepts-storage]
- [Kubernetes / AKS skála][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
