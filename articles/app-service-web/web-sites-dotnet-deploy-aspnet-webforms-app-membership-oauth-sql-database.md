<properties 
    pageTitle="使用成員資格、OAuth 和 SQL Database，建立安全的 ASP.NET Web Forms 應用程式並部署至 Azure App Service。 " 
    description="本教學課程說明如何建置包含 SQL Database 的安全 ASP.NET 4.5 Web Forms Web 應用程式，並將應用程式部署至 Azure。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Erikre" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/10/2015" 
    ms.author="erikre"/>


# 使用成員資格、OAuth 和 SQL Database，建立安全的 ASP.NET Web Forms 應用程式並部署至 Azure App Service。 

##概觀
本教學課程說明如何建置包含 SQL Database 的安全 ASP.NET 4.5 Web Forms Web 應用程式，並將應用程式部署至 Azure。 

>[AZURE.NOTE] 
本教學課程的 MVC 版本，請參閱 [使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2013，SDK 會自動安裝 Visual Studio 2013 for Web Express。 您可以開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Microsoft Azure 的經驗。 完成此教學課程後，您將會有一個已在雲端中啟動並執行、並使用雲端資料庫的 Web 應用程式。

您將了解：

- 如何建立 ASP.NET 4.5 Web Forms 專案，並將該專案發行至 Azure App Service。
- 如何使用 OAuth 與 ASP.NET 成員資格來確保您的應用程式安全。
- 如何將單一資料庫同時用於成員資格和應用程式資料。
- 如何使用 Web Forms Scaffolding 來建立可讓您修改資料的網頁。
- 如何使用新的成員資格 API 來新增使用者和角色。
- 如何使用 SQL 資料庫在 Azure 中儲存資料。

您將建立一個採用 ASP.NET 4.5 Web Forms，並使用 Entity Framework 存取資料庫的簡單連絡人清單 Web 應用程式。 下圖顯示允許讀取與寫入資料庫的 Web Forms 頁面：

![Contacts - Edit Page](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms00.png)

>[AZURE.NOTE] 
若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有這類帳戶，可以<a href="/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">啟用自己的 Visual Studio 訂閱者權益</a>，或是<a href="/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">申請免費試用</a>。 如果您想要註冊帳戶前開始使用 Azure，請移至 [試用 App Service](https://tryappservice.azure.com/), ，您可以立即建立短期的 ASP.NET 簡易版網站在 Azure 中免費。 不需要信用卡，沒有承諾。

##設定開發環境 
首先，安裝 Visual Studio 2013 和 Azure SDK for .NET 以設定您的開發環境。

1. 安裝 [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=306566), ，如果您還沒有安裝此軟體。  
2. 安裝 [Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409)。 本教學課程需要 Visual Studio 2013，才能安裝 Azure SDK for Visual Studio 2013。 視您的電腦上有多少 SDK 相依性而定，安裝 SDK 可能需要很長的時間 (從數分鐘到半小時以上不等)。  

3. 如果系統提示您執行或儲存安裝可執行檔，請按一下 [ **執行**。
4. 在 **Web Platform Installer** ] 視窗中，按一下 [ **安裝** 並繼續進行安裝。  
    ![Web Platform Installer](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-01.png)  

      如果您已安裝 SDK，則待安裝的項目會為 0。 若要安裝的項目數目將會顯示在左下方 **Web Platform Installer** 視窗。  

5. 如果您還沒有 **Visual Studio Update 2**, 、 下載及安裝 **[Visual Studio 2013 Update 2](http://www.microsoft.com/download/details.aspx?id=42666)** 或更高版本。  

    >[AZURE.NOTE]  
    您必須安裝 Visual Studio 2013 Update 2 或更高版本，才能在不會出現警告的情況下，在本機使用 Goggle OAuth 2.0 和 SSL。 此外，您需要 Update 2 才能使用 Web Forms Scaffolding。  

安裝完成時，您即可開始進行開發。

##設定 Azure 環境
在這一節中，您將利用在 Azure 中建立 Azure 和 SQL 資料庫，來設定 Azure 環境。

###在 Azure 中建立 Web 應用程式和 SQL Database 
在本教學課程中，您的 Web 應用程式會在共用主控環境中執行，意即其會與其他 Azure App Service 中的 Web 應用程式共用的虛擬機器 (VM) 上執行。 共用主控環境是一種在雲端中開始營運的低成本方法。 因為應用程式是在專用 VM 上執行，所以如果日後您的 Web 流量增加，可依需求對應用程式進行延展。 如果您需要更複雜的架構，可以移轉至 Azure 雲端服務。 雲端服務是執行於您可視本身需求來設定的專用 VM 上。 

Azure SQL Database 是以 SQL Server 技術為基礎來建置的雲端型關聯式資料庫服務。 工具和應用程式如果使用 SQL Server，同樣也可以使用 SQL Database。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **Web 應用程式** 中左側索引標籤，然後按一下 **新增**。  
    ![Web Platform Installer](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-02.png)
2. 按一下 [ **Web 應用程式**, ，然後按一下 [ **自訂建立**。  
    ![自訂建立](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-03.png)  
     **新的 Web 應用程式-自訂建立** 精靈] 隨即開啟。  

3. 在 **建立 Web 應用程式** 步驟的精靈中，輸入字串 **URL** 方塊，以做為您的應用程式唯一的 URL。 完整的 URL 將包含您在此處輸入的字串，加上您在文字方塊旁看到的尾碼。 下圖顯示 URL 可能已被佔用， **您必須選擇不同的 URL**。  
    ![連絡人-建立新的網站](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-04.png)  
4. 在 [Web 主控方案] 下拉式清單中，選擇最接近您位置的區域。 此設定可指定 VM 將在哪個資料中心執行。
5. 在 **資料庫** 下拉式清單中，選擇 **建立免費的 20 MB SQL database**。
6. 在 **DB Connection String Name** 方塊中，保留預設值為 *DefaultConnection*。
7. 按一下方塊底部的箭頭。 
精靈會進入 **指定資料庫設定** 步驟。
8. 在 **名稱** 方塊中，輸入 *`ContactDB`*。  
    ![資料庫設定](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-05.png)  
9. 在 **伺服器** 方塊中，選取 **新的 SQL 資料庫** 伺服器。
或者，如果您已建立 SQL Server 資料庫，可從下拉式清單控制項選取該 SQL Server。
10. 設定 **區域** 的相同區域中，您建立 web 應用程式。
11. 輸入系統管理員 **登入名稱** 和 **密碼**。 
如果您選取 **新的 SQL 資料庫伺服器** 不輸入現有的名稱和密碼，而輸入新名稱和密碼，您現在定義以未來存取資料庫時使用。 如果選取先前建立的 SQL Server，系統會提示您提供先前建立之 SQL Server 帳戶名稱的密碼。 本教學課程中，您將不會檢查 **進階** 方塊。
12. 按一下方塊右下角的打勾記號來表示完成。

 **Azure 傳統入口網站** 傳回 **Web 應用程式** ] 頁面上，而 **狀態** 資料行會顯示建立網站。 不久之後 （通常小於一分鐘）， **狀態** 欄會顯示已成功建立站台。 在左側導覽列中的儲存體中的站台數旁會出現 **Web 應用程式** 圖示，然後資料庫數目旁會出現 **SQL 資料庫** 圖示。
##建立 ASP.NET Web Forms 應用程式 
您已建立 Web 應用程式，但其中還沒有內容。 接下來的步驟是建立要發行至 Azure 的 Visual Studio Web 應用程式。
###建立專案 
1. 選取 **新的專案** 從 **檔案** Visual Studio 中的功能表。  
    ![[檔案] 功能表-新專案](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms01.png)  
2. 選取 **範本** ]-> [ **Visual C#** ]-> [ **Web** 左邊的範本群組。 
3. 選擇 **ASP.NET Web 應用程式** 中間欄的範本。
4. 將您的專案 *ContactManager* 按一下 **確定**。  
    ![新增專案] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms02.png)  

      本教學課程系列中的專案名稱是 **ContactManager**。 建議您使用完全相同的專案名稱，以便整個教學課程系列中所提供的程式碼可如預期般運作。  

5. 在 **新增 ASP.NET 專案** 對話方塊中，選取 **Web Form** 範本。 取消核取 **定域機組中的主機** 如果選取核取方塊，然後按一下 **確定**。  
    ![[新增 ASP.NET 專案] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms03.png)  
    將建立 Web Forms 應用程式。

###更新主要頁面
在 ASP.NET Web Forms 中，主要頁面可用來建立應用程式中頁面的一致性版面配置。 單一主要頁面可為應用程式中的所有頁面 (或頁面群組) 定義您想要的外觀與風格及標準行為。 接著，您可以建立包含所需顯示內容的個別內容頁面。 當使用者要求內容頁面時，ASP.NET 會將這些頁面與主要頁面合併，以產生結合主要頁面之配置與內容頁面之內容的輸出。
新的網站需要更新的應用程式名稱與連結。 此連結將指向顯示連絡人詳細資料的頁面。 若要進行這些變更，您需修改主要頁面上的 HTML。 

1. 在 **方案總管] 中**, ，尋找並開啟 *Site.Master* 頁面。 
2. 如果頁面處於 **設計** 檢視中，切換至 **來源** 檢視。
3. 透過修改或加入標記來更新主版頁面，使頁面標記的顯示方式如下所示：

        <%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>
        
        <!DOCTYPE html>
        
        <html lang="en">
        <head runat="server">
            <meta charset="utf-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1.0" />
            <title><%: Page.Title %> - Contact Manager</title>
        
            <asp:PlaceHolder runat="server">
                <%: Scripts.Render("~/bundles/modernizr") %>
            </asp:PlaceHolder>
            <webopt:bundlereference runat="server" path="~/Content/css" />
            <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
        
        </head>
        <body>
            <form runat="server">
                <asp:ScriptManager runat="server">
                    <Scripts>
                        <%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                        <%--Framework Scripts--%>
                        <asp:ScriptReference Name="MsAjaxBundle" />
                        <asp:ScriptReference Name="jquery" />
                        <asp:ScriptReference Name="bootstrap" />
                        <asp:ScriptReference Name="respond" />
                        <asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                        <asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                        <asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                        <asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                        <asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                        <asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                        <asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                        <asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                        <asp:ScriptReference Name="WebFormsBundle" />
                        <%--Site Scripts--%>
                    </Scripts>
                </asp:ScriptManager>
        
                <div class="navbar navbar-inverse navbar-fixed-top">
                    <div class="container">
                        <div class="navbar-header">
                            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                                <span class="icon-bar"></span>
                                <span class="icon-bar"></span>
                                <span class="icon-bar"></span>
                            </button>
                            <a class="navbar-brand" runat="server" id="ContactDemoLink" href="~/Contacts/Default.aspx">Contact Demo</a>
                        </div>
                        <div class="navbar-collapse collapse">
                            <ul class="nav navbar-nav">
                                <li><a runat="server" href="~/">Home</a></li>
                                <li><a runat="server" href="~/About">About</a></li>
                                <li><a runat="server" href="~/Contact">Contact</a></li>
                            </ul>
                            <asp:LoginView runat="server" ViewStateMode="Disabled">
                                <AnonymousTemplate>
                                    <ul class="nav navbar-nav navbar-right">
                                        <li><a runat="server" href="~/Account/Register">Register</a></li>
                                        <li><a runat="server" href="~/Account/Login">Log in</a></li>
                                    </ul>
                                </AnonymousTemplate>
                                <LoggedInTemplate>
                                    <ul class="nav navbar-nav navbar-right">
                                        <li><a runat="server" href="~/Account/Manage" title="Manage your account">Hello, <%: Context.User.Identity.GetUserName()  %> !</a></li>
                                        <li>
                                            <asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                        </li>
                                    </ul>
                                </LoggedInTemplate>
                            </asp:LoginView>
                        </div>
                    </div>
                </div>
                <div class="container body-content">
                    <asp:ContentPlaceHolder ID="MainContent" runat="server">
                    </asp:ContentPlaceHolder>
                    <hr />
                    <footer>
                        <p>&copy; <%: DateTime.Now.Year %> - Contact Manager</p>
                    </footer>
                </div>
            </form>
        </body>
        </html>

    後續在本教學課程中，您將新增 Web Forms 樣板。 Scaffolding 將建立上述 “Contact Demo” 連結的參考頁面。
 
###在本機執行應用程式 
 
1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 *Default.aspx* 頁面，並選取 **設定為起始頁**。 
2. 接著，請按 **CTRL + F5** 執行應用程式。  
    預設的瀏覽器視窗中會出現應用程式預設頁面。
    ![連絡人-建立新的網站](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms04.png)  

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。 稍後，您將新增資料庫功能，以及可顯示與編輯連絡人資料的必要頁面。
###將應用程式部署至 Azure
現在您已在本機建立與執行您的應用程式，是將應用程式部署至 Azure 的時候了。

1. 在 Visual Studio 中，以滑鼠右鍵按一下專案中的 **方案總管] 中** ，然後選取 **發行** 從內容功能表。  
    ![選取 [發行]](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms05.png)  
     **發行 Web** 對話方塊隨即出現。  

2. 在 **設定檔** ] 索引標籤的 **發行 Web** 對話方塊中，按一下 [ **Azure Web 應用程式**。  
      
3. 如果您尚未登入，按一下 [ **登入** 按鈕 **選取現有的 Web 應用程式** 對話方塊。 完成登入後，請選取您在本教學課程第一個部分中所建立的 Web 應用程式。 按一下 [ **確定** 以繼續。  
    ![選取現有的網站] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms07.png)  
Visual Studio 將會下載您的發佈設定。
4. 在 **發行 Web** 對話方塊中，按一下 [ **發行**。  
    ![發行 Web] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms08.png)  
您會看到整體發行狀態 **Web 發佈活動** Visual Studio 中的視窗 ︰  
    ![Web 發佈活動](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms09.png)  

您建立的應用程式現在正在雲端中執行。 下次當您從 Visual Studio 部署應用程式時，只會部署變更的 (或新的) 檔案。  
    ![App in Browser](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms10.png)  

>[AZURE.NOTE] 
如果您在發佈到已建立的 Web 應用程式時遇到錯誤，則您可以在加入新檔案之前先清除位置。  
發行您的應用程式，但在 **發行 Web** 對話方塊中，選取 **設定** ] 索引標籤。 然後，將設定為 **偵錯** 然後選取選項來 **移除目的端的其他檔案**。 選取 **發行** 來部署您的應用程式。  
    ![發行 Web] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms11.png)  

