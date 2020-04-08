---
title: Erőforrás-kezelési gyakorlati tanácsok
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az alkalmazásfejlesztőknek az erőforrás-kezeléssel kapcsolatos gyakorlati tanácsait az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 0052657c947f8a9ff9c9d6aef86ff16d9a22adae
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803483"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Gyakorlati tanácsok az alkalmazásfejlesztők számára az erőforrások kezeléséhez az Azure Kubernetes szolgáltatásban (AKS)

Az Azure Kubernetes-szolgáltatás (AKS) alkalmazások fejlesztése és futtatása során néhány kulcsfontosságú területet kell figyelembe venni. Az alkalmazástelepítések kezelésének módjában negatívhatással lehet a nyújtott szolgáltatások végfelhasználói élményére. A siker érdekében tartsa szem előtt az okat az ajánlott eljárásokat, amelyeket az Alkalmazások AKS-ben való fejlesztése és futtatása közben követhet.

Ez az ajánlott eljárások cikk a fürt és a számítási feladatok alkalmazásfejlesztői szempontból történő futtatására összpontosít. A felügyeleti gyakorlati tanácsokról a [Fürtoperátor okainak elkülönítési és erőforrás-kezelési gyakorlati tanácsait az Azure Kubernetes-szolgáltatásban (AKS)][operator-best-practices-isolation]talál. Ebben a cikkben a következőket ismerheti meg:

> [!div class="checklist"]
> * Mik azok a pod erőforrás-kérelmek és korlátok
> * Alkalmazások fejlesztésének és üzembe helyezésének módjai a Fejlesztői terek és a Visual Studio-kód segítségével
> * Az eszköz `kube-advisor` használata a központi telepítésekkel kapcsolatos problémák ellenőrzésére

## <a name="define-pod-resource-requests-and-limits"></a>Pod erőforrás-kérelmek és -korlátok meghatározása

**Ajánlott eljárásokra vonatkozó útmutatás** – Pod-kérelmek és a YAML-jegyzékek ben lévő összes pod korlátozásának beállítása. Ha az AKS-fürt *erőforráskvótákat*használ, a telepítés elutasításra kerülhet, ha nem adja meg ezeket az értékeket.

Az AKS-fürtön belüli számítási erőforrások kezelésének elsődleges módja a pod-kérelmek és -korlátok használata. Ezek a kérelmek és korlátok lehetővé teszik a Kubernetes-ütemező tudja, milyen számítási erőforrásokat kell hozzárendelni egy pod.

* **Pod CPU/memória kérelmek** határozza meg a beállított mennyiségű PROCESSZOR és a memória, hogy a pod szüksége van rendszeresen.
    * Amikor a Kubernetes-ütemező megpróbál egy pod ot elhelyezni egy csomóponton, a pod-kérelmek segítségével határozza meg, hogy melyik csomópont rendelkezik elegendő erőforrással az ütemezéshez.
    * Pod-kérelem beállításának mellőzése alapértelmezés szerint a megadott korlátra fog korlátozódni.
    * Nagyon fontos, hogy az alkalmazás teljesítményét a kérelmek módosításához. Ha nem elegendő a kérelmek, az alkalmazás előfordulhat, hogy a csomópont túlütemezése miatt csökkent teljesítményt kap. Ha a kérelmek túlbecsülték, az alkalmazás lehet, hogy nagyobb nehézséget egyre ütemezett.
