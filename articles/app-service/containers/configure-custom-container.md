---
title: Ügyfél tárolókat – az Azure App Service konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan működik az Azure App Service Node.js-alkalmazások konfigurálása
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 7f850cdfe99fce81c9be045b4882dc42bf2aa5f0
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551097"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Egyéni Linux-tárolóban konfigurálása az Azure App Service-ben

Ez a cikk bemutatja, hogyan futtathatók az Azure App Service egyéni Linux-tárolóban konfigurálása.

Ez az útmutató a főbb fogalmakat és a Linux-alkalmazások App Service-ben bontás vonatkozó utasításokat tartalmaz. Ha korábban nem használta az Azure App Service, kövesse a [egyéni tároló rövid](quickstart-docker-go.md) és [oktatóanyag](tutorial-custom-docker-image.md) első. Emellett van egy [többtárolós alkalmazás – rövid útmutató](quickstart-multi-container.md) és [oktatóanyag](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Portszám konfigurálása

A webkiszolgálót az egyéni rendszerkép az előfordulhat, hogy a nem a 80-as portot használja. A port, amelyet az egyéni használ használatával kapcsolatos utasítja az Azure a `WEBSITES_PORT` alkalmazásbeállítást. A [jelen oktatóanyagban lévő Python-mintához](https://github.com/Azure-Samples/docker-django-webapp-linux) tartozó GitHub-oldalon az látható, hogy a `WEBSITES_PORT` értékét _8000_-re kell állítani. Beállíthatja a futó [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Példa:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az egyéni tároló felhasználhatja külsőleg megadása szükséges környezeti változókat. Továbbíthatja azokat futtatásával [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Példa:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Ez a módszer használható mind egy tároló vagy a többtárolós alkalmazásokat, ahol a környezeti változók vannak megadva a *docker-compose.yml* fájlt.

## <a name="use-persistent-shared-storage"></a>Állandó megosztott tárolók használatához

Használhatja a */home* könyvtárat az alkalmazás fájlrendszerében fájlok megőrzése újraindítások között, és megoszthatja őket a példányok között. A `/home` az alkalmazásban megadott lehetővé teszik a tároló alkalmazás állandó tároló eléréséhez.

Amikor az állandó tároló le van tiltva, majd kiírja a `/home` directory alkalmazás-újraindítások között vagy több példánya között nem maradnak meg. Az egyetlen kivétel a `/home/LogFiles` könyvtár, amely a Docker és a tároló naplóinak tárolására szolgál. Ha engedélyezve van-e az állandó tárolóból, az összes írási művelet a `/home` directory megmaradnak, és a egy kiterjesztett alkalmazás összes példánya hozzáférhet.

Alapértelmezés szerint az állandó tárolóból *le van tiltva*. Engedélyezze vagy tiltsa le, állítsa be a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` Alkalmazásbeállítás futtatásával [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Példa:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> Emellett [konfigurálása az állandó tárolóban](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Az SSH engedélyezéséhez

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ahhoz, hogy egy egyéni tároló SSH támogatásához hozzá kell adnia azokat a docker-fájlban.

> [!TIP]
> Beépített Linux-tárolók hozzáadta az SSH-utasítások a lemezkép-tárházak esetében. Az alábbi utasításokat a végigveheti a [Node.js 10.14 tárház](https://github.com/Azure-App-Service/node/blob/master/10.14) megtekintéséhez, hogyan engedélyezett van.

- Használja a [futtatása](https://docs.docker.com/engine/reference/builder/#run) utasítás az SSH-kiszolgáló telepítése és beállítása a root fiók jelszava `"Docker!"`. Például egy gépen alapuló rendszerképet [Alpine Linux](https://hub.docker.com/_/alpine), van szüksége a következő parancsokat:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Ez a konfiguráció nem teszi lehetővé külső kapcsolatok létesítését a tárolóval. Az SSH csak keresztül érhető el `https://<app-name>.scm.azurewebsites.net` hitelesítés szükséges a közzétételi hitelesítő adatokkal.

- Adja hozzá [ezen az sshd_config fájlon](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) a lemezképtárban, és használja a [MÁSOLÁSI](https://docs.docker.com/engine/reference/builder/#copy) utasítást a fájl átmásolása a */etc/ssh/* könyvtár. További információ *sshd_config* fájlok, lásd: [OpenBSD dokumentáció](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Az *sshd_config* fájlnak tartalmaznia kell a következő elemeket:
    > - A `Ciphers` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - A `MACs` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `hmac-sha1,hmac-sha1-96`.

- Használja a [ELÉRHETŐVÉ](https://docs.docker.com/engine/reference/builder/#expose) utasítás a tároló 2222-es port megnyitásához. Bár a legfelső szintű jelszó ismert, a 2222-es port nem érhető el az internetről. Érhető el csak egy privát virtuális hálózat hálózati hidas kapcsolatán belüli tárolók által.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- A tároló indítási parancsfájlban indítsa el az SSH-kiszolgálót.

    ```bash
    /usr/sbin/sshd
    ```

    Egy vonatkozó példáért lásd: hogyan az alapértelmezett [Node.js 10.14 tároló](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) elindítja az SSH-kiszolgálót.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Többtárolós alkalmazások konfigurálása

- [Tartós tároláshoz használjon a Docker Compose](#use-persistent-storage-in-docker-compose)
- [Előzetes verzió korlátozásai](#preview-limitations)
- [A docker Compose-beállítások](#docker-compose-options)
- [Kubernetes konfigurációs lehetőségek](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Tartós tároláshoz használjon a Docker Compose

Többtárolós alkalmazások, mint például a WordPress adattárolásra megfelelő működéséhez szükséges. Az engedélyezéshez, a Docker Compose-konfigurációját a tárhelyet kell mutatnia *kívül* a tárolót. A tárolóhelyek tárolón belüli változtatások túl az alkalmazás újraindítása nem megőrzéséhez.

Állandó tároló módosításával engedélyezheti a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` alkalmazás beállítását, használja a [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs Cloud shellben.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Az a *docker-compose.yml* fájlt, és leképezheti a `volumes` beállítást `${WEBAPP_STORAGE_HOME}`. A `WEBAPP_STORAGE_HOME` egy környezeti változó az App Service szolgáltatásban, amely az alkalmazás állandó tárolójára mutat. Példa:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Többtárolós jelenleg előzetes verzióban érhető el. A következő App Service platform funkciók nem támogatottak:

- Hitelesítés/engedélyezés
- Felügyelt identitások

### <a name="docker-compose-options"></a>A docker Compose-beállítások

A következő listákban támogatott és nem támogatott a Docker Compose konfigurációs beállítások megjelenítése:

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
- 80-as és 8080-as (azaz a rendszer figyelmen kívül hagyja)-astól különböző portok

> [!NOTE]
> Nem kifejezetten feltüntettük az egyéb beállításokat figyelmen kívül hagyja a nyilvános előzetes verzióban érhető el.

### <a name="kubernetes-configuration-options"></a>Kubernetes konfigurációs lehetőségek

Az alábbi konfigurációs lehetőségeket a Kubernetes támogatja:

- args
- command
- tárolók
- image
- név
- ports
- spec

> [!NOTE]
> Az egyéb beállításokat nem kifejezetten emelte ki a nyilvános előzetes verzióban nem támogatottak.
>

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Üzembe helyezés a privát tárház](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Többtárolós WordPress-alkalmazás](tutorial-multi-container-app.md)
