---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991015"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Kód előkészítése Docker- vagy Kubernetes-fejlesztéshez
Már rendelkezik egy helyileg futó alapszintű webalkalmazással. Most olyan objektumok létrehozásával egy tárolóba fogja helyezni, amelyek meghatározzák az alkalmazás tárolóját, és a Kubernetesben történő üzembe helyezésének módját. Ez a feladat az Azure Dev Spaces segítségével egyszerűen elvégezhető: 

1. Indítsa el a VS Code-ot, majd nyissa meg a `webfrontend` mappát. (Figyelmen kívül hagyhat minden olyan alapértelmezett kérést, amely az objektumok hibakeresésére vagy a projekt visszaállítására vonatkozik.)
1. Nyissa meg a VS Code Integrált terminálját (a **View (Nézet) > Integrated Terminal (Integrált terminál)** menüvel).
1. Futtassa ezt a parancsot (győződjön meg róla, hogy a **webfrontend** a jelenlegi mappája):

    ```cmd
    azds prep --public
    ```

Az Azure CLI `azds prep` parancsa alapértelmezett beállításokkal hoz létre Docker- és Kubernetes-objektumokat:
* A `./Dockerfile` ismerteti az alkalmazás tárolólemezképét, illetve hogy a forráskód hogyan épül fel és hogyan fut a tárolón belül.
* A `./charts/webfrontend` alatt található [Helm-diagram](https://docs.helm.sh) ismerteti a konténer Kubernetesben történő üzembe helyezését.

Egyelőre még nem fontos a fájlok teljes tartalmát megérteni. Ugyanakkor azt megjegyeznénk, hogy **használhatja ugyanazokat a Docker és a Kubernetes kóddal való konfigurálási objektumokat a fejlesztéstől kezdve egészen az éles környezetig, így jobb konzisztenciát biztosíthat a különböző környezetekben.**
 
A `prep` parancs egy `./azds.yaml` nevű fájlt is létrehoz, amely az Azure Dev Spaces konfigurációs fájlja. Ez olyan további konfigurációval egészíti ki a Docker- és Kubernetes-munkadarabokat, amely engedélyez az Azure-ban egy iteratív fejlesztési környezetet.
