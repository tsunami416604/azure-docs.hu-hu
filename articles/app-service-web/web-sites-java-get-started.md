<properties
    pageTitle="在 Azure App Service 中建立 Java Web 應用程式 | Microsoft Azure"
    description="本教學課程示範如何將 Java Web 應用程式部署至 Azure App Service。"
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
    ms.topic="hero-article"
    ms.date="10/20/2015"
    ms.author="robmcm"/>

# 在 Azure App Service 中建立 Java Web 應用程式

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP-Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP-FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

本教學課程示範如何建立 Java [Azure App Service 中的 web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714) 使用 [Azure 入口網站](https://portal.azure.com/)。 Azure 入口網站是可用來管理 Azure 資源的 Web 介面。

> [AZURE.NOTE] 若要完成本教學課程，您需要 Microsoft Azure 帳戶。 如果您沒有帳戶，您可以 [啟動 Visual Studio 的訂閱者優惠] [] 或 [註冊免費試用] []。
>
> 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service][]。 您可以於該處，在 App Service 中立即建立短期的入門 Web app - 不需信用卡，不需任何承諾。

## Java 應用程式選項

有數種方式可讓您在 App Service Web 應用程式中設定 Java 應用程式。 

1. 使用來自 Azure Marketplace 的範本。

    Azure Marketplace 包含數個範本，其可使用 Tomcat 或 Jetty Web 容器自動建立及設定 Java Web 應用程式。 設定範本的 Web 容器是可設定的。 如需詳細資訊，請參閱 [從 Azure Marketplace 使用 Java 範本](#marketplace) 本教學課程的區段。
 
1. 建立應用程式，然後設定 **應用程式設定**。

    App Service 提供數個 Tomcat 或 Jetty 版本 (包含預設組態)。 如果您將託管的應用程式會使用其中一個內建版本，這就是設定 Web 容器的最簡單方法，但是它缺乏其他方法中的設定功能。 這個方法，在 Azure 入口網站中，建立應用程式，然後移至應用程式的 **應用程式設定** 刀鋒視窗中選擇的 Java 的版本，以及所需的 Java web 容器。 當您使用這個方法時，應用程式會從背景工作角色用來裝載應用程式的本機硬碟機執行，而不會佔用租用戶的磁碟空間。 當您使用此模型時，您沒有存取檔案系統中，表示您不能進行類似設定作業的這個部分中編輯檔案 *server.xml* 檔案或程式庫檔案中的放置 */lib* 資料夾。  如需詳細資訊，請參閱 [建立及設定 Java web 應用程式](#appsettings) 本教學課程稍後的章節。  
  
3. 建立應用程式，然後手動複製並編輯組態檔 

    您可能想要裝載不會部署在 App Service 所提供的任何 Web 容器中的自訂 Java 應用程式。  例如，以下是這麼做的一些原因：
    
    * Java 應用程式需要 App Service 未直接支援或資源庫不提供的 Tomcat 或 Jetty 版本。
    * Java 應用程式會接受 HTTP 要求，但不會以 WAR 形式部署到預先存在的 Web 容器中。
    * 您想要自行從頭設定 Web 容器。 
    * 您想要使用 App Service 所不支援的 Java 版本並自行將它上傳。

    對於這種案例來說，您可以使用 Azure 入口網站建立應用程式，然後以手動提供適當的執行階段檔案。 在此情況下，會針對您的 App Service 計畫的儲存空間配額計算檔案。 如需詳細資訊，請參閱 [將自訂 Java web 應用程式上傳至 Azure](https://acom-sandbox.azurewebsites.net/en-us/documentation/articles/web-sites-java-custom-upload/)。

## <a name="marketplace"></a> 從 Azure Marketplace 使用 Java 範本

本節說明如何使用 Azure Marketplace 來建立 Java Web 應用程式。  相同的一般流程也可以用來建立 Java 型行動或 API 應用程式。  

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [ **新 > 服務商場**。

    ![](./media/web-sites-java-get-started/newmarketplace.png)

3. 按一下 [ **Web + 行動**。

    您可能必須向左捲動以查看 **Marketplace** ] 刀鋒視窗中，您可以在其中選取 **Web + 行動**。

4. 在搜尋文字方塊中，輸入 Java 應用程式伺服器名稱，例如 **Apache Tomcat** 或 **Jetty**, ，然後按 Enter 鍵。

5. 在搜尋結果中，按一下 Java 應用程式伺服器。

    ![](./media/web-sites-java-get-started/webmobilejetty.png)

6. 在第一個 **Apache Tomcat** 或 **Jetty** 刀鋒視窗中，按一下 [ **建立**。

    ![](./media/web-sites-java-get-started/jettyblade.png)

7. 在下一個 **Apache Tomcat** 或 **Jetty** 刀鋒視窗中，輸入中的 web 應用程式的名稱 **Web 應用程式** 方塊。

    此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net。 如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。

8. 選取 **資源群組** 或建立新的帳戶。

    如需資源群組的詳細資訊，請參閱 [使用 Azure 入口網站來管理您的 Azure 資源](../resource-group-portal.md)。

9. 選取 **App Service 方案/位置** 或建立新的帳戶。

    如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

10. 按一下 [ **建立**。

    ![](./media/web-sites-java-get-started/jettyportalcreate2.png)

    Azure 很快 (通常不到一分鐘) 就會完成建立新的 Web 應用程式。

11. 按一下 [ **Web 應用程式 > {新 web 應用程式}**。

12. 按一下 [ **URL** 瀏覽至新站台。

    ![](./media/web-sites-java-get-started/jettyurl.png)

    Tomcat 會隨附一組預設頁面，所以如果選擇 Tomcat，您會看到類似下列範例的頁面。

    ![使用 Apache Tomcat 的 Web 應用程式](./media/web-sites-java-get-started/tomcat.png)

    如果選擇 Jetty，您會看到類似下列範例的頁面。 Jetty 沒有一組預設頁面，所以用於空白 Java 網站的相同 JSP 會在此重複使用。

    ![使用 Jetty 的 Web 應用程式](./media/web-sites-java-get-started/jetty.png)

現在，您已建立 web 應用程式與應用程式容器，請參閱 [後續步驟](#next-steps) 區段，如需有關如何上傳至 web 應用程式的應用程式資訊。

## <a name="portal"></a> 建立及設定 Java web 應用程式

本節說明如何建立 web 應用程式，並將它設定為使用 Java **應用程式設定** 的入口網站] 分頁。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [ **新增 > Web + 行動 > Web 應用程式**。

    ![](./media/web-sites-java-get-started/newwebapp.png)

4. 輸入中的 web 應用程式的名稱 **Web 應用程式** 方塊。

    此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net。 如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。

5. 選取 **資源群組** 或建立新的帳戶。

    如需資源群組的詳細資訊，請參閱 [使用 Azure 入口網站來管理您的 Azure 資源](../resource-group-portal.md)。

6. 選取 **App Service 方案/位置** 或建立新的帳戶。

    如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

7. 按一下 [ **建立**。

    ![](./media/web-sites-java-get-started/newwebapp2.png)
 
8. 建立 web 應用程式後，按一下 [ **Web 應用程式 > {您的 web 應用程式}**。
 
    ![](./media/web-sites-java-get-started/selectwebapp.png)

9. 在 **Web 應用程式** 刀鋒視窗中，按一下 [ **設定**。

10. 按一下 [ **應用程式設定**。

11. 選擇需要 **Java 版本**。 

12. 選擇需要 **Java 次要版本**。  如果您選取 **最新**, ，您的應用程式會使用該 Java 主要版本的應用程式服務中使用的最新的次要版本。

12. 選擇需要 **Web 容器**。 如果您選取的容器名稱開頭 **最新**, ，您的應用程式將會保留在該 web 容器主要版本所提供的應用程式服務中的最新版本。 

    ![](./media/web-sites-java-get-started/versions.png)

13. 按一下 [ **儲存**。

    幾分鐘之後，您的 Web 應用程式就會變成 Java 型的 Web 應用程式，並已設定成使用您選取的 Web 容器。

14. 按一下 [ **Web 應用程式 > {新 web 應用程式}**。

15. 按一下 [ **URL** 瀏覽至新站台。

    網頁會確認您已建立 Java 型 Web 應用程式。

## 後續步驟

此時，在 Azure App Service 中，您會擁有在 Web 應用程式中執行的 Java 應用程式伺服器。 若要將自己的程式碼部署至 web 應用程式，請參閱 [將應用程式或網頁新增至 Java web 應用程式](web-sites-java-add-app.md)。

如需有關如何開發 Java 應用程式在 Azure 中的詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

<!-- External Links -->
[activate your Visual Studio subscriber benefits]: http://go.microsoft.com/fwlink/?LinkId=623901
[sign up for a free trial]: http://go.microsoft.com/fwlink/?LinkId=623901

[Try App Service]: http://go.microsoft.com/fwlink/?LinkId=523751