* **Pod CPU/memória korlátok** a maximális mennyiségű CPU és a memória, amely egy pod használható. Ezek a korlátok segítenek meghatározni, hogy mely podokat kell megölni a csomópont instabilitása esetén az elégtelen erőforrások miatt. Megfelelő korlátok nélkül a podok elpusztulnak, amíg az erőforrás-nyomás meg nem szűnik.
    * Pod korlátok segítségével határozza meg, ha egy pod elvesztette az erőforrás-felhasználás vezérlését. Ha egy korlátot túllép, a pod a leölés a csomópont állapotának fenntartása és a csomópontot megosztó podok hatásának minimalizálása érdekében prioritást élvez.
    * Pod-korlát beállításának elnemállása alapértelmezés szerint egy adott csomóponton elérhető legmagasabb értékre állítja be.
    * Ne állítson be magasabb podkorlátot, mint amit a csomópontok támogatnak. Minden AKS-csomópont egy meghatározott mennyiségű PROCESSZORt és memóriát foglal le az alapvető Kubernetes-összetevők számára. Előfordulhat, hogy az alkalmazás megpróbál túl sok erőforrást felhasználni a csomóponton más podok sikeres futtatásához.
    * Ismét nagyon fontos, hogy figyelemmel kíséri a teljesítményét az alkalmazás különböző időpontokban a nap folyamán vagy a héten. Határozza meg, hogy a csúcsigény mikor van, és igazítsa a pod korlátokat az alkalmazás maximális igényeinek megfelelő erőforrásokhoz.

A pod specifikációk, **ajánlott eljárás, és nagyon fontos,** hogy meghatározza ezeket a kéréseket és korlátokat a fenti információk alapján. Ha nem tartalmazza ezeket az értékeket, a Kubernetes ütemező nem tudja figyelembe venni az erőforrásokat az alkalmazások szükséges az ütemezési döntések támogatásához.

Ha az ütemező egy podot helyez el egy olyan csomóponton, amely nem elegendő erőforrással, az alkalmazás teljesítménye lefogy. Erősen ajánlott a fürtrendszergazdák számára *erőforráskvóták* beállítása olyan névtérben, amely erőforrás-kérelmek és korlátok beállítását igényli. További információt az [AKS-fürtök erőforráskvótái című témakörben talál.][resource-quotas]

Cpu-kérelem vagy korlát definiálásakor az értéket cpu-egységekben méri a rendszer. 
* *1.0* CPU megegyezik egy mögöttes virtuális CPU mag a csomóponton. 
* Ugyaneza mérés a GPU-k esetében is használatos.
* A törteket millimagban is meghatározhatja. Például *100m* egy mögöttes vCPU mag *0,1.*

A következő alapvető példában egyetlen NGINX-pod, a pod *100 m-es* PROCESSZOR-időt és *128Mi* memória kérelmek. A pod erőforrás-korlátai *250 m-es* PROCESSZOR és *256 Mi* memória:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Az erőforrás-mérésekről és -hozzárendelésekről a [tárolók számítási erőforrásainak kezelése][k8s-resource-limits]című témakörben talál további információt.

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Alkalmazások fejlesztése és hibakeresése AKS-fürtön

**Ajánlott eljárások –** A fejlesztői csapatoknak üzembe kell helyezniük és hibakeresést kell alkalmazniuk egy AKS-fürtön a fejlesztői tárolóhelyek használatával. Ez a fejlesztési modell gondoskodik arról, hogy a szerepköralapú hozzáférés-vezérlés, hálózati vagy tárolási igények megvalósítása előtt az alkalmazás éles környezetben.

Az Azure Dev Spaces segítségével közvetlenül fejlesztheti, debugolhatja és tesztelheti az alkalmazásokat egy AKS-fürtön. A csapaton belüli fejlesztők együtt dolgoznak az alkalmazás teljes életciklusa során történő összeállításon és tesztelésen. Továbbra is használhatja a meglévő eszközöket, például a Visual Studio vagy a Visual Studio Code. Egy bővítmény telepítve van a fejlesztői tárolóhelyekhez, amely lehetőséget ad az alkalmazás Futtatására és hibakeresésére egy AKS-fürtben:

