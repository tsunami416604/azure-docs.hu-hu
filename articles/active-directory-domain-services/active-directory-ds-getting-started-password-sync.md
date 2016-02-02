<properties
    pageTitle="Azure Active Directory 網域服務預覽：開始使用 | Microsoft Azure"
    description="開始使用 Azure Active Directory 網域服務"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="udayh"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/09/2015"
    ms.author="maheshu"/>


# Azure AD 網域服務 (預覽)** - 開始使用

## 步驟 5：啟用密碼同步處理

一旦您已針對 Azure AD 租用戶啟用 Azure AD 網域服務之後，下一個步驟就是啟用密碼的同步處理。 這讓使用者能夠使用他們的公司認證來登入網域。

所需的步驟會根據組織是僅限雲端，或已設定為使用 Azure AD Connect 來同步處理內部部署目錄而有所不同。

### 僅限雲端的租用戶 - 在 Azure AD 中啟用 NTLM 和 Kerberos 認證雜湊產生功能

如果您的組織是僅限雲端的 Azure AD 租用戶，需要使用 Azure AD 網域服務的使用者就必須變更其密碼。 這個步驟的結果就是在 Azure AD 中產生 Azure AD 網域服務進行 Kerberos 和 NTLM 驗證所需的認證雜湊。 您可能會過期的需要使用 Azure AD 網域服務，或指示變更密碼，這些使用者的租用戶中的所有使用者的密碼。

以下是您需要提供給使用者，以變更其密碼的指示:

1. 瀏覽至組織的 [Azure AD 存取面板] 頁面。 這是通常位於 [http://myapps.microsoft.com](http://myapps.microsoft.com)。
2. 選取此頁面上的 [設定檔]**** 索引標籤。
3. 按一下此頁面上的 [變更密碼]**** 圖格來初始密碼變更。

    ![建立適用於 Azure AD 網域服務的虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. 這會顯示 [變更密碼]**** 頁面。 使用者可以輸入現有的 (舊) 密碼，然後繼續進行變更其密碼。

    ![建立適用於 Azure AD 網域服務的虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

使用者變更其密碼之後，新的密碼能夠用在 Azure AD 網域服務很快。 幾分鐘之後，使用者就能使用最近變更的密碼，來登入已加入受管理網域的電腦。


### 同步處理的租用戶 - 能夠將 NTLM 和 Kerberos 認證雜湊同步處理到 Azure AD

如果貴組織的 Azure AD 租用戶已設定為使用Azure AD Connect 來同步處理內部部署目錄，則您必須設定 Azure AD Connect，來同步處理 NTLM 和 Kerberos 驗證所需的認證雜湊。 這些雜湊預設不會同步處理到 Azure AD，而下列步驟可讓您將雜湊同步處理到 Azure AD 租用戶。

#### 安裝或更新 Azure AD Connect

您必須在已加入網域的電腦上安裝最新的 Azure AD Connect 建議的版本。 如果您目前已設定 Azure AD Connect 的執行個體，就必須更新它來使用 Azure AD Connect GA 組建。 請確定您使用的是目前的 Azure AD Connect 版本，以避免已知的問題/bug。

* *[下載 Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)* *

最低建議版本：**1.0.9125** - 已於 2015 年 11 月 3 日發行。
  > [AZURE.WARNING] 若要讓舊的密碼認證 (NTLM 和 Kerberos 驗證所需) 同步處理至 Azure AD 租用戶，您必須安裝最新的 Azure AD Connect 建議的版本。 無法在舊版本的 Azure AD Connect 或舊版的目錄同步工具中使用這項功能。

注意：您不再需要利用最新的 Azure AD Connect 版本 (亦即 1.0.9125 和更新版本) 來建立 'EnableWindowsLegacyCredentialsSync' 登錄機碼。

Azure AD Connect 的安裝指示可用於下列文件- [開始使用 Azure AD Connect](../active-directory/active-directory-aadconnect.md)


#### 強制執行與 Azure AD 的完整密碼同步處理

若要強制執行完整密碼同步處理並啟用所有的內部部署使用者的密碼雜湊 (包括 NTLM/Kerberos 驗證所需的認證雜湊) 以同步處理到您的 Azure AD 租用戶，請在每個 AD 樹系上執行下列 PowerShell 指令碼。

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

視您的目錄大小而定 (數字的使用者，群組等)，同步處理至 Azure AD 認證需要時間。 將認證雜湊同步處理到 Azure AD 之後，密碼短時間內就能在 Azure AD 網域服務管理的網域上使用。





