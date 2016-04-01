<properties
    pageTitle="在 Eclipse 中啟用 Azure 部署的遠端存取"
    description="了解如何使用適用於 Eclipse 的 Azure 工具組來啟用 Azure 部署的遠端存取。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# 在 Eclipse 中啟用 Azure 部署的遠端存取 #

為協助疑難排解您的部署，您可以啟用並使用「遠端存取」以連線到裝載您的部署的虛擬機器。 遠端存取功能需要遠端桌面通訊協定 (RDP)。 您可以先將您的部署發行到 Azure 之後再設定遠端存取，或如果您使用 Eclipse 與 Windows 作業系統，則可先設定遠端存取，再將它發行到 Azure。 請注意，要連線到您在 Azure 的部署的虛擬機器，您需要相容於您的作業系統的遠端桌面用戶端。

## 如何在部署到 Azure 之前先啟用遠端存取 ##

>[AZURE.NOTE] 若要部署應用程式到 Azure 之前，請啟用遠端存取，您需要在 Windows 上執行 Eclipse。

下圖顯示 **遠端存取** 用來啟用遠端存取的 [內容] 對話方塊。

![][ic719494]

有兩種方式來顯示 **遠端存取** 屬性] 對話方塊 ︰

* 按一下 [ **進階** 中連結 **遠端存取** 區段 **Publish to Azure** ] 對話方塊。
* 開啟 **屬性** 的 Azure 專案] 對話方塊。

當您建立新的 Azure 部署專案時，專案預設不會啟用遠端存取。 不過，您可以輕鬆地啟用遠端存取所指定的使用者名稱和密碼在 **Publish to Azure** ] 對話方塊。 遠端存取密碼使用 X.509 憑證加密。 如果您未使用它來提供您自己的憑證，加密是依賴隨附於 Azure Plugin for Eclipse 的自我簽署憑證。 這個自我簽署的憑證是在 **cert** Azure 專案，同時公開憑證檔案 (SampleRemoteAccessPublic.cer) 和個人資訊交換 (PFX) 憑證檔案 (SampleRemoteAccessPrivate.pfx) 儲存的資料夾。 後者包含憑證的私密金鑰，而且具有預設密碼 **Password1**。 不過，由於此密碼是公開資訊，預設的憑證應僅供教學使用，不適用於實際執行部署。 因此以外的學習的目的，當您想要啟用遠端工作階段為您的部署，您應該按一下 **進階** 中連結 **Publish to Azure** ] 對話方塊來指定您自己的憑證。 請注意，您必須將憑證的 PFX 版本上傳至 Azure 管理入口網站中您裝載的服務，這樣 Azure 才可以解密使用者密碼。

其餘的教學課程示範如何為一開始建立時停用遠端存取的 Azure 部署專案啟用遠端存取。 基於本教學課程的目的，我們將建立一個新的自我簽署憑證，而其 .pfx 檔案將包含您選擇的密碼。 您也可以選擇使用憑證授權單位所核發的憑證。

## 如何在部署到 Azure 之後啟用遠端存取 ##

若要在部署到 Azure 之後啟用遠端存取，請使用下列步驟：

