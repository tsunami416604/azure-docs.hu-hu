<properties 
    pageTitle="將 Java 應用程式新增至 Azure App Service Web Apps" 
    description="本教學課程說明如何將頁面或應用程式新增至已設定為使用 Java 的 Azure App Service Web Apps 執行個體。" 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="robmcm"/>


# 將 Java 應用程式新增至 Azure App Service Web Apps

初始化中的 Java web 應用程式之後 [Azure 應用程式服務]][] 所述在 [Azure App Service 中建立 Java web 應用程式](web-sites-java-get-started.md), ，可以將 WAR 放上傳您的應用程式 **webapps** 資料夾。

**webapps** 資料夾的導覽路徑會根據 Web Apps 執行個體的設定方式而有所不同。

- 如果您已設定 web 應用程式使用 Azure Marketplace 的路徑 **webapps** 資料夾位於表單 **d:\home\site\wwwroot\bin\application\_server\webapps**, ，其中 **application\_server 會** 是應用程式伺服器的作用中您 Web 應用程式執行個體的名稱。
- 如果您使用 Azure 組態 UI 來設定 Web 應用程式，則 **webapps** 資料夾的路徑會是 **d:\home\site\wwwroot\webapps** 的格式。

請注意，您可以使用原始檔控制來上傳應用程式或網頁，包括連續整合案例。 使用原始檔控制與 web 應用程式的指示位於 [Azure App Service 中使用 GIT 連續部署](web-sites-publish-source-control.md)。 FTP 也是一個可上傳應用程或網頁的選項。

Tomcat Web 應用程式注意事項：將 WAR 檔案上傳至 **webapps** 資料夾之後，Tomcat 應用程式伺服器便會偵測到您已新增該檔案，並會將其自動載入。 請注意，如果您將檔案 (WAR 檔案除外) 複製到根目錄，則在使用這些檔案之前，必須重新啟動應用程式伺服器。 在 Azure 上執行的 Tomcat Java Web 應用程式，其自動載入功能會視新增的 WAR 檔案或新增至 **webapps** 資料夾的新檔案或目錄而定。

## 後續步驟

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]



[azure app service]: http://go.microsoft.com/fwlink/?LinkId=529714 

