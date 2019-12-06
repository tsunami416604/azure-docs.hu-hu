---
title: Az ACR-feladatok áttekintése
description: Az ACR-feladatok, a Azure Container Registry szolgáltatásainak egyik csomagja, amely biztonságos, automatizált tároló-rendszerképek készítését, kezelését és javítását teszi lehetővé a felhőben.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 96997f963f0bcb319d5318e2dd88a6e1e21fb36b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840765"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>A tárolók rendszerképének fejlesztése és karbantartása ACR-feladatokkal

A tárolók új virtualizációs szinteket biztosítanak, az alkalmazások és a fejlesztői függőségek elkülönítését az infrastruktúra és az üzemeltetési követelmények alapján. A továbbra is szükség van arra, hogy az alkalmazás virtualizálása hogyan legyen kezelve, és hogyan történik a tároló életciklusa.

## <a name="what-is-acr-tasks"></a>Mi az az ACR-feladat?

Az **ACR-feladatok** a Azure Container Registryon belüli szolgáltatások egyik csomagja. Felhőalapú tároló-rendszerképeket kínál a platformok, például a Linux, a Windows és az ARM [platformokhoz](#image-platforms) , és automatizálja az [operációs rendszer és a keretrendszer javítását](#automate-os-and-framework-patching) a Docker-tárolók számára. Az ACR-feladatok nem csupán a "belső hurkos" fejlesztési ciklust bővítik a felhőre az igény szerinti tárolók rendszerképének létrehozásával, hanem lehetővé teszik a forráskód frissítései által aktivált automatizált buildek, a tárolók alaprendszerképének vagy időzítőinak frissítését is. Például az alaprendszerkép-frissítési eseményindítók segítségével automatizálhatja az operációs rendszer és az alkalmazás-keretrendszer javítási munkafolyamatát, és megőrizheti a biztonságos környezeteket, miközben betartja a nem változtatható tárolók alapelveit.

## <a name="task-scenarios"></a>Feladatok forgatókönyvei

Az ACR-feladatok több forgatókönyvet is támogatnak a tároló-lemezképek és egyéb összetevők kiépítéséhez és karbantartásához. A részletekért tekintse meg a cikk következő részeit.

* **[Gyors feladat](#quick-task)** – egyetlen tároló-rendszerkép létrehozása és leküldése igény szerint egy tároló-beállításjegyzékbe az Azure-ban anélkül, hogy helyi Docker-motort kellene telepítenie. Gondolja át, `docker build`, `docker push` a felhőben.
* **Automatikusan aktivált feladatok** – egy vagy több *eseményindító* létrehozása rendszerkép létrehozásához:
  * **[A forráskód frissítésének indítása](#trigger-task-on-source-code-update)** 
  * **[Aktiválás az alapszintű rendszerkép frissítése során](#automate-os-and-framework-patching)** 
  * **[Indítás ütemezett](#schedule-a-task)** 
* **[Többlépéses feladat](#multi-step-tasks)** – a több lépésből álló, többtárolós alapú munkafolyamatokkal kiterjesztheti az ACR-feladatok egyetlen Rendszerképbeli felépítését és leküldési képességeit. 

Minden ACR-feladathoz tartozik egy társított [forráskód-környezet](#context-locations) – egy tároló-rendszerkép vagy más összetevő létrehozásához használt forrásfájlok helye. Például a környezetek git-tárházat vagy helyi fájlrendszert tartalmaznak.

A feladatok a [futtatási változók](container-registry-tasks-reference-yaml.md#run-variables)előnyeit is kihasználhatják, így felhasználhatja a feladatokhoz és az összetevőkhöz tartozó feladatokhoz tartozó és szabványosított címkéket.

## <a name="quick-task"></a>Gyors feladat

A belső hurok fejlesztési ciklusa, az alkalmazás kódjának írásához, kiépítéséhez és teszteléséhez szükséges iterációs folyamat valóban a tároló életciklus-felügyeletének kezdete.

Mielőtt elvégezte a kód első sorát, az ACR-feladatok [gyors feladatával](container-registry-tutorial-quick-task.md) integrált fejlesztési élmény biztosítható a tároló rendszerképének az Azure-ba való kiszervezésével. A gyors feladatokkal ellenőrizheti az automatizált Build-definíciókat, és elvégezheti a lehetséges problémákat a kód véglegesítése előtt.

Az ismerős `docker build` formátum használatakor az az [ACR Build][az-acr-build] parancs az Azure CLI-ben igénybe veszi a [környezetet](#context-locations) (a felépített fájlok készletét), ELküldi az ACR-feladatokat, és alapértelmezés szerint elküldi a létrehozott rendszerképet a beállításjegyzékbe a befejezés után.

Bevezetésért tekintse meg a Azure Container Registryban található [tároló rendszerképének elkészítéséhez és futtatásához](container-registry-quickstart-task-cli.md) szükséges rövid útmutatót.  

Az ACR-feladatok tároló-életciklus primitívek. Például integrálhatja az ACR-feladatokat a CI/CD-megoldásba. Ha az [az login][az-login] [szolgáltatást egy egyszerű szolgáltatással][az-login-service-principal]hajtja végre, akkor a CI/CD-megoldás ezután kiadhatja [az ACR Build][az-acr-build] -parancsokat a rendszerkép-buildek elindításához.

Megtudhatja, hogyan használhatja a gyors feladatokat az első ACR-feladatok oktatóanyagban, [Azure Container Registry feladatokkal felépítheti a Felhőbeli tároló lemezképeit](container-registry-tutorial-quick-task.md).

> [!TIP]
> Ha közvetlenül a forráskódból szeretne létrehozni és leküldeni egy képet, Docker nélkül, Azure Container Registry biztosítja az az [ACR Pack Build][az-acr-pack-build] parancsot (előzetes verzió). Ez az eszköz létrehoz egy rendszerképet, és leküldi az alkalmazás forráskódját a [felhőalapú natív Buildpacks](https://buildpacks.io/)használatával.

## <a name="trigger-task-on-source-code-update"></a>Feladat elindítása a forráskód frissítésekor

Hozzon létre egy tároló-rendszerkép Build vagy többlépéses feladatot a kód véglegesítése, vagy egy lekéréses kérelem vagy frissítés után egy nyilvános vagy privát git-tárházban a GitHubon vagy az Azure DevOps. Például konfiguráljon egy Build feladatot az Azure CLI-parancs az [ACR Task Create][az-acr-task-create] paranccsal egy git-tárház és opcionálisan egy ág-és Docker megadásával. Amikor a csapat frissíti a tárházban lévő kódot, az ACR-feladatok által létrehozott webhook elindítja a tárházban definiált tároló-rendszerkép összeállítását. 

Az ACR-feladatok a következő eseményindítókat támogatják, amikor a git-tárházat a feladat kontextusa szerint állítja be:

| Eseményindító | Alapértelmezés szerint engedélyezett |
| ------- | ------------------ |
| Véglegesítés | Igen |
| Lekéréses kérelem | Nem |

A forráskód-frissítési trigger konfigurálásához meg kell adnia a feladatot egy személyes hozzáférési jogkivonat (PAT) számára, hogy a webhookot a nyilvános vagy privát GitHub vagy az Azure DevOps-tárházban állítsa be.

> [!NOTE]
> Az ACR-feladatok jelenleg nem támogatják a commit vagy a pull kérelem eseményindítóit a GitHub Enterprise reposban.

Ismerje meg, hogyan indíthat triggert forráskód-végrehajtásra a második ACR-feladatok oktatóanyagban, és [automatizálja a tárolók rendszerképét Azure Container Registry feladatokkal](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Az operációs rendszer és a keretrendszer javításának automatizálása

Az ACR-feladatok hatékonyságának növelése a tároló-Build munkafolyamattal való hatékony feladatokból származik, és képes az alaprendszerkép frissítésének észlelésére. Ha a frissített alaplemezképet leküldi a beállításjegyzékbe, vagy egy alaprendszerkép frissül egy nyilvános tárházban (például a Docker hub-ban), az ACR-feladatok automatikusan létrehoznak egy alkalmazás-lemezképet az alapján.

A tárolók képei széles körben kategorizálva *lehetnek az* alapképekbe és az *alkalmazás* -lemezképbe. Az alaplemezképek jellemzően tartalmazzák az operációs rendszer és az alkalmazás által felépített keretrendszereket, valamint más testreszabásokat is. Ezek az alaplemezképek általában nyilvános upstream lemezképeken alapulnak, például: [Alpine Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]vagy [Node. js][base-node]. Az alkalmazási lemezképek közül több is megoszthat egy közös alapképet.

Ha a felsőbb rétegbeli karbantartó az operációs rendszer vagy az alkalmazás-keretrendszer lemezképét frissíti, például kritikus operációsrendszer-biztonsági javítással, akkor az alaplemezképeket is frissítenie kell, hogy tartalmazza a kritikus javítást. Ezután az egyes alkalmazás-rendszerképeket is újra kell építeni, hogy az tartalmazza az alaprendszerkép részét képező felsőbb rétegbeli javításokat.

Mivel az ACR-feladatok dinamikusan felderítik az alapszintű képfüggőségeket a tárolók rendszerképének létrehozásakor, az képes érzékelni az alkalmazás rendszerképe alaprendszerképének frissítésekor. Egy előre konfigurált [Build feladattal](container-registry-tutorial-base-image-update.md#create-a-task)az ACR-feladatok **automatikusan újraépítik az összes alkalmazás rendszerképét** . Ezzel az Automatikus észleléssel és újjáépítéssel az ACR-feladatok elmentik azt az időt és fáradságot, amelyet a rendszer általában a frissített alaprendszerképre hivatkozó minden egyes rendszerkép manuális nyomon követéséhez és frissítéséhez szükséges.

A Docker származó rendszerkép-buildek esetében az ACR-feladatok nyomon követik az alaprendszerkép frissítését, ha az alaprendszerkép a következő helyekre esik:

* Ugyanaz az Azure Container Registry, ahol a feladat fut
* Egy másik Azure Container Registry ugyanabban a régióban 
* Nyilvános tárház a Docker hub-ban
* Nyilvános tárház a Microsoft Container Registryban

> [!NOTE]
> * Az alaprendszerkép-frissítési trigger alapértelmezés szerint engedélyezve van egy ACR-feladatban. 
> * Jelenleg az ACR-feladatok csak az alkalmazás (*Runtime*) rendszerképein lévő alaprendszerkép-frissítéseket figyelik. Az ACR-feladatok nem követik nyomon a többfázisú Dockerfiles használt közbenső (*buildtime*) lemezképek alaprendszerkép-frissítéseit. 

További információ az operációs rendszerről és a keretrendszer javításáról: a harmadik ACR-feladatok oktatóanyaga, az [alapszintű rendszerképek frissítése Azure Container Registry feladatokkal](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Feladat ütemezése

A feladat létrehozásakor vagy frissítésekor egy vagy több *időzítő eseményindító* beállításával is ütemezhet egy feladatot. A feladatok ütemezése hasznos lehet a tároló számítási feladatainak meghatározott ütemezés szerinti futtatásához, illetve karbantartási műveletek vagy tesztek futtatására a beállításjegyzékbe rendszeresen leküldve. Részletekért lásd: [ACR-feladat futtatása meghatározott ütemterven](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Többlépéses feladatok

A többlépéses tevékenységek a Felhőbeli tároló-lemezképek létrehozásához, teszteléséhez és javításához szükséges lépéseken alapuló feladatok meghatározását és végrehajtását teszik lehetővé. A [YAML-fájlokban](container-registry-tasks-reference-yaml.md) meghatározott feladatok lépései egyedi Build-és leküldéses műveleteket határoznak meg a tároló lemezképei vagy más összetevői számára. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

Létrehozhat például egy több lépésből álló feladatot, amely automatizálja a következőket:

1. Webalkalmazás-rendszerkép összeállítása
1. A webalkalmazás-tároló futtatása
1. Webalkalmazás-tesztelési rendszerkép összeállítása
1. Futtassa a webalkalmazás-teszt tárolót, amely teszteket hajt végre a futó alkalmazás tárolóján.
1. Ha a tesztek sikeresek, hozzon létre egy Helm diagram archiválási csomagot
1. `helm upgrade` végrehajtása az új Helm chart Archive csomag használatával

A többlépéses feladatok lehetővé teszik a rendszerképek kiépítése, futtatása és tesztelése több megkomponálható lépésre, a lépésenkénti függőségek támogatásával. Az ACR-feladatok több lépésből álló feladatai esetében részletesebben szabályozható a rendszerképek kiépítése, tesztelése, valamint az operációs rendszer és a keretrendszer javítási folyamatai.

Ismerje meg a többlépéses feladatokat a [több lépésből álló Build-, tesztelési és javítási feladatok futtatása az ACR-feladatokban](container-registry-tasks-multi-step.md)című témakörben.

## <a name="context-locations"></a>Környezet helyei

Az alábbi táblázat néhány példát mutat be az ACR-feladatok támogatott környezeti helyeire:

| Környezet helye | Leírás | Példa |
| ---------------- | ----------- | ------- |
| Helyi fájlrendszer | A helyi fájlrendszer egyik könyvtárában található fájlok. | `/home/user/projects/myapp` |
| GitHub-főkiszolgáló ág | Egy nyilvános vagy privát GitHub-tárház főkiszolgálójának (vagy más alapértelmezett) ágában lévő fájljai.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-ág | Nyilvános vagy privát GitHub-tárház adott ága.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-almappa | Egy nyilvános vagy privát GitHub-tárház almappájába tartozó fájlok. Példa egy ág és almappa specifikációjának kombinációját mutatja be. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure DevOps almappa | Egy nyilvános vagy privát Azure-tárház almappájába tartozó fájlok. Példa az ág és az almappa specifikációjának kombinációját mutatja be. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Távoli Fez | Tömörített archívumban található fájlok egy távoli webkiszolgálón. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Ha privát git-tárházat használ egy tevékenység környezetében, meg kell adnia egy személyes hozzáférési jogkivonatot (PAT).

## <a name="image-platforms"></a>Képplatformok

Alapértelmezés szerint az ACR-feladatok lemezképeket készítenek a Linux operációs rendszerhez és az AMD64 architektúrához. A `--platform` címke megadásával Windows-lemezképeket vagy Linux-rendszerképeket hozhat létre más architektúrák számára. Megadhatja az operációs rendszer és a választható architektúrát az operációs rendszer/architektúra formátumában (például `--platform Linux/arm`). ARM-architektúrák esetén az operációs rendszer/architektúra/Variant formátumban is megadhat egy változatot (például `--platform Linux/arm64/v8`):

| Operációs rendszer | Architektúra|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>Feladatütemezés megtekintése

Minden feladat futtatásakor a rendszer naplózza a napló kimenetét, amely alapján megállapíthatja, hogy a feladat lépései sikeresen futottak-e. Ha az az [ACR Build](/cli/azure/acr#az-acr-build), [az ACR Run](/cli/azure/acr#az-acr-run)vagy [az ACR Task Run](/cli/azure/acr/task#az-acr-task-run) parancsot használja a feladat elindításához, a rendszer továbbítja a feladat futtatásának naplózási kimenetét a konzolra, és a későbbi lekéréshez is tárolja. Ha egy feladat automatikusan aktiválódik, például a forráskód-végrehajtás vagy egy alaprendszerkép frissítése esetén, a rendszer csak a feladat naplóit tárolja. Tekintse meg a Azure Portalben futtatott feladatok naplóit, vagy használja az az [ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) parancsot.

Alapértelmezés szerint a rendszer a beállításjegyzékben futtatott feladatok adatait és naplóit 30 napig őrzi meg, majd automatikusan kiüríti. Ha archiválni szeretné a feladatok futtatásához szükséges adatok archiválását, engedélyezze az az [ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) paranccsal történő archiválást. A következő példa engedélyezi az archiválást a feladat futtatási *CF11* a beállításjegyzék *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Következő lépések

Ha készen áll a tárolók rendszerképének és a Felhőbeli karbantartásának automatizálására, tekintse meg az [ACR feladatok oktatóanyag-sorozatát](container-registry-tutorial-quick-task.md).

Telepítse a [Docker-bővítményt a Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) -hoz, és az [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítményét az Azure-beli tároló-beállításjegyzékkel való együttműködéshez. Lemezképek lekérése és leküldése egy Azure Container registrybe, vagy az ACR-feladatok futtatása a Visual Studio Code-ban.

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
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
