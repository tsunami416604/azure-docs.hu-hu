<properties 
    pageTitle="在 Azure 應用程式服務中建立使用 Azure Active Directory 驗證的 .NET MVC Web 應用程式" 
    description="了解如何在 Azure 應用程式服務中建立使用 Azure Active Directory 進行驗證的 ASP.NET MVC 特定業務應用程式。" 
    services="app-service\web, active-directory" 
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
    ms.date="12/10/2015" 
    ms.author="cephalin"/>


# 在 Azure 應用程式服務中建立使用 Azure Active Directory 驗證的 .NET MVC Web 應用程式

在本文中，您將學習如何建立 ASP.NET MVC 特定業務應用程式中的 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 使用 [Azure Active Directory](/services/active-directory/) 身分識別提供者。 您也將學習如何使用 [Azure Active Directory Graph 用戶端程式庫](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) 來查詢目錄資料的應用程式中。

您使用的Azure Active Directory 租用戶可以是純 Azure 目錄，或可以是與您的內部部署 Active Directory (AD) 同步處理的目錄，以便為內部部署或遠端的背景工作角色建立單一登入體驗。
>[AZURE.NOTE] 對於 Azure App Service Web Apps，您可以設定 Azure Active Directory 租用戶的驗證，只需按幾下按鈕即可。 如需詳細資訊，請參閱 [使用 Active Directory 進行驗證，在 Azure App Service 中](web-sites-authentication-authorization.md)。

<a name="bkmk_build"></a>
## 將建置的項目

您將在應用程式服務 Web Apps 中建置簡單的「建立/讀取/更新/刪除」(CRUD) 特定業務應用程式，它使用下列功能追蹤工作項目：

- 比對 Azure Active Directory 驗證使用者
- 實作登入和登出功能
- 使用 `Authorize` 授權使用者使用不同的 CRUD 動作
- 查詢 Azure Active Directory 資料使用 [Azure Active Directory Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- 使用 [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (而不是 Windows Identity Foundation，也就是 WIF)，也就是未來的 ASP.NET 和更容易地進行驗證和授權比使用 WIF 設定

<a name="bkmk_need"></a>
## 必要元件

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]
>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

您需要下列項目完成本教學課程：

- Azure Active Directory 租用戶與不同的群組中的使用者
- 在 Azure Active Directory 租用戶建立應用程式的權限
- Visual Studio 2013 或更新版本
- [Azure SDK 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) 或更新版本

<a name="bkmk_sample"></a>
## 使用特定業務範本的範例應用程式

在本教學課程中，範例應用程式 [研究 RoleClaims WebApp DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims), Azure Active Directory 團隊所建立，可用來當做範本來輕鬆建立新的特定業務應用程式。 它有下列內建功能：

- 使用 [OpenID Connect](http://openid.net/connect/) 來向 Azure Active Directory
- 包含範例 `TaskTracker` 控制站，示範如何授權應用程式中，其中包括標準的使用方式的特定動作的不同角色 `Authorize`。
- 是具備您可以立即指定使用者和群組之預先定義角色的多租用戶應用程式。

<a name="bkmk_run" />
## 執行範例應用程式

1.  在範例解決方案複製或下載 [研究 RoleClaims WebApp DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) 到本機目錄。

2.  請依照下列指示 [方式來執行範例為單一租用戶應用程式](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#how-to-run-the-sample-as-a-single-tenant-app) 設定 Azure Active Directory 應用程式和專案。
請務必遵循所有指示以將應用程式從多租用戶轉換為單一租用戶。

3.  在 [Azure 傳統入口網站](https://manage.windowsazure.com) 檢視您剛建立的 Azure Active Directory 應用程式中，按一下 **使用者** ] 索引標籤。 然後，將想要的使用者指派給想要的角色。
    >[AZURE.NOTE] 如果您想要將角色指派給群組，以及使用者群組，您必須升級您的 Azure Active Directory 租用戶 [Azure Active Directory Premium](/pricing/details/active-directory/)。 在您的應用程式的傳統入口網站 UI 中，如果您看到 **使用者** ] 索引標籤上，而不是 **使用者及群組] 索引標籤上，您可以嘗試 Azure Active Directory Premium，請前往 Azure Active Directory 租用戶的 * * 授權** ] 索引標籤。 

3.  一旦您完成設定應用程式時，輸入 `F5` Visual Studio 執行 ASP.NET 應用程式中。

4.  應用程式載入後，按一下 [**登入**]，並使用具有 Azure 傳統入口網站中「管理員」角色的使用者登入。

5.  如果已適當地設定 Azure Active Directory 應用程式，並在 Web.config 設定對應的設定，您應該重新導向至登入。 只要使用您在 Azure 傳統入口網站中建立 Azure Active Directory 應用程式時所使用的帳戶登入即可，因為它是 Azure Active Directory 應用程式的預設擁有者。

<a name="bkmk_deploy"></a>
## 將範例應用程式部署到 App Service Web Apps

在這裡，您要將應用程式發佈到 Azure 應用程式服務中的 Web 應用程式。 已指示 [README.md](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims/blob/master/README.md) 也部署到應用程式服務 Web 應用程式，不過這些步驟包含本機偵錯環境的設定。 我將告訴您如何在保留偵錯設定的情況下部署。

1. 以滑鼠右鍵按一下專案，然後選取 [發佈]****。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. 選取 [Microsoft Azure Web Apps]****。

3. 如果您還沒有登入 Azure，請按一下 [新增帳戶]**** 並使用您 Azure 訂用帳戶的 Microsoft 帳戶登入。

4. 登入之後，按一下 [新增]**** 在 Azure 中來建立新的 Web 應用程式。

5. 在 [裝載]**** 中，填寫所有必要欄位。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

5. 您必須具備資料庫連線，此應用程式才能儲存角色對應、快取的權杖及任何應用程式資料。 在 [建立應用程式服務]**** 對話方塊中，按一下 [服務]****。 在 [SQL Database]**** 旁按一下加號，加入新的資料庫。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-database.png)

