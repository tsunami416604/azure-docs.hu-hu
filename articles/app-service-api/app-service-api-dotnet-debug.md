<properties 
    pageTitle="偵錯 Azure App Service 中的 API 應用程式" 
    description="了解如何在 API 應用程式於 Azure App Service 執行時使用 Visual Studio 進行偵錯。" 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="bradygaster" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/08/2015" 
    ms.author="tdykstra"/>

# 偵錯 Azure App Service 中的 API 應用程式

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## 概觀

在本教學課程中，偵錯設定為執行的 ASP.NET Web API 程式碼 [API 應用程式](app-service-api-apps-why-best-platform.md) 中 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。 您會在本機執行和在 Azure 中遠端執行程式碼時，對程式碼進行偵錯。 

本教學課程會使用 API 應用程式您 [建立](app-service-dotnet-create-api-app.md) 和 [部署](app-service-dotnet-deploy-api-app.md) 先前的教學課程，本系列中。

## 遠端偵錯 API 應用程式 

若要啟用遠端偵錯，您必須將偵錯組建部署至 Azure。

1. 在 Visual Studio **方案總管] 中**, ，以滑鼠右鍵按一下您在部署專案 [先前的教學課程](app-service-dotnet-deploy-api-app.md), ，然後選取 **發行**。

2. 在 **發行 Web** 對話方塊中，選取 **設定** 索引標籤並選取 **偵錯** 組建組態。

4. 按一下 [ **發行**。

    ![發佈專案](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

    瀏覽器視窗會開啟至您的 API 應用程式的基底 URL。

4. 在瀏覽器位址列中，將 /swagger 加到 URL 的結尾，然後按 Enter 鍵。 

    這個步驟假設您啟用 Swagger UI 中的指示 [建立](app-service-dotnet-create-api-app.md) 教學課程。

    ![Swagger UI](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. 回到 Visual Studio 中，然後按一下從 **檢視 > 伺服器總管**。 

6. 在 **伺服器總管**, ，依序展開 **Azure > App Service** 節點。 

7. 尋找您在部署 API 應用程式時所建立的資源群組。 

8. 在資源群組，以滑鼠右鍵按一下 [API 應用程式] 節點，然後選取 **附加偵錯工具**。 

    ![附加偵錯工具](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

    遠端偵錯工具會嘗試連線。 在某些情況下，您可能需要重試按一下 [ **附加偵錯工具** 建立連線，因此如果失敗，再試一次。

    ![附加偵錯工具](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. 建立連線之後，請開啟 **ContactsController.cs** 檔案在 API 應用程式專案中，並新增中斷點 `Get` 方法。

    ![將中斷點套用至控制器](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. 返回瀏覽器工作階段，請按一下 **取得** 顯示的結構描述的動詞命令 *連絡人* 物件，然後再按一下 **試試看**。 Visual Studio 會在您的中斷點上停止程式執行，且您可以對控制器的邏輯進行偵錯。 

    ![立即試用](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## 在本機上對 API 應用程式進行偵錯 

您有時可能想要在本機上對 API 應用程式進行偵錯；例如，如果 Azure 伺服器的來回行程讓偵錯變慢。 本節說明如何偵錯在本機使用 Swagger UI 做為測試用戶端的 API 應用程式。

2. 在瀏覽器中瀏覽至 [Azure 預覽入口網站](https://portal.azure.com)。 

3. 按一下 [ **瀏覽** 資訊看板]，然後選取按鈕 **API 應用程式**。 

    ![瀏覽 Azure 入口網站上的選項](./media/app-service-api-dotnet-debug/ld-browse.png)

4. 在訂用帳戶的 API 應用程式清單中，選取您建立的 API 應用程式。

    ![API 應用程式清單](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. 在 [API 應用程式的刀鋒視窗中，按一下 [ **API 應用程式主機**。

    ![API 應用程式主機](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. 在 [API 應用程式主機刀鋒視窗中，按一下 [ **所有設定**。

    ![API 應用程式主機的所有設定](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. 在 **設定** 刀鋒視窗中，按一下 [ **應用程式設定**。

    ![API 應用程式主機的應用程式設定](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. 在 **Web 應用程式設定** 刀鋒視窗中，向下捲動至 **應用程式設定** 一節。

    ![API 應用程式主機針對本機偵錯的應用程式設定](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

1. 在 Visual Studio 中開啟 API 應用程式專案 *web.config* 檔案。 

9. 從 **應用程式設定**, ，新增下列索引鍵和值的每個 *web.config* 檔案的 **appSettings** 一節。
    - **EMA\_MicroserviceId**
    - **EMA\_Secret**
    - **EMA\_RuntimeUrl**

    當您完成時， **appSettings** 區段您 *web.config* 應該類似下列螢幕擷取畫面。

    ![API 應用程式主機針對本機偵錯的應用程式設定](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

    **注意 ︰**  *EMA_* 值加入至您 *web.config* 本節中的檔案包含機密的授權資訊。 因此，建議您避免在公開原始檔控制儲存機制中儲存這些值。 如需詳細資訊，請參閱 [的密碼和其他機密資料部署至 ASP.NET 和 Azure App Service 最佳作法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)。   

10. 將 API 應用程式的控制器程式碼中的一個中斷點，放到 `Get` 方法中。

11. 按 F5 啟動 Visual Studio 偵錯工作階段。
 
13.  若要將 API 應用程式的存取等級設為 **公用 （匿名）**, ，您可以使用 Swagger UI 頁面來測試。

    * When the browser loads the page, you see an HTTP 403 error page. Add */swagger* to the end of the URL in your browser's address bar and press Enter.

    * Once the Swagger UI has loaded, click the **Get** verb on the browser window to display the schema for the Contact object, and then click **Try it Out**.

        Visual Studio stops program execution on your breakpoint, and you can debug your controller's logic. 

14. 如果 API 應用程式的存取層級設定為 **公用 （驗證）**, ，您需要驗證，並使用下列程序中所示的瀏覽器工具 [保護 API 應用程式](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) 對於 Post 要求，這是 ︰

    * 移至閘道器登入 URL，並輸入認證進行登入。
    * 從 x-zumo-auth Cookie 取得 Zumo Token 值。
    * 將 x-zumo-auth 標頭加入您的要求，並將其值設定為 x-zumo-auth Cookie 值。
    * 提交要求。

    **注意 ︰** 當您在本機執行時，Azure 無法控制的存取權的 API 應用程式，以確保只有已驗證的使用者可以執行其方法。 當您在 Azure 中執行時，適用於 API 應用程式的所有流量都會透過閘道器路由，而且閘道器不會傳遞未經驗證的要求。 當您在本機執行時，不會進行任何重新導向，這表示會禁止未驗證的要求存取 API 應用程式。  如上所述驗證的值是您可以在 API 應用程式中成功執行驗證相關程式碼 (例如，擷取已登入使用者相關資訊的程式碼)。 如需閘道如何處理驗證的 API 應用程式的詳細資訊，請參閱 [API 應用程式和行動應用程式驗證](../app-service/app-service-authentication-overview.md#azure-app-service-gateway)。

## 後續步驟

在本教學課程中，您已了解如何偵錯 API Apps。 

如需詳細的疑難排解資訊，請參閱 [疑難排解 Azure App Service 使用 Visual Studio 中的 web 應用程式](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)。 因為 API 應用程式是具有額外功能 (用於裝載 Web 服務) 的 Web 應用程式，所以您可對 API 應用程式使用您對 Web 應用程式使用的相同偵錯和疑難排解工具。    


