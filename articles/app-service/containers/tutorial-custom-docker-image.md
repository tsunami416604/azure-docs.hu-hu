---
title: 'Oktatóanyag: egyéni rendszerkép létrehozása és futtatása Azure App Service'
description: Lépésenkénti útmutató egyéni Linux-rendszerképek létrehozásához, a lemezkép Azure Container Registry való elküldéséhez, majd a lemezkép központi telepítéséhez Azure App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
ms.custom: mvc, seodec18, tracking-python
ms.openlocfilehash: bfe1e9fd2532e308c474aee6983615e28a8081f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506839"
---
# <a name="tutorial-run-a-custom-docker-image-in-app-service"></a>Oktatóanyag: egyéni Docker-rendszerkép futtatása App Service

A Azure App Service a Docker-tároló technológiáját használja a beépített rendszerképek és az Egyéni rendszerképek üzemeltetéséhez. A beépített rendszerképek listájának megtekintéséhez futtassa az ["az WebApp List-Runtimes--Linux"](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)nevű Azure CLI-parancsot. Ha ezek a képek nem felelnek meg az igényeinek, létrehozhat és üzembe helyezhet egyéni lemezképeket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy egyéni rendszerképet, ha a beépített rendszerkép nem elégíti ki az igényeinek megfelelőt
> * Az egyéni rendszerkép leküldése az Azure-beli privát tároló-beállításjegyzékbe
> * Az egyéni rendszerkép futtatása App Service
> * Környezeti változók konfigurálása
> * A rendszerkép frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók elérése
> * Csatlakozás a tárolóhoz SSH használatával

Az oktatóanyag elvégzésével kis díjat számítunk fel az Azure-fiókjában a Container Registry számára, és további költségekkel jár, ha a tárolót több mint egy hónap alatt üzemelteti.

## <a name="set-up-your-initial-environment"></a>A kezdeti környezet beállítása

