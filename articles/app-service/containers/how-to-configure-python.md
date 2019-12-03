---
title: Linux Python-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy előre elkészített Python-tárolót az alkalmazásához. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: b8de6df5761baef79310062614f578a92f17b826
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670484"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Linux Python-alkalmazás konfigurálása a Azure App Servicehoz

Ez a cikk leírja, hogyan futtatja [Azure app Service](app-service-linux-intro.md) a Python-alkalmazásokat, és hogyan szabhatja testre a app Service viselkedését, ha szükséges. A Python-alkalmazásokat az összes szükséges [pip](https://pypi.org/project/pip/) -modullal telepíteni kell.

A App Service üzembe helyezési motor automatikusan aktiválja a virtuális környezetet, és futtatja `pip install -r requirements.txt` a git- [tárház](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)telepítésekor, vagy egy olyan [ZIP-csomagot](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) , amelyen a Build-folyamatok be lettek kapcsolva.

Ez az útmutató ismerteti a Python-fejlesztők számára a App Service beépített Linux-tárolóját használó főbb fogalmakat és útmutatást. Ha még soha nem használta a Azure App Servicet, először kövesse a [Python](quickstart-python.md) rövid [útmutatóját és a Pythont a PostgreSQL oktatóanyaggal](tutorial-python-postgresql-app.md) .

> [!NOTE]
> A Linux jelenleg az ajánlott lehetőség a Python-alkalmazások App Service-ben való futtatására. További információ a Windows lehetőségről: [Python a app Service Windows-íz](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Python-verzió megjelenítése

Az aktuális Python-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Python-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

A Python nem támogatott verzióját a saját tároló rendszerképének létrehozásával futtathatja. További információ: [Egyéni Docker-rendszerkép használata](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Python-verzió beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a Python-verzió 3,7-re való beállításához:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>A tároló jellemzői

A Linuxon App Service rendszerre telepített Python-alkalmazások a GitHub-tárházban, a [python 3,6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) -ban vagy a [Python 3,7](https://github.com/Azure-App-Service/python/tree/master/3.7.0)-ben meghatározott Docker-tárolón belül futnak.

Ez a tároló a következő jellemzőkkel rendelkezik:

- Az alkalmazások a [Gunicorn WSGI HTTP-kiszolgáló](https://gunicorn.org/) és további argumentumok használatával futnak`--bind=0.0.0.0 --timeout 600`.

- Alapértelmezés szerint az alaprendszerkép magában foglalja a Flask webes keretrendszert, de a tároló a WSGI-vel és a Python 3.7-es verziójával kompatibilis keretrendszereket is támogat, így például a Djangót.

- További csomagok, mint például a Django, telepítéséhez hozzon létre egy [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) fájlt a projekt gyökérkönyvtárában a `pip freeze > requirements.txt` parancs használatával. Ezt követően a Git üzemelő példányával tegye közzé projektjét az App Service-be, amely az alkalmazásfüggőségek telepítéséhez automatikusan futtatja a tárolóban a `pip install -r requirements.txt` parancsot.

## <a name="container-startup-process"></a>Tároló indítási folyamata

Rendszerindítás során a Linux-tárolóban lévő App Service a következő lépéseket futtatja:

1. Ha meg van adni, használjon [Egyéni indítási parancsot](#customize-startup-command).
2. Ellenőrizze, hogy létezik-e [Django alkalmazás](#django-app), és ha észlelte, indítsa el a Gunicorn.
3. Ellenőrizze, hogy létezik-e a [lombik-alkalmazás](#flask-app), és ha észlelt, indítsa el a Gunicorn.
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

A lombik esetében App Service egy *Application.py* vagy *app.py* nevű fájlt keres, és elindítja a Gunicorn a következőképpen:

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

## <a name="customize-startup-command"></a>Indítási parancs testreszabása

A tároló indítási viselkedését egy egyéni Gunicorn indítási parancs megadásával szabályozhatja. Ehhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Ha például van egy olyan lombik-alkalmazás, amelynek a fő modulja a *Hello.py* , és az abban a fájlban található lombik alkalmazás-objektum neve `myapp`, akkor *\<custom-Command >* a következő:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Ha a főmodul egy almappában található (például `website`), a mappát a `--chdir` argumentummal határozhatja meg:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

A Gunicorn további argumentumait is hozzáadhatja *\<egyéni parancs >* , például `--workers=4`. További információkért lásd: [A Gunicorn futtatása](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Ha nem Gunicorn-kiszolgálót (például [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)) szeretne használni, az alábbihoz hasonló módon lecserélheti *\<custom-Command >* :

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> Az App Service figyelmen kívül hagyja az egyéni parancsfájl feldolgozásakor előforduló hibákat, majd az indítási folyamat következő lépéseként megkeresi a Django- és a Flask-alkalmazásokat. Ha nem várt viselkedést tapasztal, ellenőrizze, hogy az indítási fájl telepítve lett-e az App Service-be, és hogy az nem tartalmaz-e hibákat.

## <a name="access-environment-variables"></a>Hozzáférési környezeti változók

App Service az [Alkalmazásbeállítások](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezt követően a szabványos [operációs rendszer. Enviro](https://docs.python.org/3/library/os.html#os.environ) minta használatával férhet hozzájuk. Ha például egy `WEBSITE_SITE_NAME`nevű alkalmazás-beállítást szeretne elérni, használja a következő kódot:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécét.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

A népszerű webes keretrendszerek lehetővé teszik a szabványos alkalmazási mintában lévő `X-Forwarded-*` információk elérését. A [CodeIgniter](https://codeigniter.com/)-ben a [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi `X_FORWARDED_PROTO` értékét.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibakeresés

- **Saját alkalmazáskódjának telepítése után megjelenik az alapértelmezett alkalmazás.** Az alapértelmezett alkalmazás jelenik meg, mert vagy nem telepítette az alkalmazás kódját App Servicere, vagy App Service nem találta meg az alkalmazás kódját, és nem futtatta az alapértelmezett alkalmazást.
- Indítsa újra az App Service-t, várjon 15-20 másodpercet, és ellenőrizze újra az alkalmazást.
- Bizonyosodjon meg róla, hogy az App Service Linux- és nem Windows-alapú példányát használja. Az Azure CLI parancssori felületről futtassa az `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` parancsot, a `<resource_group_name>` és az `<app_service_name>` helyőrzőket megfelelően behelyettesítve. Az `app,linux` kimenetet kell látnia, máskülönben hozza újra létre az App Service szolgáltatást, és válassza a linuxos verziót.
- SSH- vagy a Kudu konzol használatával közvetlenül csatlakozzon az App Service-hez, és győződjön meg arról, hogy a fájlok léteznek a *site/wwwroot* könyvtárban. Ha a fájlok nem léteznek, tekintse át a telepítési folyamatot, és telepítse újra az alkalmazást.
- Ha a fájlok léteznek, az App Service nem tudta azonosítani az adott indítási fájlt. Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).
- **A böngészőben megjelenik „A szolgáltatás nem érhető el” üzenet.** A böngésző az App Service válaszára vára túllépte az időkorlátot, ami azt jelzi, hogy az App Service elindította a Gunicorn-kiszolgálót, de az alkalmazás kódját meghatározó argumentumok helytelenek.
- Frissítse a böngészőt, különösen akkor, ha az App Service-csomag legalacsonyabb tarifacsomagját használja. Az ingyenes szolgáltatásszintek használatakor például az alkalmazás lassabban indul, és csak a böngésző frissítése után fog ismét reagálni.
- Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).
- [A log stream elérése](#access-diagnostic-logs).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: üzembe helyezés Private Container adattárból](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [App Service Linux – gyakori kérdések](app-service-linux-faq.md)
