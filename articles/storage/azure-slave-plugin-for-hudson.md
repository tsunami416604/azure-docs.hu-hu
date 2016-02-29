<properties
    pageTitle="如何搭配使用 Azure 從屬外掛程式與 Hudson 連續整合"
    description="說明如何搭配使用 Azure 從屬外掛程式與 Hudson 連續整合。"
    services="storage" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor="jimbe" />

<tags
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="v-dedomi"/>

#如何搭配使用 Azure 從屬外掛程式與 Hudson 連續整合

適用於 Hudson 的 Azure 從屬外掛程式可讓您在執行分散式組建時在 Azure 上佈建從屬節點。

## 安裝 Azure 從屬外掛程式
1. 在 Hudson 儀表板中，按一下 [ **Manage Hudson**。
2. 在 **Manage Hudson** 頁面上，按一下 **管理外掛程式**。
3. 按一下 [ **可用** ] 索引標籤。
4. 按一下 [ **搜尋** 和型別 **Azure** ，讓清單只顯示相關外掛程式。

    如果您選擇捲動以查看可用的外掛程式清單，您會發現下的 Azure 從屬外掛程式 **叢集管理和分散式組建** 一節中 **其他** ] 索引標籤。
     
5. 選取核取方塊， **Azure 從屬外掛程式**。
6. 按一下 [ **安裝**。
7. 重新啟動 Hudson。

現在您已安裝外掛程式，接下來的步驟是在 Azure 訂用帳戶設定檔中設定外掛程式，以及建立用來建立從屬節點 VM 的範本。


## 在 Azure 訂用帳戶設定檔中設定 Azure 從屬外掛程式

訂用帳戶設定檔也稱為發佈設定，它是一個 XML 檔案，內含要在開發環境中使用 Azure 所需具備的安全認證和一些額外資訊。 若要設定 Azure 從屬外掛程式，您需要： 

* 訂用帳戶識別碼
* 訂用帳戶的管理憑證

您可以在訂用帳戶設定檔中找到這些資訊。 如果您沒有訂用帳戶設定檔的複本，您可以下載從 [這裡](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0)。 以下是訂用帳戶設定檔的範例。

    <?xml version="1.0" encoding="utf-8"?>
        <PublishData>
        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">
        <Subscription
            ServiceManagementUrl="https://management.core.windows.net"
            Id="<Subscription ID>"
            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />
        </PublishProfile>
    </Publis有了訂用帳戶設定檔後，請遵循下列步驟來設定 Azure 從屬外掛程式。 plug在 Hudson 儀表板中，按一下 [ **Manage Hudson**。udso按一下 [ **設定系統**。yste若要尋找頁面上向下的捲動 **定域機組** 一節。sect按一下 [ **新增雲端 > Microsoft Azure**。Azure**.新增雲端 new cloud](./media/azure-slave-plugin-for-hudson/hudson-setup-addcloud.此動作會顯示一些欄位，您必須在其中輸入訂用帳戶的詳細資料。n detail設定設定檔re profile](./media/azure-slave-plugin-for-hudson/hudson-setup-configureprofile從訂用帳戶設定檔複製訂用帳戶識別碼和管理憑證，並貼至適當欄位。te fie在複製訂用帳戶識別碼和管理憑證時， **不** 括住值的引號也包括。he values按一下 [ **驗證組態**。urat當成功驗證組態後時，按一下 [ **儲存**。 **Sa設定 Azure 從屬外掛程式的虛擬機器範本av虛擬機器範本會定義外掛程式用來在 Azure 上建立從屬節點的參數。 在下列步驟中，我們將會建立 Ubuntu VM 的範本。Ubuntu在 Hudson 儀表板中，按一下 [ **Manage Hudson**。 Hud按一下 [ **設定系統**。 Sys若要尋找頁面上向下的捲動 **定域機組** 一節。* se內 **定域機組** 區段中，尋找 **新增 Azure 虛擬機器範本** 按一下 **新增** ] 按鈕。** butto新增 VM 範本 vm template](./media/azure-slave-plugin-for-hudson/hudson-setup-addnewvmtempla指定雲端服務名稱在 **名稱** 欄位。a如果您指定的名稱參照現有雲端服務，便會在該服務中佈建 VM。h否則，Azure 會建立一個新的。e a n在 **描述** 欄位中，輸入您要建立之範本的說明文字。r此資訊僅供記錄之用，並不會用於佈建 VM。ioni在 **標籤** 欄位中，輸入 **linux**。r此標籤可用來識別您要建立的範本，而且後續會用來在建立 Hudson 工作時參照範本。 Huds選取將要建立 VM 的區域。 be 選取適當的 VM 大小。ate V指定將要建立 VM 的儲存體帳戶。 請確定它與您將要使用的雲端服務位在相同的區域中。l如果您想要建立新的儲存體，可以將此欄位保留空白。field保留時間會指定 Hudson 要等待幾分鐘的時間才將閒置的從屬節點刪除。 請讓此欄位保持使用預設值 60。value在 **使用量**, ，選取適當的條件，將會使用此從屬節點。i現在，選取 **利用此節點儘可能**。 possi到目前為止，您的表單應該會與下圖類似：milar to範本設定emplate config](./media/azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withd在 **映像系列或識別碼** 您必須指定您的 VM 上安裝系統映像。e您可以從映像系列清單中進行選取，或指定自訂映像。 custo如果您想從映像系列清單中進行選取，請輸入映像系列名稱的第一個字元 (需區分大小寫)。e例如，輸入 **U** 會顯示 Ubuntu Server 系列的清單。r在從清單中進行選取後，Jenkins 將會在佈建 VM 時使用該系列的該系統映像的最新版本。oning yo作業系統系列清單OS family list](./media/azure-slave-plugin-for-hudson/hudson-oslist.如果您想要改用自訂映像，請輸入該自訂映像的名稱。 清單中不會顯示自訂映像名稱，因此您必須確實輸入正確的名稱。red correc本教學課程中，輸入 **U** 顯示 Ubuntu 映像的清單，並選取 **Ubuntu Server 14.04 LTS**。r 14.04如 **啟動方法**, ，請選取 **SSH**。elect將下列指令碼複製並貼入 **Init 指令碼** 欄位。cript** field.

        # Install Java
        sudo apt-get -y update
        sudo apt-get install -y openjdk-7-jdk
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y openjdk-7-jdk

        # Install git
        sudo apt-get install -y git

        #Install ant
        sudo apt-get install -y ant
        sudo apt-get -y update --fix-missing
        sudo apt **Init 指令碼** VM 建立後就會執行。 在此範例中，指令碼會安裝 Java、git 和 ant。talls Java在 **Username** 和 **密碼** 欄位中，將會在 VM 上建立的系統管理員帳戶輸入偏好使用的值。ll be按一下 [ **驗證範本** 以檢查您所指定的參數是否有效。 you 按一下 [ **儲存**。d.
18.建立在 Azure 的從屬節點上執行的 Hudson 工作 o在本節中，您將建立在 Azure 的從屬節點上執行的 Hudson 工作。on a s在 Hudson 儀表板中，按一下 [ **新工作**。board輸入要建立之工作的名稱。the 針對工作類型] 選取 **建置自由樣式的軟體作業**。ree-按一下 [ **確定**。e jo在工作組態頁面中，選取 **限制可以執行此專案的位置**。his 選取 **節點和標籤功能表** ，然後選取 **linux** (在上一節中建立虛擬機器範本時，我們指定此標籤)。in the在 **建置** 區段中，按一下 **新增建置步驟** ，然後選取 **執行殼層**。sele編輯下列指令碼，將 **(github 帳戶名稱)**, ，**(專案名稱)**, ，和 **(專案目錄)** 取代為適當值，並在出現的文字區域中貼上編輯過的指令碼。e text area that appears.


        # Clone from git repo
        currentDir="$PWD"
        if [ -e (your project directory) ]; then
            cd (your project directory)
            git pull origin master
        else
            git clone https://github.com/(your github account name)/(your project name).git
        fi
        
        # change directory to project
        cd $currentDir/(your project directory)

        #Execute 按一下 [ **儲存**。  ant在 Hudson 儀表板中，尋找您剛才建立的工作，然後按一下 **排程組建** 圖示。ck Hudson 就會使用上一節建立的範本建立從屬節點，並執行您針對這項工作指定於組建步驟中的指令碼。ied in the build step for this task.






  

  
