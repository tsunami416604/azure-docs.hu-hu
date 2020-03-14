---
title: Az Azure dev Spaces-eszközök frissítése
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Ismerje meg, hogyan frissítheti az Azure dev Spaces parancssori eszközöket, a Visual Studio Code-bővítményt és a Visual Studio-bővítményt
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265232"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Az Azure dev Spaces-eszközök frissítése

Ha van új kiadás, és már használja az Azure dev Spaces-t, lehet, hogy frissítenie kell az Azure dev Spaces-ügyféleszközök eszközeit.

## <a name="update-the-azure-cli"></a>Az Azure CLI frissítése

Amikor frissíti a legújabb Azure CLI-t, a dev Spaces CLI bővítmény legújabb verzióját is letöltheti.

Nem kell eltávolítania a korábbi verziót, csak keresse meg a megfelelő letöltést az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)-ben.


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>A dev Spaces CLI bővítmény és a parancssori eszközök frissítése

Futtassa az alábbi parancsot:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>A VS Code bővítmény frissítése

A telepítés után a bővítmény automatikusan frissül. Előfordulhat, hogy újra kell töltenie a bővítményt az új funkciók használatához. A VS Code-ban nyissa meg a **bővítmények** ablaktáblát, válassza ki az **Azure dev Spaces** bővítményeket, és válassza az **Újratöltés**lehetőséget.

## <a name="update-the-visual-studio-extension"></a>A Visual Studio bővítmény frissítése

A többi bővítményhez és frissítéshez hasonlóan a Visual Studio is értesíti Önt, ha rendelkezésre áll egy frissítés a Visual Studio Tools for Kubernetes, amely tartalmazza az Azure dev Spaces szolgáltatást. A képernyő jobb felső részén keresse meg a jelölő ikont.

Az eszközök a Visual Studióban való frissítéséhez válassza az **eszközök > bővítmények és frissítések** menüpontot, és a bal oldalon válassza a **frissítések**lehetőséget. Keresse meg a **Kubernetes Visual Studio-eszközöket** , és kattintson a **frissítés** gombra.

## <a name="next-steps"></a>Következő lépések

Tesztelje az új eszközöket új fürt létrehozásával. Próbálja ki a gyors útmutatókat és oktatóanyagokat az [Azure dev Spaces](/azure/dev-spaces)webhelyen.
