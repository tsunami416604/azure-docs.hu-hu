---
title: Egyéni Linux-tároló konfigurálása
description: Megtudhatja, hogyan konfigurálhat egyéni Linux-tárolókat a Azure App Serviceban. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 2f26f1b041b2d369b68aeb11755c8e8053862b16
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083018"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Egyéni Linux-tároló konfigurálása Azure App Servicehoz

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy egyéni Linux-tárolót, hogy Azure App Serviceon fusson.

Ez az útmutató a Linux-alkalmazások App Service-ben történő tárolókra bontás kapcsolatos főbb fogalmakat és útmutatásokat tartalmazza. Ha még soha nem használta Azure App Servicet, először kövesse az [Egyéni tároló](quickstart-custom-container.md?pivots=container-linux) rövid [útmutatóját és az oktatóanyagot](tutorial-custom-container.md?pivots=container-linux) . A [multi-Container app](quickstart-multi-container.md) rövid [útmutatója és oktatóanyaga](tutorial-multi-container-app.md)is rendelkezésre áll.

## <a name="configure-port-number"></a>Portszám konfigurálása

Alapértelmezés szerint a App Service feltételezi, hogy az egyéni tároló figyeli a 80-es portot. Az egyéni rendszerképben található webkiszolgáló a 80-től eltérő portot is használhat. Tájékoztassa az Azure-t arról, hogy az egyéni tároló milyen portot használ az Alkalmazásbeállítások használatával `WEBSITES_PORT` . A [jelen oktatóanyagban lévő Python-mintához](https://github.com/Azure-Samples/docker-django-webapp-linux) tartozó GitHub-oldalon az látható, hogy a `WEBSITES_PORT` értékét _8000_-re kell állítani. Megadhatja [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) a parancs futtatásával a Cloud shell. Például:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az egyéni tároló olyan környezeti változókat használhat, amelyeket külsőleg kell megadni. Átadhatja azokat a parancs futtatásával [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) a Cloud shell. Például:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Ez a módszer egytárolós alkalmazások vagy többtárolós alkalmazások esetén is működik, ahol a környezeti változók a *Docker-compose. YML* fájlban vannak megadva.

## <a name="use-persistent-shared-storage"></a>Állandó megosztott tároló használata

Az alkalmazás fájlrendszerében a */Home* Directory használatával megtarthatja a fájlokat az újraindítások között, és megoszthatja azokat a példányok között. Az `/home` alkalmazásban elérhetővé teszi a tároló alkalmazás számára az állandó tárterület elérését.

Ha az állandó tárterület le van tiltva, akkor a rendszer a címtárba való írást `/home` nem őrzi meg az alkalmazások újraindítása vagy több példánya között. Az egyetlen kivétel az a `/home/LogFiles` könyvtár, amely a Docker és a tároló naplóinak tárolására szolgál. Ha az állandó tárterület engedélyezve van, a címtárba való összes írás megmarad, és a kibővített `/home` alkalmazás összes példánya elérhetővé válik.

Alapértelmezés szerint az állandó tárterület *engedélyezve* van, és a beállítás nem érhető el az alkalmazás beállításaiban. A letiltásához állítsa az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` alkalmazás beállítását a [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) Cloud Shell parancs futtatásával. Például:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> [Saját állandó tárterületet is beállíthat](configure-connect-to-azure-storage.md?pivots=platform-linux).

## <a name="enable-ssh"></a>SSH engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ahhoz, hogy egy egyéni tároló támogassa az SSH-t, fel kell vennie azt a Docker.

> [!TIP]
> Az összes beépített Linux-tároló hozzá lett adva az SSH-utasításokhoz a rendszerkép-tárházban. A [Node.js 10,14 adattárral](https://github.com/Azure-App-Service/node/blob/master/10.14) az alábbi utasításokat követve megtekintheti, hogyan engedélyezhető ott.

- A [futtatási](https://docs.docker.com/engine/reference/builder/#run) utasítás használatával telepítse az SSH-kiszolgálót, és állítsa be a rendszergazdai fiók jelszavát a következőre: `"Docker!"` . Az [alpesi Linux](https://hub.docker.com/_/alpine)-alapú rendszerképekhez például a következő parancsokat kell megadnia:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Ez a konfiguráció nem engedélyezi a külső kapcsolatokat a tárolóval. Az SSH csak `https://<app-name>.scm.azurewebsites.net` a és a közzétételi hitelesítő adatokkal való hitelesítéssel érhető el.

- Adja hozzá [ezt a sshd_config fájlt](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) a rendszerkép-tárházhoz, és a [másolási](https://docs.docker.com/engine/reference/builder/#copy) utasítás használatával másolja a fájlt a */etc/ssh/* könyvtárba. *Sshd_config* fájlokról további információt az [OpenBSD dokumentációjában](https://man.openbsd.org/sshd_config)talál.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Az *sshd_config* fájlnak tartalmaznia kell a következő elemeket:
    > - A `Ciphers` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - A `MACs` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `hmac-sha1,hmac-sha1-96`.

- A következő [utasítás használatával](https://docs.docker.com/engine/reference/builder/#expose) nyissa meg a tárolóban az 2222-es portot. Bár a gyökér jelszava ismert, a 2222-es port nem érhető el az internetről. A szolgáltatás csak a privát virtuális hálózat híd hálózatán lévő tárolók számára érhető el.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- A tároló indítási parancsfájljában indítsa el az SSH-kiszolgálót.

    ```bash
    /usr/sbin/sshd
    ```

    Példaként tekintse meg, hogy az alapértelmezett [Node.js 10,14 tároló](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) hogyan indítja el az SSH-kiszolgálót.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="change-the-docker-image-of-a-custom-container"></a>Egyéni tároló Docker-rendszerképének módosítása

Ha módosítani szeretné egy meglévő egyéni tároló alkalmazást a jelenlegi Docker-rendszerképből egy új képre, használja a következő parancsot:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Rendszerkép használata privát beállításjegyzékből

Ha privát beállításjegyzékből (például Azure Container Registry) szeretne képet használni, futtassa a következő parancsot:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

A *\<username>* és a esetében *\<password>* adja meg a saját beállításjegyzék-fiókja bejelentkezési hitelesítő adatait.

## <a name="configure-multi-container-apps"></a>Többtárolós alkalmazások konfigurálása

- [Állandó tároló használata a Docker-összeállításban](#use-persistent-storage-in-docker-compose)
- [Előzetes verzió korlátozásai](#preview-limitations)
- [Docker-összeállítás beállításai](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Állandó tároló használata a Docker-összeállításban

A többtárolós alkalmazások, például a WordPress esetében állandó tárterületre van szükség a megfelelő működéshez. Az engedélyezéshez a Docker-összeállítás konfigurációjának a tárolón *kívüli* tárolási helyre kell mutatnia. A tárolón belüli tárolóhelyek nem tartanak fenn módosításokat az alkalmazás újraindítása után.

Engedélyezze az állandó tárterületet az Alkalmazásbeállítások beállításával az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs használatával Cloud Shellban.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

A *Docker-compose. YML* fájlban rendelje hozzá a ( `volumes` `${WEBAPP_STORAGE_HOME}` ) beállítást. 

A `WEBAPP_STORAGE_HOME` egy környezeti változó az App Service szolgáltatásban, amely az alkalmazás állandó tárolójára mutat. Például:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Előzetes verzió korlátozásai

A multi-Container jelenleg előzetes verzióban érhető el. A következő App Service platform-funkciók nem támogatottak:

- Hitelesítés/engedélyezés
- Felügyelt identitások

### <a name="docker-compose-options"></a>Docker-összeállítás beállításai

Az alábbi listában a támogatott és nem támogatott Docker-összeállítási beállítások láthatók:

#### <a name="supported-options"></a>Támogatott beállítások

- command
- entrypoint
- környezet
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
- 80 és 8080 közötti (figyelmen kívül hagyott) portok

> [!NOTE]
> A rendszer figyelmen kívül hagyja a nem kifejezetten kinevezett egyéb beállításokat a nyilvános előzetes verzióban.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: üzembe helyezés Private Container adattárból](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Oktatóanyag: Multi-Container WordPress-alkalmazás](tutorial-multi-container-app.md)
