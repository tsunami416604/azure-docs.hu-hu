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
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938172"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Kód Docker és Kubernetes fejlesztés előkészítése
Eddig hogy egy egyszerű webalkalmazást helyileg futtathat. Akkor lesz most containerize az eszközök, amelyek meghatározzák az alkalmazás tárolót, és hogyan telepíti a Kubernetes létrehozásával. Ez a feladat áll Azure fejlesztői szóközök: 

1. Indítsa el a Visual STUDIO Code, és nyissa meg a `webfrontend` mappát. (Figyelmen kívül hagyhatja hibakeresési eszközök hozzáadása vagy a projekt visszaállítása alapértelmezett figyelmeztetéseket.)
1. Nyissa meg a integrált terminál Visual STUDIO Code (használja a **Nézet > integrált Terminálszolgáltatások** menü).
1. Futtassa ezt a parancsot (ügyeljen arra, hogy **webfrontend** van az aktuális mappát):

    ```cmd
    azds prep --public
    ```

Az Azure parancssori felület `azds prep` parancs létrehozza az alapértelmezett beállításokkal Docker és Kubernetes eszközök:
* `./Dockerfile` az alkalmazás tároló lemezképet, és hogyan forráskódját épül, és a tárolóban fut ismerteti.
* A [Helm diagram](https://docs.helm.sh) alatt `./charts/webfrontend` ismerteti, hogyan lehet telepíteni a tároló Kubernetes.

Egyelőre nem szükséges tudni, hogy ezek a fájlok teljes tartalmát. Érdemes mutat, azonban, hogy **az azonos Kubernetes és Docker konfigurációs, kód eszközök segítségével keresztül fejlesztési éles, lehetővé téve jobb konzisztencia különböző környezetek között.**
 
Nevű fájl `./azds.yaml` is állítja elő a `prep` parancsot, és az Azure fejlesztői tárolóhelyek konfigurációs fájlját. Kiegészíti a Docker és Kubernetes összetevők további konfiguráció egy iteratív fejlesztési felület, amellyel az Azure-ban.
