---
title: Oktatóanyag – rendszerkép-Build kiváltása privát alaprendszerkép-frissítéssel
description: Ebben az oktatóanyagban egy Azure Container Registry feladat konfigurálásával automatikusan elindítja a tároló rendszerképét a felhőben, ha egy másik privát Azure Container registryben található alaprendszerkép frissül.
ms.topic: tutorial
ms.date: 01/22/2020
ms.openlocfilehash: e8aae8a91288d470c801dc4d82cfa6b44369d832
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617701"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Oktatóanyag: a tároló rendszerképének automatizálása, amikor egy alaprendszerkép frissül egy másik privát tároló beállításjegyzékében 

Az ACR-feladatok támogatják az automatizált lemezképek összeállítását a tároló [alaprendszerképének frissítésekor](container-registry-tasks-base-images.md), például az operációs rendszer vagy az alkalmazás keretrendszerének az egyik alaplemezképben való javításához. 

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy olyan ACR-feladatot, amely elindítja a Felhőbeli buildet, ha egy tároló alaprendszerképét egy másik Azure Container registrybe küldi a rendszer. Azt is megteheti, hogy egy olyan ACR-feladatot hoz létre, amely elindítja a rendszerképeket, amikor egy alaprendszerképet [ugyanarra az Azure Container registrybe](container-registry-tutorial-base-image-update.md)küldenek.

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Alaprendszerkép felépítése egy alapszintű beállításjegyzékbe
> * Alkalmazás-létrehozási feladat létrehozása egy másik beállításjegyzékben az alaprendszerkép nyomon követéséhez 
> * Az alapként szolgáló rendszerkép frissítése egy alkalmazásrendszerkép-feladat aktiválásához
> * Az aktivált feladat megjelenítése
> * A frissített alkalmazás-rendszerkép ellenőrzése

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyileg szeretné használni az Azure CLI-t, az Azure CLI **2.0.68** vagy újabb verzióját kell telepítenie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepítenie vagy frissítenie kell a CLI-t, tekintse meg az [Azure CLI telepítését][azure-cli]ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

### <a name="complete-the-previous-tutorials"></a>Az előző oktatóanyagok elvégzése

Ez az oktatóanyag feltételezi, hogy elvégezte a sorozat első két oktatóanyagának alábbi lépéseit:

* Azure Container Registry létrehozása
* Mintaadattár leágaztatása
* Mintaadattár klónozása
* Személyes hozzáférési jogkivonat létrehozása a GitHubban

Ha még nem tette meg, a folytatás előtt végezze el a következő oktatóanyagokat:

[Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Tasks használatával](container-registry-tutorial-quick-task.md)

[Tárolórendszerképek összeállításának automatizálása az Azure Container Registry Tasks használatával](container-registry-tutorial-build-task.md)

Az előző oktatóanyagokhoz létrehozott Container Registry mellett létre kell hoznia egy beállításjegyzéket az alaplemezképek tárolásához. Ha szeretné, hozza létre a második beállításjegyzéket egy másik helyen, mint az eredeti beállításjegyzékből.

### <a name="configure-the-environment"></a>A környezet konfigurálása

Láss el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem tölti fel ezeket a környezeti változókat, manuálisan kell lecserélnie az egyes értékeket, bárhol is megjelenjenek a példában szereplő parancsokban.