##新增資料庫至應用程式 
接下來，您將更新 Web Forms 應用程式，在預設資料庫中新增顯示與更新連絡人，以及儲存資料的功能。 建立 Web Forms 專案時，預設也會建立資料庫。 應用程式將使用 Entity Framework，以存取資料庫以及讀取和更新資料庫中的資料。
###新增資料模型類別 
首先，使用程式碼建立簡單的資料模型。 此資料模型會包含在名為 `Contacts` 的類別內。 `Contacts` 類別名稱，是為了避免與根據 Web Forms 範本建立的 Contact.aspx.cs 檔案中所包含的 `Contact` 類別衝突。  

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 *模型* 資料夾，然後選取 **新增** ]-> [ **類別**。  
    ![選取類別](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms12.png)  
     **加入新項目** 對話方塊隨即出現。  

2. 將這個新類別 *Contacts.cs* 按一下 **新增**。  
    ![加入新項目對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13.png)  
3. 使用下列程式碼來取代預設程式碼：  

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        
        namespace ContactManager.Models
        {
            public class Contacts
            {
                [ScaffoldColumn(false)]
                [Key]
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }

 **連絡人** 類別定義您將會儲存每個連絡人，以及主索引鍵的資料 (`ContactID`) 資料庫需要。  **連絡人** 類別代表將顯示的連絡人資料。 每個連絡人物件的執行個體會對應到關聯式資料庫資料表中的資料列，而每個連絡人類別的屬性會對應到關聯式資料庫資料表中的資料欄。 後續在本教學課程中，您將檢閱資料庫中所包含的連絡人資料。

###Web Forms Scaffolding 
您已建立 **連絡人** 模型上述的類別。 現在，您可以使用 Web Forms Scaffolder 來產生 *清單*, ，*插入*, ，*編輯* 和 *刪除* 頁面，以供使用此資料時。 Web Forms Scaffolder 使用 Entity Framework、Bootstrap 和動態資料。 依預設，使用 Visual Studio 2013 時，Web Forms Scaffolder 會以擴充功能的形式安裝在您的專案上，以作為專案範本的一部分。

您可透過下列步驟來使用 Web Forms Scaffolder。

1. 在 Visual Studio 中，從功能表列選取 **工具** ]-> [ **擴充功能和更新**。  
     **擴充功能和更新** 對話方塊隨即出現。
2. 從對話方塊的左窗格中，選取 **線上** ]-> [ **Visual Studio 元件庫** ]-> [ **工具** ]-> [ **樣板**。
3. 如果您在清單中看不到 'Web Forms Scaffolding'，請在對話方塊右側的搜尋方塊中輸入 'Web Forms Scaffolding'。  
4. 如果尚未安裝 Web Forms Scaffolder，請選取 **下載** 下載並安裝 Web Forms Scaffolding'。 如有需要，請重新啟動 Visual Studio。 系統提出要求時，請務必將變更儲存至專案。  
    ![擴充功能和更新對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/ExtensionsAndUpdatesDB.png)  
5. 建置專案 (**Ctrl + Shift + B**)。  
    使用樣板機制前必須先建置專案。  
6. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 *專案* ，然後選取 **新增** ]-> [ **新增建構項目**。  
     **Add Scaffold** 對話方塊隨即出現。
7. 選取 **Web Form** 左的窗格中選取 **Web Forms Pages using Entity Framework** 從中央窗格。 然後按一下 [ **新增**。  
    ![新增 Scaffold] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13a.png)  
     **Add Web Forms Pages** 對話方塊隨即出現。  

8. 在 **Add Web Forms Pages** ] 對話方塊中，將 **模型類別** 到 `Contacts (ContactManager.Models)`。 設定 **資料內容類別** 到 `ApplicationDbContext (ContactManager.Models)`。 然後按一下 [ **新增**。 
    ![新增 Web Forms Pages] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13b.png)  

Web Form Scaffolder 會新增新的資料夾，其中包含 *Default.aspx*, ，*Delete.aspx*, ，*Edit.aspx*, ，和 *Insert.aspx* 頁面。 Web Forms Scaffolder 還會建立 *Entitytemplates* 所在的資料夾 *Fieldtemplates* 資料夾和 *Dynamicdata* 資料夾。 `ApplicationDbContext` 同時用於成員資格資料庫和連絡人資料。

###設定應用程式以使用資料模型 
下一個工作是啟用 Code First 移轉功能，以便根據建立的資料模型建立資料庫。 另外，您將新增範例資料和資料初始設定式。  

1. 在 **工具** 功能表上，選取 **NuGet 封裝管理員** 然後 **Package Manager Console**。  
    ![新增 Web Forms Pages] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13c.png)  
