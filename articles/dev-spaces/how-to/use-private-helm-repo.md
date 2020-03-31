---
title: Privát Helm-tárház használata az Azure dev spaces-ben
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Használjon egy privát Helm-tárházat egy Azure Dev Space-ben.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, tárolók, Helm
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240469"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Privát Helm-tárház használata az Azure dev spaces-ben

[Helm][helm] a Kubernetes csomagkezelője. A Helm [diagramformátumot][helm-chart] használ a függőségek csomagolásához. A helm diagramok egy tárházban tárolódnak, amely lehet nyilvános vagy privát. Az Azure Dev Spaces csak az alkalmazás futtatásakor lekéri a helm-diagramokat a nyilvános adattárakból. Azokban az esetekben, ahol a Helm-tárház privát vagy az Azure Dev Spaces nem tud hozzáférni, hozzáadhat egy diagramot, hogy a tárház közvetlenül az alkalmazáshoz. A diagram hozzáadása közvetlenül lehetővé teszi az Azure Dev Spaces futtatásához az alkalmazást anélkül, hogy a privát Helm tárház eléréséhez.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Adja hozzá a privát Helm-tárházat a helyi számítógéphez

Helm [repo add][helm-repo-add] és [helm repo frissítés][helm-repo-update] használatával a helyi helm-tárház helyi gépről való eléréséhez.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>A diagram hozzáadása az alkalmazáshoz

Nyissa meg a projekt könyvtárát, és futtassa a programot. `azds prep`

```cmd
azds prep --enable-ingress
```

> [!TIP]
> A `prep` parancs [docker-fájl- és helmdiagramot](../how-dev-spaces-works-prep.md#prepare-your-code) próbál létrehozni a projekthez. Az Azure Dev Spaces ezeket a fájlokat használja a kód létrehozásához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítését és futtatását.

Hozzon létre egy [requirements.yaml][helm-requirements] fájlt a diagrammal az alkalmazás diagramkönyvtárában. Ha például az alkalmazás neve *app1,* *akkor diagramokat/app1/requirements.yaml-t kell létrehoznia.*

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Keresse meg az alkalmazás diagramkönyvtárát, és [helm függőségi frissítés][helm-dependency-update] használatával frissítse az alkalmazás Helm-függőségeit, és töltse le a diagramot a magántárházból.

```cmd
helm dependency update
```

Ellenőrizze, hogy egy *tgz-fájlt* tartalmazó *diagramalkönyvtár* at adott-e hozzá az alkalmazás diagramkönyvtárához. Például *diagramok/app1/diagramok/mychart-0.1.0.tgz*.

A privát Helm-tárházból származó diagram le lett töltve, és hozzáadva a projekthez. Távolítsa el a *requirements.yaml* fájlt, hogy a fejlesztői tárolók ne kíséreljék meg frissíteni ezt a függőséget.

## <a name="run-your-application"></a>Az alkalmazás futtatása

Keresse meg a projekt gyökérkönyvtárát, és futtassa `azds up` az alkalmazás sikeres futtatásának ellenőrzését a fejlesztői területen.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>További lépések

Tudjon meg többet [a Helm és hogyan működik][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
