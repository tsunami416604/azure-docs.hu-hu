<properties 
    pageTitle="Django and MySQL on Azure with Python Tools 2.2 for Visual Studio (Django és MySQL az Azure-ban, Python Tools 2.2 for Visual Studio alkalmazással)" 
    description="Megismerheti a Python Tools for Visual Studio használatát olyan Django-webalkalmazás létrehozásához, amely az adatokat a MySQL-adatbázis egy példányában tárolja és az Azure App Service Web Apps szolgáltatáson helyezi üzembe." 
    services="app-service\web" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python"
    ms.topic="get-started-article" 
    ms.date="07/07/2016"
    ms.author="huvalo"/>

# Django and MySQL on Azure with Python Tools 2.2 for Visual Studio (Django és MySQL az Azure-ban, Python Tools 2.2 for Visual Studio alkalmazással) 

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ebben az oktatóanyagban a [Python Tools for Visual Studio] (PTVS) alkalmazásával fog létrehozni egy egyszerű szavazási webalkalmazást az egyik PTVS-mintasablon használatával. Elsajátíthatja egy, az Azure-ban üzemeltetett MySQL-szolgáltatás használatát, a webalkalmazás a MySQL használatára való konfigurálását, valamint annak az [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) szolgáltatásban történő közzétételét.

> [AZURE.NOTE] Az ebben az oktatóanyagban szereplő információk az alábbi videóban is megtekinthetők:
> 
> [PTVS 2.1: Django app with MySQL] (PTVS 2.1: Django-alkalmazás és MySQL) [videó]

A [Python fejlesztői központban] találhat további, az Azure App Service Web Apps szolgáltatásának PTVS-sel történő fejlesztését ismertető cikkeket a Bottle, a Flask és a Django webes keretrendszerek használatával, olyan szolgáltatások esetében, mint az Azure Table Storage, a MySQL és az SQL Database. Az App Service-t tárgyaló jelen cikkben szereplő lépések hasonlóak az [Azure Cloud Services] fejlesztése esetében használtakhoz.