```azurecli-interactive
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Az alapként szolgáló rendszerkép frissítése forgatókönyv

Ez az oktatóanyag végigvezeti az alapként szolgáló rendszerkép frissítési forgatókönyvén. Ez a forgatókönyv egy olyan fejlesztési munkafolyamatot tükröz, amely egy közös, privát tároló-beállításjegyzékben kezeli az alaplemezképeket, amikor az alkalmazás lemezképeit más jegyzékekben hozza létre. Az alapképek olyan közös operációs rendszereket és keretrendszereket adhatnak meg, amelyeket egy csapat, vagy akár közös szolgáltatás-összetevők is használnak.

A saját beállításjegyzékben szereplő alkalmazási lemezképeket fejlesztő fejlesztők például hozzáférhetnek a Common Base registryben karbantartott alaplemezképek készletéhez. Az alapszintű beállításjegyzék lehet egy másik régióban vagy földrajzilag replikálva is.

A [kód minta][code-sample] két Dockerfiles tartalmaz: egy alkalmazás rendszerképét, valamint egy, az alapként megadott képet. A következő részekben olyan ACR-feladatot hoz létre, amely automatikusan elindítja az alkalmazás rendszerképének összeállítását, ha az alaprendszerkép új verziója egy másik Azure Container registrybe van leküldve.

* [Docker-app][dockerfile-app]: egy kis Node. js-alapú webalkalmazás, amely egy statikus weboldalt jelenít meg, amelyen a Node. js azon verziója látható, amelyen alapul. A verziósztring szimulált elem: az alapként szolgáló rendszerképben definiált környezeti változó (`NODE_VERSION`) tartalmát jeleníti meg.

* [Docker-Base][dockerfile-base]: az a rendszerkép, amelyet a `Dockerfile-app` az alapjaként határoz meg. Maga a [csomópont][base-node] -rendszerképen alapul, és tartalmazza a `NODE_VERSION` környezeti változót.

A következő szakaszokban létrehozunk egy feladatot, frissítjük a `NODE_VERSION` értékét az alapként szolgáló rendszerkép Docker-fájljában, majd az ACR Tasks használatával összeállítjuk az alapként szolgáló rendszerképet. Amikor az ACR-feladat leküldi az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az automatikusan kiváltja az alkalmazás-rendszerkép összeállítását. Ha kívánja, az alkalmazástároló-rendszerkép helyi futtatásával megtekintheti az összeállított rendszerképek eltérő verzió-sztringjeit.

Ebben az oktatóanyagban az ACR-feladat egy Docker megadott alkalmazás-tároló képet hoz létre és küld le. Az ACR-feladatok [több lépésből álló feladatokat](container-registry-tasks-multi-step.md)is futtathatnak, egy YAML-fájllal a több tároló létrehozásához, leküldéséhez és opcionális teszteléséhez szükséges lépéseket határozzák meg.

## <a name="build-the-base-image"></a>Az alapként szolgáló rendszerkép összeállítása

Először hozza létre az alapképet egy ACR-feladatok *gyors feladattal*, az [az ACR Build][az-acr-build]használatával. Amint azt a sorozat [első részében](container-registry-tutorial-quick-task.md) bemutattuk, ez a folyamat nem csupán összeállítja a rendszerképet, hanem siker esetén le is küldi azt a tárolóregisztrációs adatbázisba. Ebben a példában a rendszerképet az alaprendszerkép beállításjegyzékére küldi a rendszer.

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Feladat létrehozása a titkos alap rendszerképének nyomon követéséhez

Ezután hozzon létre egy feladatot az alkalmazás rendszerképének beállításjegyzékében az [az ACR Task Create][az-acr-task-create]paranccsal, amely [felügyelt identitást](container-registry-tasks-authentication-managed-identity.md)engedélyez. A felügyelt identitást a későbbi lépések során kell használni, hogy a feladat az alaprendszerkép beállításjegyzékével legyen hitelesítve. 

Ez a példa egy rendszerhez rendelt identitást használ, de bizonyos helyzetekben létrehozhat és engedélyezhet felhasználó által hozzárendelt felügyelt identitást. Részletekért lásd: a [beállításjegyzék közötti hitelesítés egy ACR-feladatban egy Azure által felügyelt identitás használatával](container-registry-tasks-cross-registry-authentication.md).

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```


