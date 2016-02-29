<properties
    pageTitle="自動向 Azure Active Directory 註冊加入網域的 Windows 裝置 | Microsoft Azure"
    description="IT 管理員可以選擇以自動和無訊息方式向 Azure Active Directory (Azure AD) 註冊加入網域的 Windows 裝置。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/24/2015"
    ms.author="femila"/>

# 自動向 Azure Active Directory 註冊加入網域的 Windows 裝置

身為 IT 管理員，您可以選擇以自動和無訊息方式向 Azure Active Directory (Azure AD) 註冊加入網域的 Windows 裝置。 如果您已經設定裝置型條件式存取原則給 Office365 應用程式或由 AD FS 在內部部署管理的應用程式，這非常有用。 您可以進一步了解裝置註冊狀況所讀取 [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)。

「自動向 Azure Active Directory 註冊裝置」適用於已加入 Active Directory 網域的 Windows 7 和 Windows 8.1 電腦。 這些通常是公司所擁有已提供給資訊工作者的電腦。

若要開始向 Azure AD 註冊已加入網域的 Windows 裝置，請遵循下列必要條件。 一旦您完成必要條件，請為加入網域的 Windows 裝置設定自動註冊裝置。

## 自動向 Azure Active Directory 註冊加入網域的 Windows 裝置的必要條件

使用 Azure Active Directory Connect 部署 AD FS 和連接到 Azure Active Directory
----------------------------------------------------------------------------------------------
1. 使用 Azure Active Directory Connect 將 Active Directory Federation Services (AD FS) 部署到 Windows Server 2012 R2，並設定與 Azure Active Directory 的同盟關係。
2. 設定其他 Azure Active Directory 信賴憑證者信任宣告規則。
3. 開啟 [AD FS 管理主控台並瀏覽至 **AD FS**>**信任關係 > 信賴憑證者信任**。 Microsoft Office 365 識別平台信賴憑證者信任物件上按一下滑鼠右鍵，然後選取 **編輯宣告規則...**
4. 在 **發佈轉換規則** 索引標籤上，選取 **新增規則**。
5. 選取 **使用自訂規則傳送宣告** 從 **宣告規則** 範本下拉式方塊。 選取 **下一步**。
6. 型別 *驗證方法宣告規則* 中 **宣告規則名稱:** 文字方塊。
7. 輸入下列宣告規則中的 **宣告規則:** 文字方塊:

        c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
        => issue(claim = c);
    
8. 按一下 [ **確定** 兩次以完成對話方塊。

設定其他 Azure Active Directory 信賴憑證者信任驗證類別參考
-----------------------------------------------------------------------------------------------------
在您的同盟伺服器上，開啟 Windows PowerShell 命令視窗並輸入：


  `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`
   
Where <RPObjectName> 為您的 Azure Active Directory 的信賴憑證者合作對象物件名稱信賴憑證者信任物件。 此物件通常命名為「Microsoft Office 365 身分識別平台」。

AD FS 全域驗證原則
-----------------------------------------------------------------------------
設定 AD FS 全域主要驗證原則以允許內部網路使用 Windows 整合式驗證 (這是預設值)。


Internet Explorer 設定
------------------------------------------------------------------------------
在您的 Windows 裝置的 Internet Explorer 中，對「近端內部網路」安全性區域進行下列設定：

- 只有一個憑證時，不提示用戶端憑證選取:  **啟用**
- 允許指令碼:  **啟用**
- 只在近端內部網路區域自動登入:  **檢查**

這些是 Internet Explorer 近端內部網路安全性區域的預設設定。 您可以檢視或管理這些設定，在 Internet Explorer 中的瀏覽至 **網際網路選項** > **安全性** > 近端內部網路 > 自訂層級。 您也可以使用 Active Directory 群組原則進行這些設定。

網路連線
-------------------------------------------------------------
加入網域的 Windows 裝置必須連線到 AD FS 與 Active Directory 網域控制站，才能自動向 Azure AD 註冊。 這通常表示電腦必須連接到公司網路。 這可能包括有線連線、Wi-Fi 連線、DirectAccess 或 VPN。

## 設定 Azure Active Directory 裝置註冊探索
Windows 7 和 Windows 8.1 裝置會藉由結合使用者帳戶名稱與知名裝置註冊伺服器名稱，探索裝置註冊伺服器。 您必須建立 DNS CNAME 記錄，該記錄指向與您的 Azure Active Directory 裝置註冊服務相關聯的 A 記錄。 CNAME 記錄必須使用已知的前置詞 **enterpriseregistration** 後面接著您組織的使用者帳戶所用的 UPN 尾碼。 如果您的組織使用多個 UPN 尾碼，則必須在 DNS 中建立多個 CNAME 記錄。

例如，如果您在貴組織使用兩個名為 @contoso.com 和 @region.contoso.com 的 UPN 尾碼，您將建立下列 DNS 記錄。

| 項目                                     | 類型  | 位址                            |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com        | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

##為加入網域的 Windows 7 和 Windows 8.1 裝置設定自動註冊裝置

使用下列連結為加入網域的 Windows 7 和 Windows 8.1 裝置設定自動註冊裝置。 繼續之前，請確定您已完成上述必要條件。

* [為加入網域的 Windows 8.1 裝置設定自動註冊裝置](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

* [為加入網域的 Windows 7 裝置設定自動註冊裝置](active-directory-conditional-access-automatic-device-registration-windows7.md)

其他注意事項
--------------------------------------------------------------------

向 Azure AD 註冊裝置可讓裝置發揮全面的功能。 Azure AD 裝置註冊可讓您註冊個人 (BYOD) 行動裝置與公司所擁有已加入網域的裝置。 裝置可以搭配託管服務一起使用，例如 Office365 和由 AD FS 在內部部署管理的服務。 

如果公司使用行動裝置和傳統裝置，或使用 Office365、Azure AD 或其他 Microsoft 服務，則應該使用 Azure AD 裝置註冊服務在 Azure AD 中註冊裝置。如果您的公司不使用行動裝置，也不使用任何 Microsoft 服務，例如 Office365、Azure AD 或 Microsoft Intune，而只是裝載內部部署應用程式，您可以選擇使用 AD FS 在 Active Directory 中註冊裝置。 

您可以進一步了解部署搭配 AD FS 的裝置註冊 [這裡](https://technet.microsoft.com/library/dn486831.aspx)。

## 其他主題

- [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)
- [為加入網域的 Windows 7 裝置設定自動註冊裝置](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [為加入網域的 Windows 8.1 裝置設定自動註冊裝置](active-directory-conditional-access-automatic-device-registration-windows8_1.md)
