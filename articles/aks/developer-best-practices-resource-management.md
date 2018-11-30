---
title: Fejlesztői ajánlott eljárások – erőforrás-kezelés az Azure Kubernetes-szolgáltatások (AKS)
description: Az alkalmazás fejlesztői ajánlott eljárások az erőforrás-kezelés az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 50f55ca3b05a6902841f1b1268cfbde742a9d589
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428283"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások az alkalmazások fejlesztői az Azure Kubernetes Service (AKS)-erőforrások kezeléséhez

Fejlesztés és az Azure Kubernetes Service (AKS) alkalmazásokat futtathat, nincsenek néhány kulcsterületeket kell figyelembe venni. Az alkalmazások központi telepítésének kezelése negatív hatással a szolgáltatás Ön által megadott végfelhasználói élménye. Segítséget nyújtanak a sikeres, tartsa szem előtt ajánlott eljárásokat is követheti fejlesztésére és alkalmazások futtatásához az aks-ben.

Ajánlott eljárások a cikkben a fürt és a számítási feladatok futtatása az alkalmazás fejlesztői szempontból összpontosít. Felügyeleti ajánlott eljárásokkal kapcsolatos további információkért lásd: [operátor ajánlott eljárások az elkülönítési és erőforrás-kezelést az Azure Kubernetes Service (AKS)-fürt][operator-best-practices-isolation]. Ez a cikk bemutatja:

> [!div class="checklist"]
> * Mik a pod erőforrás-kérelmek és korlátai
> * Fejlesztés és üzembe helyezés alkalmazások fejlesztői, szóközök és a Visual Studio Code módjai
> * Hogyan használható a `kube-advisor` eszközzel ellenőrizze az üzemelő példányok problémáival kapcsolatban

## <a name="define-pod-resource-requests-and-limits"></a>Adja meg a pod erőforrás-kérelmek és korlátozások

**Ajánlott eljárásokkal kapcsolatos útmutatás** – állítsa be a pod kérelmek és a YAML jegyzékek összes podok vonatkozó korlátozások. Ha az AKS-fürt használ *erőforráskvóták*, az üzembe helyezés kell utasítani, ha nem ad meg ezeket az értékeket.

Elsődleges lehet kezelni a számítási erőforrások belül egy AKS-fürtöt, hogy pod kérelmek és korlátait. Ezeket a kérelmeket és korlátai lehetővé teszik a Kubernetes-ütemező tudja, Mik a számítási erőforrásokat, hozzá kell rendelni egy pod.

* **Kérelmek pod** Processzor- és a pod igénylő időkereten meghatározása. Ezek a kérelmek kell lennie a pod kell adnia a teljesítmény elfogadható szintű számítási erőforrások mennyisége.
    * Ha a Kubernetes-ütemező próbál podot helyezze egy csomóponton, a pod kérelmek segítségével meghatározhatja, melyik csomópont van a szükséges erőforrások ténylegesen rendelkezésre.
    * Állítsa be ezeket a kérelmeket, győződjön meg arról, hogy a teljesítmény elfogadható szintű fenntartásához szükséges kevesebb erőforrást nem ad meg, hogy az alkalmazás teljesítményének figyelése.
* **A pod korlátok** Processzor és a egy pod használó memória maximális mennyisége is. Ezek a korlátok megakadályozhatja az egy vagy két sorozatos podok túl sok Processzor- és a csomópont a felvételt. Ebben a forgatókönyvben csökkentené a csomópont és az azokon futó egyéb podok teljesítményét.
    * Nem állít be egy pod korlát magasabb, mint a csomópontok is támogatja. AKS-csomópontok foglal le Processzor- és a Kubernetes alapvető összetevők időkereten. Az alkalmazás próbálja meg más podok, hogy sikeresen futtatni a csomóponton túl sok erőforrást igényelnek.
    * Újra különböző időpontokban, a nap vagy hét folyamán az alkalmazás teljesítményének figyeléséhez. Határozza meg a megugrása esetén, és az alkalmazások szükségleteihez szükséges erőforrásokat a pod-korlátozások igazítása.

A pod megadott ajánlott ezeket a kérelmeket és korlátokat meghatározásához. Ha nem adja meg ezeket az értékeket, a Kubernetes-ütemező nem ismeri, milyen erőforrásokat van szükség. Az ütemező is ütemezheti a pod nélkül alkalmazás elfogadható teljesítményt biztosítanak elegendő erőforrás a csomóponton. A fürt rendszergazdája állíthatja be *erőforráskvóták* erőforrás-kérelmek és korlátait, szükséges egy névtérhez. További információkért lásd: [erőforráskvóták az AKS-fürtök][resource-quotas].

A CPU-kérelem és a korlát meghatározása során a program méri a CPU-egységekben. *1.0-s* CPU-adatbázisnak felel meg egy alapul szolgáló virtuális CPU-magot a csomóponton. Gpu-k azonos mérésére szolgál. Is meghatározhatja a tört kérést vagy korlátot, általában millicpu. Ha például *100m* van *0,1* az alapul szolgáló virtuális Processzormagok.

