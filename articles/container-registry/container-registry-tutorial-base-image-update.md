---
title: Oktatóanyag – Az Azure Container Registry Build használata a tárolórendszerképek összeállításának az alapként szolgáló rendszerkép frissítésekor történő automatikus indításához
description: Ez az oktatóanyag azt mutatja be, hogy hogyan konfigurálhatók az összeállítási feladatok úgy, hogy automatikusan aktiválják a tárolórendszerképek összeállítását a felhőben egy alapként szolgáló rendszerkép frissítésekor.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 531aeaacf0bd70521d70afb45d141fc3296ebb04
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>Oktatóanyag: Az Azure Container Registry Build használata a rendszerképek összeállításának az alapként szolgáló rendszerkép frissítésekor történő automatikus indításához

Az ACR Build támogatja az összeállítások automatizált végrehajtását a tárolók alapként szolgáló rendszerképének frissítésekor, például ha az operációs rendszer vagy az alkalmazás-keretrendszer javítását telepíti az alapként szolgáló rendszerképek egyikében. Ez az oktatóanyag azt mutatja be, hogy hogyan hozhat létre olyan összeállítási feladatot az ACR Buildben, amely összeállítást aktivál a felhőben a tárolók alapként szolgáló rendszerképeinek a regisztrációs adatbázisba való leküldésekor.

Az oktatóanyag-sorozat utolsó része az alábbi lépésekből áll:

> [!div class="checklist"]
> * Az alapként szolgáló rendszerkép összeállítása
> * Alkalmazásrendszerkép-összeállítási feladat létrehozása
> * Az alapként szolgáló rendszerkép frissítése egy alkalmazásrendszerkép-összeállítás aktiválásához
> * Az aktivált összeállítás megjelenítése
> * A frissített alkalmazás-rendszerkép ellenőrzése

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI-t helyben szeretné használni, az Azure CLI **2.0.32-es** vagy újabb verzióját kell telepítenie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése][azure-cli].

## <a name="prerequisites"></a>Előfeltételek

### <a name="complete-previous-tutorials"></a>Az előző oktatóanyagok elvégzése

Ez az oktatóanyag feltételezi, hogy elvégezte a sorozat első két oktatóanyagának alábbi lépéseit:

* Azure Container Registry létrehozása
* Mintaadattár leágaztatása
* Mintaadattár klónozása
* Személyes hozzáférési jogkivonat létrehozása a GitHubban

Ha még nem tette meg, végezze el az első két oktatóanyagot, mielőtt továbblépne:

[Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Build használatával](container-registry-tutorial-quick-build.md)

