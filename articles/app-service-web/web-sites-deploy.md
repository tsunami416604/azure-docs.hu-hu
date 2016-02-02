<properties
    pageTitle="Azure App Service 部署文件"
    description="找到說明如何將您的 app 部署至 Azure App Service 的文件。"
    services="app-service"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/06/2015"
    ms.author="tdykstra"/>


# Azure App Service 部署文件

## 概觀

本文列出可用的方法部署您自己的內容 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), ，包括文件和部落格，包含使用說明資訊的連結。 若有其他文章發佈，將會加入這份清單。

若要部署 web 應用程式，最好是設定 [連續傳遞工作流程](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) 整合與您 [原始檔控制系統](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)。 自動化不但可以讓開發程序更有效率，也可以使您的備份和還原程序更容易管理且可靠。

##### 從雲端代管的原始檔控制系統部署

* [使用 Visual Studio Team Services 的連續傳遞](#vsts)
* [使用 Git 儲存機制網站](#git)
* [使用 Mercurial 的儲存機制網站](#mercurial)
* [從 Dropbox 部署作業自動化](#dropbox)

##### 從內部部署原始檔控制系統部署

* [連續傳遞與 Team Foundation Server (TFS)](#tfs)
* [內部部署 Git 或 Mercurial 儲存機制](#onpremises)

##### 使用命令列工具自動化部署

* [使用 MSBuild 來自動化部署](#msbuild)
* [使用 FTP 工具和指令碼檔案複製](#ftp)
* [使用 Windows PowerShell 來自動化部署](#powershell)
* [使用.NET 管理 API 來自動化部署](#api)
* [將部署從 Azure 命令列介面 (Azure CLI)](#cli)
* [從 Web Deploy 命令列部署](#webdeploy)

##### 從整合式開發環境 (IDE) 部署

* [直接從 Visual Studio 部署](#vs)
* [直接從 WebMatrix 部署](#webmatrix)

另一個部署選項是使用雲端服務，例如 [Octopus 部署](http://en.wikipedia.org/wiki/Octopus_Deploy)。 如需詳細資訊，請參閱 [部署 ASP.NET 應用程式至 Azure 網站](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)。

## <a name="vsts"></a>使用 Visual Studio Team Services 的連續傳遞

[Visual Studio Team Services](http://www.visualstudio.com/) (先前稱為 Team Foundation Service) 是 Microsoft 的雲端解決方案的原始檔控制和團隊共同作業。 此服務可免費供 5 個開發人員以內的團隊使用。 您可以執行連續傳遞至應用程式服務中的 web 應用程式，而您的儲存機制可以使用 [Git 或 TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs)。

如需詳細資訊，請參閱下列資源：

* [連續傳遞至 Azure，使用 Visual Studio Team Services 和 TFVC](../cloud-services-continuous-delivery-use-vso.md)。 示範如何使用 TFVC，從 Visual Studio Team Services 設定連續傳遞至 Web 應用程式的逐步教學課程。 TFVC 是集中式原始檔控制選項，相對於作為分散式原始檔控制選項的 Git。
* [連續傳遞至 Azure 中使用 Visual Studio Team Services 和 Git](../cloud-services-continuous-delivery-use-vso-git.md)。 與上一個教學課程類似，但使用 Git 而不是 TFVC。

## <a name="git"></a>使用 Git 儲存機制網站

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) 是受歡迎的分散式原始檔控制系統。 Azure 具有內建的功能，輕鬆地從儲存 Git 儲存機制，包括常用 web 型儲存機制網站進行自動部署至 web 應用程式 [GitHub](http://www.github.com), ，[CodePlex](http://www.codeplex.com/), ，和 [BitBucket](https://bitbucket.org/)。 使用 Git 進行部署的好處之一，是在必要時較容易回復為原先的部署。

如需詳細資訊，請參閱下列資源：

* [從原始檔控制發行至 Web 應用程式使用 Git](web-sites-publish-source-control.md)。 說明如何使用 Git，從本機電腦直接發行至 Web Apps (在 Azure 中，這個發行方法稱為「本機 Git」)。 此外也說明如何為 Git 儲存機制啟用從 GitHub、CodePlex 或 BitBucket 的連續部署。
* [部署至 Web 應用程式使用 Kudu 透過 GitHub](http://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/)。 由 Scott Hanselman 和 David Ebbo 提供的影片，示範如何從 GitHub 直接將 Web 應用程式部署至 Web Apps。
* [部署至 Web 應用程式的 Azure 按鈕](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/)。 觸發從 Git 儲存機制部署之方法的相關部落格。
* [Git、 Mercurial 和 Dropbox 的 azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

## <a name="mercurial"></a>使用 Mercurial 的儲存機制網站

如果您使用 [Mercurial](http://mercurial.selenic.com/) 為原始檔控制系統，並將儲存機制儲存在 [CodePlex](http://www.codeplex.com/) 或 [BitBucket](https://bitbucket.org/), ，您可以在 Azure App Service 中使用內建功能自動部署您的內容。

如需如何使用 Mercurial 進行部署的相關資訊，請參閱下列資源：

* [從原始檔控制發行至 Web 應用程式使用 Git](web-sites-publish-source-control.md)。 此教學課程說明的是發佈 Git 儲存機制的方式，但在 CodePlex 或 BitBucket 中受到代管的 Mercurial 儲存機制其實也採用類似的程序。
* [Git、 Mercurial 和 Dropbox 的 azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

## <a name="dropbox"></a>從 Dropbox 部署作業自動化

[Dropbox](https://www.dropbox.com/) 不是原始檔控制系統，但如果將原始程式碼儲存在 Dropbox 您可以從 Dropbox 帳戶進行自動部署。

* [從 Dropbox 部署至 Web 應用程式](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)。 如何使用 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715) 設定 Dropbox 部署。
* [Dropbox 部署至 Web Apps](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites)。 這段影片會逐步解說將 DropBox 資料夾連接到 Web 應用程式的程序，並示範您只需使用拖放部署，即可快速啟動及執行 Web 應用程式並加以維護。
* [Git、 Mercurial 和 Dropbox 的 azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

## <a name="vs"></a>直接從 Visual Studio 部署

如需如何從 Visual Studio 部署至 Web Apps 的相關資訊，請參閱下列資源：

* [開始使用 Azure 和 ASP.NET](web-sites-dotnet-get-started.md)。 說明如何使用 Visual Studio 和 Web Deploy 建立及部署簡易的 ASP.NET MVC Web 專案。
* [如何使用 Visual Studio 部署 Azure Webjob](websites-dotnet-deploy-webjobs.md)。 如何設定主控台應用程式專案，使其部署為 WebJob。
* [使用成員資格、 OAuth 和 SQL Database 的安全 ASP.NET MVC 5 應用程式部署至 Web Apps](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。 說明如何使用 Visual Studio、Web Deploy 和 Entity Framework Code First Migrations，建立及部署具有 SQL Database 的 ASP.NET MVC Web 專案。
* [的 Visual Studio 和 ASP.NET web 部署概觀](http://msdn.microsoft.com/library/dd394698.aspx)。 這是使用 Visual Studio 進行 Web 部署的基本簡介。 這項資源已過時，但仍包含相關資訊，包括部署資料庫和 Web 應用程式的選項概觀，以及您可能必須執行或手動設定 Visual Studio 為您執行的額外部署工作。 這是與一般部署有關的主題，而不是只討論對 Web Apps 的部署。
* [使用 Visual Studio 的 ASP.NET Web 部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。 這是分成 12 個單元的教學課程系列，其中討論的部署工作比此處所列的其他資源更為詳盡。 本教學課程撰寫後已新增某些 Azure 部署功能，但稍後的附註會說明遺漏的功能。
* [將 ASP.NET 網站部署至 Azure，Visual Studio 2012 中從 Git 儲存機制直接](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。 說明如何使用 Git 外掛程式將程式碼認可至 Git，以及將 Azure 連接到 Git 儲存機制，以在 Visual Studio 中部署 ASP.NET Web 專案。 自 Visual Studio 2013 起，Git 支援已是內建的功能，不需安裝外掛程式。

## <a name="webmatrix"></a>直接從 WebMatrix 部署

如需如何從 WebMatrix 部署至 Web Apps 的相關資訊，請參閱下列資源：

* [建置和部署 Node.js 網站至 Azure 使用 WebMatrix](web-sites-nodejs-use-webmatrix.md)。
* [建立並部署 Php-mysql web 應用程式使用 WebMatrix](web-sites-php-mysql-use-webmatrix.md)。
* [WebMatrix 3: 整合 Git 並部署至 Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD)。 說明如何使用 WebMatrix 從 Git 原始檔控制儲存機制進行部署。

如需詳細資訊，請參閱下列資源：

* [建立 Php-mysql web 應用程式，並使用 FTP 部署](web-sites-php-mysql-deploy-use-ftp.md)。

## <a name="tfs"></a>連續傳遞與 Team Foundation Server (TFS)

Team Foundation Server 是 Microsoft 針對原始檔控制和團隊共同作業而推出的內部部署解決方案。 您可以設定 TFS，以便對 Web 應用程式執行連續傳遞。

如需詳細資訊，請參閱下列資源：

* [服務在 Azure 中的定域機組的連續傳遞](../cloud-services-dotnet-continuous-delivery.md)。 本文是針對 Azure 雲端服務而撰寫，但其部分內容也與 Web Apps 有關。

## <a name="gitmercurial"></a>內部部署 Git 或 Mercurial 儲存機制

在 Azure 中，您可以輸入任何使用 Git 或 Mercurial 之儲存機制的 URL，以從該位置進行部署。 您也可以直接從本機 Git 儲存機制推送至 Web 應用程式。

如需詳細資訊，請參閱下列資源：

* [從原始檔控制發行至 Web 應用程式使用 Git](web-sites-publish-source-control.md)。 說明如何使用 Git，從本機電腦直接發行至 Web 應用程式 (在 Azure 中，這個發行方法稱為「本機 Git」)。 此外也說明如何為 Git 儲存機制啟用從 GitHub、CodePlex 或 BitBucket 的連續部署。
* [從任何 git/hg 儲存機制發行至 Web Apps](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。 說明 Web Apps 中「外部儲存機制」功能的部落格。
* [Git、 Mercurial 和 Dropbox 的 azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。
* [從一個 Git 儲存機制的兩個部署網站至 Azure](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx)。 取自 Scott Hanselman 的部落格文章。

## <a name="msbuild"></a>使用 MSBuild 來自動化部署

如果您使用 [Visual Studio IDE](#vs) 進行開發，您可以使用 [MSBuild](http://msbuildbook.com/) 將任何您可以在 IDE 中自動化。 您可以設定 MSBuild，以使用 [Web Deploy](#webdeploy) 或 [FTP/FTPS](#ftp) 來複製檔案。 Web Deploy 也可自動化其他多種部署相關工作，例如部署資料庫。

如需使用 MSBuild 進行命令列部署的詳細資訊，請參閱下列資源：

* [使用 Visual Studio 的 ASP.NET Web 部署: 命令列部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。 這是系列中的第十個教學課程，討論如何使用 Visual Studio 部署至 Azure。 此課程說明在 Visual Studio 中設定發佈設定檔後，如何使用命令列進行部署。
* [深入了解 Microsoft Build Engine: 使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/)。 這是實體書籍，其中有幾章會說明如何使用 MSBuild 進行部署。

## <a name="ftp"></a>使用 FTP 工具和指令碼檔案複製

您可以將內容部署至您的應用程式使用 [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 來複製檔案。 很容易建立 web 應用程式的 FTP 認證，您可以使用 FTP，包括瀏覽器，例如 Internet Explorer 以及完整的免費公用程式，例如應用程式或指令碼中使用它們 [FileZilla](https://filezilla-project.org/)。 Web Apps 也支援更安全的 FTPS 通訊協定。

雖然使用 FTP 公用程式可讓您輕鬆地將 Web 應用程式的檔案複製到 Azure，但這些公用程式並不會自動處理或協調相關的部署工作，例如，部署資料庫或變更連接字串。 此外，有許多 FTP 工具並不會比較來源與目的地檔案，以略過沒有差異的檔案而不加以複製。 就大型應用程式而言，若一律複製所有檔案，就會導致部署時間拉長，即便是微幅更新也是如此，因為一律要複製所有檔案。

如需詳細資訊，請參閱下列資源：

* [使用 FTP 批次指令碼](http://support.microsoft.com/kb/96269)。

## <a name="powershell"></a>使用 Windows PowerShell 來自動化部署

您可以執行 MSBuild 或 FTP 部署功能，從 [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx)。 如果您這麼做，則您也可以使用 Windows PowerShell Cmdlet 的集合，使 Azure REST 管理 API 更容易呼叫。

如需詳細資訊，請參閱下列資源：

* [連結至 GitHub 儲存機制的 web 應用程式部署](app-service-web-arm-from-github-provision.md)
* [佈建 web 應用程式與 SQL 資料庫](app-service-web-arm-with-sql-database-provision.md)
* [佈建和部署如預期般在 Azure 中的微服務](app-service-deploy-complex-application-predictably.md)
* [建置真實世界的雲端應用程式與 Azure-自動化各個項目](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。 這是電子書的其中一章，說明電子書中的範例應用程式如何使用 Windows PowerShell 指令碼建立 Azure 測試環境，並加以部署。 請參閱 [資源](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) 區段，如需其他 Azure PowerShell 文件的連結。
* [使用 Windows PowerShell 指令碼來發行至開發和測試環境](http://msdn.microsoft.com/library/dn642480.aspx)。 如何使用 Visual Studio 產生的 Windows PowerShell 部署指令碼。

## <a name="api"></a>使用.NET 管理 API 來自動化部署

您可以撰寫 C# 程式碼，以執行部署所需的 MSBuild 或 FTP 功能。 如果您這麼做，您將可存取 Azure 管理 REST API，以執行網站管理功能。

如需詳細資訊，請參閱下列資源：

* [使用 Azure 管理庫和.NET 將一切自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。 這是 .NET 管理 API 的簡介，並提供相關文件的連結。

## <a name="cli"></a>將部署從 Azure 命令列介面 (Azure CLI)

您可以透過 FTP，使用 Windows、Mac 或 Linux 機器中的命令列進行部署。 如果您這麼做，也可以使用 Azure CLI 存取 Azure REST 管理 API。

如需詳細資訊，請參閱下列資源：

* [Azure 命令列工具](/downloads/#cmd-line-tools)。 Azure.com 中提供命令列工具資訊的入口網站頁面。

## <a name="webdeploy"></a>從 Web Deploy 命令列部署

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) 是 Microsoft 軟體以部署至 IIS，不僅提供智慧型檔案同步功能，但也可以執行或協調其他許多與部署相關工作無法在您使用 FTP 時自動化。 例如，Web Deploy 可對您的 Web 應用程式部署新的資料庫或資料庫更新。 Web Deploy 也可儘量縮短更新現有網站所需的時間，因為它具有智慧功能，可僅複製有所變更的檔案。 Microsoft WebMatrix、Visual Studio、Visual Studio Team Services 和 Team Foundation Server 皆有內建的 Web Deploy 支援，但您也可直接從命令列使用 Web Deploy，將部署自動化。 Web Deploy 命令的功能十分強大，但學習起來較為困難。

如需詳細資訊，請參閱下列資源：

* [簡單 Web 應用程式: 部署](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)。 David Ebbo 所撰寫使 Web Deploy 更容易使用之工具的部落格。
* [Web 部署工具](http://technet.microsoft.com/library/dd568996)。 這是 Microsoft TechNet 網站上的正式文件。 雖已過時，但仍適合入門使用。
* [Using Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). 這是 Microsoft IIS.NET 網站上的正式文件。 同樣也已過時，但仍適合入門使用。
* [StackOverflow](http://www.stackoverflow.com). 如果您需要如何從命令列使用 Web Deploy 的最新相關資訊，這將是您的最佳選擇。
* [使用 Visual Studio 的 ASP.NET Web 部署: 命令列部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。 MSBuild 是 Visual Studio 所使用的建置引擎，您也可以從命令列加以使用，來將 Web 應用程式部署至 Web Apps。 這是系列中的教學課程之一，主要與 Visual Studio 部署有關。

## <a name="nextsteps"></a>後續步驟

在某些情況中，您可能想要能夠輕鬆地在預備版本和生產版本的 Web 應用程式之間來回切換。 如需詳細資訊，請參閱 [上 Web 應用程式的預備部署](web-sites-staged-publishing.md)。

具有備份及還原計劃是部署工作流程中相當重要的環節。 Web 應用程式的相關資訊的備份和還原功能，請參閱 [Web Apps 備份](web-sites-backup.md)。

如需如何使用 Azure 的角色型存取控制來管理 Web 應用程式部署的存取資訊，請參閱 [RBAC 和 Web 應用程式發行](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing)。

如需其他部署主題的詳細資訊，請參閱 < 部署 > 一節中的 [Web Apps 文件](/documentation/services/web-sites/)。

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)






