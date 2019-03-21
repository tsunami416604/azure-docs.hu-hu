---
title: Alapelvei – a Kubernetes alapjai az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg, az alapszintű fürt és a Kubernetes, és hogyan kapcsolódnak az Azure Kubernetes Service (AKS) funkciókat a számítási feladatok összetevői
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: bf1ff4391e65fea68ac019be8fde8709fb4422b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181350"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>A Kubernetes alapvető fogalmainak Azure Kubernetes Service (AKS)

Alkalmazásfejlesztés a tárolókon alapuló megközelítés felé helyezi át, mivel összehangolhatja és kezelheti az erőforrásokat kell fontos. Kubernetes az vezető platform, amely lehetővé teszi a hibatűrő alkalmazások és szolgáltatások megbízható ütemezés megadása. Az Azure Kubernetes Service (AKS) egy felügyelt Kubernetes, ajánlat, amely további leegyszerűsíti a tárolóalapú alkalmazások üzembe helyezését és felügyeletét.

Ez a cikk bemutatja a fő Kubernetes-infrastruktúra összetevőinek például a *fürt fő*, *csomópontok*, és *csomópontkészletek*. Számítási erőforrások, például *podok*, *központi telepítések*, és *beállítja* jelennek még meg, hogyan csoportosíthatók az erőforrások, valamint *névterek*.

## <a name="what-is-kubernetes"></a>Mit jelent a Kubernetes?

Kubernetes az gyorsan fejlődő platform, amely tárolóalapú alkalmazások és a társított hálózatkezelési és tárolási összetevők kezeli. Az alkalmazások számítási feladatai, nem az alapul szolgáló infrastruktúra-összetevőket való összeállításán van. Kubernetes-környezetek, API-k robusztus készletét felügyeleti műveletek által támogatott deklaratív módszert biztosít.

Ön létrehozhatja és futtathatja a modern, hordozható, mikroszolgáltatás-alapú alkalmazások, amelyek Kubernetes replikálásával segít a vállalatnak, és ezeket alkalmazás-összetevő rendelkezésre állásának kezelése. Kubernetes csapatok folyamatban van a mikroszolgáltatás-alapú alkalmazások elfogadásával is állapot nélküli és állapotalapú alkalmazásokat támogatja.

Nyílt platformon, mint a Kubernetes lehetővé teszi a preferált programozási nyelv, az operációs rendszer, kódtárakat vagy üzenetküldési bus az alkalmazások létrehozási. A meglévő folyamatos integráció és folyamatos készregyártás (CI/CD) eszközei integrálhatók a kubernetes használatával történő ütemezésére és üzembe helyezése a kiadások.