[Tárolórendszerképek összeállításának automatizálása az Azure Container Registry Build használatával](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>A környezet konfigurálása

Láss el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem látja el értékkel a változókat, manuálisan kell majd behelyettesítenie az egyes értékeket, amikor megjelennek a példaparancsokban.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Alapként szolgáló rendszerképek

A tárolórendszerképek többségét definiáló Docker-fájlok megadnak egy szülőrendszerképet, amely a rendszerképek alapjául szolgál – ezt gyakran *alapként szolgáló rendszerképnek* nevezik. Az alapként szolgáló rendszerképek általában az operációs rendszert tartalmazzák (például [Alpine Linux][base-alpine] vagy [Windows Nano Server][base-windows]), amelyre a tároló többi szintje alkalmazva lesz. Alkalmazás-keretrendszereket is tartalmazhatnak, például a [Node.js][base-node] vagy a [.NET Core][base-dotnet] keretrendszert.

### <a name="base-image-updates"></a>Az alapként szolgáló rendszerképek frissítése

A rendszerképek kezelői gyakran frissítik az alapként szolgáló rendszerképet, hogy új szolgáltatásokkal és javításokkal bővítsék a rendszerképben található operációs rendszert vagy keretrendszert. A biztonsági javítások jelentik az alapként szolgáló rendszerkép frissítésének egy másik gyakori okát.

Az alapként szolgáló rendszerképek frissítésekor újra össze kell állítania a rájuk épülő regisztrációs adatbázisokban található tárolórendszerképeket is, hogy tartalmazzák az új szolgáltatásokat és javításokat. Az ACR Build lehetővé teszi a rendszerképek automatikus összeállítását a tárolók alapként szolgáló rendszerképének frissítésekor.

### <a name="base-image-update-scenario"></a>Az alapként szolgáló rendszerkép frissítése forgatókönyv

Ez az oktatóanyag végigvezeti az alapként szolgáló rendszerkép frissítési forgatókönyvén. A [kódminta][code-sample] két Docker-fájlt tartalmaz: egy alkalmazás-rendszerképet, és egy rendszerképet, amelyet az alkalmazás-rendszerkép az alapként szolgáló rendszerképeként ad meg. A következő szakaszokban egy ACR Build-feladatot hoz létre, amely automatikusan aktiválja az alkalmazás-rendszerkép összeállítását, ha a rendszer az alapul szolgáló rendszerkép új verzióját küldi le a tárolóregisztrációs adatbázisba.

[Dockerfile-app][dockerfile-app]: Egy kisméretű Node.js-webalkalmazás, amely az alapjául szolgáló Node.js-verziót jelző statikus weboldalt jelenít meg. A verzió-karakterlánc szimulált elem abban az értelemben, hogy az alapként szolgáló rendszerképben definiált környezeti változó (`NODE_VERSION`) tartalmát jeleníti meg.

[Dockerfile-base][dockerfile-base]: A rendszerkép, amelyet a `Dockerfile-app` az alapjaként ad meg. Egy [Node][base-node]-rendszerképen alapul, és tartalmazza a `NODE_VERSION` környezeti változót.

A következő szakaszokban létrehozunk egy összeállítási feladatot, frissítjük a `NODE_VERSION` értékét az alapként szolgáló rendszerkép Docker-fájljában, majd az ACR Build használatával összeállítjuk az alapként szolgáló rendszerképet. Amikor az ACR Build leküldi az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az automatikusan kiváltja az alkalmazás-rendszerkép összeállítását. Ha kívánja, az alkalmazástároló-rendszerkép helyi futtatásával megtekintheti az összeállított rendszerképek eltérő verzió-karakterláncait.

## <a name="build-base-image"></a>Alapként szolgáló rendszerkép összeállítása

Első lépésként állítsa össze az alapként szolgáló rendszerképet az ACR Build *gyors összeállítás* funkciójával. Amint azt a sorozat [első részében](container-registry-tutorial-quick-build.md) bemutattuk, a funkció nem csupán összeállítja a rendszerképet, hanem siker esetén le is küldi azt a tárolóregisztrációs adatbázisba.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>Összeállítási feladat létrehozása

Ezután hozzon létre egy összeállítási feladatot az [az acr build-task create][az-acr-build-task-create] paranccsal:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

Ez az összeállítási feladat hasonlít az [előző oktatóanyagban](container-registry-tutorial-build-task.md) létrehozott feladathoz. A feladat utasítja az ACR Buildet egy rendszerkép-összeállítás aktiválására, amikor a rendszer leküld egy véglegesítést a `--context` által megadott adattárba.

A működése abban tér el, hogy az *alapként szolgáló rendszerképének* a frissítésekor is aktiválja a rendszerkép összeállítását. A `--file` argumentum által meghatározott Docker-fájl, a [Dockerfile-app][dockerfile-app] az alapjával egyező regisztrációs adatbázisból származó rendszerképek meghatározását támogatja:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Összeállítási feladatok futtatásakor az ACR Build észleli a rendszerképek függőségeit. Ha a `FROM` utasításban megadott, alapként szolgáló rendszerkép ugyanabban a regisztrációs adatbázisban található, hozzáad egy hookot, amely biztosítja a rendszerkép újbóli összeállítását az alapjának a frissítésekor.

> [!NOTE]
> Az előzetes verzióban az ACR Build csak akkor támogatja a származtatott rendszerképek összeállítását, ha az alapként szolgáló rendszerkép és az arra hivatkozó rendszerkép ugyanabban az Azure-beli tárolóregisztrációs adatbázisban található.

## <a name="build-application-container"></a>Alkalmazástároló összeállítása

Ahhoz, hogy az ACR Build meghatározhassa és nyomon követhesse egy tárolórendszerképek függőségeit – amelyek az alapul szolgáló rendszerképet is magukban foglalják –, először **legalább egyszer** aktiválnia **kell** a rendszerkép összeállítási feladatát.

Az [az acr build-task run][az-acr-build-task-run] parancs használatával manuálisan aktiválja az összeállítási feladatot, és állítsa össze az alkalmazás-rendszerképet:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Amint az összeállítás véget ért, jegyezze fel a **Build ID** (Összeállítás-azonosító) értékét (például „aa6”), ha a következő nem kötelező lépést is végre szeretné hajtani.

### <a name="optional-run-application-container-locally"></a>Nem kötelező: Alkalmazástároló helyi futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, a tároló futtatásakor az alkalmazást megtekintheti egy webböngészőben, mielőtt újraépítené az alapként szolgáló rendszerképét. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja az `az acr login` és a `docker run` parancsot).

Először jelentkezzen be a tárolóregisztrációs adatbázisba az [az acr login][az-acr-login] paranccsal:

```azurecli
az acr login --name $ACR_NAME
```

Futtassa helyileg a tárolót a `docker run` paranccsal. A **\<build-id\>** (összeállítás-azonosító) helyére az előző lépés kimenetében található összeállítás-azonosítót (például „aa6”) írja.

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Lépjen a http://localhost:8080 címre a böngészőben, és a weboldalon a Node.js verziószámának kell megjelennie az alábbihoz hasonlóan. Egy későbbi lépésben majd újabbra frissíti a verziót egy „a” tag hozzáadásával a verzió-karakterlánchoz.

![A böngészőben megjelenített mintaalkalmazás képernyőképe][base-update-01]

## <a name="list-builds"></a>Összeállítások listázása

Ezután listázza az összeállításokat, amelyeket az ACR Build elkészített a regisztrációs adatbázis számára:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Ha elvégezte az előző oktatóanyagot (és nem törölte a regisztrációs adatbázist), az alábbihoz hasonló kimenetnek kell megjelennie. Jegyezze fel az összeállítások számát, valamint a BUILD ID (Összeállítás-azonosító) oszlop legújabb értékét, hogy összehasonlíthassa a kimenettel, miután frissítette az alapként szolgáló rendszerképet a következő szakaszban.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa6         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual      2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="update-base-image"></a>Az alapként szolgáló rendszerkép frissítése

Itt most egy keretrendszer-javítást szimulál az alapként szolgáló rendszerképben. Módosítsa a **Dockerfile-base** fájlt, és adja hozzá az „a” tagot a `NODE_VERSION` attribútumban meghatározott verziószám után:

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

Futtasson egy gyors összeállítást az ACR Buildben az alapként szolgáló módosított rendszerkép összeállításához. Jegyezze fel a kimenetben található **Build ID** (Összeállítás-azonosító) értékét.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Miután befejeződött az összeállítás, és az ACR Build leküldte az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, ami kiváltja az alkalmazás-rendszerkép összeállítását. Eltarthat némi ideig, amíg a korábban létrehozott ACR Build-feladat aktiválja az alkalmazás-rendszerkép összeállítását, mivel észlelnie kell az újonnan elkészült és leküldött alapként szolgáló rendszerképet.

## <a name="list-builds"></a>Összeállítások listázása

Az alapként szolgáló rendszerkép frissítését követően, listázza újra az összeállításokat, hogy összehasonlíthassa azokat a korábbi listával. Ha a kimenet eleinte nem tér el a korábbitól, időnként futtassa újra a parancsot, amíg az új összeállítás meg nem jelenik a listában.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

A kimenet a következőkhöz hasonló. Az utolsóként végrehajtott összeállítás TRIGGER (Eseményindító) értékének az „Image Update” (Rendszerképfrissítés) értéknek kell lennie, ami jelzi, hogy az összeállítási feladatot az alapként szolgáló rendszerkép gyors összeállítása aktiválta.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
aa8         buildhelloworld  Linux       Succeeded  Image Update  2018-05-10T20:09:52Z  00:00:45
aa7                          Linux       Succeeded  Manual        2018-05-10T20:09:17Z  00:00:40
aa6         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual        2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit    2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual        2018-05-10T19:10:14Z  00:00:55
```

Ha szeretné végrehajtani a következő nem kötelező lépést az újonnan összeállított tároló futtatására és a frissített verziószám megtekintésére, jegyezze fel a rendszerképfrissítés által kiváltott összeállítás **BUILD ID** (Összeállítás-azonosító) értékét (az előző kimenetben ez az „aa8” érték).

### <a name="optional-run-newly-built-image"></a>Nem kötelező: Az újonnan összeállított rendszerkép futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, futtassa az új alkalmazás-rendszerképet, amint annak összeállítása befejeződött. A `<build-id>` az előző lépésben beszerzett összeállítás-azonosítót írja. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja a `docker run` parancsot).

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Lépjen a http://localhost:8081 címre a böngészőben, ahol a weboldalon a Node.js frissített verziószámának (az „a” taggal kiegészítve) kell megjelennie:

![A böngészőben megjelenített mintaalkalmazás képernyőképe][base-update-02]

Figyelje meg, hogy miután frissítette az **alapként szolgáló rendszerképet** egy új verziószámmal, az utoljára összeállított **alkalmazás-rendszerkép** az új verziót jeleníti meg. Az ACR Build észlelte az alapként szolgáló rendszerkép módosítását, és automatikusan újraépítette az alkalmazás-rendszerképet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag-sorozatban létrehozott összes erőforrás, így a tárolóregisztrációs adatbázis, a tárolópéldány, a kulcstároló és a szolgáltatásnév eltávolításához hajtsa végre az alábbi parancsokat:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogy hogyan használhatók az összeállítási feladatok a tárolórendszerképek összeállításának automatikus aktiválására a rendszerkép alapként szolgáló rendszerképének frissítése esetén. Most lépjen tovább a tárolóregisztrációs adatbázisban való hitelesítést ismertető témakörre.

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
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png