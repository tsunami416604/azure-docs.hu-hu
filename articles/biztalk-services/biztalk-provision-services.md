<properties
    pageTitle="在 Azure 入口網站中建立 Azure BizTalk 服務 | Microsoft Azure"
    description="了解如何在 Azure 入口網站中佈建或建立 BizTalk 服務；MABS，WABS"
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/01/2015"
    ms.author="mandia"/>



# 使用 Azure 入口網站建立 BizTalk 服務

本文說明如何使用在 Azure 入口網站中建立 Azure BizTalk 服務。

> [AZURE.TIP] 若要登入 Azure 入口網站，您將需要 Azure 帳戶和 Azure 訂用帳戶。 如果沒有帳戶，您可在幾分鐘內建立免費試用帳戶。 請參閱 [Azure 免費試用版](http://go.microsoft.com/fwlink/p/?LinkID=239738)。

## 建立 BizTalk 服務
視您所選的版本而定，部分 BizTalk 服務設定可能無法使用。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在導覽窗格底部選取 **新增**:  
![選取 [新增] 按鈕][NEWButton]

3. 選取 **應用程式服務** > **BIZTALK 服務** > **自訂建立**:  
![選取 BizTalk 服務，然後選取 [自訂建立][NewBizTalkService]

4. 輸入 BizTalk 服務設定：

    <table border="1">
    <tr>
    <td><strong>BizTalk 服務名稱</strong></td>
    <td>您可以輸入任何名稱，但請使用特定的名稱。 部分範例包括：<br/><br/>
    <em>mycompany</em>。.biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>。.biztalk.windows.net<br/>
    <em>myapplication</em>。.biztalk.windows.net<br/><br/>".biztalk.windows.net" 會自動新增至您輸入的名稱。 這會建立用來存取 BizTalk 服務，例如 URL <strong>https://<em>myapplication</em>。.biztalk.windows.net</strong>。
    </td>
    </tr>
    <tr>
    <td><strong>版本</strong></td>
    <td>如果您是在測試/開發階段中，選擇 [ <strong>開發人員</strong>。 如果您處於實際執行階段時，請使用 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk 服務 ︰ 版本圖表</a> 判斷 <strong>高階</strong>, ，<strong>標準</strong>, ，或 <strong>基本</strong> 是您的商業案例的正確選擇。
    </td>
    </tr>
    <tr>
    <td><strong>區域</strong></td>
    <td>選取地理區域來主控您的 BizTalk 服務。</td>
    </tr>
    <tr>
    <td><strong>網域 URL</strong></td>
    <td><strong>選擇性</strong>。 根據預設，網域 URL 是 <em>YourBizTalkServiceName</em>。.biztalk.windows.net。 您也可輸入自訂網域。 例如，如果您的網域是 <em>contoso</em>, ，您可以輸入 ︰ <br/><br/>
    <em>MyCompany</em>。 contoso.com<br/>
    <em>MyCompanyMyApplication</em>。 contoso.com<br/>
    <em>MyApplication</em>。 contoso.com<br/>
    <em>YourBizTalkServiceName</em>。 contoso.com<br/>
    </td>
    </tr>
    </table>
選取下一個箭頭。

5. 輸入儲存體和資料庫設定：

    <table border="1">
    <tr>
    <td><strong>監視/封存儲存體帳戶</strong></td>
    <td>選取現有的儲存體帳戶或建立新的儲存體帳戶。 <br/><br/>如果您建立新的儲存體帳戶，請輸入 <strong>儲存體帳戶名稱</strong>。</td>
    </tr>
    <tr>
    <td><strong>追蹤資料庫</strong></td>
    <td>如果您使用現有的 Azure SQL Database，其他 BizTalk 服務將無法使用它。 您需要建立 Azure SQL Database 伺服器時輸入的登入名稱和密碼。<br/><br/><strong>秘訣</strong> 與 BizTalk 服務相同的區域中建立追蹤資料庫和監視/封存儲存體帳戶。</td>
    </tr>
    </table>
選取下一個箭頭。

6. 輸入資料庫設定：

    <table border="1">
    <tr>
    <td><strong>名稱</strong></td>
    <td>時，才能使用 <strong>建立新的 SQL 資料庫執行個體</strong> 在前一個畫面中選取。
    <br/><br/>
    輸入 BizTalk 服務要使用的 SQL Database 名稱。</td>
    </tr>
    <tr>
    <td><strong>伺服器</strong></td>
    <td>時，才能使用 <strong>建立新的 SQL 資料庫執行個體</strong> 在前一個畫面中選取。
    <br/><br/>
    選取現有的 SQL Database 伺服器，或是建立全新的 SQL Database 伺服器。</td>
    </tr>
    <tr>
    <td><strong>伺服器登入名稱</strong></td>
    <td>輸入登入使用者名稱。</td>
    </tr>
    <tr>
    <td><strong>伺服器登入密碼</strong></td>
    <td>輸入登入密碼。</td>
    </tr>
    <tr>
    <td><strong>區域</strong></td>
    <td>時，才能使用 <strong>建立新的 SQL 資料庫執行個體</strong> 已選取。 選取地理區域來主控您的 SQL Database。</td>
    </tr>
    </table>

選取核取記號來完成精靈。 進度圖示隨即出現 ︰  
![進度圖示即會顯示][ProgressComplete]

完成時，會建立 Azure BizTalk 服務，並備妥供應用程式使用。 預設設定已夠用。 如果您想要變更預設設定，請選取 **BIZTALK 服務** 的左方瀏覽窗格中，然後選取您的 BizTalk 服務。 其他設定會顯示在 [儀表板、 監視器和調整索引標籤](biztalk-dashboard-monitor-scale-tabs.md) 頂端。

有一些無法完成的作業，視 BizTalk 服務的狀態而定。 如需這些作業的清單，請移至 [BizTalk 服務狀態圖](biztalk-service-state-chart.md)。


## 佈建後續步驟

-  [在本機電腦上安裝憑證](#InstallCert)
-  [新增實際執行備妥憑證](#AddCert)
-  [取得存取控制命名空間](#ACS)

#### <a name="InstallCert"></a>在本機電腦上安裝憑證
自我簽署憑證是 BizTalk 服務佈建的一部分，會在您訂閱 BizTalk 服務時建立並相關聯。 您必須下載此憑證，並將它安裝於您部署 BizTalk 服務應用程式、或向 BizTalk 服務端點傳送訊息的電腦上。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 選取 **BIZTALK 服務** 的左方瀏覽窗格中，然後選取 [BizTalk 服務訂閱。
3. 選取 **儀表板** ] 索引標籤。
4. 選取 **下載 SSL 憑證**:  
![修改 SSL 憑證][QuickGlance]
5. 按兩下此憑證，然後完成執行精靈，即可安裝憑證。 請確定安裝憑證 **信任的根憑證授權單位** 儲存。

#### <a name="AddCert"></a>新增實際執行備妥憑證
在建立 BizTalk 服務時自動建立的自我簽署憑證僅限用於開發環境。 若為生產案例，使用實際執行備妥憑證取代它。

1. 在 **儀表板** 索引標籤上，選取 **更新 SSL 憑證**。
2. 瀏覽至私用的 SSL 憑證 (*CertificateName*.pfx)，包括 BizTalk 服務名稱，輸入密碼，然後按一下 [核取記號。

#### <a name="ACS"></a>取得存取控制命名空間

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 選取 **BIZTALK 服務** 的左方瀏覽窗格中，然後選取您的 BizTalk 服務。
3. 在工作列上，選取 **連接資訊**:  
![選取 [連線資訊][ACSConnectInfo]

4. 複製存取控制值。

從 Visual Studio 部署 BizTalk 服務專案時，您可以輸入此存取控制命名空間。 為 BizTalk 服務自動建立存取控制命名空間。

存取控制值可用於任何應用程式。 當建立 Azure BizTalk 服務時，此存取控制命名空間會利用 BizTalk 服務部署來控制驗證。 如果您想要變更訂閱或管理命名空間，請選取 **ACTIVE DIRECTORY** 左的導覽窗格中，然後選取您的命名空間。 工作列會列出您的選項。

按一下 **管理** 開啟存取控制管理入口網站。 存取控制管理入口網站中，在 BizTalk 服務會使用 **服務身分識別**:  
![ACS 服務身分識別，在 [存取控制管理入口網站][ACSServiceIdentities]

存取控制服務身分識別是一組認證，可讓應用程式或用戶端直接使用 Azure AD 存取控制進行驗證，並接收權杖。

> [AZURE.IMPORTANT] BizTalk 服務會使用 **擁有者** 作為預設服務身分識別和 **密碼** 值。 如果您使用對稱金鑰值而不是密碼值時，可能會發生下列錯誤。<br/><br/>*無法利用指定的認證連接至存取控制管理服務帳戶*

[管理 ACS 命名空間](https://msdn.microsoft.com/library/azure/hh674478.aspx) 列出一些指導方針和建議。

## 說明各項需求

這些需求並不適用於免費版本。
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>您需要什麼</strong></td>
        <td><strong>您為何需要它</strong></td>
</tr>
<tr>
<td>Azure 訂閱</td>
<td>訂用帳戶可判定誰能夠登入 Azure 入口網站。 帳戶持有者可在<a HREF="https://account.windowsazure.com/Subscriptions"> Azure 訂用帳戶</a>中建立訂用帳戶 。
<br/><br/>
Azure 帳戶可擁有多個訂用帳戶，只要使用者取得允許皆可管理這些帳戶。 例如，您的 Azure 帳戶持有者會建立名為 <em>BizTalkServiceSubscription</em> ，並讓 BizTalk 系統管理員，在您的公司 (例如 ContosoBTSAdmins@live.com) 存取此訂用帳戶。 在此案例中，BizTalk 系統管理員可以登入 Azure 入口網站，並對訂用帳戶中的所有代管服務 (包括 Azure BizTalk 服務) 具有完整系統管理員權限。 BizTalk 系統管理員不是 Azure 帳戶持有者，因此無權存取任何任何計費資訊。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> 管理 Azure 入口網站中的訂閱和儲存體帳戶</a> 提供詳細資訊。
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>儲存 Azure BizTalk 服務所使用的資料表、檢視和預存程序，包括追蹤資料。
<br/><br/>
在建立 BizTalk 服務時，您可使用現有的 Azure SQL Server、Azure SQL Database，或自動建立新的伺服器或資料庫。
<br/><br/>
系統會自動設定 SQL Database 調整。 一般來說，預設的調整對 BizTalk 服務已夠用。 修改調整會影響定價。 請參閱 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930"> 帳戶和 Azure SQL Database 中的計費</a>
<br/><br/>
<strong>附註</strong>
<br/>
<ul>
<li> 當您建立新的 Azure SQL Server 和 Database 時，即會自動啟用 Azure 服務。 BizTalk 服務需要啟用 Azure 服務。</li>
<li>如果在現有的 Azure SQL Server 上建立新的 Azure SQL Database，則不會變更伺服器的防火牆規則。 因此，可能不允許其他 Azure 服務存取伺服器的資料庫。</li>
</ul>
</td>
</tr>
<tr>
<td>Azure 存取控制命名空間</td>
<td>利用 Azure BizTalk 服務進行驗證。 從 Visual Studio 部署 BizTalk 服務專案時，您可以輸入此存取控制命名空間。 當您建立 BizTalk 服務時，存取控制命名空間會自動建立。</td>
</tr>

<tr>
<td>Azure 儲存體帳戶</td>
<td>允許使用 BizTalk 服務所用的資料表、Blob 和佇列，以儲存下列檔案：

<ul>
<li>監視 BizTalk 服務的記錄檔。 監視輸出也會顯示在 **監視** Azure 入口網站] 索引標籤。</li>
<li>在夥伴之間建立 X12 或 AS2 協定時，您可以啟用封存功能來儲存訊息內容。 此資料儲存在此儲存體帳戶中。</li>
</ul>
<br/>
當建立 BizTalk 服務時，您可以使用現有的儲存體帳戶，或自動建立新的儲存體帳戶。
<br/><br/>
預設儲存體設定對 BizTalk 服務已夠用。
<br/><br/>
當建立儲存體帳戶時，會自動建立主要金鑰和次要金鑰。 這些金鑰可控制儲存體帳戶的存取。 BizTalk 服務會自動使用主要金鑰。
<br/><br/>
請參閱 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671"> 儲存體</a> 如需詳細資訊。
</td>
</tr>

<tr>
<td>SSL 專用憑證</td>
<td>
當建立 Azure BizTalk 服務時，也會建立一個 HTTPS URL，其中包括 BizTalk 服務名稱。 此 URL 會自動設定為使用自我簽署的開發專用憑證。 若為生產案例，您需要私密的 SSL 憑證。
<br/><br/>
<strong>重要 SSL 憑證資訊</strong>

<ul>
<li>憑證到期日必須少於 5 年。</li>
<li>所有專用憑證都需要一個密碼。 知道此密碼，而且最佳作法為與系統管理員分享此密碼。</li>
<li>自我簽署憑證是在測試/開發環境中使用。 當使用自我簽署憑證時，請將憑證匯入至您的個人憑證存放區和受信任的根授權單位憑證存放區。</li>
</ul>
<br/>將實際執行憑證要求傳送至您的憑證授權單位時，請提供下列憑證內容：
<br/>

<ul>
<li><strong>增強金鑰使用方法</strong>︰ 最低限度，Azure BizTalk 服務需要伺服器驗證。</li>
<li><strong>一般名稱</strong>︰ 輸入 Azure BizTalk 服務 URL 的完整的網域名稱 (FQDN)。 請參閱 <a HREF="#BizTalk">建立 BizTalk 服務</a> 在這篇文章。</li>
</ul>
<br/>
在建立 BizTalk 服務後，可以加入新的或不同的憑證。
</td>
</tr>
</table>



## 混合式連線

當您建立 Azure BizTalk 服務時， **混合式連線** ] 索引標籤 ︰

![混合式連線索引標籤][HybridConnectionTab]

混合式連線可將 Azure 網站或 Azure 行動服務連線到使用靜態 TCP 連接埠的任何內部部署資源，例如 SQL Server、MySQL、HTTP Web API、行動服務及大多數的自訂 Web 服務。  混合式連線和 BizTalk 配接器服務不同。 BizTalk 配接器服務是用於將 Azure BizTalk 服務連線至內部部署企業營運 (LOB) 系統。

 請參閱 [混合式連線](integration-hybrid-connection-overview.md) 若要了解詳細資訊，包括建立和管理混合式連線。


## 後續步驟

既然已建立 BizTalk 服務，讓自己熟悉如何使用不同 [BizTalk 服務 ︰ 儀表板、 監視器和調整索引標籤](biztalk-dashboard-monitor-scale-tabs.md)。 您的 BizTalk 服務已準備好可供您的應用程式使用。 若要開始建立應用程式，請移至 [Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

## 另請參閱
- [BizTalk 服務：版本圖表](biztalk-editions-feature-chart.md)<br/>
- [BizTalk 服務 ︰ 狀態圖表](biztalk-service-state-chart.md)<br/>
- [BizTalk 服務：備份與還原](biztalk-backup-restore.md)<br/>
- [BizTalk 服務：節流](biztalk-throttling-thresholds.md)<br/>
- [BizTalk 服務：簽發者名稱和簽發者金鑰](biztalk-issuer-name-issuer-key.md)<br/>
- [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Hybrid Connections](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png


