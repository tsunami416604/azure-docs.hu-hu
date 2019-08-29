---
title: A tárolók rendszerképeinek kiépítése és javítása Azure Container Registry feladatokkal (ACR-feladatok)
description: Az ACR-feladatok, a Azure Container Registry szolgáltatásainak egyik csomagja, amely biztonságos, automatizált tároló-rendszerképek készítését, kezelését és javítását teszi lehetővé a felhőben.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 1459b6fc45bb3d875b4869d1dcb4302dec21eb96
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114805"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>A tárolók rendszerképének fejlesztése és karbantartása ACR-feladatokkal

A tárolók új virtualizációs szinteket biztosítanak, az alkalmazások és a fejlesztői függőségek elkülönítését az infrastruktúra és az üzemeltetési követelmények alapján. A továbbra is szükség van arra, hogy az alkalmazás virtualizálása hogyan legyen kezelve, és hogyan történik a tároló életciklusa.

## <a name="what-is-acr-tasks"></a>Mi az az ACR-feladat?

Az **ACR-feladatok** a Azure Container Registryon belüli szolgáltatások egyik csomagja. A szolgáltatás felhőalapú, a Linux, a Windows és az ARM rendszerekhez készült rendszerkép-összeállítást nyújt, és automatizálja az [operációs rendszert és a keretrendszer javítását](#automate-os-and-framework-patching) a Docker-tárolók számára. Az ACR-feladatok nem csupán a "belső hurkos" fejlesztési ciklust bővítik a felhőre az igény szerinti tárolók rendszerképének összeállításával, de lehetővé teszi az automatizált buildek forráskód-végrehajtását, illetve a tároló alaprendszerképének frissítését is. Az alaprendszerkép-frissítési eseményindítók segítségével automatizálhatja az operációs rendszer és az alkalmazás-keretrendszer javítási munkafolyamatát, megőrizheti a biztonságos környezeteket, miközben betartja a megváltoztathatatlan tárolók rendszerbiztonsági feladatait.

Hozzon létre és tesztelje a tároló lemezképeit ACR-feladatokkal négy módon:

* [Gyors feladat](#quick-task): Igény szerint hozhat létre és küldhet le tároló-lemezképeket az Azure-ban anélkül, hogy helyi Docker-motort kellene telepítenie. Szerintem `docker build`afelhőben. `docker push` Kiépítés helyi forráskódból vagy git-tárházból.
* [Build a forráskód](#automatic-build-on-source-code-commit)-végrehajtáshoz: A tároló rendszerképének kiváltása automatikusan történik, ha a kód elkötelezett a git-tárházban.
* [Build az alapszintű rendszerkép frissítéséhez](#automate-os-and-framework-patching): A rendszerkép alaprendszerképének frissítésekor aktiválja a tároló rendszerképének összeállítását.
* [Több lépésből álló feladatok](#multi-step-tasks): Olyan többlépéses feladatokat határozhat meg, amelyek képeket készítenek, a tárolókat parancsként futtatják, és leküldik a lemezképeket egy beállításjegyzékbe. Az ACR-feladatok ezen funkciója támogatja az igény szerinti feladat-végrehajtást és a párhuzamos rendszerképek létrehozására, tesztelésére és leküldésére vonatkozó műveleteket.

## <a name="quick-task"></a>Gyors feladat

A belső hurok fejlesztési ciklusa, az alkalmazás kódjának írásához, kiépítéséhez és teszteléséhez szükséges iterációs folyamat valóban a tároló életciklus-felügyeletének kezdete.

Mielőtt elvégezte a kód első sorát, az ACR-feladatok [gyors](container-registry-tutorial-quick-task.md) feladatával integrált fejlesztési élmény biztosítható a tároló rendszerképének az Azure-ba való kiszervezésével. A gyors feladatokkal ellenőrizheti az automatizált Build-definíciókat, és elvégezheti a lehetséges problémákat a kód véglegesítése előtt.

A jól ismert `docker build` formátum használatával az Azure CLI-ben az az [ACR Build][az-acr-build] parancs egy *kontextust* (a felépített fájlok készletét), az ACR-feladatokat küldi el, és alapértelmezés szerint a betöltés után leküldi a létrehozott rendszerképet a beállításjegyzékbe.

Bevezetésért tekintse meg a Azure Container Registryban található [tároló rendszerképének elkészítéséhez és futtatásához](container-registry-quickstart-task-cli.md) szükséges rövid útmutatót.  

Az alábbi táblázat néhány példát mutat be az ACR-feladatok támogatott környezeti helyeire:

| Környezet helye | Leírás | Példa |
| ---------------- | ----------- | ------- |
| Helyi fájlrendszer | A helyi fájlrendszer egyik könyvtárában található fájlok. | `/home/user/projects/myapp` |
| GitHub-főkiszolgáló ág | A GitHub-tárház fő (vagy más alapértelmezett) ágában található fájlok.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-ág | Egy GitHub-tárház adott ága.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-almappa | Egy GitHub-tárházban lévő almappában található fájlok. Példa egy ág és almappa specifikációjának kombinációját mutatja be. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Távoli Fez | Tömörített archívumban található fájlok egy távoli webkiszolgálón. | `http://remoteserver/myapp.tar.gz` |

Az ACR-feladatok tároló-életciklus primitívek. Például integrálhatja az ACR-feladatokat a CI/CD-megoldásba. Ha az [az login][az-login] [szolgáltatást egy egyszerű szolgáltatással][az-login-service-principal]hajtja végre, akkor a CI/CD-megoldás ezután kiadhatja [az ACR Build][az-acr-build] -parancsokat a rendszerkép-buildek elindításához.

Megtudhatja, hogyan használhatja a gyors feladatokat az első ACR-feladatok oktatóanyagban, Azure Container Registry feladatokkal felépítheti [a Felhőbeli tároló lemezképeit](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatikus Build a forráskód-végrehajtáshoz

Az ACR-feladatok használatával automatikusan elindíthatja a tárolók rendszerképének összeállítását, ha a kód elkötelezett a GitHubon vagy az Azure DevOps található git-tárházban. Felépítési feladatok, az Azure CLI-paranccsal konfigurálható az [ACR Task][az-acr-task], lehetővé teszi a git-tárház és opcionálisan egy ág-és Docker megadását. Amikor a csapata kódot hoz létre az adattárhoz, az ACR-feladatok által létrehozott webhook létrehozza a tárházban definiált tároló rendszerképét.

> [!IMPORTANT]
> Ha az előzetes `az acr build-task` verzióban korábban létrehozott feladatokat a paranccsal, ezeket a feladatokat az az [ACR Task][az-acr-task] paranccsal újra létre kell hozni.

Ismerje meg, hogyan indíthat triggert forráskód-végrehajtásra a második ACR-feladatok oktatóanyagban, és automatizálja a [tárolók rendszerképét Azure Container Registry](container-registry-tutorial-build-task.md)feladatokkal.

## <a name="automate-os-and-framework-patching"></a>Az operációs rendszer és a keretrendszer javításának automatizálása

Az ACR-feladatok hatékonyságának növelése a tároló-Build munkafolyamattal való hatékony feladatokból származik, és képes az alaprendszerkép frissítésének észlelésére. Ha a frissített alaplemezképet leküldi a beállításjegyzékbe, vagy egy alaprendszerkép frissül egy nyilvános tárházban (például a Docker hub-ban), az ACR-feladatok automatikusan létrehoznak egy alkalmazás-lemezképet az alapján.

A tárolók képei széles körben kategorizálva lehetnek az alapképekbe és az *alkalmazás* -lemezképbe. Az alaplemezképek jellemzően tartalmazzák az operációs rendszer és az alkalmazás által felépített keretrendszereket, valamint más testreszabásokat is. Ezek az alaplemezképek jellemzően nyilvános upstream lemezképeken alapulnak, például: [Alpine Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]vagy [Node. js][base-node]. Az alkalmazási lemezképek közül több is megoszthat egy közös alapképet.

Ha a felsőbb rétegbeli karbantartó az operációs rendszer vagy az alkalmazás-keretrendszer lemezképét frissíti, például kritikus operációsrendszer-biztonsági javítással, akkor az alaplemezképeket is frissítenie kell, hogy tartalmazza a kritikus javítást. Ezután az egyes alkalmazás-rendszerképeket is újra kell építeni, hogy az tartalmazza az alaprendszerkép részét képező felsőbb rétegbeli javításokat.

Mivel az ACR-feladatok dinamikusan felderítik az alapszintű képfüggőségeket a tárolók rendszerképének létrehozásakor, az képes érzékelni az alkalmazás rendszerképe alaprendszerképének frissítésekor. Egy előre konfigurált [Build](container-registry-tutorial-base-image-update.md#create-a-task)feladattal az ACR-feladatok **automatikusan újraépítik az összes alkalmazás rendszerképét** . Ezzel az Automatikus észleléssel és újjáépítéssel az ACR-feladatok elmentik azt az időt és fáradságot, amelyet a rendszer általában a frissített alaprendszerképre hivatkozó minden egyes rendszerkép manuális nyomon követéséhez és frissítéséhez szükséges.

Az ACR-feladatok nyomon követik az alaprendszerkép frissítését, ha az alaprendszerkép a következő helyekre esik:

* Ugyanaz az Azure Container Registry, ahol a feladat fut
* Egy másik Azure Container Registry ugyanabban a régióban 
* Nyilvános tárház a Docker hub-ban
* Nyilvános tárház a Microsoft Container Registryban

További információ az operációs rendszerről és a keretrendszer javításáról: a harmadik ACR-feladatok oktatóanyaga, az alapszintű rendszerképek [frissítése Azure Container Registry](container-registry-tutorial-base-image-update.md)feladatokkal.

## <a name="multi-step-tasks"></a>Többlépéses feladatok

A többlépéses tevékenységek a Felhőbeli tároló-lemezképek létrehozásához, teszteléséhez és javításához szükséges lépéseken alapuló feladatok meghatározását és végrehajtását teszik lehetővé. A feladatlépések tárolólemezképek különálló buildelési és leküldéses műveleteit határozzák meg. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

Létrehozhat például egy több lépésből álló feladatot, amely automatizálja a következőket:

1. Webalkalmazás-rendszerkép összeállítása
1. A webalkalmazás-tároló futtatása
1. Webalkalmazás-tesztelési rendszerkép összeállítása
1. Futtassa a webalkalmazás-teszt tárolót, amely teszteket hajt végre a futó alkalmazás tárolóján
1. Ha a tesztek sikeresek, hozzon létre egy Helm diagram archiválási csomagot
1. Az új Helm chart Archive csomag használata `helm upgrade`

A többlépéses feladatok lehetővé teszik a rendszerképek kiépítése, futtatása és tesztelése több megkomponálható lépésre, a lépésenkénti függőségek támogatásával. Az ACR-feladatok több lépésből álló feladatai esetében részletesebben szabályozható a rendszerképek kiépítése, tesztelése, valamint az operációs rendszer és a keretrendszer javítási folyamatai.

Ismerje meg a többlépéses feladatokat a [több lépésből álló Build-, tesztelési és javítási feladatok futtatása az ACR-](container-registry-tasks-multi-step.md)feladatokban című témakörben.

## <a name="view-task-logs"></a>Feladatütemezés megtekintése

Minden feladat futtatásakor a rendszer naplózza a napló kimenetét, amely alapján megállapíthatja, hogy a feladat lépései sikeresen futottak-e. Ha az az [ACR Build](/cli/azure/acr#az-acr-build), [az ACR Run](/cli/azure/acr#az-acr-run)vagy [az ACR Task Run](/cli/azure/acr/task#az-acr-task-run) parancsot használja a feladat elindításához, a rendszer továbbítja a feladat futtatásának naplózási kimenetét a konzolra, és a későbbi lekéréshez is tárolja. Tekintse meg a Azure Portalben futtatott feladatok naplóit, vagy használja az az [ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) parancsot.

A 2019. júliustól kezdődően a feladatok adatait és naplóit a beállításjegyzékben a rendszer alapértelmezés szerint 30 napig megőrzi, majd automatikusan törli. Ha archiválni szeretné a feladatok futtatásához szükséges adatok archiválását, engedélyezze az az [ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) paranccsal történő archiválást. A következő példa engedélyezi az archiválást a feladat futtatási *CF11* a beállításjegyzék *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>További lépések

Ha készen áll az operációs rendszer és a keretrendszer javításának automatizálására a tároló lemezképének a felhőben való létrehozásával, tekintse meg a három részből álló [ACR-feladatok oktatóanyag-sorozatot](container-registry-tutorial-quick-task.md).

Telepítse a Docker-bővítményt a [Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) -hoz, és az [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítményét az Azure-beli tároló-beállításjegyzékkel való együttműködéshez. Lemezképek lekérése és leküldése egy Azure Container registrybe, vagy az ACR-feladatok futtatása a Visual Studio Code-ban.

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
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