* Rendelkeznie kell aktív előfizetéssel rendelkező Azure-fiókkal. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Telepítse a [Docker](https://docs.docker.com/get-started/#setup)-t, amelyet Docker-rendszerképek létrehozásához használ. A Docker telepítéséhez szükség lehet a számítógép újraindítására.
* Telepítse az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 vagy újabb verzióját, amellyel az Azure-erőforrások kiépítéséhez és konfigurálásához bármilyen rendszerhéjban parancsokat futtathat.

A Docker és az Azure CLI telepítése után nyisson meg egy terminál ablakot, és ellenőrizze, hogy telepítve van-e a Docker:

```bash
docker --version
```

Győződjön meg arról is, hogy az Azure CLI verziója 2.0.80 vagy magasabb:

```azurecli
az --version
```

Ezután jelentkezzen be az Azure-ba a CLI használatával:

```azurecli
az login
```

A `az login` parancs megnyit egy böngészőt a hitelesítő adatok összegyűjtéséhez. Ha a parancs befejeződik, az megjeleníti a JSON-kimenetet, amely az előfizetésekkel kapcsolatos információkat tartalmaz.

Miután bejelentkezett, futtathatja az Azure-parancsokat az Azure CLI-vel, hogy az előfizetésében lévő erőforrásokkal működjön.

## <a name="clone-or-download-the-sample-app"></a>A minta alkalmazás klónozása vagy letöltése

Ehhez az oktatóanyaghoz a git-klón vagy a letöltés használatával szerezheti be a mintát.

### <a name="clone-with-git"></a>Klónozás a git-vel

A minta tárház klónozása:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Ügyeljen arra, hogy tartalmazza az `--config core.autocrlf=input` argumentumot, amely garantálja a megfelelő sorok befejezését a Linux-tárolón belül használt fájlokban:

Ezután nyissa meg a mappát:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Letöltés a GitHubról

A git-klón használata helyett látogasson el a klónozás lehetőségre, [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) majd válassza a **zip letöltése**lehetőséget. **Clone** 

Csomagolja ki a ZIP-fájlt egy *Docker-Django-WebApp-Linux*nevű mappába. 

Ezután nyisson meg egy terminál-ablakot a *Docker-Django-WebApp-Linux* mappában.

## <a name="optional-examine-the-docker-file"></a>Választható A Docker-fájl vizsgálata

A _Docker_ nevű mintában szereplő fájl, amely leírja a Docker-rendszerképet, és konfigurációs utasításokat tartalmaz:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* A parancsok első csoportja telepíti az alkalmazás követelményeit a környezetben.
* A második parancs egy [SSH](https://www.ssh.com/ssh/protocol/) -kiszolgálót hoz létre a tároló és a gazdagép közötti biztonságos kommunikációhoz.
* Az utolsó sor, `ENTRYPOINT ["init.sh"]` amely elindítja `init.sh` az SSH-szolgáltatást és a Python-kiszolgálót.

## <a name="build-and-test-the-image-locally"></a>A rendszerkép helyi létrehozása és tesztelése

1. A rendszerkép létrehozásához futtassa a következő parancsot:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Ellenőrizze, hogy a Build működik-e a Docker-tároló helyi futtatásával:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Ez [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) a parancs az argumentumot tartalmazó portot adja meg, `-p` amelyet a rendszerkép neve követ. 
    
    > [!TIP]
    > Ha Windows rendszeren fut, és megtekinti a hibát, *standard_init_linux. go: 211: az exec felhasználói folyamata "nincs ilyen fájl vagy könyvtár"*, a *init.sh* -fájl a várt LF végződés helyett a CR-LF sorok végét tartalmazza. Ez a hiba akkor fordul elő, ha a git használatával klónozott a minta tárházat, de kihagyta a `--config core.autocrlf=input` paramétert. Ebben az esetben a tárházat a "--config" argumentummal újra klónozással. A hiba akkor is megjelenhet, ha szerkesztette a *init.sh* , és CRLF-végződésekkel mentette. Ebben az esetben mentse újra a fájlt csak LF végződéssel.

1. `http://localhost:8000`A webalkalmazás és a tároló megfelelő működésének ellenőrzéséhez keresse fel a következőt:.

    ![Webalkalmazás helyi tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Ebben a szakaszban és a következő lépésekben olyan erőforrásokat kell kiépíteni az Azure-ban, amelyeken leküldi a lemezképet, majd üzembe helyezi a tárolót Azure App Service. Először hozzon létre egy erőforráscsoportot, amelyben az összes erőforrást össze szeretné gyűjteni.

Futtassa az az [Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) parancsot egy erőforráscsoport létrehozásához:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Az érték módosításával `--location` megadhatja az Ön közelében lévő régiót.

## <a name="push-the-image-to-azure-container-registry"></a>A rendszerkép leküldése Azure Container Registry

Ebben a szakaszban leküldi a rendszerképet arra a Azure Container Registry, amelyről a App Service telepítheti.

1. [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create)Azure Container Registry létrehozásához futtassa a parancsot:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Cserélje le a helyére a `<registry-name>` beállításjegyzék megfelelő nevét. A név csak betűket és számokat tartalmazhat, és egyedinek kell lennie az összes Azure-ban.

1. Futtassa a [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) parancsot a beállításjegyzék hitelesítő adatainak lekéréséhez:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    A parancs JSON-kimenete két jelszót biztosít a beállításjegyzék felhasználónevével együtt.
    
1. A `docker login` parancs használatával jelentkezzen be a tároló-beállításjegyzékbe:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Cserélje `<registry-name>` le `<registry-username>` az és az értékeket az előző lépésekből származó értékekre. Ha a rendszer kéri, írja be az előző lépésben szereplő jelszavak egyikét.

    Ugyanazt a beállításjegyzék-nevet használja a szakasz összes hátralévő lépésében.

1. Miután a bejelentkezés sikeres volt, címkézze meg a beállításjegyzék helyi Docker-rendszerképét:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. A következő `docker push` paranccsal küldje le a rendszerképet a beállításjegyzékbe:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    A rendszerkép feltöltése az első alkalommal eltarthat néhány percig, mert az kiinduló képet tartalmazza. A későbbi feltöltések általában gyorsabbak.

    Várakozás közben a következő szakaszban ismertetett lépések végrehajtásával konfigurálhatja a App Servicet a beállításjegyzékből való telepítéshez.

1. A `az acr repository list` parancs használatával ellenőrizze, hogy a leküldése sikeres volt-e:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    A kimenetnek meg kell jelenítenie a rendszerkép nevét.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>App Service konfigurálása a rendszerkép beállításjegyzékből való üzembe helyezéséhez

Azure App Service tároló üzembe helyezéséhez először létre kell hoznia egy webalkalmazást a App Service, majd a webalkalmazást a tároló-beállításjegyzékhez kell kötni. A webalkalmazás indításakor App Service automatikusan lekéri a rendszerképet a beállításjegyzékből.

1. Hozzon létre egy App Service tervet a [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) parancs használatával:

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    A App Service-csomag megfelel a webalkalmazást futtató virtuális gépnek. Alapértelmezés szerint az előző parancs egy olcsó B1-es [díjszabási szintet](https://azure.microsoft.com/pricing/details/app-service/linux/) használ, amely az első hónapban ingyenes. A szintet a paraméterrel szabályozhatja `--sku` .

1. Hozza létre a webalkalmazást a következő [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal:

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Cserélje le a `<app-name>` nevet a webalkalmazás nevére, amelynek egyedinek kell lennie az összes Azure-ban. Cserélje le a `<registry-name>` elemet az előző szakaszban szereplő beállításjegyzék nevére is.

1. A [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) `WEBSITES_PORT` környezeti változó az alkalmazás kódjának megfelelően történő beállításához használja a következőt: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Cserélje le az `<app-name>` nevet az előző lépésben használt névre.
    
    A környezeti változóval kapcsolatos további információkért tekintse [meg a minta GitHub-tárházában található Readme](https://github.com/Azure-Samples/docker-django-webapp-linux)témakört.

1. [Felügyelt identitás](/azure/app-service/overview-managed-identity) engedélyezése a webalkalmazás számára a következő [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) paranccsal:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Cserélje le az `<app-name>` nevet az előző lépésben használt névre. A parancs kimenete (a és az argumentumok alapján szűrve `--query` `--output` ) a hozzárendelt identitás egyszerű szolgáltatása, amelyet hamarosan használ.

    A felügyelt identitás lehetővé teszi, hogy engedélyeket adjon a webalkalmazásnak más Azure-erőforrások eléréséhez, anélkül, hogy konkrét hitelesítő adatokat kellene megadnia.

1. Kérje le az előfizetés-azonosítót a [`az account show`](/cli/azure/account?view=azure-cli-latest#az-account-show) paranccsal, amelyet a következő lépésben kell megadnia:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Adja meg a webalkalmazás engedélyt a tároló beállításjegyzékének eléréséhez:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Cserélje le a következő értékeket:
    - `<principal-id>`a szolgáltatás egyszerű azonosítója a `az webapp identity assign` parancsból
    - `<registry-name>`a tároló-beállításjegyzék nevével
    - `<subscription-id>`a parancsból beolvasott előfizetés-AZONOSÍTÓval `az account show`

További információ ezekről az engedélyekről: [Mi az Azure szerepköralapú hozzáférés-vezérlés](/azure/role-based-access-control/overview) és 

## <a name="deploy-the-image-and-test-the-app"></a>A rendszerkép üzembe helyezése és az alkalmazás tesztelése

Ezeket a lépéseket akkor hajthatja végre, ha a rendszerkép leküldése a tároló-beállításjegyzékbe történik, és a App Service teljesen kiépítve.

1. A [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) paranccsal adhatja meg a tároló-beállításjegyzéket és a webalkalmazáshoz telepítendő lemezképet:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Cserélje le a `<app_name>` nevet a webalkalmazás nevére, és cserélje le `<registry-name>` két helyen a beállításjegyzék nevével. 

    - Ha a Docker hub-tól eltérő beállításjegyzéket használ (ebben a példában látható), akkor `--docker-registry-server-url` `https://` a beállításjegyzék teljes tartományneve után kell formázni.
    - A következő üzenet jelenik meg: "nincs hitelesítő adat a Azure Container Registry eléréséhez. Kísérlet a keresésre... " azt jelzi, hogy az Azure az alkalmazás felügyelt identitását használja a tároló beállításjegyzékének hitelesítéséhez, és nem kér felhasználónevet és jelszót.
    - Ha hibát tapasztal, a "AttributeError:" NoneType "objektumnak nincs" foglalt "attribútuma, ellenőrizze, hogy `<app-name>` helyes-e.

    > [!TIP]
    > A webalkalmazás tárolójának beállításait bármikor lekérheti a paranccsal `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . A rendszerkép a tulajdonságban van megadva `DOCKER_CUSTOM_IMAGE_NAME` . Ha a webalkalmazás üzembe helyezése az Azure DevOps vagy a Azure Resource Manager-sablonokon keresztül történik, akkor a lemezkép a nevű tulajdonságban is megjelenhet `LinuxFxVersion` . Mindkét tulajdonság ugyanazt a célt szolgálja. Ha mindkettő megtalálható a webalkalmazás konfigurációjában, `LinuxFxVersion` elsőbbséget élvez.

1. A `az webapp config container set` parancs végrehajtása után a webalkalmazásnak a app Service tárolójában kell futnia.

    Az alkalmazás teszteléséhez keresse meg a `http://<app-name>.azurewebsites.net` következőt:, és cserélje le a kifejezést `<app-name>` a webalkalmazás nevére. Először is eltarthat egy ideig, amíg az alkalmazás válaszolni tud, mert App Service a teljes rendszerképet a beállításjegyzékből kell lekérnie. Ha a böngésző túllépi az időkorlátot, csak frissítse az oldalt. A kezdeti rendszerkép kihúzása után a további tesztek sokkal gyorsabban futnak.

    ![Az Azure-beli webalkalmazás sikeres tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Az alkalmazás kódjának módosítása és újbóli üzembe helyezése

Ebben a szakaszban módosítja a webalkalmazás kódját, újraépíti a tárolót, majd leküldi a tárolót a beállításjegyzékbe. App Service ezután automatikusan lekéri a frissített rendszerképet a beállításjegyzékből a futó webalkalmazás frissítéséhez.

1. A helyi *Docker-Django-WebApp-Linux* mappában Nyissa meg a file *app/templates/app/index.html*fájlt.

1. Módosítsa az első HTML-elemet úgy, hogy az megfeleljen a következő kódnak.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Mentse a módosításokat.

1. Váltson a *Docker-Django-WebApp-Linux* mappára, és hozza létre újra a rendszerképet:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Frissítse a rendszerkép címkéjén található verziószámot v 1.0.1-re:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Cserélje le a `<registry-name>` elemet a tárolójegyzék nevére.

1. Küldje le a rendszerképet a beállításjegyzékbe:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Indítsa újra a webalkalmazást:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    A `<app_name>` rész helyére a webalkalmazás nevét írja be. Újraindítás után App Service lekéri a frissített rendszerképet a tároló-beállításjegyzékből.

1. A tallózással ellenőrizze, hogy a frissítés telepítve van-e `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

1. A tároló naplózásának bekapcsolása:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. A log stream engedélyezése:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

    A naplófájlokat a böngészőből is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

1. Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a **CTRL C billentyűt** + **C**.

## <a name="connect-to-the-container-using-ssh"></a>Csatlakozás a tárolóhoz SSH használatával

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. A tárolóhoz való SSH-kapcsolatok engedélyezéséhez konfigurálnia kell az egyéni rendszerképet. Miután a tároló fut, megnyithat egy SSH-kapcsolatokat.

### <a name="configure-the-container-for-ssh"></a>Az SSH-tároló konfigurálása

Az oktatóanyagban használt minta alkalmazás már rendelkezik a szükséges konfigurációval a *Docker*, amely telepíti az SSH-kiszolgálót, és beállítja a bejelentkezési hitelesítő adatokat is. Ez a szakasz csak tájékoztató információkat tartalmaz. A tárolóhoz való kapcsolódáshoz ugorjon a következő szakaszra

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Ez a konfiguráció nem engedélyezi a külső kapcsolatokat a tárolóval. Az SSH csak a Kudu/SCM webhelyen keresztül érhető el. Az kudu/SCM-hely hitelesítése az Azure-fiókkal történik.

A *Docker* az *sshd_config* fájlt is átmásolja a */etc/ssh/* mappába, és az 2222-es portot a tárolón teszi elérhetővé:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Az 2222-es port egy belső port, amely csak a privát virtuális hálózathoz tartozó Bridge hálózaton lévő tárolók számára érhető el. 

Végül, a *init.sh*, elindítja az SSH-kiszolgálót.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>A tároló SSH-kapcsolatának megnyitása

1. Keresse meg `https://<app-name>.scm.azurewebsites.net/webssh/host` és jelentkezzen be az Azure-fiókjával. A `<app-name>` rész helyére a webalkalmazás nevét írja be.

1. Miután bejelentkezett, a rendszer átirányítja a webalkalmazáshoz tartozó információs oldalra. Válassza az **SSH** lehetőséget az oldal tetején a rendszerhéj megnyitásához és a parancsok használatához.

    Megvizsgálhatja például a-ben futó folyamatokat a parancs használatával `top` .
    
## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Az ebben a cikkben létrehozott erőforrások folyamatos költségeket okozhatnak. az erőforrások törléséhez csak az azokat tartalmazó erőforráscsoportot kell törölnie:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Egyéni rendszerkép üzembe helyezése egy privát tároló beállításjegyzékében
> * Üzembe helyezés és az egyéni lemezkép App Service
> * A rendszerkép frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók elérése
> * Csatlakozás a tárolóhoz SSH használatával

A következő oktatóanyagban megismerheti, hogyan képezhető le egyéni DNS-név az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Multi-Container WordPress-alkalmazás](tutorial-multi-container-app.md)
