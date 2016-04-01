<properties
    pageTitle="將 Azure SQL Server 虛擬機器設定為 IPython Notebook 伺服器 | Microsoft Azure"
    description="使用 SQL Server 和 IPython Server 來設定資料科學虛擬機器。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev" 
    manager="paulettm"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2015"
    ms.author="mohabib;xibingao;bradsev" />

# 將 Azure SQL Server 虛擬機器設定為 IPython Notebook 伺服器供進階分析使用

本主題示範如何佈建及設定 SQL Server 虛擬機器，以用來做為雲端架構資料科學環境的一部分。 Windows 虛擬機器是使用支援工具 (例如，IPython Notebook、Azure 儲存體總管及 AzCopy)，以及其他對於資料科學專案非常實用的公用程式來設定。 例如，Azure 儲存體總管和 AzCopy 會提供便利的方法，將資料從本機電腦上傳至 Azure Blob 儲存體，或者從 Blob 儲存體將資料下載到本機電腦。

Azure 虛擬機器組件庫涵蓋數個包含 Microsoft SQL Server 的映像。 選取適合您資料需求的 SQL Server VM 映像。 建議的映像如下：

- SQL Server 2012 SP2 Enterprise，適用於小型到中型的資料大小
- SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads，適用於大型到非常大型的資料大小

 > [AZURE.NOTE] SQL Server 2012 SP2 Enterprise 映像 **不包含資料磁碟**。 您必須新增和 (或) 連接一或多個虛擬硬碟來儲存資料。 當您建立 Azure 虛擬機器時，它會有一個作業系統的磁碟對應至 C 磁碟機，還有一個暫存磁碟對應至 D 磁碟機。 請勿使用 D 磁碟機來儲存資料。 顧名思義，它只提供暫存儲存空間。 它並不提供備援或備份，因為它不在 Azure 儲存體內。


##<a name="Provision"></a>連線到 Azure 傳統入口網站並佈建 SQL Server 虛擬機器

