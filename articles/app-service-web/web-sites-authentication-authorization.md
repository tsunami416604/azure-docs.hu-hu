<properties 
    pageTitle="在 Azure 應用程式服務中使用 Active Directory 進行驗證" 
    description="了解部署至 Azure App Service Web Apps 之企業營運應用程式的不同驗證和授權選項" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="12/10/2015" 
    ms.author="cephalin"/>

# 在 Azure 應用程式服務中使用 Active Directory 進行驗證 #

[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 可藉由支援使用者單一登入 (SSO) 的使用者從您的內部部署環境或公用網際網路存取應用程式可讓企業特定業務應用程式案例。 它可以整合 [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) (AAD) 或在內部安全權杖服務 (STS)，例如 Active Directory Federation Services (AD FS) 來驗證您的內部 Active Directory (AD) 使用者並適當地授權。

## 零阻力的驗證與授權 ##

只要按幾下按鈕，就能啟用 Web 應用程式的驗證和授權機制。 每個 Azure Web 應用程式中的核取方塊樣式設定均可提供企業營運 Web 應用程式的基本存取控制。 它是在授與使用者您的 Web 應用程式內容的存取權之前，藉由對您選擇的 Azure AD 租用戶強制執行 HTTPS 和驗證，來達到存取控制的目的。 如需詳細資訊，請參閱 [Web 應用程式驗證 / 授權](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/)。

>[AZURE.NOTE] 這項功能目前為預覽狀態。

## 手動實作驗證與授權 ##

在許多情況下，您會想要自訂應用程式的驗證和授權行為，例如登入和登出頁面、自訂授權邏輯及多重租用戶應用程式行為等。 在這些情況下，以手動方式設定驗證和授權以更靈活地使用功能可能是較佳的選擇。 以下是兩個主要選項：  

-   [Azure AD](web-sites-dotnet-lob-application-azure-ad.md) -您可以使用 Azure AD web 應用程式實作驗證和授權。 使用 Azure AD 作為身分識別提供者具有下列特性：
    -   支援普遍的驗證通訊協定，例如 [OAuth 2.0](http://oauth.net/2/), ，[OpenID Connect](http://openid.net/connect/), ，和 [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0)。 支援的通訊協定的完整清單，請參閱 [Azure Active Directory 驗證通訊協定](http://msdn.microsoft.com/library/azure/dn151124.aspx)。
    -   可以使用僅 Azure 的識別提供者，不需要任何內部部署基礎結構。
    -   也可以設定與內部部署 AD (受管理的內部部署) 同步目錄。
    -   當 AD 使用者經由內部網路和網際網路進行存取時，從內部部署的 AD 網域進行目錄同步的 Azure AD 可為您的 Web 應用程式提供流暢的 SSO 體驗。 經由內部網路時，AD 使用者可透過整合式驗證自動存取 Web 應用程式。 經由網際網路時，AD 使用者可使用其 Windows 認證登入 Web 應用程式。
    -   提供 SSO 至 [Azure AD 支援的所有應用程式](/marketplace/active-directory/), ，包括 Azure、 Office 365、 Dynamics CRM Online、 Microsoft Intune 及數千種非 Microsoft 的雲端應用程式。 
    -   Azure AD 的管理工作委派 [信賴憑證者的合作對象](http://en.wikipedia.org/wiki/Relying_party) 應用程式，以非系統管理員角色，但機密目錄資料的應用程式存取仍必須由全域系統管理員設定。
    -   傳送一組一般用途的宣告類型供所有信賴憑證者應用程式使用。 如需宣告類型清單，請參閱 [支援的權杖和宣告類型](http://msdn.microsoft.com/library/azure/dn195587.aspx)。 宣告無法自訂。
    -   [Azure AD Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx) 可讓 Azure AD 中的應用程式存取目錄資料。
-   [內部部署安全權杖服務 (STS)，例如 AD FS](../web-sites-dotnet-lob-application-adfs/) -您可以為 web 應用程式實作驗證和授權，與 AD FS 像內部部署 STS。 使用內部部署 AD FS 具有下列特性：
    -   AD FS 拓撲必須在內部部署，需要成本和管理費用。
    -   公司政策要求 AD 資料儲存在內部時最佳。
    -   只有 AD FS 系統管理員可以設定 [信賴憑證者信任和宣告規則](http://technet.microsoft.com/library/dd807108.aspx)。
    -   可以管理 [宣告](http://technet.microsoft.com/library/ee913571.aspx) 針對每個應用程式。
    -   必須有透過公司防火牆存取內部部署 AD 資料的獨立解決方案。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
 

