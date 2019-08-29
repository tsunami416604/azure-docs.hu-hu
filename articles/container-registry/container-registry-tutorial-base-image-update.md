---
title: Oktatóanyag – a tároló rendszerképének automatizálása az alapszintű rendszerkép frissítése – Azure Container Registry feladatok
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy Azure Container Registry feladatot úgy, hogy az alaprendszerkép frissítésekor automatikusan aktiválja a tároló rendszerképét a felhőben.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 23b0990be7f215d9cc443c5549ae38de86826d17
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114614"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Oktatóanyag: A tároló rendszerképének automatizálása, ha alaprendszerkép frissül egy Azure Container registryben 

Az ACR Tasks támogatja az összeállítások automatizált végrehajtását a tárolók alapként szolgáló rendszerképének frissítésekor, például ha az operációs rendszer vagy az alkalmazás-keretrendszer javítását telepíti az alapként szolgáló rendszerképek egyikében. Ez az oktatóanyag azt mutatja be, hogyan hozhat létre olyan feladatot az ACR Tasksban, amely összeállítást aktivál a felhőben a tárolók alapként szolgáló rendszerképeinek a regisztrációs adatbázisba való leküldésekor.

Az oktatóanyag-sorozat utolsó része az alábbi lépésekből áll:

> [!div class="checklist"]
> * Az alapként szolgáló rendszerkép összeállítása
> * Alkalmazásrendszerkép-összeállítási feladat létrehozása
> * Az alapként szolgáló rendszerkép frissítése egy alkalmazásrendszerkép-feladat aktiválásához
> * Az aktivált feladat megjelenítése
> * A frissített alkalmazás-rendszerkép ellenőrzése

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI-t helyben szeretné használni, az Azure CLI **2.0.46-os** vagy újabb verzióját kell telepítenie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepítenie vagy frissítenie kell a CLI-t, tekintse meg az [Azure CLI telepítését][azure-cli]ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

### <a name="complete-the-previous-tutorials"></a>Az előző oktatóanyagok elvégzése

Ez az oktatóanyag feltételezi, hogy elvégezte a sorozat első két oktatóanyagának alábbi lépéseit:

* Azure Container Registry létrehozása
* Mintaadattár leágaztatása
* Mintaadattár klónozása
* Személyes hozzáférési jogkivonat létrehozása a GitHubban

Ha még nem tette meg, végezze el az első két oktatóanyagot, mielőtt továbblépne:

[Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Tasks használatával](container-registry-tutorial-quick-task.md)

