<properties
    pageTitle="Azure 角色屬性"
    description="了解如何使用適用於 Eclipse 的 Azure 工具組以進行 Azure 角色設定。"
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
    ms.date="11/20/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# Azure 角色屬性 #

您可以在適用於 Eclipse 的 Azure 工具組內設定 Azure 角色的各種組態設定。

## 設定 Azure 角色屬性 ##

透過背景工作角色的各個屬性對話方塊，即可完成 Azure 角色屬性的設定工作。 在 Eclipse 的專案總管] 窗格中開啟角色的內容功能表，然後選取 **Azure** 子功能表。 (如果在 Eclipse 的 [專案總管] 中看不到角色，請在 [專案總管] 中展開 Azure 專案)。

![][ic789599]

可以從設定的各種屬性 **屬性** 對話方塊以本主題所述。 要注意的是，在您建立新的 Azure 部署專案時，系統會自動填入其中的許多屬性。

以下是可供 Azure 角色使用的屬性頁。

* [虛擬機器屬性](#virtual_machine_properties)
* [快取屬性](#caching_properties)
* [憑證屬性](#certificates_properties)
* [元件屬性](#components_properties)
* [偵錯屬性](#debugging_properties)
* [端點屬性](#endpoints_properties)
* [環境變數屬性](#environment_variables_properties)
* [負載平衡/工作階段親和性 (也稱為「黏性工作階段」) 屬性](#session_affinity_properties)
* [本機儲存體屬性](#local_storage_properties)
* [伺服器組態屬性](#server_configuration_properties)
* [SSL 卸載屬性](#ssl_offloading_properties)
    
<a name="virtual_machine_properties"></a>
### 虛擬機器屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **屬性**, ，，您將能夠變更虛擬機器的大小，且也可以變更數個執行個體，如下圖所示。

![][ic719499]

>[AZURE.NOTE] 僅限 Windows: 當您將執行個體數目的值大於 1，您也可以設定應用程式伺服器，此工具組可讓執行在模擬器中，這個設定只有 1 角色執行個體。 這是為了避免不同的伺服器執行個體在同一部電腦上執行時，彼此之間發生連接埠繫結衝突 (例如，全都嘗試繫結至連接埠 8080) 。 您想要的執行個體計數設定會保留下來，但此設定只會在您部署至雲端時生效。

<a name="caching_properties"></a> 
### 快取屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **快取**。 在這個對話方塊中，您可以啟用已具名且共置的 memcache 相容快取，以協助 Web 應用程式加快速度。

![][ic719483]

內 **快取** 屬性頁上，您可以指定下列通用設定:

* 是否啟用共置快取。
* 快取大小佔記憶體的百分比。
* 當應用程式以雲端服務的形式執行時，用來儲存快取狀態的儲存體帳戶名稱，若不想儲存快取狀態，則為無  (在計算模擬器中執行應用程式時，不會使用儲存體帳戶名稱)。如果您將設定儲存體帳戶名稱 **(自動)** (此為預設值)，您的快取設定會自動使用相同的儲存體帳戶與您在中選取的一個 **Publish to Azure** ] 對話方塊。

>[AZURE.NOTE]  **(自動)** 設定將會有正面的影響只有當您發行使用 Eclipse 工具組部署發行精靈。 如果您發行.cspkg 檔案，以手動方式使用外部機制，例如 [Azure 管理入口網站][], ，部署將無法正確運作。

下列對話方塊顯示快取的各項屬性。

![][ic719501]

* **名稱:** 共置快取的名稱。
* **連接埠號碼:** 用於快取的連接埠號碼。
* **到期原則:** 指定的索引鍵快取中過期時的下列值之一。
    * **絕對值:** 指定的時間時金鑰到期 **分鐘存留** 為止。
    * **NeverExpires:** 金鑰沒有到期時間。
    * **SlidingWindow:** 的金鑰會到期，如果尚未存取所指定的時間長度 **分鐘存留**; 每次存取時，就會重設到期時鐘。
* **分鐘的時間才能 live:** 的一樣，受限於到期原則的金鑰存活的分鐘數上限。
* **使用不同的角色執行個體上的複寫備份的高可用性:** 啟用時，有助於提供高可用性利用複寫在不同的角色執行個體上的備份。 但請注意，若要讓此功能運作，您的部署中至少要有兩個作用中的角色執行個體。

若要新增新的快取，請按一下 [ **新增** 按鈕 **快取** 屬性頁和 **設定具名快取** 隨即會開啟對話方塊。 提供上述屬性的值。

若要修改具名快取，請選取 [快取，然後按一下 **編輯** 按鈕 **快取** 屬性頁。 隨即會開啟對話方塊供您修改快取屬性。 按下 **確定** 儲存快取的值。

若要刪除快取中，選取快取，然後按一下 **移除** 按鈕 **快取** 屬性頁上，然後再按一下 **是** 以確認刪除。

如需有關如何使用快取的詳細資訊，請參閱 [如何使用共置快取][]。

<a name="certificates_properties"></a> 
### 憑證屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **憑證**。

![][ic710964]

在這個對話方塊中，您可以新增或移除 Eclipse 專案所參考的憑證。 請注意，此處所列的憑證不會自動儲存在任何 Java 金鑰存放區內，因此無法自動在 Java 應用程式內提供使用。 這些憑證只會向 Azure 進行註冊，以便可以預先載入到執行部署之虛擬機器上的 Windows 憑證存放區，並於隨後供其他 Windows 軟體使用。 目前，唯一的功能會使用的憑證的工具組參考這種方式 **憑證** 對話方塊是 [SSL 卸載][], ，因為它可以依賴網際網路資訊服務 (IIS) 和應用程式要求路由 (ARR)，需要適當的憑證可用於以這種方式。

當您使用發佈精靈將專案部署至 Azure 時，系統會提示您指向這些憑證所對應的個人資訊交換 (PFX) 檔案以及憑證的密碼，以便自動將憑證上傳至 Azure 服務，但前提是這些憑證先前並未上傳至該處。

<a name="components_properties"></a> 
### 元件屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **元件**。 在這個對話方塊中，您可以新增、修改或移除角色的元件，以及變更它們的處理順序。

![][ic719502]

元件功能可讓您對 Azure 部署專案新增相依項目，例如 Java 應用程式專案、特殊檔案，以及部署所需的可執行命令列陳述式。

針對每個元件，您可以指定：

* 在建置 Azure 部署專案時，將元件匯入到其中時所要採取的步驟。
* 在 Azure 雲端部署該元件時所要採取的步驟。

>[AZURE.NOTE] 當指定元件檔案或命令列，請注意，您的部署將會發行至 Windows 虛擬機器，因此您自訂的步驟必須適用於 Windows 架構的作業系統。 

元件具有下列屬性：

* **匯入:** 方法，表示元件如何將匯入專案建置專案時。 此屬性可為下列其中一個值：
    * **複製:** 元件會從指定的本機路徑複製 **從** 進入此角色的屬性 **approot** 目錄。
    * **EAR:** 元件是 Java 企業封存檔 (EAR) 從企業應用程式專案中所指定的本機路徑上匯入 **從** 屬性。 (工具組會根據該位置之專案的性質自動偵測此元件)。
    * **JAR:** 元件是 Java 封存檔 (JAR)，並且從所指定的本機路徑上的 Java 專案匯入 **從** 屬性。 (工具組會根據該位置之專案的性質自動偵測此元件)。
    * **none:** 匯入元件會採取任何動作。 此選項適用於當元件被假設為已出現在角色中 **approot** 目錄中，或當元件只是可執行的命令列，為陳述式中所指定 **為** 屬性時 **部署** 方法是 **exec**。
    * **WAR:** 元件是 Java web 應用程式封存檔 (WAR)，並且從所指定的本機路徑上的動態 Web 專案匯入 **從** 屬性。 (工具組會根據該位置之專案的性質自動偵測此元件)。
    * **zip:** 元件是 zip 檔案，並且壓縮目錄或指定的檔案匯入 **從** 屬性。
* **從:** 資料夾或檔案匯入至您的部署項目表示本機電腦上的來源路徑。 此屬性可以使用 Windows 環境變數。 匯入的所有元件都會都匯入角色的 **approot** 建置專案時的目錄。
    
    請注意，在部署到雲端 (而非計算模擬器) 時，可以透過下載來部署元件。 請參閱以下有關新增元件的相關資訊。    
    
* **做為:** 檔案名稱的元件就會匯入角色的 **approot** 目錄，以及最後部署在 Azure 雲端。 將此屬性空白，可讓名稱保持與位於本機電腦上時相同  (可執行檔元件，也就是指 **部署** 方法會設定為 **exec**, ，這可以是任意的 Windows 命令列陳述式。)

    >[AZURE.IMPORTANT] 如果您使用空格字元，這個值，則會以不同方式處理根據部署方法。 如果部署方法 **exec**, ，空格會被解譯為命令列引數分隔符號，而不是檔案名稱的一部分。 若為其他所有部署方法，系統會將空格解譯為檔案名稱的一部分。
    
* **部署:** 方法，表示在開始部署時套用至元件的動作。 此屬性可為下列其中一個值：
    * **複製:** 元件會複製到指定的目的地路徑 **至** 屬性。
    * **exec:** 元件是可執行檔路徑所指定的內容中執行的 Windows 命令列陳述式 **到** 屬性，在開始部署。
    * **none:** 啟動部署時，將會套用至元件的採取任何動作。
    * **zip:** 元件會解壓縮到指定的目的地路徑 **至** 屬性。 這個方法是時才可使用 **匯入** 屬性是 **zip**。
* **[到]:** 元件將會部署在虛擬機器上的目的地路徑。 Windows 環境變數，請使用這個屬性，而且檔案路徑相對於 **approot**。
    
若要新增新元件，請按一下 [ **新增** 按鈕 **元件** 屬性頁和 **Azure 角色元件** 隨即會開啟對話方塊。 提供上述屬性的值。 

下圖顯示新增 WAR 元件的範例。

![][ic719503]

當部署到雲端 (而非計算模擬器)，如果您想要部署元件無法下載，請確認 **不包含在封裝中，定域機組中時，從部署** 已核取。 如果您想要從 Azure 儲存體帳戶下載中，選取 [從儲存體帳戶 **儲存體帳戶** 下拉式清單 (您可以按一下 **帳戶** 連結可修改功能的清單)，這部分會填入 **URL** 欄位，然後填入 URL 的其餘部分。 如果您不想使用 Azure 儲存體，選取 **(無)** 從 **儲存體帳戶** 下拉式清單，然後輸入您的元件中的 URL **URL** 欄位。 指定下列其中一個方法：

* **複製:** 下載元件複製到指定的目的地路徑 **到目錄** 路徑。
* **相同:** 所使用的相同方法 **從下載部署** 與 **部署套件從**。
* **zip:** 下載元件會解壓縮到指定的目的地路徑 **到目錄** 路徑。

若要修改的元件，請選取元件，然後按一下 **編輯** 按鈕 **元件** 屬性頁。 隨即會開啟對話方塊供您修改元件屬性。 按下 **確定** 儲存元件值。

若要刪除元件時，請在選取的元件，然後按一下 **移除** 按鈕 **元件** 屬性頁上，然後再按一下 **是** 以確認刪除。

元件會依照所列順序進行處理。 使用 **移** 和 **下移** 按鈕來排列的順序。

>[AZURE.NOTE] 伺服器組態功能依賴元件。 不移除對應的伺服器組態，就無法移除或編輯這些元件。 當您嘗試對這類元件進行變更時，將會提示您相關資訊。

<a name="debugging_properties"></a> 
### 偵錯屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **偵錯**。 在這個對話方塊中，您可以啟用或停用遠端偵錯以及建立偵錯組態，如下圖所示。

![][ic719504]

如需有關偵錯的相關資訊，請參閱 [在 Eclipse 中偵錯 Azure 應用程式][]。

<a name="endpoints_properties"></a> 
### 端點屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **端點**。 在這個對話方塊中，您可以建立端點以及編輯或移除端點，如下圖所示。

![][ic719505]

若要新增端點，請按一下 [ **新增** 按鈕 **端點** 屬性頁和 **新增端點** 隨即會開啟對話方塊。

![][ic710897]

輸入端點的名稱，選取型別 (任一 **輸入**, ，**內部**, ，或 **InstanceInput**)，並指定的公用和私用連接埠。 按下 **確定** 以儲存新的端點值。

根據端點的類型，您可以使用的連接埠範圍如下：

* 輸入執行個體端點，公用連接埠可以是連接埠範圍 (例如 **2000年 2010年**) 和私人連接埠是固定的值。
* 若為內部端點，則不會使用公用連接埠，而且私用連接埠可以是範圍、保留空白，或設為星號以表示它會由 Azure 自動設定。
* 若為輸入端點，則公用連接埠只能是固定值，但私用連接埠可以是固定值、保留空白，或設為星號以表示它會由 Azure 自動設定。

如果您想要使用單一連接埠號碼而非某個範圍，請將代表範圍結尾的文字方塊保留空白。

應用程式的連接埠設定為自動執行，如果您需要判斷執行階段期間實際使用的通訊埠，可以使用 Azure 服務執行階段 API，而其已記錄 [com.microsoft.windowsazure.serviceruntime (英文) 封裝摘要][]。

若要查看如何使用執行個體輸入的端點，以協助偵錯多個執行個體部署，請參閱 [偵錯多個執行個體部署中的特定角色執行個體][]。

若要修改端點，選取端點，然後按一下 **編輯** 按鈕 **端點** 屬性頁。 隨即會開啟對話方塊供您修改端點名稱、類型和公用與私人連接埠。 按下 **確定** 以儲存修改的端點值。

若要刪除端點，選取端點，然後按一下 **移除** 按鈕 **端點** 屬性頁上，然後再按一下 **是** 以確認刪除。

為了正確設定使用者對某個角色所啟用的某些功能 (例如快取、遠端偵錯、工作階段親和性或 SSL 卸載)，工具組可能會自動設定隨使用者定義的端點一起列出的特殊端點。 只要啟用關聯的功能，工具組就會防止使用者編輯或刪除這類自動產生的端點。

<a name="environment_variables_properties"></a> 
### 環境變數屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **環境變數**。 在這個對話方塊中，您可以建立環境變數以及修改或移除環境變數，如下圖所示。

![][ic719506]

當角色啟動時，啟動指令碼就能夠使用環境變數。

>[AZURE.NOTE] 在指定環境變數，請記住，您的部署將會發行至 Windows 虛擬機器，因此您的環境變數必須適用於 Windows 架構的作業系統。

例如在角色啟動時的環境變數中，建立新的環境變數，即可 **新增** ] 按鈕。 下圖顯示名為 **{1>myroleversion** 所建立，並將值指派給 **1.0**。

![][ic659268]

在 jsp 程式碼中，您可以使用 `System.getenv` 方法來顯示值：

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

當應用程式執行時會產生以下輸出：

![][ic552233]

若要修改環境變數，請選取 [環境變數，然後按一下 **編輯** 按鈕 **環境變數** 屬性頁。 隨即會開啟對話方塊供您修改環境變數屬性。 按下 **確定** 來儲存的環境變數值。

若要刪除環境變數，請選取 [環境變數，然後按一下 **移除** 按鈕 **環境變數** 屬性頁上，然後再按一下 **是** 以確認刪除。

為了正確設定使用者對某個角色所啟用的某些功能 (例如伺服器組態、遠端偵錯或本機儲存體)，工具組可能會自動設定隨使用者定義的環境變數一起列出的特殊環境變數。 只要啟用關聯的功能，工具組就會防止使用者編輯或刪除這類自動產生的環境變數。

<a name="session_affinity_properties"></a> 
### 負載平衡/工作階段親和性 (也稱為「黏性工作階段」) 屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **負載平衡**。 在這個對話方塊中，您可以啟用或停用工作階段親和性，如下圖所示。

![][ic719492]

如需相關資訊，請參閱 [工作階段同質性][]。 另外請注意這項功能行為的 SSL 卸載，內容中所述 [SSL 卸載][]。

<a name="local_storage_properties"></a> 
### 本機儲存體屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **本機儲存體**。 在這個對話方塊中，您可以建立、修改或移除正在執行應用程式之虛擬機器的暫存本機儲存體。 您可以針對本機儲存體大小，以及回收角色時是否保留其內容設定特定值，如下圖所示。

![][ic719508]

您也可以選擇性地指定對應至本機儲存體的環境變數。

根據預設，所有您將部署至 Azure 的項目是放置 (和解壓縮) **approot** 角色執行個體的資料夾。 雖然大多數的簡單部署可容納解壓縮之後，將空間配置給 **approot** 目錄可是有限且未妥善定義 (小於 1 GB 是合理的經驗法則)。 因此，以確保 Azure 配置足夠的磁碟空間較大的部署，可能不適合在 **approot** 資料夾中，您應該設定本機儲存體資源使用 **本機儲存體** ] 對話方塊。 若要這樣做的簡單方法，請參閱 [部署大型部署][]。

您可以容易從啟動指令碼參考儲存體資源 (例如，您 **startup.cmd**) 中所示，使用環境變數會自動關聯 Eclipse 工具組資源， **本機儲存體** ] 對話方塊。 該環境變數會包含您已在執行啟動指令碼時設定之本機資源的完整路徑。 

若要修改本機儲存體資源，請選取本機儲存體資源，然後按一下 **編輯** 按鈕 **本機儲存體** 屬性頁。 隨即會開啟對話方塊供您修改本機儲存資源屬性。 按下 **確定** 儲存本機儲存體資源的值。

若要刪除本機儲存體資源，請選取本機儲存體資源，然後按一下 **移除** 按鈕 **本機儲存體** 屬性頁上，然後再按一下 **是** 以確認刪除。

<a name="server_configuration_properties"></a> 
### 伺服器組態屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **伺服器組態**。 在這個對話方塊中，您可以新增、移除和修改部署所使用的 JDK 和 Java 應用程式伺服器，以及新增或移除部署所使用的應用程式 (例如 WAR、JAR 或 EAR 檔案)。

### JDK 組態 ###

這個對話方塊可讓您指定要用於部署的 JDK 封裝。 如果您在 Windows 上使用 Eclipse，您可以指定當 JDK 封裝是在 Azure 模擬器中執行時，讓此封裝在本機上使用，而且您可以選擇將該本機安裝部署至 Azure。 在非 Windows 作業系統上則不適用模擬器 JDK 設定，而且您無法部署本機安裝的 JDK，因為它與 Windows 不相容。 不過，無論您使用什麼作業系統，一律都可以選擇使用第三方 JDK 封裝來部署至 Azure，或從其他下載位置指向您自己的 Windows 相容 JDK 封裝。

以下範例說明如何在 Windows 上指定 JDK：

![][ic780647]

如果您在 Windows 上使用 Eclipse，您可以指定用於計算模擬器; JDK若要這樣做，請確定 **時使用此檔案路徑在本機測試** 簽入 **模擬器部署** 一節。 然後，指定 JDK 的本機路徑，如果系統未自動選取您想使用的 JDK，您可以瀏覽至不同的 JDK。 您也可以選擇要將 JDK 部署至 Azure 雲端服務;若要這樣做，請選取 **部署我的本機 JDK (自動上傳至雲端儲存體)** 選項 **雲端部署** 一節。

附註: 在非 Windows 作業系統， **模擬器部署** 設定和 **部署我的本機 JDK** 選項則無法使用。 下列範例說明如何在 Mac 或其他支援的非 Windows 作業系統上指定 JDK：

![][ic789643]

不論您是在作業系統，您有下列兩個 **雲端部署** 來源和類型的 JDK 封裝的選項:

* **部署 Azure 提供的第三方 JDK 封裝** 
* **從自訂下載部署** 

如果您使用 **部署可從 Azure 的第 3 個廠商 JDK 封裝** 選項:

1. 核取方塊，名為 **部署可從 Azure 的第 3 個廠商 JDK 封裝**。
1. 從下拉式清單中，選取 Azure 提供的第三方 JDK 封裝。
1. 您 **JDK** 看起來類似下列的 Windows 索引標籤:
    ![][ic780648]
    然後，它看起來類似下列的 Mac OS 或其他支援的非 Windows 作業系統:
    ![][ic789643]
1. 按一下 [ **確定** 以儲存變更。
1. 當系統提示您接受第三方 JDK 封裝提供者所提出的授權合約時，請檢閱授權條款。 假設您接受這些條款，請按一下 [ **是** 關閉 **接受授權合約** ] 對話方塊。
    請注意，出現的下拉式清單中的哪些項目為基礎的邏輯 **部署可從 Azure 的第 3 個廠商 JDK 封裝** 可以自訂選項。 若要在自訂項目， **JDK** ] 對話方塊中，按一下 [ **自訂** 連結。 這樣會關閉 **JDK** 屬性頁，並開啟 **{1>componentsets.xml** Eclipse，您可以再視需要修改的檔案。 文件 **{1>componentsets.xml** 納入 **{1>componentsets.xml** 檔案本身。

如果您使用 **從自訂下載部署 JDK** 選項:

1. 建立 JDK 安裝目錄的 ZIP 檔，確保目錄節點本身是 ZIP 結構的子系，而非其內容的子系。 記下目錄名稱，因為您稍後會需要它，並請記住此 JDK 安裝將會部署到 Windows 虛擬機器。
1. 以 blob 的形式將 ZIP 檔上傳到 Azure 儲存體帳戶。 若要這麼做，您可以使用外部取得的工具來將 blob 上傳至 Azure 儲存體。 建議您使用私人 blob。 記下 ZIP 內容的 blob URL。
1. 核取方塊，名為 **從自訂下載部署 JDK**。
    如果您想要從 Azure 儲存體帳戶下載中，選取 [從儲存體帳戶 **儲存體帳戶** 下拉式清單 (您可以按一下 **帳戶** 連結可修改功能的清單)，這部分會填入 **URL** 欄位，然後填入 URL 的其餘部分。 如果您不想使用 Azure 儲存體，選取 **(無)** 從 **儲存體帳戶** 下拉式清單，然後輸入您 JDK 下載內容中的 URL **URL** 欄位。 如果使用 Azure 儲存體，則 URL 中的 blob 名稱必須是小寫。
1. 請確認 **JAVA_HOME** 文字方塊參考正確的目錄名稱。 根據預設，它會參考與您為本機使用所選擇之值相同的 JDK 目錄名稱。 但如果 ZIP 包含的目錄有不同的名稱 (例如，由於使用不同的版本)，更新中的目錄名稱 **JAVA_HOME** 文字方塊中，因為這項設定會使用 (不在計算模擬器) 定域機組中。
1. 按一下 [ **確定** 以儲存變更。

就這麼簡單。 現在，當您針對雲端進行建置時，您會發現封裝大小變小很多，建置程序所需的時間通常較少，而且部署本身在發佈至雲端時所需的時間也應該會變少。 請注意， **部署我的本機 JDK (自動上傳至雲端儲存體)** 或 **從自訂下載部署 JDK** 選項是實際上只有當應用程式部署在雲端中。 在使用計算模擬器時，這兩個選項不會有任何作用，當您部署至計算模擬器時，仍然會使用本機版本的元件。 

### 伺服器組態 ###

以下範例說明如何指定應用程式伺服器。

![][ic796926]

確認 **部署這種伺服器** 核取方塊已選取，然後選擇您想要使用的應用程式伺服器的類型。

若要指定要用於雲端部署的伺服器，您可以利用下列選項：

1. **部署在 Azure 上可用的第 3 個合作對象伺服器** -這是特別適用於開發/測試案例，其中部署效率和簡化是優先考量，而伺服器不需要自訂組態。 或者，當您想要使用其中一部伺服器做為起點，但您在部署的啟動邏輯中加入了適當的伺服器自訂步驟。
1. **從自訂下載部署** -這是特別適用於實際執行案例，當您想要使用雲端應用特別準備及設定伺服器。
1. **部署我的本機伺服器安裝** -這是特別適用於如果本機伺服器安裝已針對您使用自訂設定。 如果您選擇此選項時，您也必須指定本機伺服器的路徑中 **本機伺服器路徑** 文字方塊下方。

如果您使用 **部署在 Azure 上可用的第 3 個合作對象伺服器** 選項:

1. 核取方塊，名為 **部署在 Azure 上可用的第 3 個合作對象伺服器**。
1. 從下拉式功能表中，選取想要在雲端中搭配部署使用的伺服器軟體。 請注意，如果您先前已指定要使用的伺服器類型，您會遭到限制而只能選擇與該伺服器類型位於相同系列中的雲端伺服器。 但如果您沒有選擇伺服器類型，則可以選擇 Azure 上目前提供的任何伺服器，而且系統會自動為您選取伺服器類型。
1. 按一下 [ **確定** 以儲存變更。

如果使用 **從自訂下載部署** 選項:

1. 確定您已根據上述步驟選取伺服器類型。 這會告訴外掛程式如何從自訂下載部署伺服器，因為伺服器必須來自與所選伺服器類型相同的系列。
1. 核取方塊，名為 **從自訂下載部署**。
    如果您想要從 Azure 儲存體帳戶下載中，選取 [從儲存體帳戶 **儲存體帳戶** 下拉式清單 (您可以按一下 **帳戶** 連結可修改功能的清單)，這部分會填入 **URL** 欄位，以及您伺服器的 URL 的其餘部分中然後填滿下載 ZIP (當使用 Azure 儲存體，在 URL 中的 blob 名稱必須是小寫)。 如果您不想使用 Azure 儲存體，選取 **(無)** 從 **儲存體帳戶** 下拉式清單，然後輸入您的伺服器的 URL 下載的 ZIP **URL** 欄位。 ZIP 檔會包含代表應用程式伺服器安裝目錄的子資料夾。 例如，如果您使用 Apache Tomcat 7.0.35 的 zip，zip 內會是代表安裝目錄，例如子資料夾 **apache-tomcat-7.0.35**。 
1. 指定主目錄環境變數的值。 它會預設為用於本機應用程式伺服器的值 (如果有的話)，但如果您的雲端應用程式伺服器與本機應用程式伺服器不同，則可以指定不同的值。 不過，您必須確定雲端應用程式伺服器屬於與先前所選伺服器類型相同的系列。
    如果您日後更新雲端應用程式伺服器 ZIP 檔，您可以手動變更主目錄設定，或讓它再次符合本機設定 (如果您也變更了本機應用程式伺服器)。
1. 按一下 [ **確定** 以儲存變更。

項目出現在基礎邏輯 **伺服器** ] 索引標籤的 **伺服器組態** 可自訂屬性頁。 如果您需要的不是預設值，或如果您想要新增其他伺服器，就可能需要這項進階功能。 若要在自訂邏輯， **伺服器** ] 對話方塊中，按一下 [ **自訂** 連結。 這樣會關閉 **伺服器組態** 屬性頁，並開啟 **{1>componentsets.xml** Eclipse，您可以修改，以擴充伺服器的組態範本所需的檔案。 文件 **{1>componentsets.xml** 納入 **{1>componentsets.xml** 檔案本身。

如果您使用 **部署我的本機伺服器 (自動上傳至雲端儲存體)** 選項:

1. 核取方塊，名為 **部署我的本機伺服器 (自動上傳至雲端儲存體)**。
1. 使用 **儲存體帳戶** 下拉式清單中，選取 **(自動)**。 如果您指定 **(自動)** 在這裡，Eclipse 工具組會使用相同的儲存體帳戶為您的伺服器與您為您的部署中選取的一個 **Publish to Azure** ] 對話方塊。
1. 按一下 [ **確定** 以儲存變更。

選取您的電腦上的伺服器安裝路徑 **本機伺服器路徑** 文字方塊中，如果下列任一條件成立:

* 您想要在模擬器中測試部署 (僅適用於 Windows)。
* 您想要將本機安裝的伺服器部署到雲端。
* 您想要使用您自己定域機組中案例的自訂伺服器下載，也請確認 **部署我的本機伺服器 (自動上傳至雲端儲存體)** 先前已選取的選項。

如果您的情況不適用上述任何選項，則可以選擇使用本機伺服器設定。

### 應用程式組態 ###

以下範例說明如何指定應用程式。

![][ic719512]

按一下 [ **新增** 新增另一個應用程式，或 **移除** 移除應用程式。 基於效率考量，如果您想要部署到雲端，使用時，應用程式的來源使用的下載 [元件屬性](#components_properties) 以指定的 URL，儲存體帳戶等。 

從 2014 年 4 月版本開始，您的應用程式會自動上傳至相同的儲存體帳戶 (在 **eclipsedeploy** 容器) 做為您的部署選取的一個。 部署的啟動邏輯包含先從該儲存體帳戶下載這些應用程式的步驟。 這表示您不需要重新建置並重新部署整個封裝就可以升級部署中的應用程式，方法是直接將較新版的應用程式手動上傳至該儲存體帳戶 (例如使用 Azure 入口網站)，來取代工具組原本上傳至該處的 WAR 檔案。 然後，就只要再次使用 Azure 的管理入口網站或透過命令列公用程式開始回收所有角色執行個體  (目前不支援直接從 Eclipse 工具組內觸發角色回收程序)。

### 伺服器組態的相關注意事項 ###

透過所做的變更 **伺服器組態** 屬性頁會反映在 `<component>` package.xml 檔的項目。

當您使用 **自動上傳...** 或 **從下載部署...** 定域機組 (而非計算模擬器)，建置 JDK 或應用程式伺服器和您的選項會連線到網路，您可能會發現建置訊息，如下所示，在主控台輸出中，因為 Ant 產生器會驗證下載的可用性:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

如果您選取 **從下載部署...** 選項，可能會顯示下列警告，但仍會繼續建置:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

只有這個警告會指出尚未驗證下載的可用性。 因此如果雲端中的部署因為某些原因失敗，請檢查是否有收到這個警告。

如果您想要停用下載驗證 (例如，如果您認為進行驗證只會拖慢建置速度)，請在 package.xml 的 `<windowsazurepackage>` 項目中，將 `verifydownloads` 屬性設定為 `false`。 

`<windowsazurepackage verifydownloads="false" ...>` 

如果您選取 **自動上傳...** 選項，然後在主控台視窗中，您會看到建置訊息報告上傳每隔 5 秒，每當需要上傳時的進度。

<a name="ssl_offloading_properties"></a> 
### SSL 卸載屬性 ###

在 Eclipse 的專案總管] 窗格中，角色的內容功能表中開啟按一下 **Azure**, ，然後按一下 [ **SSL 卸載**。 

![][ic719481]

在這個對話方塊中，您可以啟用 SSL 卸載，以便在 Azure 上的 Java 部署中輕鬆啟用超文字安全傳輸通訊協定 (HTTPS) 支援，而不必在 Java 應用程式伺服器中設定 SSL。 如需詳細資訊，請參閱 [SSL 卸載][] 和 [如何使用 SSL 卸載][]。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[安裝 Azure Toolkit for Eclipse][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[Azure 專案屬性][]

[Azure 儲存體帳戶清單][]

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Project Properties]: http://go.microsoft.com/fwlink/?LinkID=699524
[Azure Storage Account List]: http://go.microsoft.com/fwlink/?LinkID=699528
[com.microsoft.windowsazure.serviceruntime package summary]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Debugging a specific role instance in a multi-instance deployment]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Debugging Azure Applications in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Deploying Large Deployments]: http://go.microsoft.com/fwlink/?LinkID=699536
[How to Use Co-located Caching]: http://go.microsoft.com/fwlink/?LinkID=699542
[How to Use SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699548
[SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

