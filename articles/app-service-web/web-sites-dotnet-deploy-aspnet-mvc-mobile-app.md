<properties 
    pageTitle="在 Azure App Service 中部署 ASP.NET MVC 5 行動 Web 應用程式" 
    description="指導您如何使用 ASP.NET MVC 5 Web 應用程式的行動功能，將 Web 應用程式部署到 Azure App Service 的教學課程。" 
    services="app-service" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/16/2015" 
    ms.author="cephalin;riande"/>



# 在 Azure App Service 中部署 ASP.NET MVC 5 行動 Web 應用程式

本教學課程將教導您如何建立 ASP.NET MVC 5 的基本概念
適合行動的並將它部署至 Azure App Service 的 web 應用程式。 在本教學課程中，您需要 
[Visual Studio Express 2013 for Web ][visual studio express 2013]
或 Visual Studio Professional Edition (如果您已經
有該版本)。 您可以使用 [Visual Studio 2015] 但螢幕擷取畫面將會不同，您必須使用 ASP.NET 4.x 範本。

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## 您要建置的內容

本教學課程中，將把行動功能新增於簡單
會議清單應用程式中提供 [起始專案 ][starterproject]。 下列螢幕擷取畫面顯示的 ASP.NET 工作階段中已完成
應用程式，在 Internet Explorer 11 F12 的瀏覽器模擬器中所示
開發人員工具。

![][fixedsessionsbytag]

您可以使用 Internet Explorer 11 F12 開發人員工具和 [Fiddler
工具] Fiddler，以協助偵錯程式
重新接收。


## 您要學習的技術

以下是您要學習的內容：

-   如何使用 Visual Studio 2013，將 Web 應用程式直接發行到 Azure App Service 中的 Web 應用程式。
-   ASP.NET MVC 5 範本如何使用 CSS Bootstrap 架構
    改善行動裝置顯示的畫面
-   如何建立行動專用的檢視至目標特定行動裝置
    瀏覽器中，例如 iPhone 和 Android。
-   如何建立回應靈敏的檢視 (可回應不同的檢視
    在裝置上的瀏覽器)

## 設定開發環境

安裝 Azure SDK for .NET 2.5.1 或更新版本，以設定您的開發環境。

1. 若要安裝 Azure SDK for .NET，請按一下底下連結： 如果您尚未安裝 Visual Studio 2013，按下該連結會進行安裝。 本教學課程需要 Visual Studio 2013。 [Azure SDK for Visual Studio 2013 ][azuresdkvs2013]
1. 在 [Web Platform Installer] 視窗中，按一下 [安裝]**** 並繼續進行安裝。

您還需要一個行動瀏覽器模擬器。 下列任一項目都可使用
使用：

-   在瀏覽器模擬器 [Internet Explorer 11 F12 開發人員工具 ][emulatorie11] (用於所有行動
    瀏覽器螢幕擷取畫面中)。 它具有 Windows Phone 8、Windows Phone 7 和 Apple iPad 的使用者代理程式字串預設項目。
-   在瀏覽器模擬器 [Google Chrome DevTools ][emulatorchrome]。 它包含許多 Android 裝置，以及 Apple iPhone、Apple iPad 和 Amazon Kindle Fire 的預設項目。 它也會模擬觸控事件。
-   [Opera Mobile 模擬器 ][emulatoropera]

此處提供具有 C# 原始程式碼的 Visual Studio 專案來幫助您完成
此主題：

-   [入門專案下載 ][starterproject]
-   [完成專案下載 ][completedproject]

## <a name="bkmk_DeployStarterProject"></a>將入門專案部署至 Azure web 應用程式

1.  下載會議清單應用程式 [起始專案 ][starterproject]。

2.  接著在 Windows 檔案總管中，以滑鼠右鍵按一下 Mvc5Mobile.zip 檔案並選擇 [內容]**。

3.  在 **Mvc5Mobile.zip 屬性** 對話方塊中，
選擇 **解除封鎖** ] 按鈕。 (解除封鎖可防止安全性警告
發生於您嘗試使用 *.zip* 您下載的檔案
安全性警告。)

