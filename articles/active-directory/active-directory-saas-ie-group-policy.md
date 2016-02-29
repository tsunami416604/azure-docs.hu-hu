<properties
   pageTitle="如何使用群組原則部署 Internet Explorer 的存取面板延伸模組 | Microsoft Azure"
   description="如何使用群組原則針對我的 app 入口網站部署 Internet Explorer 附加元件。"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="stevenpo"
   editor=""/>
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/18/2015"
   ms.author="liviodlc"/>

#如何使用群組原則部署 Internet Explorer 的存取面板延伸模組

本教學課程示範如何使用群組原則，在您的使用者電腦上遠端安裝 Internet Explorer 的存取面板延伸模組。 此延伸模組都需要 Internet Explorer 使用者登入已設定使用的應用程式需要 [密碼型單一登入](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)。

我們建議系統管理員自動化部署這個延伸模組。 否則，使用者必須自行下載並安裝延伸模組，這樣很容易發生使用者錯誤，而且需要系統管理員權限。 本教學課程涵蓋使用群組原則自動化軟體部署的一種方法。 [進一步了解群組原則。](https://technet.microsoft.com/windowsserver/bb310732.aspx)

存取面板延伸模組也會提供如 [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) 和 [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), ，這兩者需要系統管理員權限，才能安裝。

##先決條件

- 您已設定 [Active Directory 網域服務](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), ，和使用者的電腦加入網域。
- 您必須擁有 [編輯設定] 權限，才能編輯群組原則物件 (GPO)。 根據預設，下列安全性群組的成員擁有此權限：網域系統管理員、企業系統管理員及群組原則建立者擁有者。 [深入了解。](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##步驟 1：建立發佈點

首先，您必須將安裝程式套件放在網路位置上，該位置可以從您要在上面遠端安裝延伸模組的所有電腦存取。 若要這樣做，請遵循下列步驟：

1. 以系統管理員身分登入伺服器

2. 在 **伺服器管理員** ] 視窗中，移至 **檔案和存放服務**。

    ![開啟檔案和存放服務](./media/active-directory-saas-ie-group-policy/files-services.png)

3. 移至 **共用** ] 索引標籤。 然後按一下 [ **工作** > **新共用...**

    ![開啟檔案和存放服務](./media/active-directory-saas-ie-group-policy/shares.png)

4. 完成 **新增共用精靈** 及設定權限，以確保它可以在從使用者的機器進行存取。 [進一步了解共用。](https://technet.microsoft.com/library/cc753175.aspx)

5. 下載下列 Microsoft Windows Installer 套件 (.msi 檔案): [存取面板 Extension.msi] (https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access 面板 Extension.msi)

6. 將安裝程式套件複製到共用上想要的位置。

    ![將 .msi 檔案複製到您的共用。](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. 確認用戶端電腦能夠從共用存取安裝程式套件。 

##步驟 2：建立群組原則物件

1. 登入裝載 Active Directory 網域服務 (AD DS) 安裝的伺服器。

2. 在 [伺服器管理員，請移至 **工具** > **群組原則管理**。

    ![移至工具 > 群組原則管理](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. 在左窗格中 **群組原則管理** ] 視窗中，檢視您的組織單位 (OU) 階層，並判斷您想要套用群組原則的範圍。 例如，您可能決定針對測試挑選小型 OU 以部署到少數使用者，或者您可能會挑選最上層 OU 以部署到整個組織。

    > [AZURE.NOTE] 如果您想要建立或編輯您的組織單位 (Ou)，切換回 [伺服器管理員並移至 **工具** > **Active Directory 使用者和電腦**。

4. 一旦您選取的 OU，以滑鼠右鍵按一下它，然後選取 **在這個網域中建立 GPO 並連結到...**

    ![建立新的 GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. 在 **新的 GPO** 提示字元下輸入新的群組原則物件的名稱。

    ![命名新的 GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. 以滑鼠右鍵按一下您剛才建立的群組原則物件上，選取 **編輯**。

    ![編輯新的 GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##步驟 3：指派安裝套件

1. 決定您想要部署為基礎的延伸 **電腦設定** 或 **使用者設定**。 當使用 [電腦設定](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), ，不論哪一個使用者登入電腦上，將會安裝延伸模組。 相反地，與 [使用者設定](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), ，使用者必須安裝，無論哪一台電腦在其登入的延伸模組。

2. 在左窗格中 **群組原則管理編輯器** ] 視窗中，移至下列資料夾路徑，視哪種類型的組態選擇其中一項:
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. 以滑鼠右鍵按一下 **軟體安裝**, ，然後選取 **新增** > **封裝...**

    ![建立新的軟體安裝套件](./media/active-directory-saas-ie-group-policy/new-package.png)

4. 移至共用資料夾，其中包含從安裝程式套件 [步驟 1: 建立發佈點](#step-1-create-the-distribution-point), ，選取.msi 檔案，然後按一下 **開啟**。

    > [AZURE.IMPORTANT] 如果共用位於相同的伺服器上，確認您要透過網路檔案路徑，而不是本機檔案路徑存取此.msi。

    ![從共用資料夾中選取安裝套件。](./media/active-directory-saas-ie-group-policy/select-package.png)

5. 在 **部署軟體** 提示，請選取 **指派** 部署方法。 然後按一下 [ **確定**。

    ![選取 [已指派]，然後按一下 [確定]。](./media/active-directory-saas-ie-group-policy/deployment-method.png)

延伸模組現在已部署至您所選取的 OU。 [進一步了解群組原則軟體安裝。](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##步驟 4：自動啟用 Internet Explorer 的延伸模組 

除了執行安裝程式之外，Internet Explorer 的每個延伸模組必須明確啟用才能使用。 遵循下列步驟以使用群組原則啟用存取面板延伸模組：

1. 在 **群組原則管理編輯器** ] 視窗中，移至下列路徑，視哪種類型的組態中選擇其中一項 [步驟 3: 指定安裝套件](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. 以滑鼠右鍵按一下 **附加元件清單**, ，然後選取 **編輯**。
    ![編輯附加元件清單。](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. 在 **附加元件清單** 視窗中，選取 **啟用**。 然後，在 **選項** 區段中，按一下 **顯示...**。

    ![按一下 [啟用]，然後按一下 [顯示...]](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. 在 **顯示內容** ] 視窗中，執行下列步驟:

    1. 第一個資料行 ( **值名稱** 欄位)、 複製及貼上下列類別識別碼: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. 第二個資料行 ( **值** 欄位)，輸入下列值: `1`

    3. 按一下 [ **確定** 關閉 **顯示內容** 視窗。

    ![填寫值，如上述步驟所指定。](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. 按一下 [ **確定** 以套用變更並關閉 **附加元件清單** 視窗。

延伸模組現在應該已在所選 OU 中的機器啟用。 [進一步瞭解使用群組原則來啟用或停用 Internet Explorer 附加元件。](https://technet.microsoft.com/library/dn454941.aspx)

##步驟 5 (選用)：停用 [記住密碼] 提示

當使用者登入使用存取面板擴充功能的網站時，Internet Explorer 可能會顯示下列提示詢問「是否要儲存密碼？」

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

如果不想使用者看到此提示，請依照下列步驟防止自動完成記住密碼：

1. 在 **群組原則管理編輯器** ] 視窗中，移至下面所列的路徑。 請注意，此設定僅可在 **使用者設定**。
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. 尋找名為設定 **開啟自動完成功能的使用者名稱和密碼，在表單上的**。

    > [AZURE.NOTE] 舊版的 Active Directory 可能會列出這項設定名稱 **不允許儲存密碼自動完成**。 該設定的組態不同於本教學課程中所描述的設定。

    ![記得要在 [使用者設定] 下尋找此項目。](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. 上述的設定，請以滑鼠右鍵按一下，然後選取 **編輯**。

4. 在視窗中標題為 **開啟自動完成功能的使用者名稱和密碼，在表單上的**, ，請選取 **已停用**。

    ![選取 [停用]](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. 按一下 [ **確定** 來套用這些變更並關閉視窗。

使用者不能再繼續儲存其認證，或使用自動完成來存取之前儲存的認證。 不過，這項原則允許使用者對其他類型的表單欄位可繼續使用自動完成，例如搜尋欄位。

> [AZURE.WARNING] 如果之後使用者已選擇儲存某些認證，此原則將會啟用此原則 *不* 清除已儲存的認證。

##步驟 6：測試部署

請遵循下列步驟以確認延伸模組是否成功部署：

1. 如果您部署使用 **電腦設定**, ，登入用戶端電腦所屬的組織單位，您在選取登入 [步驟 2: 建立群組原則物件](#step-2-create-the-group-policy-object)。 如果您部署使用 **使用者設定**, ，請確定屬於該 OU 的使用者身分登入。

2. 可能要登入好幾次才能讓群組原則變更完全更新至此電腦。 若要強制更新，請開啟 **命令提示字元** 視窗，然後執行下列命令: `gpupdate /force`

3. 您必須重新啟動電腦才能進行安裝。 安裝延伸模組時，開機可能需要比平常更多的時間。

4. 重新啟動之後，開啟 **Internet Explorer**。 在視窗的右上角，按一下 [ **工具** (齒輪圖示)，然後選取 **管理附加元件**。

    ![移至工具 > 管理附加元件](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. 在 **管理附加元件** ] 視窗中，確認 **存取面板延伸模組** 已安裝且其 **狀態** 已設定為 **啟用**。

    ![確認存取面板延伸模組已安裝並啟用。](./media/active-directory-saas-ie-group-policy/verify-install.png)

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]
