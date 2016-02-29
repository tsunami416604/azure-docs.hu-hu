<properties 
    pageTitle="使用 Azure 儲存體搭配 Jenkins 連續整合解決方案 | Microsoft Azure" 
    description="本教學課程說明如何使用 Azure Blob 服務作為 Jenkins 連續整合解決方案所建立之組建成品的存放庫。" 
    services="storage" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/12/2015" 
    ms.author="robmcm"/>

# 使用 Azure 儲存體搭配 Jenkins 連續整合解決方案

## 概觀

下列資訊說明如何使用 Azure Blob 服務，作為 Jenkins 連續整合 (CI) 解決方案所建立的組建成品儲存機制，或作為在組建程序中要使用之可下載檔案的來源。 您會發現這很實用的其中一種情況就是，當您在敏捷式開發環境中編寫程式碼 (使用 Java 或其他語言) 時，組建是根據連續整合來執行，而您需要一個存放庫來存放組建成品，以便能夠將這些成品分享給其他組織成員或客戶，或是維護封存等等。 另一種情況是當組建工作本身需要其他檔案時，例如，要隨組建輸入一起下載的相依性項目。

在本教學課程中，您將使用由 Microsoft 所提供適用於 Jenkins CI 的 Azure 儲存體外掛程式。

## Jenkins 概觀 ##

Jenkins 透過讓開發人員輕鬆整合自己的程式碼變更，並會以自動且頻繁的方式產生組建，來實現軟體專案的連續整合，從而提升開發人員產能。 組建會分版本存在，而組建成品可以上傳至各種存放庫。 本主題將示範如何使用 Azure Blob 儲存體作為組建成品的存放庫。 其中也示範如何從 Azure Blob 儲存體下載相依性項目。

Jenkins 的詳細資訊，請參閱 [認識 Jenkins][]。

## 使用 Blob 服務的優點 ##

使用 Blob 服務來裝載您敏捷式開發組建成品的優點包括：

- 組建成品和/或可下載相依性項目的高可用性。
- 在您的 Jenkins CI 解決方案上傳組建成品時提供良好的效能。
- 在您的客戶和合作夥伴下載組建成品時提供良好的效能。
- 提供使用者存取原則控制，可以選擇匿名存取、期限型共用存取簽章存取、私用存取等。

## 必要條件 ##

若要使用 Blob 服務搭配 Jenkins CI 解決方案，您將需要下列項目：

- 一套 Jenkins 連續整合解決方案。

    如果您目前沒有 Jenkins CI 解決方案，可以透過下列步驟執行 Jenkins CI 解決方案：

    1. 在已啟用 Java 的機器上，下載 jenkins.war 從 <http://jenkins-ci.org>。
    2. 在命令提示字元中切換至包含 jenkins.war 的資料夾，然後執行：

        `java -jar jenkins.war`

    3. 在瀏覽器中開啟 `http://localhost:8080/`。 這樣會開啟 Jenkins 儀表板，您將使用此儀表板來安裝及設定 Azure 儲存體外掛程式。

        雖然一般 Jenkins CI 解決方案會設定成以服務的形式執行，但對本教學課程來說，在命令列執行 Jenkins war 已經足夠。

- 一個 Azure 帳戶。 您可以註冊 Azure 帳戶，請造訪 <http://www.azure.com>。

- 一個 Azure 儲存體帳戶。 如果您還沒有儲存體帳戶，您可以建立一個使用中的步驟 [如何建立儲存體帳戶][]。

- 建議您熟悉 Jenkins CI 解決方案，但這並非必要，因為下列內容將使用一個基本範例來示範使用 Blob 服務作為 Jenkins CI 組建成品的存放庫時所需的步驟。

## 如何使用 Blob 服務搭配 Jenkins CI ##

若要使用 Blob 服務搭配 Jenkins，您將要安裝 Azure 儲存體外掛程式、設定外掛程式來使用您的儲存體帳戶，然後建立一個會將您的組建成品上傳至您儲存體帳戶的建置後動作。 這些步驟將於下列各節中說明。

## 如何安裝 Azure 儲存體外掛程式 ##

