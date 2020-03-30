---
title: Hogyan működik a fürt beállítása az Azure Dev Spaces számára?
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Bemutatja, hogyan működik az Azure Kubernetes-szolgáltatásfürt beállítása az Azure dev spaces-hez
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241724"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Hogyan működik a fürt beállítása az Azure Dev Spaces számára?

Az Azure Dev Spaces többféle lehetőséget kínál a Kubernetes-alkalmazások gyors iterálására és hibakeresésére, valamint a csapatával való együttműködésre egy Azure Kubernetes-szolgáltatás (AKS) fürtön. Ennek egyik módja, hogy engedélyezze az Azure Dev Spaces-t az AKS-fürtön, így [a szolgáltatásokat közvetlenül a fürtön futtathatja,][how-it-works-up] és [további hálózati és útválasztási lehetőségeket][how-it-works-routing]használhat. Ez a cikk ismerteti, mi történik, amikor előkészíti a fürtöt, és engedélyezi az Azure dev spaces.

## <a name="prepare-your-aks-cluster"></a>Az AKS-fürt előkészítése

Az AKS-fürt dev-terek előkészítése, ellenőrizze, hogy az AKS-fürt az [Azure Dev Spaces által támogatott][supported-regions] régióban van-e, és a Kubernetes 1.10.3 vagy újabb webhelyet futtatja. Ha engedélyezni szeretné az Azure Dev Spaces szolgáltatást a fürtön az Azure Portalról, keresse meg a fürtöt, kattintson a *fejlesztői terek*elemre, módosítsa *a Fejlesztői terek használata lehetőséget* *igen*re, majd kattintson a *Mentés gombra.* Az Azure DEV Spaces-t az Azure `az aks use-dev-spaces`CLI-ből is engedélyezheti a futtatásával.

A fejlesztői tárolóhelyek AKS-fürtjének beállítását a csapatfejlesztési rövid útmutató ban [például a csoportfejlesztés ről.For][quickstart-team]example of setting an AKS cluster for Dev Spaces, see the team development quickstart.

Ha az Azure Dev Spaces engedélyezve van az AKS-fürtön, telepíti a fürt vezérlőjét. A vezérlő az AKS-fürtön kívül található. Ez hajtja a viselkedést és a kommunikációt az ügyféloldali eszközök és az AKS-fürt között. Ha engedélyezve van, az ügyféloldali eszközök segítségével kommunikálhat a vezérlővel.

A vezérlő a következő műveleteket hajtja végre:

* A fejlesztői terület létrehozását és kiválasztását kezeli.
* Telepíti az alkalmazás Helm diagramját, és létrehozza a Kubernetes objektumokat.
* Az alkalmazás tárolórendszerképének létrehozása.
* Az alkalmazás telepítése az AKS-re.
* Növekményes buildek és újraindul, amikor a forráskód megváltozik.
* Kezeli a naplókat és a HTTP-nyomkövetéseket.
* Előre stdout és stderr az ügyféloldali szerszámozás.
* Útválasztást állít be a térben belüli, valamint a szülő- és gyermektereken belüli alkalmazásokhoz.

A vezérlő egy külön Azure-erőforrás a fürtön kívül, és a fürt erőforrásait a következőkre végzi:

* Létrehoz vagy kijelöl egy Kubernetes-névteret, amelyet fejlesztési térként használhat.
* Eltávolítja az *azds*nevű Kubernetes névteret, ha létezik, és létrehoz egy újat.
* Kubernetes webhook-konfiguráció t telepít.
* Webhook felvételi kiszolgáló telepítése.

Ugyanazt a egyszerű szolgáltatást használja, amelyet az AKS-fürt más Azure Dev Spaces-összetevőkszolgáltatás-hívások kezdeményezéséhez használ.

![Az Azure dev spaces előkészíti a fürtöt](media/how-dev-spaces-works/prepare-cluster.svg)

Az Azure Dev Spaces használatához legalább egy fejlesztői területnek kell lennie. Az Azure dev spaces kubernetes névtereket használ az AKS-fürtön a fejlesztési terekhez. A vezérlő telepítésekor egy új Kubernetes-névtér létrehozása vagy egy meglévő névtér kiválasztása az első fejlesztési területként használható. Alapértelmezés szerint a vezérlő felajánlja a meglévő *alapértelmezett* Kubernetes névtér frissítését az első fejlesztési területre.

Ha egy névtér fejlesztési térként van kijelölve, a vezérlő hozzáadja a *azds.io/space=true* címkét a névtérhez, hogy fejlesztői térként azonosíthassa. A létrehozott vagy kijelölt kezdeti fejlesztési terület alapértelmezés szerint a fürt előkészítése után van kiválasztva. Ha egy terület van kiválasztva, az Azure Dev Spaces új számítási feladatok létrehozásához használja.

Az ügyféloldali eszközintézés segítségével új fejlesztői tereket hozhat létre, és eltávolíthatja a meglévő fejlesztési tereket. A Kubernetes korlátozása miatt az *alapértelmezett* fejlesztői terület nem távolítható el. A vezérlő eltávolítja az *ügyféloldali* eszközelem által használt `azds` paranccsal való ütközéseket a meglévő Kubernetes-névterekből is.

