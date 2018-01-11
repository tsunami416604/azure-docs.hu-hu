---
title: "Bevezetés az Azure Container Service for Kubernetes használatába"
description: "Az Azure Container Service for Kubernetes használatával egyszerűen helyezhetőek üzembe és felügyelhetőek a tárolóalapú alkalmazások az Azure-ban."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 2980d4733dcf138c3a9aa1713cff994bbf5a296c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Az Azure Container Service (AKS) bemutatása

Az Azure Container Service (AKS) elősegíti a virtuális gépek egy olyan fürtjének egyszerűbb létrehozását, konfigurálását és kezelését, amely tárolóalapú alkalmazások futtatására lett konfigurálva. Így lehetővé válik, hogy a meglévő ismereteit használja, vagy egy nagy és egyre növekvő közösségi szakértelmet vegyen igénybe a tárolóalapú alkalmazások Microsoft Azure-on történő üzembe helyezéséhez és kezeléséhez.

Az AKS használatával igénybe veheti az Azure nagyvállalati szintű funkcióit, miközben továbbra is fenntartja az alkalmazás-hordozhatóságot a Kubernetes és a Docker rendszerképformátuma révén.

## <a name="managed-kubernetes-in-azure"></a>Felügyelt Kubernetes az Azure-ban

Az AKS egyszerűsíti a Kubernetes-fürtök kezelését és csökkenti annak működési munkaterhelését azzal, hogy a felelősség nagy részért az Azure-ra helyezi át. Üzemeltetett Kubernetes-szolgáltatásként az Azure olyan fontos műveleteket bonyolít le, mint az állapotmonitorozás és a karbantartás. Emellett ebben az esetben csak a fürtjei ügynökcsomópontjaiért kell fizetnie, a fő csomópontokért nem. Üzemeltetett Kubernetes-szolgáltatásként az AKS a következőket biztosítja:

> [!div class="checklist"]
> * Automatizált Kubernetes-verziófrissítés és -javítás
> * Egyszerű fürtméretezés
> * Üzemeltetett vezérlési sík önjavítása (fő csomópontok esetében)
> * Költségmegtakarítás – csak a futó ügynök-készletcsomópontokért kell fizetnie

Ha az Azure bonyolítja az AKS-fürt csomópontjainak kezelését, nem kell többé manuálisan végezni az olyan műveleteket, mint a fürtfrissítések. Mivel a legfontosabb karbantartási műveleteket az Azure végzi, az AKS nem ad közvetlen hozzáférést (például SSH-n keresztül) a fürthöz.

## <a name="using-azure-container-service-aks"></a>Az Azure Container Service (AKS) használata
Az AKS célja az, hogy a manapság az ügyfelek körében népszerű nyílt forráskódú eszközök és technológiák használatával nyújtson tárolóüzemeltetési környezetet. E célból elérhetővé tesszük a standard Kubernetes API-végpontokat. A standard végpontokkal bármely olyan szoftvert használhat, amely képes a Kubernetes-fürtökkel folytatott kommunikációra. Például választhatja a következőket: [kubectl][kubectl-overview], [helm][helm] vagy [draft][draft].

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Kubernetes-fürt létrehozása az Azure Container Service (AKS) használatával
Az AKS használatához üzembe kell helyeznie egy AKS-fürtöt az [Azure CLI][aks-quickstart] használatával vagy a portálon keresztül (keressen a piactéren az **Azure Container Service** kifejezésre). Amennyiben Ön tapasztalt felhasználó, és átfogóbb vezérlésre van szüksége az Azure Resource Manager-sablonok fölött, a nyílt forráskódú [acs-engine][acs-engine] projekt segítségével felépíthet egy saját egyéni Kubernetes-fürtöt, és üzembe helyezheti azt az `az` parancssori felület használatával.

### <a name="using-kubernetes"></a>A Kubernetes használata
A Kubernetes automatizálja a tárolóalapú alkalmazások üzembe helyezését, méretezését és felügyeletét. Többek között a következő funkciókat tartalmazza:
* Automatikus bin-csomagolás
* Önjavítás
* Vízszintes méretezés
* Szolgáltatásészlelés és terheléselosztás
* Automatizált kibocsátások és visszaállítások
* Titkos kódok és konfigurációk kezelése
* Tárolóvezénylés
* Kötegelt végrehajtás

## <a name="videos"></a>Videók

Azure Container Service (AKS) – Azure Friday, 2017. október:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Eszközök az alkalmazások fejlesztéséhez és üzembe helyezéséhez a Kubernetes szolgáltatásban – Azure OpenDev, 2017. június:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Az AKS gyors útmutatójának segítségével többet tudhat meg az AKS üzembe helyezéséről és felügyeletéről.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

