---
title: Az Azure dev Spaces használatának engedélyezése az AK-ban & az ügyféloldali eszközök telepítése
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Ismerje meg, hogyan engedélyezheti az Azure dev Spaces szolgáltatást egy AK-fürtön, és hogyan telepítheti az ügyféloldali eszközöket.
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 0b7f6cb4a801c84df59bd5157d8c2a1a15eaaf7e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302899"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Az Azure dev-helyek engedélyezése egy AK-fürtön és az ügyféloldali eszközök telepítése

Ebből a cikkből megtudhatja, hogyan engedélyezheti az Azure dev Spaces szolgáltatást egy AK-fürtön, valamint telepítheti az ügyféloldali eszközöket.

## <a name="enable-azure-dev-spaces-using-the-cli"></a>Az Azure dev Spaces használatának engedélyezése a parancssori felületről

Ahhoz, hogy a parancssori felületről engedélyezzék a dev Spaces használatát, a következőkre lesz szüksége:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot][az-portal-create-account].
* [Az Azure CLI telepítve van][install-cli].
* Egy [AK-fürt][create-aks-cli] egy [támogatott régióban][supported-regions].

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

A fenti parancs lehetővé teszi a dev Spaces használatát a *myAKSCluster* -fürtön a *myResourceGroup* csoportban, és létrehoz egy *alapértelmezett* fejlesztői helyet.

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

Az `use-dev-spaces` parancs az Azure dev Spaces CLI-t is telepíti.

## <a name="enable-azure-dev-spaces-using-the-azure-portal"></a>Az Azure dev Spaces használatának engedélyezése a Azure Portal

Mielőtt a Azure Portal használatával engedélyezzük a fejlesztői szóközöket, a következőkre lesz szüksége:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot][az-portal-create-account].
* Egy [AK-fürt][create-aks-portal] egy [támogatott régióban][supported-regions].

Az Azure dev Spaces használatának engedélyezése a Azure Portal használatával:
1. Jelentkezzen be az [Azure Portal][az-portal].
1. Navigáljon az AK-fürthöz.
1. Válassza ki a *dev Spaces* menüpontot.
1. Módosítsa a *fejlesztői tárhelyek engedélyezése* *beállítást igen* értékre, és kattintson a *Mentés*gombra.

![Fejlesztői szóközök engedélyezése a Azure Portalban](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Az Azure dev-helyek a Azure Portal használatával történő engedélyezése **nem** telepít ügyféloldali eszközöket az Azure dev Spaces szolgáltatáshoz.

## <a name="install-the-client-side-tools"></a>Az ügyféloldali eszközök telepítése

Az Azure dev Spaces ügyféloldali eszközeivel a helyi gépről a fejlesztői tereket egy AK-fürtön keresztül használhatja. Az ügyféloldali eszközök több módon is telepíthetők:

* A [Visual Studio Code][vscode]-ban telepítse az [Azure dev Spaces bővítményt][vscode-extension].
* A [Visual Studio 2019][visual-studio]-es verziójában telepítse az Azure-fejlesztési számítási feladatot.
* A Visual Studio 2017-es verziójában telepítse a webes fejlesztés számítási feladatait és a [Visual Studio-eszközöket a Kubernetes][visual-studio-k8s-tools].
* Töltse le és telepítse a [Windows][cli-win], [Mac][cli-mac]vagy [Linux][cli-linux] parancssori felületet.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-development-qs]

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
