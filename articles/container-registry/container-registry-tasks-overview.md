---
title: Az ACR-feladatok áttekintése
description: Bevezetés az ACR-feladatok, az Azure Container Registry szolgáltatáscsomagja, amely biztonságos, automatikus tárolólemezképek et biztosít, felügyeletet és javítást biztosít a felhőben.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087277"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>A tárolórendszerkép-összeállítások és karbantartás automatizálása az ACR-feladatokkal

A tárolók a virtualizáció új szintjeit biztosítják, elkülönítve az alkalmazások és a fejlesztői függőségeket az infrastruktúrától és az üzemeltetési követelményektől. Mi marad azonban annak szükségessége, hogy foglalkozzon azzal, hogy ezt az alkalmazás virtualizációját hogyan kezelik és javítják a tároló életciklusa során.

## <a name="what-is-acr-tasks"></a>Mi az ACR-feladatok?

**Az ACR-feladatok** az Azure Container Registry szolgáltatáscsomagja. Felhőalapú tárolórendszerkép-építést biztosít [a Linux,](#image-platforms) a Windows és az ARM platformokszámára, és automatizálhatja az operációs rendszert és a [keretrendszer-javítást](#automate-os-and-framework-patching) a Docker-tárolókhoz. Az ACR-feladatok nem csak kiterjesztik a "belső hurok" fejlesztési ciklusát a felhőre az igény szerinti tárolórendszerkép-buildekkel, hanem lehetővé teszik a forráskód-frissítések, a tároló alaplemezképének frissítései vagy időzítők által kiváltott automatikus buildeket is. Az alaprendszerkép-frissítési eseményindítókkal például automatizálhatja az operációs rendszer és az alkalmazáskeretrendszer javítási munkafolyamatát, fenntartva a biztonságos környezeteket, miközben betartja a nem módosítható tárolók alapelveit.

## <a name="task-scenarios"></a>Feladat-forgatókönyvek

Az ACR-feladatok számos forgatókönyvet támogat a tárolórendszerképek és egyéb összetevők létrehozásához és karbantartásához. A részleteket a cikk következő szakaszaiban találja.

* **[Gyors feladat](#quick-task)** – Egyetlen tárolórendszerképet hozhat létre és lökthet le egy tárolóbeállítás-beállításjegyzékbe igény szerint az Azure-ban anélkül, hogy helyi Docker-motor-telepítésre lenne szükség. Gondolkozz, `docker build` `docker push` a felhőben.
* **Automatikusan aktivált feladatok** – Egy vagy több *eseményindító engedélyezése* lemezkép létrehozásához:
  * **[Eseményindító a forráskód frissítésén](#trigger-task-on-source-code-update)** 
  * **[Eseményindító az alapkép frissítésén](#automate-os-and-framework-patching)** 
  * **[Eseményindító ütemezés szerint](#schedule-a-task)** 
* **[Többlépéses feladat](#multi-step-tasks)** – Az ACR-feladatok egyetlen lemezkép-buildelési és leküldéses képességének kiterjesztése többlépéses, többtárolós munkafolyamatokkal. 

Minden ACR-feladathoz tartozik egy [forráskód-környezet](#context-locations) – a tárolórendszerkép vagy más összetevő létrehozásához használt forrásfájlok készletének helye. Példa környezetek közé tartozik a Git-tárház vagy a helyi fájlrendszer.

A feladatok kihasználhatják a [futtatott változók előnyeit](container-registry-tasks-reference-yaml.md#run-variables)is, így újra felhasználhatja a feladatdefiníciókat, és szabványosíthatja a címkéket a képekhez és az összetevőkhöz.

## <a name="quick-task"></a>Gyors feladat

A belső hurok fejlesztési ciklus, az iteratív folyamat a kód írása, az alkalmazás létrehozása és tesztelése, mielőtt elkötelezné magát a forrás-ellenőrzés, valóban a tároló életciklus-kezelés kezdete.

Az első kódsor véglegesítése előtt az ACR-feladatok [gyorsfeladat-funkciója](container-registry-tutorial-quick-task.md) integrált fejlesztési élményt nyújthat a tárolórendszerkép-buildek Azure-ba való kiszervezésével. A gyors feladatok segítségével ellenőrizheti az automatikus builddefiníciókat, és a kód véglegesítése előtt felkaphatja a lehetséges problémákat.

A megszokott `docker build` formátumhasználatával az [az acr build][az-acr-build] parancs az Azure CLI vesz egy [környezetben](#context-locations) (a létrehozott fájlok készletét), elküldi az ACR feladatok, és alapértelmezés szerint leküldi a beépített lemezképet a rendszerleíró adatbázisba befejezését követően.

A bevezetés, tekintse meg a rövid útmutató [egy tároló rendszerkép az](container-registry-quickstart-task-cli.md) Azure Container Registry létrehozásához és futtatásához.  

Az ACR-feladatok tárolóéletciklus-primitívként vannak kialakítva. Integrálja például az ACR-feladatokat a CI/CD-megoldásba. Az [bejelentkezés][az-login] egyszerű [szolgáltatással][az-login-service-principal]történő végrehajtásával a CI/CD-megoldás ezután kiadhatja [az acr build][az-acr-build] parancsokat a lemezkép-buildek indításához.

Ismerje meg, hogyan használhatja a gyorsfeladatokat az első ACR-feladatok oktatóanyagában, [a Tárolórendszerképek létrehozása a felhőben az Azure Container Registry Tasks segítségével.](container-registry-tutorial-quick-task.md)

> [!TIP]
> Ha azt szeretné, hogy hozzon létre, és leküldéses egy lemezképet közvetlenül a forráskódból, dockerfile nélkül, az Azure Container Registry biztosítja az [az acr pack build][az-acr-pack-build] parancsot (előzetes verzió). Ez az eszköz a [Cloud Native Buildpacks](https://buildpacks.io/)használatával létrehoz és lelök egy lemezképet az alkalmazás forráskódjából.

## <a name="trigger-task-on-source-code-update"></a>Eseményindító feladat a forráskód frissítésén

Indítsa el a tárolórendszerkép-összeállítási vagy többlépéses feladatot, ha a kód véglegesítése, vagy egy lekéréses kérelem készül, vagy egy nyilvános vagy privát Git-tárház a GitHub vagy az Azure DevOps rendszerében. Konfiguráljon például egy buildelési feladatot az Azure CLI-parancs [az acr feladat létrehozásával][az-acr-task-create] egy Git-tárház és adott esetben egy ág és a Dockerfile megadásával. Amikor a csapat frissíti a kódot a tárházban, az ACR-feladatok által létrehozott webhook elindítja a tárházban definiált tárolórendszerkép buildje. 

Az ACR-feladatok a következő eseményindítókat támogatják, ha a Git-tárta a feladat környezeteként állít be:

| Eseményindító | Alapértelmezés szerint engedélyezett |
| ------- | ------------------ |
| Véglegesítés | Igen |
| Lekéréses kérelem | Nem |

A forráskód-frissítési eseményindító konfigurálásához meg kell adnia a feladatot egy személyes hozzáférési jogkivonat (PAT) a webhook beállításához a nyilvános vagy privát GitHub vagy az Azure DevOps-tárházban.

> [!NOTE]
> Jelenleg az ACR-feladatok nem támogatják a véglegesítési vagy lekéréses kérelmek eseményindítóit a GitHub Enterprise-repos-ban.

Ismerje meg, hogyan indíthatja el a forráskód véglegesítése a második ACR-feladatok oktatóanyag, [a Tárolórendszerkép-összeállítások automatizálása az Azure Container Registry Tasks segítségével.](container-registry-tutorial-build-task.md)

## <a name="automate-os-and-framework-patching"></a>Az operációs rendszer és a keretkép javításának automatizálása

Az ACR-feladatok ereje a tárolókészítési munkafolyamat valóban javítására abból a képességből származik, hogy képes észlelni egy *alaplemezkép frissítését.* A legtöbb tárolólemezkép egyik szolgáltatása, az alaplemezkép egy szülőlemezkép, amelyen egy vagy több alkalmazásrendszer-lemezkép alapul. Az alaplemezképek általában az operációs rendszert és néha az alkalmazáskeretrendszereket tartalmazzák. 

Beállíthat egy ACR-feladatot az alaplemezkép függőségének nyomon követésére, amikor alkalmazáslemezképet hoz létre. Amikor a frissített alaprendszerképet a rendszerleíró adatbázisba rendeli, vagy egy alaprendszerképet frissít egy nyilvános tárházban, például a Docker Hubban, az ACR-feladatok automatikusan létrehozhatnak bármilyen alkalmazáslemezképet az alapján.
Ezzel az automatikus észleléssel és újraépítéssel az ACR-feladatok időt és energiát takarítanak meg, amelyek általában szükségesek a frissített alaplemezképre hivatkozó minden egyes alkalmazáskép manuális nyomon követéséhez és frissítéséhez.

További információ az ACR-feladatok [alapképfrissítési eseményindítóiról.](container-registry-tasks-base-images.md) És megtudhatja, hogyan indíthat el egy lemezképet, ha egy alaprendszerkép egy tároló rendszerleíró adatbázisba kerül az oktatóanyagban, [amely automatikus tárolórendszerkép-összeállítások, amikor egy alaprendszerkép frissül egy Azure-tároló beállításjegyzékében](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Feladat ütemezése

Ha a feladat létrehozásakor vagy frissítésekor egy vagy több *időzítőesemény ütemezésével* ütemez egy feladatot. A feladat ütemezése akkor hasznos, ha a tárolószámítási feladatok egy meghatározott ütemezés szerint fut, vagy karbantartási műveletek vagy tesztek futtatása a rendszerkocsikat rendszeresen a rendszerleíró adatbázisba leadott képeken. További információt az [ACR-feladat futtatása meghatározott ütemezés sel.](container-registry-tasks-scheduled.md)

## <a name="multi-step-tasks"></a>Többlépéses feladatok

A többlépéses feladatok lépésalapú feladatdefiníciót és -végrehajtást biztosítanak a tárolóképek felhőbeli létrehozásához, teszteléséhez és javításához. A [YAML-fájlban](container-registry-tasks-reference-yaml.md) definiált feladatlépések egyedi buildelési és leküldéses műveleteket határoznak meg a tárolóképekhez vagy más összetevőkhöz. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

Létrehozhat például egy többlépéses feladatot, amely automatizálja a következőket:

1. Webalkalmazás-lemezkép létrehozása
1. A webalkalmazás-tároló futtatása
1. Webalkalmazás-tesztkép létrehozása
1. Futtassa a webalkalmazás-teszttárolót, amely teszteket hajt végre a futó alkalmazástárolón
1. Ha a tesztek megfelelnek, hozzon létre egy Helm chart archív csomagot
1. Az `helm upgrade` új Helm-diagram archívumcsomag használatával történő művelet

A többlépéses feladatok lehetővé teszik a lemezkép összeállítását, futtatását és tesztelését több futtatható lépésre, a függőségek közötti támogatással. Az ACR-feladatok többlépéses feladataival részletesebben szabályozhatja a lemezképek készítését, tesztelését, valamint az operációs rendszer és a keretrendszer javítási munkafolyamatait.

Ismerje meg a többlépéses feladatok futtatása [többlépéses buildelési, tesztelési és javítási feladatok futtatása az ACR-feladatokban](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Környezeti helyek

Az alábbi táblázat néhány példát mutat be az ACR-feladatok támogatott környezethelyeire:

| Környezet helye | Leírás | Példa |
| ---------------- | ----------- | ------- |
| Helyi fájlrendszer | A helyi fájlrendszer könyvtárában lévő fájlok. | `/home/user/projects/myapp` |
| GitHub főága | Fájlok egy nyilvános vagy privát GitHub-tárház fő-(vagy más alapértelmezett) ágán belül.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-ág | Egy nyilvános vagy privát GitHub-tárterület adott ága.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-almappa | Nyilvános vagy privát GitHub-tárházban lévő almappában lévő fájlok. Példa egy elágazás és az almappa specifikációjának kombinációjára. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub véglegesítése | Adott véglegesítés nyilvános vagy privát GitHub-tárházban. Példa a véglegesítési kivonat (SHA) és az almappa specifikációjának kombinációjára. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps-almappa | Nyilvános vagy privát Azure-tárházban lévő almappában lévő fájlok. A példa az elágazás és az almappa specifikációjának kombinációját mutatja be. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Távoli tarball | Fájlok tömörített archívumban egy távoli webkiszolgálón. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Ha egy feladat környezeteként egy privát Git-tárta használatával egy feladat, meg kell adnia egy személyes hozzáférési jogkivonatot (PAT).

## <a name="image-platforms"></a>Kép platformok

Alapértelmezés szerint az ACR-feladatok képeket hoz létre a Linux operációs rendszerhez és az amd64 architektúrához. Adja `--platform` meg a címkét a Windows-lemezképek vagy Linux-lemezképek más architektúrákhoz való létrehozásához. Adja meg az operációs rendszert és opcionálisan egy támogatott `--platform Linux/arm`architektúrát os/architektúra formátumban (például). ARM architektúrák esetén tetszés szerint adjon meg egy `--platform Linux/arm64/v8`változatot OS/architektúra/variáns formátumban (például):

| Operációs rendszer | Architektúra|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>kar64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

Minden feladatfuttatás naplókimenetet hoz létre, amelyből megállapíthatja, hogy a feladatlépések sikeresen futottak-e. Ha manuálisan indít el egy feladatot, a naplókimenet a feladatfuttatáshoz a konzolra kerül, és későbbi lekérésre is tárolja. Ha egy feladat automatikusan aktiválódik, például egy forráskód véglegesítése vagy egy alapkép frissítése, a feladatnaplók csak tárolásra kerülnek. Tekintse meg a futtatási naplók az Azure Portalon, vagy használja az [az acr feladatnaplók parancsot.](/cli/azure/acr/task#az-acr-task-logs)

További információk a [feladatnaplók megtekintéséről és kezeléséről.](container-registry-tasks-logs.md)

## <a name="next-steps"></a>További lépések

Ha készen áll a tárolórendszerkép-buildek és -karbantartás automatizálására a felhőben, tekintse meg az [ACR-feladatok oktatóanyag-sorozatot.](container-registry-tutorial-quick-task.md)

Igény szerint telepítheti a [Docker-bővítmény t a Visual Studio-kódhoz](https://code.visualstudio.com/docs/azure/docker) és az [Azure-fiókbővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) az Azure-tároló-beállításjegyzékekkel való együttműködésre. Lekérése és leküldéses lemezképek egy Azure-tároló beállításjegyzékbe, vagy futtassa az ACR-feladatok, mindezt a Visual Studio-kód.

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
