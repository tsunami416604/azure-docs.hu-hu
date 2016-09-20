<properties
    pageTitle="Webalkalmazások létrehozása a Djangóval az Azure-ban"
    description="A Python webalkalmazás az Azure App Service Web Apps szolgáltatásban történő futtatását bemutató oktatóanyag."
    services="app-service\web"
    documentationCenter="python"
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article" 
    ms.date="02/19/2016"
    ms.author="huvalo"/>


# Webalkalmazások létrehozása a Djangóval az Azure-ban

Ez az oktatóanyag a Python futtatásának első lépéseit mutatja be az [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) felületén. A Web Apps korlátozott ingyenes üzemeltetést és gyors üzembe helyezést kínál, ráadásul a Python alkalmazást is használhatja! Az alkalmazása növekedésével átválthat fizetős üzemeltetésre, valamint integrálhatja alkalmazásába az összes többi Azure-szolgáltatást is.

A Django webes keretrendszer használatával fog létrehozni egy alkalmazást (lásd az oktatóanyag alternatív verzióit a [Flask](web-sites-python-create-deploy-flask-app.md) és a [Bottle](web-sites-python-create-deploy-bottle-app.md) használatával). Létre fog hozni egy webalkalmazást az Azure Piactérről, be fogja állítani a Git üzemelő példányt, majd helyileg fogja klónozni a tárházat. Ezt követően helyileg fogja futtatni az alkalmazást, módosításokat fog elvégezni rajta, majd véglegesíteni fogja őket, és el fogja küldeni az Azure-ba. Ez az oktatóanyag ennek a folyamatnak a Windows vagy Mac/Linux operációs rendszeren történő végrehajtását mutatja be.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az Azure App Service kipróbálása](http://go.microsoft.com/fwlink/?LinkId=523751) oldalra, ahol azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.


## Előfeltételek

- Windows, Mac vagy Linux
- Python 2.7 vagy 3.4
- setuptools, pip, virtualenv (csak Python 2.7)
- Git
- [Python Tools for Visual Studio][] (PTVS) – Megjegyzés: nem kötelező

**Megjegyzés:** A TFS-közzététel a Python-projektek esetében jelenleg nem támogatott.

### Windows

Ha még nem telepítette a Python 2.7-es vagy 3.4-es (32 bites) verzióját, javasoljuk a [Python 2.7-hez készült Azure SDK] vagy a [Python 3.4-hez készült Azure SDK] Webplatform-telepítővel történő telepítését. Ezzel többek között a következőket telepíti: a Python 32 bites verziója, setuptools, pip, virtualenv (a Python 32 bites verziója van telepítve az Azure-gazdagépeken). Alternatív megoldásként a Python eszközt a [python.org] webhelyről is beszerezheti.

A Git esetében a [Git for Windows] vagy a [GitHub for Windows] használatát javasoljuk. Visual Studio használata esetén használhatja a beépített Git-támogatást.

Szintén javasoljuk a [Python Tools 2.2 for Visual Studio] telepítését. Ez ugyan nem kötelező, de ha rendelkezik az ingyenes Visual Studio Community 2013 vagy Visual Studio Express 2013 for Web alkalmazásokat is magában foglaló [Visual Studio] szoftverrel, a nagyszerű Python IDE előnyeit is élvezheti.

### Mac/Linux

A Python és a Git alkalmazásoknak már telepítve kell lenniük, de győződjön meg arról, hogy a Python verziószáma 2.7-es vagy 3.4-es.


## Webalkalmazás létrehozása a portálon

Saját alkalmazása létrehozásának első lépése egy webalkalmazás létrehozása az [Azure portálon](https://portal.azure.com).

1. Jelentkezzen be az Azure portálra, majd kattintson a bal alsó sarokban található **NEW** (ÚJ) gombra.
3. A keresőmezőbe írja be a „python” kifejezést.
4. A keresési eredmények közül válassza ki a **Django** elemet (amelyet a PTVS tett közzé), majd kattintson a **Create** (Létrehozás) gombra.
5. Konfigurálja az új Django-alkalmazást, például új App Service-csomag és egy ahhoz tartozó új erőforráscsoport létrehozásával. Ezt követően kattintson a **Create** (Létrehozás) gombra.
6. Konfigurálja az újonnan létrehozott webalkalmazáshoz tartozó Git-közzétételt a [Local Git Deployment to Azure App Service](app-service-deploy-local-git.md) (Helyi Git-üzembehelyezés az Azure App Service-ben) részben megadott utasítások szerint.

## Az alkalmazás áttekintése

### A Git-tárház tartalma

Az alábbiakban áttekintjük a kiindulási Git-tárházban található fájlokat. A következő szakaszban ezek klónozását fogjuk elvégezni.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Az alkalmazás fő forrásai. 3 oldalból (index, névjegy, kapcsolat) áll egy fő elrendezéssel. Példák statikus tartalomra és parancsfájlokra: bootstrap, jquery, modernizr és respond.

    \manage.py

A helyi felügyelet és a fejlesztési kiszolgáló támogatása. Válassza ezt a lehetőséget például az alkalmazás helyi futtatásához vagy az adatbázis szinkronizálásához.

    \db.sqlite3

Az alapértelmezett adatbázis. Tartalmazza az alkalmazás futtatásához szükséges táblákat, de nem tartalmaz felhasználókat (felhasználó az adatbázis szinkronizálásával hozható létre).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

A [Python Tools for Visual Studio] alkalmazással használható projektfájlok.

    \ptvs_virtualenv_proxy.py

IIS-proxy a virtuális környezetekhez, valamint távoli hibaelhárítási támogatás a PTVS-hez.

    \requirements.txt

Az alkalmazás számára szükséges külső csomagok. A telepítési parancsfájl elvégzi az ebben a fájlban felsorolt csomagok telepítését.

    \web.2.7.config
    \web.3.4.config

IIS-konfigurációs fájlok. A telepítési parancsfájl a megfelelő web.x.y.config fájlt fogja használni, és web.config fájlként fogja azt másolni.

### Opcionális fájlok – A telepítés testre szabása

[AZURE.INCLUDE [web-sites-python-django-customizing-deployment](../../includes/web-sites-python-django-customizing-deployment.md)]

### Opcionális fájlok – Python-futtatókörnyezet

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### További fájlok a kiszolgálón

Egyes fájlok megtalálhatóak ugyan a kiszolgálón, de nem lettek felvéve a Git-tárházba. Ezeket a telepítési parancsfájl hozza létre.

    \web.config

IIS-konfigurációs fájl. Minden telepítéshez a web.x.y.config fájlból jön létre.

    \env\

Python virtuális környezet. A telepítés során jön létre, ha a webalkalmazáson még nem található kompatibilis virtuális környezet. Megtörténik a requirements.txt fájlban felsorolt csomagok telepítése, a korábban már telepített csomagokat azonban a pip már nem telepíti újra.

A következő három szakaszban a webalkalmazások fejlesztéséről talál információkat, három különböző környezetben:

- Windows, Python Tools for Visual Studio alkalmazással
- Windows, parancssorral
- Mac/Linux, parancssorral


## Webalkalmazás-fejlesztés – Windows – Python Tools for Visual Studio

### A tárház klónozása

Első lépésben klónozza a tárházat az Azure Portalon található URL-cím használatával. További információ: [Local Git Deployment to Azure App Service](app-service-deploy-local-git.md) (Helyi Git-üzembehelyezés az Azure App Service-ben).

Nyissa meg a tárház gyökérkönyvtárában található megoldásfájlt (.sln).

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### Virtuális környezet létrehozása

Most helyi telepítéshez tartozó virtuális környezetet hozunk létre. Kattintson a jobb gombbal a **Python Environments** (Python-környezetek) elemre, majd válassza az **Add Virtual Environment...** (Virtuális környezet hozzáadása...) lehetőséget.

- Győződjön meg arról, hogy a környezet neve a következő: `env`.

- Válassza ki az alapszintű értelmezőt. Győződjön meg arról, hogy a Python ugyanazon verzióját használja, mint ami a webalkalmazás esetében ki lett választva (ez az információ az Azure portálon lévő webalkalmazásának runtime.txt fájljában vagy az **Application Settings** (Alkalmazásbeállítások) panelen található meg).

- Győződjön meg arról, hogy a csomagok letöltése és telepítése be van jelölve.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Kattintson a ** Create** (Létrehozás) gombra. Ezzel létrejön a virtuális környezet, valamint települnek a requirements.txt fájlban található függőségek.

### Felügyelő létrehozása

Az alkalmazáshoz tartozó adatbázishoz nincs megadva felügyelő. Az alkalmazás bejelentkezési funkciójának vagy a Django felügyeleti felületének használatához (ha úgy dönt, hogy engedélyezi) felügyelőt kell létrehozni.

A projektmappa parancssorából futtassa a következő parancsot:

    env\scripts\python manage.py createsuperuser

Kövesse a megjelenő utasításokat a felhasználónév és a jelszó beállításához és egyéb műveletekhez.

### Futtatás fejlesztési kiszolgálóval

A hibakeresés elindításához nyomja le az F5 billentyűt, a webböngészőjében. Ekkor automatikusan a helyileg futtatott lap nyílik meg.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Többek között töréspontokat állíthat be a forrásokban, vagy használhatja a figyelőablakokat. A különböző funkciókról további információkat [a Python Tools for Visual Studio dokumentációjában] talál.

### Módosítások végrehajtása

Most kísérletezhet azzal, hogy módosításokat hajt végre az alkalmazásforrásokon és/vagy -sablonokon.

A módosítások tesztelését követően véglegesítse azokat a Git-tárházba:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### További csomagok telepítése

Az alkalmazása a Python és a Django eszközön kívüli függőségekkel is rendelkezhet.

A pip használatával további csomagokat is telepíthet. Csomag telepítéséhez kattintson a jobb gombbal a virtuális környezetre, majd válassza az **Install Python Package** (Python-csomag telepítése) lehetőséget.

Például az Azure Storage, Service Bus és további Azure-szolgáltatásokhoz hozzáférést biztosító, Pythonhoz készült Azure SDK telepítéséhez adja meg az `azure` karakterláncot:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Kattintson a jobb gombbal a virtuális környezetre, majd a requirements.txt fájl frissítéséhez válassza a **Generate requirements.txt** (requirements.txt fájl létrehozása) lehetőséget.

Ezt követően véglegesítse a requirements.txt fájl módosításait a Git-tárházban.

### Üzembe helyezés az Azure-ban

Az üzembe helyezés indításához kattintson a **Sync** (Szinkronizálás) vagy a **Push** (Leküldés) lehetőségre. A szinkronizálás lekérési és leküldési funkcióval is rendelkezik.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

Az első üzembe helyezés hosszabb időt vesz igénybe, mivel ennek során megtörténik többek között a virtuális környezet létrehozása és a csomagok telepítése is.

A Visual Studio nem jeleníti meg az üzembe helyezés állapotát. A kimenet áttekintéséhez lásd: [Hibaelhárítás – Üzembe helyezés](#troubleshooting-deployment).

A módosítások megtekintéséhez lépjen az Azure URL-címére.


## Webalkalmazás-fejlesztés – Windows – parancssor

### A tárház klónozása

Első lépésben klónozza a tárházat az Azure Portalon található URL-cím használatával, majd távoli tárházként vegye fel az Azure-tárházat. További információ: [Local Git Deployment to Azure App Service](app-service-deploy-local-git.md) (Helyi Git-üzembehelyezés az Azure App Service-ben).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### Virtuális környezet létrehozása

Fejlesztői célra létre fogunk hozni egy új virtuális környezetet (ezt ne vegye fel a tárházba). A Python virtuális környezetei nem helyezhetőek át, így az alkalmazáson dolgozó összes fejlesztő helyileg hozza létre a sajátját.

Győződjön meg arról, hogy a Python ugyanazon verzióját használja, mint ami a webalkalmazás esetében ki lett választva (ez az információ az Azure portálon lévő webalkalmazásának runtime.txt fájljában vagy az Application Settings (Alkalmazásbeállítások) panelen található meg).

Python 2.7 esetén:

    c:\python27\python.exe -m virtualenv env

Python 3.4 esetén:

    c:\python34\python.exe -m venv env

Telepítse az alkalmazáshoz esetlegesen szükséges külső csomagokat. A csomagok virtuális környezetben történő telepítéséhez a tárház gyökérkönyvtárában található requirements.txt fájlt használhatja:

    env\scripts\pip install -r requirements.txt

### Felügyelő létrehozása

Az alkalmazáshoz tartozó adatbázishoz nincs megadva felügyelő. Az alkalmazás bejelentkezési funkciójának vagy a Django felügyeleti felületének használatához (ha úgy dönt, hogy engedélyezi) felügyelőt kell létrehozni.

A projektmappa parancssorából futtassa a következő parancsot:

    env\scripts\python manage.py createsuperuser

Kövesse a megjelenő utasításokat a felhasználónév és a jelszó beállításához és egyéb műveletekhez.

### Futtatás fejlesztési kiszolgálóval

A következő paranccsal indíthatja el az alkalmazást egy fejlesztési kiszolgáló alatt:

    env\scripts\python manage.py runserver

A konzolon megjelenik az URL-cím és az a port, amelyen a kiszolgáló figyel:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Ezt követően nyissa meg a webböngészőjében ezt az URL-címet.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### Módosítások végrehajtása

Most kísérletezhet azzal, hogy módosításokat hajt végre az alkalmazásforrásokon és/vagy -sablonokon.

A módosítások tesztelését követően véglegesítse azokat a Git-tárházba:

    git add <modified-file>
    git commit -m "<commit-comment>"

### További csomagok telepítése

Az alkalmazása a Python és a Django eszközön kívüli függőségekkel is rendelkezhet.

A pip használatával további csomagokat is telepíthet. Például az Azure Storage, Service Bus és további Azure-szolgáltatásokhoz hozzáférést biztosító, Pythonhoz készült Azure SDK telepítéséhez írja be a következőt:

    env\scripts\pip install azure

Győződjön meg arról, hogy frissítette a requirements.txt fájlt:

    env\scripts\pip freeze > requirements.txt

Véglegesítse a módosításokat:

    git add requirements.txt
    git commit -m "Added azure package"

### Üzembe helyezés az Azure-ban

Az üzembe helyezés indításához küldje le a módosításokat az Azure-ba:

    git push azure master

Itt megtekinthető a telepítési parancsfájl kimenete, amely tartalmazza a virtuális környezet létrehozását, a csomagok telepítését és a web.config fájl létrehozását.

A módosítások megtekintéséhez lépjen az Azure URL-címére.


## Webalkalmazás-fejlesztés – Mac/Linux – parancssor

### A tárház klónozása

Első lépésben klónozza a tárházat az Azure Portalon található URL-cím használatával, majd távoli tárházként vegye fel az Azure-tárházat. További információ: [Local Git Deployment to Azure App Service](app-service-deploy-local-git.md) (Helyi Git-üzembehelyezés az Azure App Service-ben).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### Virtuális környezet létrehozása

Fejlesztői célra létre fogunk hozni egy új virtuális környezetet (ezt ne vegye fel a tárházba). A Python virtuális környezetei nem helyezhetőek át, így az alkalmazáson dolgozó összes fejlesztő helyileg hozza létre a sajátját.

Győződjön meg arról, hogy a Python ugyanazon verzióját használja, mint ami a webalkalmazás esetében ki lett választva (ez az információ az Azure portálon lévő webalkalmazásának runtime.txt fájljában vagy az Application Settings (Alkalmazásbeállítások) panelen található meg).

Python 2.7 esetén:

    python -m virtualenv env

Python 3.4 esetén:

    python -m venv env

vagy

    pyvenv env

Telepítse az alkalmazáshoz esetlegesen szükséges külső csomagokat. A csomagok virtuális környezetben történő telepítéséhez a tárház gyökérkönyvtárában található requirements.txt fájlt használhatja:

    env/bin/pip install -r requirements.txt

### Felügyelő létrehozása

Az alkalmazáshoz tartozó adatbázishoz nincs megadva felügyelő. Az alkalmazás bejelentkezési funkciójának vagy a Django felügyeleti felületének használatához (ha úgy dönt, hogy engedélyezi) felügyelőt kell létrehozni.

A projektmappa parancssorából futtassa a következő parancsot:

    env/bin/python manage.py createsuperuser

Kövesse a megjelenő utasításokat a felhasználónév és a jelszó beállításához és egyéb műveletekhez.

### Futtatás fejlesztési kiszolgálóval

A következő paranccsal indíthatja el az alkalmazást egy fejlesztési kiszolgáló alatt:

    env/bin/python manage.py runserver

A konzolon megjelenik az URL-cím és az a port, amelyen a kiszolgáló figyel:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Ezt követően nyissa meg a webböngészőjében ezt az URL-címet.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### Módosítások végrehajtása

Most kísérletezhet azzal, hogy módosításokat hajt végre az alkalmazásforrásokon és/vagy -sablonokon.

A módosítások tesztelését követően véglegesítse azokat a Git-tárházba:

    git add <modified-file>
    git commit -m "<commit-comment>"

### További csomagok telepítése

Az alkalmazása a Python és a Django eszközön kívüli függőségekkel is rendelkezhet.

A pip használatával további csomagokat is telepíthet. Például az Azure Storage, Service Bus és további Azure-szolgáltatásokhoz hozzáférést biztosító, Pythonhoz készült Azure SDK telepítéséhez írja be a következőt:

    env/bin/pip install azure

Győződjön meg arról, hogy frissítette a requirements.txt fájlt:

    env/bin/pip freeze > requirements.txt

Véglegesítse a módosításokat:

    git add requirements.txt
    git commit -m "Added azure package"

### Üzembe helyezés az Azure-ban

Az üzembe helyezés indításához küldje le a módosításokat az Azure-ba:

    git push azure master

Itt megtekinthető a telepítési parancsfájl kimenete, amely tartalmazza a virtuális környezet létrehozását, a csomagok telepítését és a web.config fájl létrehozását.

A módosítások megtekintéséhez lépjen az Azure URL-címére.


## Hibaelhárítás – Csomagok telepítése

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Hibaelhárítás – Virtuális környezet

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Hibaelhárítás – Statikus fájlok

A Django statikus fájlokat gyűjt. Ez azt jelenti, hogy az összes statikus fájlt eredeti helyükről egyetlen mappába másolja. A jelen alkalmazás esetében a másolás helye: `/static`.

Ez azért történik, mert a statikus fájlok különböző Django-alkalmazásokból származhatnak. A Django felügyeleti felületeiről származó statikus fájlok például a virtuális környezetben, egy Django-könyvtár almappájában találhatóak. A jelen alkalmazásban megadott statikus fájlok itt találhatóak: `/app/static`. Ahogy egyre több Django-alkalmazást használ, egyre több helyen lesznek statikus fájlok.

Az alkalmazás hibakeresési módban történő futtatásakor az alkalmazás az eredeti helyükről szolgálja ki a statikus fájlokat.

Az alkalmazás kiadási módban történő futtatásakor az alkalmazás **nem** szolgálja ki a statikus fájlokat. A fájlok kiszolgálása a webkiszolgáló feladata. A jelen alkalmazás esetében az IIS a következő helyről szolgálja ki a statikus fájlokat: `/static`.

A statikus fájlok összegyűjtése automatikusan történik az üzembe helyezési parancsfájl futtatása során, az előzőleg összegyűjtött fájlok törlésével. Ez azt jelenti, hogy a fájlok összegyűjtése minden üzembe helyezés során megtörténik, ami lelassítja kissé a folyamatot, ugyanakkor biztosítja, hogy a potenciális biztonsági kockázatot jelentő elavult fájlok ne legyen elérhetőek.

Ha ki szeretné hagyni a Django-alkalmazáshoz a statikus fájlok összegyűjtését, használja ezt:

    \.skipDjango

Ezt követően manuálisan kell elvégeznie a gyűjtést a helyi gépen:

    env\scripts\python manage.py collectstatic

Ezután távolítsa el a `\static` mappát a `.gitignore` elemből, és vegye fel azt a Git-tárházba.


## Hibaelhárítás – Beállítások

Az alkalmazás számos beállítása módosítható a következő helyen: `DjangoWebProject/settings.py`.

A hibakeresési mód a fejlesztők kényelme érdekében engedélyezve van. Ez szerencsés módon azzal is jár, hogy helyi futtatáskor anélkül tekintheti meg a képeket és az egyéb statikus tartalmakat, hogy a statikus fájlokat össze kellene gyűjtenie.

A hibakeresési mód letiltása:

    DEBUG = False

Ha a hibakeresés le van tiltva, az `ALLOWED_HOSTS` értékét módosítani kell úgy, hogy tartalmazza az Azure-gazdagép nevét. Példa:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

vagy az engedélyezéshez:

    ALLOWED_HOSTS = (
        '*',
    )

A gyakorlatban lehetséges, hogy olyan összetettebb műveleteket is végre kíván hajtani, mint a hibakeresési és a kiadási mód közötti váltás, illetve a gazdagépnév beszerzése.

A környezeti változókat az Azure portál **CONFIGURE** (KONFIGURÁLÁS) lapjának **app settings** (alkalmazásbeállítások) szakaszában állíthatja be.  Ez olyan értékek beállításakor lehet hasznos, amelyeket esetleg nem kíván megjeleníteni a forrásokban (például kapcsolati karakterláncok, jelszavak stb.), illetve amelyeket az Azure és a helyi gép esetében eltérően kíván megadni. A `settings.py` elemben az `os.getenv` használatával kérdezhetők le a környezeti változók.


## Adatbázis használata

Az alkalmazásban egy sqlite-adatbázis található. Fejlesztési célra ez egy kényelmes és hasznos alapértelmezett adatbázis, mivel nem igényel szinte semmilyen beállítást. Az adatbázis a projektmappa db.sqlite3 fájljában található.

Az Azure a Django-alkalmazásokból könnyen használható adatbázis-szolgáltatásokat kínál. Az [SQL Database] és a [MySQL] Django-alkalmazásból történő használatához kapcsolódó oktatóanyagokban szerepelnek az adatbázis-szolgáltatás létrehozásához és az adatbázis beállításainak `DjangoWebProject/settings.py` elemben történő módosításához szükséges lépések, valamint a telepítéshez szükséges könyvtárak.

Természetesen ha Ön szeretné kezelni a saját adatbázis-kiszolgálóit, az Azure platformon futó Windows vagy Linux rendszerű virtuális gépek használatával megteheti azt.


## A Django felügyeleti felülete

Amikor hozzákezd a modelljei felépítéséhez, fel fogja tölteni az adatbázist adatokkal. A Django felügyeleti felületének használatával egyszerűen, interaktív módon vehet fel és szerkeszthet tartalmakat.

A felügyeleti felülethez tartozó kód az alkalmazásforrásokban megjegyzésként szerepel, de mivel egyértelműen meg van jelölve, könnyen engedélyezheti azt (keressen rá az „admin” kifejezésre).

Miután engedélyezte, szinkronizálja az adatbázist, futtassa az alkalmazást, majd lépjen a következő helyre: `/admin`.


## Következő lépések

Az alábbi hivatkozásokat követve tudhat meg többet a Django és a Python Tools for Visual Studio alkalmazásokról:

- [A Django dokumentációja]
- [a Python Tools for Visual Studio dokumentációjában]

Információk az SQL Database és a MySQL használatáról:

- [Django and MySQL on Azure with Python Tools for Visual Studio (Django és MySQL az Azure-ban, Python Tools for Visual Studio alkalmazással)]
- [Django and SQL Database on Azure with Python Tools for Visual Studio (Django és SQL Database az Azure-ban, Python Tools for Visual Studio alkalmazással)]

További információ: [Python fejlesztői központ](/develop/python/).


## A változások
* Információk a Websites szolgáltatásról az App Service-re való váltásról: [Az Azure App Service és a hatása a meglévő Azure-szolgáltatásokra](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Django and MySQL on Azure with Python Tools for Visual Studio (Django és MySQL az Azure-ban, Python Tools for Visual Studio alkalmazással)]: web-sites-python-ptvs-django-mysql.md
[Django and SQL Database on Azure with Python Tools for Visual Studio (Django és SQL Database az Azure-ban, Python Tools for Visual Studio alkalmazással)]: web-sites-python-ptvs-django-sql.md
[SQL Database]: web-sites-python-ptvs-django-sql.md
[MySQL]: web-sites-python-ptvs-django-mysql.md

<!--External Link references-->
[Python 2.7-hez készült Azure SDK]: http://go.microsoft.com/fwlink/?linkid=254281
[Python 3.4-hez készült Azure SDK]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git for Windows]: http://msysgit.github.io/
[GitHub for Windows]: https://windows.github.com/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[a Python Tools for Visual Studio dokumentációjában]: http://aka.ms/ptvsdocs
[A Django dokumentációja]: https://www.djangoproject.com/



<!--HONumber=sep16_HO1-->


