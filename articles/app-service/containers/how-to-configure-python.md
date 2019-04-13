---
title: Python-alkalmazások – az Azure App Service konfigurálása
description: Ez az oktatóanyag a Python-alkalmazások szerzői műveleteit és konfigurálási lehetőségeit ismerteti az Azure App Service-hez Linux rendszeren.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: f8894132dae179be2d5d9d9b6887851be78d7746
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548145"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Az Azure App Service Linux Python-alkalmazás konfigurálása

Ez a cikk azt ismerteti, hogyan [Azure App Service](app-service-linux-intro.md) fut, a Python-alkalmazásokat, és hogyan szabhatja testre a szükség esetén az App Service viselkedését. Python-alkalmazásokat telepíteni kell az összes szükséges [pip](https://pypi.org/project/pip/) modulok.

Az App Service üzembe helyezési motorban automatikusan aktiválja a virtuális környezetet, és futtatja `pip install -r requirements.txt` , ha telepít egy [Git-tárház](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), vagy egy [Zip-csomagját](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) az összeállítási folyamatairól be van kapcsolva.

Ez az útmutató a főbb fogalmakat és az App Service-ben egy beépített Linux-tárolót használó Python-fejlesztőknek utasításokat tartalmaz. Ha korábban nem használta az Azure App Service, kövesse a [Python rövid](quickstart-python.md) és [Python és PostgreSQL-oktatóanyag](tutorial-python-postgresql-app.md) első.

> [!NOTE]
> Linux jelenleg a javasolt megoldás a Python-alkalmazással futó App Service-ben. Információk a Windows esetén: [a Windows íz az App Service a Python](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Python-verzió megjelenítése

A jelenlegi Python-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Összes támogatott Python-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Ehelyett saját tárolórendszerkép létrehozásával futtathatja a Python nem támogatott verzióját. További információkért lásd: [egyéni Docker-rendszerkép használata](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Python-verzió beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) 3.7-es verzióját, a Python-verzió beállítása:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>A tároló jellemzői

Python-alkalmazások üzembe helyezését futtathatók Docker-tároló a GitHub-adattár definiált Linuxon futó App Service [Python 3.6-os](https://github.com/Azure-App-Service/python/tree/master/3.6.6) vagy [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Ez a tároló a következő jellemzőkkel rendelkezik:

- Az alkalmazások a [Gunicorn WSGI HTTP-kiszolgáló](https://gunicorn.org/) és további argumentumok használatával futnak`--bind=0.0.0.0 --timeout 600`.

- Alapértelmezés szerint az alaprendszerkép magában foglalja a Flask webes keretrendszert, de a tároló a WSGI-vel és a Python 3.7-es verziójával kompatibilis keretrendszereket is támogat, így például a Djangót.

- További csomagok, mint például a Django, telepítéséhez hozzon létre egy [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) fájlt a projekt gyökérkönyvtárában a `pip freeze > requirements.txt` parancs használatával. Ezt követően a Git üzemelő példányával tegye közzé projektjét az App Service-be, amely az alkalmazásfüggőségek telepítéséhez automatikusan futtatja a tárolóban a `pip install -r requirements.txt` parancsot.

## <a name="container-startup-process"></a>Tároló indítási folyamat

Rendszerindítás során a Linux-tárolóban lévő App Service a következő lépéseket futtatja:

1. Használja a [egyéni indítási parancs](#customize-startup-command), ha meg van adva.
2. Létezik-e keresni egy [Django-alkalmazás](#django-app), és indítsa el a Gunicorn, ha észleli.
3. Létezik-e keresni egy [Flask-alkalmazás](#flask-app), és indítsa el a Gunicorn, ha észleli.
4. Ha más alkalmazás nem található, indítson el egy alapértelmezett alkalmazást, amely a tárolóba van beépítve.

A következő szakaszok további információkkal szolgálnak az egyes beállításokról.

### <a name="django-app"></a>Django-alkalmazás

Django-alkalmazások esetén az App Service a(z) `wsgi.py` nevű fájlt keresi az alkalmazáskódban, majd a Gunicorn futtatásához a következő parancsot használja:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Ha az indítási parancsot szeretné jobban szabályozni, használjon egy [egyéni indítási parancsot](#customize-startup-command), és cserélje le a(z) `<module>` modult annak a modulnak a nevével, amely tartalmazza a *wsgi.py* fájlt.

### <a name="flask-app"></a>Flask-alkalmazás

Flask, az App Service nevű fájlt keresi *application.py* vagy *app.py* és Gunicorn elindítja a következő:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Ha a fő alkalmazásmodul egy másik fájlban található, használjon másik nevet az alkalmazásobjektum számára, vagy ha további argumentumok szeretne megadni a Gunicornhoz, használjon egy [egyéni indítási parancsot](#customize-startup-command).

### <a name="default-behavior"></a>Alapértelmezett viselkedés

Ha az App Service nem talál egyéni parancsot, vagy Django-, illetve Flask-alkalmazást, akkor egy alapértelmezett csak olvasható alkalmazást futtat, amely az _opt/defaultsite_ mappában található. Az alapértelmezett alkalmazás a következőképpen jelenik meg:

![Alapértelmezett App Service a Linux webhelyen](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>Az indítási parancs testreszabása

A tároló indítási viselkedését egy egyéni Gunicorn indítási parancs megadásával szabályozhatja. Ehhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Például, ha már olyan Flask-alkalmazás amelynek fő modul, *hello.py* és a Flask-alkalmazás objektum, az adott fájlban nevű `myapp`, majd  *\<egyéni-parancs >* a következő:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Ha a főmodul egy almappában található (például `website`), a mappát a `--chdir` argumentummal határozhatja meg:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Azt is megteheti, Gunicorn további argumentumokat  *\<egyéni-parancs >*, például `--workers=4`. További információkért lásd: [A Gunicorn futtatása](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Egy nem Gunicorn server használandó [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), lecserélheti  *\<egyéni-parancs >* , a következőhöz hasonló:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> Az App Service figyelmen kívül hagyja az egyéni parancsfájl feldolgozásakor előforduló hibákat, majd az indítási folyamat következő lépéseként megkeresi a Django- és a Flask-alkalmazásokat. Ha nem várt viselkedést tapasztal, ellenőrizze, hogy az indítási fájl telepítve lett-e az App Service-be, és hogy az nem tartalmaz-e hibákat.

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben is [állítsa be az alkalmazásbeállításokat](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) kívül a kódját. Ezután elérheti azokat a standard használatával [os.environ](https://docs.python.org/3/library/os.html#os.environ) mintát. Például egy alkalmazás-beállítás eléréséhez nevű `WEBSITE_SITE_NAME`, a következő kóddal:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Észleli a HTTPS-KAPCSOLATON keresztül

Az App Service-ben [SSL-lezárást](https://wikipedia.org/wiki/TLS_termination_proxy) történik, ha a hálózati terheléselosztók, így az összes HTTPS-kérelmek elérni az alkalmazás nem titkosított HTTP-kérések. Ha az alkalmazás logikai igényeinek megfelelően, ellenőrizze, hogy ha a felhasználói kérelmek titkosítottak-e vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejléc.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Népszerű webes keretrendszerek, hozzáférést biztosítanak a `X-Forwarded-*` a szabványos mintában információkat. A [CodeIgniter](https://codeigniter.com/), a [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) értékét ellenőrzi `X_FORWARDED_PROTO` alapértelmezés szerint.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Nyissa meg böngészőben SSH-munkamenet

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

- **Saját alkalmazáskódjának telepítése után megjelenik az alapértelmezett alkalmazás.** Az alapértelmezett alkalmazás jelenik meg, vagy még nem telepítette a kód App Service-ben, vagy hogy App Service-ben nem sikerült megtalálni a kód és az alapértelmezett alkalmazás ehelyett futott.
- Indítsa újra az App Service-t, várjon 15-20 másodpercet, és ellenőrizze újra az alkalmazást.
- Bizonyosodjon meg róla, hogy az App Service Linux- és nem Windows-alapú példányát használja. Az Azure CLI parancssori felületről futtassa az `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` parancsot, a `<resource_group_name>` és az `<app_service_name>` helyőrzőket megfelelően behelyettesítve. Az `app,linux` kimenetet kell látnia, máskülönben hozza újra létre az App Service szolgáltatást, és válassza a linuxos verziót.
- SSH- vagy a Kudu konzol használatával közvetlenül csatlakozzon az App Service-hez, és győződjön meg arról, hogy a fájlok léteznek a *site/wwwroot* könyvtárban. Ha a fájlok nem léteznek, tekintse át a telepítési folyamatot, és telepítse újra az alkalmazást.
- Ha a fájlok léteznek, az App Service nem tudta azonosítani az adott indítási fájlt. Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).
- **A böngészőben megjelenik „A szolgáltatás nem érhető el” üzenet.** A böngésző az App Service válaszára vára túllépte az időkorlátot, ami azt jelzi, hogy az App Service elindította a Gunicorn-kiszolgálót, de az alkalmazás kódját meghatározó argumentumok helytelenek.
- Frissítse a böngészőt, különösen akkor, ha az App Service-csomag legalacsonyabb tarifacsomagját használja. Az ingyenes szolgáltatásszintek használatakor például az alkalmazás lassabban indul, és csak a böngésző frissítése után fog ismét reagálni.
- Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).
- [A naplózási adatfolyam eléréséhez](#access-diagnostic-logs).

## <a name="next-steps"></a>További lépések

A beépített Python-rendszerkép a linuxon futó App Service-ben jelenleg előzetes verzióban érhető el, és testre szabhatja a parancsot az alkalmazás indításához. Éles Python-alkalmazásokat egyéni tároló használatával is létrehozhat.

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Üzembe helyezés a privát tárház](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)