[Tárolórendszerképek összeállításának automatizálása az Azure Container Registry Tasks használatával](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>A környezet konfigurálása

Láss el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem látja el értékkel ezeket a környezeti változókat, manuálisan kell majd behelyettesítenie az egyes értékeket, amikor megjelennek a példaparancsokban.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Alapként szolgáló rendszerképek

A tárolórendszerképek többségét definiáló Docker-fájlok megadnak egy szülőrendszerképet, amely a rendszerképek alapjául szolgál – ezt gyakran *alapként szolgáló rendszerképnek* nevezik. Az alaplemezképek általában az operációs rendszert tartalmazzák, például az [alpesi Linux][base-alpine] vagy a [Windows Nano Server][base-windows], amelyeken a tároló rétegeinek további részei lesznek alkalmazva. Lehetnek olyan alkalmazás-keretrendszerek is, mint a [Node. js][base-node] vagy a [.net Core][base-dotnet].

### <a name="base-image-updates"></a>Az alapként szolgáló rendszerképek frissítése

A rendszerképek kezelői gyakran frissítik az alapként szolgáló rendszerképet, hogy új szolgáltatásokkal és javításokkal bővítsék a rendszerképben található operációs rendszert vagy keretrendszert. A biztonsági javítások jelentik az alapként szolgáló rendszerkép frissítésének egy másik gyakori okát.

Az alapként szolgáló rendszerképek frissítésekor újra össze kell állítania a rájuk épülő regisztrációs adatbázisokban található tárolórendszerképeket is, hogy tartalmazzák az új szolgáltatásokat és javításokat. Az ACR Tasks lehetővé teszi a rendszerképek automatikus összeállítását a tárolók alapként szolgáló rendszerképének frissítésekor.

### <a name="tasks-triggered-by-a-base-image-update"></a>Alapszintű rendszerkép frissítése által aktivált feladatok

* A Docker származó lemezképek esetében az ACR-feladat az alábbi helyekről észleli az alaplemezképek függőségeit:

  * Ugyanaz az Azure Container Registry, ahol a feladat fut
  * Egy másik Azure Container Registry ugyanabban a régióban 
  * Nyilvános tárház a Docker hub-ban 
  * Nyilvános tárház a Microsoft Container Registryban

   Ha az `FROM` utasításban megadott alaprendszerkép ezen a helyen található, az ACR-feladat egy hookot ad hozzá, amely biztosítja, hogy a rendszer az alapértékének frissítésekor bármikor újraépítse a rendszerképet.

* Az ACR-feladatok jelenleg csak az alkalmazás-(*futtatókörnyezet*-) lemezképek alaprendszerkép-frissítéseinek nyomon követésére használhatók. Nem követ nyomon a többfázisú Dockerfiles használt közbenső (*buildtime*) lemezképek alaprendszerkép-frissítéseit.  

* Amikor egy ACR-feladatot hoz létre az az [ACR Task Create][az-acr-task-create] paranccsal, alapértelmezés szerint a feladat alaprendszerkép-frissítéssel aktiválható. Vagyis a `base-image-trigger-enabled` tulajdonság értéke TRUE (igaz). Ha le szeretné tiltani ezt a viselkedést egy feladatban, frissítse a tulajdonságot hamis értékre. Futtassa például a következő az [ACR Task Update][az-acr-task-update] parancsot:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* Egy ACR-feladat engedélyezéséhez, amely meghatározza és nyomon követheti a tárolók rendszerképének függőségeit – amely tartalmazza az alaprendszerképét –, először **legalább egyszer**aktiválnia kell a feladatot. Például a feladat manuális elindításához használja az az [ACR Task Run][az-acr-task-run] parancsot.

* Az alaprendszerkép frissítésére vonatkozó feladat elindításához a kiindulási képnek *stabil* címkével kell rendelkeznie `node:9-alpine`, például:. Ez a címkézés jellemző olyan alaplemezképek esetében, amelyek az operációs rendszer és a keretrendszer javításával frissítve lettek egy legújabb stabil kiadásra. Ha az alaprendszerkép új verzió címkével frissül, nem indít el feladatot. A képcímkézéssel kapcsolatos további információkért tekintse meg az [ajánlott eljárásokat ismertető útmutatót](container-registry-image-tag-version.md). 

### <a name="base-image-update-scenario"></a>Az alapként szolgáló rendszerkép frissítése forgatókönyv

Ez az oktatóanyag végigvezeti az alapként szolgáló rendszerkép frissítési forgatókönyvén. A [kód minta][code-sample] két Dockerfiles tartalmaz: egy alkalmazás rendszerképét, valamint egy, az alapként megadott képet. A következő részekben olyan ACR-feladatot hoz létre, amely automatikusan elindítja az alkalmazás rendszerképének összeállítását, ha az alaprendszerkép új verziója ugyanarra a tároló-beállításjegyzékre van leküldve.

[Docker-alkalmazás][dockerfile-app]: Egy kis Node. js-alapú webalkalmazás, amely egy statikus weblapot jelenít meg, amelyen a Node. js azon verziója jelenik meg, amelyen alapul. A verziósztring szimulált elem: az alapként szolgáló rendszerképben definiált környezeti változó (`NODE_VERSION`) tartalmát jeleníti meg.

[Docker – alap][dockerfile-base]: Az a rendszerkép `Dockerfile-app` , amely az alapszintként van meghatározva. Maga a [csomópont][base-node] -rendszerképen alapul, és tartalmazza a `NODE_VERSION` környezeti változót.

A következő szakaszokban létrehozunk egy feladatot, frissítjük a `NODE_VERSION` értékét az alapként szolgáló rendszerkép Docker-fájljában, majd az ACR Tasks használatával összeállítjuk az alapként szolgáló rendszerképet. Amikor az ACR-feladat leküldi az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az automatikusan kiváltja az alkalmazás-rendszerkép összeállítását. Ha kívánja, az alkalmazástároló-rendszerkép helyi futtatásával megtekintheti az összeállított rendszerképek eltérő verzió-sztringjeit.

Ebben az oktatóanyagban az ACR-feladat egy Docker megadott alkalmazás-tároló képet hoz létre és küld le. Az ACR-feladatok [több lépésből álló feladatokat](container-registry-tasks-multi-step.md)is futtathatnak, egy YAML-fájllal a több tároló létrehozásához, leküldéséhez és opcionális teszteléséhez szükséges lépéseket határozzák meg.

## <a name="build-the-base-image"></a>Az alapként szolgáló rendszerkép összeállítása

Első lépésként állítsa össze az alapként szolgáló rendszerképet az ACR Tasks *gyors feladat* funkciójával. Amint azt a sorozat [első részében](container-registry-tutorial-quick-task.md) bemutattuk, ez a folyamat nem csupán összeállítja a rendszerképet, hanem siker esetén le is küldi azt a tárolóregisztrációs adatbázisba.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Feladat létrehozása

Ezután hozzon létre egy feladatot az [az ACR Task Create][az-acr-task-create]paranccsal:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Ha az előzetes `az acr build-task` verzióban korábban létrehozott feladatokat a paranccsal, ezeket a feladatokat az az [ACR Task][az-acr-task] paranccsal újra létre kell hozni.

Ez a feladat hasonlít az [előző oktatóanyagban](container-registry-tutorial-build-task.md) létrehozott gyors feladathoz. A feladat utasítja az ACR Tasksot egy rendszerkép-összeállítás aktiválására, amikor a rendszer leküld egy véglegesítést a `--context` által megadott adattárba. Míg az előző oktatóanyagban a rendszerkép létrehozásához használt Docker meghatározza a nyilvános alaprendszerképet (`FROM node:9-alpine`), a Docker ebben a feladatban, [Docker-app][dockerfile-app], egy alaprendszerképet ad meg ugyanabban a beállításjegyzékben:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Ez a konfiguráció megkönnyíti a keretrendszer-javítások szimulálása az alaprendszerképben az oktatóanyag későbbi részében.

## <a name="build-the-application-container"></a>Az alkalmazástároló összeállítása

Használja az [az ACR Task Run parancsot][az-acr-task-run] a feladat manuális elindításához és az alkalmazás rendszerképének elkészítéséhez. Ez a lépés biztosítja, hogy a feladat nyomon követi az alkalmazás rendszerképének függőségét az alaprendszerképen.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Amint a feladat véget ért, jegyezze fel a **Run ID** (Futtatási azonosító) értékét (például „da6”), ha a következő nem kötelező lépést is végre szeretné hajtani.

### <a name="optional-run-application-container-locally"></a>Nem kötelező: Alkalmazás-tároló helyi futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, a tároló futtatásakor az alkalmazást megtekintheti egy webböngészőben, mielőtt újraépítené az alapként szolgáló rendszerképét. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja az `az acr login` és a `docker run` parancsot).

Először hitelesítse magát a Container registryben az [az ACR login][az-acr-login]használatával:

```azurecli
az acr login --name $ACR_NAME
```

Futtassa helyileg a tárolót a `docker run` paranccsal. A **\<run-id\>** helyére az előző lépés kimenetében található Futtatási azonosítót (például „da6”) írja. Ez a példa a tárolót `myapp` nevezi el, és tartalmazza azt a `--rm` paramétert, amellyel el szeretné távolítani a tárolót, amikor leállítja.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Lépjen a `http://localhost:8080` címre a böngészőben, és a weboldalon a Node.js verziószámának kell megjelennie az alábbihoz hasonlóan. Egy későbbi lépésben majd újabbra frissíti a verziót úgy, hogy egy „a” tagot ad hozzá a verziósztringhez.

![A böngészőben megjelenített mintaalkalmazás képernyőképe][base-update-01]

A tároló leállításához és eltávolításához futtassa a következő parancsot:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Összeállítások listázása

Következő lépésként sorolja fel azt a feladatot, amely szerint az ACR-feladatok befejeződtek a beállításjegyzékben az az [ACR Task List-Run][az-acr-task-list-runs] parancs használatával:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Ha elvégezte az előző oktatóanyagot (és nem törölte a regisztrációs adatbázist), az alábbihoz hasonló kimenetnek kell megjelennie. Jegyezze fel a feladatfuttatások számát, valamint a RUN ID oszlop legújabb értékét, hogy összehasonlíthassa a kimenettel, miután frissítette az alapként szolgáló rendszerképet a következő szakaszban.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Az alapként szolgáló rendszerkép frissítése

Itt most egy keretrendszer-javítást szimulál az alapként szolgáló rendszerképben. Módosítsa a **Dockerfile-base** fájlt, és adja hozzá az „a” tagot a `NODE_VERSION` attribútumban meghatározott verziószám után:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Futtasson egy gyors feladatot az alapként szolgáló módosított rendszerkép összeállításához. Jegyezze fel a kimenetben található **Run ID** (Futtatási azonosító) értékét.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Miután befejeződött az összeállítás, és az ACR-feladat leküldte az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az aktiválja az alkalmazás-rendszerkép összeállítását. Eltarthat egy ideig, amíg a korábban létrehozott feladat aktiválja az alkalmazás-rendszerkép összeállítását, mivel észlelnie kell az újonnan összeállított és leküldött alapként szolgáló rendszerképet.

## <a name="list-updated-build"></a>Frissített összeállítás listázása

Az alapként szolgáló rendszerkép frissítését követően listázza újra a feladatfuttatásokat, hogy összehasonlíthassa azokat a korábbi listával. Ha a kimenet eleinte nem tér el a korábbitól, időnként futtassa újra a parancsot, amíg az új feladatfuttatás meg nem jelenik a listában.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A kimenet a következőkhöz hasonló. Az utolsóként végrehajtott összeállítás TRIGGER (Eseményindító) értékének az „Image Update” (Rendszerképfrissítés) értéknek kell lennie, ami jelzi, hogy a feladatot az alapként szolgáló rendszerkép gyors feladata aktiválta.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Ha szeretné végrehajtani a következő nem kötelező lépést az újonnan összeállított tároló futtatására és a frissített verziószám megtekintésére, jegyezze fel a rendszerképfrissítés által kiváltott összeállítás **RUN ID** (Futtatási azonosító) értékét (az előző kimenetben ez a „da8” érték).

### <a name="optional-run-newly-built-image"></a>Nem kötelező: Újonnan létrehozott rendszerkép futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, futtassa az új alkalmazás-rendszerképet, amint annak összeállítása befejeződött. A `<run-id>` helyére az előző lépésben beszerzett RUN ID (Futtatási azonosító) értéket írja. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja a `docker run` parancsot).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Lépjen a http://localhost:8081 címre a böngészőben, ahol a weboldalon a Node.js frissített verziószámának (az „a” taggal kiegészítve) kell megjelennie:

![A böngészőben megjelenített mintaalkalmazás képernyőképe][base-update-02]

Figyelje meg, hogy miután frissítette az **alapként szolgáló rendszerképet** egy új verziószámmal, az utoljára összeállított **alkalmazás-rendszerkép** az új verziót jeleníti meg. Az ACR Tasks észlelte az alapként szolgáló rendszerkép módosítását, és automatikusan újraépítette az alkalmazás-rendszerképet.

A tároló leállításához és eltávolításához futtassa a következő parancsot:

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag-sorozatban létrehozott összes erőforrás, így a tárolóregisztrációs adatbázis, a tárolópéldány, a kulcstároló és a szolgáltatásnév eltávolításához hajtsa végre az alábbi parancsokat:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogyan használhatók a feladatok a tárolórendszerképek összeállításának automatikus aktiválására a rendszerkép alapként szolgáló rendszerképének frissítése esetén. Most lépjen tovább a tárolóregisztrációs adatbázisban való hitelesítést ismertető témakörre.

> [!div class="nextstepaction"]
> [Hitelesítés az Azure Container Registryben](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