4.  以滑鼠右鍵按一下 *Mvc5Mobile.zip* 檔案，然後選取 **解壓縮全部** 至
解壓縮檔案。

5.  在 Visual Studio 中，開啟 *Mvc5Mobile.sln* 檔案。

6.  在 [方案總管] 中，於專案上按一下滑鼠右鍵，再按一下 [發行]****。

    ![][deployclickpublish]

7.  在 [發行 Web] 中，按一下 [**Microsoft Azure Web Apps**]。

    ![][deployclickwebsites]

8.  按一下 [**登入**]。

    ![][deploysignin]

9.  依照提示來登入您的 Azure 帳戶。

11. [選取現有的 Web 應用程式] 對話方塊此時應顯示為已登入。 按一下 [新增]****。

    ![][deploynewwebsite]

12. 在 [**Web App 名稱**] 欄位中，指定唯一的 App 名稱前置詞。 您的完整 web 應用程式名稱會是 *< 前置詞 >*。 azurewebsites.net。 同時設定 Web 應用程式的 **App Service 方案**、**資源群組**，以及 [**區域**] 欄位。 然後按一下 [建立]****。

    ![][deploysitesettings]

13. 隨即會將新 Web 應用程式的設定填入 [發行 Web] 對話方塊中。 按一下 [發行]****。

    ![][deploypublishsite]

    當 Visual Studio 完成將入門專案發行至 Azure Web 應用程式之後，隨即會開啟桌面瀏覽器以顯示作用中的 Web 應用程式。

14. 啟動行動瀏覽器模擬器，複製的 URL
會議應用程式 (*<prefix>*。 a) 到模擬器，然後按一下
右上角的按鈕，並選取 **依標籤瀏覽**。 若您使用 Internet
Explorer 11 做為預設瀏覽器，您只需輸入 `F12`, ，然後
`Ctrl + 8`, ，然後變更瀏覽器設定檔 **Windows Phone**。 Auch die Eigenschaften
下的圖顯示 *AllTags* (透過選擇直向模式的檢視
**依標籤瀏覽**)。

    ![][alltags]

>[AZURE.NOTE] 當您在 Visual Studio 中偵錯 MVC 5 應用程式時，可以再次將 Web 應用程式發行至 Azure，直接從行動瀏覽器或瀏覽器模擬器確認作用中的 Web 應用程式。

該顯示內容在行動裝置上非常清楚易讀。 您可能也已經
看到一些 Bootstrap CSS 架構所套用的視覺效果。
按一下 [**ASP.NET**] 連結。

![][sessionsbytagasp.net]

ASP.NET 標籤檢視會縮放至適合大小畫面中，而這是 bootstrap
為您自動。 不過，您可以改善此檢視，使其更適合
行動瀏覽器中。 例如， **日期** 欄非常難以
讀取。 稍後在本教學課程中您要變更 *AllTags* 檢視，使其
適合行動裝置。

## <a name="bkmk_bootstrap"></a> Bootstrap CSS 架構

MVC 5 範本中的新功能是內建的 Bootstrap 支援。 您必須
已經看過它是如何立即改善中的不同檢視您
重新接收。 例如，在頂端的導覽列會自動
在瀏覽器寬度較小時，可摺疊。 在桌面瀏覽器中，
請嘗試調整大小的瀏覽器視窗，並觀察導覽列如何改變
其外觀。 這就是內建於
Bootstrap 中回應靈敏的 Web 設計。

若要查看 Web 應用程式未啟動載入時的外觀，請開啟
*App\_Start\\BundleConfig.cs* 和註解化包含
*bootstrap.js* 和 *bootstrap.css*。 下列程式碼會顯示 `RegisterBundles` 方法變更之後的最後
兩個陳述式 `RegisterBundles` 之後變更的方法:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));
    
    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

按 `CTRL+F5` 執行應用程式。

發現可摺疊的導覽列現在只是一般
未排序的清單。 再按一下 [**依標籤瀏覽**]，然後按一下 [**ASP.NET**]。
在行動模擬器檢視中，您現在可以看到它不再
縮放至適合大小 [] 畫面中，您必須橫向捲動才能看到
右邊的表格。

