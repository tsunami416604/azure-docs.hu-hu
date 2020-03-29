---
title: Az Azure Dev Spaces engedélyezése az AKS-en & telepítse az ügyféloldali eszközöket
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Megtudhatja, hogyan engedélyezheti az Azure Dev Spaces-t egy AKS-fürtön, és hogyan telepítheti az ügyféloldali eszközöket.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898948"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Az Azure Dev Spaces engedélyezése AKS-fürtön és az ügyféloldali eszközök telepítése

Ez a cikk az Azure Dev Spaces AKS-fürtön való engedélyezésének, valamint az ügyféloldali eszközök telepítésének számos módját mutatja be.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Az Azure Dev Spaces engedélyezése vagy eltávolítása a CLI használatával

A fejlesztői szóközök CLI használatával történő engedélyezéséhez a következőkre van szükség:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot][az-portal-create-account].
* [Az Azure CLI telepítve van.][install-cli]
* [AKS-fürt][create-aks-cli] támogatott [régióban.][supported-regions]

A `use-dev-spaces` paranccsal engedélyezheti a fejlesztői szóközöket az AKS-fürtön, és kövesse az utasításokat.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

A fenti parancs engedélyezi a dev spaces a *myAKSCluster* fürt a *myResourceGroup* csoportban, és létrehoz egy *alapértelmezett* fejlesztői terület.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

A `use-dev-spaces` parancs az Azure Dev Spaces CLI-t is telepíti.

Az Azure Dev Spaces eltávolítása az AKS-fürtből, használja a `azds remove` parancsot. Példa:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

A fenti parancs eltávolítja az Azure dev spaces-t a *MyResourceGroup* *MyAKS* fürtjéből. Az Azure Dev Spaces használatával létrehozott névterek a számítási feladatokkal együtt maradnak, de az ezekben a névterekben lévő új számítási feladatok nem lesznek instrumentátorolva az Azure Dev Spaces-szel. Emellett ha újraindítja az Azure Dev Spaces-szel instrumentált meglévő podokat, hibák jelenhetnek meg. Ezeket a podokat az Azure Dev Spaces eszközök nélkül kell újratelepíteni. Az Azure Dev Spaces teljes eltávolításához a fürtből törölje az összes podot minden olyan névtérből, ahol engedélyezve volt az Azure Dev Spaces.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Az Azure Dev Spaces engedélyezése vagy eltávolítása az Azure Portalon

A fejlesztői szóközök az Azure Portalhasználatával való engedélyezéséhez a következőkre van szüksége:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot][az-portal-create-account].
* [AKS-fürt][create-aks-portal] támogatott [régióban.][supported-regions]

Az Azure Dev Spaces engedélyezése az Azure Portalhasználatával:
1. Jelentkezzen be az [Azure Portalra.][az-portal]
1. Nyissa meg az AKS-fürtöt.
1. Válassza a *Fejlesztői terek* menüelemet.
1. Módosítsa *a Fejlesztői szóközök engedélyezése* lehetőséget *Igen* értékre, és kattintson a *Mentés gombra.*

![Fejlesztői tárolók engedélyezése az Azure Portalon](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Az Azure Dev Spaces azure-portál használatával történő engedélyezése **nem** telepít ügyféloldali eszközöket az Azure Dev Spaces számára.

Ha el szeretné távolítani az Azure Dev Spaces-t az AKS-fürtből, módosítsa *a Fejlesztői terek engedélyezése* *nem* értékre, és kattintson a *Mentés gombra.* Az Azure Dev Spaces használatával létrehozott névterek a számítási feladatokkal együtt maradnak, de az ezekben a névterekben lévő új számítási feladatok nem lesznek instrumentátorolva az Azure Dev Spaces-szel. Emellett ha újraindítja az Azure Dev Spaces-szel instrumentált meglévő podokat, hibák jelenhetnek meg. Ezeket a podokat az Azure Dev Spaces eszközök nélkül kell újratelepíteni. Az Azure Dev Spaces teljes eltávolításához a fürtből törölje az összes podot minden olyan névtérből, ahol engedélyezve volt az Azure Dev Spaces.

## <a name="install-the-client-side-tools"></a>Az ügyféloldali eszközök telepítése

Az Azure Dev Spaces ügyféloldali eszközök segítségével kezelheti a fejlesztési terek egy AKS-fürtön a helyi gépről. Az ügyféloldali eszközök többféleképpen telepíthetők:

* A [Visual Studio Code alkalmazásban][vscode]telepítse az Azure Dev Spaces [bővítményt.][vscode-extension]
* A [Visual Studio 2019-ben][visual-studio]telepítse az Azure Development számítási feladatokat.
* A Visual Studio 2017-ben telepítse a Webdevelopment munkaterhelést és a [Visual Studio Tools for Kubernetes][visual-studio-k8s-tools]alkalmazást.
* Töltse le és telepítse a [Windows][cli-win], [Mac][cli-mac]vagy [Linux][cli-linux] CLI.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Csapatfejlesztés az Azure Dev Spaces-ben][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
