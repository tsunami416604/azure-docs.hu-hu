---
title: Alapképfrissítések - Feladatok
description: Ismerje meg az alkalmazástároló-lemezképek alaplemezképeit, és azt, hogy egy alaprendszerkép-frissítés hogyan indíthat el egy Azure Container Registry feladatot.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617930"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Az ACR-feladatok alapképfrissítései

Ez a cikk háttér-információkat tartalmaz az alkalmazás alaplemezképének frissítéseiről, és arról, hogy ezek a frissítések hogyan indíthatnak el egy Azure Container Registry feladatot.

## <a name="what-are-base-images"></a>Mik azok az alapképek?

A legtöbb tárolórendszerképet definiáló Dockerfiles egy szülőlemezképet határoz meg, amelyből a rendszerkép alapul, amelyet gyakran *alaprendszerképnek*is neveznek. Az alapként szolgáló rendszerképek általában az operációs rendszert tartalmazzák (például [Alpine Linux][base-alpine] vagy [Windows Nano Server][base-windows]), amelyre a tároló többi szintje alkalmazva lesz. Alkalmazás-keretrendszereket is tartalmazhatnak, például a [Node.js][base-node] vagy a [.NET Core][base-dotnet] keretrendszert. Ezek az alapképek maguk általában nyilvános upstream képeken alapulnak. Előfordulhat, hogy az alkalmazáslemezképek közül több közös alaplemezképet is megoszt.

A rendszerképek kezelői gyakran frissítik az alapként szolgáló rendszerképet, hogy új szolgáltatásokkal és javításokkal bővítsék a rendszerképben található operációs rendszert vagy keretrendszert. A biztonsági javítások jelentik az alapként szolgáló rendszerkép frissítésének egy másik gyakori okát. Amikor ezek a felső streamelési frissítések bekövetkeznek, az alaplemezképeket is frissítenie kell, hogy tartalmazzák a kritikus javítást. Minden alkalmazáslemezképet újra kell építeni, hogy tartalmazza ezeket a felsőáramú javításokat, amelyek most már szerepelnek az alaplemezképben.

Bizonyos esetekben, például egy privát fejlesztői csapat, egy alaprendszerkép több, mint operációs rendszer vagy keretrendszer. Egy alaplemezkép például lehet egy megosztott szolgáltatásösszetevő-lemezkép, amelyet nyomon kell követni. Előfordulhat, hogy a csapat tagjainak nyomon kell követniük ezt az alaplemezképet tesztelésre, vagy rendszeresen frissíteniük kell a lemezképet az alkalmazásképek fejlesztése kor.

## <a name="track-base-image-updates"></a>Alapképfrissítéseinek nyomon követése

Az ACR Tasks lehetővé teszi a rendszerképek automatikus összeállítását a tárolók alapként szolgáló rendszerképének frissítésekor.

Az ACR-feladatok dinamikusan észlelik az alaprendszerkép-függőségeket, amikor tárolólemezképet hoz létre. Ennek eredményeképpen észleli, ha egy alkalmazáskép alaplemezképe frissül. Egy előre konfigurált buildfeladattal az ACR-feladatok automatikusan újraépíthetik az alaplemezképet hivatkozó összes alkalmazásképet. Ezzel az automatikus észleléssel és újraépítéssel az ACR-feladatok időt és energiát takarítanak meg, amelyek általában szükségesek a frissített alaplemezképre hivatkozó minden egyes alkalmazáskép manuális nyomon követéséhez és frissítéséhez.

## <a name="base-image-locations"></a>Alapképhelyek

A Docker-fájlból származó lemezképek esetén az ACR-feladat a következő helyeken észleli az alaplemezképek függőségeit:

* Ugyanaz az Azure-tároló beállításjegyzéke, ahol a feladat fut
* Egy másik privát Azure-tároló beállításjegyzék ugyanabban vagy egy másik régióban 
* Nyilvános tárházban a Docker Hubban 
* Nyilvános tártár a Microsoft Container Registry szolgáltatásban

Ha az utasításban megadott `FROM` alaplemezkép ezen helyek egyikén található, az ACR-feladat hozzáad egy horgot, amely biztosítja, hogy a rendszerkép bármikor újraépüljön, amikor az alap frissül.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

* **Alaplemezképek alkalmazásképek** – Jelenleg egy ACR feladat csak nyomon követi az alapkép frissítések alkalmazás (*futásidejű*) képek. Nem követi nyomon a többlépcsős Dockerfiles-okban használt köztes (*buildidő)* lemezképek alaprendszerkép-frissítéseit.  

* **Alapértelmezés szerint engedélyezve** – Ha acr-feladatot az [az acr feladat létrehozási][az-acr-task-create] paranccsal hoz létre, alapértelmezés szerint a feladatot egy alapképfrissítés *engedélyezi* az aktiváláshoz. Ez azt, `base-image-trigger-enabled` hogy az ingatlan értéke Igaz. Ha le szeretné tiltani ezt a viselkedést egy feladatban, frissítse a tulajdonságot Hamis értékűre. Futtassa például a következő [az acr feladatfrissítési][az-acr-task-update] parancsot:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **A függőségek nyomon követésének eseményindítója** – Ahhoz, hogy egy ACR-feladat megállapíthassa és nyomon követhesse a tárolórendszerkép függőségeit – amelyek tartalmazzák az alaplemezképet –, először el kell indítania a feladatot a lemezkép **legalább egyszer**való létrehozásához. Indítsa el például a feladatot manuálisan az [az acr feladatfuttatásparalat.][az-acr-task-run]

* **Stabil címke az alapképhez** – Az alapkép frissítésén elindítandó feladat `node:9-alpine`elindításához az alapképnek *stabil* címkével kell rendelkeznie, például . Ez a címkézés jellemző egy alaprendszerkép, amely frissített operációs rendszer és a keretrendszer javítások at a legújabb stabil kiadás. Ha az alaplemezképet új verziócímkével frissítik, az nem indít el feladatot. A képcímkézésről további információt az [ajánlott eljárásokra vonatkozó útmutatóban](container-registry-image-tag-version.md)talál. 

* **Egyéb feladateseményindítók** – Az alaplemezkép-frissítések által indított feladatban engedélyezheti az eseményindítókat a [forráskód véglegesítése](container-registry-tutorial-build-task.md) vagy [ütemezése](container-registry-tasks-scheduled.md)alapján is. Az alaplemezkép frissítése [többlépéses feladatot](container-registry-tasks-multi-step.md)is elindíthat.

## <a name="next-steps"></a>További lépések

Az alaplemezkép frissítése után az alkalmazáskép-felépítéseket az alábbi oktatóanyagokban láthatja el:

* [A tárolórendszerkép automatikus létrehozása, ha egy alaprendszerkép ugyanabban a beállításjegyzékben frissül](container-registry-tutorial-base-image-update.md)

* [A tárolórendszerkép automatizálása, ha egy alaprendszerkép egy másik beállításjegyzékben frissül](container-registry-tutorial-base-image-update.md)


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
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