Ez a feladat hasonló az [előző oktatóanyagban](container-registry-tutorial-build-task.md)létrehozott feladathoz. A feladat utasítja az ACR Tasksot egy rendszerkép-összeállítás aktiválására, amikor a rendszer leküld egy véglegesítést a `--context` által megadott adattárba. Míg az előző oktatóanyagban a rendszerkép létrehozásához használt Docker megadja a nyilvános alapképet (`FROM node:9-alpine`), a feladatban szereplő Docker, a [Docker-app][dockerfile-app], az alaprendszerkép beállításjegyzékének alaprendszerképét adja meg:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Ez a konfiguráció megkönnyíti a keretrendszer-javítások szimulálása az alaprendszerképben az oktatóanyag későbbi részében.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Identitás-lekérési engedélyek megadása az alap beállításjegyzékhez

Ha meg szeretné adni a feladat felügyelt identitásának engedélyeit a lemezképek alaprendszerkép-beállításjegyzékből való lekéréséhez, először futtassa az [ACR Task show][az-acr-task-show] parancsot az identitás egyszerű szolgáltatásnév-azonosítójának beszerzéséhez. Ezután futtassa az [ACR show][az-acr-show] parancsot az alap beállításjegyzék erőforrás-azonosítójának lekéréséhez:

```azurecli-interactive
# Get service principal ID of the task
principalID=$(az acr task show --name taskhelloworld --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Rendelje hozzá a felügyelt identitás-lekérési engedélyeket a beállításjegyzékhez az [az role hozzárendelés Create][az-role-assignment-create]:

```azurecli-interactive
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Cél beállításjegyzékbeli hitelesítő adatok hozzáadása a feladathoz

Futtassa az [ACR Task hitelesítőadat-Hozzáadás][az-acr-task-credential-add] parancsot a hitelesítő adatok a feladathoz való hozzáadásához. Adja át a `--use-identity [system]` paramétert annak jelzésére, hogy a feladat rendszer által hozzárendelt felügyelt identitása hozzáfér a hitelesítő adatokhoz.

```azurecli-interactive
az acr task credential add \
  --name taskhelloworld \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Használja az [az ACR Task Run parancsot][az-acr-task-run] a feladat manuális elindításához és az alkalmazás rendszerképének elkészítéséhez. Erre a lépésre azért van szükség, hogy a feladat nyomon követi az alkalmazás rendszerképének függőségét az alaprendszerképen.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Amint a feladat véget ért, jegyezze fel a **Run ID** (Futtatási azonosító) értékét (például „da6”), ha a következő nem kötelező lépést is végre szeretné hajtani.

### <a name="optional-run-application-container-locally"></a>Nem kötelező: Alkalmazástároló helyi futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, a tároló futtatásakor az alkalmazást megtekintheti egy webböngészőben, mielőtt újraépítené az alapként szolgáló rendszerképét. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja az `az acr login` és a `docker run` parancsot).

Először hitelesítse magát a Container registryben az [az ACR login][az-acr-login]használatával:

```azurecli
az acr login --name $ACR_NAME
```

Futtassa helyileg a tárolót a `docker run` paranccsal. A **\<run-id\>** helyére az előző lépés kimenetében található Futtatási azonosítót (például „da6”) írja. Ez a példa a tároló `myapp` nevet tartalmazza, és a `--rm` paraméterrel törli a tárolót a leállításkor.

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
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
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

### <a name="optional-run-newly-built-image"></a>Nem kötelező: Az újonnan összeállított rendszerkép futtatása

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

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt mutatta be, hogyan használhatók a feladatok a tárolórendszerképek összeállításának automatikus aktiválására a rendszerkép alapként szolgáló rendszerképének frissítése esetén. Most lépjen a következő oktatóanyagra, amelyből megtudhatja, hogyan indíthat el műveleteket egy meghatározott ütemterven.

> [!div class="nextstepaction"]
> [Feladat futtatása ütemterv szerint](container-registry-tasks-scheduled.md)

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
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
