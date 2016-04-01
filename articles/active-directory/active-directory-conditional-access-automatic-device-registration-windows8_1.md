<properties
    pageTitle="為加入網域的 Windows 8.1 裝置設定自動註冊裝置 | Microsoft Azure"
    description=" 設定群組原則將加入網域的 Windows 8.1 裝置自動向 Azure AD 註冊的步驟。 "
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

# 為加入網域的 Windows 8.1 裝置設定自動註冊裝置

您可以使用 Active Directory 群組原則，設定加入網域的 Windows 8.1 裝置自動向 Azure AD 註冊。 若要設定群組原則，您必須至少有一台已加入網域且已安裝群組原則管理功能的 Windows Server 2012 R2 或 Windows 8.1 電腦。一旦您的網域啟用這個群組原則，任何登入電腦的網域使用者都會自動且無訊息地向 Azure AD 中的裝置物件註冊。 在 Azure AD 中，實體裝置的每一個已註冊的使用者都有一個裝置物件。請務必詳細閱讀並完成「自動向 Azure Active Directory 註冊加入網域的 Windows 裝置」中的必要條件。

## 設定加入網域的 Windows 8.1 裝置的群組原則

1. 開啟 [伺服器管理員，並瀏覽至 **工具** > **群組原則管理**。
2. 從群組原則管理] 瀏覽至 [網域] 節點，對應到網域中您想要啟用 **自動地點**。
3. 以滑鼠右鍵按一下 **群組原則物件** ，然後選取 **新增**。 指定群組原則物件的名稱，例如 **自動地點**。 按一下 [ **確定**。
4. 新的群組原則物件上按一下滑鼠右鍵，然後選取 **編輯**。
5. 瀏覽至 **電腦設定** > **原則** > **系統管理範本** > **Windows 元件** > **地點**。
6. 以滑鼠右鍵按一下自動加入工作地點的用戶端電腦，然後選取 **編輯**。
7. 選取 [啟用] 選項按鈕，然後按一下 [套用]。 按一下 [ **確定**。
8. 您現在可以將群組原則物件連結到您所選擇的位置。 若要對組織中所有加入網域的 Windows 8.1 裝置啟用此原則，請將群組原則連結到網域。

## 取消註冊加入網域的 Windows 8.1 裝置

您可以選擇取消註冊您加入網域的 Windows 8.1 裝置，執行下列步驟 ︰
修改在上一節中建立的工作地點加入的群組原則設定。 將 [自動將用戶端電腦加入工作場所] 原則設定為 [停用]。 這會防止新的裝置自動加入工作場所。

遵循下列兩個選項的其中一個，取消註冊現有加入網域的 Windows 8.1 電腦：

* 選項 1: **取消註冊 Windows 8.1 網域加入裝置的電腦設定**
  1. 在 Windows 8.1 裝置上，瀏覽至 **電腦設定** > **網路** > **工作場所**
  2. 選取 **保留**。
必須對每個已登入電腦且已自動加入工作場所的網域使用者重複此程序。

* 選項 2：使用指令碼取消註冊加入網域的 Windows 8.1 裝置
    1. 開啟 Windows 8.1 機器上的命令提示字元並執行下列命令 ︰
   ` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
此命令必須在已登入電腦的每個網域使用者的環境下執行。
事件檢視器和加入網域的 Windows 8.1 裝置的錯誤

Windows 8.1 電腦上的 Windows 事件記錄檔會顯示與裝置註冊相關的訊息。 您會找到成功和失敗事件的訊息。 事件記錄檔可以在事件檢視器找到 [應用程式及服務 **記錄** > **Microsoft** > **Windows > 工作地點加入**。

##其他詳細資料

群組原則會在系統啟用排定的工作，此工作是在使用者的情境脈絡下執行，並於使用者登入時觸發。 登入完成之後，此工作會以無訊息方式將向 Azure AD 註冊使用者和裝置。 已排程工作，請參閱 Windows 8.1 裝置中進行的工作排程器程式庫 **Microsoft** > **Windows** > **地點**。 此工作會執行並註冊任何和所有登入的 Active Directory 使用者。 

## 其他主題
- [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)
- [自動向 Azure Active Directory 註冊加入網域的 Windows 裝置](active-directory-conditional-access-automatic-device-registration.md)
- [為加入網域的 Windows 7 裝置設定自動註冊裝置](active-directory-conditional-access-automatic-device-registration-windows7.md)



