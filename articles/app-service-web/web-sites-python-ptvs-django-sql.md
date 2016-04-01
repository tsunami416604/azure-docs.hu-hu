<properties 
    pageTitle="Azure 上使用 Python Tools 2.2 for Visual Studio 的 Django 和 SQL Database" 
    description="了解如何使用 Python Tools for Visual Studio 建立 Django Web 應用程式，藉此將資料儲存在 SQL Database 執行個體中，並部署到 Azure App Service Web Apps。" 
    services="app-service\web" 
    tags="python"
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/13/2015"
    ms.author="huguesv"/>




# Azure 上使用 Python Tools 2.2 for Visual Studio 的 Django 和 SQL Database 

在本教學課程中，我們將使用 [Python Tools for Visual Studio] 來建立簡單的民調 web 應用程式使用其中一個 PTVS 範例範本。 本教學課程也會提供做為 [視訊](https://www.youtube.com/watch?v=ZwcoGcIeHF4)。

我們將學習如何使用 Azure 上代管的 SQL 資料庫、 如何設定 web 應用程式以使用 SQL 資料庫，以及如何將 web 應用程式發佈 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。

請參閱 [Python Developer Center] 如需文件開發的 Azure 應用程式服務 Web 應用程式使用 PTVS 使用 Bottle、 Flask 和 Django web 架構，來使用 MongoDB、 Azure 資料表儲存體、 MySQL 和 SQL 資料庫的服務。 雖然本文著重於應用程式服務，開發時的步驟很類似 [Azure Cloud Services]。

## 必要條件

 - Visual Studio 2013 或 2015
 - [Python Tools 2.2 for Visual Studio]
 - [Python Tools 2.2 for Visual Studio 範例 VSIX]
 - [Azure SDK Tools for VS 2013] 或 [Azure SDK Tools for VS 2015]
 - [Python 2.7 (32 位元)]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 建立專案

在這一節中，我們將使用範例範本建立 Visual Studio 專案。 我們將建立虛擬環境並安裝必要的套件。 我們將使用 sqlite 建立本機資料庫。 接著，在本機執行 Web 應用程式。

1.  在 Visual Studio 中，選取 **檔案**, ，**新的專案**。

1.  之下可取得 PTVS 範例 VSIX 專案範本中 **Python**, ，**範例**。 選取 **Polls Django Web Project** 按一下 [確定] 以建立專案。

    ![新專案對話](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  系統會提示您安裝外部套件。 選取 **虛擬環境安裝**。

    ![外部套件對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  選取 **Python 2.7** 做為基底解譯器。

    ![新增虛擬環境對話方塊](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  以滑鼠右鍵按一下專案節點，然後選取 **Python**, ，**Django Sync DB**。

    ![Django Sync DB 命令](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  此舉會開啟 Django 管理主控台。 依照提示建立使用者。

    這會在專案資料夾中建立 sqlite 資料庫。

    ![Django 管理主控台視窗](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  按 <kbd>F5</kbd> 確認應用程式可運作。

1.  按一下 [ **登入** 從頂端導覽列。

    ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  為您在同步處理資料庫時建立的使用者輸入認證。

    ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  按一下 [ **Create Sample Polls**。

    ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  按一下民調並進行投票。

    ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## 建立 SQL Database

對於資料庫，我們將建立 Azure SQL 資料庫。

您可以依照下列步驟來建立資料庫。

1.  登入 [Azure Portal]。

1.  在導覽窗格的底部，按一下 [ **新增**。 按一下 [ **資料 + 儲存體** > **SQL Database**。

    <!-- ![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png) -->

1.  設定新的 SQL Database，做法是建立新的資源群組，然後為其選取一個適當的位置。

    <!-- ![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png) -->

1.  一旦建立 SQL 資料庫時，按一下 [ **Visual Studio 中開啟** 資料庫刀鋒視窗中。
2.  按一下 [ **設定您的防火牆**。
3.  在 **防火牆設定** 刀鋒視窗中，新增下列防火牆規則 **起始 IP** 和 **結束 IP** 設為您的開發電腦的公用 IP 位址。 按一下 [ **儲存**。

    這樣可讓您從開發電腦連線至資料庫伺服器。

4.  在 [資料庫] 刀鋒視窗中，按一下 **屬性**, ，然後按一下 [ **顯示資料庫連接字串**。 

2.  使用 [複製] 按鈕的值放 **ADO.NET** 剪貼簿上。

## 設定專案

本節中，我們將設定 web 應用程式使用 SQL 資料庫 
。 我們也將安裝搭配使用 SQL 資料庫與 Django 時 
所需的其他 Python 封裝。 接著，在本機執行 Web 應用程式。

1.  在 Visual Studio 中開啟 **settings.py**, ，從 *ProjectName* 資料夾。 暫時在編輯器中貼上連接字串。 連接字串的格式如下：

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

使用上述值編輯 `DATABASES` 的定義。

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1.  在 [方案總管在 **Python 環境**, ，以滑鼠右鍵按一下虛擬環境，然後選取 **安裝 Python 封裝**。

1.  安裝封裝 `pyodbc` 使用 **pip**。

    ![安裝 Python 封裝對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  安裝封裝 `django-pyodbc-azure` 使用 **pip**。

    ![安裝 Python 封裝對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  以滑鼠右鍵按一下專案節點，然後選取 **Python**, ，**Django Sync DB**。 

    此舉會為我們在上一節中建立的 SQL Database 建立資料表。 依照提示建立使用者，該使用者不需符合在第一節中建立之 sqlite 資料庫中的使用者。

    ![Django 管理主控台視窗](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  使用 `F5` 執行應用程式。 用來建立的民調 **Create Sample Polls** 以及投票所提交的資料將會序列化為 SQL 資料庫中。


## 將 Web 應用程式發佈至 Azure App Service

Azure .NET SDK 提供簡單的方法將 Web 應用程式部署至 Azure App Service Web Apps。

1.  在 **方案總管] 中**, ，以滑鼠右鍵按一下專案節點，然後選取 **發行**。

    <!-- ![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png) -->

1.  按一下 [ **Microsoft Azure Web Apps**。

1.  按一下 [ **新增** 建立新的 web 應用程式。

1.  填寫下列欄位，然後按一下 **建立**。
    -   **Web 應用程式名稱**
    -   **App Service 方案**
    -   **資源群組**
    -   **區域**
    -   保留 **資料庫伺服器** 設 **沒有資料庫**

    <!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png) -->

1.  接受所有其他預設值，然後按一下 [ **發行**。

1.  您的 Web 瀏覽器將會自動開啟到已發佈的 Web 應用程式。 您應該會看到 web 應用程式運作不如預期，使用 **SQL** Azure 上裝載的資料庫。

    恭喜！

    ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## 後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Django 和 SQL Database。

- [Python Tools for Visual Studio 說明文件]
  - [Web 專案]
  - [雲端服務專案]
  - [在 Microsoft Azure 上進行遠端偵錯]
- [Django 說明文件]
- [SQL Database]

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 for Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Remote Debugging on Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Web Projects]: http://go.microsoft.com/fwlink/?LinkId=624027
[Cloud Service Projects]: http://go.microsoft.com/fwlink/?LinkId=624028
[Django Documentation]: https://www.djangoproject.com/
[SQL Database]: /documentation/services/sql-database/