2. 在 [Package Manager Console] 視窗中，輸入下列命令：  

        enable-migrations
 
    Enable-migrations 命令會建立 *移轉* 資料夾，並放入該資料夾中 *Configuration.cs* 來植入資料庫及設定資料移轉，您可以編輯的檔案。  
3. 在 **Package Manager Console** ] 視窗中，輸入下列命令 ︰  

        add-migration Initial

     `add-migration Initial` 命令中產生名為的檔案 < date_stamp > 初始 *移轉* 建立資料庫的資料夾。 第一個參數 ( Initial ) 是任意的，可用來建立檔案的名稱。 您可以看到新的類別檔案中 **方案總管] 中**。 在 `Initial` 類別中，`Up` 方法會建立 `Contact` 資料表，`Down` 方法 (當您希望返回前個狀態時使用) 則會捨棄該資料表。  
4. 開啟 *migrations\ configuration.cs* 檔案。 

5. 新增下列命名空間：  

        using ContactManager.Models;

6. 以下列程式碼取代 `Seed` 方法：  

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contacts
               {
                   ContactId = 1,
                   Name = "Ivan Irons",
                   Address = "One Microsoft Way",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "ivani@wideworldimporters.com",
               },
               new Contacts
                {
                    ContactId = 2,
                    Name = "Brent Scholl",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "brents@wideworldimporters.com",
                },
                new Contacts
                {
                    ContactId = 3,
                    Name = "Terrell Bettis",
                    Address = "9012 State St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "terrellb@wideworldimporters.com",
                },
                new Contacts
                {
                    ContactId = 4,
                    Name = "Jo Cooper",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "joc@wideworldimporters.com",
                },
                new Contacts
                {
                    ContactId = 5,
                    Name = "Ines Burnett",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "inesb@wideworldimporters.com",
                }
                );
        }

    此程式碼會以連絡人資訊初始化 (植入) 該資料庫。 如需植入資料庫的詳細資訊，請參閱 [植入及偵錯 Entity Framework (EF) Db](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)。  
