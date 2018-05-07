---
title: Automatizálja az operációs rendszer és a keretrendszer javítását az Azure tároló beállításjegyzék Build (ACR összeállítása)
description: ACR létrehozásához, egy csomag funkcióját biztosító biztonságos, Azure-tároló beállításjegyzék bevezetést automatikus tároló lemezkép összeállítása és a felhőben javítását.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 05/01/2018
ms.author: marsma
ms.openlocfilehash: 7506351c0d65fb167136478d2dc383cdabd81835
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Az operációs rendszer és a keretrendszer javítását az ACR Build automatizálása

Tárolók új virtualizációs, az infrastruktúra és működési követelmények alkalmazás és a fejlesztői függőségek elkülönítése tartalmaz. Mi marad, azonban szükség, cím, hogyan az alkalmazásvirtualizálás lett-e.

**ACR Build**, egy tartalmazó csomag Azure tároló beállításjegyzék, a szolgáltatásokat, nem csak natív tároló kép összeállítása képességet biztosít, de is automatizálja [az operációs rendszer és a keretrendszer javítás](#automate-os-and-framework-patching) az a Docker tárolókat.

> [!IMPORTANT]
> ACR összeállítása jelenleg előzetes verzióban érhetők, és csak az Azure-tárolót nyilvántartó támogatja a **USA keleti régiója** és **Nyugat-Európában** régiók. Az előzetes verziójú funkciók elérhetővé válnak, a feltétellel, hogy elfogadja a [kiegészítő használati feltételek][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="what-is-acr-build"></a>Mi az az ACR Build?

Egy Azure-natív kép összeállítása tárolószolgáltatás Azure tároló beállításjegyzék Build. Lehetővé teszi, hogy belső – hurok fejlesztési ACR összeállítása a felhőben, igény szerinti tároló lemezképpel épít fel és kód véglegesítési és végzett forráslemezkép automatizált épít frissítését.

Eseményindító tároló kép automatikusan épít, amikor kódot elkötelezte magát a Git-tárház, vagy amikor frissül egy tároló alapjául szolgáló lemezképhez. A frissítés eseményindítók alapjául szolgáló lemezképhez, az operációs rendszer automatizálhatja és alkalmazás-keretrendszer munkafolyamat, a rendszerbiztonsági tagok nem módosítható tároló megtartásával biztonságos környezetek fenntartása érdekében.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Gyors Build: belső – hurok terjeszteni a felhőben

Életciklus-felügyeletének elejére elindítja a fejlesztők véglegesíti a kód első sora előtt. ACR Build [gyors létrehozása](container-registry-tutorial-quick-build.md) szolgáltatás lehetővé teszi egy integrált helyi belső – hurok fejlesztési felület, kiszervezésével buildek az Azure-bA. A gyors hoz létre ellenőrizheti a automatizált build definíciók előtt véglegesítése a kódot.

Az ismerős használatával `docker build` formátum, a [az acr build] [ az-acr-build] parancs az Azure parancssori felületen időt vesz igénybe a helyi környezet, elküldi a ACR Build szolgáltatás, és alapértelmezés szerint leküldéses értesítések beépített kép során a beállításjegyzék létrehozása után. ACR Build követi a georeplikált nyilvántartó szétszórt fejlesztési csapat kihasználhatják a legközelebbi replikált beállításjegyzék engedélyezése. Előzetes ACR build nem áll rendelkezésre az USA keleti régiója és Nyugat-Európában területeket.

ACR Build út a hardvercseréhez egy egyszerű tároló életciklusát. Például integrálása ACR összeállítása a CI/CD megoldás. A következő futtatásával [az bejelentkezési] [ az-login] a egy [szolgáltatás egyszerű][az-login-service-principal], a CI/CD megoldás hogyan adhat ki volt [az acrösszeállítása] [ az-acr-build] parancsok futtatásával indítsa buildek lemezképet.

Gyors buildek használata az első ACR Build oktatóanyag [lemezképeket tároló a felhőben az Azure tároló beállításjegyzék Build](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>A forrás kód véglegesítési automatikus build

Automatikusan elindítható a tároló-lemezkép összeállítása ACR használata hozhat létre. Ha kód nem véglegesített Git-tárházba. Felépíteni, konfigurálható az Azure CLI paranccsal [az acr build-tevékenység][az-acr-build-task], egy Git-tárház és opcionálisan egy ágat és Dockerfile adhatók meg. A csapat kód véglegesíti a tárházhoz, amikor egy ACR Build által létrehozott webhook a tárházban definiált tároló kép build váltja ki.

Megtudhatja, hogyan indít buildek a második ACR Build oktatóanyag a forrás kód érvényesítéskor [automatizálás tároló kép hoz létre az Azure tároló beállításjegyzék Build](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Az operációs rendszer és a keretrendszer javítását automatizálása

A hatványa ACR Build valóban javítható a tároló-létrehozási folyamat képességét a észleli a frissítést az alapjául szolgáló lemezképhez, származik. Ha a beállításjegyzék kerül a frissített alapjául szolgáló lemezképhez, ACR Build automatikusan építhet bármely alkalmazás-lemezképek alapján.

Tároló képek körben osztályozhatók *alap* képek és *alkalmazás* képek. A kiinduló lemezképeket általában tartalmaznak, az operációs rendszer és az alkalmazás-keretrendszert, amelyen az alkalmazás épül, valamint más testreszabás is szerepelt. A kiinduló lemezképek maguk általában a alapján nyilvános fölérendelt képek, például [Alpine Linux] [ base-alpine] vagy [Node.js][base-node]. Előfordulhat, hogy ossza meg az alkalmazás-lemezképek számos egy közös alapjául szolgáló lemezképhez.

Az operációs rendszer vagy alkalmazás keretrendszer lemezkép frissítésekor a felsőbb rétegbeli karbantartó által például kritikus fontosságú az operációs rendszer biztonsági javítással, frissíteni kell a alap lemezképeket, hogy a kritikus hiba. Minden egyes alkalmazás-lemezképet kell majd is úgy, hogy ezek a felsőbb rétegbeli javítások, most már szerepel az alapjául szolgáló lemezképhez tartalmazza.

ACR Build dinamikusan felderíti az alapjául szolgáló lemezképhez függőségek, a tároló lemezkép stílusbeállításokat, mert azt képes észlelni, amikor egy alkalmazás-lemezképet alapjául szolgáló lemezképhez frissül. Egy előre konfigurált [feladat létrehozása](container-registry-tutorial-base-image-update.md#create-build-task), majd létre ACR **automatikusan újraépíti az összes alkalmazás-lemezképet** meg. Az automatikus észlelése és újraépítését, ACR Build menti az idő és erőfeszítés általában szükséges manuális nyomon követése és minden alkalmazás frissítése rendszerképet készítene a frissített alapjául szolgáló lemezképhez hivatkozik.

További tudnivalók az operációs rendszer és a keretrendszer javítását a harmadik ACR Build oktatóanyagban [automatizálás kép alapjául szolgáló lemezképhez frissítés épít, az Azure tároló beállításjegyzék Build](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> A kezdeti előzetes alapjául szolgáló lemezképhez frissítések eseményindító épít, csak akkor, ha az alkalmazás és a képek a azonos Azure-tárolót beállításjegyzék található.

## <a name="next-steps"></a>További lépések

Amikor készen áll a automatizálja az operációs rendszer és a keretrendszer által a felhőalapú tároló-lemezképek összeállításakor javítását, tekintse meg a három részből ACR Build oktatóanyag adatsorozat.

> [!div class="nextstepaction"]
> [Lemezképeket tároló a felhőben az Azure tároló beállításjegyzék összeállítása](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