A Kubernetes webhook felvételi kiszolgáló segítségével adja podok három tárolók üzembe helyezés során a műszerek: egy devspaces-proxy tároló, egy devspaces-proxy-init tároló, és egy devspaces-build tároló. **Mindhárom tároló az AKS-fürt root hozzáféréssel fut.** Ugyanazt a egyszerű szolgáltatást is használják, amelyet az AKS-fürt más Azure Dev Spaces-összetevőkszolgáltatás-hívások kezdeményezéséhez használ.

![Azure dev spaces Kubernetes webhook felvételi kiszolgáló](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

A devspaces-proxy tároló egy oldalkocsitároló, amely kezeli az összes TCP-forgalmat az alkalmazástárolóba, és segít az útválasztás végrehajtásában. A devspaces-proxy tároló átirányítja a HTTP-üzeneteket, ha bizonyos szóközöket használ. Például segíthet a HTTP-üzenetek továbbításában a szülő- és gyermekterekben lévő alkalmazások között. Minden nem HTTP-forgalom áthalad devspaces-proxy módosítatlan. A devspaces-proxy tároló is naplózza az összes bejövő és kimenő HTTP-üzeneteket, és elküldi azokat az ügyféloldali eszközküldés nyomkövetésként. Ezeket a nyomkövetéseket ezután a fejlesztő megtekintheti az alkalmazás viselkedésének vizsgálatához.

A devspaces-proxy-init tároló egy [init tároló,](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) amely további útválasztási szabályokat ad hozzá a térhierarchia alapján az alkalmazás tárolójához. Útválasztási szabályokat ad hozzá az alkalmazástároló */etc/resolv.conf* fájljának és iptables konfigurációjának frissítésével az indítás előtt. Az */etc/resolv.conf* frissítései lehetővé teszik a szolgáltatások DNS-feloldását a szülőterekben. Az iptables konfigurációs frissítések biztosítják az alkalmazás tárolójába be- és kivezető tcp-forgalmat a devspaces-proxy útvonalon. A Devspaces-proxy-init összes frissítése a Kubernetes által hozzáadott szabályok mellett történik.

A devspaces-build tároló egy init tároló, és a projekt forráskód és a Docker-szoftvercsatorna csatlakoztatva van. A projekt forráskódja és a Docker-hozzáférés lehetővé teszi, hogy az alkalmazástároló közvetlenül a pod által épüljenek.

> [!NOTE]
> Az Azure Dev Spaces ugyanazt a csomópontot használja az alkalmazás tárolójának létrehozásához és futtatásához. Ennek eredményeképpen az Azure Dev Spaces nem kell egy külső tároló beállításjegyzék az alkalmazás létrehozásához és futtatásához.

A Kubernetes webhook felvételi kiszolgáló figyel minden új pod, amely az AKS-fürtben létrehozott. Ha a pod telepítve van bármely névtér a *azds.io/space=true* címkével, a további tárolók at adja be, hogy a pod. A devspaces-build tároló csak akkor injektált, ha az alkalmazás tárolója fut az ügyféloldali eszközhasználat használatával.

Miután elkészítette az AKS-fürtöt, az ügyféloldali eszközök segítségével előkészítheti és futtathatja a kódot a fejlesztői térben.

## <a name="client-side-tooling"></a>Ügyféloldali eszközök

Az ügyféloldali eszközeszköz lehetővé teszi a felhasználó számára, hogy:
* Hozzon létre egy Dockerfile, Helm chart és az Azure Dev Spaces konfigurációs fájlt az alkalmazáshoz.
* Hozzon létre szülő- és gyermekfejlesztői tereket.
* Mondja meg a vezérlőnek, hogy építse fel és indítsa el az alkalmazást.

Az alkalmazás futása közben az ügyféloldali eszközök is:
* Fogadja és megjeleníti az AKS-ben futó alkalmazás stdout és stderr.
* [Port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) használatával engedélyezi az alkalmazás hoz\/való webes hozzáférést a http: /localhost használatával.
* Csatol egy hibakeresőt a futó alkalmazáshoz az AKS-ben.
* A forráskódot szinkronizálja a fejlesztői térben, amikor a növekményes buildek változása, amely lehetővé teszi a gyors iteráció.
* Lehetővé teszi, hogy a fejlesztői gépet közvetlenül az AKS-fürthöz csatlakoztassa.

Az ügyféloldali eszköza a parancssorból a `azds` parancs részeként használható. Az ügyféloldali eszközök a következőkkel is használhatók:

* Visual Studio-kód az [Azure Dev Spaces bővítmény](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)használatával.
* Visual Studio [a Visual Studio Tools for Kubernetes segítségével.](https://aka.ms/get-vsk8stools)

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni arról, hogy miként készül het elő és futtathat a kód dev-tárhelyén, olvassa el a [Projekt előkészítése az Azure Dev Spaces-hez című][how-it-works-prep]témakört.

Az Azure Dev Spaces csapatfejlesztéshez való használatának első lépései az [Azure Dev Spaces rövid útmutatójában.][quickstart-team]

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md