5. 在 [設定 SQL Database]**** 對話方塊中，選取或建立伺服器、設定名稱，然後按一下 [確定]****。

     ![](./media/web-sites-dotnet-lob-application-azure-ad/4-config-database.png)

6. 按一下 [建立]****。 一旦建立 Web 應用程式後，會開啟 [發佈 Web]**** 對話方塊。

7. 在 [目的地 URL]**** 中，將 **http** 變更為 **https**。 將完整的 URL 複製到文字編輯器。 您稍後將使用它。 然後按 [下一步]****。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. 清除 [啟用組織驗證]**** 核取方塊。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/6-enable-code-first-migrations.png)

8. 展開 [RoleClaimContext]**** 並選取 [執行 Code First 移轉 (在應用程式開頭執行)]****。 [Code First 移轉](https://msdn.microsoft.com/data/jj591621.aspx) 有助於稍後才要定義其他 Code First 資料模型時，請更新您的應用程式在 Azure 中的資料庫結構描述。

9. 不要按一下 [發佈]**** 進行 Web 發佈，請按一下 [關閉]****。 按一下 [是]**** 將變更儲存至發行設定檔。

2. 在 [Azure 傳統入口網站](https://manage.windowsazure.com), ，移至 Azure Active Directory 租用戶，然後按一下 [ **應用程式** ] 索引標籤。

2. 按一下頁面底部的 [新增]****。

2. 按一下 [Add an application my organization is developing]****。

3. 選取 [Web 應用程式和/或 Web API]****。

4. 指定應用程式的名稱，並按 [下一步]****。

5. 在應用程式屬性，設定 **登入 URL** 儲存稍 web 應用程式 url (例如 `https://<site-name>.azurewebsites.net/`)，而 **應用程式識別碼 URI** 至 `https://<aad-tenanet-name>/<app-name>`。然後，按一下 [完成]****。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

2.  應用程式建立之後，更新應用程式資訊清單相同的方式稍早的指示從 [定義您的應用程式角色](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#step-2-define-your-application-roles)。

3.  在 [Azure 傳統入口網站](https://manage.windowsazure.com) 檢視您剛建立的 Azure Active Directory 應用程式中，按一下 **使用者** ] 索引標籤。 然後，將想要的使用者指派給想要的角色。

6. 按一下 [設定]**** 索引標籤。

7. 在 [索引鍵]**** 下，在下拉式清單中選取 [1 年]**** 來建立新索引鍵。

8. 在 [其他應用程式的權限]**** 下，於 [委派權限]**** 下拉式清單中為 [Azure Active Directory]**** 項目選取 [登入並讀取使用者設定檔]**** 和 [讀取目錄資料]****。
    > [AZURE.NOTE] 您在這裡需要的實際權限取決於您應用程式所需的功能。 有些權限需要設定「全域管理員」****角色，但本教學課程所需的權限只需要「使用者」****角色。

9.  按一下 [儲存]****。

10.  離開已儲存的設定頁面之前，請將下列資訊複製到文字編輯器。

    -   Client ID
    -   Key (if you navigate away from the page, you will not be able to see the key again)

11. 在 Visual Studio 中，開啟專案中的 **Web.Release.config**。將下列 XML 插入 `< 設定 >` 標記，並使用您儲存新的 Azure Active Directory 應用程式的資訊來取代每個索引鍵的值。
<pre class="prettyprint">
                  
&lt;appSettings&gt;
  < 新增機碼 ="ida: ClientId"value ="
                  <mark>[例如 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>
                  「 xdt:Transform ="SetAttributes"xdt:Locator="Match(key)"/ >
  < 新增機碼 ="ida: AppKey"value ="
                  <mark>[例如 rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo =]</mark>
                  「 xdt:Transform ="SetAttributes"xdt:Locator="Match(key)"/ >
  < 新增機碼 ="ida: PostLogoutRedirectUri"value ="
                  <mark>[例如 https://mylobapp.azurewebsites.net/]</mark>
                  「 xdt:Transform ="SetAttributes"xdt:Locator="Match(key)"/ >
</appSettings>
                </pre>

請確定 ida:PostLogoutRedirectUri 值的結尾有一條斜線 "/"。

1. 以滑鼠右鍵按一下專案，然後選取 [發佈]****。

2. 按一下 [發佈]****，以發佈至 Azure App Service Web Apps。

當您完成時，您會在 Azure 傳統入口網站中設定兩個 Azure Active Directory 應用程式：一個適用於 Visual Studio 中的偵錯環境，另一個適用於 Azure 中的已發佈 Web 應用程式。 在偵錯期間，Web.config 中的應用程式設定可讓您的**偵錯**設定使用 Azure Active Directory，且在發佈後 (預設會發佈「版本」****組態) 會上傳轉換的 Web.config，其中包含 Web.Release.config 的應用程式設定變更。

如果您想要將已發佈的 Web 應用程式附加到偵錯工具 (您必須上傳已發佈 Web 應用程式中的程式碼偵錯符號)，您可以建立偵錯組態的複本進行 Azure 偵錯，但使用其自訂的 Web.config 轉換 (例如 Web.AzureDebug.config)，從 Web.Release.config 使用 Azure Active Directory 設定。 這可讓您跨不同的環境維護靜態組態。

<a name="bkmk_crud"></a>
## 將特定業務功能功能新增至範例應用程式

在這部分的教學課程中，您將學習如何根據範例應用程式建立所需的特定業務功能。 您將建立簡單 CRUD 工作項目追蹤程式，類似於 TaskTracker 控制器，但使用標準的 CRUD 建構和設計模式。 您也會使用隨附的 Scripts\AadPickerLibrary.js，使用 Azure Active Directory Graph API 的資料豐富您的應用程式。

5.  在 [模型] 資料夾中，建立新 [Code First](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) 模型稱為的 WorkItem.cs，並以下列程式碼取代程式碼:

    using System.ComponentModel.DataAnnotations;
    
    namespace WebApp_RoleClaims_DotNet.Models
    {
        public class WorkItem
        {
            [Key]
            public int ItemID { get; set; }
            public string AssignedToID { get; set; }
            public string AssignedToName { get; set; }
            public string Description { get; set; }
            public WorkItemStatus Status { get; set; }
        }
    
        public enum WorkItemStatus
        {
            Open, 
            Investigating, 
            Resolved, 
            Closed
        }
    }

6.  開啟 DAL\RoleClaimContext.cs 並新增反白顯示的程式碼：
<pre class="prettyprint">
                  
public class RoleClaimContext : DbContext
{
   public RoleClaimContext() : base("RoleClaimContext") { }
   public DbSet&lt;Task&gt; Tasks { get; set; }
    
                  <mark>public DbSet&lt;WorkItem&gt; WorkItems { get; set; }</mark>
                  
    public DbSet&lt;TokenCacheEntry&gt; TokenCacheEntries { get; set; }
}
                </pre>

7.  建置專案，讓 Visual Studio 中的建構邏輯可存取新的模型。

8.  加入新的建構項目 `WorkItemsController` 至 Controllers 資料夾。 若要這樣做，請以滑鼠右鍵按一下 [控制器]****，指向 [新增]****，然後選取 [新增建構項目]****。

9.  選取 [使用 Entity Framework，包含檢視的 MVC 5 控制器]****，再按一下 [新增]****。

10. 選取您剛才建立的模型，然後按一下 [新增]****。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.  開啟 Controllers\WorkItemsController.cs

11. 將反白顯示的 [Authorize] 裝飾新增至下列的相應動作。
<pre class="prettyprint">
                  
...
                  <mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
                  
public class WorkItemsController : Controller
{
   ...
    
                  <mark>[Authorize(Roles = "Admin, Writer")]</mark>
                  
    public ActionResult Create()
   ...
    
                  <mark>[Authorize(Roles = "Admin, Writer")]</mark>
                  
    public async Task&lt;ActionResult&gt; Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
   ...
    
                  <mark>[Authorize(Roles = "Admin, Writer")]</mark>
                  
    public async Task&lt;ActionResult&gt; Edit(int? id)
   ...
    
                  <mark>[Authorize(Roles = "Admin, Writer")]</mark>
                  
    public async Task&lt;ActionResult&gt; Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
   ...
    
                  <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
                  
    public async Task&lt;ActionResult&gt; Delete(int? id)
   ...
    
                  <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
                  
    public async Task&lt;ActionResult&gt; DeleteConfirmed(int id)
   ...
}
                </pre>

由於您負責 Azure 傳統入口網站 UI 中的角色對應，因此您只需要確定每個動作都授權適當的角色。
> [AZURE.NOTE] 您可能已經注意到 <code>[ValidateAntiForgeryToken]</code> 裝飾的動作。 由於所描述的行為 [Brock Allen](https://twitter.com/BrockLAllen) 在 [MVC 4、 AntiForgeryToken 和宣告](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) HTTP POST 可能無法防偽語彙基元驗證，因為:
> + Azure Active Directory 不會傳送 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider，所需依預設防偽語彙基元。
> + 如果 Azure Active Directory 目錄與 AD FS 進行同步處理，AD FS 信任預設並不傳送 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider 宣告，但您可以手動設定 AD FS 來傳送此宣告。
> 您會在下一步這麼做。

12.  在 App_Start\Startup.Auth.cs，加入下列這一行中的程式碼 `ConfigureAuth` 方法。 以滑鼠右鍵按一下每個命名的解決方案錯誤來修正這個問題。

        AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;

    `ClaimTypes.NameIdentifies` specifies the claim `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, which Azure Active Directory does supply. Now that you have taken care of the authorization part (seriously, that didn't take long), you can devote your time to the actual functionality of the actions. 

13. 在 Create () 和 Edit() 中新增下列程式碼，讓 JavaScript 稍後可以使用一些變數。 以滑鼠右鍵按一下每個命名的解決方案錯誤來修正這個問題。

        ViewData["token"] = AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
        ViewData["tenant"] = ConfigHelper.Tenant;

13. `否則` 方法尚未定義，因此定義在 `WorkItemsController` 現在類別。 以滑鼠右鍵按一下每個命名的解決方案錯誤來修正這個問題。

        static string AcquireToken(string userObjectId)
        {
            ClientCredential cred = new ClientCredential(ConfigHelper.ClientId, ConfigHelper.AppKey);
            Claim tenantIdClaim = ClaimsPrincipal.Current.FindFirst(Globals.TenantIdClaimType);
            AuthenticationContext authContext = new AuthenticationContext(String.Format(CultureInfo.InvariantCulture, ConfigHelper.AadInstance, tenantIdClaim.Value), new TokenDbCache(userObjectId));
            AuthenticationResult result = authContext.AcquireTokenSilent(ConfigHelper.GraphResourceId, cred, new UserIdentifier(userObjectId, UserIdentifierType.UniqueId));
            return result.AccessToken;
        }

14. 在 Views\WorkItems\Create.cshtml (自動建構的項目) 中尋找 `Html.BeginForm` helper 方法，並修改它，如下所示:
<pre class="prettyprint">
                  @ 使用 (Html.BeginForm (
                  <mark>「 建立 」、 「 工作項目 」 FormMethod.Post，新 {id ="主要表單"}</mark>
                  ))
{
   @Html.AntiForgeryToken()
   <div class="form-horizontal">
       <h4>WorkItem</h4>
       <hr />
       @Html.ValidationSummary(true, "", new { @class = "text-danger" })
       <div class="form-group">
           <div class="col-md-10">
               @Html.EditorFor (模型 = > 模型。新 AssignedToID {htmlAttributes = new {@class = 「 表單控制項 」
                  <mark>@type = 「 隱藏 」</mark>
                   } })
               @Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
           </div>
       </div>
       <div class="form-group">
           @Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
           <div class="col-md-10">
               @Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
               @Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
           </div>
       </div>
       <div class="form-group">
           @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
           <div class="col-md-10">
               @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
               @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
           </div>
       </div>
       <div class="form-group">
           @Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
           <div class="col-md-10">
               @Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
               @Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
           </div>
       </div>
       <div class="form-group">
           <div class="col-md-offset-2 col-md-10">
               < 輸入類型 ="提交"值 ="Create"class ="btn btn-default" 
                  <mark>id ="提交 button"</mark>
                   />
           </div>
       </div>
   </div>
    
                  <mark>< 指令碼 >
           // 人員/群組選擇器程式碼
           var maxResultsPerPage = 14;
           var input = document.getElementById("AssignedToName");
           var token = "@ViewData["token"]";
           var tenant = "@ViewData["tenant"]";
           var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
           // 送出要指派的所選使用者/群組。
           $("#submit-button").click({ picker: picker }, function () {
               if (!picker.Selected())
                   return;
               $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
           });
   </script></mark>
                  
}
                </pre>

在指令碼中，AadPicker 物件會呼叫 [Azure Active Directory Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 使用者和群組符合輸入的搜尋。

15. 開啟 [封裝管理員主控台](http://docs.nuget.org/Consume/Package-Manager-Console) 並執行 **Enable-migrations-EnableAutomaticMigrations**。 更新您的應用程式中的資料庫結構描述類似於您選取當您發佈至 Azure 的應用程式的選項，此命令可 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) 時進行偵錯在 Visual Studio 中。

15. 現在請在 Visual Studio 偵錯工具中執行應用程式，或是再次發佈至 App Service Web Apps。應用程式擁有者身分登入，並瀏覽至 `https://&lt;webappname&gt;.azurewebsites.net/WorkItems/Create`。您現在會看到，您可以從下拉式清單挑選 Azure Active Directory 使用者或群組，或輸入內容來篩選清單。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. 填寫表單的其餘部分，並按一下 [建立]****。 ~/WorkItems/Index 頁面現在會顯示新建立的工作項目。 您還會看到下面的螢幕擷取畫面中，我移除 `AssignedToID` Views\WorkItems\Index.cshtml 中的資料行。

    ![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11. 現在，對 [編輯]**** 檢視做類似的變更。 在 Views\WorkItems\Edit.cshtml 中，進行的變更 `Html.BeginForm` 與上一個步驟中 Views\WorkItems\Create.cshtml 適用相同的 helper 方法 (在上述反白顯示的程式碼來取代"Edit"的 「 建立 」)。

就這麼簡單！

既然您已為 WorkItems 控制器中的不同動作設定授權和特定業務功能，您可以嘗試以不同應用程式角色的使用者身分登入來查看應用程式如何回應。

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## 進一步資源

- [保護應用程式使用 SSL 和 Authorize 屬性](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [使用 Active Directory 進行 Azure App Service 中驗證](web-sites-authentication-authorization.md)
- [在 Azure App Service 中建立的.NET MVC web 應用程式，使用 AD FS 驗證](web-sites-dotnet-lob-application-adfs.md)
- [Microsoft Azure Active Directory 範例與文件](https://github.com/AzureADSamples)
- [Vittorio Bertocci 的部落格](http://blogs.msdn.com/b/vbertocci/)
- [將 VS2013 Web 專案從 WIF 移轉到 Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Azure 的新混合式連線不是您的 #hybridCloud](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Active Directory 與 Azure Active Directory 之間的相似處](http://technet.microsoft.com/library/dn518177.aspx)
- [搭配單一登入實例目錄同步作業](http://technet.microsoft.com/library/dn441213.aspx)
- [Azure Active Directory 支援的權杖和宣告類型](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]