![][sessionsbytagasp.netnobootstrap]

復原您的變更並重新整理行動瀏覽器
若要確認適合行動的顯示畫面已還原。

Bootstrap 並非專屬於 ASP.NET MVC 5，而且您可以利用
這些功能在任何 web 應用程式。 但它目前內建於
ASP.NET MVC 5 專案範本，所以 MVC 5 Web 應用程式可以使用
利用啟動安裝程式預設。

如需 Bootstrap 的詳細資訊，請移至
[Bootstrap ][bootstrapsite] 站台。

您將在下一節看到如何提供行動瀏覽器專用的
檢視。

## <a name="bkmk_overrideviews"></a> 覆寫檢視、 配置與部分檢視

您可以覆寫任何檢視 (包括配置與部分檢視)
一般情況下，針對個別行動瀏覽器或行動的瀏覽器
特定瀏覽器。 若要提供行動裝置專屬的檢視，您可以複製檢視
檔案，並加入 *。行動* 的檔案名稱。 例如，若要建立行動
*索引* ] 檢視中，您可以複製 *Views\\Home\\Index.cshtml* 至
*Views\\Home\\Index.Mobile.cshtml*。

本節將建立行動裝置專屬的配置檔案。

若要開始，複製 *Views\\Shared\\\_Layout.cshtml* 至
*Views\\Shared\\\_Layout.Mobile.cshtml*。 開啟 *\_Layout.Mobile.cshtml*
將標題從變更和 **MVC5 Application** 至 * * MVC5 應用程式
(Mobile) * *。

在每個 `Html.ActionLink` 導覽列的呼叫當中，移除 「 Browse by 」 中每個連結
*ActionLink*。下列程式碼會顯示已完成 `< u l class ="導覽瀏覽列 – 導覽">` 行動配置檔案的標記。

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

複製 *Views\\Home\\AllTags.cshtml* 檔案重新命名為
*Views\\Home\\AllTags.Mobile.cshtml*。 開啟新檔案，然後將
`< h 2 >` 元素從"Tags"為"Tags (M)":

    <h2>Tags (M)</h2>

瀏覽至標籤頁面使用桌面瀏覽器及行動瀏覽器
模擬器。 行動瀏覽器模擬器會顯示您所做的兩個變更
(從標題 *\_Layout.Mobile.cshtml* 的標題及
*AllTags.Mobile.cshtml*)。

![][alltagsmobile_layoutmobile]

相較之下，桌面顯示則沒有變更 (\_layout.cshtml *\_Layout.cshtml* 和 
*AllTags.cshtml*)。

![][alltagsmobile_layoutmobiledesktop]

## <a name="bkmk_browserviews"></a> 建立瀏覽器專用的檢視

除了行動與桌面專用的檢視，您可以
建立個別的瀏覽器的檢視。 例如，您可以建立
是專為 iPhone 或 Android 瀏覽器的檢視。 本節中，
您要建立 iPhone 瀏覽器及 iPhone 版的配置
*AllTags* 檢視。

開啟 *Global.asax* 檔案，並加入下列程式碼的底部
`Application_Start` 方法。

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

此程式碼會定義名為 「 iPhone 」，將會比對的新顯示模式
對每個連入要求。 如果傳入要求符合
您定義 (也就是如果使用者代理程式包含字串條件
"iPhone")，則 ASP.NET MVC 會尋找名稱包含
"iPhone" 後置詞的檢視。
>[AZURE.NOTE] 新增行動瀏覽器專用的顯示模式時，例如 iPhone 和 Android，請務必將第一個引數設定為 `0` (插入於清單的頂端) 以確定該瀏覽器的專用模式會優先於行動範本 (*。.Mobile.cshtml)。 若位於清單頂端的是行動範本，則會優先選取該行動範本，而不是您想要的顯示模式 (第一個相符的會成功，而行動範本符合所有行動瀏覽器)。 

