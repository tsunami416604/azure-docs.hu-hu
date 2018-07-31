---
title: Automatizálja az operációs rendszer és a keretrendszert az Azure Container Registry létrehozása (ACR Builddel) javítása
description: ACR Builddel, egy csomag az Azure Container Registry védelmét biztosító funkcióit bemutató automatikus tároló-rendszerkép összeállítását és a javítással, a felhőben.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 07/28/2018
ms.author: marsma
ms.openlocfilehash: 532817c6289c1718fd82a502e04dc10715ee7203
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343100"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatizálja az operációs rendszer és a keretrendszer javítás ACR builddel

Tárolók adja meg az új szintet jelentenek a virtualizálás, alkalmazás és a fejlesztői függőségek infrastruktúra-és működési követelmények elkülönítése. Milyen állapotban marad, azonban szükség, cím, hogyan van az application virtualization tudjon fókuszálni.

**ACR Builddel** Azure Container Registry lévő szolgáltatások együttese. A Linux, Windows és ARM biztosít a felhőalapú tároló-lemezkép létrehozása, és automatizálható [operációs rendszer és a keretrendszer javítása](#automate-os-and-framework-patching) a Docker-tárolók számára.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>Mi az ACR Builddel?

Az Azure Container Registry létrehozása szolgáltatása egy natív Azure tároló rendszerképet hozhat létre. ACR Builddel lehetővé teszi, hogy belső-hurok fejlesztés a felhőben, igény szerinti tárolórendszerképet hoz létre, és automatizált buildekig kód érvényesítése és alap forráslemezkép a frissítése.

Eseményindító tárolórendszerkép automatikusan épít, amikor a kód számára fontos, hogy egy Git-tárház, vagy amikor frissül egy tároló alaplemezkép. Az alaplemezkép frissítés eseményindítók, automatizálhatja az operációs rendszer és alkalmazási keretrendszer munkafolyamat, biztonságos környezetben fenntartása a rendszerbiztonsági tagok nem módosítható a tárolók megtartásával javítása.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Gyors Build: belső ciklikus kiterjesztése a felhőbe a

Az életciklus-felügyelet elején elindítja a fejlesztők számára az első sornyi kód véglegesítése előtt. ACR Builddel [gyors létrehozása](container-registry-tutorial-quick-build.md) funkció lehetővé teszi egy integrált helyi belső-hurok fejlesztési környezetet biztosít, tehermentesítést buildeket, az Azure-bA. A gyors hoz létre ellenőrizheti az automatizált buildelési definíciókat a kód véglegesítése előtt.

Az ismert `docker build` formátum, a [az acr-build] [ az-acr-build] parancs az Azure CLI-ben vesz igénybe egy helyi környezetet, elküldi azt az ACR Builddel szolgáltatásnak, és alapértelmezés szerint leküldi az összeállított rendszerképet a beállításjegyzékbe, Befejezés. ACR Builddel követi a georeplikált tárolójegyzékek elosztott fejlesztői csapatok kihasználhatja a legközelebbi replikált beállításjegyzék engedélyezése.

ACR Builddel tervezték, mint egy egyszerű tároló életciklus. Ha például integrálhatja az ACR Builddel CI/CD-megoldását. Végrehajtásával [az bejelentkezési] [ az-login] az egy [szolgáltatásnév][az-login-service-principal], CI/CD-megoldását sikerült majd ki [az acr builddel] [ az-acr-build] parancsok felhőplatformos termékeiért buildek kép.

Megtudhatja, hogyan használja a gyors hoz létre az első ACR Builddel oktatóanyagban [felhőbeli tárolórendszerképek létrehozása az Azure Container Registry létrehozása](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>A forrás kód véglegesítésére automatikus build

Amikor a kód számára fontos, hogy egy Git-tárház használata ACR Builddel tárolórendszerkép aktiválják automatikusan hozhat létre. Buildekkel kapcsolatos feladatok, konfigurálható az Azure CLI-paranccsal [az acr-build-tevékenység][az-acr-build-task], lehetővé teszik egy Git-tárház és opcionálisan egy ágat és docker-fájlban adja meg. Csapata a tárházba kód véglegesítésére, amikor egy ACR Builddel által létrehozott webhook aktiválja a tároló rendszerképét az adattárban lévő meghatározott build.

Ismerje meg, hogyan build aktiválása a forrás kód véglegesítés az ACR Builddel második oktatóanyagban [automatizálása tárolórendszerképet hoz létre az Azure Container Registry létrehozása](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizálja az operációs rendszer és a keretrendszer javítása

Hatékonyságát ACR Builddel valóban növelése érdekében a tároló létrehozási folyamat képességét a frissítés alapképet észleléséhez származik. Ha a frissített alaplemezkép van leküldte a tárolójegyzékbe, ACR Builddel automatikusan hozhat létre bármilyen alkalmazás-lemezképek alapján.

Tárolórendszerképek széles körben kategorizálhatók *alap* lemezképek és *alkalmazás* lemezképek. Az alaplemezképek rendszerint az operációs rendszer és alkalmazási keretrendszerek, amelyen az alkalmazást a létrehozása mellett egyéb testreszabásokat. Ezek alaplemezképek önmagukban általában nyilvános felsőbb rétegbeli rendszerképen alapuló, például: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], vagy [Node.js][base-node]. Előfordulhat, hogy ossza meg az alkalmazás-lemezképek számos közös alapképet.

Egy operációs rendszer vagy alkalmazás keretrendszer-lemezkép frissítésekor a felsőbb rétegbeli karbantartó által, például egy kritikus fontosságú az operációs rendszer biztonsági javítás is frissítenie kell az alaplemezképek a kritikus fontosságú javítás szükséges. Minden egyes alkalmazás-lemezképet majd is újra kell építeni ezeket most már szerepel az alaprendszerképet a felsőbb rétegbeli javításokat tartalmazza.

ACR Builddel dinamikusan felderíti az alaplemezkép függőségeit, amikor azt összeállít egy tárolórendszerképet, mert azt képes észlelni, ha egy alkalmazás-lemezképet alaplemezkép frissül. Egy előre konfigurált [készítése feladatot](container-registry-tutorial-base-image-update.md#create-build-task), majd az ACR Builddel **automatikusan újraépíti az összes alkalmazás-lemezképet** az Ön számára. Ezt automatikus észlelése és újraépítése, az ACR Builddel menti a frissített alaplemezkép hivatkozó kép, az idő és munka, általában az manuálisan nyomon követésére, és minden alkalmazás frissítése szükséges.

Ismerje meg az operációs rendszer és a keretrendszer javítás ACR Builddel harmadik oktatóanyag [automatizálása rendszerképet az Azure Container Registry létrehozása épül rendszerkép alapszintű frissítésének](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> A kezdeti előzetes alaplemezkép frissítések eseményindító csak akkor, amikor az ugyanazon az Azure container registry vagy a nyilvánosan elérhető-e a Docker Hub-tárházak találhatók az alap- és az alkalmazás képeket hoz létre.

## <a name="next-steps"></a>További lépések

Amikor elkészült, az operációs rendszer és a felhőben a tárolólemezképek létrehozásával javítás keretrendszer automatizálása, tekintse meg háromrészes ACR Builddel oktatóanyag-sorozat.

> [!div class="nextstepaction"]
> [Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Build használatával](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
