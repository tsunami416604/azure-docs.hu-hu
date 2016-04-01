<properties 
    pageTitle="教學課程：Azure Active Directory 與 Dropbox for Business 整合 | Microsoft Azure" 
    description="了解如何使用 Dropbox for Business 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 Dropbox for Business 整合
  
本教學課程的目的是要示範 Azure 與 Dropbox for Business 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Dropbox for Business 中的測試租用戶
  
完成本教學課程之後, 的 Azure AD 使用者您已指派給 Dropbox 商務才能夠使用單一登入應用程式在商務公司網站 （服務提供者起始登入），或使用 Dropbox [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Dropbox for Business 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario")



##啟用 Dropbox for Business 的應用程式整合
  
本節的目的是要說明如何啟用 Dropbox for Business 的應用程式整合。

###若要啟用 Dropbox for Business 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **dropbox 企業版**。

    ![應用程式庫](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Application gallery")

7.  在 [結果] 窗格中，選取 **dropbox 企業版**, ，然後按一下 [ **完成** 加入應用程式。

    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶向 Dropbox for Business 驗證。

在這個程序中，您需要上傳 Base-64 編碼憑證到您的 Dropbox for Business 租用戶。 如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **dropbox 企業版** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 dropbox 企業版** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，執行下列步驟 ︰

     3.1. 登入您的 Dropbox 企業租用戶。 <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configure single sign-on")

     3.2. 在左側導覽窗格中按一下 [ **系統管理員主控台**。 <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configure single sign-on")

     3.3. 在 **系統管理員主控台**, ，按一下 [ **驗證** 左的導覽窗格中。 <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configure single sign-on")

     3.4. 在 **單一登入** 區段中，選取 **啟用單一登入**, ，然後按一下 [ **詳細** 以展開此區段。  <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configure single sign-on")

     3.5. 複製 URL 旁邊 **使用者輸入其電子郵件地址，可以登入，或者他們可以直接移**。 <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configure single sign-on")

     3.6. 在 Azure 入口網站中，在 **dropbox 企業版登入** URL] 文字方塊中，貼上 URL。 <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configure single sign-on")  



4. 在 **在 dropbox 企業版設定單一登入** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。  <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configure single sign-on")


5. 在您的 Dropbox 企業租用戶中 **單一登入** 區段 **驗證** 頁面上，執行下列步驟 ︰ <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configure single sign-on")

     5.1. 按一下 [ **需要**。

     5.2. 在 Azure 網站中，在 **在 dropbox 企業版設定單一登入** 對話方塊頁面中，複製 **登入頁面 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。


     5.3. Create a **Base-64 encoded** file from your downloaded certificate. > [AZURE.TIP] For more details, see [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).


     5.4. Click **Choose certificate**, and then browse to your **base-64 encoded certificate file**.


     5.5. Click **Save changes** to complete the configuration on your DropBox for Business tenant.


6. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。 <br><br>  ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configure single sign-on")





##設定使用者佈建
  
本節的目的是要說明如何對 Dropbox for Business 啟用 Active Directory 使用者帳戶的使用者佈建。


### 若要設定使用者佈建，請執行下列步驟：

1. 在 Azure 管理入口網站中，在 **dropbox 企業版** 應用程式整合頁面上，按一下 [ **設定使用者佈建** 開啟 **設定使用者佈建** ] 對話方塊。

2. 在啟用使用者佈建至 DropBox 企業頁面，按一下 [啟用使用者佈建以開啟 [登入 Dropbox 以連結與 Azure AD] 對話方塊。  <br><br> ![使用者佈建](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "User provisioning")

3. 在 **登入 dropbox 以連結與 Azure AD** ] 對話方塊中，登入您的 Dropbox 企業租用戶。 <br><br> ![使用者佈建](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "User provisioning")



4. 按一下 [ **允許** 授與存取 Dropbox 的 Azure AD。 <br><br> ![使用者佈建](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "User provisioning")



5. 若要完成組態，請按一下 [ **完成** ] 按鈕。  <br><br> ![使用者佈建](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "User provisioning")




##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Dropbox for Business，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * dropbox 企業版 * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Yes")
  


請等候 10 分鐘並確認帳戶已同步至企業適用的 Dropbox。

第一個驗證步驟中，您可以檢查佈建狀態中，依序按一下 **儀表板** 中 **dropbox 企業版** Azure 管理入口網站上的應用程式整合頁面

<br><br>  ![指派使用者](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assign users")


成功完成的使用者佈建週期會以相關狀態表示。

<br><br>  ![指派使用者](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assign users")


如果要測試您的單一登入設定，請開啟存取面板。
如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