7. 在 **Package Manager Console** 輸入命令 ︰  

        update-database

`update-database` 會執行第一次移轉，並建立資料庫。 根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。  
    ![封裝管理員主控台](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13d.png)  

###在本機執行應用程式並顯示資料 
現在就執行應用程式，了解如何檢視連絡人。

1. 首先，建置專案 (**Ctrl + Shift + B**)。  
2. 按下 **Ctrl + F5** 執行應用程式。  
    瀏覽器便會開啟並顯示 *Default.aspx* 頁面。
3. 選取 **Contact Demo** 頂端的 [要顯示的頁面連結 *連絡人清單* 頁面。  
    ![連絡人清單] 頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms17.png)  

##對專案啟用 SSL 
安全通訊端層 (SSL) 是一種定義的通訊協定，允許 Web 伺服器和 Web 用戶端透過加密，以更安全的方式進行通訊。 未使用 SSL 時，在用戶端和伺服器之間傳送的資料會開放給任何可實體存取網路的人員進行封包探查。 此外，數種常見驗證結構描述在一般的 HTTP 上並不是很安全。 尤其是，基本驗證和表單驗證會傳送未加密的認證。 為了安全的理由，這些驗證結構描述必須使用 SSL。

1. 在 **方案總管] 中**, ，按一下 [ **ContactManager** 專案，再按 **F4** 顯示 **屬性** 視窗。 
2. 變更 **啟用 SSL** 到 `true`。 
3. 複製 **SSL URL** 供稍後使用。  
    SSL URL 將是 `https://localhost:44300/` 除非您先前已建立 SSL web 應用程式 （如下所示）。
    ![專案屬性](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms18.png)  
4. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 **Contact Manager** 專案，然後按一下 **屬性**。
5. 在左側索引標籤中，按一下 [ **Web**。
6. 變更 **專案 Url** 使用 **SSL URL** 先前儲存的。  
    ![專案 Web 屬性](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms19.png)  
7. 儲存頁面按 **CTRL + S**。
8. 按下 **Ctrl + F5** 執行應用程式。  
Visual Studio 將會顯示可避開 SSL 警告的選項。  
9. 按一下 [ **是** 以信任 IIS Express SSL 憑證並繼續。  
    ![IIS Express SSL 憑證的詳細資訊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms20.png)  
    隨即顯示一則安全性警告。  

10. 按一下 [ **是** 將憑證安裝到您的 localhost。  
    ![安全性警告對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21.png)  
    瀏覽器視窗隨即出現。  

您可以輕鬆地使用 SSL 在本機測試 Web 應用程式。



##新增 OAuth 2.0 提供者 
ASP.NET Web Forms 提供成員資格和驗證的增強功能選項。 這些增強功能包括 OAuth。 OAuth 是一種開放式通訊協定，可讓 Web、行動和桌面應用程式以簡單、標準的方法執行安全授權。 ASP.NET MVC 網際網路範本使用 OAuth 來公開 Facebook、Twitter、Google 和 Microsoft 的驗證提供者身分。 雖然本教學課程僅使用 Google 作為驗證提供者，但您可以輕易修改程式碼來使用任何提供者。 實作其他提供者的步驟，與您將在本教學課程中看到的步驟極為類似。 

除了驗證，本教學課程也會使用角色來實作授權。 只有您新增至 `canEdit` 角色的使用者才能建立、編輯或刪除連絡人。 

下列步驟可新增 Google 驗證提供者。

1. 開啟 *App_Start\Startup.Auth.cs* 檔案。 
2. 移除 `app.UseGoogleAuthentication()` 方法中的註解字元，然後此方法會顯示如下：  

        app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
        {
            ClientId = "",
            ClientSecret = ""
        });

3. 瀏覽至 [Google 開發人員主控台](https://console.developers.google.com/)。 您還需要使用您的 Google 開發人員電子郵件帳戶 (gmail.com) 登入。 如果您沒有 Google 帳戶，請選取 **建立帳戶** 連結。  
    接下來，您會看到 **Google 開發人員主控台**。
    ![Google 開發人員主控台](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21a.png)  

4. 按一下 [ **選取專案** > **建立專案** ，並輸入專案名稱和識別碼 （您可以使用預設值）。 然後按一下 [ **協議] 核取方塊** 和 **建立** ] 按鈕。  
    ![Google-New Project](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21b.png)  
    幾秒鐘內即可建立新的專案，您的瀏覽器便會顯示新的專案頁面。
5. 在 **Google 開發人員主控台** 下拉式選單中，按一下 [ **API 管理員**, ，然後按一下 [ **認證**。
6. 按一下 [ **建立新的用戶端識別碼** 下 **OAuth**。  
     **Create Client ID** 對話方塊隨即出現。
    ![Google-Create Client ID](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21c.png)  
7. 在 **Create Client ID** ] 對話方塊中，保留預設的 **Web 應用程式** 應用程式類型。  
8. 設定 **授權 JavaScript 來源** 您稍早在本教學課程中使用的 SSL url (**https://localhost:44300/ /** 除非您已建立其他 SSL 專案)。  
    此 URL 會是應用程式的原始來源。 在此範例中，您將僅輸入 localhost 測試 URL。 不過，您可以在帳戶中輸入多個 URL 以供 localhost 和生產使用。  

9. 設定 **Authorized Redirect URI** 如下 ︰  

        https://localhost:44300/signin-google  

    此值是 ASP.NET OAuth 使用者與 Google OAuth 伺服器進行通訊的 URI。 請記住您上面使用的 SSL URL (**https://localhost:44300/ /** 除非您已建立其他 SSL 專案)。 
 
10. 按一下 [ **建立** ] 按鈕。
11. 在 Visual Studio 中，更新 `UseGoogleAuthentication` 方法 *Startup.Auth.cs* 複製並貼上頁面 **AppId** 和 **應用程式密鑰** 到方法。  **AppId** 和 **應用程式密鑰** 值如下所示的範例，將無法運作。  

        using System;
        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;
        using Microsoft.AspNet.Identity.Owin;
        using Microsoft.Owin;
        using Microsoft.Owin.Security.Cookies;
        using Microsoft.Owin.Security.DataProtection;
        using Microsoft.Owin.Security.Google;
        using Owin;
        using ContactManager.Models;
        
        namespace ContactManager
        {
            public partial class Startup {
        
                // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
                public void ConfigureAuth(IAppBuilder app)
                {
                    // Configure the db context and user manager to use a single instance per request
                    app.CreatePerOwinContext(ApplicationDbContext.Create);
                    app.CreatePerOwinContext(ApplicationUserManager.Create);
        
                    // Enable the application to use a cookie to store information for the signed in user
                    // and to use a cookie to temporarily store information about a user logging in with a third party login provider
                    // Configure the sign in cookie
                    app.UseCookieAuthentication(new CookieAuthenticationOptions
                    {
                        AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                        LoginPath = new PathString("/Account/Login"),
                        Provider = new CookieAuthenticationProvider
                        {
                            OnValidateIdentity = SecurityStampValidator.OnValidateIdentity(
                                validateInterval: TimeSpan.FromMinutes(20),
                                regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
                        }
                    });
                    // Use a cookie to temporarily store information about a user logging in with a third party login provider
                    app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
        
                    // Uncomment the following lines to enable logging in with third party login providers
                    //app.UseMicrosoftAccountAuthentication(
                    //    clientId: "",
                    //    clientSecret: "");
        
                    //app.UseTwitterAuthentication(
                    //   consumerKey: "",
                    //   consumerSecret: "");
        
                    //app.UseFacebookAuthentication(
                    //   appId: "",
                    //   appSecret: "");
        
                    app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
                    {
                        ClientId = "000000000000.apps.googleusercontent.com",
                        ClientSecret = "00000000000"
                    });
                }
            }
        }

12. 按下 **CTRL + F5** 以建置並執行應用程式。 按一下 [ **登入** 連結。
13. 在 **用來登入的另一個服務**, ，按一下 [ **Google**。  
    ![登入](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21d.png)  
14. 如果您需要輸入認證，您會被重新導向至 Google 網站，您可以在此輸入認證。  
    ![Google - 登入](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21e.png)  
15. 輸入認證後，系統便會提示您提供權限給剛建立的 Web 應用程式：  
    ![專案預設服務帳戶](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21f.png)  
16. 按一下 [ **接受**。 您現在會被重新導向回到 **註冊** 頁面 **ContactManager** 應用程式，您可以在此註冊 Google 帳戶。
    ![註冊您的 Google 帳戶](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21g.png)  
17. 您可以選擇變更用於 Gmail 帳戶的本機電子郵件註冊名稱，但您通常會想保留預設電子郵件別名 (也就是，您用來驗證的名稱)。 按一下 [ **登入**。

##使用成員資格 API 來限制存取 
ASP.NET 身分識別是成員資格系統，可用於建置 ASP.NET Web 應用程式時的驗證。 它會讓整合使用者特定設定檔資料與應用程式資料變得再容易不過了。 另外，ASP.NET 身分識別可讓您選擇應用程式中使用者設定檔的持續性模型。 您可以將資料儲存在 SQL Server 資料庫或其他資料存放區，包括 *NoSQL* 資料存放區，例如 Azure 儲存體資料表。

使用預設的 ASP.NET Web Forms 範本，您便擁有內建的成員資格功能，可在應用程式執行時立即使用。 您將使用 ASP.NET 身分識別新增系統管理員角色，並對該角色指派使用者。 接著，您將了解如何限制存取管理資料夾，以及該資料夾中用來修改連絡人資料的頁面。

###新增系統管理員 
使用 ASP.NET 身分識別，您可以新增系統管理員角色，並使用程式碼對該角色指派使用者。 

1. 在 **方案總管] 中**, ，開啟 *Configuration.cs* 檔案中 *移轉* 資料夾。
2. 在 `ContactManger.Migrations` 命名空間中新增以下 `using` 陳述式：  

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

3. 將下列 `AddUserAndRole` 方法新增至 `Seed` 方法之後的 `Configuration` 類別：  

        public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
        {
            IdentityResult IdRoleResult;
            IdentityResult IdUserResult;
        
            var roleStore = new RoleStore<IdentityRole>(context);
            var roleMgr = new RoleManager<IdentityRole>(roleStore);
        
            if (!roleMgr.RoleExists("canEdit"))
            {
                IdRoleResult = roleMgr.Create(new IdentityRole { Name = "canEdit" });
            }
        
            //var userStore = new UserStore<ApplicationUser>(context);
            //var userMgr = new UserManager<ApplicationUser>(userStore);
            var userMgr = new UserManager<ApplicationUser>(new UserStore<ApplicationUser>(context));
        
            var appUser = new ApplicationUser
            {
                UserName = "canEditUser@wideworldimporters.com",
                Email = "canEditUser@wideworldimporters.com"
            };
            IdUserResult = userMgr.Create(appUser, "Pa$$word1");
        
            if (!userMgr.IsInRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit"))
            {
              //  IdUserResult = userMgr.AddToRole(appUser.Id, "canEdit");
                IdUserResult = userMgr.AddToRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit");
            }
        }

4. 從 `Seed` 方法開頭新增呼叫至 `AddUserAndRole` 方法。 請注意，這裡只顯示 `Seed` 的開頭。  

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            AddUserAndRole(context);

5. 在儲存所有變更之後, **Package Manager Console** 執行下列命令 ︰  

        Update-Database

此程式碼會建立名為的新角色 `canEdit` 和電子郵件 canEditUser@wideworldimporters.com 建立新的本機使用者。 然後，程式碼會將 canEditUser@wideworldimporters.com 至 `canEdit` 角色。 如需詳細資訊，請參閱 [ASP.NET 身分識別](http://www.asp.net/identity) 資源網頁。  

###限制對管理資料夾的存取 
 **ContactManager** 範例應用程式允許匿名使用者和登入的使用者檢視連絡人。 不過，完成此區段之後，指派給 "canEdit" 角色的已登入使用者將會是唯一能夠修改連絡人的使用者。

您將建立名為的資料夾 *Admin* 只有指派給"canEdit"角色的使用者可以存取。

1. 在 **方案總管] 中**, ，新增到子資料夾 *連絡人* 資料夾，然後將新的子資料夾命名 *Admin*。
2. 將下列檔案從 *連絡人* 資料夾 *Contacts/Admin* 資料夾 ︰  
    - *Delete.aspx *和* Delete.aspx.cs*
    - *Edit.aspx *和* Edit.aspx.cs*
    - *Insert.aspx *和* Insert.aspx.cs*
3. 更新中的連結參考 *contacts* 加上"Admin / 」 連結的頁面參考前面 *Insert.aspx*, ，*Edit.aspx*, ，和 *Delete.aspx* ，如下所示 ︰  

        <%@ Page Title="ContactsList" Language="C#" MasterPageFile="~/Site.Master" CodeBehind="Default.aspx.cs" Inherits="ContactManager.Contacts.Default" ViewStateMode="Disabled" %>
        <%@ Register TagPrefix="FriendlyUrls" Namespace="Microsoft.AspNet.FriendlyUrls" %>
        
        <asp:Content runat="server" ContentPlaceHolderID="MainContent">
            <h2>Contacts List</h2>
            <p>
                <asp:HyperLink runat="server" NavigateUrl="Admin/Insert.aspx" Text="Create new" />
            </p>
            <div>
                <asp:ListView runat="server"
                    DataKeyNames="ContactId" ItemType="ContactManager.Models.Contacts"
                    AutoGenerateColumns="false"
                    AllowPaging="true" AllowSorting="true"
                    SelectMethod="GetData">
                    <EmptyDataTemplate>
                        There are no entries found for Contacts
                    </EmptyDataTemplate>
                    <LayoutTemplate>
                        <table class="table">
                            <thead>
                                <tr>
                                    <th>Name</th>
                                    <th>Address</th>
                                    <th>City</th>
                                    <th>State</th>
                                    <th>Zip</th>
                                    <th>Email</th>
                                    <th>&nbsp;</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr runat="server" id="itemPlaceholder" />
                            </tbody>
                        </table>
                    </LayoutTemplate>
                    <ItemTemplate>
                        <tr>
                            <td>
                                <asp:DynamicControl runat="server" DataField="Name" ID="Name" Mode="ReadOnly" />
                            </td>
                            <td>
                                <asp:DynamicControl runat="server" DataField="Address" ID="Address" Mode="ReadOnly" />
                            </td>
                            <td>
                                <asp:DynamicControl runat="server" DataField="City" ID="City" Mode="ReadOnly" />
                            </td>
                            <td>
                                <asp:DynamicControl runat="server" DataField="State" ID="State" Mode="ReadOnly" />
                            </td>
                            <td>
                                <asp:DynamicControl runat="server" DataField="Zip" ID="Zip" Mode="ReadOnly" />
                            </td>
                            <td>
                                <asp:DynamicControl runat="server" DataField="Email" ID="Email" Mode="ReadOnly" />
                            </td>
                            <td>
                                <a href="Admin/Edit.aspx?ContactId=<%#: Item.ContactId%>">Edit</a> | 
                                <a href="Admin/Delete.aspx?ContactId=<%#: Item.ContactId%>">Delete</a>
                            </td>
                        </tr>
                    </ItemTemplate>
                </asp:ListView>
            </div>
        </asp:Content>

4. 在下列三個檔案中，將 `Response.Redirect("Default.aspx")` 程式碼的六個參考更新到 `Response.Redirect("~/Contacts/Default.aspx")`：  
    - *Delete.aspx.cs*
    - *Edit.aspx.cs*
    - *Insert.aspx.cs*  

    現在，當您顯示與更新連絡人資料時，這些連結皆可正常運作。
5. 若要限制存取 *Admin* 資料夾中，從 **方案總管] 中** 以滑鼠右鍵按一下 *Admin* 資料夾，然後選取 **加入新項目**。
6. 從 Visual C# Web 範本清單中，選取 **Web 組態檔** 中間清單中，接受預設名稱 *Web.config*, ，然後選取 **新增**。
7. 取代現有的 XML 內容中 *Web.config* 取代為下列檔案 ︰

        <?xml version="1.0"?>
        <configuration>
          <system.web>
            <authorization>
              <allow roles="canEdit"/>
              <deny users="*"/>
            </authorization>
          </system.web>
        </configuration>

8. 儲存 *Web.config* 檔案。 
     *Web.config* 檔案指定只有指派給"canEdit"角色的使用者可以存取中所包含的頁面 *Admin* 資料夾。 

當不屬於"canEdit"角色的使用者嘗試修改資料時，則會重新導向至 *登入* 頁面。

##將使用資料庫的應用程式部署至 Azure 
現在，Web 應用程式已完成，您可以將它發行至 Azure。

###發行應用程式 
1. 在 Visual Studio 中建置專案 (**Ctrl + Shift + B**)。
2. 中的專案上按一下滑鼠右鍵 **方案總管] 中** ，然後選取 **發行**。  
    ![發佈功能表選項](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22.png)  
     **發行 Web** 對話方塊隨即出現。  
    ![發行 Web] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22a.png)  
3. 從 **設定檔** 索引標籤上，選取 **Azure App Service** 作為發行目標，如果未選取。  
    ![發行 Web] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms23.png)  
4. 按一下 [ **登入** 如果您沒有已登入。
5. 選取您稍早在本教學課程中建立的現有 web 應用程式 **現有 Web 應用程式** 下拉式清單方塊，然後按一下 **確定** ] 按鈕。  
    ![選取現有的網站] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms25.png)  
    如果系統要求您將變更儲存至設定檔時，選取 **是**。
6. 按一下 [ **設定** ] 索引標籤。  
    ![選取現有的網站] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26.png)  
7. 設定 **組態** 下拉式方塊設 **偵錯**。
8. 按一下 [ **向下箭號** 旁的圖示 **ApplicationDbContext** 並將它設定為 **ContactDB**。
9. 檢查 **Execute Code First Migrations** 核取方塊。  
    在本範例中，您應只在首次發佈應用程式時選取此核取方塊。 如此一來， *種子* 方法中的 *Configuration.cs* 檔案將只呼叫一次。  

10. 然後按一下 [ **發行**。  
    您的應用程式便會發佈到 Azure。

>[AZURE.NOTE]  
若在建立發佈設定檔後關閉再重新開啟 Visual Studio，可能無法在下拉式清單中看到連接字串。 這種情況下，而不是編輯您稍早建立的發行設定檔，建立一個新的相同方法，來更早版本，並依照下列步驟上 **設定** ] 索引標籤。)  

###在 Azure 中檢閱應用程式 
1. 在瀏覽器中，按一下 [ **Contact Demo** 連結。  
    [連絡人清單] 便會隨即顯示。
    ![在瀏覽器中列出的連絡人](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms27.png)  

2. 選取 **新建** 上 **連絡人清單** 頁面。  
    ![在瀏覽器中列出的連絡人](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms29.png)  
    將您重新導向 **登入** 頁面，因為您不使用可修改連絡人的帳戶登入。  
3. 在輸入以下的電子郵件和密碼之後，按一下 **登入** ] 按鈕。  
    **電子郵件**: `canEditUser@wideworldimporters.com`  
    **密碼**: `Pa$$word1`  
    ![登入頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms28.png)  

4. 每個欄位中輸入新的資料，然後按 **插入** ] 按鈕。  
    ![新增新連絡人] 頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms30.png)  
     *EditContactList.aspx* 頁面會顯示新的記錄。  
    ![新增新連絡人] 頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms31.png)  

5. 選取 **登出** 連結。

###停止應用程式 
為了防止其他人註冊並使用您的範例應用程式，您將停止 Web 應用程式。

1. 在 Visual Studio 的 **檢視] 功能表**, ，請選取 **伺服器總管**。 
2. 在 **伺服器總管**, ，瀏覽至 **Web 應用程式**。
3. 每個 web 應用程式執行個體上按一下滑鼠右鍵，然後選取 **停止 Web 應用程式**。  
    ![停止網站功能表項目](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26a.png)  

    或者，從 Azure 傳統入口網站，您可以選取 web 應用程式，然後按一下 **停止** 在頁面底部的圖示。  
    ![新增新連絡人] 頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26b.png)  

##檢閱資料庫 
了解如何直接檢視與修改資料庫十分重要。 了解如何直接與資料庫合作，可讓您確認資料庫中的資料，並且了解如何在每個資料表中儲存資料。

###檢查 SQL Azure DB 
1. 在 Visual Studio 中開啟 **伺服器總管** 並瀏覽至 **ContactDB**。
2. 以滑鼠右鍵按一下 **ContactDB** ，然後選取 **在 SQL Server 物件總管中開啟**。  
    ![在 SQL Server 物件總管] 功能表項目中開啟](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms32.png)  
