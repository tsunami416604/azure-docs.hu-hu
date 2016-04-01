<properties
   pageTitle="教學課程：整合 NetSuite 與 Azure Active Directory | Microsoft Azure"
   description="了解如何使用 NetSuite 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
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
   ms.date="10/20/2015"
   ms.author="liviodlc"/>

#教學課程：如何整合 NetSuite 與 Azure Active Directory

本教學課程將示範如何將 NetSuite 環境與 Azure Active Directory (Azure AD) 連接。 您將學習如何設定單一登入 NetSuite、如何啟用自動的使用者佈建，以及如何指派使用者存取 NetSuite。 

##必要條件

1. 若要存取 Azure Active Directory 透過 [Azure 管理入口網站](https://manage.windowsazure.com), ，您必須先具備有效的 Azure 訂用帳戶。

2. 您必須具有系統管理員存取權 [NetSuite](http://www.netsuite.com/portal/home.shtml) 訂用帳戶。 您可以使用免費試用帳戶來使用任何服務。

##步驟 1：將 NetSuite 加入您的目錄

1. 在 [Azure 管理入口網站](https://manage.windowsazure.com), ，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![從左方的導覽窗格中選取 [Active Directory]。][0]

2. 從 **目錄** 清單中，選取您想要將 NetSuite 加入的目錄。

3. 按一下 [ **應用程式** 上方功能表中。

    ![按一下 [應用程式]。][1]

4. 按一下 [ **新增** 頁面的底部。

    ![按一下 [新增] 以加入新的應用程式。][2]

5. 在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![按一下 [從資源庫新增應用程式]。][3]

6. 在 **搜尋方塊**, ，型別 **NetSuite**。 然後選取 **NetSuite** 從結果中，然後按一下 **完成** 加入應用程式。

    ![新增 [NetSuite]。][4]

7. 您現在應該會看到 NetSuite 的 [快速入門] 頁面：

    ![Azure AD 中 NetSuite 的 [快速入門] 頁面][5]

##步驟 2：啟用單一登入

1. 在 Azure AD 中 NetSuite 中，[快速入門] 頁面上，按一下 [ **設定單一登入** ] 按鈕。

    ![[設定單一登入] 按鈕][6]

2. 此時會開啟一個對話方塊，您會看到一個畫面，詢問「您要讓使用者如何登入 NetSuite?」 選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![選取 [Azure AD 單一登入]][7]

    > [AZURE.NOTE] 若要深入了解有關不同單一登入的選項， [按一下這裡](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. 在 **設定應用程式設定** ] 頁面上，針對 **回覆 URL** 欄位中，輸入您 NetSuite 租用戶 url 使用下列格式之一 ︰
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![輸入您的租用戶 URL][8]

4. 在 **在 NetSuite 設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後將儲存在本機電腦上的憑證檔案。

    ![下載中繼資料。][9]

5. 在瀏覽器中開啟新索引標籤，以系統管理員的身分登入您的 Netsuite 公司站台。

6. 在頁面頂端的工具列中，按一下 [ **安裝**, ，然後按一下 [ **安裝管理員**。

    ![移至 [設定管理員]][10]

7. 從 **安裝工作** 清單中，選取 **整合**。

    ![移至 [整合]][11]

8. 在 **管理驗證** 區段中，按一下 **SAML 單一登入**。

    ![移至 [SAML 單一登入]][12]

9. 在 **SAML 設定** 頁面上，執行下列步驟 ︰

    - 在 Azure Active Directory 中，複製 **遠端登入 URL** 值，並將它貼到 **身分識別提供者登入頁面** 在 NetSuite 中的欄位。

        ![[SAML 設定] 頁面。][13]

    - 在 NetSuite 中，選取 **主要驗證方法**。

    - 欄位標示為 **SAMLV2 身分識別提供者中繼資料**, ，請選取 **上傳 IDP 中繼資料檔案**。 然後按一下 [ **瀏覽** 上傳您在步驟 #4 中下載的中繼資料檔案。

        ![上傳中繼資料][16]

    - 按一下 [ **提交**。

9. 在 Azure AD 中，選取單一登入組態確認核取方塊以啟用您上傳到 NetSuite 的憑證。 然後按一下 [ **下一步**。

    ![核取確認核取方塊][14]

10. 在對話方塊的最後一頁，如果您想要收到電子郵件通知與此單一登入組態相關的錯誤和警告，請輸入電子郵件地址。 

    ![輸入您的電子郵件地址。][15]

11. 按一下 [ **完成** 以關閉對話方塊。 接著，按一下 **屬性** 頁面的頂端。

    ![按一下 [屬性]。][17]

12. 按一下 [ **新增使用者屬性**。

    ![按一下 [新增使用者屬性]。][18]

13. 如 **屬性名稱** 欄位中，輸入 `account`。 如 **屬性值** 欄位中，輸入您的 NetSuite 帳戶識別碼。 如何找出帳戶識別碼的指示如下：

    ![新增您的 NetSuite 帳戶識別碼。][19]

    - 在 NetSuite 中，按一下 **安裝** 從頂端導覽功能表。
    - 然後按一下 [下 **安裝工作** 區段的左方瀏覽功能表上，選取 **整合** 區段，然後按一下 **Web 服務喜好設定**。
    - 複製您的 NetSuite 帳戶識別碼，並將它貼到 **屬性值** Azure AD 中的欄位。

        ![取得您的帳戶識別碼][20]

14. 在 Azure AD 中，按一下 [ **完成** 以完成新增 SAML 屬性。 然後按一下 [ **套用變更** 下方功能表上。

    ![儲存您的變更。][21]

15. 在使用者可以執行單一登入 NetSuite 之前，必須先在 NetSuite 中指派適當的權限。 請依照下列指示來指派這些權限。

    - 在上方導覽功能表上，按一下 [ **安裝**, ，然後按一下 [ **安裝管理員**。

        ![移至 [設定管理員]][10]

    - 在左的導覽功能表中，選取 **使用者/角色**, ，然後按一下 [ **管理角色**。

        ![請移至 [管理角色]][22]

    - 按一下 [ **新角色**。

    - 輸入 **名稱** 您新的角色，然後選取 **單一登入只** 核取方塊。

        ![命名新的角色。][23]

    - 按一下 [ **儲存**。

    - 在頂端功能表中，按一下 [ **權限**。 然後按一下 [ **安裝**。

        ![移至 [權限]][24]

    - 選取 **設定 SAM 單一登入**, ，然後按一下 [ **新增**。

    - 按一下 [ **儲存**。

    - 在上方導覽功能表上，按一下 [ **安裝**, ，然後按一下 [ **安裝管理員**。

        ![移至 [設定管理員]][10]

    - 在左的導覽功能表中，選取 **使用者/角色**, ，然後按一下 [ **管理使用者**。

        ![移至 [管理使用者]][25]

    - 選取測試使用者。 然後按一下 [ **編輯**。

        ![移至 [管理使用者]][26]

    - 在 [角色] 對話方塊中，選取角色，您已建立，並按一下 [ **新增**。

        ![移至 [管理使用者]][27]

    - 按一下 [ **儲存**。

19. 若要測試您的組態，請參閱下面的一節 [指派使用者至 NetSuite](#step-4-assign-users-to-netsuite)。

##步驟 3：啟用自動的使用者佈建

> [AZURE.NOTE] 根據預設，佈建的使用者將會加入根之子公司 NetSuite 環境。

1. 在 Azure Active Directory 中，在 NetSuite 中，快速入門] 頁面上按一下 **設定使用者佈建**。

    ![設定使用者佈建][28]

2. 在開啟的對話方塊中，輸入系統管理員認證在 NetSuite 中，然後按一下 [ **下一步**。

    ![輸入您的 NetSuite 系統管理員認證。][29]

3. 在確認頁面上，輸入您的電子郵件地址以接收佈建失敗的通知。

    ![確認。][30]

4. 按一下 [ **完成** 以關閉對話方塊。

##步驟 4：指派使用者至 NetSuite

1. 若要測試您的組態，請先在目錄中建立新的測試帳戶。

2. 在 NetSuite [快速入門] 頁面上，按一下 **指派使用者** ] 按鈕。

    ![按一下 [指派使用者]][31]

3. 選取測試使用者，然後按一下 [ **指派** 在畫面底部的按鈕 ︰

 - 如果還沒有啟用自動的使用者佈建，您會看到下列確認提示：

        ![Confirm the assignment.][32]

 - 如果已啟用自動的使用者佈建，您會看到提示，定義使用者在 NetSuite 中要具備何種角色。 幾分鐘之後，新佈建的使用者應該就會出現在 NetSuite 環境中。

4. 若要測試您的單一登入設定，請開啟 [存取面板] [https://myapps.microsoft.com](https://myapps.microsoft.com/), 、 登入測試帳戶，然後按一下 **NetSuite**。

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png


