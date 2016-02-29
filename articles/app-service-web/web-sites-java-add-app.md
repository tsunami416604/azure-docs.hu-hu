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

一旦您初始化 Java web 應用程式 [Azure App Service] [] 中的所述在 [Azure App Service 中建立 Java web 應用程式](web-sites-java-get-started.md), ，可以將 WAR 放上傳您的應用程式 **webapps** 資料夾。

導覽路徑 **webapps** 資料夾根據您設定您的 Web 應用程式執行個體的方式而有所不同。

- 如果您已設定 web 應用程式使用 Azure Marketplace 的路徑 **webapps** 資料夾位於表單 **d:\home\site\wwwroot\bin\application\_server\webapps**, ，其中 **application\_server 會** 是應用程式伺服器的作用中您 Web 應用程式執行個體的名稱。 
- 如果您已設定 web 應用程式使用 Azure 組態 UI 的路徑 **webapps** 資料夾位於表單 **d:\home\site\wwwroot\webapps**。 

請注意，您可以使用原始檔控制來上傳應用程式或網頁，包括連續整合案例。 使用原始檔控制與 web 應用程式的指示位於 [Azure App Service 中使用 GIT 連續部署](web-sites-publish-source-control.md)。 FTP 也是一個可上傳應用程或網頁的選項。

Tomcat web 應用程式注意事項: 上傳您的 WAR 檔案將 **webapps** 資料夾之後，Tomcat 應用程式伺服器會偵測您已新增它，並自動將其載入。 請注意，如果您將檔案 (WAR 檔案除外) 複製到根目錄，則在使用這些檔案之前，必須重新啟動應用程式伺服器。 在 Azure 上執行的 Tomcat Java web 應用程式的自動載入功能根據新的 WAR 檔案，或新檔案或目錄加入至 **webapps** 資料夾。 

## 後續步驟

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714

