---
title: Oktatóanyag - A rendszer létrehozása a privát alapkép frissítésével
description: Ebben az oktatóanyagban konfigurálja az Azure Container Registry Task automatikusan elindítja a tárolórendszerkép-buildek a felhőben, amikor egy alaprendszerkép egy másik privát Azure-tároló beállításjegyzékben frissül.
ms.topic: tutorial
ms.date: 01/22/2020
ms.openlocfilehash: e8aae8a91288d470c801dc4d82cfa6b44369d832
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77617701"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Oktatóanyag: A tárolórendszerkép automatizálása, ha egy alaprendszerkép frissül egy másik privát tároló beállításjegyzékében 

Az ACR-feladatok automatikus lemezképet támogatnak, ha egy tároló [alaplemezképe frissül,](container-registry-tasks-base-images.md)például amikor az operációs rendszert vagy az alkalmazáskeretrendszert az egyik alaplemezképen javítja. 

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy ACR-feladatot, amely elindítja a felhőben, amikor egy tároló alaprendszerképének leküldése egy másik Azure-tároló beállításjegyzékbe. Megpróbálhat egy oktatóanyagot is, hogy hozzon létre egy ACR-feladatot, amely elindítja a rendszerkép létrehozását, amikor egy alaprendszerképet ugyanarra az [Azure-tároló beállításjegyzékébe](container-registry-tutorial-base-image-update.md)tol.

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Az alaprendszerkép létrehozása alapbeállítási jegyzékben
> * Alkalmazásbuild-feladat létrehozása egy másik beállításjegyzékben az alaplemezkép nyomon követéséhez 
> * Az alapként szolgáló rendszerkép frissítése egy alkalmazásrendszerkép-feladat aktiválásához
> * Az aktivált feladat megjelenítése
> * A frissített alkalmazás-rendszerkép ellenőrzése

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI-t helyileg szeretné használni, az Azure CLI **2.0.68-as** vagy újabb verzióját kell telepítenie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet, olvassa el [az Azure CLI telepítését][azure-cli] ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

### <a name="complete-the-previous-tutorials"></a>Az előző oktatóanyagok elvégzése

Ez az oktatóanyag feltételezi, hogy elvégezte a sorozat első két oktatóanyagának alábbi lépéseit:

* Azure Container Registry létrehozása
* Mintaadattár leágaztatása
* Mintaadattár klónozása
* Személyes hozzáférési jogkivonat létrehozása a GitHubban

Ha még nem tette meg, a folytatás előtt végezze el az alábbi útmutatókat:

[Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Tasks használatával](container-registry-tutorial-quick-task.md)

[Tárolórendszerképek összeállításának automatizálása az Azure Container Registry Tasks használatával](container-registry-tutorial-build-task.md)

Az előző oktatóanyagokhoz létrehozott tárolóbeállítás-jegyzék mellett létre kell hoznia egy rendszerleíró adatbázist az alaplemezképek tárolásához. Ha szeretné, hozza létre a második rendszerleíró adatbázist az eredeti től eltérő helyen.

### <a name="configure-the-environment"></a>A környezet konfigurálása

Láss el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem feltölti ezeket a környezeti változókat, manuálisan kell cserélnie az egyes értékeket, bárhol is jelenjen meg a példaparancsokban.