3. 如果 **新增防火牆規則** ] 對話方塊隨即顯示，請選取 **新增防火牆規則**。  
      如果您無法展開 **SQL 資料庫** 而且看 **ContactDB** 從 Visual Studio 中，您可以依照指示開放一個防火牆連接埠或連接埠的範圍。 若要這樣做，請依照下列指示 **設定 Azure 防火牆規則** 的結尾附近 [MVC 教學課程](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。 或者，您也可以檢閱本機資料庫的資料建置、 執行，並將資料加入至本機的應用程式 (**CTRL + F5** 從 Visual Studio)。  

4. 如果 **連接到伺服器** ] 對話方塊隨即顯示，請輸入 **密碼** 您在此教學課程和按下開始建立 **連接** ] 按鈕。  
      如果不記得密碼，您可以在本機專案檔案中找到此資訊。 在 **方案總管] 中**, ，依序展開 *屬性* 資料夾，然後展開 [ *PublishProfiles* 資料夾。 開啟 *contactmanager.pubxml* 檔案 （您的檔案可能有不同的名稱）。 搜尋檔案以找出您的發行密碼。

5. 展開 **contactDB** 資料庫，然後展開 **資料表**。
6. 以滑鼠右鍵按一下 **dbo。AspNetUsers** 資料表，然後選取 **檢視資料**。  
    ![檢視資料] 功能表項目](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms34.png)  
    您可以檢視與 canEditUser@contoso.com 使用者相關聯的資料。  
    ![[ContactManager] 視窗](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms35.png)  

