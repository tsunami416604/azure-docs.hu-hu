<properties
   pageTitle="教學課程：整合 Google Apps 與 Azure Active Directory | Microsoft Azure"
   description="了解如何使用 Google Apps 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/01/2015"
   ms.author="liviodlc"/>

#教學課程：如何整合 Google Apps 與 Azure Active Directory

本教學課程將示範如何將 Google Apps 環境與 Azure Active Directory (Azure AD) 連接。 您將學習如何設定單一登入 Google Apps、如何啟用自動的使用者佈建，以及如何指派使用者存取 Google Apps。 

##必要條件

1. 若要存取 Azure Active Directory 透過 [Azure 管理入口網站](https://manage.windowsazure.com), ，您必須先具備有效的 Azure 訂用帳戶。

2. 您必須擁有有效的租用戶的 [Google Apps for Work](https://www.google.com/work/apps/) 或 [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/)。 您可以使用免費試用帳戶來使用任何服務。

##影片教學課程

如何在 2 分鐘內啟用單一登入至 Google Apps：

> [AZURE.VIDEO enable-single-sign-on-to-google-apps-in-2-minutes-with-azure-ad]

##步驟 1：將 Google Apps 加入您的目錄

1. 在 [Azure 管理入口網站](https://manage.windowsazure.com), ，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![從左方的導覽窗格中選取 [Active Directory]。][0]

2. 從 **目錄** 清單中，選取您想要將 Google Apps 加入的目錄。

3. 按一下 [ **應用程式** 上方功能表中。

    ![按一下 [應用程式]。][1]

4. 按一下 [ **新增** 頁面的底部。

    ![按一下 [新增] 以加入新的應用程式。][2]

5. 在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![按一下 [從資源庫新增應用程式]。][3]

6. 在 **搜尋方塊**, ，型別 **Google Apps**。 然後選取 **Google Apps** 從結果中，然後按一下 **完成** 加入應用程式。

    ![新增 [Google Apps]。][4]

7. 您現在應該會看到 Google Apps 的 [快速入門] 頁面：

    ![Azure AD 中 Google Apps 的 [快速入門] 頁面][5]

##步驟 2：啟用單一登入

1. 在 Azure AD 中 Google Apps 的快速入門] 頁面上，按一下 [ **設定單一登入** ] 按鈕。

    ![[設定單一登入] 按鈕][6]

2. 此時會開啟一個對話方塊，您會看到一個畫面，詢問「您要讓使用者如何登入 Google Apps?」 選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![選取 [Azure AD 單一登入]][7]

    > [AZURE.NOTE] 若要深入了解有關不同單一登入的選項， [按一下這裡](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. 在 **設定應用程式設定** ] 頁面上，針對 **登入 URL** 欄位中，輸入您 Google Apps 租用戶 url 使用下列格式: `https://mail.google.com/a/<yourdomain>`

    ![輸入您的租用戶 URL][8]

4. 在 **自動設定單一登入** 頁面上，輸入網域中 Google Apps 租用戶。 然後按下 **設定** ] 按鈕。

    ![輸入您的網域名稱並按下 [設定]。](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)

    > [AZURE.NOTE] 如果您想要手動設定單一登入，請參閱 [選擇性步驟: 手動設定單一登入](#optional-step-manually-configure-single-sign-on)

5. 登入您的 Google Apps 管理員帳戶。 然後按一下 [ **允許** 若要允許 Azure Active Directory 中 Google 應用程式訂閱進行組態變更。

    ![輸入您的網域名稱並按下 [設定]。](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)

6. 在 Azure Active Directory 設定 Google Apps 租用戶時請稍候幾秒的時間。 當它完成時，按一下 [ **下一步**。

10. 在對話方塊的最後一頁，如果您想要收到電子郵件通知與此單一登入組態相關的錯誤和警告，請輸入電子郵件地址。

    ![輸入您的電子郵件地址。][14]

11. 按一下 [ **完成** 以關閉對話方塊。 若要測試您的組態，請參閱下面的一節 [指派使用者至 Google Apps](#step-4-assign-users-to-google-apps)。

##選擇性步驟：手動設定單一登入

如果您想要手動設定單一登入，請完成下列步驟：

1. 在 Azure AD 中 Google Apps 的快速入門] 頁面上，按一下 [ **設定單一登入** ] 按鈕。

    ![[設定單一登入] 按鈕][6]

2. 此時會開啟一個對話方塊，您會看到一個畫面，詢問「您要讓使用者如何登入 Google Apps?」 選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![選取 [Azure AD 單一登入]][7]

    > [AZURE.NOTE] 若要深入了解有關不同單一登入的選項， [按一下這裡](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. 在 **設定應用程式設定** ] 頁面上，針對 **登入 URL** 欄位中，輸入您 Google Apps 租用戶 url 使用下列格式: `https://mail.google.com/a/<yourdomain>`

    ![輸入您的租用戶 URL][8]

4. 在 **自動設定單一登入** 頁面上，選取核取方塊 **手動設定此應用程式進行單一登入**。 然後按一下 [ **下一步**。

    ![按一下 [手動組態]。](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)

4. 在 **在 Google Apps 設定單一登入** 頁面上，按一下 **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![下載憑證。][9]

5. 在瀏覽器中開啟新索引標籤，然後登入 [Google Apps 管理控制台](http://admin.google.com/) 使用系統管理員帳戶。

6. 按一下 [ **安全性**。 如果您沒有看到連結，它可能隱藏在 **其他控制項** 在畫面底部的功能表。

    ![按一下 [安全性]。][10]

7. 在 **安全性** 頁面上，按一下 **設定單一登入 (SSO)。**

    ![按一下 [SSO]。][11]

8. 執行下列組態變更：

    ![設定 SSO][12]

    - 選取 **協力廠商身分識別提供者設定 SSO**。

    - 在 Azure AD 中，複製 **單一登入服務 URL**, ，並將它貼到 **登入頁面 URL** Google Apps 中的欄位。

    - 在 Azure AD 中，複製 **單一登出服務 URL**, ，並將它貼到 **登出頁面 URL** Google Apps 中的欄位。

    - 在 Azure AD 中，複製 **變更密碼 URL**, ，並將它貼到 **變更密碼 URL** Google Apps 中的欄位。

    - 在 Google Apps 中的 **驗證憑證**, 上, 傳您在步驟 #4 中下載的憑證。

    - 按一下 [ **儲存變更**。

9. 在 Azure AD 中，選取單一登入組態確認核取方塊以啟用您上傳到 Google Apps 的憑證。 然後按一下 [ **下一步**。

    ![核取確認核取方塊][13]

10. 在對話方塊的最後一頁，如果您想要收到電子郵件通知與此單一登入組態相關的錯誤和警告，請輸入電子郵件地址。 

    ![輸入您的電子郵件地址。][14]

11. 按一下 [ **完成** 以關閉對話方塊。 若要測試您的組態，請參閱下面的一節 [指派使用者至 Google Apps](#step-4-assign-users-to-google-apps)。

##步驟 3：啟用自動的使用者佈建

> [AZURE.NOTE] 自動化使用者佈建至 Google Apps 另一個可行的選項是使用 [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) 的佈建至 Google Apps 將內部部署 Active Directory 身分識別。 但是，本教學課程中的解決方案則會將您的 Azure Active Directory (雲端) 使用者和啟用郵件功能的群組佈建至 Google Apps。

1. 登入 [Google Apps 管理控制台](http://admin.google.com/) 使用您的系統管理員帳戶，然後按一下 **安全性**。 如果您沒有看到連結，它可能隱藏在 **其他控制項** 在畫面底部的功能表。

    ![按一下 [安全性]。][10]

2. 在 **安全性** 頁面上，按一下 **API 參考**。

    ![按一下 [API 參考]。][15]

3. 選取 **啟用 API 存取**。

    ![按一下 [API 參考]。][16]

    > [AZURE.IMPORTANT] 針對每個您想要佈建至 Google 應用程式，其 Azure Active Directory 中的使用者名稱的使用者 *必須* 繫結至自訂網域。 例如，使用者名稱看起來像是 Google Apps 中，將不會接受 bob@contoso.onmicrosoft.com 而 bob@contoso.com 會被接受。 您可以在 Azure AD 中編輯現有使用者的內容，來變更其網域。 以下將說明如何設定 Azure Active Directory 與 Google Apps 兩者的自訂網域。

4. 如果您還沒將自訂網域名稱加入 Azure Active directory，請執行下列步驟：

    - 在 [Azure 管理入口網站](https://manage.windowsazure.com), ，在左的導覽窗格中，按一下 [ **Active Directory**。 在目錄清單中，選取您的目錄。 

    - 按一下 [ **網域** 從最上層功能表，然後按一下 **新增自訂網域**。

        ![新增自訂網域][17]

    - 輸入您的網域名稱 **網域名稱** 欄位。 這個網域名稱必須與您要用於 Google Apps 的網域名稱相同。 準備好時，按一下 [ **新增** ] 按鈕。

        ![輸入您的網域名稱。][18]

    - 按一下 [ **下一步** 移至 [驗證] 頁面。 若要確認您擁有此網域，您必須根據此頁面提供的值，編輯網域的 DNS 記錄。 您也可以確認使用 **MX 記錄** 或 **TXT 記錄**, ，取決於您選取的 **記錄類型** 選項。 如需有關如何驗證與 Azure AD 網域名稱的更完整指示，請參閱 [您自己的網域名稱新增至 Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)。

        ![驗證您的網域名稱。][19]

    - 對於所有您想要加入目錄的網域重複上述步驟。

5. 您已經向 Azure AD 驗證所有網域，但是您還必須向 Google Apps 再驗證一次。 對於尚未向 Google Apps 註冊的每個網域，執行下列步驟：

    - 在 [Google Apps 管理控制台](http://admin.google.com/), ，按一下 [ **網域**。

        ![按一下 [網域]][20]

    - 按一下 [ **加入網域或網域別名**。

        ![新增網域][21]

    - 選取 **加入其他網域**, ，並輸入您想要加入的網域名稱。

        ![輸入您的網域名稱][22]

    - 按一下 [ **繼續並驗證網域擁有**。 然後依照步驟以驗證您擁有網域名稱。 如需有關如何驗證您的 Google Apps 網域的完整指示，請參閱 [確認站台擁有權與 Google Apps](https://support.google.com/webmasters/answer/35179)。

    - 對於您想要加入 Google Apps 的任何其他網域重複上述步驟。

    > [AZURE.WARNING] 如果您在 Google Apps 租用戶，變更主要網域，而且如果已經設定單一登入與 Azure AD，則您必須重複執行步驟 #3 下方 [步驟 2: 啟用單一登入](#step-two-enable-single-sign-on)。

6. 在 [Google Apps 管理控制台](http://admin.google.com/), ，按一下 [ **系統管理員角色**。

    ![按一下 [Google Apps]][26]

7. 決定您想要用來管理使用者佈建的管理帳戶。 如 **admin 角色** 的該帳戶，請編輯 **權限** 該角色。 請確定它具有所有 **管理 API 權限** 啟用，此帳戶可以用於佈建。

    ![按一下 [Google Apps]][27]

    > [AZURE.NOTE] 如果您要設定生產環境，最佳作法是特別針對此步驟，在 Google Apps 中建立新的系統管理員帳戶。 這些帳戶必須具有與其相關聯的管理角色，具備必要的 API 權限。

8. 在 Azure Active Directory 中，按一下 **應用程式** 中最上層功能表，然後按一下 **Google Apps**。

    ![按一下 [Google Apps]][23]

9. 在 Google Apps 的快速入門頁面，按一下 **設定使用者佈建**。

    ![設定使用者佈建][24]

10. 在 [開啟] 對話方塊中，按一下 **啟用使用者佈建** 入您想要用來管理佈建的 Google 應用程式系統管理員帳戶進行驗證。

    ![啟用佈建][25]

11. 確認您想要授與 Azure Active Directory 權限來變更您的 Google Apps 租用戶。

    ![確認權限。][28]

12. 按一下 [ **完成** 以關閉對話方塊。

##步驟 4：指派使用者至 Google Apps

1. 若要測試您的組態，請先在目錄中建立新的測試帳戶。

2. 在 Google Apps [快速入門] 頁面上，按一下 **指派使用者** ] 按鈕。

    ![按一下 [指派使用者]][29]

3. 選取測試使用者，然後按一下 [ **指派** 在畫面底部的按鈕:

 - 如果還沒有啟用自動的使用者佈建，您會看到下列確認提示：

        ![Confirm the assignment.][30]

 - 如果已啟用自動的使用者佈建，您會看到提示，定義使用者在 Google Apps 中要具備何種角色。 幾分鐘之後，新佈建的使用者應該就會出現在 Google Apps 環境中。

4. 若要測試您的單一登入設定，請開啟 [存取面板] [https://myapps.microsoft.com](https://myapps.microsoft.com/), 、 登入測試帳戶，然後按一下 **Google Apps**。

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png