在程式碼，以滑鼠右鍵按一下 `DefaultDisplayMode`, ，選擇 [ **解決**, ，並
然後選擇 [ `using System.Web.WebPages`。 這會新增對
`System.Web.WebPages` 命名空間，而這是定義
`DisplayModeProvider` 和 `DefaultDisplayMode` 型別定義。

![][resolvedefaultdisplaymode]

或者，您可以只透過手動方式將下列程式碼行新增至
`使用` 檔案區段。

    using System.Web.WebPages;

儲存變更。 複製
*Views\\Shared\\\_Layout.Mobile.cshtml* 檔案重新命名為
*Views\\Shared\\\_Layout.iPhone.cshtml*。 開啟新檔案
然後將標題從變更 `MVC5 Application (Mobile)` 至
`MVC5 Application (iPhone)`。

複製 *Views\\Home\\AllTags.Mobile.cshtml* 檔案重新命名為
*Views\\Home\\AllTags.iPhone.cshtml*。 在新的檔案變更
`< h 2 >` 為 「 Tags (iPhone) 」 的項目從 「 Tags (M)"。

執行應用程式。 執行行動瀏覽器模擬器，請確定它的使用者
代理程式設定為 「 iPhone 」，並瀏覽至 *AllTags* 檢視。 如果您是
使用 Internet Explorer 11 F12 開發人員工具] 中的模擬器
設定下列模擬:

-   瀏覽器設定檔 = **Windows Phone**
-   使用者代理程式字串 = **Custom**
-   自訂字串 = **Apple-iPhone5C1/1001.525**

下列螢幕擷取畫面顯示 *AllTags* 中呈現的檢視
(此為 iPhone 5c 使用者代理程式字串) 的自訂使用者代理字串的 Internet Explorer 11 F12 開發人員工具中的模擬器。

![][alltagsiphone_layoutiphone]

在行動瀏覽器中，選取 [演講者]**** 連結。 因為沒有
行動檢視 (*AllSpeakers.Mobile.cshtml*)，預設的演講者檢視
(*AllSpeakers.cshtml*) 呈現透過行動配置檢視
(*\_Layout.Mobile.cshtml*)。 如下所示，標題 * * MVC5 應用程式
() Mobile** 定義於 *\_Layout.Mobile.cshtml*。

![][allspeakers_layoutmobile]

您可以全域停用預設 (非行動) 檢視呈現
設定行動配置內 `RequireConsistentDisplayMode` 至
`true` 中 *Views\\\_ViewStart.cshtml* 檔案，像這樣:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

當 `RequireConsistentDisplayMode` 設為 `true`, ，行動配置
(*\_Layout.Mobile.cshtml*) 只適用於行動檢視 (也就是當
檢視檔案的格式是 *** ViewName**。.Mobile.cshtml *)。 您可能會想
若要設定 `RequireConsistentDisplayMode` 來 `true` 若行動配置
不適用於您的非行動檢視。 以下螢幕擷取畫面顯示
如何 *喇叭* 頁面呈現時 `RequireConsistentDisplayMode` 是
設定為 `true` (不含字串 「 (Mobile) 」 在上方導覽列中)。

![][allspeakers_layoutmobileoverridden]

您可以在檢視檔案中將
`RequireConsistentDisplayMode` 至 `false` 檢視檔案中。 Auch die Eigenschaften
中的以下標記 *Views\\Home\\AllSpeakers.cshtml* 檔案集
`RequireConsistentDisplayMode` 至 `false`:

    @model IEnumerable<string>
    
    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

在本節中，我們已了解如何建立行動配置和檢視表和
如何建立配置和檢視有關特定裝置，例如 iPhone。
不過，Bootstrap CSS 架構的主要優點是
回應靈敏的配置，表示單一樣式表可以套用
跨桌面、 手機和平板電腦瀏覽器建立一致的外觀和
感覺。 下一節中，您將看到如何利用 Bootstrap 建立
適合行動的檢視。

## <a name="bkmk_Improvespeakerslist"></a> 改善演講者清單

如您剛才所見， *喇叭* 檢視可以讀取，但連結卻非常
小而且很難在行動裝置上點選。 本節中，
您要使 *AllSpeakers* 檢視適合行動用途，以顯示
大型、 容易點選連結，並包含可快速找到的搜尋方塊
喇叭。

您可以使用 Bootstrap [連結清單群組 []][] 樣式
改善 *喇叭* 檢視。 在 *Views\\Home\\AllSpeakers.cshtml*,，
下列程式碼取代 Razor 檔案的內容。

     @model IEnumerable<string>
    
    @{
        ViewBag.Title = "All Speakers";
    }
    
    <h2>Speakers</h2>
    
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

`類別 = 「 群組清單 」` 屬性中 `< d i v >` 標籤
Bootstrap 清單樣式，而 `類別 = 「 輸入群組的項目 」` 屬性
會將 Bootstrap 清單項目樣式套用到每個連結。

重新整理行動瀏覽器。 更新的檢視如下所示：

![][allspeakersfixed]

啟動安裝程式 [連結清單群組 []][] 樣式讓每個的整個方塊
連結都可以點選，這是更好的使用者體驗。 切換至
桌面檢視，並觀察一致的外觀及操作。

![][allspeakersfixeddesktop]

雖然已經改善行動瀏覽器檢視，很難
瀏覽一長串的喇叭。 啟動安裝程式並不提供
搜尋篩選功能--現成的但您可以將它與
幾行程式碼。 您要先將搜尋方塊加入檢視然後
與連結，以取得篩選功能的 JavaScript 程式碼。 在
*Views\\Home\\AllSpeakers.cshtml*, ，新增 \<form\> 標籤後面 \<h2\> 標記，如下所示:

    @model IEnumerable<string>
    
    @{
        ViewBag.Title = "All Speakers";
    }
    
    <h2>Speakers</h2>
    
    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

請注意， `< 表單 >` 和 `< 輸入 >` 標籤都已啟動安裝程式
套用樣式。 `< s p a n >` 元素會將 Bootstrap
[glyphicon[]][] to the
。

在 *Scripts* 資料夾中，加入名為 *filter.js* 的 JavaScript 檔案。 開啟
檔案並貼上下列程式碼到檔案中:

    $(function () {
    
        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });
    
        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");
    
            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

您也需要在註冊的套件中包含 filter.js。 開啟
*App\_Start\\BundleConfig.cs* 並變更第一組套件。 變更
第一個 `套組。新增` 陳述式 (如 **jquery** bundle) 包含
*Scripts\\filter.js*, 、，如下所示:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

**Jquery** 配套已呈現預設 *\_Layout*
檢視呈現。 稍後，您可以利用相同的 JavaScript 程式碼，將
篩選功能套用到其他清單檢視。

請重新整理行動瀏覽器，並移至 *AllSpeakers* 檢視。 在
搜尋方塊中，輸入"sc"。 應該現在會篩選出演講者清單
根據您的搜尋字串。

![][allspeakersfixedsearchbysc]

## <a name="bkmk_improvetags"></a> 改善標籤清單

像 *喇叭* ] 檢視中， *標記* 檢視可以讀取，但連結
不但小，而且很難在行動裝置上點選。 您可以修正 *標記* 檢視的一樣修正 *喇叭* 檢視時，如果您使用，但先前描述以下列程式碼變更 `Html.ActionLink` 方法語法 *Views\\Home\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