1. 在 Jenkins 儀表板中，按一下 [ **管理 Jenkins**。
2. 在 **管理 Jenkins** 頁面上，按一下 **管理外掛程式**。
3. 按一下 [ **可用** ] 索引標籤。
4. 在 **Artifact Uploaders** ] 區段中，核取 **Microsoft Azure 儲存體外掛程式**。
5. 按一下 [ **不需重新啟動安裝** 或 **立即下載並安裝之後重新啟動**。
6. 重新啟動 Jenkins。

## 如何設定 Azure 儲存體外掛程式來使用您的儲存體帳戶 ##

1. 在 Jenkins 儀表板中，按一下 [ **管理 Jenkins**。
2. 在 **管理 Jenkins** 頁面上，按一下 **設定系統**。
3. 在 **Microsoft Azure 儲存體帳戶組態** 區段:
    1. 輸入您儲存體帳戶名稱，您可以從取得 [Azure 入口網站](portal.azure.com)。
    2. 輸入儲存體帳戶金鑰，也可行，或是從 [Azure 入口網站](portal.azure.com)。
    3. 使用的預設值為 **Blob 服務端點 URL** 如果您使用公用 Azure 雲端。 如果您使用不同的 Azure 雲端，使用的端點中所指定 [Azure 入口網站](portal.azure.com) 儲存體帳戶。 
    4. 按一下 [ **驗證儲存體認證** 以驗證您的儲存體帳戶。 
    5. [選用]如果您有其他儲存體帳戶要提供給 Jenkins CI 使用，請按一下 **新增更多的儲存體帳戶**。
    6. 按一下 [ **儲存** 以儲存設定。

## 如何建立會將您的組建成品上傳至您儲存體帳戶的建置後動作 ##

為了方便說明，首先，我們需要建立一個會建立數個檔案的工作，然後新增一個會將這些檔案上傳至您儲存體帳戶的建置後動作。

1. 在 Jenkins 儀表板中，按一下 [ **新項目**。
2. 將工作 **MyJob**, ，按一下 [ **建置自由樣式的軟體專案**, ，然後按一下 [ **確定**。
3. 在 **建置** 區段的工作組態中，按一下 [ **新增建置步驟** 選擇 **Execute Windows batch 命令**。
4. 在 **命令**, ，使用下列命令:

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. 在 **建置後動作** 區段的工作組態中，按一下 [ **加入建置後動作** 選擇 **將成品上傳至 Azure Blob 儲存體**。
6. 如 **儲存體帳戶名稱**, ，選取要使用的儲存體帳戶。
7. 如 **容器名稱**, ，指定容器名稱。 (如果上傳組建成品時該容器尚未存在，將會建立該容器。)您可以使用環境變數，因此這個範例輸入 **${JOB_NAME}** 作為容器名稱。

    **秘訣**
    
    下面 **命令** 您已輸入的指令碼的區段 **Execute Windows batch 命令** Jenkins 所辨識環境變數的連結。 按一下該連結即可了解各環境變數名稱和描述。 請注意，環境變數包含特殊字元，例如 **BUILD_URL** 環境變數不能當做容器名稱或共同虛擬路徑。

8. 按一下 [ **新容器變成公開預設** 此範例中。 (如果您想要使用私用容器，則需要建立共用存取簽章來允許存取。 這已超出本主題的範圍。 您可以進一步了解共用的存取簽章在 [建立共用存取簽章](http://go.microsoft.com/fwlink/?LinkId=279889)。)
9. [選用]按一下 [ **Clean container** 如果您想要上傳組建成品之前清除內容的容器 (保留為未選取如果您不想清除容器的內容)。
10. 如 **要上傳--成品**, ，輸入 **文字 /*.txt**。
11. 如 **已上傳的成品的共同虛擬路徑**, ，本教學課程中的，輸入 **${BUILD\_ID} / ${BUILD\_NUMBER}**。
12. 按一下 [ **儲存** 以儲存設定。
13. 在 Jenkins 儀表板中，按一下 [ **現在建置** 執行 **MyJob**。 檢查主控台輸出中的狀態。 當建置後動作開始上傳組建成品時，主控台輸出中將會包含 Azure 儲存體的狀態訊息。
14. 順利完成作業時，您就可以開啟公用 Blob 來檢查組建成品。
    1. 登入 [Azure 入口網站](portal.azure.com)。
    2. 按一下 [ **儲存體**。
    3. 按一下用於 Jenkins 的儲存體帳戶名稱。
    4. 按一下 [ **容器**。
    5. 按一下 [容器名稱 **myjob**, ，這是您建立 Jenkins 工作時所指派之工作名稱的小寫版本。 在 Azure 儲存體中，容器名稱和 Blob 名稱皆為小寫 (並且區分大小寫)。 清單內的 blob 容器名稱 **myjob** 您應該會看到 **hello.txt** 和 **date.txt**。 請複製這些項目中任何一項的 URL，然後在瀏覽器中開啟它。 您會看到文字檔已上傳作為組建成品。

每一工作只能建立一個將成品上傳至 Azure Blob 儲存體的建置後動作。 請注意，將成品上傳至 Azure blob 儲存體的單一建置後動作可以指定不同的檔案 (包含萬用字元) 和中的檔案路徑 **要上傳--成品** 使用分號作為分隔符號。 例如，若 Jenkins 組建產生 JAR 檔和 TXT 檔，在工作區的 **建置** 資料夾，而您想要上傳至 Azure blob 儲存體的兩，請使用下列 **要上傳--成品** 值: **build/\*.jar;build/\*.txt**。 您也可以使用雙冒號語法來指定要在 Blob 名稱內使用的路徑。 比方說，如果您想要使用上傳 Jar **二進位檔** 在 blob 路徑和使用上傳的 TXT 檔案 **通知** 在 blob 路徑中，使用下列 **要上傳--成品** 值: **build/\*.jar::binaries;build/\*.txt::notices**。

## 如何建立從 Azure Blob 儲存體下載的組建步驟 ##

下列步驟示範如何設定從 Azure Blob 儲存體下載項目的組建步驟。 如果您要在組建中加入項目，例如您存放在 Azure Blob 儲存體中的 JAR，則這會很有用。

1. 在 **建置** 區段的工作組態中，按一下 [ **新增建置步驟** 選擇 **從 Azure Blob 儲存體下載**。
2. 如 **儲存體帳戶名稱**, ，選取要使用的儲存體帳戶。
3. 如 **容器名稱**, ，指定您想要下載的 blob 容器的名稱。 您可以使用環境變數。
4. 如 **Blob 名稱**, ，指定 blob 名稱。 您可以使用環境變數。 另外，您也可以在指定 Blob 名稱的開頭字母之後，使用星號作為萬用字元。 例如， **專案 *** 指定名稱開頭的所有 blob **專案**。
5. [選用]如 **下載路徑**, ，指定您要從 Azure blob 儲存體下載檔案在 Jenkins 機器上的路徑。 也可以使用環境變數 (如果您未提供值給 **下載路徑**, ，從 Azure blob 儲存體檔案將下載到工作的工作區。)

如果您還有其他項目要從 Azure Blob 儲存體下載，您可以建立其他組建步驟。

執行組建之後，您可以檢查組建記錄主控台輸出，或查看下載位置，了解是否已成功下載您想要的 Blob。  

## Blob 服務所使用的元件 ##

以下提供 Blob 服務元件的概觀。

- **儲存體帳戶**: 存取 Azure 儲存體透過儲存體帳戶。 這是存取 blob 用的最高等級的命名空間。 帳戶可以包含不限數目的容器，只要它們的大小總計低於 100TB 即可。
- **容器**: 容器提供一組 blob 的群組。 所有 Blob 都必須放在容器中。 一個帳戶可以包含的容器不限數量。 容器可以儲存無限制的 Blob。
- **Blob**: 任何類型和大小的檔案。 Azure 儲存中可以儲存兩種 Blob：區塊 Blob 和分頁 Blob。 大部分檔案都是區塊 Blob。 單一區塊 Blob 的大小上限為 200GB。 本教學課程使用區塊 Blob。 分頁 Blob (另一種 Blob 類型) 的大小上限為 1TB，當檔案中的位元組範圍經常修改時，分頁 Blob 的效率較高。 如需 blob 的詳細資訊，請參閱 [了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)。
- **URL 格式**: 利用下列 URL 格式來定址 Blob:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (上述格式適用於公用 Azure 雲端。 如果您使用不同的 Azure 雲端，使用中的端點 [Azure 入口網站](portal.azure.com) 來判斷您的 URL 端點。)

    在上述格式中，`storageaccount` 代表您的儲存體帳戶名稱，`container_name` 代表您的容器名稱，而 `blob_name` 代表您的 Blob 名稱。 容器名稱中可以有多個以正斜線分隔的路徑 **/**。 在本教學課程的範例容器名稱為 **MyJob**, ，和 **${BUILD\_ID} / ${BUILD\_NUMBER}** 所使用的共同虛擬路徑，產生的 blob url 格式如下:

    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## 後續步驟

  [How to Create a Storage Account]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Meet Jenkins]: https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins
 
