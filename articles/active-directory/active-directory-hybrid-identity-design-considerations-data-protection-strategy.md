<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量 - 定義資料保護策略 | Microsoft Azure"
    description="您將為混合式身分識別解決方案定義資料保護策略，以符合您已定義的商務需求："
    documentationCenter=""
    services="active-directory"
    authors="yuridio"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="11/11/2015"
    ms.author="yuridio"/>


# 定義混合式身分識別解決方案的資料保護策略

在這項工作中，您將為混合式身分識別解決方案定義資料保護策略，以符合您已定義的商務需求：

- [判斷資料保護需求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) 
- [判斷內容管理需求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [判斷存取控制需求](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [判斷事件因應需求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## 定義資料保護選項
如所述 [判斷目錄同步作業需求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), ，Microsoft Azure AD 可以同步處理您 Active Directory 網域服務 (AD DS) 與位於內部部署上。 這項整合可讓組織在使用者嘗試存取公司資源時，利用 Azure AD 來驗證使用者的認證。 下列兩種案例都適用此作法：內部部署和雲端中的待用資料。  要存取 Azure AD 中的資料，必須透過 Security Token Service (STS) 進行使用者驗證。 

經過驗證後，即會從驗證權杖中讀取使用者主體名稱 (UPN)，並判斷對應於使用者網域的複寫資料分割和容器。 授權系統會使用使用者的存在情形、啟用狀態和角色的相關資訊，判斷這名使用者在此工作階段中對目標租用戶的存取要求是否已獲授權。 特定的授權動作 (具體而言，如建立使用者、密碼重設) 會建立可供租用戶管理員用來管理法規遵循工作或調查的稽核記錄。

由於資料數量、頻寬可用性或其他考量，透過網際網路連線將資料從您的內部部署資料中心移至 Azure 儲存體中，並不一定永遠可行。  [Azure 儲存體匯入/匯出服務](../storage/storage-import-export-service.md) 提供硬體為基礎的選項，針對放置/擷取大量的 blob 儲存體中的資料。 它可讓您傳送 [BitLocker 加密](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) 直接到 Azure 資料中心位置定域機組運算子將內容上傳至儲存體帳戶，或他們可以下載至您的磁碟機，以返回您的 Azure 資料的硬碟磁碟機。 在此程序中只能使用加密磁碟 (使用服務本身在工作設定期間產生的 BitLocker 金鑰)。 BitLocker 金鑰會個別提供給 Azure，藉此提供頻外金鑰共用。 

由於資料在傳輸過程中的需要不同的案例中的位置，也會知道 Microsoft Azure 會使用與有關 [虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/) 隔離租用戶流量，採用量值，例如主機和客體層級防火牆、 IP 封包篩選，封鎖連接埠，和 HTTPS 端點。 不過，Azure 的內部通訊大多也會加密，包括基礎結構對基礎結構和基礎結構對客戶 (內部部署) 的通訊。 另一個重要的案例是 Azure 資料中心內的通訊；Microsoft 會管理網路，以確保沒有 VM 可以模擬或竊聽其他 VM 的 IP 位址。 在存取 Azure 儲存體或 SQL Database，或是在連接到雲端服務時，會使用 TLS/SSL。 在此情況下，客戶的系統管理員會負責取得 TLS/SSL 憑證，並將其部署至其租用戶基礎結構。 透過 Microsoft Azure 虛擬網路在相同部署中的虛擬機器之間或單一部署中的租用戶之間移動的資料流量，可透過加密的通訊協定 (例如 HTTPS、SSL/TLS 或其他通訊協定) 受到保護。

根據您對中的問題的回答 [決定資料保護需求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), ，您應該能夠判斷要如何保護您的資料以及混合式身分識別解決方案的幫助您的。 下表顯示 Azure 所支援並且可用於各種資料保護案例的選項。 


| 資料保護選項         | 在雲端中待用 | 在內部部署中待用 | 傳輸中 |
|---------------------------------|----------------------|---------------------|------------|
| BitLocker 磁碟機加密      | X                    | X                   |            |
| 要加密資料庫的 SQL Server | X                    | X                   |            |
| VM 對 VM 加密             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
讀取 [依功能遵循](http://azure.microsoft.com/support/trust-center/services/) 在 [Microsoft Azure 信任中心](http://azure.microsoft.com/support/trust-center/) 要深入了解每個 Azure 服務是否相容的認證。
由於資料保護的選項採用多層式方法，因此在執行這項工作時無法比較這些選項。 請確實針對資料所處的各種狀態，運用所有可用的選項。

## 定義內容管理選項
使用 Azure AD 來管理混合式身分識別基礎結構的優點之一，是整個程序對使用者而言是完全透明的。 使用者會嘗試存取共用資源，而資源需要驗證，因此使用者必須將驗證要求傳送至 Azure AD，才能夠取得權杖並存取資源。 整個程序都會在背景執行，使用者無須動作。 您也可授與權限 [群組](active-directory-manage-groups.md#getting-started-with-access-management) 的使用者，以便讓他們執行某些常見的動作。

有資料隱私權顧慮的組織通常需要對其解決方案進行資料分類。 如果其目前的內部部署基礎結構已使用資料分類，就能夠利用 Azure AD 作為使用者身分識別的主要儲存機制。 常用的工具，它是內部使用，會呼叫資料分類為 [資料分類工具組](https://msdn.microsoft.com/library/Hh204743.aspx) 適用於 Windows Server 2012 R2。 這項工具有助於您對私人雲端中的檔案伺服器進行資料的識別、分類及保護。 您也可利用 [自動檔案分類](https://technet.microsoft.com/library/hh831672.aspx) 來完成這項作業的 Windows Server 2012 中。

如果您的組織不已備妥資料分類，但需要保護敏感檔案而不加入新的伺服器在內部，他們可以使用 Microsoft [Azure Rights Management 服務](https://technet.microsoft.com/library/JJ585026.aspx)。  Azure RMS 會使用加密、身分識別和授權原則協助您保護檔案和電子郵件，並且可跨多個裝置運作 — 手機、平板電腦和 PC。 Azure RMS 屬於雲端服務，因此您無須明確設定對其他組織的信任，即可與他們共用受保護的內容。 如果他們已有 Office 365 或 Azure AD 目錄，則會自動支援跨組織的共同作業。 您也可以使用 Azure Active Directory 同步處理服務 (AAD 同步) 或 Azure AD Connect，僅同步處理 Azure RMS 支援您內部部署 Active Directory 帳戶的一般身分識別所需的目錄屬性。

了解誰正在存取哪個資源，是內容管理很重要的一部分，因此強大的記錄功能對於身分識別管理解決方案而言，是很重要的。 Azure AD 提供超過 30 天的記錄，其中包括：

- 角色成員資格的變更 (例如：新增至全域管理員角色的使用者)
- 認證更新 (例如：密碼變更)
- 網域管理 (例如：驗證自訂網域、移除網域)
- 新增或移除應用程式
- 使用者管理 (例如：新增、移除、更新使用者)
- 新增或移除授權

>[AZURE.NOTE]
讀取 [Microsoft Azure 安全性和稽核記錄檔管理](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) 要深入了解在 Azure 中的記錄功能。
根據您對中的問題的回答 [決定內容管理需求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), ，您應該能夠判斷如何在您想要管理混合式身分識別方案中的內容。 雖然表 6 中顯示的所有選項都都能夠與 Azure AD 整合，但請務必定義何者較適用於您的商務需求。

| 內容管理選項                                                               | 優點                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 缺點                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 集中式的內部部署 (Active Directory Rights Management Server)                      | 負責將資料分類的伺服器基礎結構的完整控制權 <br> 在 Windows Server 中，不需要額外的授權或訂閱的內建功能 <br> 可以與混合式案例中的 Azure AD 整合 <br> Microsoft 線上服務，例如 Exchange Online 和 SharePoint Online 以及 Office 365 中支援的資訊版權管理 (IRM) 功能 <br> 支援內部部署 Microsoft 伺服器產品，例如 Exchange Server、 SharePoint Server 以及執行 Windows Server 和檔案分類基礎結構 (FCI) 的檔案伺服器。 | 更高版本，維護 (保留安裝的更新、 設定與升級)，因為 IT 擁有該伺服器 <br> 需要伺服器基礎結構內部部署<br> Doesn'tleverage 的 Azure 功能原生                                     |
| 集中於雲端 (Azure RMS)                                                     | 更輕鬆地管理相較於內部部署解決方案 <br> 可以與混合式案例中的 AD DS 整合 <br>  完全與 Azure AD 整合 <br> 不需要伺服器的內部部署服務 <br> 支援內部部署 Microsoft 伺服器產品，例如 Exchange Server、 SharePoint、 伺服器和執行 Windows Server 和檔案分類基礎結構 (FCI) 的檔案伺服器 <br> IT 可以擁有其租用戶金鑰 BYOK 功能的完整控制權。                                                                                    | 您的組織必須具有支援 RMS 的雲端訂閱 <br> 您的組織必須具備 Azure AD 目錄才能支援 RMS 的使用者驗證                                                                                  |
| 混合式 (Azure RMS 與內部部署 Active Directory Rights Management Server 整合) | 這種情況下結合了集中式內部部署和雲端兩者的優點。                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | 您的組織必須具有支援 RMS 的雲端訂閱 <br> 您的組織必須具備 Azure AD 目錄才能支援 RMS，使用者驗證 <br> 需要 Azure 雲端服務之間的連接和內部基礎結構 |

## 定義存取控制選項
利用 Azure AD 中提供的驗證、授權和存取控制功能，您將能夠讓公司使用中央身分識別儲存機制，同時允許使用者和合作夥伴使用單一登入 (SSO)，如下圖所示：

![](./media/hybrid-id-design-considerations/centralized-management.png)

集中式管理以及與其他目錄的完全整合

Azure Active Directory 可為數千個 SaaS 應用程式和內部部署 Web 應用程式提供單一登入。 請閱讀 [Azure Active Directory 同盟相容性清單: 可用來實作單一登入的協力廠商身分識別提供者](https://msdn.microsoft.com/library/azure/jj679342.aspx) 如需詳細資訊 SSO 協力廠商都經過 microsoft 測試文件。 這項功能可讓組織實作各種 B2B 案例，同時得以控制身分識別和存取管理。 不過，在 B2B 期間，要了解合作夥伴所將使用的驗證方法驗證，並驗證 Azure 是否支援此方法，設計程序是很重要的。 以下是 Azure AD 目前支援的方法：

- 安全性判斷提示標記語言 (SAML)
- OAuth
- Kerberos
- 權杖
- 憑證


>[AZURE.NOTE]
讀取 [Azure Active Directory 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn151124.aspx) 知道有關每個通訊協定和它的功能，在 Azure 中更多詳細資料。
藉由 Azure AD 支援，行動商業應用程式可使用相同的簡單行動服務驗證功能，讓員工利用公司的 Active Directory 認證登入其行動應用程式。 透過這項功能，Azure AD 可受到支援作為行動服務中的身分識別提供者，與我們已支援的其他身分識別提供者 (包括 Microsoft 帳戶、Facebook ID、Google ID 和 Twitter ID) 搭配運作。 如果內部部署應用程式使用位於公司 AD DS 的使用者認證，則來自雲端的協力廠商和使用者所做的存取應該是透明的。 您可以管理使用者對 (以雲端為基礎的) Web 應用程式、Web API、Microsoft 雲端服務、協力廠商 SaaS 應用程式和原生 (行動) 用戶端應用程式的條件式存取控制，並且可因為安全性、稽核和報告全都集中在同一處而獲益。 不過，建議您在非生產環境中或使用者數量有限的環境中進行此驗證。 

>[AZURE.TIP]
務必要提到的是，Azure AD 不像 AD DS 一樣具有群組原則。 若要強制執行裝置的原則您需要行動裝置管理解決方案例如 [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx)。

使用 Azure AD 驗證使用者之後，務必要評估使用者所將擁有的存取層級。 使用者對於資源的存取層級可能會改變，因為 Azure AD 可藉由控制對某些資源的存取而增加額外的安全性層級，因此您必須謹記，資源本身也可能有其本身的存取控制清單，例如，對位於檔案伺服器中之檔案的存取控制。 下圖摘要說明您在混合式案例中可以有的存取控制層級：

![](./media/hybrid-id-design-considerations/accesscontrol.png)


圖 X 中顯示的每個互動，分別代表 Azure AD 可以涵蓋的一個存取控制案例。 每個案例的說明如下：

1. 內部部署裝載之應用程式的條件式存取：您可以使用已註冊的裝置搭配適用於已設定為使用 AD FS with Windows Server 2012 R2 之應用程式的存取原則。 如需條件式存取的內部部署上設定的詳細資訊，請參閱 [設定內部部署條件式存取使用 Azure Active Directory 裝置註冊](active-directory-conditional-access-on-premises-setup.md)。 
2. 存取控制以 Azure 管理入口網站: Azure 也能夠使用 RBAC (角色型存取控制)，以控制能夠存取管理入口網站。 此方法可讓公司限制個人存取 Azure 管理入口網站之後可執行的作業數量。 使用 RBAC 來控制存取入口網站時，IT 系統管理員可以利用下列存取管理方法來委派存取：

 - 群組型角色指派：您可以指派存取權給可從本機 Active Directory 同步處理的 Azure AD 群組。 這可讓您運用組織目前在群組管理工具和程序方面所做的投資。 您也可以使用 Azure AD Premium 的委派群組管理功能。
 - 運用 Azure 中內建的角色：您可以使用三個角色 — 擁有者、參與者和讀者，以確保使用者和群組只擁有他們執行工作所需的權限。 
 - 資源細微存取權: 您可以將角色指派給使用者和群組特定的訂用帳戶、 資源群組或個別的 Azure 資源，例如網站或資料庫。 如此一來，您可以確保使用者能夠存取他們需要的資源，但不能存取他們不需要管理的資源。

 >[AZURE.NOTE]
  讀取 [Azure 預覽入口網站中的角色型存取控制](http://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) 知道這項功能的詳細。 如果是建置應用程式並想要自訂其存取控制的開發人員，可以使用 Azure AD 應用程式角色來進行授權。 檢閱這 [研究 RoleClaims WebApp DotNet 範例](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) 如何建置應用程式來使用這項功能。

3. Office 365 應用程式使用 Microsoft Intune 的條件式存取: IT 系統管理員可以佈建條件式存取裝置原則來保護公司資源，而同時讓資訊工作者存取服務的相容裝置上。 如需詳細資訊，請參閱 [Office 365 服務的條件式存取裝置原則](active-directory-conditional-access-device-policies.md)。

4. Saas 應用程式的條件式存取: [這項功能](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) 可讓您設定每個應用程式多因素驗證存取規則與封鎖不受信任的網路上的使用者存取權限。 您可以將多因素驗證規則套用至所有已指派給應用程式的使用者，或只套用至指定的安全性群組內的使用者。 如果使用者是從組織網路內的 IP 位址存取應用程式，則可從多因素驗證需求中排除這些使用者。

由於存取控制的選項採用多層式方法，因此在執行這項工作時無法比較這些選項。 請確實針對每個要求您控制資源存取權的案例，使用所有適用的選項。

## 定義事件回應選項
Azure AD 可藉由監視使用者活動，協助 IT 人員識別環境中潛在的安全性風險；IT 人員可利用 Azure AD 存取和使用量報告深入了解組織目錄的完整性與安全性。 利用此資訊，IT 管理員更能夠判斷可能發生安全性風險的位置，以便適當地規劃來減輕這些風險。  [Azure AD Premium 訂用帳戶](articles/active-directory-get-started-premium.md) 都有一組安全性報告，可以讓 IT 能夠取得這項資訊。 [Azure AD 報告](active-directory-view-access-usage-reports.md) 分為，如下所示:

- **異常報告**: 包含登入我們發現異常的事件。 我們的目標在於使您注意這類活動，並讓您能夠判斷事件是否可疑。 
- **整合應用程式報告**: 提供深入的雲端應用程式在組織中的使用方式。 Azure Active Directory 提供與數千個雲端應用程式的整合。 
- **錯誤報告**: 指出帳戶佈建至外部應用程式時可能發生的錯誤。
- **特定使用者報告**: 顯示裝置/登入特定的使用者活動資料。
- **活動記錄檔**: 包含過去 24 小時、 過去 7 天或過去 30 天，以及群組活動變更，以及密碼重設和註冊活動內所有稽核事件的記錄。

>[AZURE.TIP]
也可以協助處理案例事件回應小組的另一份報表是 [認證外洩的使用者](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) 報表。  此報告會呈現這些外洩的認證清單與您的租用戶之間的任何相符項目。

Azure AD 中還有其他可在事件回應調查期間使用的重要內建報告，包括：

- **密碼重設活動**: 深入了解如何重設密碼是在組織中正在使用您提供系統管理員。
- **密碼重設註冊活動**: 提供的使用者已註冊密碼重設，其方法的深入資訊，而且已選取的方法。
- **群組活動**: 提供變更記錄到群組 (ex: 新增或移除的使用者)，而在存取面板中啟動。

除了 Azure AD Premium 中可在事件回應調查程序期間使用的核心報告功能以外，IT 人員也可使用稽核報告取得資訊，例如：

- 角色成員資格的變更 (例如：新增至全域管理員角色的使用者)
- 認證更新 (例如：密碼變更)
- 網域管理 (例如：驗證自訂網域、移除網域)
- 新增或移除應用程式
- 使用者管理 (例如：新增、移除、更新使用者)
- 新增或移除授權

由於事件回應的選項採用多層式方法，因此在執行這項工作時無法比較這些選項。 請確實針對每個要求您在公司的事件回應程序中使用 Azure AD 報告功能的案例，使用所有適用的選項。

## 後續步驟
[判斷混合式身分識別管理工作](active-directory-hybrid-identity-design-considerations-hybridId-management-tasks.md)


## 另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)