```azurecli-interactive
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Az alapként szolgáló rendszerkép frissítése forgatókönyv

Ez az oktatóanyag végigvezeti az alapként szolgáló rendszerkép frissítési forgatókönyvén. Ebben a forgatókönyvben tükrözi a fejlesztési munkafolyamat kezelésére alaplemezképek egy közös, privát tároló beállításjegyzékben alkalmazáslemezképek létrehozásakor más kibocsátásiegység-forgalmi jegyzékekben. Az alaplemezképek meghatározhatják a csoport által használt közös operációs rendszereket és keretrendszereket, vagy akár a közös szolgáltatásösszetevőket is.

Például azok a fejlesztők, akik alkalmazásképeket fejlesztenek saját beállításjegyzékükben, hozzáférhetnek a közös alapbeállítás-jegyzékben karbantartott alaplemezképek készletéhez. Az alap beállításjegyzék lehet egy másik régióban, vagy akár georeplikált.

A [kódminta][code-sample] két Docker-fájlt tartalmaz: egy alkalmazás-rendszerképet, és egy rendszerképet, amelyet az alkalmazás-rendszerkép az alapként szolgáló rendszerképeként ad meg. A következő szakaszokban hozzon létre egy ACR-feladatot, amely automatikusan elindítja az alkalmazásrendszer képének buildjét, amikor az alaprendszerkép új verziója egy másik Azure-tároló beállításjegyzékbe kerül.

* [Dockerfile-app][dockerfile-app]: Egy kisméretű Node.js-webalkalmazás, amely az alapjául szolgáló Node.js-verziót jelző statikus weboldalt jelenít meg. A verziósztring szimulált elem: az alapként szolgáló rendszerképben definiált környezeti változó (`NODE_VERSION`) tartalmát jeleníti meg.

* [Dockerfile-base][dockerfile-base]: A rendszerkép, amelyet a `Dockerfile-app` az alapjaként ad meg. Egy [Node][base-node]-rendszerképen alapul, és tartalmazza a `NODE_VERSION` környezeti változót.

A következő szakaszokban létrehozunk egy feladatot, frissítjük a `NODE_VERSION` értékét az alapként szolgáló rendszerkép Docker-fájljában, majd az ACR Tasks használatával összeállítjuk az alapként szolgáló rendszerképet. Amikor az ACR-feladat leküldi az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az automatikusan kiváltja az alkalmazás-rendszerkép összeállítását. Ha kívánja, az alkalmazástároló-rendszerkép helyi futtatásával megtekintheti az összeállított rendszerképek eltérő verzió-sztringjeit.

Ebben az oktatóanyagban az ACR-feladat egy Docker-fájlban megadott alkalmazástároló-rendszerképet hoz létre és lelök. Az [ACR-feladatok többlépéses feladatokat](container-registry-tasks-multi-step.md)is futtathatnak, yaml-fájl használatával definiálhatják a több tároló létrehozásának, leküldéses és opcionális tesztelésének lépéseit.

## <a name="build-the-base-image"></a>Az alapként szolgáló rendszerkép összeállítása

Kezdje azzal, hogy az alapképet egy ACR-feladattal *készíti*el , az [acr build][az-acr-build]használatával. Amint azt a sorozat [első részében](container-registry-tutorial-quick-task.md) bemutattuk, ez a folyamat nem csupán összeállítja a rendszerképet, hanem siker esetén le is küldi azt a tárolóregisztrációs adatbázisba. Ebben a példában a rendszerkép az alaplemezkép-beállításjegyzékbe kerül.

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Feladat létrehozása a privát alapkép nyomon követéséhez

Ezután hozzon létre egy feladatot az alkalmazáskép-jegyzékben az [acr feladat létrehozása,][az-acr-task-create]amely lehetővé teszi a [felügyelt identitást.](container-registry-tasks-authentication-managed-identity.md) A felügyelt identitás későbbi lépésekben használatos, így a feladat hitelesíti magát az alaplemezkép-beállításjegyzékkel. 

Ez a példa egy rendszer által hozzárendelt identitást használ, de létrehozhat és engedélyezhet egy felhasználó által hozzárendelt felügyelt identitást bizonyos esetekben. További információt a [Rendszerleíróadatbázis-hitelesítés egy Azure által felügyelt identitást használó ACR-feladatban című témakörben](container-registry-tasks-cross-registry-authentication.md)talál.

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


Ez a feladat hasonló az [előző oktatóanyagban](container-registry-tutorial-build-task.md)létrehozott feladathoz. A feladat utasítja az ACR Tasksot egy rendszerkép-összeállítás aktiválására, amikor a rendszer leküld egy véglegesítést a `--context` által megadott adattárba. Míg az előző oktatóanyagban a lemezkép létrehozásához használt Dockerfile egy nyilvános alaprendszerképet (`FROM node:9-alpine`ad meg a feladatban, a [Dockerfile-app,][dockerfile-app]egy alaplemezképet az alaprendszerkép-beállításjegyzékben:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Ez a konfiguráció megkönnyíti a keretrendszer-javítás szimulálását az alaplemezképben az oktatóanyag későbbi részében.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Identitáslekéréses engedélyek megadása a rendszerleíró adatbázis alapbeállításához

Ahhoz, hogy a feladat felügyelt identitás engedélyeket lekérése lemezképek az alaplemezkép-beállításjegyzékből, először futtassa [az az acr feladat show-hoz][az-acr-task-show] kap a szolgáltatás egyszerű azonosítója az identitás. Ezután futtassa [az az acr show-t][az-acr-show] az alaprendszerleíró adatbázis erőforrás-azonosítójának leéséhez:

```azurecli-interactive
# Get service principal ID of the task
principalID=$(az acr task show --name taskhelloworld --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Rendelje hozzá a felügyelt identitáslekéréses engedélyeket a rendszerleíró adatbázishoz az [a szerepkör-hozzárendelés létrehozása futtatásával:][az-role-assignment-create]

```azurecli-interactive
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Célrendszerleíró adatbázis hitelesítő adatainak hozzáadása a feladathoz

Futtassa [az acr feladat hitelesítő adatok hozzáadása][az-acr-task-credential-add] hitelesítő adatok hozzáadása a feladathoz. Adja `--use-identity [system]` át a paramétert annak jelzésére, hogy a feladat rendszeráltal hozzárendelt felügyelt identitása hozzáférhet a hitelesítő adatokhoz.

```azurecli-interactive
az acr task credential add \
  --name taskhelloworld \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Az [acr feladat futtatásával][az-acr-task-run] manuálisan indítsa el a feladatot, és az alkalmazáslemezképet. Erre a lépésre azért van szükség, hogy a feladat nyomon követi az alkalmazásrendszer alaplemezképtől való függőségét.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Amint a feladat véget ért, jegyezze fel a **Run ID** (Futtatási azonosító) értékét (például „da6”), ha a következő nem kötelező lépést is végre szeretné hajtani.

### <a name="optional-run-application-container-locally"></a>Nem kötelező: Alkalmazástároló helyi futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, a tároló futtatásakor az alkalmazást megtekintheti egy webböngészőben, mielőtt újraépítené az alapként szolgáló rendszerképét. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja az `az acr login` és a `docker run` parancsot).

Először hitelesítse magát a tároló rendszerleíró adatbázisában [az acr bejelentkezéssel:][az-acr-login]

```azurecli
az acr login --name $ACR_NAME
```

Futtassa helyileg a tárolót a `docker run` paranccsal. Cserélje le ** \<a\> futtatási azonosítót** az előző lépés kimenetében található futtatási azonosítóra (például "da6"). Ez a példa `myapp` elnevezi `--rm` a tárolót, és tartalmazza a tároló leállításának paraméterét.

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

Ezután listázza a feladatfuttatásokat, amelyeket az ACR Tasks elkészített a regisztrációs adatbázis számára az [az acr task list-runs][az-acr-task-list-runs] paranccsal:

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

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogyan használhatók a feladatok a tárolórendszerképek összeállításának automatikus aktiválására a rendszerkép alapként szolgáló rendszerképének frissítése esetén. Most lépjen tovább a következő oktatóanyagra, és ismerje meg, hogyan indíthat el feladatokat egy meghatározott ütemezés szerint.

> [!div class="nextstepaction"]
> [Feladat futtatása ütemezés szerint](container-registry-tasks-scheduled.md)

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
