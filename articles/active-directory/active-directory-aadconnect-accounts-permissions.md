<properties
   pageTitle="Azure AD Connect：帳戶與權限 | Microsoft Azure"
   description="本主題描述使用和建立的帳戶以及所需的權限。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/16/2015"
   ms.author="andkjell;billmath"/>


# Azure AD Connect 所需的帳戶和權限

Azure AD Connect 安裝精靈提供兩個不同的路徑：

- 在快速設定中，我們需要更多權限，以便輕鬆設定您的組態，而不需要您建立使用者或個別設定權限。

- 在自訂設定中，我們會提供您更多選擇和選項；但在某些情況下，您必須確定自己擁有正確的權限。

## 相關文件
如果您沒有讀取文件上 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md), 下, 表提供相關主題連結。

| 主題 |  |
| --------- | --------- |
| 使用快速設定進行安裝 | [快速安裝 Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| 使用自訂設定進行安裝 | [自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md) |
| 從 DirSync 升級 | [從 Azure AD 同步作業工具 (DirSync) 升級](active-directory-aadconnect-dirsync-upgrade-get-started.md) |


## 快速設定安裝
在快速設定中，安裝精靈會要求企業管理員認證，讓您可以設定內部部署 Active Directory，使其具備 Azure AD Connect 所需的權限。 如果是從 DirSync 升級，企業管理員認證可用來重設 DirSync 所使用的帳戶的密碼。

精靈頁面  | 收集的認證 | 所需的權限| 用於
------------- | ------------- |------------- |------------- |
N/A|執行安裝精靈的使用者| 本機伺服器的系統管理員| <li>建立本機帳戶做為使用 [同步處理引擎服務帳戶](#azure-ad-connect-sync-service-account)。
連接至 Azure AD| Azure AD 目錄認證 | Azure AD 中的全域管理員角色 | <li>啟用 Azure AD 目錄中的同步處理。</li>  <li>建立 [Azure AD 帳戶](#azure-ad-service-account) ，將用於持續同步處理作業在 Azure AD 中。</li>
連線到 AD DS | 內部部署 Active Directory 認證 | Active Directory 中 Enterprise Admins (EA) 群組成員| <li>建立 [帳戶](#active-directory-account) Active Directory 和授與權限。 這個建立帳戶用來讀取和寫入目錄同步處理期間的資訊。</li>

### 企業管理員認證
這些認證只能在安裝期間使用，而不能在安裝完成後使用。 它是企業管理員而不是網域管理員，以確定可以在所有網域中設定 Active Directory 中的權限。

### 全域管理員認證
這些認證只能在安裝期間使用，而不能在安裝完成後使用。 它用來建立 [Azure AD 帳戶](#azure-ad-service-account) 用於同步處理至 Azure AD 的變更。 帳戶也會在 Azure AD 中啟用同步做為一項功能。 使用的帳戶不能啟用 MFA。

## 自訂設定安裝
使用自訂設定時，必須在安裝之前建立用來連接到 Active Directory 的帳戶。

精靈頁面  | 收集的認證 | 所需的權限| 用於
------------- | ------------- |------------- |-------------
N/A|執行安裝精靈的使用者|<li>本機伺服器的系統管理員</li><li>如果使用完整的 SQL Server，使用者必須是系統管理員 (SA) 在 SQL 中</li>| 根據預設，會建立本機帳戶做為使用 [同步處理引擎服務帳戶](#azure-ad-connect-sync-service-account)。 當系統管理員不會指定特定帳戶，才會建立帳戶。
安裝同步處理服務，服務帳戶選項 | AD 或本機使用者帳戶認證 | 使用者權限會由安裝精靈授與|如果系統管理員指定帳戶，則此帳戶會做為同步處理服務帳戶。
連接至 Azure AD|Azure AD 目錄認證| Azure AD 中的全域管理員角色| <li>啟用 Azure AD 目錄中的同步處理。</li>  <li>建立 [Azure AD 帳戶](#azure-ad-service-account) ，將用於持續同步處理作業在 Azure AD 中。</li>
連接您的目錄|各個將連線至 Azure AD 之樹系的內部部署 Active Directory 認證 | 權限而定，功能啟用，並可在 [建立 AD DS 帳戶](#create-the-ad-ds-account) |這個帳戶是在同步處理期間用來讀取和寫入目錄資訊。
AD FS 伺服器|在清單的各個伺服器中，如果執行精靈之使用者的登入認證權限不足無法連線，精靈就會收集認證|網域系統管理員|安裝和設定 AD FS 伺服器角色。
Web 應用程式 Proxy 伺服器 |在清單的各個伺服器中，如果執行精靈之使用者的登入認證權限不足無法連線，精靈就會收集認證|目標電腦上的本機系統管理員|安裝和設定 WAP 伺服器角色。
Proxy 信任憑證 |Federation Service 信任認證 (Proxy 將用來註冊 FS 信任憑證的認證) |網域帳戶是 AD FS 伺服器的本機系統管理員|FS-WAP 信任憑證的首次註冊。
AD FS 服務帳戶頁面，「使用網域使用者帳戶選項」|AD 使用者帳戶認證|網域使用者|AD 使用者帳戶所提供的認證將做為 AD FS 服務的登入帳戶。

### 建立 AD DS 帳戶
當您安裝 Azure AD Connect 指定的帳戶 **連接您的目錄** 頁面必須存在於 Active Directory 和必要的權限授與。 安裝精靈將不會驗證權限，只會在同步處理期間發現問題。

您需要的權限取決於您啟用的選用功能。 如果您有多個網域，則必須對樹系中的所有網域授與權限。 如果您不要啟用任何一項功能預設 **網域使用者** 權限都已綽綽有餘。

| 功能 | 權限 |
| ------ | ------ |
| 密碼同步處理 | <li>複寫目錄變更</li>  <li>複寫目錄全部變更 |
| Exchange 混合式部署 | 記錄中的屬性寫入權限 [Exchange 混合式回寫](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) 使用者、 群組和連絡人。 |
| 密碼回寫 | 記錄中的屬性寫入權限 [開始使用密碼管理](active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) 的使用者。 |
| 裝置回寫 | 中所述的 PowerShell 指令碼授與權限 [裝置回寫](active-directory-aadconnect-get-started-custom-device-writeback.md)。|
| 群組回寫 | 讀取、建立、更新和刪除散發群組所在 OU 中的群組物件。|

## 升級
當您從一個版本的 Azure AD Connect 升級到新的版本時，您需要下列權限：

| 主體 | 所需的權限 | 用於 |
| ---- | ---- | ---- |
| 執行安裝精靈的使用者 | 本機伺服器的系統管理員 | 更新二進位檔案。 |
| 執行安裝精靈的使用者 | ADSyncAdmins 的成員 | 對同步處理規則和其他組態進行變更。 |
| 執行安裝精靈的使用者 | 如果使用同步處理引擎資料庫的完整 SQL Server: DBO (或類似選項) | 變更資料庫層級，例如更新含有新資料行的資料表。 |

## 已建立帳戶的相關資訊

### Active Directory 帳戶

如果您使用快速設定，那麼將在 Active Directory 中建立帳戶，該帳戶將用於同步處理。 將位於樹系根網域中的使用者容器中建立的帳戶，而且必須加上其名稱 **MSOL_**。 會使用不會過期的長複雜密碼建立帳戶。 如果在網域中有密碼原則，請確定允許這個帳戶使用長密碼和複雜密碼。

![AD 帳戶](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### Azure AD Connect 同步處理服務帳戶
安裝精靈會建立兩個本機服務帳戶 (除非您在自訂設定指定要使用的帳戶)。 做為前置詞的帳戶 **AAD_** 用於以執行實際的同步處理服務。 如果您在網域控制站上安裝 Azure AD Connect，則會在網域中建立帳戶。 如果您使用 SQL server 的遠端伺服器上， **AAD_** 服務帳戶必須位於網域中。 做為前置詞的帳戶 **AADSyncSched_** 用於排定的工作正在執行同步處理引擎。

![同步服務帳戶](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

會使用不會過期的長複雜密碼建立帳戶。

對於同步處理引擎服務帳戶，此帳戶將由 Windows 用來儲存加密金鑰，使得此帳戶的密碼不被重設或變更。

如果您使用完整的 SQL Server，那麼服務帳戶將會是為同步引擎建立的資料庫的 DBO。 使用其他權限，服務將無法如預期般運作。 也會建立 SQL 登入。

此帳戶也會獲授與檔案、登錄機碼及與其他同步引擎相關的物件權限。

### Azure AD 服務帳戶
將會在 Azure AD 中建立帳戶供同步服務使用。 此帳戶可以用它的顯示名稱來識別。

![AD 帳戶](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

使用帳戶所在伺服器的名稱可以透過使用者名稱的第二個部分來識別。 在上圖中，伺服器名稱是 FABRIKAMCON。 如果您有預備伺服器，則每個伺服器將會有自己的帳戶。 Azure AD 中有 10 個同步服務帳戶的限制。

會使用不會過期的長複雜密碼建立服務帳戶。 它會授與特殊角色 **目錄同步處理帳戶** 具有執行目錄同步處理工作的權限。 這個特殊的內建角色不能授與 Azure AD Connect 精靈以外，Azure 入口網站只會顯示此帳戶與角色 **使用者**。

![AD 帳戶角色](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## 後續步驟

深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。


