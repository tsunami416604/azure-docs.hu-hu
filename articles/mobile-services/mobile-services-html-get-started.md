<properties
    pageTitle="開始使用適用於 HTML/JavaScript App 的 Azure 行動服務 | Microsoft Azure"
    description="遵循此教學課程，可開始使用 Azure 行動服務進行 HTML 開發。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html5"
    ms.devlang="javascript"
    ms.topic="get-started-article" 
    ms.date="11/30/2015"
    ms.author="glenga"/>


# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##概觀 

本教學課程說明如何使用 Azure行動服務在 HTML 應用程式中新增雲端型後端服務。 在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項清單*應用程式。 您可以於下方檢視本教學課程的影片版本。 

> [AZURE.VIDEO mobile-get-started-html]
 
以下是完成應用程式的螢幕擷取畫面：

![][0]

此教學課程是 HTML 應用程式其他所有行動服務教學課程的先修課程。 PhoneGap/Cordova 應用程式，請參閱 [PhoneGap/Cordova 版本](mobile-services-javascript-backend-phonegap-get-started.md) 本教學課程。

##必要條件

需要有下列項目，才能完成本教學課程：

+ 您的本機電腦必須執行下列其中一部網頁伺服器：

    +  **在 Windows 上**: IIS Express。 IIS Express 由安裝 [Microsoft Web Platform Installer]。
    +  **在 MacOS X 上**︰ 已安裝 Python。
    +  **在 Linux 上**: Python。 您必須安裝 [latest version of Python]。

    您可以使用任何網頁伺服器來裝載應用程式，但是這些網頁伺服器需受所下載的指令碼支援。  

+ 支援 HTML5 的網頁瀏覽器。
+ 一個 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank)。 


## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 HTML 應用程式

當您建立自己的行動服務之後，就可以依照 Azure 傳統入口網站中簡單的快速入門，來建立新的應用程式或修改現有的應用程式，以便連線到您的行動服務。

在本節中，您將建立連線至您行動服務的新 HTML 應用程式。

1.  在 [Azure classic portal], ，按一下 [ **行動電話服務**, ，然後按一下您剛才建立的行動服務。


2. 在 [快速入門] 索引標籤中，按一下 [ **Windows** 下 **選擇平台** 展開 **建立新的 HTML 應用程式**。

    ![][6]

    This displays the three easy steps to create and host an HTML app connected to your mobile service.

    ![][7]

3. 按一下 [ **Create TodoItems table** 來建立儲存應用程式資料的資料表。

4. 在 **下載並執行您的應用程式**, ，按一下 [ **下載**。

    這會下載範例的網站檔案 _待辦事項清單_ 連線到您的行動服務的應用程式。 請將壓縮檔儲存至本機電腦，並記下儲存位置。

5. 在 **設定** 索引標籤上，確認 `localhost` 已列在 **允許提出要求的主機名稱** 清單 **跨原始資源共用 (CORS)**。 如果沒有，請輸入 `localhost` 中 **主機名稱** 欄位，然後按一下 **儲存**。

    ![][9]

    > [AZURE.IMPORTANT] 如果您將快速入門應用程式部署至 localhost 以外的 web 伺服器時，您必須加入至 web 伺服器的主機名稱 **允許提出要求的主機名稱** 清單。 如需詳細資訊，請參閱 [跨原始資源共用](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx)。

## 裝載並執行 HTML 應用程式

本教學課程的最終階段是在本機電腦上裝載並執行新的應用程式。

1. 瀏覽至儲存壓縮的專案檔案的位置、 您的電腦上將檔案解壓縮並啟動一個從下列的命令檔 **伺服器** 子資料夾。

    + **啟動 windows** （Windows 電腦）
    + **launch-mac.command** （Mac OS X 電腦）
    + **launch-linux.sh** （Linux 電腦）

    > [AZURE.NOTE] 在 Windows 電腦上，輸入 `R` PowerShell 要求您確認您想要執行指令碼時。 因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。 發生此情況時，您必須要求瀏覽器繼續載入指令碼。

    如此會在本機電腦上啟動網頁伺服器來裝載新的應用程式。

2. 在網頁瀏覽器中開啟 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> 以啟動應用程式。

3. 在應用程式中，輸入有意義的文字，例如 _完成教學課程_, 中 **輸入新工作**, ，然後按一下 [ **新增**。

    ![][10]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。 要求中的資料會插入 TodoItem 資料表中。 行動服務會傳回資料表中儲存的項目，而該資料會顯示在應用程式的第二欄。

    > [AZURE.NOTE] 您可以檢閱存取行動服務來查詢和插入資料，page.js 檔案中找到的程式碼。

4. 回到 [Azure classic portal], ，按一下 [ **資料** 標籤，然後按一下 **TodoItems** 資料表。

    ![][11]

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![][12]

## <a name="next-steps"> </a>後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* **[Add authentication to your app]**  
  了解如何透過身分識別提供者來驗證您的應用程式使用者。

* **[Mobile Services HTML/JavaScript How-to Conceptual Reference]**  
  深入了解如何搭配使用行動服務與 HTML/JavaScript


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Add authentication to your app]: mobile-services-html-get-started-users.md

[Azure classic portal]: https://manage.windowsazure.com/
[Microsoft Web Platform Installer]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[latest version of Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx
 


