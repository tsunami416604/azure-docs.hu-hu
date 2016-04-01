<properties
    pageTitle="搭配 Django 的 Python Web 應用程式 | Microsoft Azure"
    description="本教學課程會教您如何藉由傳統部署模型使用執行 Windows Server 2012 R2 Datacenter 的虛擬機器，在 Azure 上裝載 Django 型網站。"
    services="virtual-machines"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# Windows Server VM 上的 Django Hello World Web 應用程式

> [AZURE.SELECTOR]
- [Windows](virtual-machines-python-django-web-app-windows-server.md)
- [Mac/Linux](virtual-machines-python-django-web-app-linux.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。
 

本教學課程說明如何使用 Windows Server 虛擬機器在 Microsoft
Azure 裝載 Django 型網站。 本教學課程假設您先前沒有使用 Azure 的經驗。 完成本教學課程之後，您將在雲端啟動並執行 Django 型應用程式。

您將了解如何：

* 設定 Azure 虛擬機器以裝載 Django。 雖然本教學課程說明如何在 Windows Server 下完成這項程序，不過使用 Azure 中裝載的 Linux VM 也可以完成相同的程序。
* 從 Windows 建立新的 Django 應用程式。

依照本教學課程的步驟，您將建置一個簡單的 Hello World Web
重新接收。 該應用程式將裝載於 Azure 虛擬機器中。

已完成應用程式的螢幕擷取畫面如下。

![A browser window displaying the hello world page on Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## 建立及設定 Azure 虛擬機器以裝載 Django

1. 請遵循所提供的指示 [這裡](virtual-machines-windows-tutorial-classic-portal.md) 來建立 Windows Server 2012 R2 Datacenter 散發套件的 Azure 虛擬機器。

1. 指示 Azure 將連接埠 80 的流量從 Web 導向虛擬機器上的連接埠 80：
 - 巡覽至新建立的虛擬機器在 Azure 傳統入口網站中，按一下 **端點** ] 索引標籤。
 - 按一下 [ **新增** 在畫面底部的按鈕。
    ![加入端點](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - 開啟 **TCP** 通訊協定的 **公用連接埠 80** 為 **私人連接埠 80**。
![][port80]
1. 從 **儀表板** 索引標籤上，按一下 [ **連接** 使用 **遠端桌面** 從遠端登入新建立的 Azure 虛擬機器。  

**重要事項 ︰** 以下所有指示皆假設您已登入虛擬機器正確，並且會發出命令，而不是本機電腦。

## <a id="setup"></a>安裝 Python、Django 和 WFastCGI

**附註 ︰** 以便下載使用 Internet Explorer，您可能需要設定 IE ESC 設定 (開始/系統管理工具] / [伺服器管理員] / [本機伺服器，然後按一下 [  **IE 增強式安全性設定**, ，將其設為 Off)。

1. 安裝最新的 Python 2.7 或 3.4 從 [python.org][]。
1. 使用 pip 安裝 wfastcgi 和 django 封裝。

    針對 Python 2.7，請使用下列命令。

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    針對 Python 3.4，請使用下列命令。

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## 安裝含 FastCGI 的 IIS

1. 安裝含 FastCGI 支援的 IIS。  執行的時間可能需要幾分鐘。

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## 建立新的 Django 應用程式

1.  從 *C:\inetpub\wwwroot*, ，輸入下列命令以建立新的 Django 專案 ︰

    針對 Python 2.7，請使用下列命令。

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    針對 Python 3.4，請使用下列命令。

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![The result of the New-AzureService command](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

1.   **Django 管理** 命令會產生 Django 型網站的基本結構 ︰

  -   **helloworld\ manage.py** 可協助您開始裝載及停止裝載 Django 型網站
  -   **helloworld\helloworld\settings.py** 包含您的應用程式的 Django 設定。
  -   **helloworld\helloworld\urls.py** 包含每個 url 與其檢視之間的對應碼。

1.  建立新的檔名為 **views.py** 中 *C:\inetpub\wwwroot\helloworld\helloworld* 目錄。 這將包含轉譯 "hello world" 頁面的檢視。 啟動您的編輯器並輸入下列程式碼：

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  使用下列程式碼取代 urls.py 檔案的內容。

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## 設定 IIS

1. 在全域 applicationhost.config 中解除鎖定處理常式區段。  這會讓 web.config 中的 python 處理常式可供使用。

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. 啟用 WFastCGI。  這會將應用程式新增至全域 applicationhost.config，其中表示您的 Python 解譯器可執行檔和 wfastcgi.py 指令碼。

    Python 2.7：

        c:\python27\scripts\wfastcgi-enable

    Python 3.4：

        c:\python34\scripts\wfastcgi-enable

1. 建立 web.config 檔中的 *C:\inetpub\wwwroot\helloworld*。  `scriptProcessor` 屬性的值應該符合上一個步驟的輸出。  請參閱頁面 [wfastcgi][] pypi 如需詳細的 wfastcgi 設定上。

    Python 2.7：

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

    Python 3.4：

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

1. 更新 IIS 預設網站的位置，以指向 django 專案資料夾。

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. 最後，在您的瀏覽器中載入網頁。

![A browser window displaying the hello world page on Azure][1]


## 關閉 Azure 虛擬機器

完成本教學課程時，請關閉和/或移除新建立的 Azure 虛擬機器以便釋出資源供其他教學課程使用，同時避免產生使用 Azure 的費用。

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi


