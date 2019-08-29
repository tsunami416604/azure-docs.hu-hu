---
title: Fogalmak – az Azure Kubernetes Services (ak) Kubernetes alapjai
description: Ismerje meg a Kubernetes alapszintű fürt-és munkaterhelés-összetevőit, valamint azt, hogy azok hogyan kapcsolódnak az Azure Kubernetes szolgáltatás (ak) szolgáltatásaihoz
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: e606b4fee2c46f66f13c45586bcc25577bd90a1f
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147186"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) Kubernetes alapvető fogalmai

Mivel az alkalmazásfejlesztés egy tároló alapú megközelítés irányába mozdul el, fontos a szükséges erőforrások összehangolása és kezelése. A Kubernetes a vezető platform, amely lehetővé teszi a hibatűrő alkalmazások számítási feladatainak megbízható ütemezését. Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes-ajánlat, amely tovább egyszerűsíti a tároló-alapú alkalmazások üzembe helyezését és felügyeletét.

Ez a cikk bemutatja a fő Kubernetes infrastruktúra-összetevőket, például a *fürt*főkiszolgálóját, csomópontjait és *csomópont-készleteit*. A munkaterhelésekerőforrásai, például a hüvelyek, a *központi telepítések*és a *készletek* , valamint az erőforrások *névterek*szerinti csoportosítása is bevezethető.

## <a name="what-is-kubernetes"></a>Mi a Kubernetes?

A Kubernetes egy gyorsan változó platform, amely a tároló-alapú alkalmazásokat és a hozzájuk társított hálózatkezelési és tárolási összetevőket kezeli. A hangsúly az alkalmazás munkaterhelésén, nem pedig az alapul szolgáló infrastruktúra-összetevőkön van. A Kubernetes a központi telepítések deklaratív megközelítését biztosítja, amely az API-k robusztus készletével támogatott a felügyeleti műveletekhez.

Létrehozhat és futtathat modern, hordozható, Kubernetes-alapú alkalmazásokat, amelyek kihasználják az alkalmazás-összetevők rendelkezésre állásának előkészítését és felügyeletét. A Kubernetes olyan állapot nélküli és állapot-nyilvántartó alkalmazásokat is támogat, mint a csapatok előrehaladása a Service-alapú alkalmazások bevezetésével.

Nyílt platformként a Kubernetes lehetővé teszi, hogy alkalmazásai a kívánt programozási nyelv, az operációs rendszer, a könyvtárak vagy az üzenetkezelési busz használatával legyenek felépítve. A meglévő folyamatos integrációs és folyamatos teljesítési (CI/CD) eszközök integrálása a Kubernetes a kiadások ütemezett és üzembe helyezéséhez.

Az Azure Kubernetes Service (ak) olyan felügyelt Kubernetes szolgáltatást biztosít, amely csökkenti az üzembe helyezés és az alapvető felügyeleti feladatok összetettségét, beleértve a frissítések koordinálását is. Az AK-fürt főkiszolgálóit az Azure platform felügyeli, és csak az alkalmazásokat futtató AK-csomópontok kell fizetnie. Az AK-t a nyílt forráskódú Azure Kubernetes Service Engine ([AK-motor][aks-engine]) fölé építették.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-fürt architektúrája

A Kubernetes-fürtök két összetevőhöz vannak osztva:

- A *fürt fő* csomópontjai biztosítják a Kubernetes szolgáltatásokat és az alkalmazások számítási feladatainak előkészítését.
- A csomópontok futtatják az alkalmazás munkaterheléseit.