重新整理後的桌面瀏覽器外觀如下：

![][alltagsfixeddesktop]

而重新整理後的行動瀏覽器外觀如下：

![][alltagsfixed]
>[AZURE.NOTE] 若您發現行動瀏覽器中仍顯示原始的清單格式，想知道設好的 Bootstrap 樣式有什麼問題，這其實是您先前建立行動專用檢視的動作所產生的結果。 然而，既然您使用 Bootstrap CSS 架構建立回應靈敏的 Web 設計，請移除這些行動專用的檢視和行動專用的配置檢視。 完成移除後，再重新整理行動瀏覽器，就會顯示 Bootstrap 樣式。

## <a name="bkmk_improvedates"></a> 改善日期清單

您可以改善 *日期* 檢視一樣改善 *喇叭* 和
*標記* 檢視如果使用，但先前描述以下列程式碼變更 `Html.ActionLink` 方法語法 *Views\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

重新整理後，您會獲得如下的行動瀏覽器檢視：

![][alldatesfixed]

您可以進一步改善 *日期* 檢視按日期組織日期時間
值。 這可以透過 Bootstrap
[panels[]][] styling. 將
內容 *Views\\Home\\AllDates.cshtml* 檔案中使用
下列程式碼：

    @model IEnumerable<DateTime>
    
    @{
        ViewBag.Title = "All Dates";
    }
    
    <h2>Dates</h2>
    
    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

