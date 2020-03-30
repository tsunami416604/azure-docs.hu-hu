---
title: Egyéni Linux-tároló konfigurálása
description: Ismerje meg, hogyan konfigurálhat egy egyéni Linux-tárolót az Azure App Service-ben. Ez a cikk a leggyakoribb konfigurációs feladatokat mutatja be.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280143"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Egyéni Linux-tároló konfigurálása az Azure App Service-hez

Ez a cikk bemutatja, hogyan konfigurálhat egy egyéni Linux-tárolót az Azure App Service-en való futtatáshoz.

Ez az útmutató a legfontosabb fogalmakat és utasításokat a Linux-alkalmazások az App Service-ben. Ha még soha nem használta az Azure App Service-t, kövesse először az [egyéni tároló rövid útmutatóját](quickstart-docker-go.md) és [oktatóanyagát.](tutorial-custom-docker-image.md) Van is egy [több tárolós alkalmazás gyorsindítás](quickstart-multi-container.md) és [bemutató](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Portszám konfigurálása

Az egyéni lemezkép webkiszolgálója 80-tól eltérő portot is használhat. Az egyéni tároló által használt portról az `WEBSITES_PORT` alkalmazásbeállítás használatával tájékoztassa az Azure-t. A [jelen oktatóanyagban lévő Python-mintához](https://github.com/Azure-Samples/docker-django-webapp-linux) tartozó GitHub-oldalon az látható, hogy a `WEBSITES_PORT` értékét _8000_-re kell állítani. Beállíthatja, hogy [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) fut parancs a Cloud Shell. Példa:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az egyéni tároló használhat olyan környezeti változókat, amelyeket külsőleg kell megadni. A Cloud Shell parancs [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) futtatásával adhatja át őket. Példa:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Ez a módszer egytárolós alkalmazásokhoz vagy többtárolós alkalmazásokhoz is működik, ahol a környezeti változók a *docker-compose.yml* fájlban vannak megadva.

## <a name="use-persistent-shared-storage"></a>Állandó megosztott tároló használata

Az alkalmazás fájlrendszerében található */home* könyvtár segítségével megőrizheti a fájlokat az újraindítások során, és megoszthatja őket példányok között. Az `/home` alkalmazás ban biztosított, hogy a tároló alkalmazás eléréséhez állandó tároló.

Ha az állandó tároló le van `/home` tiltva, majd a címtárba írt írások nem maradnak meg az alkalmazás újraindítása vagy több példány között. Az egyetlen kivétel `/home/LogFiles` a könyvtár, amely a Docker- és tárolónaplók tárolására szolgál. Ha az állandó tároló engedélyezve `/home` van, a címtárba írt összes írás megmarad, és egy kibővített alkalmazás összes példánya elérhető.

Alapértelmezés szerint az állandó tároló *engedélyezve* van, és a beállítás nem érhető el az alkalmazás beállításai között. A letiltáshoz `WEBSITES_ENABLE_APP_SERVICE_STORAGE` állítsa be [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) az alkalmazásbeállítást a Cloud Shell parancs futtatásával. Példa:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Saját [állandó tárolót](how-to-serve-content-from-azure-storage.md)is konfigurálhat.

## <a name="enable-ssh"></a>SSH engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Annak érdekében, hogy egy egyéni tároló támogatja az SSH-t, hozzá kell adnia magát a Dockerfile-hoz.

> [!TIP]
> Az összes beépített Linux-tároló hozzáadta az SSH-utasításokat a rendszerkép-tárolókhoz. A [Node.js 10.14 tárházsegítségével](https://github.com/Azure-App-Service/node/blob/master/10.14) a következő utasításokat is megtekintheti, hogyan van engedélyezve.

- A [RUN](https://docs.docker.com/engine/reference/builder/#run) utasítással telepítse az SSH-kiszolgálót, és `"Docker!"`állítsa a gyökérfiók jelszavát a-ra. Az [Alpine Linux](https://hub.docker.com/_/alpine)on alapuló lemezképhez például a következő parancsokra van szükség:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Ez a konfiguráció nem engedélyezi a külső kapcsolatokat a tárolóhoz. Az SSH csak `https://<app-name>.scm.azurewebsites.net` a közzétételi hitelesítő adatokon keresztül érhető el, és a közzétételi hitelesítő adatokkal hitelesíthető.

- Adja hozzá [ezt a sshd_config fájlt](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) a képtárhoz, és a [COPY](https://docs.docker.com/engine/reference/builder/#copy) utasítással másolja a fájlt az */etc/ssh/* könyvtárba. A *sshd_config* fájlokról az [OpenBSD dokumentációjában](https://man.openbsd.org/sshd_config)olvashat bővebben.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Az *sshd_config* fájlnak tartalmaznia kell a következő elemeket:
    > - A `Ciphers` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - A `MACs` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `hmac-sha1,hmac-sha1-96`.

- Az [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) utasítással nyissa meg a 2222-es portot a tárolóban. Bár a gyökérjelszó ismert, a 2222-es port nem érhető el az internetről. Csak egy privát virtuális hálózat hídhálózatán belüli tárolók érhetők el.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- A tároló indítási parancsfájljában indítsa el az SSH-kiszolgálót.

    ```bash
    /usr/sbin/sshd
    ```

    Például tekintse meg, hogyan indítja el az alapértelmezett [Node.js 10.14 tároló](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) az SSH-kiszolgálót.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Többtárolós alkalmazások konfigurálása

- [Állandó tároló használata a Docker Compose-ben](#use-persistent-storage-in-docker-compose)
- [Előnézeti korlátozások](#preview-limitations)
- [Docker-írási beállítások](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Állandó tároló használata a Docker Compose-ben

Több-konténer alkalmazások, mint a WordPress kell tartós tárolás megfelelő működéséhez. Az engedélyezéshez a Docker Compose konfigurációnak a *tárolón kívüli* tárolóhelyre kell mutatnia. A tárolón belüli tárolási helyek nem maradnak meg az alkalmazás újraindítása után.

Engedélyezze az állandó `WEBSITES_ENABLE_APP_SERVICE_STORAGE` tárolást az alkalmazásbeállítás beállításával, a Cloud Shell [azaz webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsával.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

A *docker-compose.yml* fájlban `volumes` rendelje `${WEBAPP_STORAGE_HOME}`hozzá a lehetőséget a számára. 

A `WEBAPP_STORAGE_HOME` egy környezeti változó az App Service szolgáltatásban, amely az alkalmazás állandó tárolójára mutat. Példa:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Előnézeti korlátozások

A többtárolós tároló jelenleg előzetes verzióban érhető el. Az App Service platform alábbi funkciói nem támogatottak:

- Hitelesítés / engedélyezés
- Felügyelt identitások

### <a name="docker-compose-options"></a>Docker-írási beállítások

Az alábbi listák a Docker Compose támogatott és nem támogatott konfigurációs beállításait mutatják:

#### <a name="supported-options"></a>Támogatott beállítások

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Nem támogatott beállítások

- build (nem engedélyezett)
- depends_on (figyelmen kívül hagyva)
- networks (figyelmen kívül hagyva)
- secrets (figyelmen kívül hagyva)
- portok nem 80 és 8080 (figyelmen kívül hagyva)

> [!NOTE]
> A nyilvános előzetes verzió figyelmen kívül hagyja a kifejezetten nem kifejezetten megnevezett egyéb beállításokat.

## <a name="configure-vnet-integration"></a>Virtuálishálózat-integráció konfigurálása

Virtuálishálózat-integrációval rendelkező egyéni tároló használata további tárolókonfigurációt igényelhet. Lásd: [Az alkalmazás integrálása egy Azure virtuális hálózattal.](../web-sites-integrate-with-vnet.md)

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Üzembe helyezés a privát tárolótárból](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Bemutató: Többtárolós WordPress alkalmazás](tutorial-multi-container-app.md)