1. 使用您的 Azure 帳戶登入 Azure 管理入口網站
1. 清單中的 **雲端服務**, ，選取您已部署的雲端服務
1. 在雲端服務 web 頁面上，按一下 [ **設定** 連結
1. 在 [組態] 頁面底部，按一下 [ **遠端** 連結
1. 當快顯對話方塊出現時：
    1. 指定您想要為其啟用遠端存取的角色
    1. 按一下以選取 **啟用遠端桌面** 核取方塊
    1. 指定您想要用於遠端存取的使用者名稱和密碼
    1. 選取要使用的憑證
1. 按一下 [ **[確定]** 

您會看到訊息指出您的組態變更正在進行，可能需要幾分鐘才能完成。 完成組態變更之後，請依照下列中的步驟 **從遠端登入** 本文稍後的章節。
    
## 如何在您的封裝中啟用遠端存取 ##

* 在 Eclipse 的專案總管] 窗格中，以滑鼠右鍵按一下 Azure 專案，然後按一下 [ **屬性**。
* 在 **屬性** ] 對話方塊中，展開 **Azure** 左窗格中按一下 **遠端存取**。
* 在 **遠端存取** ] 對話方塊中，確定 **啟用所有的角色，以接受遠端桌面連線與這些登入認證** 已核取。
* 指定遠端桌面連線的使用者名稱。
* 指定並確認使用者的密碼。 當您建立遠端桌面連線時，將使用在此對話方塊中設定的使用者名稱和密碼值。 (請注意，這個密碼不同於您的 PFX 密碼)。
* 指定使用者帳戶的到期日。
* 按一下 [ **新增** 來建立新的自我簽署的憑證。 (或者，您可以選取憑證，從您的工作區或檔案系統，透過 **工作區** 或 **FileSystem** 按鈕，分別，但基於本教學課程中，我們將建立新的憑證。)
* 在 **新憑證** ] 對話方塊中，指定並確認您將使用 PFX 檔案的密碼。
* 接受提供的值 **名稱 (CN)**, ，或使用自訂的名稱。
* 指定儲存新憑證的路徑和檔案名稱 (.cer 格式)。 如需此步驟和下一步，您可以使用 **cert** 資料夾的 Azure 專案，但您也可以選擇另一個位置。 基於本教學課程的目的，我們將使用 **c:\mycert\mycert.cer**。 (建立 **c:\mycert** 資料夾再繼續作業或使用現有的資料夾，如有需要。)
* 指定儲存新憑證和其私密金鑰的路徑和檔案名稱 (私密金鑰為 .pfx 格式)。 基於本教學課程的目的，我們將使用 **c:\mycert\mycert.pfx**。 您 **新憑證** 對話方塊看起來應該類似如下 (如果您未使用更新的資料夾路徑， **c:\mycert**):
    ![][ic712275]
* 按一下 [ **確定** 關閉 **新憑證** ] 對話方塊。
* 您 **遠端存取** 對話方塊應該看起來如下 ︰</p>
    ![][ic719495]
* 按一下 [ **確定** 關閉 **遠端存取** ] 對話方塊。
    
使用為部署到雲端所設定的組建，重新建立您的應用程式。

## 從遠端登入 ##

您的角色執行個體準備就緒後，您可以從遠端登入裝載您的應用程式的虛擬機器。

* 如果使用 Eclipse 在 Windows 和您所選上 **啟動遠端桌面部署** 選項在您部署至 Azure，您會向遠端桌面連線登入畫面時啟動您的部署。 當系統提示您輸入使用者名稱和密碼時，輸入您為遠端使用者指定的值就能登入。
* 從遠端登入的另一種方式是透過 <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Azure 管理入口網站</a>：
    * 內 **雲端服務** 檢視 Azure 管理入口網站，按一下 [雲端服務，請按一下 **執行個體**, ，按一下 [特定的執行個體，然後按 **連接** ] 按鈕。  **連接** ] 按鈕會出現在命令列中包含下列 ︰
    ![][ic659273]  
    >[AZURE.NOTE] 如果您是在非 Windows 作業系統上，您要使用遠端桌面用戶端與作業系統相容，並依照步驟來設定該用戶端下載的 RDP 檔案中的設定。
    * 之後按一下 **連接** 按鈕，將會提示您開啟 RDP 檔案。 開啟檔案，並依照提示執行。 (您可以也將此檔案儲存到您的本機電腦，然後按兩下執行該檔案以遠端登入您的虛擬機器，而不需要先前往管理入口網站。)
    * 當系統提示您輸入使用者名稱和密碼，輸入您為遠端使用者指定的值，並將能夠登入。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[安裝 Azure Toolkit for Eclipse][] 

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