![Alkalmazások hibakeresése a fejlesztői szóközökkel rendelkező AKS-fürtben](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Ez az integrált fejlesztési és tesztelési folyamat a fejlesztői tárolóhelyekkel csökkenti a helyi tesztkörnyezetek, például a [minikube][minikube]szükségességét. Ehelyett egy AKS-fürtöt fejleszt és tesztel. Ez a fürt biztonságos és elkülöníthető a névterek használatával a fürt logikai elkülönítéséhez való használatáról szóló előző szakaszban. Amikor az alkalmazások készen állnak az éles környezetben való üzembe helyezésre, magabiztosan üzembe helyezheti, mivel a fejlesztés egy valódi AKS-fürt ellen történt.

Az Azure Dev Spaces linuxos podokon és csomópontokon futó alkalmazásokkal való használatra készült.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>A Kubernetes Visual Studio-kódbővítményének használata

**Ajánlott eljárások –** Telepítse és használja a VS-kód bővítményt a Kubernetes-hez a YAML-jegyzékek írásakor. A bővítmény integrált központi telepítési megoldáshoz is használható, amely segíthet az AKS-fürtöt ritkán kommunikáló alkalmazástulajdonosoknak.

A [Kubernetes Visual Studio-kódbővítménye][vscode-kubernetes] segít az alkalmazások fejlesztésében és üzembe helyezésében az AKS-ben. A kiterjesztés intellisense kubernetes források, és Helm diagramok és sablonok. A Kubernetes-erőforrásokat a VS-kódból is böngészheti, telepítheti és szerkesztheti. A bővítmény is biztosít egy intellisense ellenőrizze az erőforrás-kérelmek vagy korlátok, hogy a pod előírások:

![VS Code kiterjesztés a Kubernetes számára, figyelmeztetés a hiányzó memóriakorlátokról](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Rendszeresen ellenőrizze a kube-tanácsadóalkalmazási problémákat

**Ajánlott eljárások –** Rendszeresen futtassa a nyílt forráskódú eszköz legújabb verzióját `kube-advisor` a fürtben felmerülő problémák észleléséhez. Ha erőforráskvótákat alkalmaz egy meglévő AKS-fürtön, először futtassa `kube-advisor` azokat a podokat, amelyek nem rendelkeznek erőforrás-kérelmekkel és korlátokkal.

A [kube-advisor][kube-advisor] eszköz egy társított AKS nyílt forráskódú projekt, amely megvizsgálja a Kubernetes-fürtöt, és jelentéseket készít a talált problémákról. Egy hasznos ellenőrzés, hogy azonosítsa a podok, amelyek nem rendelkeznek erőforrás-kérelmek és korlátok érvényben.

A kube-advisor eszköz jelentést tehet az erőforrás-kérelemről, és a PodSpecs for Windows alkalmazások, valamint a Linux-alkalmazások hiányzó korlátairól, de maga a kube-advisor eszköz egy Linux podon kell ütemezve. Ütemezheti a pod egy adott operációs rendszerrel rendelkező csomópontkészleten a pod konfigurációjában lévő [csomópontválasztó][k8s-node-selector] használatával.

Egy AKS-fürt, amely számos fejlesztői csapatok és alkalmazások, nehéz lehet nyomon követni podok nélkül ezeket az erőforrás-kérelmeket és korlátokat beállítva. Ajánlott eljárásként rendszeresen `kube-advisor` futtassa az AKS-fürtökön.

## <a name="next-steps"></a>További lépések

Ez az ajánlott eljárások cikk középpontjában, hogyan kell futtatni a fürt és a számítási feladatok a fürt operátori szempontból. A felügyeleti gyakorlati tanácsokról a [Fürtoperátor okainak elkülönítési és erőforrás-kezelési gyakorlati tanácsait az Azure Kubernetes-szolgáltatásban (AKS)][operator-best-practices-isolation]talál.

Az ajánlott eljárások némelyikének megvalósításához olvassa el az alábbi cikkeket:

* [Fejlesztés a Dev Spaces használatával][dev-spaces]
* [Ellenőrizze, hogy vannak-e problémák a kube-advisorral][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
