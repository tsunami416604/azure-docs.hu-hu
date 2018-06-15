---
title: A Windows Server Azure virtuális gép Django-webalkalmazás |} Microsoft Docs
description: Útmutató az Azure-ban a Windows Server 2012 R2 Datacenter virtuális Gépet a klasszikus üzembe helyezési modellel Django-alapú webhely üzemeltetésére.
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
ms.locfileid: "24057018"
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>A Django Hello World webes alkalmazás a Windows Server virtuális gép

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és a klasszikus üzembe helyezési modellel](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzembe helyezési modellben. Azt javasoljuk, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ez az oktatóanyag bemutatja, hogyan egy Django-alapú webhely a Windows Server Azure virtuális gépek üzemeltetéséhez. Az oktatóanyagban feltételezzük, hogy nincs korábbi tapasztalata kapcsolatban az Azure-ral. Az oktatóanyag befejezése után a Django-alapú alkalmazások és a felhőben futó lehet.

Az alábbiak végrehajtásának módját ismerheti meg:

* Állítson be egy Azure virtuális gép gazdagépre Django. Bár ez az oktatóanyag azt ismerteti, hogyan erre a **Windows Server**, akkor is képes ugyanerre az Azure szolgáltatásban üzemeltetett Linux virtuális gép.
* A Windows egy új Django-alkalmazás létrehozása

Az oktatóanyag bemutatja, hogyan hozhat létre egy alapszintű Hello World webalkalmazáshoz. Az alkalmazás üzemel egy Azure virtuális gépen.

Az alábbi képernyőfelvételen a kész alkalmazás látható:

![Egy böngészőablakban a hello world lap megjeleníti az Azure-ban][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Hozzon létre, és állítson be egy Azure virtuális gép gazdagépre Django

1. Azure virtuális gép létrehozása a Windows Server 2012 R2 Datacenter eloszláshoz: [hozzon létre egy olyan virtuális géphez a Windows Azure-portálon](tutorial.md).
2. Port 80 forgalmát a webes 80-as portot a virtuális gépen való közvetlen Azure beállítása:
   
   1. Az Azure portálon az irányítópult megnyitásához, és válassza ki az újonnan létrehozott virtuális gépet.
   2. Kattintson a **Végpontok**, majd a **Hozzáadás** lehetőségre.

     ![Végpont hozzáadása](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. Az a **végpont hozzáadása** lap, a **neve**, adja meg **HTTP**. A nyilvános és titkos TCP csak portot adjon meg **80**.

     ![Adjon meg nevet és a nyilvános és magánhálózati portot adjon meg](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Kattintson az **OK** gombra.
     
3. Az irányítópulton válassza ki a virtuális Gépet. Remote Desktop Protocol (RDP) használatával távolról jelentkeznek be az újonnan létrehozott Azure virtuális géphez, kattintson a **Connect**.  

> [!IMPORTANT] 
> Az alábbi utasítások azt feltételezik, hogy jelentkezett be a virtuális gép megfelelően. Akkor is azt feltételezik, hogy vannak-e kiadása parancsok a virtuális gépen, és nem a helyi számítógépen.

## <a id="setup"></a>Python, a Django és WFastCGI telepítése
> [!NOTE]
> Letöltése Internet Explorerrel, lehetséges, hogy az Internet Explorer konfigurálása **fokozott biztonsági beállítások** beállításait. Ehhez kattintson **Start** > **felügyeleti eszközök** > **Kiszolgálókezelő** > **helyi kiszolgáló**. Kattintson a **Internet Explorer fokozott biztonsági beállítások**, majd válassza ki **ki**.

1. Telepítse a legújabb verzióit Python 2.7-es vagy a Python 3.4-es [python.org][python.org].
2. A pip használatával wfastcgi és a django csomagok telepítése.
   
    Python 2.7 esetén használja a következő parancsot:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Python 3.4 esetén használja a következő parancsot:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Telepítse az IIS FastCGI
* Internet Information Services (IIS) telepítése a FastCGI-támogatással rendelkező. Ennek végrehajtása több percig is eltarthat.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Hozzon létre egy új Django-alkalmazáshoz
1. C:\inetpub\wwwroot egy új Django-projekt létrehozásához adja meg a következő parancsot:
   
   Python 2.7 esetén használja a következő parancsot:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Python 3.4 esetén használja a következő parancsot:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![A New-AzureService parancs eredménye](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. A `django-admin` parancs létrehoz egy alapszintű struktúrát Django-alapú webhelyekhez:
   
   * `helloworld\manage.py`segítségével indíthatja el, és állítsa le a Django-alapú webhely üzemeltetéséhez.
   * `helloworld\helloworld\settings.py`az alkalmazás Django-beállításokkal rendelkezik.
   * `helloworld\helloworld\urls.py`a leképezési kódot valamennyi URL-címet és a nézet között van.
3. A C:\inetpub\wwwroot\helloworld\helloworld könyvtárban hozzon létre egy új fájlt views.py. Ez a fájl rendelkezik a nézet jeleníti meg az a "hello world" lapon. A kód szerkesztése adja meg a következő parancsokat:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Cserélje le a urls.py fájl tartalmát a következő parancsokat:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>IIS beállítása
1. A globális applicationhost.config fájlban a kezelők szakasz zárolásának feloldása.  Ez lehetővé teszi, hogy a web.config fájlt a Python-kezelő használatával. Adja hozzá ezt a parancsot:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. WFastCGI aktiválása. Ez biztosítja, hogy az alkalmazás a globális applicationhost.config fájlt, amely a Python parancsértelmező végrehajtható és a wfastcgi.py parancsfájl hivatkozik.
   
    A Python 2.7 esetén:
   
        C:\python27\scripts\wfastcgi-enable
   
    A Python 3.4 esetén:
   
        C:\python34\scripts\wfastcgi-enable
3. C:\inetpub\wwwroot\helloworld hozzon létre egy web.config fájlt. Értékét a `scriptProcessor` attribútumot meg kell felelnie, hogy a kimenet az előző lépésben. A wfastcgi beállítással kapcsolatos további információkért lásd: [pypi wfastcgi][wfastcgi].
   
   A Python 2.7 esetén:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
   A Python 3.4 esetén:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Frissítse az IIS alapértelmezett webhelye, hogy a Django-projekt mappára helyét:
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. A böngészőben a képernyőn látható weblapon betölteni.

![Egy böngészőablakban a hello world oldalát jeleníti meg az Azure-on][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Az Azure virtuális gép leállítása
Amikor elkészült, az oktatóanyaghoz, azt javasoljuk, hogy állítsa le vagy távolítsa el a az oktatóanyaghoz létrehozott Azure virtuális Gépet. Ezzel felszabadul erőforrások más oktatóanyagok, és elkerülheti a nélül Azure hálózathasználati költségeket.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