此程式碼會建立個別 `< v class ="面板面板-主要 」 >` 標記
每個不同的日期清單中，並使用 [連結清單群組 []][] 的
。 以下是執行此程式碼時的行動瀏覽器外觀
：

![][alldatesfixed2]

切換成桌面瀏覽器。 然後會再次發現有一致的外觀。

![][alldatesfixed2desktop]

## <a name="bkmk_improvesessionstable"></a> 改善 SessionsTable 檢視

在本節中，您要使 *SessionsTable* 檢視更多
適合行動裝置。 這項變更比先前的變更更加廣泛。

在行動瀏覽器中，點選 **標記** ] 按鈕，然後輸入 `asp` 中
。

![][alltagsfixedsearchbyasp]

點選 **ASP.NET** 連結。

![][sessionstabletagasp.net]

如您所見，顯示畫面會格式化為表格，目前這是
設計成在桌面瀏覽器中檢視。 不過，它會稍微
難以閱讀行動瀏覽器上。 若要修正此問題，請開啟
*Views\\Home\\SessionsTable.cshtml* ，然後取代的內容
下列程式碼檔案:

    @model IEnumerable<Mvc5Mobile.Models.Session>
    
    <h2>@ViewBag.Title</h2>
    
    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

此程式碼會執行 3 個動作：

-   使用 Bootstrap [自訂連結清單群組 []][]
    以垂直方式，格式化工作階段資訊，讓所有這些
    (使用類別，例如清單群組的項目文字) 的行動瀏覽器上閱讀的資訊
-   適用於 [方格系統 []][] 至
    版面配置，使工作階段項目水平流動，在桌面上
    瀏覽器以垂直方式在行動瀏覽器中 (使用欄-md-4 類別)
-   使用 [回應靈敏的公用程式 []][] 至
    時，隱藏工作階段標籤 (使用 hidden-xs 類別) 的行動瀏覽器中檢視

您也可以點選標題連結，以進入個別工作階段。 映像
下面會反映的程式碼變更。

![][fixedsessionsbytag]

排列您自動套用的 Bootstrap 方格系統
在行動瀏覽器中垂直的工作階段。 此外，請注意，標記
不會顯示。 切換成桌面瀏覽器。

![][sessionstablefixedtagasp.netdesktop]

在桌面瀏覽器，您會發現有顯示標籤。 此外，您也會看見您所套用的 Bootstrap 方格系統
將工作階段項目排列於兩欄中。 如果您放大
瀏覽器中，您會看到發現排列變更為三個資料行。

## <a name="bkmk_improvesessionbycode"></a> 改善 SessionByCode 檢視

最後，您要修正 *SessionByCode* 檢視，使其適合行動用途。

在行動瀏覽器中，點選 **標記** ] 按鈕，然後輸入 `asp` 中
。

![][alltagsfixedsearchbyasp]

點選 **ASP.NET** 連結。 隨即會顯示 ASP.NET 標籤的工作階段。

![][fixedsessionsbytag]

選擇 [* * 建置單一頁面應用程式使用 ASP.NET 和
AngularJS * *] 連結。

![][sessionbycode3-644]

預設的桌面檢視雖然不錯，但您可以使用一些 Bootstrap GUI 元件輕鬆地改善它的外觀。

開啟 *Views\\Home\\SessionByCode.cshtml* 和取代內容
下列標記：

    @model Mvc5Mobile.Models.Session
    
    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>
    
    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>
    
    <p>@Model.Abstract</p>
    
    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

新的標記會使用 Bootstrap 的面板樣式改善行動檢視。

重新整理行動瀏覽器。 下圖反映了您剛才所做的
程式碼變更：

![][sessionbycodefixed3-644]

## 總結與複習

本教學課程示範了如何使用 ASP.NET MVC 5 以開發
適合行動的 Web 應用程式。 其中包含：

