<properties 
    pageTitle="安裝彈性資料庫工作 | Microsoft Azure" 
    description="逐步解說如何安裝彈性工作功能。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh"/>

# 安裝彈性資料庫工作概觀
[**彈性資料庫工作**](sql-database-elastic-jobs-overview.md) 可透過 PowerShell 來安裝或透過 Azure 傳統 Portal.You 可以存取建立和安裝 PowerShell 套件時才使用 PowerShell API 來管理工作。 此外，PowerShell API 目前比入口網站提供更多的功能。 

如果您已安裝 **彈性資料庫工作** 透過入口網站，從現有 **彈性資料庫集區**, ，最新的 Powershell 預覽包含要升級現有的安裝指令碼。 強烈建議您安裝升級至最新 **彈性資料庫工作** 才能利用透過 PowerShell Api 公開的新功能的元件。

## 必要條件
* Azure 訂用帳戶。 如需免費試用版，請參閱 [免費試用版](http://azure.microsoft.com/pricing/free-trial/)。
* Azure PowerShell 版本 >= 0.8.16。 安裝最新版本 (0.9.5) 透過 [Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376)。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。
* [NuGet 命令列公用程式](https://nuget.org/nuget.exe) 用來安裝彈性資料庫工作的封裝。 如需詳細資訊，請參閱 http://docs.nuget.org/docs/start-here/installing-nuget。

## 下載並匯入彈性資料庫工作 PowerShell 封裝
1. 啟動 Microsoft Azure PowerShell 命令視窗，並瀏覽至您下載 NuGet 命令列公用程式 (nuget.exe) 的目錄。

2. 下載並匯入 **彈性資料庫工作** 封裝到目前的目錄，使用下列命令:

        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease

     **彈性資料庫工作** 檔案會放置在名為的資料夾中的本機目錄 **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** 其中 *x.x.xxxx.x* 反映版本號碼。 PowerShell cmdlet (包括必要的用戶端.dll 檔) 位於 **tools\ElasticDatabaseJobs** 子目錄和 PowerShell 指令碼來安裝、 升級和解除安裝也位於 **工具** 子目錄。

3. 例如，輸入  cd tools，瀏覽至 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x 資料夾下的 tools 子目錄：

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4.  執行 .\InstallElasticDatabaseJobsCmdlets.ps1 script to copy the ElasticDatabaseJobs directory into $home\Documents\WindowsPowerShell\Modules。 這也會自動匯入模組，以供使用，例如：

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1 
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## 使用 PowerShell 安裝彈性資料庫工作元件
1.  啟動 Microsoft Azure PowerShell 命令視窗，並且瀏覽至 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x 資料夾底下的 \tools 子目錄：輸入 cd \tools

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2.  執行.\InstallElasticDatabaseJobs.ps1 PowerShell 指令碼，並提供其所要求之變數的值。 此指令碼會建立中描述的元件 [彈性資料庫工作元件和定價](sql-database-elastic-jobs-overview/#components-and-pricing) 以及設定 Azure 雲端服務，以適當地使用相關的元件。

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1 
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

當您執行此命令] 視窗隨即開啟向您詢問 **使用者名稱** 和 **密碼**。 這不是您的 Azure 認證，請輸入要用於新伺服器之系統管理員認證的使用者名稱和密碼。 

在此範例引動過程上提供的參數可以進行修改，以適用於您所需的設定。 下列提供每個參數行為的詳細資訊：

<table style="width:100%">
  <tr>
    <th>參數</th>
    <th>說明</th>
  </tr>

<tr>
    <td>resourceGroupName</td>
    <td>提供建立的 Azure 資源群組名稱，以包含新建的 Azure 元件。 此參數預設為 “__ElasticDatabaseJob”。 不建議變更此值。</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Provides the Azure location to be used for the newly created Azure components. This parameter defaults to the Central US location.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>提供要安裝的服務背景工作數目。 此參數預設為 1。 可以使用更多的背景工作來相應放大服務，並提供高可用性。 建議針對需要服務的高可用性的部署使用 "2"。</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>提供雲端服務內的使用量 VM 大小。 此參數預設為 A0。 接受 A0/A1/A2/A3 的參數值，這會導致背景工作角色分別使用 ExtraSmall/Small/Medium/Large 大小。 加上更多有關背景工作角色大小，請參閱 [Elastic Database jobs components and pricing](sql-database-elastic-jobs-overview/#components-and-pricing)。</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Standard edition 中提供的服務等級目標。 此參數預設為 S0。 接受 S0/S1/S2/S3 的參數值，這會導致 Azure SQL Database 使用各自的 SLO。 如需有關 SQL 資料庫 Slo 的詳細資訊，請參閱 [Elastic Database jobs components and pricing](sql-database-elastic-jobs-overview/#components-and-pricing)。</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>提供新建的 Azure SQL Database 伺服器的系統管理員使用者名稱。 未指定時，將開啟 PowerShell 認證視窗，提示輸入認證。</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>提供新建的 Azure SQL Database 伺服器的系統管理員密碼。 未提供時，將開啟 PowerShell 認證視窗，提示輸入認證。</td>
</tr>
</table>

對於將對大量資料庫具有大量平行執行的工作設為目標的系統，建議指定如下參數：-ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## 使用 PowerShell 更新現有的彈性資料庫工作元件安裝

**彈性資料庫工作** 可以更新現有延展性和高可用性安裝中。 此程序允許未來的服務程式碼升級，而不需捨棄並重新建立控制資料庫。 此程序也可在相同版本內用來修改服務的 VM 大小或伺服器的背景工作計數。

若要更新安裝的 VM 大小，請執行下列指令碼，並將參數更新為您選擇的值。

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>參數</th>
  <th>說明</th>
</tr>

  <tr>
    <td>resourceGroupName</td>
    <td>識別一開始安裝彈性資料庫工作元件時所使用的 Azure 資源群組名稱。 此參數預設為 “__ElasticDatabaseJob”。 不建議變更此值，因為您應該不必指定此參數。</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>提供要安裝的服務背景工作數目。  此參數預設為 1。  可以使用更多的背景工作來相應放大服務，並提供高可用性。  建議針對需要服務的高可用性的部署使用 "2"。</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Provides the VM size for usage within the Cloud Service. This parameter defaults to A0. Parameters values of A0/A1/A2/A3 are accepted which cause the worker role to use an ExtraSmall/Small/Medium/Large size, respectively. Fo more information on worker role sizes, see [Elastic Database jobs components and pricing](sql-database-elastic-jobs-overview/#components-and-pricing).</td>
</tr>

</table>

## 使用入口網站安裝彈性資料庫工作元件

一旦 [建立彈性資料庫集區](sql-database-elastic-pool-portal.md), ，您可以安裝 **彈性資料庫工作** 元件，才能執行彈性資料庫集區中每個資料庫的系統管理工作。 不同於何時使用 **彈性資料庫工作** PowerShell Api 入口網站介面是目前限制為僅針對現有的集區執行。


**預估完成時間:** 10 分鐘。

1. 透過彈性資料庫集區的儀表板檢視從 [Azure 入口網站](https://ms.portal.azure.com/#) , ，按一下 [ **建立工作**。
2. 如果您第一次建立工作，您必須安裝 **彈性資料庫工作** 按一下 **預覽條款**。 
3. 然後按一下核取方塊接受條款。
4. 在 [安裝服務] 檢視中，按一下 [ **工作認證**。

    ![安裝服務][] 1

5. 輸入資料庫管理員的使用者名稱和密碼。 在安裝過程中，會建立新的 Azure SQL Database 伺服器。 在這個新的伺服器內，會建立稱為控制資料庫的新資料庫，並用來包含彈性資料庫工作的中繼資料。 這裡建立的使用者名稱和密碼用於登入控制資料庫。 個別的認證用於對集區內的資料庫執行指令碼。

    ![建立使用者名稱和密碼][] 2

6. 按一下 [確定] 按鈕。 元件會為您建立新的幾分鐘後 [資源群組](../resource-group-portal.md)。 新的資源群組已釘選到「開始面板」，如下所示。 建立後，會在群組中建立所有彈性資料庫工作 (雲端服務、SQL Database、服務匯流排和儲存體)。

    ![開始面板 」 中的資源群組][] 3

7. 如果您嘗試建立或管理工作，而在安裝彈性資料庫工作，提供 **認證** 您會看到下列訊息。 

    ![仍在進行部署][] 4

如果需要解除安裝，請刪除資源群組。 請參閱 [如何解除安裝彈性資料庫工作元件](sql-database-elastic-jobs-uninstall.md)。

## 後續步驟

請確定具有足夠的權限的認證在群組中，如需詳細資訊，請參閱每個資料庫上建立指令碼執行所 [如何將使用者新增至我的群組的資料庫中的所有資料庫](sql-database-elastic-jobs-add-logins-to-dbs.md)。 
請參閱 [建立和管理彈性資料庫工作](sql-database-elastic-jobs-create-and-manage.md) 以便開始使用。

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 
