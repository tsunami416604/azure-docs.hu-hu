---
title: Linux Python-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a webalkalmazásokat futtató Python-tárolót a Azure Portal és az Azure CLI használatával.
ms.topic: quickstart
ms.date: 10/06/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: df4b94702d14278a3279c504f52f46b922859db8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822800"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Linux Python-alkalmazás konfigurálása a Azure App Servicehoz

Ez a cikk leírja, hogyan futtatja [Azure app Service](overview.md) a Python-alkalmazásokat, és hogyan szabhatja testre a app Service viselkedését, ha szükséges. A Python-alkalmazásokat az összes szükséges [pip](https://pypi.org/project/pip/) -modullal telepíteni kell.

A App Service üzembe helyezési motor automatikusan aktiválja a virtuális környezetet, és `pip install -r requirements.txt` a [git-tárház](deploy-local-git.md)vagy egy zip- [csomag](deploy-zip.md)telepítésekor futtatja azt.

Ez az útmutató ismerteti a Python-fejlesztők számára a App Service beépített Linux-tárolóját használó főbb fogalmakat és útmutatást. Ha még soha nem használta a Azure App Servicet, először kövesse a [Python](quickstart-python.md) rövid [útmutatóját és a Pythont a PostgreSQL oktatóanyaggal](tutorial-python-postgresql-app.md).

A konfiguráláshoz használhatja a [Azure Portal](https://portal.azure.com) vagy az Azure CLI-t:

- **Azure Portal**használja az alkalmazás **Beállítások**  >  **konfigurációs** lapját az [Azure Portal app Service alkalmazás konfigurálása](configure-common.md)című cikkben leírtak szerint.

- **Azure CLI**: két lehetőség közül választhat.

    - Futtassa a parancsokat a [Azure Cloud Shellban](../cloud-shell/overview.md), amelyet a kód blokk jobb felső sarkában található **TRY IT (kipróbálás** ) gombra kattintva nyithat meg.
    - Futtassa a parancsokat helyileg az [Azure CLI](/cli/azure/install-azure-cli)legújabb verziójának telepítésével, majd jelentkezzen be az Azure-ba az [az login](/cli/azure/reference-index#az-login)használatával.
    
> [!NOTE]
> A Linux jelenleg az ajánlott lehetőség a Python-alkalmazások App Service-ben való futtatására. További információ a Windows lehetőségről: [Python a app Service Windows-íz](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Python-verzió konfigurálása

- **Azure Portal**: használja a **konfiguráció** lap **általános beállítások** lapját a Linux-tárolók [általános beállításainak konfigurálása](configure-common.md#configure-general-settings) című témakörben leírtak szerint.

- **Azure CLI**:

    -  Az aktuális Python-verzió megjelenítése az [az WebApp config show paranccsal](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli-interactive
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Cserélje `<resource-group-name>` le `<app-name>` a és a nevet a webalkalmazásának megfelelő nevekre.
    
    - A Python verziójának beállítása az [WebApp config set](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli-interactive
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - A Azure App Service által támogatott összes Python-verzió megjelenítése az az [WebApp List-Runtimes](/cli/azure/webapp#az_webapp_list_runtimes)paranccsal:
    
        ```azurecli-interactive
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
A Python nem támogatott verzióját a saját tároló rendszerképének létrehozásával futtathatja. További információ: [egyéni Docker-rendszerkép használata](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>A Build Automation testreszabása

App Service a Oryx nevű Build-rendszer a következő lépéseket hajtja végre az alkalmazás git vagy zip-csomagok használatával történő telepítésekor:

1. Ha ezt a beállítást adja meg, futtasson egyéni, előkészítő parancsfájlt `PRE_BUILD_COMMAND` .
1. A `pip install -r requirements.txt` parancs futtatása. A *requirements.txt* fájlnak jelen kell lennie a projekt gyökérkönyvtárában. Ellenkező esetben a fordítási folyamat a következő hibát jelenti: "nem található a setup.py vagy a requirements.txt; Nem fut a pip telepítése. "
1. Ha a *Manage.py* a tárház gyökerében található (Django-alkalmazást jelez), futtassa a *Manage.py collectstatic*. Ha azonban ez a `DISABLE_COLLECTSTATIC` beállítás `true` , ez a lépés kimarad.
1. Ha a beállítás megadja az egyéni létrehozás utáni parancsfájlt, `POST_BUILD_COMMAND`

Alapértelmezés szerint a `PRE_BUILD_COMMAND` , a `POST_BUILD_COMMAND` és a `DISABLE_COLLECTSTATIC` Beállítások üresek. 

- Ha le szeretné tiltani a collectstatic futtatását a Django-alkalmazások létrehozásakor, állítsa a `DISABLE_COLLECTSTATIC` beállítást igaz értékre.

- Az előkészítő parancsok futtatásához állítsa a `PRE_BUILD_COMMAND` beállítást úgy, hogy az a `echo Pre-build command` projekt gyökeréhez képest egy parancsot (például) vagy egy parancsfájl elérési útját (például) tartalmazza `scripts/prebuild.sh` . Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérkönyvtárához.

- A létrehozás utáni parancsok futtatásához állítsa a `POST_BUILD_COMMAND` beállítást úgy, hogy a a `echo Post-build command` projekt gyökeréhez képest egy parancsot (például) vagy egy parancsfájl elérési útját (például) tartalmazza `scripts/postbuild.sh` . Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérkönyvtárához.

A Build Automation testreszabásával kapcsolatos további beállításokért lásd: [Oryx-konfiguráció](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

A Python-alkalmazások Linux rendszeren való futtatásával és a App Serviceával kapcsolatos további információkért lásd: [how Oryx észleli és létrehozza a Python-alkalmazásokat](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

> [!NOTE]
> A és a beállítások azonosak a és a, és az `PRE_BUILD_SCRIPT_PATH` `POST_BUILD_SCRIPT_PATH` `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` örökölt célokra támogatottak.
> 
> `SCM_DO_BUILD_DURING_DEPLOYMENT`Ha A (z) vagy 1 értéket tartalmaz, a rendszer elindítja a Oryx- `true` buildet az üzembe helyezés során. A beállítás igaz a git, az Azure CLI-parancs `az webapp up` és a Visual Studio Code használatával történő üzembe helyezéskor.

> [!NOTE]
> Mindig relatív elérési utakat használjon az összes előre és post-Build parancsfájlban, mert az a Build-tároló, amelyben a Oryx fut, eltér az alkalmazást futtató futtatókörnyezeti tárolótól. Soha ne támaszkodjon az alkalmazás Project mappájának a tárolón belüli pontos elhelyezésére (például úgy, hogy az a *site/wwwroot*alá van helyezve).

## <a name="container-characteristics"></a>A tároló jellemzői

App Service rendszerbe való üzembe helyezéskor a Python-alkalmazások egy, a [app Service Python GitHub-tárházban](https://github.com/Azure-App-Service/python)meghatározott Linux Docker-tárolón belül futnak. A rendszerkép-konfigurációk a verziószámozási könyvtárakban találhatók.

Ez a tároló a következő jellemzőkkel rendelkezik:

- Az alkalmazások a [Gunicorn WSGI HTTP-kiszolgáló](https://gunicorn.org/) és további argumentumok használatával futnak`--bind=0.0.0.0 --timeout 600`.
    - A Gunicorn konfigurációs beállításait megadhatja a *gunicorn.conf.py* -fájlon keresztül a [Gunicorn-konfiguráció áttekintése](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org) című cikkben leírtak szerint. Lehetőség van [az indítási parancs testreszabására](#customize-startup-command)is.

    - A webalkalmazások véletlen vagy szándékos DDOS-támadások elleni védelem érdekében a Gunicorn egy Nginx fordított proxy mögött fut a Gunicorn (docs.gunicorn.org) [üzembe helyezése](https://docs.gunicorn.org/en/latest/deploy.html) című témakörben leírtak szerint.

- Alapértelmezés szerint az alapszintű tároló képe csak a lombik webes keretrendszerét tartalmazza, a tároló azonban támogatja a WSGI-kompatibilis és a Python 3.6 + szabvánnyal kompatibilis más keretrendszerek használatát, például a Django-t.

- További csomagok, például a Django telepítéséhez hozzon létre egy [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) fájlt a projekt gyökérkönyvtárában, amely meghatározza a közvetlen függőségeket. A App Service ezután automatikusan telepíti ezeket a függőségeket a projekt központi telepítésekor.

    A telepítendő függőségekhez a *requirements.txt* fájlnak a projekt gyökerében *kell* lennie. Ellenkező esetben a fordítási folyamat a következő hibát jelenti: "nem található a setup.py vagy a requirements.txt; Nem fut a pip telepítése. " Ha ezt a hibát tapasztalja, ellenőrizze a követelmények fájljának helyét.

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
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Ha pontosabban szeretné vezérelni az indítási parancsot, használjon [Egyéni indítási parancsot](#customize-startup-command), cserélje le `<module>` a nevet a *WSGI.py*tartalmazó mappa nevére, és adjon hozzá egy `--chdir` argumentumot, ha a modul nem szerepel a projekt gyökerében. Ha például a *WSGI.py* a *knboard/háttér/konfiguráció* területen található a projekt gyökerénél, használja az argumentumokat `--chdir knboard/backend config.wsgi` .

Az éles naplózás engedélyezéséhez adja hozzá a `--access-logfile` és a `--error-logfile` paramétereket az [Egyéni indítási parancsokra](#customize-startup-command)vonatkozó példákban látható módon.

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

![Alapértelmezett App Service a Linux webhelyen](media/configure-language-python/default-python-app.png)

## <a name="customize-startup-command"></a>Indítási parancs testreszabása

A cikkben korábban leírtaknak megfelelően a Gunicorn konfigurációs beállításait a *gunicorn.conf.py* -fájlon keresztül is megadhatja a projekt gyökerében, a [Gunicorn-konfiguráció áttekintése](https://docs.gunicorn.org/en/stable/configure.html#configuration-file)című témakörben leírtak szerint.

Ha ez a konfiguráció nem elegendő, a tároló indítási viselkedését beállíthatja úgy, hogy egyéni indítási parancsot vagy több parancsot indít el egy indítási parancsfájlban. Az indítási parancsfájl tetszőleges nevet használhat, például *Startup.sh*, *Startup. cmd*, *startup.txt*stb.

Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérkönyvtárához.

Indítási parancs vagy parancsfájl megadásához:

- **Azure Portal**: válassza ki az alkalmazás **konfiguráció** lapját, majd válassza az **általános beállítások**lehetőséget. Az **indítási parancs** mezőben helyezze el az indítási parancs teljes szövegét vagy az indítási parancsfájl nevét. Ezután kattintson a **Mentés** gombra a módosítások alkalmazásához. Lásd: a Linux-tárolók [általános beállításainak konfigurálása](configure-common.md#configure-general-settings) .

- **Azure CLI**: használja az az [WebApp config set](/cli/azure/webapp/config#az_webapp_config_set) parancsot a `--startup-file` paraméterrel az indítási parancs vagy fájl beállításához:

    ```azurecli-interactive
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    A helyére írja `<custom-command>` be az indítási parancs teljes szövegét vagy az indítási parancsfájl nevét.
        
App Service figyelmen kívül hagyja az egyéni indítási parancsok vagy fájlok feldolgozásakor előforduló hibákat, majd a Django és a lombik alkalmazások keresésével folytatja az indítási folyamatát. Ha nem látja a várt viselkedést, ellenőrizze, hogy az indítási parancs vagy a fájl hibamentes-e, és hogy az alkalmazás kódjával együtt App Service-e az indítási parancsfájl. További információkért tekintse meg a [diagnosztikai naplókat](#access-diagnostic-logs) is. Tekintse meg az alkalmazás **diagnosztizálási és megoldási problémáit** ismertető oldalt is a [Azure Portal](https://portal.azure.com).

### <a name="example-startup-commands"></a>Példa indítási parancsokra

- **Hozzáadott Gunicorn argumentumai**: a következő példa hozzáadja a `--workers=4` -t egy Gunicorn parancssorhoz egy Django-alkalmazás indításához: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    További információkért lásd: [A Gunicorn futtatása](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- A **Django éles naplózásának engedélyezése**: adja hozzá a (z) `--access-logfile '-'` és `--error-logfile '-'` argumentumokat a parancssorhoz:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Ezek a naplók a [app Service log streamben](#access-diagnostic-logs)fognak megjelenni.

    További információ: Gunicorn- [naplózás](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Egyéni lombik főmodulja**: alapértelmezés szerint a app Service feltételezi, hogy a lombik-alkalmazás fő modulja a *application.py* vagy a *app.py*. Ha a fő modul más nevet használ, testre kell szabnia az indítási parancsot. Például YF van egy olyan lombik-alkalmazás, amelynek a fő modulja *Hello.py* , és az abban a fájlban található lombik alkalmazás-objektum neve `myapp` , a parancs a következő:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Ha a főmodul egy almappában található (például `website`), a mappát a `--chdir` argumentummal határozhatja meg:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Nem Gunicorn-kiszolgáló**használata: Ha másik webkiszolgálót (például [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)) szeretne használni, használja a megfelelő parancsot indítási parancsként vagy az indítási parancsfájlban:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Alkalmazás beállításainak elérése környezeti változókként

Az Alkalmazásbeállítások az alkalmazás [beállításainak konfigurálása](configure-common.md#configure-app-settings)című részben leírtaknak megfelelően a felhőben tárolt értékek. Ezek a beállítások környezeti változókként érhetők el az alkalmazás kódjában, és a szabványos [operációs rendszer. környezettel](https://docs.python.org/3/library/os.html#os.environ) érhetők el.

Ha például létrehozta az Alkalmazásbeállítás nevű alkalmazást `DATABASE_SERVER` , a következő kód lekéri a beállítás értékét:

```python
db_server = os.environ['DATABASE_SERVER']
```
    
## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-lezárás](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

A népszerű webes keretrendszerek lehetővé teszik a `X-Forwarded-*` szabványos alkalmazási mintában lévő információk elérését. A [CodeIgniter](https://codeigniter.com/)-ben a [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi a értéket `X_FORWARDED_PROTO` .

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

A naplók a Azure Portalon keresztüli eléréséhez válassza a **figyelés**  >  **napló stream** lehetőséget az alkalmazás bal oldali menüjében.

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

- **Saját alkalmazáskódjának telepítése után megjelenik az alapértelmezett alkalmazás.** Az alapértelmezett alkalmazás jelenik meg, mert vagy nem telepítette az alkalmazás kódját App Servicere, vagy App Service nem találta meg az alkalmazás kódját, és nem futtatta az alapértelmezett alkalmazást.

    - Indítsa újra az App Service-t, várjon 15-20 másodpercet, és ellenőrizze újra az alkalmazást.
    
    - Bizonyosodjon meg róla, hogy az App Service Linux- és nem Windows-alapú példányát használja. Az Azure CLI parancssori felületről futtassa az `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind` parancsot, a `<resource-group-name>` és az `<app-service-name>` helyőrzőket megfelelően behelyettesítve. Az `app,linux` kimenetet kell látnia, máskülönben hozza újra létre az App Service szolgáltatást, és válassza a linuxos verziót.
    
    - SSH- vagy a Kudu konzol használatával közvetlenül csatlakozzon az App Service-hez, és győződjön meg arról, hogy a fájlok léteznek a *site/wwwroot* könyvtárban. Ha a fájlok nem léteznek, tekintse át a telepítési folyamatot, és telepítse újra az alkalmazást.
    
    - Ha a fájlok léteznek, az App Service nem tudta azonosítani az adott indítási fájlt. Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).

- **A böngészőben megjelenik „A szolgáltatás nem érhető el” üzenet.** A böngésző az App Service válaszára vára túllépte az időkorlátot, ami azt jelzi, hogy az App Service elindította a Gunicorn-kiszolgálót, de az alkalmazás kódját meghatározó argumentumok helytelenek.

    - Frissítse a böngészőt, különösen akkor, ha az App Service-csomag legalacsonyabb tarifacsomagját használja. Az ingyenes szolgáltatásszintek használatakor például az alkalmazás lassabban indul, és csak a böngésző frissítése után fog ismét reagálni.

    - Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).

    - Ellenőrizze, hogy van-e hibaüzenet a [naplózási adatfolyamban](#access-diagnostic-logs) .

- **A log stream a következőt jeleníti meg: "nem található Setup.py vagy requirements.txt; Nem fut a pip telepítése. "**: a Oryx-létrehozási folyamat nem találta a *requirements.txt* fájlt.

    - Az SSH vagy a kudu-konzol használatával csatlakozzon közvetlenül a App Servicehoz, és ellenőrizze, hogy a *requirements.txt* létezik-e közvetlenül a *site/wwwroot*alatt. Ha nem létezik, tegye meg a fájlt a tárházban, és tartalmazza a központi telepítés részét képező helyet. Ha egy különálló mappában van, helyezze át a gyökerébe.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: üzembe helyezés Private Container adattárból](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [App Service a Linuxon – gyakori kérdések](faq-app-service-linux.md)