## Előfeltételek

 - Visual Studio 2015
 - [Python 2.7 32 bites] vagy [Python 3.4 32 bites]
 - [Python Tools 2.2 for Visual Studio]
 - [Python Tools 2.2 for Visual Studio Samples VSIX]
 - [Azure SDK Tools for VS 2015]
 - Django 1.9 vagy újabb

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [AZURE.NOTE] Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az Azure App Service kipróbálása](http://go.microsoft.com/fwlink/?LinkId=523751) oldalra, ahol azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.

## A projekt létrehozása

Ebben a szakaszban mintasablon használatával fog létrehozni Visual Studio-projektet. Létrehozza majd a virtuális környezetet, és telepíti a szükséges csomagokat. Az sqlite használatával létre fog hozni egy helyi adatbázist. Ezt követően az alkalmazást helyileg fogja futtatni.

1. A Visual Studio felületén válassza a **File** (Fájl), **New Project** (Új projekt) lehetőséget.

1. A [Python Tools 2.2 for Visual Studio Samples VSIX] projektsablonjai a **Python**, **Példák** elem alatt érhetők el. Válassza a **Polls Django Web Project** (Szavazási Django webes projekt) lehetőséget, majd kattintson az OK gombra a projekt létrehozásához.

    ![A New Project (Új projekt) párbeszédpanel](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1. A rendszer fel fogja kérni külső csomagok telepítésére. Válassza az **Install into a virtual environment** (Telepítés virtuális környezetbe) lehetőséget.

    ![Az External Packages (Külső csomagok) párbeszédpanel](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1. Alapszintű értelmezőként válassza ki a **Python 2.7** vagy **Python 3.4** alkalmazást.

    ![Az Add Virtual Environment (Virtuális környezet hozzáadása) párbeszédpanel](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1. A **Megoldáskezelő** felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza a **Python**, végül pedig a **Django Migrate** lehetőséget.  Ezután válassza a **Django Create Superuser** elemet.

1. Ekkor megnyílik a Django felügyeleti konzol, majd sqlite-adatbázis jön létre a projektmappában. Kövesse az utasításokat a felhasználó létrehozásához.

1. Az alkalmazás működőképességét az `F5` billentyű lenyomásával ellenőrizze.

1. Kattintson a felső rész navigációs sávján található **Log in** (Bejelentkezés) gombra.

    ![Django navigációs sáv](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1. Adja meg azon felhasználó hitelesítő adatait, amelyeket az adatbázis szinkronizálásakor hozott létre.

    ![Bejelentkezési űrlap](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1. Kattintson a **Create Sample Polls** (Mintaszavazások létrehozása) gombra.

    ![Create Sample Polls (Mintaszavazások létrehozása)](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1. Kattintson egy szavazásra, és szavazzon.

    ![Szavazás mintaszavazásokon](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## MySQL-adatbázis létrehozása

Az adatbázis tekintetében az Azure felületén létre fog hozni egy, a MySQL által üzemeltetett ClearDB adatbázist.

Másik lehetőségként létrehozhatja saját Azure-beli virtuális gépét, majd telepítheti és felügyelheti a MySQL-t.

Az alábbi lépéseket követve ingyenes csomaggal rendelkező adatbázist hozhat létre.

1. Jelentkezzen be az [Azure Portal].

1. A navigációs ablaktábla felső részén kattintson a **NEW** (ÚJ) > **Data + Storage** (Adatok + tárolás) > **MySQL Database** (MySQL-adatbázis) elemre. 

1. Konfigurálja az új MySQL-adatbázist új erőforráscsoport létrehozásával, majd válassza ki számára a megfelelő helyet.

1. A MySQL-adatbázis létrehozását követően kattintson az adatbázis paneljén a **Properties** (Tulajdonságok) lehetőségre.

1. A **CONNECTION STRING** (KAPCSOLATI KARAKTERLÁNC) vágólapra helyezéséhez használja a Copy (Másolás) gombot.

## A projekt konfigurálása

Ebben a szakaszban a webalkalmazást a most létrehozott MySQL-adatbázis használatára fogja konfigurálni. Emellett telepíteni fog olyan további Python-csomagokat, amelyek a MySQL-adatbázisok a Django alkalmazással történő használatához szükségesek. Ezt követően a webalkalmazást helyileg fogja futtatni.

1. A Visual Studio felületén nyissa meg a **settings.py** fájlt a *ProjectName* mappából. Ideiglenesen illessze be a kapcsolati karakterláncot a szerkesztőbe. A kapcsolati karakterlánc formátuma a következő:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Módosítsa az adatbázis alapértelmezett **ENGINE** értékét a MySQL használatára, majd állítsa be a **NAME**, a **USER**, a **PASSWORD** és a **HOST** paraméter értékét a **CONNECTIONSTRING** (KAPCSOLATI KARAKTERLÁNC) karakterláncból.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1. A Solution Explorer (Megoldáskezelő) **Python Environments** (Python-környezetek) területén kattintson a jobb gombbal a virtuális környezetre, majd válassza az **Install Python Package** (Python-csomag telepítése) lehetőséget.

1. Telepítse a `mysqlclient` csomagot a **pip** használatával.

    ![Az Install Package (Csomag telepítése) párbeszédpanel](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1. A **Megoldáskezelő** felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza a **Python**, végül pedig a **Django Migrate** lehetőséget.  Ezután válassza a **Django Create Superuser** elemet.

    Ekkor létrejönnek az előző szakaszban a MySQL-adatbázishoz létrehozott táblák. Kövesse az utasításokat a felhasználó létrehozásához. Ennek a felhasználónak nem kell megegyeznie a jelen cikk első szakaszában létrehozott felhasználóval, amely az sqlite-adatbázisban található.

1. Futtassa az alkalmazást az `F5` billentyű lenyomásával. A **Create Sample Polls** (Mintaszavazások létrehozása) szolgáltatással előállított szavazások, illetve a szavazás során elküldött adatok szerializálása a MySQL-adatbázisban történik.

## A webalkalmazás közzététele az Azure App Service szolgáltatásban

Az Azure .NET SDK egyszerű módot kínál a webalkalmazása az Azure App Service szolgáltatásban történő közzétételére.

1. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza a **Publish** (Közzététel) lehetőséget.

    ![A Publish Web (Webes közzététel) párbeszédpanel](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1. Kattintson a **Microsoft Azure App Service** lehetőségre.

1. A **New** (Új) gombra kattintva hozzon létre egy új webalkalmazást.

1. Töltse ki az alábbi mezőket, majd kattintson a **Create** (Létrehozás) gombra:
    - **A webalkalmazás neve**
    - **App Service-csomag**
    - **Erőforráscsoport**
    - **Régió**
    - Hagyja változatlanul a **Database server** (Adatbázis-kiszolgáló) **No database** (Nincs adatbázis) beállítását

1. Fogadja el az összes többi alapértelmezett értéket, majd kattintson a **Publish** (Közzététel) gombra.

1. A webböngészőjében automatikusan a közzétett webalkalmazás nyílik meg. Ekkor azt kell látnia, hogy a webalkalmazás a várt módon, az Azure által üzemeltetett **MySQL**-adatbázist használva működik.

    ![Webböngésző](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

    Gratulálunk! MySQL-alapú webalkalmazásának közzététele sikeresen megtörtént az Azure-ban.

## Következő lépések

A következő hivatkozásokat követve tudhat meg többet a Python Tools for Visual Studio-, a Django- és a MySQL.

- [A Python Tools for Visual Studio dokumentációja]
  - [Webes projektek]
  - [Cloud Service projektek]
  - [Remote Debugging on Microsoft Azure (Távoli hibakeresés a Microsoft Azure-ban)]
- [A Django dokumentációja]
- [MySQL]

További információ: [Python fejlesztői központban](/develop/python/).

<!--Link references-->

[Python fejlesztői központban]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->

[Azure Portal]: https://portal.azure.com
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 for Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 bites]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bites]: http://go.microsoft.com/fwlink/?LinkId=517191
[a Python Tools for Visual Studio dokumentációjában]: http://aka.ms/ptvsdocs
[Remote Debugging on Microsoft Azure (Távoli hibakeresés a Microsoft Azure-ban)]: http://go.microsoft.com/fwlink/?LinkId=624026
[Webes projektek]: http://go.microsoft.com/fwlink/?LinkId=624027
[Cloud Service projektek]: http://go.microsoft.com/fwlink/?LinkId=624028
[A Django dokumentációja]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[Videó]: http://youtu.be/oKCApIrS0Lo



<!--HONumber=sep16_HO1-->


