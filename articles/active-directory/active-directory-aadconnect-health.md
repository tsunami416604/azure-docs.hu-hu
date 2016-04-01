<properties
    pageTitle="在雲端中監視內部部署身分識別基礎結構。"
    description="這是 Azure AD Connect Health 頁面，其中說明它的功能，以及您可能會使用它的原因。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="12/16/2015"
    ms.author="billmath"/>

# 在雲端中監視內部部署身分識別基礎結構和同步處理服務。

Azure AD Connect Health 可協助您監視和了解內部部署身分識別基礎結構和透過 Azure AD Connect 取得的同步處理服務。  它可讓您檢視警示、效能、使用模式、組態設定，並讓您穩定連線至 Office 365 ，還有更多功能。 這些功能必須使用安裝在目標伺服器上的代理程式才可完成。  

![Azure AD Connect Health 是什麼](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


這些資訊會全部呈現在 Azure AD Connect Health 入口網站中。  您可以使用 Azure AD Connect Health 入口網站檢視警示、效能監視和使用量分析。  這些資訊會全部呈現在容易找到的同一位置中，您便不必浪費時間尋找需要的資訊。

![Azure AD Connect Health 是什麼](./media/active-directory-aadconnect-health/usage.png)

Azure AD Connect Health 未來更新的內容包括額外監視，以及其他身分識別元件的深入資訊。 因此，它透過身分識別透鏡提供單一儀表板，讓您能擁有更穩健、良好的整合式環境，使用者便能夠充分利用以增進工作能力。


<center>![Azure AD Connect Health 是什麼](./media/active-directory-aadconnect-health/logo1.png)</center>




## 使用 Azure AD Connect Health 的原因

將內部部署目錄與 Azure AD 整合可提供通用身分識別供存取雲端和內部部署資源，進而讓您的使用者更具生產力。 然而，整合後帶來的挑戰是，必須確保此環境良好健全，才能讓使用者從任何裝置可靠地存取內部部署和雲端中的資源。 Azure AD Connect Health 提供簡易的雲端方法，監視及深入了解用來存取 Office 365 或其他 Azure AD 應用程式的內部部署身分識別基本結構。 使用方式相當簡單，您只需將代理程式安裝在各個內部部署身分識別伺服器中即可。 

適用於 AD FS 的 Azure AD Connect Health 支援 Windows Server 2008/2008 R2 中的 AD FS 2.0，以及 Windows Server 2012/2012 R2 中的 AD FS。 其中也包括提供外部網路存取驗證支援的任何 AD FS Proxy 或 Web 應用程式 Proxy 伺服器。 適用於 AD FS 的 Azure AD Connect Health 提供下列一組主要功能：

- 檢視警示並據此採取行動，以穩定存取受 AD FS 保護的應用程式 (包括 Azure AD)
- 重大警示的電子郵件通知
- 檢視效能資料以判斷容量規劃
- 詳細檢視 AD FS 登入模式，以判斷異常狀況或建立容量規劃基準


適用於同步處理的 Azure AD Connect Health 可監視及提供內部部署 Active Directory 與 Azure Active Directory 之間發生的同步處理相關資訊。 適用於同步處理的 Azure AD Connect Health 提供下列一組主要功能：

- 檢視警示並採取動作，確保內部部署基礎結構與 Azure Active Directory 之間的同步處理極為可靠。
- 重大警示的電子郵件通知
- 檢視效能資料

下列影片將提供 Azure AD Connect Health 的概觀：

[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]



## 開始使用 Azure 入口網站
若要開始使用 Azure Active Directory Connect Health，請遵循下列步驟進行。 

1. 登入 [Microsoft Azure 入口網站。](https://portal.azure.com/)
2. 前往 Marketplace 並搜尋 Azure Active Directory Connect Health，或選取 Marketplace，然後選取 [安全性] 和 [身分識別] 可以存取 Azure Active Directory Connect Health。
3. 在簡介刀鋒視窗 (刀鋒視窗是整體檢視的一個片段。 您可以將刀鋒視窗與視窗或飛出），按一下 [ **建立**。 這將會開啟另一個刀鋒視窗，其中包含您的目錄資訊。
4. 在目錄分頁中，按一下 [ **建立**。 如果您沒有 Azure Active Directory Premium 授權，您將需要一個授權，才能使用 Azure AD Connect Health。 如需 Azure AD Premium 的相關資訊，請參閱「開始使用 Azure AD Premium」。

>[AZURE.NOTE]請記住，您會看到您的 Azure AD Connect Health 執行個體中的任何資料之前，您必須在目標伺服器上安裝 Azure AD Connect Health 代理程式。 若要下載 Azure AD Connect Health 代理程式，請從第一個刀鋒視窗中選取 [開始使用並取得工具]。 您也可以下載代理程式直接使用 [連結](#download-the-agent) 下方。  若要使用 Azure Active Directory Connect Health，請執行下列操作：



### Azure AD Connect Health 入口網站和服務
Azure AD Connect Health 入口網站可讓您檢視警示、效能監視和使用量分析。 在第一次存取 Azure AD Connect Health 時，將會為您顯示第一個刀鋒視窗。  您可以將刀鋒視窗視為視窗。 您看到的第一個刀鋒視窗會顯示 [開始使用]、[服務] 和 [設定]。 以下螢幕擷取畫面是各自的簡短說明。  [服務] 區段會顯示 Azure AD Connect Health 正在監視的作用中服務以及這些服務的執行個體。 

![Azure AD Connect Health 入口網站](./media/active-directory-aadconnect-health/portal2.png)

- **快速入門** – 選取此您會開啟 [快速入門] 分頁。 在這裡，您將可以透過選擇 [取得工具、存取文件及提供意見反應] 來下載 Azure AD Connect Health 代理程式。
- **Active Directory Federation Services** – 這代表所有目前正在監視 Azure AD Connect Health AD FS 服務。 選取其中一個執行個體後，便會開啟一個包含該服務執行個體相關資訊的刀鋒視窗。  這項資訊包括概觀、屬性、警示、監視和使用情況分析。 
- 設定 – 這可讓您開啟或關閉下列選項：
<ol>
1. 自動更新可將 Azure AD Connect Health 代理程式自動更新為最新版本：這表示您將會在發佈最新版的 Azure AD Connect Health 代理程式時，自動更新為最新版本。 此選項預設為啟用狀態。
2. 僅允許 Microsoft 存取您的 Azure AD 目錄的健康狀況資料供疑難排解之用：這表示如果啟用此選項，Microsoft 將可以看到您所看到的相同資料。 這可能有助於進行問題的疑難排解和協助。 此選項預設為停用狀態。




## 需求
下表是開始使用 Azure AD Connect Health 之前必須符合的需求清單。

| 需求 | 說明|
| ----------- | ---------- |
|Azure AD Premium| Azure AD Connect Health 是 Azure AD Premium 的一個功能，而且需要 Azure AD Premium。 </br></br>如需詳細資訊，請參閱 [開始使用 Azure AD Premium](active-directory-get-started-premium.md)。</br></br>若要啟動免費的 30 天試用版，請參閱 [開始使用試用版。](https://azure.microsoft.com/trial/get-started-active-directory/)|.
|您必須是 Azure AD 的全域系統管理員，才能啟用 (建立) Azure AD Connect Health|依預設，只有全域系統管理員才能啟用 (建立)、存取所有資訊，以及執行 Azure AD Connect Health 內的所有作業。 如需詳細資訊，請參閱 [管理 Azure AD 目錄](active-directory-administer.md)。 <br><br> 使用角色型存取控制可允許存取 Azure AD Connect Health 與其他使用者在組織中。 如需詳細資訊，請參閱 [Azure AD Connect Health 的角色型存取控制。](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**重要事項 ︰** 安裝代理程式時所使用的帳戶必須是工作或組織帳戶，而且不能是 Microsoft 帳戶。 如需詳細資訊，請參閱 [適用於 Azure 為組織註冊](sign-up-organization.md)|
|若是 AD FS，必須啟用 AD FS 稽核，才能使用使用情況分析| 如果計劃使用 AD FS 的使用情況分析，則您必須啟用 AD FS 稽核。  </br></br>請參閱 [安裝 Azure AD Connect Health AD fs 的代理程式。](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)
|了解 Azure AD Connect Health 代理程式需求|請參閱下表以取得代理程式的特定需求。

下表是開始使用 Azure AD Connect Health 之前必須符合的代理程式需求清單。

| 需求 | 說明|
| ----------- | ---------- |
|Azure AD Connect Health 代理程式安裝在每部目標伺服器上| Azure AD Connect Health 要求在目標伺服器上安裝代理程式，以提供在入口網站中檢視的資料。 </br></br>例如，若要在 AD FS 內部部署基礎結構取得資料，代理程式必須安裝 AD FS 伺服器上。  這包括 AD FS Proxy 伺服器和 Web 應用程式 Proxy 伺服器。   </br></br>如需安裝代理程式的詳細資訊，請參閱 [Azure AD Connect 健康情況代理程式安裝](active-directory-aadconnect-health-agent-install.md)。</br></br>**重要事項 ︰** 安裝代理程式時所使用的帳戶必須是工作或組織帳戶，而且不能是 Microsoft 帳戶。   如需詳細資訊，請參閱 [適用於 Azure 為組織註冊](sign-up-organization.md)|
|適用於同步處理的 Azure AD Connect Health 代理程式| 此代理程式會自動隨著最新版的 Azure AD Connect 一起安裝。  </br></br>如果您剛開始摸索然後您不需要採取任何動作。  當您安裝 Azure AD Connect 時，將會安裝代理程式。</br></br> 如果您已安裝 Azure AD Connect，您必須升級至最新版本下載 [這裡](http://www.microsoft.com/download/details.aspx?id=47594)。
|Azure 服務端點的輸出連線|在安裝期間和執行階段，代理程式需要連線至以下列出的 Azure AD Connect Health 服務端點。 如果您封鎖輸出連線，請確定在允許清單中加入下列內容： </br></br><li>**新**: https://management.azure.com </li><li>**新**: & #42;。windows.net </li><li>**新**: & #42;。queue.core.windows.net</li><li>& #42;。.servicebus.windows.net-連接埠 ︰ 5671</li><li>https://&#42;.adhybridhealth.azure.com/</li><li>https://&#42;.table.core.windows.net/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|在執行代理程式的伺服器上的防火牆連接埠。| 代理程式需要下列的防火牆連接埠，讓代理程式與 Azure AD Health 服務端點通訊中開啟。</br></br><li>TCP/UDP 連接埠 80</li><li>TCP/UDP 連接埠 443</li><li>TCP/UDP 連接埠 5671</li>
|如果啟用 IE 增強式安全性，則允許下列網站|如果在即將安裝代理程式的伺服器上啟用 IE 增強式安全性，則必須允許下列網站。</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>為您的組織的同盟伺服器受 Azure Active Directory 的範例 ︰ https://sts.contoso.com</li>

## 下載代理程式

若要開始使用，請執行下列其中一項：


- 若要開始使用 Azure AD Connect Health AD fs 中，您可以下載最新版本的代理程式 ︰  [下載 Azure AD Connect Health 代理程式的 AD FS。](http://go.microsoft.com/fwlink/?LinkID=518973) 請確定您已從 Marketplace 中加入服務，才可安裝代理程式。
- 若要開始使用適用於同步處理的 Azure AD Connect Health，請下載並安裝最新版的 Azure AD Connect。  健康狀態代理程式將會隨著 Azure AD Connect 安裝一起安裝。  Azure AD Connect 支援從舊版就地升級。


## 相關連結

* [Azure AD Connect Health 代理程式安裝](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [使用 Azure AD Connect Health 搭配 AD FS](active-directory-aadconnect-health-adfs.md)
* [使用適用於同步處理的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health 常見問題集](active-directory-aadconnect-health-faq.md)


