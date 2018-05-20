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
ms.openlocfilehash: e9f97f804985f948e5442c64a31d95e7931b03cd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Kód Docker és Kubernetes fejlesztés előkészítése
Eddig hogy egy egyszerű webalkalmazást helyileg futtathat. Akkor lesz most containerize az eszközök, amelyek meghatározzák az alkalmazás tárolót, és hogyan telepíti a Kubernetes létrehozásával. Ez az Azure fejlesztői szóközöket csupán: 

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
 
Nevű fájl `./azds.yaml` is állítja elő a `prep` parancsot, és az Azure fejlesztői tárolóhelyek konfigurációs fájlját. Kiegészíti a Docker és Kubernetes összetevők további konfiguráció egy iteratív fejlesztési felület, amellyel az Azure-ban. Például az alapértelmezett Helm diagram nem fed fel nyilvános végpontok. Egyes esetekben azonban célszerű ideiglenesen megnyitása egy nyilvános végpontot, amellyel tesztelheti a kódját, mondja ki a fejlesztés során, a mobil eszköz vagy a webhook URL-CÍMÉT. Használatával létrehozott azds.yaml fájl `azds prep --public` felülbírálja a Helm alapértelmezett paraméterek teszi közzé egy nyilvános végpontot csak a fejlesztési idő.
