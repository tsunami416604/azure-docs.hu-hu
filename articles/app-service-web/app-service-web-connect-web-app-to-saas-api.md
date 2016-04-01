<properties 
    pageTitle="在 Azure App Service 中將  Web 應用程式連接至 API 應用程式" 
    description="本教學課程示範如何透過 Azure App Service 中裝載的 ASP.NET Web 應用程式使用 API 應用程式。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="syntaxc4" 
    manager="yochayk" 
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="09/15/2015"
    ms.author="cfowler"/>

# 在 Azure App Service 中將  Web 應用程式連接至 API 應用程式

本教學課程示範如何使用 API 應用程式中裝載 ASP.NET web 應用程式 [應用程式服務](../app-service.md)。

## 必要條件

本教學課程是根據 API 應用程式教學課程系列所建置：

1. [建立 Azure API 應用程式](../app-service-dotnet-create-api-app)
3. [部署 Azure API 應用程式](../app-service-dotnet-deploy-api-app)
4. [偵錯 Azure API 應用程式](../app-service-dotnet-remotely-debug-api-app)

## 將 API 應用程式設為可公開存取

在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715), ，選取 API 應用程式。 按一下 [ **設定** 命令列中的按鈕。 在 **應用程式設定** 刀鋒視窗中，變更 **存取層級** 至 **公用 （匿名）**。

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## 在 Visual Studio 中建立 ASP.NET MVC 應用程式

1. 開啟 Visual Studio。 使用 **新的專案** ] 對話方塊，加入新 **ASP.NET Web 應用程式**。 按一下 [ **確定**。

    ![檔案 > 新增 > Web > ASP.NET Web 應用程式](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. 選取 **MVC** 範本。 按一下 [ **變更驗證**, ，然後選取 **不需要驗證**, ，然後按一下 [ **確定** 兩次。

    ![新增 ASP.NET 應用程式](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. 在 [方案總管] 中，以滑鼠右鍵按一下新建立的 Web 應用程式專案，然後選取 **加入 Azure 應用程式參考**。

    ![新增 Azure API 應用程式參考...](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. 在 **現有 API 應用程式** 下拉式清單中，選取您想要連接到 API 應用程式。

    ![選取現有的 API 應用程式](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

    >[AZURE.NOTE] 連接到 API 應用程式的用戶端程式碼將會從 Swagger API 端點自動產生。

1. 若要利用產生的 API 程式碼，請開啟 HomeController.cs 檔案，並以下列程式碼取代 `Contact` 動作：

        public async Task<ActionResult> Contact()
        {
            ViewBag.Message = "Your contact page.";
    
            var contacts = new ContactsList();
            var response = await contacts.Contacts.GetAsync();
            var contactList = response.Body;
    
            return View(contactList);
        }

    ![HomeController.cs 程式碼更新](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. 使用下面的程式碼來更新 `Contact` 檢視，以反映連絡人動態清單：  
    <pre>// Add to the very top of the view file
    @model IList & l t;MyContactsList.Web.Models.Contact & gt;
    
    // Replace the default email addresses with the following
    & l t; h3 & gt;公用連絡人 & l t; / h 3 & gt;
    & lt; ul & gt;
        @foreach (var contact in Model)
        {
            & lt; li & gt; lt; a = & quot;mailto:@contact。EmailAddress & q u o t; & gt;@contact。名稱 & amp;lt;@contact。電子郵件位址 （& s) amp; gt; & lt; /a gt; & lt; / l i & gt;
        }
    & l t; / u l & gt; 
    </pre>

    ![Contact.cshtml 程式碼更新](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## 將 Web 應用程式部署到應用程式服務中的 Web 應用程式

請依照下列指示 [如何部署 Azure web 應用程式](web-sites-deploy.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
 


