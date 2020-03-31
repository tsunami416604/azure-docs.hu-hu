---
title: Az Azure Dev Spaces eszközök frissítése
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Megtudhatja, hogy miként frissítheti az Azure Dev Spaces parancssori eszközeit, a Visual Studio-kódbővítményt és a Visual Studio-bővítményt
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, tárolók
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265232"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Az Azure Dev Spaces eszközök frissítése

Ha van egy új kiadás, és már használja az Azure Dev Spaces, előfordulhat, hogy frissítenie kell az Azure Dev Spaces ügyféleszközök.

## <a name="update-the-azure-cli"></a>Az Azure CLI frissítése

A legújabb Azure CLI frissítésekor a Fejlesztői tereket CLI-bővítmény legújabb verzióját is beolvassa.

Nem kell eltávolítania az előző verziót, csak keresse meg a megfelelő letöltést az [Azure CLI-ben.](/cli/azure/install-azure-cli?view=azure-cli-latest)


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>A fejlesztői terhelyek CLI-bővítményének és parancssori eszközeinek frissítése

Futtassa az alábbi parancsot:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>A VS-kód bővítmény frissítése

A telepítés után a bővítmény automatikusan frissül. Előfordulhat, hogy az új szolgáltatások használatához újra be kell töltenie a bővítményt. A VS-kódban nyissa meg a **Bővítmények** ablaktáblát, válassza az Azure Dev Spaces-bővítményeket, és válassza az **Újratöltés**lehetőséget. **Azure Dev Spaces**

## <a name="update-the-visual-studio-extension"></a>A Visual Studio bővítmény frissítése

Más bővítményekhez és frissítésekhez hasonlóan a Visual Studio is értesíti, ha elérhető egy frissítés a Visual Studio Tools for Kubernetes számára, amely tartalmazza az Azure Dev Spaces szolgáltatást is. Keressen egy zászlóikont a képernyő jobb felső részén.

A Visual Studio eszközeinek frissítéséhez válassza az **Eszközök > Bővítmények és frissítések** menüelemet, majd a bal oldalon válassza a Frissítések **lehetőséget.** Keresse meg **a Visual Studio Tools for Kubernetes alkalmazást,** és válassza a **Frissítés** gombot.

## <a name="next-steps"></a>További lépések

Tesztelje az új eszközöket egy új fürt létrehozásával. Próbálja ki a rövid útmutatókat és az oktatóanyagokat az [Azure Dev Spaces](/azure/dev-spaces)webhelyen.
