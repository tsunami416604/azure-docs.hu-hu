<properties
   pageTitle="用於管理應用程式的文件庫 |Microsoft Azure"
   description="Azure Active Directory 應用程式管理主題，包含作法、疑難排解和常見問題集的技術參考連結"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/03/2015"
   ms.author="kgremban"/>

# 用於管理應用程式的文件庫

此頁面包含透過 Azure Active Directory 單一登入的主要功能的實用連結。 連結細分為下列幾個類別：

- **概觀 ︰** 功能，主題介紹與使用案例的概觀。
- **How to:** 詳細指示，以便開始使用特定案例。
- **疑難排解 ︰** 診斷常見的問題的秘訣。
- **常見問題集 ︰** 解答一般問題和考量。  

## Cloud App Discovery

|   |   |
| ------ | ------ |
| 概觀 | [如何探索組織內使用未經批准的雲端應用程式](active-directory-cloudappdiscovery-whatis.md) |
| 作法 | [開始使用 Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br><br> [雲端應用程式探索的安全性和隱私權考量](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) <br><br> [Cloud App Discovery 群組原則部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) <br><br> [雲端應用程式探索 System Center 部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) <br><br> [雲端應用程式探索 proxy 服務的登錄設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| 常見問題集 | [Cloud App Discovery—常見問題集](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |

## 應用程式指派

|   |   |
| ------ | ------ |
| 概觀 | [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md) |
| 作法 | [建立簡單的規則，設定群組的動態成員資格](active-directory-accessmanagement-simplerulegroup.md) <br><br> [使用群組管理 SaaS 應用程式的存取權](active-directory-accessmanagement-group-saasapps.md) <br><br> [設定自助式服務應用程式存取管理的 Azure AD](active-directory-accessmanagement-self-service-group-management.md) <br><br> [管理群組的擁有者](active-directory-accessmanagement-managing-group-owners.md) <br><br> [在 Azure Active Directory 中管理安全性群組](active-directory-accessmanagement-manage-groups.md) <br><br> [Azure Active Directory 中的專用群組](active-directory-accessmanagement-dedicated-groups.md) <br><br> [使用屬性來建立進階規則](active-directory-accessmanagement-groups-with-advanced-rules.md) <br><br> [使用 Azure Active Directory 自動進行 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md) <br><br> [如何使用 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md) |

## 同盟單一登入

|   |   |
| ------ | ------ |
| 概觀 |[設定與 AD FS 同盟](active-directory-aadconnect-get-started-custom.md)
| 作法 | [DirSync 與單一登入](https://msdn.microsoft.com/library/azure/dn441213.aspx) <br><br> [Microsoft Azure 現在支援 Windows Server Active Directory 的同盟](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/) <br><br> [如何使用 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md) <br><br> [Active Directory 市集](https://azure.microsoft.com/marketplace/active-directory/) |
| 疑難排解 | [疑難排解 Azure AD 目錄同步工具設定精靈](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [如何疑難排解 Azure Active Directory 同步作業工具的安裝和組態精靈錯誤](https://support.microsoft.com/kb/2684395) <br><br> [疑難排解目錄同步作業](https://msdn.microsoft.com/library/azure/jj151787.aspx) <br><br> [疑難排解單一登入](https://msdn.microsoft.com/library/azure/jj151834.aspx) |
| 常見問題集 | [管理同盟單一登入 Azure Active Directory 中的憑證](active-directory-sso-certs.md) <br><br> [Azure Active Directory Connect 常見問題集](active-directory-aadconnect-faq.md) |

## 密碼單一登入

|   |   |
| ------ | ------ |
| 作法 | [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md) <br><br> [DirSync 搭配密碼同步化](https://msdn.microsoft.com/library/azure/dn441214.aspx) |
| 疑難排解 | [疑難排解 Azure AD 目錄同步工具設定精靈](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [如何疑難排解 Azure Active Directory 同步作業工具的安裝和組態精靈錯誤](https://support.microsoft.com/kb/2684395) <br><br> [疑難排解目錄同步作業](https://msdn.microsoft.com/library/azure/jj151787.aspx) |
| 常見問題集 | [Azure Active Directory Connect 常見問題集](active-directory-aadconnect-faq.md) |

## 豐富的應用程式佈建

|   |   |
| ------ | ------ |
| 概觀 | [使用 Azure Active Directory 自動進行 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md) |
| 作法 | [教學課程：Azure Active Directory 與 Concur 整合](active-directory-saas-concur-tutorial.md) <br><br> [教學課程：Azure Active Directory 與 DocuSign 整合](active-directory-saas-docussign-tutorial.md) <br><br> [教學課程：Azure Active Directory 與 Dropbox for Business 整合](active-directory-saas-dropboxforbusiness-tutorial.md) <br><br> [教學課程：如何整合 Google Apps 與 Azure Active Directory](active-directory-saas-google-apps-tutorial.md) <br><br> [教學課程：如何整合 Salesforce 與 Azure Active Directory](active-directory-saas-salesforce-tutorial.md) <br><br> [教學課程：Azure Active Directory 與 ServiceNow 整合](active-directory-saas-servicenow-tutorial.md) <br><br> [教學課程 ︰ 設定 Workday 輸入同步處理](active-directory-saas-workday-inbound-tutorial.md) |

## Azure AD 應用程式資源庫

|   |   |
| ------ | ------ |
| 作法 | [在 Azure Active Directory 應用程式庫中列出您的應用程式](active-directory-app-gallery-listing.md) <br><br> [使用 Azure AD 自助式 SAML 組態「自備應用程式」](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |

## 自訂應用程式

|   |   |
| ------ | ------ |
| 概觀 | [使用 Azure AD 自助式 SAML 組態「自備應用程式」](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |
| 作法 | [教學課程：如何整合 Salesforce 與 Azure Active Directory](active-directory-saas-salesforce-tutorial.md) |

## 應用程式 Proxy

|   |   |
| ------ | ------ |
| 概觀 | [如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md) |
| 作法 | [啟用 Azure AD 應用程式 Proxy](active-directory-application-proxy-enable.md) <br><br> [使用 Azure AD 應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md) <br><br> [使用條件式存取](active-directory-application-proxy-conditional-access.md) <br><br> [使用 Azure AD 應用程式 Proxy 中的自訂網域](active-directory-application-proxy-custom-domains.md) <br><br> [在應用程式 Proxy 中使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md) <br><br> [單一登入應用程式 proxy](active-directory-application-proxy-sso-using-kcd.md) |
| 疑難排解 | [疑難排解應用程式 Proxy](active-directory-application-proxy-troubleshoot.md) |

## 應用程式啟動經驗

|   |   |
| ------ | ------ |
| 概觀 | [存取面板簡介](active-directory-saas-access-panel-introduction.md) |
| 作法 | [Azure Active Directory 存取面板 EMS](http://blogs.msdn.com/b/haddy_el-haggan_blog/archive/2015/04/02/azure-active-directory-access-panel-ems.aspx) <br><br> [與 Azure Active Directory 整合](active-directory-how-to-integrate.md) |


