<properties 
    pageTitle="在 Azure 應用程式服務中建立使用 AD FS 驗證的 .NET MVC Web 應用程式" 
    description="了解如何在 Azure 應用程式服務 Web 應用程式中建立使用內部部署 STS 進行驗證的 ASP.NET MVC 特定業務應用程式。本教學課程將 AD FS 選為內部部署 STS。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="12/15/2015" 
    ms.author="cephalin"/>


# 在 Azure 應用程式服務中建立使用 AD FS 驗證的 .NET MVC Web 應用程式

在本文中，您將學習如何建立 ASP.NET MVC 特定業務應用程式中的 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 使用內部 [Active Directory Federation Services](http://technet.microsoft.com/library/hh831502.aspx) 身分識別提供者。 當您想要在 Azure 應用程式服務 Web 應用程式中建立特定業務應用程式，但您的組織需要在內部儲存所有資料時，可以利用這種情況。
>[AZURE.NOTE] 如需 Azure App Service Web 應用程式不同的企業驗證和授權選項的概觀，請參閱 [使用 Active Directory 進行驗證，在 Azure App Service 中](web-sites-authentication-authorization.md)。

<a name="bkmk_build"></a>
## 將建置的項目

您將在 Azure 應用程式服務 Web 應用程式中建置具備下列功能的基本 ASP.NET 應用程式：

- 根據 AD FS 驗證使用者
- 使用 `Authorize` 授權使用者使用不同的動作
- 在 Visual Studio 中偵錯和發行至 Azure 應用程式服務 Web 應用程式的靜態設定 (設定一次，隨時偵錯和發行)

<a name="bkmk_need"></a>
## 必要元件

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]
>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

您需要下列項目完成本教學課程：

- 在內部部署 AD FS (如我所使用的測試實驗室的端對端逐步解說，請參閱 [測試實驗室: 搭配 Azure VM (僅適用於測試) 中的 AD FS 的獨立 STS](TODO))
- 在 AD FS 管理中建立信賴憑證者信任的權限
- Visual Studio 2013
- [Azure SDK 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) 或更新版本

<a name="bkmk_sample"></a>
## 使用特定業務範本的範例應用程式

在本教學課程中，範例應用程式 [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), ，由 Azure Active Directory 團隊所建立。 由於 AD FS 支援 WS-同盟，因此您可將其用作範本，輕鬆地建立新的特定業務應用程式。 它有下列功能：