Az alábbi egyszerű példában az NGINX podot, a pod kérelmek *100m* processzoridőt, és *128 Buszpéldány* memória. A pod az erőforrás korlátai vannak beállítva, hogy *250 millió* CPU és *256 Buszpéldány* memória:

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

Erőforrás-mérés és -hozzárendelések kapcsolatos további információkért lásd: [kezelése a számítási erőforrásokat tárolókhoz][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Fejlesztés és hibakeresés alkalmazásokat AKS-fürt

**Ajánlott eljárásokkal kapcsolatos útmutatás** -fejlesztői csapatok be kell-e üzembe helyezése és hibakeresése egy AKS-fürt fejlesztési szóközzel ellen. Ez a fejlesztési modell gondoskodik arról, hogy szerepköralapú hozzáférés-vezérlők, hálózati és tárolási igényeinek megfelelően vannak megvalósítva az alkalmazás éles üzembe helyezés előtt.

Az Azure fejlesztési szóközöket fejlesztése, hibakeresése, és tesztelheti az alkalmazásokat közvetlenül az AKS-fürt szemben. A fejlesztők a csapaton belüli együttműködve összeállításához és teszteléséhez alkalmazás életciklusa során. Továbbra is a meglévő eszközeivel, például a Visual Studio vagy Visual Studio Code. Egy bővítmény futtatása és hibáik keresése az AKS-fürtben az alkalmazás lehetőséget biztosít a fejlesztői tárhelyek van telepítve:

![Fejlesztői, szóközök egy AKS-fürtöt az alkalmazások hibakeresése](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Az integrált fejlesztési és tesztelési folyamat fejlesztői, szóközök csökkenti a szükségességét helyi tesztelési környezetek, például [minikube][minikube]. Ehelyett fejleszti és teszteli az AKS-fürt ellen. Ez a fürt biztonságossá tehetők és elkülönített fürt logikailag el névterek használatát az előző szakaszban leírtaknak megfelelően. Amikor az alkalmazásokat telepítésre készek az éles környezetben, ahogy a fejlesztés minden történt egy valódi AKS-fürtön hajtja magabiztosan telepítheti.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>A Kubernetes esetében a Visual Studio Code-bővítmény használata

**Ajánlott eljárásokkal kapcsolatos útmutatás** – akkor jelentkezik, telepítése és használata a VS Code-bővítménnyel, a Kubernetes YAML írásakor. A bővítmény használhatja az integrált központi telepítési megoldás, így ritkán kommunikáló alkalmazástulajdonosok az AKS-fürtöt is.

A [Visual Studio Code-bővítmény a Kubernetes] [ vscode-kubernetes] segítségével fejleszthet és helyezhet üzembe alkalmazásokat az aks-ben. A bővítmény Kubernetes-erőforrást, és a Helm-diagramok és a sablonok az intellisense biztosít. Keresse meg, üzembe helyezése, és szerkessze a VS Code belül a Kubernetes-erőforrásokat. A bővítmény is egy intellisense ellenőrzést biztosít erőforrás-kérelmek, vagy korlátozza a pod-specifikációk lesznek beállítva:

![A VS Code-bővítmény hiányzik a memóriakorlátok Kubernetes figyelmeztetést](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Rendszeresen kube-advisor az alkalmazások hibáinak keresése

**Ajánlott eljárásokkal kapcsolatos útmutatás** – rendszeresen, a legújabb verzióját futtató `kube-advisor` a hibák észleléséhez a fürtben. Ha egy meglévő AKS-fürtre erőforráskvóták alkalmaz, futtassa `kube-advisor` először, amelyek nem rendelkeznek, erőforrás-kérelmek és a meghatározott podok kereséséhez.

A [kube-advisor] [ kube-advisor] eszköz megvizsgálja egy Kubernetes-fürtöt, és jelentést készít a talált problémákat. Egy hasznos, ellenőrizze, hogy azonosítsa a podok nem rendelkező erőforrás-kérelmek és korlátozások érvényben.

Az AKS-fürt, amelyen számos fejlesztői csapatok és alkalmazásokat azok visszakövetését, hogy podok nélkül ezeket az erőforrás-kérelmek, és korlátozza a készlet. Ajánlott eljárásként rendszeresen futtassa `kube-advisor` az AKS-fürtökön.

## <a name="next-steps"></a>További lépések

Ajánlott eljárások a cikkben a fürt és a számítási feladatok futtatása a fürt operátor szempontjából összpontosít. Felügyeleti ajánlott eljárásokkal kapcsolatos további információkért lásd: [operátor ajánlott eljárások az elkülönítési és erőforrás-kezelést az Azure Kubernetes Service (AKS)-fürt][operator-best-practices-isolation].

Néhány ajánlott eljárások végrehajtásához a következő cikkekben talál:

* [Fejlesztés a fejlesztési tárolóhelyek használatával][dev-spaces]
* [Az advisor-kube kapcsolatos problémák ellenőrzése][aks-kubeadvisor]

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