-   將 ASP.NET MVC 5 應用程式部署至 App Service Web 應用程式
-   使用 Bootstrap 建立 MVC 5 應用程式中回應靈敏的
    Web 配置。
-   以全域和個別檢視方式來覆寫配置、檢視和
    部分檢視
-   控制配置和部分覆寫的強制執行，方法是使用
    `RequireConsistentDisplayMode` 屬性
-   建立以特定瀏覽器做為目標的檢視，例如 iPhone
    瀏覽器
-   在 Razor 程式碼中套用 Boostrap 樣式

## 另請參閱

-   [回應靈敏的 web 設計的 9 個基本原則](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [啟動 ][bootstrapsite]
-   [官方 Bootstrap 部落格]][]
-   [Twitter Bootstrap 教學課程，從教學課程共和國]][]
-   [Bootstrap 練習場]][]
-   [W3C 推薦的行動 Web 應用程式的最佳作法]][]
-   [W3C Candidate Recommendation 的媒體查詢]][]

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)





[deploy the starter project to an azure web app]: #bkmk_DeployStarterProject 
[bootstrap css framework]: #bkmk_bootstrap 
[override the views, layouts, and partial views]: #bkmk_overrideviews 
[create browser-specific views]: #bkmk_browserviews 
[improve the speakers list]: #bkmk_Improvespeakerslist 
[improve the tags list]: #bkmk_improvetags 
[improve the dates list]: #bkmk_improvedates 
[improve the sessionstable view]: #bkmk_improvesessionstable 
[improve the sessionbycode view]: #bkmk_improvesessionbycode 
[visual studio express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web 
[visual studio 2015]: https://www.visualstudio.com/downloads/download-visual-studio-vs 
[azuresdkvs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409 
[fiddler]: http://www.fiddler2.com/fiddler2/ 
[emulatorie11]: http://msdn.microsoft.com/library/ie/dn255001.aspx 
[emulatorchrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation 
[emulatoropera]: http://www.opera.com/developer/tools/mobile/ 
[starterproject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409 
[completedproject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409 
[bootstrapsite]: http://getbootstrap.com/ 
[webpiazuresdk23netvs13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png 
[linked list group]: http://getbootstrap.com/components/#list-group-linked 
[glyphicon]: http://getbootstrap.com/components/#glyphicons 
[panels]: http://getbootstrap.com/components/#panels 
[custom linked list group]: http://getbootstrap.com/components/#list-group-custom-content 
[grid system]: http://getbootstrap.com/css/#grid 
[responsive utilities]: http://getbootstrap.com/css/#responsive-utilities 
[official bootstrap blog]: http://blog.getbootstrap.com/ 
[twitter bootstrap tutorial from tutorial republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/ 
[the bootstrap playground]: http://www.bootply.com/ 
[w3c recommendation mobile web application best practices]: http://www.w3.org/TR/mwabp/ 
[w3c candidate recommendation for media queries]: http://www.w3.org/TR/css3-mediaqueries/ 
[deployclickpublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png 
[deployclickwebsites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png 
[deploysignin]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png 
[deployusername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png 
[deploypassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png 
[deploynewwebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png 
[deploysitesettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png 
[deploypublishsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png 
[mobilehomepage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png 
[fixedsessionsbytag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png 
[alltags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png 
[sessionsbytagasp.net]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png 
[sessionsbytagasp.netnobootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png 
[alltagsmobile_layoutmobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png 
[alltagsmobile_layoutmobiledesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png 
[resolvedefaultdisplaymode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png 
[alltagsiphone_layoutiphone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png 
[allspeakers_layoutmobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png 
[allspeakers_layoutmobileoverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png 
[allspeakersfixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png 
[allspeakersfixeddesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png 
[allspeakersfixedsearchbysc]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png 
[alltagsfixeddesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[alltagsfixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png 
[alldatesfixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png 
[alldatesfixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png 
[alldatesfixed2desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png 
[alltagsfixedsearchbyasp]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png 
[sessionstabletagasp.net]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png 
[sessionstablefixedtagasp.netdesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png 
[sessionbycode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png 
[sessionbycodefixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png 

