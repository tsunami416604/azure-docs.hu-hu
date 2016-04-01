<properties 
    pageTitle="透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能| Microsoft Azure" 
    description="提供 Windows 10 裝置如何利用 Azure AD Join 在 Azure Active Directory 上登錄的詳細概觀。" 
    services="active-directory" 
    documentationCenter="" 
    authors="femila" 
    manager="stevenpo" 
    editor=""
    tags="azure-classic-portal"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="femila"/>

# 透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能

## 什麼是 Azure Active Directory Join 
Azure Active Directory (Azure AD) Join 是一項功能，可在 Azure Active Directory 中登錄公司擁有的裝置來啟用集中式管理。 這表示使用者 (員工、學生) 現在可以透過 Azure Active Directory 連線到企業雲端。 這樣能夠簡化 Windows 部署，從任何 Windows 裝置 (公司擁有或個人擁有的裝置 (BYOD)) 存取組織 App 和資源。 

Azure AD Join 的目標對象是雲端優先/僅限雲端的企業 (通常是不具內部部署 Active Directory 基礎結構的中小型企業。但是，Azure AD Join 可以且也可在無法進行傳統網域加入的裝置上供大型組織 (例如行動裝置) 或適用於主要需存取 Office 365 或其他 Azure AD SaaS App 的使用者使用。 

儘管傳統的網域加入仍可讓您在能夠進行網域加入的裝置上取得最佳內部部署經驗，但是，Azure AD Join 適用於無法進行網域加入的裝置以及您想要使用 MDM 功能來管理雲端使用者的區域，而不是使用群組管理和 SCCM 的傳統網域管理經驗。 

![](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## 為什麼企業應該採用 Azure AD Join 

* **如果您的企業主要位於雲端**︰ 如果您已經或即將移至模型，其中會減少內部足跡，而想要操作多個定域機組中時，Azure AD join 對您有助益。 或許您已經手動或透過同步處理內部部署 AD 的方式來建立 Azure AD 帳戶。 無論如何，您在 Azure AD 中已有一個帳戶且可用來登入 Windows 10。 您的使用者可以透過 OOBE 程序或設定經驗，將他們的電腦加入 Azure AD。 您的使用者現在可以在瀏覽器或 Office 應用程式中，享有其雲端資源 (例如 Office 365) 的 SSO 存取權。 
* **教育機構**︰ 我們聽到的大案例的其中一個是教育機構有兩種使用者類型 ︰ 教職員和學生。 教職成員會被視為組織中較長期的成員，因此需要為他們建立內部部署帳戶。 但學生是組織中期限較短的成員，因而可在 Azure AD 中進行管理，如此一來，就能將目錄範圍推送至雲端，而不是內部部署。 這些學生現在可以使用其 Azure AD 帳戶登入 Windows，並在 Office 應用程式中取得 Office 365 資源的存取權。 
* **零售業**︰ 我們聽到客戶的另一個領域是他們的設計讓您更容易管理季節性工作者。  再次提醒，您可以將較長期的全職員工建立為內部部署帳戶，而他們通常會使用已加入網域的電腦。 但季節性工作者是組織中期限較短的成員，因而需要在可更輕易四處移動使用者授權的地方加以管理。 在具有 Office 365 授權的雲端中建立這些使用者，讓這些使用者能夠因為使用 Azure AD 帳戶登入 Windows 和 Office 應用程式而獲益，同時在他們離開公司之後對於他們的授權保有更多機動性。 
* **其他企業**︰ 而比這些特定的情況下，您可能會發現即使您內部部署中維護使用者 AD 目錄中，您仍然有利於讓使用者在 Azure AD join 時因為簡化的加入經驗，在 Azure AD 中自動執行 MDM 註冊，裝置管理和單一登入 Azure AD 和內部資源。  

## Azure AD Join 功能
使用 Azure AD Join，您可以： 

* **自我佈建公司擁有的裝置**︰ 使用 Windows 10，使用者可以在全新的經驗中，而不需要 IT 人員的介入設定全新的拆封授權裝置。


* **支援現代化板型規格**: Azure AD Join 將在沒有傳統網域加入功能的裝置上運作。  


* **使用現有的組織帳戶**︰ 使用者不再需要建立和維護個人的 Microsoft 帳戶，才能在公司配給的裝置，就像在 Windows 8 上獲得最佳的體驗。 他們可以改為在 Azure AD 中使用現有的工作帳戶。 對許多組織而言，這基本上表示使用可用來存取 Office 365 的相同認證來設定和登入 Windows。 


* **自動執行 MDM 註冊**︰ 裝置可以自動註冊管理時連接到 Azure AD 中。 這將與 Microsoft Intune 及協力廠商 MDM 一起運作。 使用 Intune 進行管理時，IT 將能在 SCCM 管理主控台中，一併監視/管理具備 Azure AD Join 功能的裝置以及已加入網域的裝置。


* **單一登入公司資源**︰ 單一登入從 Windows 桌面應用程式和資源定域機組，例如 Office 365 和數千個依賴 Azure AD 進行驗證，透過商務應用程式的使用者，享有 [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md)。 公司擁有的裝置加入 Azure AD 會也喜歡 SSO 內部部署資源時在裝置上公司網路，以及從任何地方時透過公開這些資源 [Azure AD 應用程式 Proxy](https://msdn.microsoft.com/library/azure/Dn768219.aspx)。 


* **OS 狀態漫遊**︰ 像是協助工具設定、 網站及 Wi-fi 密碼會與同步公司擁有的裝置不需要個人的 Microsoft 帳戶。


* **企業使用的 Windows 市集**: Windows 市集將支援應用程式取得與使用 Azure AD 帳戶授權。 組織將能取得大量授權的 App，並讓其組織中的使用者可以使用。

## 不同的裝置如何與 Azure AD Join 搭配運作

| 公司裝置 (已加入內部部署網域)                                                                                                                                                                                         | 公司裝置 （已加入至雲端）                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | 個人裝置                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 使用者使用工作認證登入 Windows (就像他們現在所做的動作)                                                                                                                                                                        | 使用者可以使用 Azure AD 中管理的工作認證來登入 Windows。 這是三個案例中的公司裝置相關 ︰ 組織沒有 AD 在內部部署 （例如，小型企業） 組織並不會建立 AD （例如學生、 顧問、 季節性工作者） 無法加入 （內部） 網域，例如電話或平板電腦執行 Mobile SKU 的公司裝置中的所有使用者帳戶。 例如，攜帶前往工廠/零售現場的第二個裝置，適用於受管理和同盟組織的工作。 | 使用者使用其個人 MSA 認證登入 Windows (沒有改變)                                                |
| 使用者可以存取漫遊設定和 Windows 市集 – 這些服務可以使用工作帳戶來運作 (不需要個人 MSA)；要求組織將其內部部署 AD 連線到 Azure AD                                        | 自助式設定 – 使用者可以透過他們的工作帳戶完整取得初次執行體驗 (FRX)，這可為佈建裝置的 IT 提供另一種方法 – 這兩種方法均受到支援。                                                                                                                                                                                                                                                                                                                                                                             | 非常輕易地新增要在 AD 或 Azure AD 中管理的工作帳戶                                                      |
| 從桌面到工作 App/網站/資源的 SSO，在使用 Azure AD 進行驗證的內部部署和 cloudApps 中                                                                                                            | 在企業目錄中自動登錄 (Azure AD) 以及在 MDM 中自動註冊。 (Azure AD Premium 功能)                                                                                                                                                                                                                                                                                                                                                                                                                                                  | 使用此工作帳戶跨 App 提供 SSO 且登入網站/資源                                              |
| 使用者可以新增其個人 MSA 來存取個人的圖片/檔案，而不會影響到企業資料 (漫遊設定會持續使用工作帳戶來運作)。MSA 帳戶會啟用 SSO，且不再啟用裝置漫遊設定。  | Winlogon 上的自助式密碼重設 (SSPR，能夠重設忘記的密碼) (您需要 AzureAD Premium 才能執行此動作)                                                                                                                                                                                                                                                                                                                                                                                                                                    | 提供企業市集的存取權，讓使用者可以在他們的個人裝置上取得並使用 Lob 應用程式。 |                                                               |


## 其他資訊
* [適合企業使用的 Windows 10：使用裝置的工作方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [透過 Microsoft Passport 不需要密碼就能驗證身分識別](active-directory-azureadjoin-passport.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)



