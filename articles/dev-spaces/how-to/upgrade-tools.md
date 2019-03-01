---
title: Azure fejlesztői tárolóhelyek eszközök frissítése
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/03/2018
ms.topic: conceptual
ms.technology: azds-kubernetes
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Container Service, tárolók
ms.openlocfilehash: b56c6069c255984e480b3e6b0e673d89e6c0ee3d
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194131"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Azure fejlesztői tárolóhelyek eszközök frissítése

Ha új kiadása és használata az Azure fejlesztési tárolóhelyek, szüksége lehet az Azure fejlesztési tárolóhelyek ügyfél eszközök frissítéséhez.

## <a name="update-the-azure-cli"></a>Az Azure parancssori felület frissítése

Azure CLI legújabb verziójára frissíti, is kap a fejlesztési tárolóhelyek CLI-bővítmény legújabb verziója.

Távolítsa el a korábbi verziót, csak keresse meg a megfelelő letölthető innen nem kell [Azure CLI-vel](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>A fejlesztői, szóközök CLI-bővítmény és a parancssori eszközök frissítése

Futtassa az alábbi parancsot:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>A VS Code-bővítmény frissítése

A telepítést követően automatikusan frissíti a bővítményt. Előfordulhat, hogy kell újra betölti a bővítményt, az új szolgáltatások használatához. A VS Code-ban nyissa meg a **bővítmények** ablaktáblán válassza ki a **Azure fejlesztési tárolóhelyek** bővítményeket, és válassza **Újrabetöltés**.

## <a name="update-the-visual-studio-extension"></a>A Visual Studio-bővítmény frissítése

Például a más bővítmények és frissítések, a Visual Studio értesíteni fogjuk, ha egy frissítés érhető el, a Visual Studio Tools for Kubernetes, az Azure fejlesztési szóközt is tartalmaz. A jelző ikonra a felső keresse meg a képernyő.

Frissítése az eszközök a Visual Studióban, válassza ki a **eszközök > bővítmények és frissítések** menü elem és a bal oldalon válassza a **frissíti**. Keresés **Visual Studio Tools for Kubernetes** , és válassza a **frissítés** gombra.

## <a name="next-steps"></a>További lépések

Az új eszközök teszteléséhez hozzon létre egy új fürtöt. Próbálja ki a rövid útmutatók és oktatóanyagok, [Azure fejlesztési tárolóhelyek](/azure/dev-spaces).

> [!WARNING]
> Az Azure fejlesztési tárolóhelyek meglévő fürtökön lesz azonnal javítható, és ellenőrizze, hogy az összes legújabb verzióját használja az Azure-környezetek, új fürt létrehozása az eszközök a frissítés után.