- 使用 [WS-同盟](http://msdn.microsoft.com/library/bb498017.aspx) 來向內部部署 AD FS 部署
- 登入和登出功能
- 使用 [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (而不是 Windows Identity Foundation，也就是 WIF)，也就是未來的 ASP.NET 和更容易地進行驗證和授權比使用 WIF 設定

<a name="bkmk_setup"></a>
## 設定範例應用程式

2.  在範例解決方案複製或下載 [研究 WSFederation WebApp DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) 到本機目錄。
    > [AZURE.NOTE] 指示 [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) 示範如何設定應用程式與 Azure Active Directory，但在本教學課程，您會為它設定 AD FS，因此遵循以下步驟改為。

3.  開啟解決方案，然後在 [方案總管]**** 中開啟 Controllers\AccountController.cs。

    您會看到程式碼只是發出驗證挑戰，以使用 WS-同盟來驗證使用者。 所有驗證都是在 App_Start\Startup.Auth.cs 中設定。

4.  開啟 App_Start\Startup.Auth.cs。 在 `ConfigureAuth` 方法，請注意行:

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

    在 OWIN 世界中，這真的是您需要設定 WS-同盟驗證的最低限度。 這比 WIF 簡單且簡潔得多，其中 Web.config 會在各處插入 XML。 您需要的唯一資訊是信賴憑證者的 (RP) 識別碼和 AD FS 服務的中繼資料檔案的 URL。 以下是範例：

    -   RP 識別碼: `https://contoso.com/MyLOBApp`
    -   中繼資料位址: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.  在 App_Start\Startup.Auth.cs 中變更靜態字串定義，如以下反白顯示：
<pre class="prettyprint">
                  
私用的靜態字串領域 = ConfigurationManager.AppSettings["ida:
                  <mark>RPIdentifier</mark>
                  "];
                  <mark>
                    <del>私用的靜態字串 aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del>
                  </mark>
<mark><del>私用的靜態字串租用戶 = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
<mark><del>私用的靜態字串中繼資料 = string。格式 ("{0} / \\ {1 \\} /federationmetadata/2007-06/federationmetadata.xml 」，aadInstance，租用戶)。</del></mark>
<mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>
<mark><del>字串的權限 = String.Format (CultureInfo.InvariantCulture，aadInstance、 租用戶)。</del></mark>
</pre>

6.  您現在要在 Web.config 中進行對應的變更。 開啟 Web.config 並修改應用程式設定，如下列反白顯示：
<pre class="prettyprint">
                  
&lt;appSettings&gt;
 <add key="webpages:Version" value="3.0.0.0" />
 <add key="webpages:Enabled" value="false" />
 <add key="ClientValidationEnabled" value="true" />
 <add key="UnobtrusiveJavaScriptEnabled" value="true" />
  
                  <mark>
                    <del>< 新增機碼 ="ida: Wtrealm"value ="[請輸入研究 WSFederation WebApp DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet 應用程式識別碼 URI]"/ ></del>
                  </mark>
  <mark><del>< 新增機碼 ="ida: AADInstance"value ="https://login.windows.net"/ ></del></mark>
  <mark><del>< 新增機碼 ="ida: Tenant"value ="[Enter 租用戶名稱，例如 contoso.onmicrosoft.com]"/ ></del></mark>
  <mark><add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /></mark>
  <mark><add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /></mark>
</appSettings>
</pre>

根據您的對應環境填寫索引鍵值。

7.  建置應用程式，確定沒有任何錯誤。

就這麼簡單。 範例應用程式現在已準備好要使用 AD FS。 您稍後仍然必須在 AD FS 中，使用此應用程式設定 RP 信任。

<a name="bkmk_deploy"></a>
## 將範例應用程式部署到 Azure 應用程式服務 Web 應用程式

在這裡，您將應用程式發行到應用程式服務 Web 應用程式中的 Web 應用程式，同時保留偵錯環境。 請注意，您即將發行應用程式，但應用程式沒有 AD FS 授予的 RP 信任，所以還是無法執行驗證。 不過，如果您現在這樣做，您可以擁有 Web 應用程式 URL，您稍後亦可使用該 URL 來設定 RP 信任。

1. 以滑鼠右鍵按一下專案，然後選取 [發佈]****。

    ![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. 選取 [**Microsoft Azure App Service**]。
3. 如果您還沒有登入 Azure，請按一下 [登入]**** 並使用您 Azure 訂用帳戶的 Microsoft 帳戶登入。
4. 登入之後，按一下 [新增]**** 來建立新的 Web 應用程式。
5. 填寫所有必要欄位。 您稍後即將連線到內部部署資料，因此您不會建立此 Web 應用程式的資料庫。

    ![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. 按一下 [建立]****。 一旦建立 Web 應用程式後，會開啟 [發行 Web] 對話方塊。
7. 在 [目的地 URL]**** 中，將 **http** 變更為 **https**。 將完整的 URL 複製到文字編輯器。 您稍後將使用它。 然後按一下 [**發佈**]。

    ![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. 在 Visual Studio 中，開啟專案中的 **Web.Release.config**。將下列 XML 插入 `< 設定 >` 標記，並使用發佈 web 應用程式的 URL 取代索引鍵值。
<pre class="prettyprint">
                  
&lt;appSettings&gt;
  < 新增機碼 ="ida: RPIdentifier"value ="
                  <mark>[例如 https://mylobapp.azurewebsites.net/]</mark>
                  「 xdt:Transform ="SetAttributes"xdt:Locator="Match(key)"/ >
</appSettings>
                </pre>

當您完成時，您會在專案中設定兩個 RP 識別碼，一個適用於 Visual Studio 中的偵錯環境，另一個適用於 Azure 中的已發行 Web 應用程式。 您會在 AD FS 中，為兩種環境的每種環境設定一個 RP 信任。 在偵錯期間，Web.config 中的應用程式設定可讓您的「偵錯」****組態使用 AD FS，且在發佈後 (預設會發佈「版本」****組態) 會上傳轉換的 Web.config，其中包含 Web.Release.config 的應用程式設定變更。

如果您想要將在 Azure 發行的 Web 應用程式附加到偵錯工具 (也就是您必須上傳已發行 Web 應用程式中的程式碼偵錯符號)，您可以建立偵錯組態的複本進行 Azure 偵錯，但使用其自訂的 Web.config 轉換 (例如 Web.AzureDebug.config)，從 Web.Release.config 使用應用程式設定。 這可讓您跨不同的環境維護靜態組態。

<a name="bkmk_rptrusts"></a>
## 在 AD FS 管理中設定信賴憑證者信任

現在您需要先在 AD FS 管理中設定 RP 信任，然後您才可以使用範例應用程式並透過 AD FS 實際驗證。 您必須設定兩個不同的 RP 信任，一個用於偵錯環境，另一個用於您發行的 Web 應用程式。
> [AZURE.NOTE] 請確定為您的兩個環境重複下列步驟。

4.  在 AD FS 伺服器上，使用具有 AD FS 之管理權限的認證登入。
5.  開啟 [AD FS 管理]。 以滑鼠右鍵按一下 [AD FS]\[信任的關係]\[信賴憑證者信任]****，然後選取 [新增信賴憑證者信任]****。

    ![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.  在 [選取資料來源]**** 頁面上，選取 [手動輸入信賴憑證者的相關資料]****。

    ![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.  在 [指定顯示名稱]**** 頁面上，輸入應用程式的顯示名稱，然後按 [下一步]****。
7.  在 [選擇通訊協定]**** 頁面上，按 [下一步]****。
8.  在 [設定憑證]**** 頁面上，按 [下一步]****。
    > [AZURE.NOTE] 由於您應該已在使用 HTTPS，因此可選擇是否使用加密的權杖。 如果您真的想要在這個頁面上加密 AD FS 的權杖，您也必須在程式碼中新增權杖解密的邏輯。 如需詳細資訊，請參閱 [手動設定 OWIN WS-同盟中介軟體和接受加密權杖](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx)。

5.  前進至下一個步驟之前，您需從 Visual Studio 專案準備一段資訊。 在專案屬性中，記下應用程式的 [SSL URL]****。

    ![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.  返回 [AD FS 管理]，在 [新增信賴憑證者信任精靈]**** 的 [設定 URL]**** 頁面中，選取 [啟用 WS-同盟被動式通訊協定的支援]**** 並輸入您在上一個步驟記下 Visual Studio 專案的 SSL URL。 然後按 [下一步]****。

    ![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)
    > [AZURE.NOTE] 驗證成功後，URL 會指定將用戶端傳送到的位置。 偵錯環境中，它應該是 <code>https://localhost: < 連接埠 > /</code>。 已發行的 Web 應用程式中，它應該是 Web 應用程式 URL。

7.  在 [設定識別碼]**** 頁面上，確認已列出專案 SSL URL，然後按 [下一步]****。 按 [下一步]**** 並選取預設選取項目，一直到精靈結束。
    > [AZURE.NOTE] 在 Visual Studio 專案 App_Start\Startup.Auth.cs，此識別碼進行比對的值 <code>WsFederationAuthenticationOptions.Wtrealm</code> 在同盟驗證期間。 根據預設，會加入上一個步驟的應用程式 URL 做為 RP 識別碼。

8.  您現在已經在 AD FS 中完成專案的 RP 應用程式設定。 接下來，您將設定此應用程式來傳送應用程式所需的宣告。 [編輯宣告規則]**** 對話方塊預設會在精靈結束時開啟，以便讓您立即啟動。 讓我們至少設定下列宣告 (使用括號括住結構描述)：

    -   使用 ASP.NET 所產生的名稱 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)- `User.Identity.Name`。
    -   使用者主要名稱 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn)-用來唯一識別組織中的使用者。
    -   群組成員資格與角色 (http://schemas.microsoft.com/ws/2008/06/identity/claims/role)-可以搭配 `[Authorize (角色 ="role1，role2，...")]` 裝飾來授權控制站/動作。 事實上，這可能不是角色授權的最有效方法，特別是如果您的 AD 使用者定期屬於數百個安全性群組時，因為這會轉譯成 SAML 權杖中的數百個角色宣告。 替代方式是根據特定群組中的使用者成員資格，有條件地傳送單一角色宣告。 不過，我們會在本教學課程中簡化該作業。
    -   名稱識別碼 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier)-可用於防偽驗證。 如需有關如何讓其使用防偽驗證的詳細資訊，請參閱 **將特定業務功能新增至範例應用程式** 區段 [Azure App Service 中建立的.NET MVC web 應用程式，使用 Azure Active Directory 驗證](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud)。

    > [AZURE.NOTE] 您必須為應用程式設定的宣告類型取決於您的應用程式需求。 Azure Active Directory 應用程式 (也就是 RP 信任) 所支援的宣告清單，例如，請參閱 [支援的權杖和宣告類型](http://msdn.microsoft.com/library/azure/dn195587.aspx)。

8.  在 [編輯宣告規則] 對話方塊中，按一下 [新增規則]****。
9.  如下所示設定名稱、UPN 和角色宣告，再按一下 [完成]****。

    ![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

    接下來，您將建立暫時性的名稱識別碼宣告中使用的步驟示範 [SAML 判斷提示中的名稱識別碼](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)。

9.  再按一下 [新增規則]****。
10. 選取 [使用自訂規則傳送宣告]**** 並按 [下一步]****。
11. 將下列規則語言貼到 [自訂規則]**** 方塊，將規則命名為 [每個工作階段識別碼]**** 並按一下 [完成]****。
<pre class="prettyprint">
                  
c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &&
c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
   => add(
       store = "_OpaqueIdStore",
       類型 = ("
                  <mark>http://contoso.com/internal/sessionid</mark>
                  "),
       查詢 ="{0}，\\ {1 \\}，\\ {<2\}}; \\ {<3\}}; \\ {<4\}}"，
       param = "useEntropy",
       param = c1.Value,
       param = c1.OriginalIssuer,
       param = "",
       param = c2.Value);
                </pre>

您的自訂規則看起來應該像這樣：

![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.  再按一下 [新增規則]****。
10. 選取 [傳輸傳入宣告]**** 並按 [下一步]****。
11. 如下所示設定規則 (使用您在自訂規則中建立的宣告類型)，按一下 [完成]****。

    ![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

    如上述的暫時性名稱識別碼宣告之步驟的詳細資訊，請參閱 [SAML 判斷提示中的名稱識別碼](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)。

12. 按一下 [編輯宣告規則]**** 對話方塊中的 [套用]****。 它現在看起來應該類似以下螢幕擷取畫面：

    ![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)
    > [AZURE.NOTE] 同樣地，請確定為偵錯環境和已發行的 Web 應用程式重複這些步驟。

<a name="bkmk_test"></a>
## 測試應用程式的同盟驗證

您已準備好針對 AD FS 測試應用程式的驗證邏輯。 在我的 AD FS 實驗室環境中，我有一名測試使用者屬於 Active Directory (AD) 中的測試群組。

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

若要在偵錯工具中測試驗證，您只需要現在是型別 `F5`。 如果您想要在已發行的 Web 應用程式中測試驗證，請巡覽至 URL。

載入 Web 應用程式之後，請按一下 [登入]****。 您現在應取得登入對話方塊，或 AD FS 所提供的登入頁面，視 AD FS 所選擇的驗證方法而定。 以下是我在 Internet Explorer 11 中看到的畫面。

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

一旦您登入 AD FS 部署的 AD 網域中的使用者，您現在應該會看到首頁，首頁一次 * * 大家好， <User Name>! * * 角。以下是我看到的內容。

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

到目前為止，您已經成功地完成下列工作：

- 您的應用程式已成功連線 AD FS，並在 AD FS 資料庫中找到相符的 RP 識別碼
- AD FS 已成功驗證 AD 使用者，並將您重新導向回應用程式的首頁
- 做為 AD FS 成功傳送至您的應用程式的名稱宣告 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) 的使用者名稱並顯示在角所示。

如果缺少名稱宣告，您應該會看到「您好！」****。 如果您看一下 Views\Shared\_LoginPartial.cshtml，您會發現它會使用 `User.Identity.Name` 顯示使用者名稱。 如前所述，若可在 SAML 權杖中取得，ASP.NET 會使用已驗證使用者的名稱宣告來產生這個屬性。 若要查看 AD FS 傳送的所有宣告，請將中斷點放在索引動作方法的 Controllers\HomeController.cs 中。 使用者通過驗證後，檢查 `System.Security.Claims.Current.Claims` 集合。

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png)

<a name="bkmk_authorize"></a>
## 授權使用者使用特定的控制器或動作

由於您已在 RP 信任組態中，做為角色宣告加入群組成員資格，您現在可以將這些直接在 `[authorize]` 裝飾用於控制器和動作。 在使用建立-讀取-更新-刪除 (CRUD) 模式的特定業務應用程式中，您可以授權特定角色來存取每個動作。 現在，您只要在現有的主控制器上嘗試這項功能。

1. 開啟 Controllers\HomeController.cs。
2. 裝飾 `有關` 和 `連絡人` 動作方法類似下列使用安全性群組已驗證的使用者擁有的成員資格。
   <pre class="prettyprint">
    <mark>[Authorize(Roles="Test Group")]</mark>
    public ActionResult About()
   {
       ViewBag.Message = "Your application description page.";
       return View();
   }
    <mark>[Authorize(Roles="Domain Admins")]</mark>
    public ActionResult Contact()
   {
       ViewBag.Message = "Your contact page.";
       return View();
   }
    </pre>

    由於我加入 **測試使用者** 至 **測試群組** 我 AD FS 實驗室環境中，我將使用測試群組來測試授權 `有關`。 如 `連絡人`, ，我將測試的負面案例 **Domain Admins**, 至其 **測試使用者** 不屬於。

3. 按下開始偵錯工具 `F5` 和登入，然後按一下 [ **有關**。 您應該立即檢視 `~/About/Index` 頁面成功，如果已驗證的使用者已授權給該動作。
4. 現在按一下 [連絡人]****，在我的案例中應該不會將該動作授權給「測試使用者」****。 不過，瀏覽器會重新導向至 AD FS，最後會顯示這則訊息：

 ![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

 如果您在 AD FS 伺服器的事件檢視器中調查此錯誤，您會看到這則例外狀況訊息：
<pre class="prettyprint">
                  
 Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: 
                  <mark>相同的用戶端瀏覽器工作階段已在 last '11' 秒 '6' 的要求。</mark>
                   如需詳細資訊，請連絡您的系統管理員。
   在 Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie (WrappedHttpListenerContext 內容)
   在 Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse (WSFederationContext 內容、 MSISSignInResponse 回應)
   在 Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest (ProtocolContext protocolContext、 PassiveProtocolHandler protocolHandler)
   在 Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext (WrappedHttpListenerContext 內容)
 
                </pre>

 發生這種情況的原因是，在未授權使用者的角色時，MVC 預設會傳回「401 未授權」。 這樣會針對您的身分識別提供者 (AD FS) 觸發重新驗證要求。 因為使用者已通過驗證，AD FS 會返回相同的頁面，然後發出另一個 401，從而建立重新導向迴圈。 您將會覆寫 AuthorizeAttribute 的 `HandleUnauthorizedRequest` 方法，使用簡單的邏輯來顯示有意義，而不是重新導向迴圈。

5. 在稱為 AuthorizeAttribute.cs 的專案中建立檔案，並將下列程式碼貼至其中。

     using System;
     using System.Web.Mvc;
     using System.Web.Routing;
    
     namespace WebApp_WSFederation_DotNet
     {
         [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
         public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
         {
             protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
             {
                 if (filterContext.HttpContext.Request.IsAuthenticated)
                 {
                     filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
                 }
                 else
                 {
                     base.HandleUnauthorizedRequest(filterContext);
                 }
             }
         }
     }

 覆寫程式碼會在進行驗證，但-未經授權的情況下傳送 HTTP 403 (禁止) 而不是 HTTP 401 (未經授權)。

6. 偵錯工具使用，然後再次執行 `F5`。 現在按一下 [連絡人]**** 會顯示更具參考性的 (雖然不討喜) 錯誤訊息：

    ![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. 再次將應用程式發行至 Azure 應用程式服務 Web 應用程式，並測試即時應用程式的行為。

<a name="bkmk_data"></a>
## 連線至內部部署資料

您會想要使用 AD FS (而不是使用 Azure Active Directory) 來實作特定業務應用程式的原因是，遵守在外部部署保存組織資料的法規需求。 這也表示您在 Azure 中的 web 應用程式必須存取在內部部署資料庫，因為不允許您使用 [SQL Database](/services/sql-database/) 做為 web 應用程式的資料層。

Azure App Service Web Apps 支援存取在內部部署資料庫，透過兩種方式: [混合式連線](../integration-hybrid-connection-overview.md) 和 [虛擬網路](web-sites-integrate-with-vnet.md)。 如需詳細資訊，請參閱 [使用 VNET 整合和混合式連線搭配 Azure App Service Web Apps](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)。

<a name="bkmk_resources"></a>
## 進一步資源

- [保護應用程式使用 SSL 和 Authorize 屬性](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [使用 Active Directory 進行 Azure App Service 中驗證](web-sites-authentication-authorization.md)
- [在 Azure App Service 中建立的.NET MVC web 應用程式，使用 Azure Active Directory 驗證](web-sites-dotnet-lob-application-azure-ad.md)
- [使用 Visual Studio 2013 中 ASP.NET 的內部部署組織驗證選項 (ADFS)](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Vittorio Bertocci 的部落格](http://blogs.msdn.com/b/vbertocci/)
- [將 VS2013 Web 專案從 WIF 移轉到 Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Active Directory Federation Services 概觀](http://technet.microsoft.com/library/hh831502.aspx)
- [WS-同盟 1.1 規格](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]