###透過編輯資料庫將使用者新增至管理角色 
在本教學課程稍早的章節中，您使用程式碼將使用者新增至 canEdit 角色。 替代的方法是直接在成員資格資料表中操作資料。 下列步驟顯示如何使用此替代方法，將使用者新增至角色。

1. 在 **SQL Server 物件總管**, ，以滑鼠右鍵按一下 **dbo。AspNetUserRoles** ，然後選取 **檢視資料**。
    ![[Aspnetuserroles] 資料](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms36.png)  
2. 複製 *RoleId* 並將其貼至空白 （新） 資料列。  
    ![[Aspnetuserroles] 資料](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms37.png)  
3. 在 **dbo。AspNetUsers** 資料表中，尋找您要放在角色中，的使用者並複製該使用者的 *識別碼*。
4. 貼上複製 *識別碼* 到 **UserId** 欄位中的新資料列的 **AspNetUserRoles** 資料表。  

>[AZURE.NOTE]  
我們正在開發一項工具，讓使用者和角色管理起來簡單得多。  

##後續步驟
如需 ASP.NET Web Form 的詳細資訊，請參閱 [了解 ASP.NET Web Forms](http://www.asp.net/web-forms) 在 ASP.NET web 應用程式和 [Microsoft Azure 教學課程和指南](http://azure.microsoft.com/documentation/services/web-sites/#net)。

本教學課程 MVC 教學課程為基礎 [使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) : Rick anderson (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) 協助 Tom Dykstra 和 Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart))。 

如果您發現喜歡的地方和希望我們改善的地方 (不論是針對本教學課程或其示範的產品)，歡迎留下意見反應。 您的意見反應將協助我們訂出優先改善要務。 您也可以要求，並對新主題進行投票 [告訴我如何處理程式碼](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code)。

 