Az Azure Kubernetes Service (AKS) egy felügyelt Kubernetes szolgáltatás, amely egyszerűbbé teheti az üzembe helyezés és az alapvető felügyeleti feladatokat, beleértve a koordinációt frissítéseket biztosít. Az Azure platform kezeli az AKS-fürt főkiszolgálót, és csak kell fizetnie az AKS-csomópontok, amelyek az alkalmazások futtatásához. Az AKS épül a nyílt forráskódú Azure Kubernetes Service-motor ([aks-motor][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-fürt architektúra

Kubernetes-fürt két összetevőből áll:

- *Fürt fő* csomópontok adja meg a fő Kubernetes-szolgáltatás és alkalmazás számítási feladatainak vezénylését.
- *Csomópontok* az alkalmazás számítási feladatok futtatásához.

![Kubernetes-fürt mester és csomópont-összetevők](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Fürt fő

Amikor egy AKS-fürtöt hoz létre, a fürt fő automatikusan létrehozta és konfigurálta. A fürt fő biztosított Azure-erőforrásként felügyelt emeli ki, a felhasználó elől. Nincs a fürt fő, csak azoknak a csomópontoknak az AKS-fürt részét képező számára ingyenesen.

A fürt fő összetevői a következők core Kubernetes:

- *kube-apiserver* -kiszolgálója az API hogyan érhetők el az alapul szolgáló Kubernetes API-kat. Ez az összetevő kommunikációt tesz lehetővé a felügyeleti eszközök, például `kubectl` vagy a Kubernetes irányítópultot.
- *etcd* – a Kubernetes-fürt és a magas rendelkezésre állású konfigurációs állapotának fenntartásához *etcd* Kubernetes belül egy kulcs-érték tároló.
- *a scheduler kube* – Ha hoz létre, vagy az alkalmazásokat, az ütemező határozza meg, hogy milyen csomópontok futtathatja a számítási feladatok, és elindítja azokat.
- *kube-tartományvezérlő-manager* – a vezérlő Manager felügyeli egy kisebb tartományvezérlőkkel, amelyek a műveleteket, például a podok replikálása és kezelési csomópont műveletek száma.

Az AKS biztosít egy egybérlős fürt főkiszolgáló, egy dedikált API-kiszolgálóval, a Feladatütemező, stb. A csomópontok mérete és száma meghatározása, és az Azure platform konfigurálja a fürt mester és csomópont közötti biztonságos kommunikációhoz. A fürt fő való interakció akkor fordul elő, Kubernetes API-k segítségével, mint például `kubectl` vagy a Kubernetes irányítópultot.

A felügyelt fürt fő azt jelenti, hogy nem kell konfigurálni az összetevők, például a magas rendelkezésre állású *etcd* tároló, de azt is jelenti, hogy a fürt fő közvetlenül nem lehet hozzáférni. A Kubernetes frissítése az Azure CLI-vel vagy az Azure Portalon, amely frissíti a fürt fő és a csomópontok Vezényel. A lehetséges hibák elhárításához, a fürt fő naplók az Azure Monitor naplóira keresztül tekintheti meg.

A fürt fő adott módon vagy hozzájuk a közvetlen hozzáférésre van szükségük van szüksége, ha a saját Kubernetes fürt használatával telepíthet [aks-motor][aks-engine].

További kapcsolódó ajánlott eljárások: [ajánlott eljárások a fürt biztonsági és frissítései az aks-ben][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Csomópont és csomópont-készletek

Az alkalmazások és a támogató szolgáltatások futtatásához, szükség van egy Kubernetes *csomópont*. AKS-fürt rendelkezik egy vagy több csomópont, amely egy Azure virtuális gép (VM) a Kubernetes csomópont-összetevőinek és a futtatókörnyezet a tárolót futtató:

- A `kubelet` a Kubernetes-ügynök, amely feldolgozza a fürt fő és a kért tárolókban futó ütemezés vezénylési kérelmeit.
- Virtuális hálózatkezelés kezeli a *kube-proxy* minden egyes csomóponton. A proxy útvonalakat a hálózati forgalmat, és kezeli a szolgáltatások és a podok IP-címzés.
- A *container modul* az a komponens, amely lehetővé teszi a tárolóalapú alkalmazások futtatásához, és további erőforrások, például a virtuális hálózat és tároló együttműködik. Az aks-ben a tároló-futtatókörnyezet Moby használják.

![Az Azure virtuális gép és a támogató erőforrások egy Kubernetes-csomópont](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Az Azure virtuális gép méretét a csomópontok határozza meg, hány processzort, mennyi memóriát és a méret és típus (például nagy teljesítményű SSD vagy HDD rendszeres) rendelkezésre álló tárhelyet. Ha várhatóan nagy mennyiségű Processzor és memória vagy nagy teljesítményű tárolást igénylő alkalmazásokhoz szükség, ennek megfelelően tervezze meg a csomópont méretét. Is méretezheti a csomópontok számát felfelé az AKS-fürt az igényeknek.

Az aks-ben Ubuntu Linux esetében a fürtben található csomópontok a Virtuálisgép-lemezkép jelenleg alapján. AKS-fürt létrehozása vagy vertikális felskálázás a csomópontok számát, az Azure platform a kért számú virtuális gépeket hoz létre, és konfigurálja őket. Nem tartozik a végezhető el a manuális konfiguráció.

Egyéni csomagok vagy egy másik gazdagép operációs Rendszeréhez, tároló-futtatókörnyezet, használni kell, ha a saját Kubernetes fürt használatával telepíthet [aks-motor][aks-engine]. A felsőbb rétegbeli `aks-engine` kiadott szolgáltatások és konfigurációs lehetőségeket talál, mielőtt hivatalosan támogatott AKS-fürtben. Például, ha szeretné használni a Windows-tárolók és a egy tároló-futtatókörnyezet Moby eltérő, használhatja `aks-engine` konfigurálásához és üzembe helyezéséhez a Kubernetes-fürt, amely az aktuális igényeinek.

### <a name="resource-reservations"></a>Erőforrás-foglalások

Nem kell minden egyes csomóponton Kubernetes alapösszetevők kezelhet, mint a *kubelet*, *kube-proxy*, és *kube-dns*, azonban néhány, a rendelkezésre álló igényelnek a számítási erőforrásokat. A csomópont teljesítményének és funkcionalitásának fenntartása a következő számítási erőforrások vannak lefoglalva minden egyes csomóponton:

- **CPU** – 60 ms
- **Memória** – 4 GIB-ra legfeljebb 20 %-os

Ezeket a fenntartásokat jelenti azt, hogy a rendelkezésre álló Processzor- és az alkalmazások memória mennyisége adatbázisénál kisebb, mint a csomópont önmaga tartalmaz. Ha korlátozott erőforrások miatt a futtatott alkalmazások számát, ezeket a fenntartásokat biztosítása a CPU és memória továbbra is elérhető, a Kubernetes alapvető összetevők. Az erőforrás-foglalások nem módosítható.

Példa:

- **Standard DS2 v2** csomópontméret tartalmaz 2 vCPU és 7 GB memória
    - 7 GB memória 20 %-át 1,4 GB =
    - Összesen *(7-1.4) 5.6-os GiB =* memória érhető el a csomópontot
    
- **Standard szintű E4s v3** csomópont mérete 4 vCPU, és a 32 GiB memóriát tartalmaz
    - 20 % 32 GiB memóriát = 6.4 GIB-ra, de az AKS csak fenntart egy legfeljebb 4 GiB
    - Összesen *(32-4) 28 GiB =* érhető el a csomópontot
    
Az alapul szolgáló OS csomópont is igényli néhány saját alapvető funkcióinak végrehajtásához a CPU és memória-erőforrások mennyiségét.

További kapcsolódó ajánlott eljárások: [ajánlott eljárásai az aks-ben alapszintű ütemezési funkciókat][operator-best-practices-scheduler].

### <a name="node-pools"></a>Csomópontkészletek

Ugyanaz a konfiguráció csomópontján vannak besorolva *csomópontkészletek*. Kubernetes-fürt egy vagy több csomópont készletet tartalmaz. A kezdeti meg csomópontok számát és méretét definiált létrehozott egy AKS-fürtöt, amely létrehoz egy *csomópontkészlet alapértelmezett*. Az alapértelmezett csomópontkészlet az aks-ben az ügynök-csomópontok futtató mögöttes virtuális gépeket tartalmaz.

Méretezhető, vagy az AKS-fürt frissítése, a művelet végrehajtását az alapértelmezett csomópontkészletek ellen. A frissítési műveletek futó tárolók ütemezése a csomópont a készletben található többi csomóponton mindaddig, amíg az összes a csomópont frissítése sikeresen megtörtént.

## <a name="pods"></a>Podok

Használja a Kubernetes *podok* egy példányt az alkalmazás futtatásához. A pod jelöli az alkalmazás egy példánya. Podok általában 1:1 leképezés egy tárolóval rendelkezik, bár van speciális forgatókönyvek ahol podot több tárolót is tartalmazhat. Ezek többtárolós podok ütemezett együtt ugyanazon a csomóponton, és engedélyezze a tárolók kapcsolódó erőforrások megosztását.

A pod létrehozásakor definiálhat *erőforráskorlátok* CPU és memória-erőforrások egy bizonyos mennyiségű kéréséhez. A Kubernetes-ütemező próbál ütemezése a podok futni a csomóponton a rendelkezésre álló erőforrások a kérelem teljesítéséhez. Adja meg a maximális erőforráskorlátok, amely megakadályozza, hogy egy adott pod felhasználása túl sok számítási erőforrás az alapul szolgáló csomópontból is. Ajánlott eljárás az erőforráskorlátok a segítségével a Kubernetes a Scheduler ismertetése, mely erőforrásokat engedélyezett és szükséges összes podok tartalmazza.

További információkért lásd: [Kubernetes-podok] [ kubernetes-pods] és [Kubernetes-podok életciklus][kubernetes-pod-lifecycle].

A pod logikai erőforrás, de a tároló(k) bérletét, amelyben az alkalmazás számítási feladatok futnak. Podok általában rövid élettartamú, rendelkezésre álló erőforrásokat, és külön-külön ütemezett podok konferenciát Kubernetes biztosít magas rendelkezésre állás és redundancia funkcióit. Ehelyett podok általában üzembe és Kubernetes által kezelt *tartományvezérlők*, például a központi telepítési vezérlő.

## <a name="deployments-and-yaml-manifests"></a>A telepítések és YAML jegyzékek

A *üzembe helyezési* egy vagy több azonos podok, a Kubernetes üzembe helyezési vezérlő által felügyelt jelöli. Központi telepítés meghatározza, hogy hány *replikák* (podok) hozhat létre, és a Kubernetes-ütemező biztosítja, hogy podok egy vagy több problémát észlel, ha újabb podok ütemezett a megfelelő állapotú csomópontokat.

Módosíthatja a központi telepítések podok konfigurációjának módosítása, a tárolórendszerképet, vagy csatolt storage. A központi telepítési vezérlő kiüríti és replikák adott számú leállítja, replikákat hoz létre az új üzemelő példány definíciója, és a folyamat továbbra is fennáll, addig, amíg a központi telepítésben lévő összes replika frissülnek.

Az aks-ben a legtöbb állapot nélküli alkalmazások egyes podok ütemezés helyett az üzembe helyezési modellt kell használnia. Kubernetes figyelni tudja, és az annak érdekében, hogy a szükséges központi telepítések állapotát a fürt futtatása replikák száma. Az egyes podok csak ütemezésekor a podok nem újraindítása, ha probléma merül fel, és nem megfelelő állapotú csomópontokat a újraütemezte, ha az aktuális csomópont hibát észlel.

Ha egy alkalmazás-példányok a kvórum mindig elérhetők vonatkozó kezelési döntést kell tenni, akadályozza meg, hogy lehetővé teszi egy frissítési folyamat nem szeretné. *Megszakítás költségvetése pod* le lehessen állítani a központi telepítés hány replikák update vagy a csomópont a frissítés során meghatározására használható. Például, ha rendelkezik *5* replikák a központi telepítésben, megadhat egy pod megszakadását *4* csak engedélyezéséhez legyenek a törölt/újraütemezte egyszerre több replikát. Pod erőforráskorlátok, az ajánlott eljárás, hogy pod megszakítás költségvetése meghatározni mindig jelen kell lennie a replikák minimális száma igénylő alkalmazásokhoz.

Központi telepítések általában létrehozása és felügyelete az `kubectl create` vagy `kubectl apply`. Hozzon létre egy központi telepítést, meghatározhatja a jegyzékfájlt a YAML (YAML Ain't Markup Language) formátumban. A következő példában létrehozunk egy alapszintű NGINX-webkiszolgálót központi telepítés. Megadja az üzembe helyezés *3* replikák hozhatók létre, és erre a portra *80-as* nyitható meg a tárolón. Erőforrás-kérelmek és korlátozások is definiálva Processzor és memória.

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

Összetettebb alkalmazásokat is beleértve a szolgáltatások például a terheléselosztók belül a YAML-jegyzékfájl hozható létre.

További információkért lásd: [Kubernetes-üzembehelyezés][kubernetes-deployments].

### <a name="package-management-with-helm"></a>A csomagkezelés a Helm használatával

Kubernetes-alkalmazások felügyelete általánosan használt megközelítése van [Helm][helm]. Is hozhat létre és használhat meglévő nyilvános Helm *diagramok* , amely tartalmazza az alkalmazás kódját csomagolt verzióját és erőforrások üzembe helyezéséhez jegyzékfájlok Kubernetes YAML. Ezek a Helm-diagramok tárolhatja helyben vagy gyakran egy távoli tárházzal, például egy [Azure Container Registry Helm-diagram tárház][acr-helm].

A Helm használata esetén egy kiszolgáló-összetevő nevű *a tiller valóban* a Kubernetes-fürtre van telepítve. A tiller valóban felügyeli a diagram a fürtön belüli telepítését. A Helm-ügyfél magán helyileg van telepítve a számítógépen, vagy lehet használni a [Azure Cloud Shell][azure-cloud-shell]. Keresse meg vagy Helm-diagramok létrehozásához az ügyféllel, és telepítse őket a Kubernetes-fürthöz.

![Helm tartalmaz egy ügyfél- és a kiszolgálóoldali a tiller valóban összetevője, amely létrehozza az erőforrásokat a Kubernetes-fürtben](media/concepts-clusters-workloads/use-helm.png)

További információkért lásd: [telepíthet alkalmazásokat a Helm használatával az Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets és DaemonSets

A központi telepítési vezérlő a Kubernetes-ütemező használja az elérhető erőforrások minden elérhető csomóponton replikák adott számú futtatásához. Ez a megközelítés az üzembe helyezett használatával elegendő lehet állapot nélküli alkalmazások, de nem egy állandó elnevezési konvenciót, vagy tárolási igénylő alkalmazásokhoz. Minden olyan csomópont, vagy a kiválasztott csomópontok, a fürtön belül létezik egy replika igénylő alkalmazásokhoz a központi telepítési vezérlő nem meg hogyan replikák a csomópontokon vannak elosztva.

Két forrásanyag Kubernetes, amelyekkel kezelheti az ilyen típusú alkalmazásokat:

- *StatefulSets* -alkalmazások mellett egy egyéni pod életciklusát, például a storage állapotának fenntartásához.
- *DaemonSets* – győződjön meg, hogy egy futó példány minden csomóponton, a Kubernetes a rendszerindítási folyamat korai szakaszaiban.

### <a name="statefulsets"></a>StatefulSets

Gyakran a probléma a modern alkalmazások fejlesztése a célja az állapotmentes alkalmazások, de *StatefulSets* állapotalapú alkalmazások, például alkalmazásokat, amelyek közé tartozik az adatbázis-összetevőket is használható. Egy StatefulSet hasonlít egy központi telepítést, hogy egy vagy több azonos podok létrehozása és felügyelete. Egy StatefulSet replikák egy biztonságos, egymást követő megközelítés kövesse az üzembe helyezés, skála, frissítések és végződnek. Egy StatefulSet, az elnevezési konvenciót, a hálózati nevek és a storage-továbbra is fennáll, a replikák időpontra van ütemezve.

A YAML formátumú használatával határozzák meg az alkalmazást `kind: StatefulSet`, a StatefulSet vezérlő majd kezeli a üzembe helyezéséhez és felügyeletéhez szükséges a replikákat. Adatok állandó tárolókba, az Azure Managed Disks vagy az Azure Files van megírva. StatefulSets az alapul szolgáló adattárolásra továbbra is marad még a StatefulSet törlésekor.

További információkért lásd: [Kubernetes StatefulSets][kubernetes-statefulsets].

Replikák egy StatefulSet ütemezett, és bármely elérhető egy AKS-fürt csomópontjának futtathatja. Győződjön meg arról, hogy legalább egy pod a Ha a készlet egy csomóponton futnak, Ehelyett használhatja a DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Adott naplógyűjtés vagy figyelési igényeit az összes megadott podot futtatására lehet szükség, vagy kijelölve, a csomópontok. A *DaemonSet* újra központi telepítéséhez használandó egy vagy több, azonos podok, de a DaemonSet vezérlő biztosítja, hogy minden egyes megadott csomópontot a pod példányát futtató.

A DaemonSet vezérlő ütemezheti a podok korai szakaszában a rendszerindítási folyamat, mielőtt Kubernetes Feladatütemező elindult az alapértelmezett csomópontokon. Ez a képesség biztosítja, hogy egy DaemonSet a podok elindulnak, mielőtt egy üzembe helyezési vagy StatefulSet hagyományos podok ütemezett.

A DaemonSet StatefulSets, például egy YAML definíció használatával részeként megadott `kind: DaemonSet`.

További információkért lásd: [Kubernetes DaemonSets][kubernetes-daemonset].

## <a name="namespaces"></a>Névterek

Kubernetes-erőforrások, például a podok és üzemelő példányok csomópontokba logikailag egy *névtér*. Ezek a Csoportosítások is logikailag egy AKS-fürtöt és korlátozhatja a hozzáférést, létrehozása, megtekintése és -erőforrások kezelése lehetőséget biztosíthat. Az üzleti csoportok, például különálló névterek hozhat létre. Felhasználók csak a hozzárendelt névterek belül erőforrásaival is interakcióba.

![Kubernetes-névterek az logikailag megoszthatók az erőforrások és alkalmazások](media/concepts-clusters-workloads/namespaces.png)

AKS-fürt létrehozásakor a következő névterek érhetők el:

- *alapértelmezett* – Ez a névtér a, ahol podok és központi telepítések jönnek létre alapértelmezés szerint ha egyiket sem. Kisebb környezetekben közvetlenül az alapértelmezett névtér alkalmazásokat helyezhet üzembe további logikai elkülönülés létrehozása nélkül. Amikor dolgozhat a Kubernetes API-t, mint például a `kubectl get pods`, az alapértelmezett névtér használatos, ha nincs megadva.
- *kube rendszer* – Ez a névtér a, ha alapvető erőforrások léteznek, például a hálózati szolgáltatások, mint például a DNS- és a proxy vagy a Kubernetes-irányítópult. Általában nem kell telepítenie a saját alkalmazásokat az ehhez a névtérhez.
- *nyilvános kube* – általában nem használja ezt a névteret, de is lesznek láthatók a teljes fürt között az erőforrások használhatók, és bármely felhasználó tekinthet meg.

További információkért lásd: [Kubernetes névterek][kubernetes-namespaces].

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti az egyes a Kubernetes-alapösszetevők, és ezek hogyan vonatkoznak az AKS-fürt. És további információkat az alapvető Kubernetes AKS fogalmait tekintse meg a következő cikkeket:

- [Kubernetes / AKS hozzáférési és azonosító][aks-concepts-identity]
- [Kubernetes / AKS biztonsági][aks-concepts-security]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes és az AKS-tároló][aks-concepts-storage]
- [Kubernetes AKS méretezése /][aks-concepts-scale]

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
