---
title: Linux Python-alkalmazások konfigurálása
description: Ismerje meg, hogyan konfigurálhat egy előre elkészített Python-tárolót az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat mutatja be.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8a9276f73c1d9bdf0289f41bb59340b29f5a2575
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046026"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Linux Python-alkalmazás konfigurálása az Azure App Service-hez

Ez a cikk ismerteti, hogyan fut [az Azure App Service](app-service-linux-intro.md) Python-alkalmazások, és hogyan szabhatja testre az App Service viselkedését, ha szükséges. A Python-alkalmazásokat az összes szükséges [pip](https://pypi.org/project/pip/) modullal kell telepíteni.

Az App Service központi telepítési motorja `pip install -r requirements.txt` automatikusan aktiválja a virtuális környezetet, és fut az Ön számára, amikor telepíti a [Git-tárházat](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), vagy egy [Zip-csomagot,](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) amelybe be van kapcsolva a buildfolyamatok.

Ez az útmutató kulcsfontosságú fogalmakat és utasításokat tartalmaz a Python-fejlesztők számára, akik egy beépített Linux-tárolót használnak az App Service-ben. Ha még soha nem használta az Azure App Service- t, először kövesse a [Python gyorsindítást](quickstart-python.md) és a [Pythont a PostgreSQL oktatóanyaggal.](tutorial-python-postgresql-app.md)

> [!NOTE]
> A Linux jelenleg az ajánlott lehetőség a Python-alkalmazások futtatásához az App Service-ben. A Windows beállításról további információt [az App Service Windows-változatáról](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service)szóló Python című témakörben talál.
>

## <a name="show-python-version"></a>Python-verzió megjelenítése

Az aktuális Python-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Python-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Futtathatja a Python nem támogatott verzióját a saját tárolórendszerkép létrehozásával. További információ: [Egyéni Docker-lemezkép használata.](tutorial-custom-docker-image.md)

## <a name="set-python-version"></a>Python-verzió beállítása

Futtassa a következő parancsot a [Cloud Shellben](https://shell.azure.com) a Python-verzió 3.7-es beállításához:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>Build-automatizálás testreszabása

Ha git- vagy zip-csomagok használatával telepíti az alkalmazást, és be van kapcsolva az alkalmazás, az App Service a következő sorrendben építi fel az automatizálási lépéseket:

1. Ha a program ezt `PRE_BUILD_SCRIPT_PATH`a.
1. Futtassa az `pip install -r requirements.txt` parancsot.
1. Ha *manage.py* található a tárház gyökerében, futtassa *manage.py collectstatic*. Ha azonban `DISABLE_COLLECTSTATIC` a `true`beállítás a beállítás, akkor ezt a lépést a program kihagyja.
1. Ha a program ezt `POST_BUILD_SCRIPT_PATH`a.

`PRE_BUILD_COMMAND`, `POST_BUILD_COMMAND`és `DISABLE_COLLECTSTATIC` olyan környezeti változók, amelyek alapértelmezés szerint üresek. Az előbuild parancsok futtatásához definiálja a programot. `PRE_BUILD_COMMAND` A létrehozás utáni parancsok `POST_BUILD_COMMAND`futtatásához definiálja a programot. A Django-alkalmazások létrehozásakor a `DISABLE_COLLECTSTATIC=true`futó collectstatic letiltásához állítsa be a beállítását.

A következő példa a két változót egy parancssorozatra adja meg, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

A buildautomatizálás testreszabásához további környezeti változókat az [Oryx konfigurációja](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)című témakörben tava látható.

Az App Service Linuxon belüli python-alkalmazások futásáról és felépítéséről az [Oryx dokumentációja: A Python-alkalmazások észlelése és létrehozása](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)című témakörben olvashat bővebben.

## <a name="container-characteristics"></a>A tároló jellemzői

Az App Service-ben Linuxon üzembe helyezett Python-alkalmazások az [App Service Python GitHub-tárházban](https://github.com/Azure-App-Service/python)definiált Docker-tárolóban futnak. A verzióspecifikus könyvtárakban található a lemezkép-konfigurációk.

Ez a tároló a következő jellemzőkkel rendelkezik:

- Az alkalmazások a [Gunicorn WSGI HTTP-kiszolgáló](https://gunicorn.org/) és további argumentumok használatával futnak`--bind=0.0.0.0 --timeout 600`.

- Alapértelmezés szerint az alaprendszerkép magában foglalja a Flask webes keretrendszert, de a tároló a WSGI-vel és a Python 3.7-es verziójával kompatibilis keretrendszereket is támogat, így például a Djangót.

- További csomagok, mint például a Django, telepítéséhez hozzon létre egy [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) fájlt a projekt gyökérkönyvtárában a `pip freeze > requirements.txt` parancs használatával. Ezt követően a Git üzemelő példányával tegye közzé projektjét az App Service-be, amely az alkalmazásfüggőségek telepítéséhez automatikusan futtatja a tárolóban a `pip install -r requirements.txt` parancsot.

## <a name="container-startup-process"></a>A tároló indítási folyamata

Rendszerindítás során a Linux-tárolóban lévő App Service a következő lépéseket futtatja:

1. Ha van ilyen, [használjon egyéni indítási parancsot.](#customize-startup-command)
2. Ellenőrizze, hogy létezik-e [Django alkalmazás,](#django-app)és indítsa el a Gunicorn alkalmazást, ha észleli.
3. Ellenőrizze, hogy létezik-e [flaska alkalmazás](#flask-app), és indítsa el a Gunicorn-t, ha észleli.
4. Ha más alkalmazás nem található, indítson el egy alapértelmezett alkalmazást, amely a tárolóba van beépítve.

A következő szakaszok további információkkal szolgálnak az egyes beállításokról.

### <a name="django-app"></a>Django-alkalmazás

Django-alkalmazások esetén az App Service a(z) `wsgi.py` nevű fájlt keresi az alkalmazáskódban, majd a Gunicorn futtatásához a következő parancsot használja:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Ha az indítási parancsot szeretné jobban szabályozni, használjon egy [ egyéni indítási parancsot](#customize-startup-command), és cserélje le a(z) `<module>` modult annak a modulnak a nevével, amely tartalmazza a *wsgi.py* fájlt.

### <a name="flask-app"></a>Flask-alkalmazás

Flask esetén az App Service egy *application.py* vagy *app.py* nevű fájlt keres, és a Gunicorn-t az alábbiak szerint indítja el:

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

A tároló indítási viselkedését egy egyéni Gunicorn indítási parancs megadásával szabályozhatja. Ehhez futaaakövetkező parancsot a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Ha például van egy Flask alkalmazás, amelynek fő modulja *hello.py* és `myapp`a lombik alkalmazás objektum a fájlban neve, majd * \<az egyéni parancs>* a következő:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Ha a főmodul egy almappában található (például `website`), a mappát a `--chdir` argumentummal határozhatja meg:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

A Gunicorn további argumentumait is hozzáadhatja * \<az egyéni parancshoz>*, például `--workers=4`a . További információkért lásd: [A Gunicorn futtatása](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Ha nem Gunicorn kiszolgálót, például [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)http-t szeretne használni, az * \<egyéni parancs>* a következőhöz hasonlóra cserélheti:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> Az App Service figyelmen kívül hagyja az egyéni parancsfájl feldolgozásakor előforduló hibákat, majd az indítási folyamat következő lépéseként megkeresi a Django- és a Flask-alkalmazásokat. Ha nem várt viselkedést tapasztal, ellenőrizze, hogy az indítási fájl telepítve lett-e az App Service-be, és hogy az nem tartalmaz-e hibákat.

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben az [alkalmazáskódokon kívül is megadhat alkalmazásbeállításokat.](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) Akkor elérheti őket a szabványos [os.environ](https://docs.python.org/3/library/os.html#os.environ) mintát. Például egy alkalmazás nevű `WEBSITE_SITE_NAME`beállítás eléréséhez használja a következő kódot:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

Az App Service-ben [az SSL-végződtetés](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztóknál történik, így minden HTTPS-kérelem titkosítatlan HTTP-kérelemként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy `X-Forwarded-Proto` a felhasználói kérelmek titkosítva vannak-e vagy sem, ellenőrizze a fejlécet.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

A népszerű webes keretrendszerek `X-Forwarded-*` lehetővé teszik a szabványos alkalmazásmintában szereplő információk elérését. A [CodeIgniter](https://codeigniter.com/)ben a [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi az értékét. `X_FORWARDED_PROTO`

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása a böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

- **Saját alkalmazáskódjának telepítése után megjelenik az alapértelmezett alkalmazás.** Az alapértelmezett alkalmazás azért jelenik meg, mert vagy nem telepítette az alkalmazáskódot az App Service-be, vagy az App Service nem találta az alkalmazáskódot, és helyette az alapértelmezett alkalmazást futtatta.
- Indítsa újra az App Service-t, várjon 15-20 másodpercet, és ellenőrizze újra az alkalmazást.
- Bizonyosodjon meg róla, hogy az App Service Linux- és nem Windows-alapú példányát használja. Az Azure CLI parancssori felületről futtassa az `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` parancsot, a `<resource_group_name>` és az `<app_service_name>` helyőrzőket megfelelően behelyettesítve. Az `app,linux` kimenetet kell látnia, máskülönben hozza újra létre az App Service szolgáltatást, és válassza a linuxos verziót.
- SSH- vagy a Kudu konzol használatával közvetlenül csatlakozzon az App Service-hez, és győződjön meg arról, hogy a fájlok léteznek a *site/wwwroot* könyvtárban. Ha a fájlok nem léteznek, tekintse át a telepítési folyamatot, és telepítse újra az alkalmazást.
- Ha a fájlok léteznek, az App Service nem tudta azonosítani az adott indítási fájlt. Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).
- **A böngészőben megjelenik „A szolgáltatás nem érhető el” üzenet.** A böngésző az App Service válaszára vára túllépte az időkorlátot, ami azt jelzi, hogy az App Service elindította a Gunicorn-kiszolgálót, de az alkalmazás kódját meghatározó argumentumok helytelenek.
- Frissítse a böngészőt, különösen akkor, ha az App Service-csomag legalacsonyabb tarifacsomagját használja. Az ingyenes szolgáltatásszintek használatakor például az alkalmazás lassabban indul, és csak a böngésző frissítése után fog ismét reagálni.
- Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).
- [A naplófolyam elérése](#access-diagnostic-logs).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python alkalmazás a PostgreSQL-rel](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Üzembe helyezés a privát tárolótárból](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)