1.  登入 [Azure 傳統入口網站](http://manage.windowsazure.com/) 使用您的帳戶。
    如果您沒有 Azure 帳戶，請造訪 [免費的 Azure
    試用](http://www.windowsazure.com/pricing/free-trial/)。

2.  在 Azure 傳統入口網站中，在左下角的 [web] 頁面
    按一下 [ **+ 新增**, ，按一下 [ **計算**, ，按一下 [ **虛擬機器**, ，並
    然後按一下 [ **從組件庫**。

3.  在 **建立虛擬機器** 頁面上，選取虛擬機器
    包含您的資料需求為基礎的 SQL Server 資料，然後按一下 [下一步] 箭頭
    右下方的頁面。 如需有關支援之 SQL Server 映像，在 Azure 上的最新
    請參閱 [開始使用 Azure 虛擬機器中 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294720) 中的主題 [Azure 虛擬機器中 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294719) 文件集。

    ![選取 SQL Server VM][1]

4.  在第一個 **虛擬機器組態** 頁面上，提供
    下列資訊 ︰

    -   提供 **虛擬機器名稱**。
    -   在 **新的使用者名稱** 方塊中，輸入唯一的使用者名稱的 vm
        本機系統管理員帳戶。
    -   在 **新密碼** 方塊中，輸入強式密碼。 如需
        詳細資訊，請參閱 [強式密碼](http://msdn.microsoft.com/library/ms161962.aspx)。
    -   在 **確認密碼** 方塊中，重新輸入密碼。
    -   選取適當 **大小** 從下拉式清單。

     > [AZURE.NOTE] 在佈建期間指定虛擬機器的大小 ︰ A2
    建議用於生產工作負載的最小大小。 Auch die Eigenschaften
    使用 SQL 時，虛擬機器的最小建議的大小為 A3
    Server 企業版。 選取 A3 或更高版本，當使用 SQL Server
    企業版。 使用 SQL Server 2012 或 2014年時，請選取 A4
    Enterprise Optimized for Transactional Workloads 映像。
     使用 SQL Server 2012 或 2014 Enterprise Optimized 時，請選取 A7
    資料倉儲工作負載映像。 選取的大小限制
    您可以設定的資料磁碟數目。 對於大多數最新狀態
    可用的虛擬機器大小和數目的詳細資訊
    資料磁碟，您可以附加至虛擬機器，請參閱 [虛擬
    機器大小
    Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)。 如需定價資訊，請參閱 [虛擬機器定價](http://azure.microsoft.com/pricing/details/virtual-machines/)。

    按一下右下角的 [下一步] 箭頭以繼續操作。

    ![VM 組態][2]

5.  第二個 **虛擬機器組態** 頁面上，設定
    網路、 儲存體和可用性的資源 ︰

    -   在 **雲端服務** 方塊中，選擇 [ **建立新的雲端
        服務**。
    -   在 **Cloud Service DNS Name** 方塊中，提供第一個部分
        您所選擇的 DNS 名稱，因此它完成中的名稱
        格式 **TESTNAME.cloudapp.net**
    -   在 **區域/同質群組/虛擬網路** 方塊中，選取
        裝載這個虛擬映像的區域。
    -   在 **儲存體帳戶**, ，選取現有的儲存體帳戶
        或選取自動產生。
    -   在 **可用性設定組** 方塊中，選取 **（無）**。
    -   閱讀並接受定價資訊。

6.  在 **端點** 區段中，按一下 [空白] 底下的下拉式清單 **名稱**, ，並
    選取 **MSSQL**  然後輸入連接埠號碼
    資料庫引擎的執行個體 (**1433年** 預設執行個體)。

7.  您的 SQL Server VM 也可以用來做為 IPython Notebook 伺服器 (將在稍後步驟中設定)。
    新增端點以指定可供 IPython Notebook 伺服器使用的連接埠。 中輸入名稱 **名稱** 資料行、 選取您所選擇的公用連接埠的連接埠號碼和私人連接埠 9999。

    按一下右下角的 [下一步] 箭頭以繼續操作。

    ![選取 MSSQL 和 IPython 連接埠][3]

8.  接受預設值 **安裝 VM 代理程式** 檢查的選項，然後按一下右下角的核取記號
    精靈完成 VM 佈建程序的角。

    `![VM 的最後選項][4]

9.  等待 Azure 準備好您的虛擬機器。 預期的虛擬
    若要繼續執行的機器狀態 ︰

    -   啟動中 (佈建中)
    -   已停止
    -   啟動中 (佈建中)
    -   執行中 (佈建中)
    -   執行中

##<a name="RemoteDesktop"></a>使用遠端桌面開啟虛擬機器並完成設定

1.  佈建完成時，按一下您的虛擬名稱
    請移至 [儀表板] 頁面上的電腦。 在頁面底部
    按一下 [ **連接**。

2.  選擇來開啟 rpd 檔案使用 Windows 遠端桌面程式
    (`%windir%\system32\mstsc.exe`).

3.  在 **Windows 安全性** 對話方塊方塊中，提供的密碼
    您在先前步驟中指定的本機系統管理員帳戶。
    （可能會要求您確認虛擬認證
    電腦上。）

4.  第一次登入這個虛擬機器時，數個程序
    若要完成，包括設定桌面，Windows 可能需要
    更新，以及完成 Windows 初始設定工作
    (sysprep)。 Windows sysprep 完成之後，SQL Server 安裝程式
    完成組態工作。 這些工作可能會造成的延遲
    幾分鐘的時間完成時。 `SELECT @@SERVERNAME` 用戶不得
    傳回 SQL Server 安裝程式完成之前，正確的名稱和 SQL
    Server Management Studio 不會出現在 [開始] 頁面。

您一次連接至虛擬機器與 Windows 遠端
桌面、 虛擬機器的作用很像任何其他電腦。 連線
若要使用 SQL Server Management Studio 的 SQL Server 的預設執行個體
（虛擬機器上執行） 以一般方式。

##<a name="InstallIPython"></a>安裝 IPython Notebook 和其他支援工具

若要設定新的 SQL Server VM 做為 IPython Notebook 伺服器，並安裝其他
這類 AzCopy、 Azure 儲存體總管、 實用的資料科學 Python 封裝和其他支援工具
特殊的自訂指令碼會提供給您。 若要安裝：

- 以滑鼠右鍵按一下 Windows [開始] 圖示，然後按一下 [ **命令提示字元 （系統管理員）**
- 複製下列命令並貼至命令提示字元。

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- 出現提示時，輸入您為 IPython Notebook 伺服器選擇的密碼。
- 自訂指令碼會自動執行數個後續安裝程序，包括：
    + 安裝和設定 IPython Notebook 伺服器
    + 在稍早建立之端點的 Windows 防火牆中開啟 TCP 連接埠：
    + 適用於 SQL Server 遠端連線
    + 適用於 IPython Notebook 伺服器遠端連線
    + 擷取 IPython Notebook 範例和 SQL 指令碼
    + 下載並安裝實用的資料科學 Python 封裝
    + 下載並安裝 Azure 工具，例如 AzCopy 和 Azure 儲存體總管  
<br>
- 您可以使用 `https://<virtual_machine_DNS_name>:<port>` 格式的 URL，從任何本機或遠端瀏覽器存取並執行 IPython Notebook，URL 格式中的 port 是您佈建虛擬機器時選取的 IPython 公用連接埠。
- IPython Notebook 伺服器正以背景服務形式執行，而且將在您重新啟動虛擬機器時自動重新啟動。

##<a name="Optional"></a>視需要連接資料磁碟

如果您的 VM 映像不包含資料磁碟，亦即，磁碟不是 C 磁碟機 (作業系統磁碟) 和 D 磁碟機 (暫存磁碟)，您就需要新增一或多個資料磁碟來儲存資料。 SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads 的 VM 映像是使用其他磁碟預先設定的，可供 SQL Server 資料和記錄檔使用。

 > [AZURE.NOTE] 請勿使用 D 磁碟機來儲存資料。 顧名思義，它只提供暫存儲存空間。 它並不提供備援或備份，因為它不在 Azure 儲存體內。

若要連接其他資料磁碟，請依照所述的步驟 [如何將資料磁碟連接至 Windows 虛擬機器](storage-windows-attach-disk.md), ，這將會引導您完成 ︰

1. 將空磁碟連接至先前步驟中佈建的虛擬機器
2. 在虛擬機器中將新磁碟初始化


##<a name="SSMS"></a>連接到 SQL Server Management Studio 並啟用混合模式驗證

SQL Server 資料庫引擎無法使用 Windows 驗證，而不需要
網域環境。 若要從另一個連接到 Database Engine
電腦上，設定 SQL Server 混合的模式驗證。 混合的模式
驗證可允許 SQL Server 驗證和 Windows
驗證。 需要 SQL 驗證模式，才能擷取資料
直接從 SQL Server VM 資料庫中
[Azure Machine Learning Studio](https://studio.azureml.net) 使用讀取程式模組。

1.  雖然使用遠端桌面連接到虛擬機器，請使用 Windows **搜尋** 窗格和型別 **SQL Server Management Studio** (SMSS)。 按一下以啟動 SQL Server Management Studio (SSMS)。 您可能想要將捷徑新增到桌面上的 SSMS，以供日後使用。

    ![啟動 SSMS][5]

    第一次開啟 Management Studio，它必須建立使用者
    Management Studio 環境。 這可能需要花費幾分鐘的時間。

2.  Management Studio 開啟時，會顯示 **連接到伺服器**
    對話方塊。 在 **伺服器名稱** 方塊中，輸入虛擬名稱
    電腦無法連線到資料庫引擎與 [物件總管] 中。
    (而不是虛擬機器名稱您也可以使用 **(local)** 或
    將一個句點當做 **伺服器名稱**。 選取 **Windows
    驗證**, ，並保留
    ***your\_VM\_name*\\your\_local\_administrator** 中 **使用者
    名稱** 方塊。 按一下 [ **連接**。

    ![連線到伺服器][6]

    <br>

     > [AZURE.TIP] 您可以變更使用 Windows 登錄機碼變更，或使用 SQL Server Management Studio 的 SQL Server 驗證模式。 若要變更驗證模式使用登錄機碼變更，請啟動 **新查詢** 並執行下列指令碼 ︰

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    若要使用 SQL Server management Studio 變更驗證模式：

3.  在 SQL Server Management Studio 物件總管中，以滑鼠右鍵按一下
    SQL Server （虛擬機器名稱） 的執行個體的名稱和
    然後按一下 [ **屬性**。

    ![伺服器屬性][7]

4.  在 **安全性** ] 頁面的 [ **伺服器驗證**, ，請選取
    **SQL Server 和 Windows 驗證模式**, ，然後按一下
    **確定**。

    ![選取驗證模式][8]

5.  在 [SQL Server Management Studio] 對話方塊中，按一下 [ **確定** 至
    確認重新啟動 SQL Server 的需求。

6.  在物件總管] 中，您的伺服器，以滑鼠右鍵按一下，然後按一下
    **重新啟動**。 （如果 SQL Server 代理程式正在執行，也必須是
    重新啟動。）

    ![重新啟動][9]

7.  在 [SQL Server Management Studio] 對話方塊中，按一下 [ **是** 至
    同意您想要重新啟動 SQL Server。

##<a name="Logins"></a>建立 SQL Server 驗證登入

若要從另一部電腦連接到 Database Engine，您必須建立
至少一個 SQL Server 驗證登入。  

> [AZURE.TIP] 您也可以透過程式設計方式建立新的 SQL Server 登入或使用 SQL Server Management Studio。 若要以程式設計方式使用 SQL 驗證建立新的系統管理員使用者，請啟動 **新查詢** 並執行下列指令碼。 取代 < 新的使用者名稱 > 和 < 新密碼 > 使用您選擇的使用者名稱和密碼。 視需要調整密碼原則 (程式碼範例會關閉原則檢查及密碼到期日)。 如需 SQL Server 登入的詳細資訊，請參閱 [建立登入](http://msdn.microsoft.com/library/aa337562.aspx)。  

    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';

若要使用 SQL Server Management Studio 建立新的 SQL Server 登入：

1.  在 [SQL Server Management Studio 物件總管中，展開資料夾
    您要建立新的登入的伺服器執行個體。

2.  以滑鼠右鍵按一下 **安全性** 資料夾，指向 **新增**, ，然後選取
    **登入...**。

    ![新增登入][10]

3.  在 **登入-新增** 對話方塊的 [ **一般** 頁面上，輸入
    在新的使用者名稱 **登入名稱** 方塊。

4.  選取 **SQL Server 驗證**。

5.  在 **密碼** 方塊中，輸入新使用者的密碼。 Enter
    該密碼一次 **確認密碼** 方塊。

6.  若要強制執行複雜性和強制性密碼原則選項
    選取 **強制執行密碼原則** （建議選項）。 這是預設值
    選取 SQL Server 驗證時的選項。

7.  若要強制執行逾期密碼原則選項，請選取 **強制執行
    密碼到期** （建議選項）。 強制執行密碼原則必須
    選取要啟用此核取方塊。 這是預設選項時 SQL
    已選取伺服器驗證。

8.  若要強制使用者在第一次建立新的密碼
    登入，則選取 **使用者必須變更密碼，在下次登入時**
    （建議使用此登入是否為其他人使用。 如果登入
    是供自己使用，請勿選取此選項。)強制執行密碼
    若要啟用此核取方塊，必須選取到期日。 這是
    選取 SQL Server 驗證時的預設選項。

9.  從 **預設資料庫** 清單中，選取預設資料庫
    登入。 **主要** 是此選項的預設值。 如果您有
    尚未建立使用者資料庫，請將此設為 **主要**。

10. 在 **預設語言** 清單中，保留 **預設** 做為值。

    ![登入屬性][11]

11. 如果這是您所建立的第一個登入，您可能想要
    將此登入指定為 SQL Server 系統管理員。 如果是的話，請在
    **伺服器角色** ] 頁面上，核取 **sysadmin**。

    **安全性注意事項 ︰** sysadmin 固定的伺服器角色的成員擁有
    Database Engine 的完整控制權限。 您應該仔細
    限制此角色的成員資格。

    ![系統管理員 (sysadmin)][12]

12. 按一下 [確定]。

##<a name="DNS"></a>決定虛擬機器的 DNS 名稱

若要從另一部電腦，連接到 SQL Server Database Engine 您
必須知道虛擬機器的網域名稱系統 (DNS) 名稱。
(這是網際網路用來識別虛擬機器的名稱。 您
可以使用 IP 位址，但是當 Azure 移動時，可能會變更 IP 位址
冗餘或維護的資源。 DNS 名稱是穩定的
因為它可以重新導向至新的 IP 位址。）

1.  在 Azure 傳統入口網站 （或上一個步驟），請選取
    **虛擬機器**。

2.  在 **虛擬機器執行個體** 頁面上，於 **DNS 名稱**
    資料行中，尋找並複製的 DNS 名稱的虛擬機器的
    前方加上 **http://**。 （使用者介面可能無法
    顯示完整名稱，但您可以以滑鼠右鍵按一下它，然後選取
    複製。）

##<a name="cde"></a>從另一台電腦連接到 Database Engine

1.  在連接網際網路的電腦上開啟 SQL Server Management Studio。

2.  在 **連接到伺服器** 或 **連接到 Database Engine**
    對話方塊中，於 **伺服器名稱** 方塊中，輸入的 DNS 名稱
    （於上一個工作中決定） 的虛擬機器和公用
    端點連接埠號碼，格式為 *DNSName，portnumber* 例如
    **tutorialtestVM.cloudapp.net,57500**。

3.  在 **驗證** 方塊中，選取 **SQL Server 驗證**。

4.  在 **登入** 方塊中，輸入您在建立登入名稱
    先前的工作。

5.  在 **密碼** 方塊中，輸入登入的密碼，您
    在先前工作中建立。

6.  按一下 [ **連接**。

##<a name="amlconnect"></a>從 Azure Machine Learning 連接 Database Engine

在進階分析程序和技術的後期階段，您將使用 [Azure Machine Learning Studio](https://studio.azureml.net) 建置和部署機器學習模型。 若要直接在 Azure Machine Learning 供訓練或評分擷取 SQL Server VM 資料庫中的資料，使用讀取器模組在新 [Azure Machine Learning Studio](https://studio.azureml.net) 實驗。 《進階分析程序和技術》指南中的連結提供更多與本主題相關的詳細資訊。 如需簡介，請參閱 [什麼是 Azure Machine Learning Studio？](machine-learning-what-is-ml-studio.md)。

2.  在 **屬性** 窗格 [讀取器模組](https://msdn.microsoft.com/library/azure/dn905997.aspx), ，請選取 **Azure SQL Database** 從 **資料來源**   下拉式清單中。

3.  在 **資料庫伺服器名稱** 文字中，輸入 `tcp:<DNS name of your virtual machine>,1433`

4.  輸入 SQL 使用者名稱，在 **伺服器使用者帳戶名稱** 文字方塊。

5.  輸入 sql 使用者的密碼在 **伺服器使用者帳戶密碼** 文字方塊。

    ![Azure ML 讀取器][13]

##<a name="shutdown"></a>關閉並解除配置非使用中的虛擬機器

Azure 虛擬機器的定價為 **只支付您所使用**。 若要確定，您不會被計費不使用虛擬機器時，它必須為 **已停止 （取消配置）** 狀態。

> [AZURE.NOTE] 關閉虛擬機器從內部 （使用 Windows 電源選項），雖然 VM 已停止，但仍然處於已配置。 若要確保您不會被計費，一律停止虛擬機器從 [Azure 傳統入口網站](http://manage.windowsazure.com/)。 您也可以藉由呼叫 ShutdownRoleOperation 搭配相當於 "StoppedDeallocated" 的 "PostShutdownAction"，透過 Powershell 來停止 VM。

關閉及取消配置虛擬機器：

1. 登入 [Azure 傳統入口網站](http://manage.windowsazure.com/) 使用您的帳戶。  

2. 選取 **虛擬機器** 從左側的導覽列。

3. 在虛擬機器清單中，按一下您的虛擬名稱
   電腦，然後移至 **儀表板** 頁面。

4. 在頁面底部，按一下 [ **關機**。

![VM 關閉][15]

虛擬機器將會取消配置，但不是刪除。 您隨時都可從 Azure 傳統入口網站重新啟動您的虛擬機器。

## 您的 Azure SQL Server VM 已準備好可供使用：下一步是什麼？

您的虛擬機器已經準備好在資料科學練習中使用。 虛擬機器也已經準備好用來做為 IPython Notebook 伺服器，以用於進行資料探索和處理，以及其他可與 Azure Machine Learning 和 Cortana 分析程序 (CAP) 一起使用的工作。

資料科學程序的下一個步驟中的對應 [學習指南 ︰ 進階資料處理，在 Azure 中](machine-learning-data-science-advanced-data-processing.md) ，而且可能包括將資料移至 HDInsight，程序並進行取樣，並在與 Azure Machine Learning 從資料學習的準備步驟。


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 