![Kubernetes-fürt fő és csomópont-összetevői](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Fürt főkiszolgálója

AK-fürt létrehozásakor a rendszer automatikusan létrehozza és konfigurálja a fürt főkiszolgálóját. Ez a fürtcsomópont felügyelt Azure-erőforrásként van megadva a felhasználótól. A fürt főkiszolgálójának csak az AK-fürt részét képező csomópontok díjait kell megfizetnie.

A fürt főkiszolgálója a következő alapvető Kubernetes összetevőket tartalmazza:

- *Kube-apiserver* – az API-kiszolgáló az alapul szolgáló Kubernetes API-k által elérhetővé vált. Ez az összetevő a felügyeleti eszközök, például `kubectl` a vagy a Kubernetes irányítópultjának interakcióját biztosítja.
- *etcd* – a Kubernetes-fürt és a konfiguráció állapotának fenntartása érdekében a magasan elérhető *etcd* a Kubernetes belüli Key Value Store.
- *Kube-Scheduler* – alkalmazások létrehozásakor vagy méretezéskor az ütemező meghatározza, hogy mely csomópontok futtathatják a munkaterhelést, és elindítják azokat.
- *Kube-Controller-Manager* – a vezérlő kezelője több kisebb vezérlőt is lát, amelyek olyan műveleteket hajtanak végre, mint például a hüvelyek replikálása és a csomópontok kezelése.

Az AK egybérlős fürtös főkiszolgálót biztosít dedikált API-kiszolgálóval, ütemező használatával stb. Megadhatja a csomópontok számát és méretét, és az Azure platform konfigurálja a fürt főkiszolgálója és csomópontjai közötti biztonságos kommunikációt. A Kubernetes API-kon keresztül, például `kubectl` a Kubernetes-irányítópulton keresztüli interakció történik.

Ez a felügyelt fürt főkiszolgálója azt jelenti, hogy nem kell olyan összetevőket konfigurálnia, mint például a *etcd* -tároló, de ez azt is jelenti, hogy a fürt főkiszolgálója nem érhető el közvetlenül. A Kubernetes-re való frissítés az Azure CLI-n vagy Azure Portalon keresztül történik, amely frissíti a fürt főkiszolgálóját és a csomópontokat. A lehetséges problémák elhárításához tekintse át a fürt fő naplóit Azure Monitor naplókon keresztül.

Ha a fürt főkiszolgálóját egy bizonyos módon kell konfigurálnia, vagy közvetlen hozzáféréssel kell rendelkeznie hozzájuk, a saját Kubernetes-fürtöt az [AK-motor][aks-engine]használatával is üzembe helyezheti.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a fürt biztonságához és a frissítésekhez az AK-ban][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Csomópontok és csomópont-készletek

Az alkalmazások és a támogató szolgáltatások futtatásához szükség van egy Kubernetes-csomópontra. Egy AK-fürt legalább egy csomóponttal rendelkezik, amely egy Azure-beli virtuális gép (VM), amely a Kubernetes csomópont-összetevőket és a tároló-futtatókörnyezetet futtatja:

- A `kubelet` a Kubernetes-ügynök, amely feldolgozza a fürtözési kérelmeket a fürt főkiszolgálójára, és ütemezi a kért tárolók futtatását.
- A virtuális hálózatkezelést a *Kube-proxy* kezeli az egyes csomópontokon. A proxy átirányítja a hálózati forgalmat, és kezeli a szolgáltatások és a hüvelyek IP-címzését.
- A *Container Runtime* az az összetevő, amely lehetővé teszi, hogy a tároló alkalmazások futtassák és használják a további erőforrásokat, például a virtuális hálózatot és a tárolót. Az AK-ban a Moby a tároló futtatókörnyezetként van használatban.

![Azure-beli virtuális gépek és a Kubernetes-csomópontok támogatási erőforrásai](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

A csomópontok Azure-beli virtuálisgép-mérete határozza meg, hogy hány processzor, mennyi memória, valamint a tárterület mérete és típusa érhető el (például nagy teljesítményű SSD vagy normál HDD). Ha várhatóan szükség van olyan alkalmazásokra, amelyek nagy mennyiségű PROCESSZORt és memóriát vagy nagy teljesítményű tárhelyet igényelnek, a csomópont méretét ennek megfelelően tervezze meg. Azt is megteheti, hogy az AK-fürtben lévő csomópontok száma az igények kielégítése érdekében felskálázásra kerül.

Az AK-ban a fürt csomópontjaihoz tartozó virtuálisgép-rendszerkép jelenleg Ubuntu Linux vagy Windows Server 2019 rendszeren alapul. Ha AK-fürtöt hoz létre vagy bővíti a csomópontok számát, az Azure platform létrehozza a kért számú virtuális gépet, és konfigurálja azokat. Nincs kézi konfiguráció a végrehajtáshoz. Az ügynök csomópontjai standard virtuális gépekként vannak kiszámlázva, ezért a rendszer automatikusan alkalmazza az Ön által használt virtuálisgép-mérethez tartozó kedvezményeket (beleértve az [Azure][reservation-discounts]-foglalásokat is).

Ha egy másik gazdagép operációs rendszert, tároló-futtatókörnyezetet vagy egyéni csomagokat is kell használnia, a saját Kubernetes-fürtöt az [AK-motorral][aks-engine]is üzembe helyezheti. A felsőbb rétegbeli `aks-engine` kiadások funkciói és konfigurációs beállításokat biztosítanak, mielőtt azok hivatalosan támogatottak legyenek az AK-fürtökben. Ha például a Moby-től eltérő tároló-futtatókörnyezetet szeretne használni, a használatával `aks-engine` konfigurálhatja és üzembe helyezheti az aktuális igényeknek megfelelő Kubernetes-fürtöt.

### <a name="resource-reservations"></a>Erőforrás-foglalások

Nem kell minden csomóponton felügyelni az alapvető Kubernetes-összetevőket, például a *kubelet*, a *Kube-proxyt*és a *Kube-DNS-* t, de a rendelkezésre álló számítási erőforrások némelyikét használják. A csomópont teljesítményének és funkcióinak fenntartásához a következő számítási erőforrások vannak lefoglalva az egyes csomópontokon:

- **CPU** -60 MS
- **Memória** – 20% akár 4 GIB

Ezek a foglalások azt jelentik, hogy az alkalmazásokhoz rendelkezésre álló CPU és memória mennyisége kevesebb, mint maga a csomópont. Ha a futtatott alkalmazások száma miatt van erőforrás-korlátozás, ezek a foglalások biztosítják, hogy a processzor és a memória továbbra is elérhető legyen az alapvető Kubernetes-összetevők számára. Az erőforrás-foglalások nem módosíthatók.

Példa:

- A **standard DS2 v2** csomópontjának mérete 2 vCPU és 7 GIB memóriát tartalmaz
    - 20%-a 7 GiB memória = 1,4 GiB
    - Összesen *(7-1,4) = 5,6 GIB* memória érhető el a csomóponthoz
    
- A **standard E4s v3** csomópontjának mérete 4 vCPU és 32 GIB memóriát tartalmaz
    - 20% 32 GiB memória = 6,4 GiB, de az AK-ban csak legfeljebb 4 GiB-t foglal le
    - A csomóponthoz összesen *(32-4) = 28 GIB* érhető el
    
A mögöttes csomópont operációs rendszerének bizonyos mennyiségű CPU-és memória-erőforrásra is szüksége van a saját alapvető funkcióinak elvégzéséhez.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások az alapszintű Scheduler-funkciókhoz az AK-ban][operator-best-practices-scheduler].

### <a name="node-pools"></a>Csomópontkészletek

Az azonos konfiguráció csomópontjai a *csomópont*-készletekbe vannak csoportosítva. A Kubernetes-fürt egy vagy több csomópontot tartalmaz. A csomópontok és a méretek kezdeti száma akkor van meghatározva, amikor egy AK-fürtöt hoz létre, amely létrehoz egy *alapértelmezett csomópont*-készletet. Ez az alapértelmezett Node-készlet az AK-ban tartalmazza az ügynök csomópontjait futtató mögöttes virtuális gépeket. Több Node-készlet támogatása jelenleg előzetes verzióban érhető el az AK-ban.

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópont-készletben.

AK-fürt méretezése vagy frissítése esetén a művelet az alapértelmezett csomópont-készleten történik. Dönthet úgy is, hogy egy adott csomópont-készletet méretezéssel vagy frissítéssel végez. A frissítési műveletek esetében a futó tárolók a csomópont más csomópontjain vannak ütemezve, amíg az összes csomópont frissítése sikeresen megtörtént.

További információ arról, hogyan használható több Node-készlet az AK-ban: [több csomópontos készlet létrehozása és kezelése a fürthöz az AK-ban][use-multiple-node-pools].

### <a name="node-selectors"></a>Csomópont-választók

A több csomópontot tartalmazó AK-fürtökben előfordulhat, hogy meg kell adnia a Kubernetes-ütemező számára, hogy melyik csomópont-készletet kell használnia egy adott erőforráshoz. A bejövő vezérlők például nem futnak a Windows Server-csomópontokon (jelenleg előzetes verzióban az AK-ban). A csomópont-választók lehetővé teszik különböző paraméterek (például a csomópont operációs rendszer) definiálását annak vezérléséhez, hogy a pod legyen ütemezve.

A következő alapvető példa egy NGINX-példányt ütemezhet egy Linux-csomóponton a *"Beta.kubernetes.IO/os"* csomópont-választó használatával: Linux:

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

További információ a hüvelyek ütemezésének szabályozásáról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Hüvely

A Kubernetes *hüvelyek* használatával futtatja az alkalmazás egy példányát. A pod az alkalmazás egy példányát jelöli. A hüvelyek általában egy tárolóval rendelkező 1:1-es hozzárendeléssel rendelkeznek, bár a pod több tárolót is tartalmazhat. Ezek a többtárolós hüvelyek ugyanazon a csomóponton vannak ütemezve, és lehetővé teszik a tárolók számára a kapcsolódó erőforrások megosztását.

Amikor létrehoz egy Pod-t, megadhat *erőforrás* -korlátozásokat, hogy bizonyos mennyiségű CPU-vagy memória-erőforrást igényeljen. A Kubernetes ütemező megkísérli a hüvelyek ütemezését, hogy az elérhető erőforrásokkal rendelkező csomóponton fusson a kérelem teljesítése érdekében. Megadhatja a maximális erőforrás-korlátot is, amely megakadályozza, hogy egy adott Pod túl sok számítási erőforrást fogyasztson a mögöttes csomópontból. Az ajánlott eljárás az, hogy az összes hüvely erőforrás-korlátait tartalmazza, hogy a Kubernetes Scheduler megértse, mely erőforrások szükségesek és engedélyezettek.

További információ: [Kubernetes hüvelyek][kubernetes-pods] és [Kubernetes Pod életciklusa][kubernetes-pod-lifecycle].

A pod logikai erőforrás, de a tároló (k), ahol az alkalmazás munkaterhelései futnak. A hüvelyek általában elmúló, eldobható erőforrások, és egyénileg ütemezett hüvelyek hiányoznak az Kubernetes által biztosított magas rendelkezésre állási és redundancia-funkciók közül. Ehelyett a hüvelyeket általában Kubernetes- *vezérlők*, például a központi telepítési vezérlő helyezi üzembe és kezeli.

## <a name="deployments-and-yaml-manifests"></a>Központi telepítések és YAML-jegyzékek

A *központi telepítés* egy vagy több azonos hüvelyt jelöl, amelyet a Kubernetes telepítési vezérlő kezel. A központi telepítés meghatározza a létrehozandó *replikák* (hüvelyek) számát, és a Kubernetes ütemező gondoskodik arról, hogy ha a hüvelyek vagy a csomópontok problémákba ütköznek, a további hüvelyek az egészséges csomópontokon vannak ütemezve.

A központi telepítések segítségével módosíthatja a hüvelyek, a felhasznált tárolók vagy a csatolt tárolók konfigurációját. A központi telepítési vezérlő leállítja a megadott számú replikát, létrehozza a replikákat az új központi telepítési definícióból, és addig folytatja a folyamatot, amíg a központi telepítésben szereplő összes replikát nem frissíti.

Az AK-ban a legtöbb állapot nélküli alkalmazásnak az egyes hüvelyek ütemezése helyett a telepítési modellt kell használnia. A Kubernetes nyomon követheti az üzemelő példányok állapotát és állapotát, így biztosítva, hogy a fürtön belül a szükséges számú replika fusson. Ha csak egyes hüvelyeket ütemezhet, a hüvelyek nem indulnak újra, ha probléma lép fel, és nem lettek átütemezett állapotba, ha az aktuális csomópont problémát észlel.

Ha egy alkalmazáshoz a példányok kvóruma szükséges, hogy mindig elérhetők legyenek a felügyeleti döntésekhez, nem szeretné, hogy a frissítési folyamat megszakítsa ezt a képességet. A *Pod* -megszakadási költségkeretekkel határozható meg, hogy egy központi telepítés hány replikáját lehet leállítani a frissítés vagy a csomópont frissítése során. Ha például *5* replika van az üzemelő példányban, megadhatja a *4* . Pod megszakadást, hogy egyszerre csak egy replikát töröljen vagy ütemezzen. A pod-erőforrások korlátaihoz hasonlóan a legjobb megoldás az, ha a pod-megszakadási költségvetést olyan alkalmazásokhoz határozza meg, amelyekhez legalább egy replika szükséges.

A központi telepítések általában a vagy `kubectl create` `kubectl apply`a alkalmazással hozhatók létre és kezelhetők. Központi telepítés létrehozásához meg kell adnia egy jegyzékfájlt a YAML (YAML nem Markup Language) formátumban. Az alábbi példa az NGINX webkiszolgáló alapszintű telepítését hozza létre. Az üzemelő példány *3* másodpéldányt határoz meg, és az *80* -es port nyitva van a tárolón. Az erőforrás-kérelmek és-korlátok a PROCESSZORhoz és a memóriához is meg vannak határozva.

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

Összetettebb alkalmazások hozhatók létre, beleértve a YAML-jegyzékben található terheléselosztó szolgáltatásokat is.

További információ: Kubernetes- [telepítések][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Csomagkezelő a Helmtal

A Kubernetes-alkalmazások kezelésének közös megközelítése a [Helm][helm]. Létrehozhat és használhat meglévő nyilvános Helm- *diagramokat* , amelyek az alkalmazás kódjának csomagolt verzióját és Kubernetes YAML-jegyzékeket tartalmaznak az erőforrások üzembe helyezéséhez. Ezek a Helm-diagramok helyileg, vagy gyakran egy távoli adattárban, például egy [Azure Container Registry Helm chart][acr-helm]-tárházban is tárolhatók.

A Helm használatához a *kormányrúd* nevű kiszolgáló-összetevő van telepítve a Kubernetes-fürtön. A kormányrúd kezeli a diagramok telepítését a fürtön belül. Maga a Helm-ügyfél helyileg van telepítve a számítógépen, vagy a Azure Cloud Shellon belül is [][azure-cloud-shell]használható. A-ügyféllel kereshet vagy létrehozhat Helm-diagramokat, majd telepítheti őket a Kubernetes-fürtbe.

![A Helm tartalmaz egy ügyfél-összetevőt és egy kiszolgálóoldali kormányrúd-összetevőt, amely erőforrásokat hoz létre a Kubernetes-fürtön belül](media/concepts-clusters-workloads/use-helm.png)

További információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets és DaemonSets

A központi telepítési vezérlő a Kubernetes ütemező használatával futtat egy adott számú replikát az elérhető erőforrásokkal rendelkező bármely elérhető csomóponton. Az üzemelő példányok használatának ezen megközelítése elegendő lehet az állapot nélküli alkalmazások számára, de nem olyan alkalmazásokhoz, amelyek állandó elnevezési konvenciót vagy tárolót igényelnek. Azon alkalmazások esetében, amelyeknek replikát kell megadniuk az egyes csomópontokon vagy a kijelölt csomópontokon egy fürtön belül, a központi telepítési vezérlő nem vizsgálja meg, hogyan oszlanak meg a replikák a csomópontok között.

Az ilyen típusú alkalmazások kezeléséhez két Kubernetes-erőforrás szükséges:

- *StatefulSets* – az alkalmazások állapotának fenntartása egy adott Pod-életcikluson túl, például a Storage-ban.
- *DaemonSets* – ellenőrizze, hogy fut-e a futó példányok minden csomóponton, a Kubernetes rendszerindítási folyamatának elején.

### <a name="statefulsets"></a>StatefulSets

A modern alkalmazásfejlesztés gyakran az állapot nélküli alkalmazások megvalósítását célozza, de a *StatefulSets* állapot-nyilvántartó alkalmazásokhoz, például adatbázis-összetevőket tartalmazó alkalmazásokhoz is használható. Egy StatefulSet hasonló a központi telepítéshez, amely egy vagy több azonos hüvelyt hoz létre és felügyel. A StatefulSet lévő replikák az üzembe helyezés, a méretezés, a frissítések és a megszakítások kecses, szekvenciális megközelítését követik. A StatefulSet az elnevezési konvenció, a hálózati nevek és a tárolók is megmaradnak, mint a replikák.

Az alkalmazást YAML formátumban `kind: StatefulSet`kell megadni, és a StatefulSet vezérlő kezeli a szükséges replikák telepítését és felügyeletét. A rendszer az Azure Managed Disks vagy Azure Files által biztosított állandó tárolóba írja az adattárolást. A StatefulSets esetében a mögöttes állandó tárterület még a StatefulSet törlésekor is marad.

További információ: [Kubernetes StatefulSets][kubernetes-statefulsets].

A StatefulSet lévő replikák ütemezése és futtatása egy AK-fürt bármely elérhető csomópontján történik. Ha meg kell győződnie arról, hogy a készletben legalább egy pod egy csomóponton fut, használhat egy Daemonset elemet.

### <a name="daemonsets"></a>DaemonSets

Bizonyos naplók vagy figyelési igények esetében előfordulhat, hogy egy adott Pod-t kell futtatnia az összes vagy a kijelölt csomóponton. Újra *daemonset elemet* egy vagy több azonos hüvely üzembe helyezéséhez, de a daemonset elemet vezérlő biztosítja, hogy a megadott csomópontok a pod egy példányát futtatják.

A Daemonset elemet-vezérlő a fürt rendszerindítási folyamata elején ütemezheti a hüvelyeket a csomópontokon, még az alapértelmezett Kubernetes-ütemező elindítása előtt. Ez a funkció biztosítja, hogy a Daemonset elemet lévő hüvelyek elindulnak, mielőtt a hagyományos hüvelyek egy központi telepítésben vagy StatefulSet vannak ütemezve.

A StatefulSets hasonlóan a Daemonset elemet a YAML definíciójának részeként van definiálva `kind: DaemonSet`a használatával.

További információ: [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Ha a [virtuális csomópontok beépülő modult](virtual-nodes-cli.md#enable-virtual-nodes-addon)használja, a DaemonSets nem hoz létre hüvelyeket a virtuális csomóponton.

## <a name="namespaces"></a>Névterek

A Kubernetes-erőforrások, például a hüvelyek és a központi telepítések logikailag vannakcsoportosítva egy névtérbe. Ezek a csoportok lehetővé teszik az AK-fürtök logikai elosztását, valamint az erőforrások létrehozásához, megtekintéséhez vagy kezeléséhez való hozzáférés korlátozását. Létrehozhat névtereket is az üzleti csoportok elkülönítéséhez, például:. A felhasználók csak a hozzájuk rendelt névterekben lévő erőforrásokkal tudnak kommunikálni.

![Kubernetes névterek az erőforrások és alkalmazások logikai elosztásához](media/concepts-clusters-workloads/namespaces.png)

Ha AK-fürtöt hoz létre, a következő névterek érhetők el:

- *alapértelmezett* – ez a névtér, ahol a hüvelyek és a központi telepítések alapértelmezés szerint jönnek létre, ha nincs megadva. Kisebb környezetekben az alkalmazások közvetlenül az alapértelmezett névtérbe helyezhetők, további logikai elkülönítések létrehozása nélkül. Ha együttműködik a Kubernetes API-val, például a- `kubectl get pods`val, akkor az alapértelmezett névtér lesz használatban, ha nincs megadva.
- *Kube-System* – ez a névtér az alapvető erőforrások, például a DNS és a proxy, illetve a Kubernetes irányítópultja. Általában nem telepítheti saját alkalmazásait ebbe a névtérbe.
- *Kube – nyilvános* – ez a névtér általában nincs használatban, de a teljes fürtön látható erőforrások számára is használható, és bármely felhasználó megtekinthető.

További információ: Kubernetes- [névterek][kubernetes-namespaces].

## <a name="next-steps"></a>További lépések

Ez a cikk a legfontosabb Kubernetes-összetevőket ismerteti, valamint azt, hogy ezek hogyan vonatkoznak az AK-fürtökre. Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Kubernetes/AK-hozzáférés és-identitás][aks-concepts-identity]
- [Kubernetes/AK biztonság][aks-concepts-security]
- [Kubernetes/AK virtuális hálózatok][aks-concepts-network]
- [Kubernetes/AK-tároló][aks-concepts-storage]
- [Kubernetes/AK-skála][aks-concepts-scale]

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
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md