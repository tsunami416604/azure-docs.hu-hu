<properties
   pageTitle="Azure AD Connect 同步處理：Lotus Domino 連接器"
   description="本文說明如何設定 Microsoft 的 Lotus Domino 連接器。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/16/2015"
   ms.author="andkjell"/>

# Lotus Domino 連接器技術參考

本文說明 Lotus Domino 連接器。 本文適用於下列產品：

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   必須使用 hotfix 4.1.3461.0 或更新版本 [KB2870703](https://support.microsoft.com/kb/2870703)。

對於 MIM2016 和 FIM2010R2 連接器是可以從下載 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=717495)。

## Lotus Domino 連接器概觀

Lotus Domino 連接器可讓您整合同步處理服務與 IBM 的 Lotus Domino 伺服器。

目前的連接器版本大致支援下列功能：

| 功能 | 支援 |
| --- | --- |
| 連接的資料來源 | 伺服器： <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>用戶端 ︰<li>Lotus Notes 8.5.x</li><li>Lotus Notes 9.x</li> |
| 案例 | <li>物件生命週期管理</li><li>群組管理</li><li>密碼管理</li> |
| 作業 | <li>完整和差異匯入</li><li>匯出</li><li>設定及變更密碼 HTTP 密碼</li> |
| 結構描述 | <li>人員 （漫遊使用者、 連絡人 （沒有憑證的人員））</li><li>群組</li><li>資源 （資源的空間，線上會議）</li><li>郵件中的資料庫</li><li>支援的物件之屬性的動態探索</li> |

Lotus Domino 連接器利用 Lotus Notes 用戶端與 Lotus Domino 伺服器進行通訊。 因此，同步處理伺服器上必須安裝支援的 Lotus Notes 用戶端。 用戶端與伺服器之間的通訊是透過 Lotus Notes .NET Interop (Interop.domino.dll) 介面來實作。 此介面可協助 Microsoft.NET 平台和 Lotus Notes 用戶端之間的通訊，並支援 Lotus Domino 文件和檢視的存取。 在執行差異匯入時，也可以使用 C++ 原生介面 (取決於所選的差異匯入方法)。

### 必要條件

在您使用連接器之前，請確定除了任何先前提到的 Hotfix 以外，同步處理伺服器上還有下列項目：

- Microsoft .NET 4.5.2 Framework 或更新版本
- 同步處理伺服器上必須安裝 Lotus Notes 用戶端
- 要使用 Lotus Domino 連接器，Domino 目錄伺服器上就必須要有預設的 Lotus Domino LDAP 結構描述資料庫 (schema.nsf)。 您必須確認它是否已存在。 如果不存在，您可以在 Domino 伺服器上執行或重新啟動 LDAP 服務來加以安裝。

### 連接的資料來源權限

若要在 Lotus Domino 連接器上執行任何支援的工作，您必須是下列群組的成員：

- 完整存取權系統管理員
- 系統管理員
- 資料庫系統管理員

下表列出每個作業所需的權限：

| 作業 | 存取權限 |
| --- | --- |
| Import | <li>讀取公用文件</li><li> 完整存取 」 系統管理員 （完整存取權的系統管理員群組的成員時，您將自動需要有效的存取權 ACL 中）。</li> |
| 匯出和設定密碼 | 有效的存取權 ︰ <li>建立文件</li><li>刪除文件</li><li>讀取公用文件</li><li>撰寫公用文件</li><li>複寫或複製文件</li>除了上述的存取，必須指派下列角色的匯出作業 ︰ <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### 直接作業和 AdminP

作業會直接進入 Domino 目錄或透過 AdminP 程序來進行。 下表列出所有支援的物件、作業以及相關的實作方法 (如果適用的話)：

**主要通訊錄**

| Object | 建立 | 更新 | 刪除 |
| --- | --- | --- | --- |
| Person | AdminP | 直接 | AdminP |
| 群組 | AdminP | 直接 | AdminP |
| MailInDB | 直接 | 直接 | 直接 |
| 資源 | AdminP | 直接 | AdminP |

**次要通訊錄**

| Object | 建立 | 更新 | 刪除 |
| --- | --- | --- | --- |
| Person | N/A | 直接 | 直接 |
| 群組 | 直接 | 直接 | 直接 |
| MailInDB | 直接 | 直接 | 直接 |
| 資源 | N/A | N/A | N/A |

建立資源時會建立 Notes 文件。 同樣地，刪除資源時也會刪除 Notes 文件。

### 連接埠和通訊協定

IBM Lotus Notes 用戶端和 Domino 伺服器使用 Notes Remote Procedure Call (NRPC) 進行通訊，其中 NRPC 應使用 TCP/IP。 預設連接埠號碼是 1352，但 Domino 系統管理員可加以變更。

### 不支援

目前的 Lotus Domino 連接器版本不支援下列作業：

- 在伺服器之間移動信箱。

## 建立新的連接器

### 用戶端軟體安裝和設定

必須在伺服器上安裝 lotus Notes **之前** 安裝連接器。

當您安裝時，請務必先完成 **單一使用者安裝**。 預設 **多使用者安裝** 將無法運作。

![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

在 [功能] 頁面上安裝的必要的 Lotus Notes 功能和 **用戶端的單一登入**。 必須要有單一登入，連接器才能登入 Domino 伺服器。

![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**注意 ︰** 與使用者帳戶相同的伺服器上使用做為連接器的服務帳戶之後，您必須啟動 Lotus Notes。

### 建立連接器

若要建立 Lotus Domino 連接器，在 **同步處理服務** 選取 **管理代理程式** 和 **建立**。 選取 **Lotus Domino (Microsoft)** 連接器。

![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

如果您的同步處理服務版本會提供可設定的功能 **架構**, ，請確定連接器設為預設值，以執行 **程序**。

### 連線能力

在 [連線能力] 頁面中，您必須指定 Lotus Domino 伺服器名稱，然後輸入登入認證。

![連線能力](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

Domino 伺服器屬性支援兩種伺服器名稱格式：

- ServerName
- ServerName/DirectoryName

 **ServerName/DirectoryName** 格式是慣用的格式，此屬性，因為連接器連絡 Domino 伺服器時，它會提供更快速的回應。

提供使用者識別碼的檔案會儲存在組態資料庫的同步處理服務。

如 **差異匯入** 有下列選項 ︰

- **無**。 連接器不會執行任何差異匯入。
- **新增/更新**。 連接器會差異匯入新增和更新作業。 刪除 **完整匯入** 是必要的作業。 這項作業會使用 Notes 的 .Net Interop。
- **加入/更新/刪除**。 連接器會差異匯入新增、更新和刪除作業。 這項作業會使用 Notes 的原生 C++ 介面。

在 **結構描述選項** 有下列選項 ︰

- **預設結構描述**。 此為預設選項，連接器將會偵測 Domino 伺服器中的結構描述。
- **DSML 結構描述**。 只在 Domino 伺服器不公開結構描述時使用。 然後，您可以使用此結構描述建立 DSML 檔案，並匯入此檔案。 如需 DSML 詳細資訊，請參閱 [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml)。

當您按 [下一步] 時，就會驗證使用者識別碼和密碼組態參數。

### 全域參數

在 [全域參數] 頁面中，您可以設定時區與匯入和匯出作業的選項。

![全域參數](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

 **Domino 伺服器時區** 參數會定義 Domino 伺服器的位置。

這個組態選項才能支援 **差異匯入** 作業因為它可讓同步處理服務判斷之間最後兩個匯入的變更。

#### 匯入設定、方法

 **執行完整匯入的** 會出現這些選項 ︰

- 搜尋
- 檢視 (建議選項)

**搜尋** 是使用索引 Domino，但它是很常見，索引也不會即時更新，並從伺服器傳回的資料不一定正確。  對於具有許多變更的系統，此選項通常不會運作得很好，並且會在某些情況下造成誤刪。 不過， **搜尋** 的速度比 **檢視**。

**檢視** 是建議的選項，因為它提供資料的正確狀態。 其速度比 [搜尋] 略慢。

#### 建立虛擬連絡人物件

 **啟用 \_Contact 物件建立** 會出現這些選項 ︰

- None
- 非參考值
- 參考和非參考值

在 Domino 中，參考屬性可包含許多不同的格式，以參考其他物件。 若要能夠代表不同的變化連接器實作 \_Contact 物件又稱為 以虛擬的連絡人 (VC)。 這些物件已建立，因此可以加入到現有 MV 物件並形成新的物件，藉此方式保留參考屬性。

啟用此設定，並參考屬性的內容不是以 DN 格式，如果建立 \_Contact 物件。 例如，群組的成員屬性可以包含 SMTP 位址。 此外，參考屬性中也可以有 shortName 和其他屬性。 針對此案例中，選取 **非參考值**。 這是 Domino 實作最常使用的設定。

Lotus Domino 已設定為個別的通訊錄代表同一個物件的辨別名稱不同，時，同時建立 \_Contact 通訊錄中找到的所有參考值的物件。 針對此案例中，選取 **參照及非參照值** 選項。

如果您有多個值的屬性中 **FullName** Domino 中再也想要啟用虛擬連絡人的建立，因此可以解析參考。 合併或分離之後，這個屬性就可以具有多個值。 針對此案例，請選取 [讓 ****FullName 能夠具有多個值] 核取方塊。

加入正確的屬性，\_Contact 物件會加入到 MV 物件。

這些物件會有 VC = \_Contact 加入至其 DN。

#### 匯入設定、衝突物件

**排除衝突物件**

在大型 Domino 實作中，可能會因為複寫問題而讓多個物件具有相同 DN。 在這些情況下，連接器會看到兩個物件雖有不同 UniversalID，卻有相同 DN。 這會導致連接器空間中建立了暫時性物件。 連接器可以忽略 Domino 中已選取做為複寫犧牲者的物件。 建議您保持選取此核取方塊。

#### 匯出設定

如果選擇 **使用 AdminP 更新參考** 未選取，然後匯出參考屬性，例如成員，將會直接呼叫，而且不會使用 AdminP 程序。 只有在 AdminP 未設定成維護參考完整性時，才應該使用此選項。

#### 路由資訊

在 Domino 中，參考屬性可能具有內嵌為 DN 尾碼的路由資訊。 例如在群組中的成員屬性可包含 **CN=example/organization@ABC**。 尾碼 @ABC 就是路由資訊。 Domino 使用路由資訊來傳送電子郵件給正確的 Domino 系統，而此系統可能是位於不同組織的系統。 在 [路由資訊] 欄位中，您可以指定組織內所使用、且在連接器範圍內的路由尾碼。 如果在參考屬性中發現有任何一個值做為其尾碼，便會從參考中移除路由資訊，以讓其符合連接器空間中物件的 DN。 如果路由的後置字元的參考值無法對應到所指定的其中一個，則會建立 \_Contact 物件。 這些 \_Contact 物件將會使用建立 **RO = @<RoutingSuffix>** 插入 DN。 這些物件的下列屬性也會新增至允許加入實際的物件，如有必要 \_Contact: \_routingName、 \_contactName、 \_displayName 和 UniversalID。

#### 其他通訊錄

如果您不需要 **協助** 安裝，可讓次要通訊錄的名稱，然後您可以手動輸入這些通訊錄。

#### 多重值的轉換

Lotus Domino 中有許多屬性具有多重值。 相對應的 Metaverse 屬性則通常是單一值。 藉由設定匯入和匯出作業選項，您可以啟用連接器來協助受影響的屬性進行必要轉譯，這麼做將能簡化組態。

**匯出**

匯出作業選項支援兩種模式：

- 附加項目
- 取代項目

**取代項目** – 當您選取此選項時，連接器會一律 Domino 中移除之屬性的目前值並取代所提供的值。 所提供的值可以是單一值或多重值。

範例：
person 物件的 Assistant 屬性具有下列值：

- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

如果新的小幫手，名為 **David 亞歷山大** 指派至此 person 物件，則結果會是 ︰

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf

**將項目附加** – 當您選取此選項時，會保留現有的值在 Domino 屬性上的連接器，並將其資料清單的上方插入新的值。

範例：
person 物件的 Assistant 屬性具有下列值：

- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

如果新的小幫手，名為 **David 亞歷山大** 指派至此 person 物件，則結果會是 ︰

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf
- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

**Import**

匯出作業選項支援兩種模式：

- 預設值
- 多重值轉單一值

**預設** – 當您選取預設選項，將匯入所有屬性的所有值。

**多重值的單一值** – 當您選取此選項時，多重值的屬性會轉換成單一值的屬性。 如果有多個值存在，則會使用最頂端的值 (這通常也是最新值)。

範例：

person 物件的 Assistant 屬性具有下列值：

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf
- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

最新的更新，這個屬性是 **David 亞歷山大**。 匯入作業選項設定為單一值的多重數值，因為連接器只會匯入 **David 亞歷山大** 帶入連接器空間。

將多重值屬性轉換成單一值屬性的邏輯並不適用於群組物件的 member 屬性和 person 物件的 fullname 屬性。

您也可以針對每個屬性設定多重值屬性的匯入和匯出轉換規則，但全域規則除外。 若要設定此選項，輸入 [save]。[attributename] 在 **匯入排除屬性清單** 和 **匯出排除屬性清單** 文字方塊。 例如，如果您輸入 Person.Assistant，且全域旗標設為匯入所有值，則只會匯入第一個值做為助理。

#### 認證者

所有組織/組織單位皆會列出。 為了能夠將 person 物件匯出至主要通訊錄，必須要有認證者及其密碼。

 **密碼所有 Certifers(s)** 適用於所有 certifiers 都具有相同的密碼。 然後您可以在此輸入密碼，並只指定認證者檔案。

如果您只要匯入，則不必指定任何認證者。

### 設定佈建階層

在設定 Lotus Domino 連接器時可以略過此對話方塊頁面。 這是因為 Lotus Domino 連接器不支援階層佈建。

![佈建階層](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### 設定資料分割和階層

在設定資料分割和階層時，您必須選取名為 NAB=names.nsf 的主要通訊錄。

除了主要通訊錄，您還可以選取次要通訊錄 (如果有的話)。

![分割數](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### 選取屬性

當您設定您的屬性時，您必須選取前面會加上的所有屬性 **\_MMS\_**。 在對 Lotus Domino 佈建新物件時，必須要有這些屬性。

![屬性](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## 物件生命週期管理

本節概述 Domino 中的不同物件。

### Person 物件

Person 物件代表組織和組織單位中的使用者。 除了預設屬性，Domino 系統管理員還可以在 Person 物件中新增自訂屬性。 Person 物件至少必須包含所有必要屬性。 必要屬性的完整清單，請參閱 [Lotus 附註屬性](#lotus-notes-properties)。 若要註冊 person 物件，必須符合下列必要條件：

- 應該已定義通訊錄 (names.nsf)，而且它應該是主要通訊錄。
- 您應該擁有 O/OU 認證者識別碼和密碼，以在組織/組織單位中註冊特定使用者。
- 您需要為 person 物件定義一組特定的 Lotus Notes 屬性。 這些屬性會用來佈建 person 物件。 如需詳細資訊，請參閱 < 呼叫 [Lotus 附註屬性](#lotus-notes-properties) 稍後在本文件中。
- person 的初始 HTTP 密碼是一個屬性，且已在佈建期間設定好。
- person 物件必須是下列三種支援類型之一：
    1. 具有郵件檔案和使用者識別碼檔案的一般使用者
    2. 漫遊使用者 (包含所有漫遊資料庫檔案的一般使用者)
    3. 連絡人 (沒有識別碼檔案的使用者)

（除了連絡人） 的人員進一步分組到美國的使用者和國際使用者，\_MMS\_IDRegType 屬性的值所定義。 這些人是使用 Notes 用戶端來存取 Lotus Domino 伺服器和資料庫、擁有 Notes Id 和 Person 文件的人員。 如果這些人使用 Notes 郵件，那麼他們也會有郵件檔案。 使用者必須經過註冊才能生效。 如需詳細資訊，請參閱：

- [設定 Notes 使用者](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [使用者註冊](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [管理使用者](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [重新命名使用者](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

這些作業全都會在 Lotus Domino 中執行，然後匯入至同步處理服務。

### 資源和會議室

資源是 Lotus Domino 中的另一種資料庫。 資源可以是備有各種設備的會議室，例如投影機。 Lotus Domino 連接器也支援子類型資源，其定義是以資源類型屬性為基礎：

| 資源類型 | 資源類型屬性 |
| --- | --- |
| 會議室 | 1 |
| 資源 (其他) | 2 |
| 線上會議 | 3 |

要讓資源物件類型運作，必須具備下列項目：

- 連接的 Domino 伺服器中應該已有資源保留資料庫
- 已為資源定義網站

資源保留資料庫包含 3 種類型的文件：

- 網站設定檔
- 資源
- 保留

如需設定的資源保留資料庫的詳細資訊，請參閱 [設定資源保留項目資料庫](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)。

**建立、更新和刪除資源**

Lotus Domino 連接器會在資源保留資料庫中執行建立、更新和刪除作業。 因此，會將資源建立為 Names.nsf 中的文件 (也就是主要通訊錄)。 如需有關編輯和刪除資源的詳細資訊，請參閱 [編輯和刪除資源文件](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html)。

**資源的匯入和匯出作業**

和其他任何物件類型一樣，您可以在同步處理服務中匯入和匯出資源。 您應該在設定期間選取物件類型做為資源。 為了成功執行匯出作業，您應該具有資源類型、會議資料庫和網站名稱的詳細資料。

### 郵寄資料庫

郵寄資料庫是設計來接收郵件的資料庫。 此資料庫是未與任何特定 Lotus Domino 使用者帳戶相關聯 (也就是沒有自己的識別碼檔案和密碼) 的 Lotus Domino 信箱 (以郵件範本為基礎的 Notes 資料庫)。 郵寄資料庫具有相關聯的唯一 UserID (「簡短名稱」)，並有自己的電子郵件地址。

每個使用者只需要一個 Lotus Domino 帳戶。 是否需要個別信箱自己可以在不同的使用者之間共用的電子郵件地址 (例如 ︰ group@contoso.com)，而非其他的備忘稿帳戶建立郵件中資料庫。 此信箱是透過其存取控制清單 (ACL) 來控制存取，此清單中包含允許其使用自己的識別碼檔案和密碼開啟信箱之 Notes 使用者的名稱。 這些使用者不需要另外的密碼就能存取郵寄資料庫。

必要屬性的清單，請參閱稱為區段 [強制屬性](#mandatory-attributes) 在本文稍後。

設計資料庫來接收郵件時，就會在 Lotus Domino 中建立郵寄資料庫文件。 儲存資料庫複本的每一部伺服器的 Domino 目錄中，都必須要有此文件。 如需建立資料庫郵件中的文件的詳細說明請參閱 [建立郵件中的資料庫文件](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html)。

在建立郵寄資料庫之前，Domino 伺服器上應該要已有此資料庫 (應該已由 Lotus 系統管理員建立)。

### 群組管理

您可以從下列資源取得 Lotus Domino 群組管理的詳細概觀：

- [使用群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [建立群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [建立和修改群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [管理群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [重新命名群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### 密碼管理

已註冊的 Lotus Domino 使用者有兩種類型的密碼：

1. 使用者密碼 (儲存在 User.id 檔案)
2. 網際網路/HTTP 密碼

Lotus Domino 連接器只支援使用 HTTP 密碼的作業。

若要執行密碼管理，您應該在 Management Agent Designer 中啟用連接器的密碼管理。 若要啟用密碼管理，請選取 **啟用密碼管理** 上 **設定延伸模組** 對話方塊頁面。

![設定延伸模組](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Lotus Domino 連接器支援下列關於網際網路密碼的作業：

- 設定密碼：設定密碼會對 Domino 使用者設定新的 HTTP/網際網路密碼。 依預設也會解除鎖定帳戶。 同步處理引擎的 WMI 介面上會顯示解除鎖定旗標。
- 變更密碼：在此案例中，使用者可能想要變更密碼，或收到在指定時間後變更密碼的提示。 若要讓這項作業進行，必須同時擁有兩者 (舊密碼和新密碼)。 一旦變更，就會在 Lotus Domino 中更新新的密碼。

如需詳細資訊，請參閱：

- [使用網際網路鎖定功能](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [管理網際網路密碼](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## 參考資訊

本節列出 Lotus Domino 連接器的屬性描述和屬性需求等資訊。

### Lotus Notes 屬性

在將 Person 物件佈建到 Lotus Domino 目錄時，物件必須有一組已填入特定值的特定屬性。 只有建立作業才需要這些值。

下表列出這些屬性，並提供其描述。

| 屬性 | 說明 |
| --- | --- |
| \_MMS_AltFullName | 使用者的替代完整名稱。 |
| \_MMS_AltFullNameLanguage | 要用來指定使用者替代完整名稱的語言。 |
| \_MMS_CertDaysToExpire | 從當日起算的憑證到期前天數。 若未指定，則預設日期是當日起算兩年後。 |
| \_MMS_Certifier | 包含認證者組織階層名稱的屬性。 例如：OU=OrganizationUnit,O=Org,C=Country。 |
| \_MMS_IDPath | 如果屬性是空的，則不會在同步處理伺服器本機上建立使用者識別碼檔案。 如果屬性包含檔案名稱，則會在 madata 資料夾中建立使用者識別碼檔案。 屬性也可以包含使用者識別碼檔案建立所在位置的完整路徑。 |
| \_MMS_IDRegType | 人員可以分類為連絡人。 美國使用者和國際使用者。 下表列出可能的值 ︰<li>0-連絡人</li><li>1-美國 user</li><li>2-國際使用者</li> |
| \_MMS_IDStoreType | 美國的必要的屬性 和國際使用者。 此屬性包含整數值，可指定要將使用者識別碼儲存為 Notes 通訊錄中的附件，還是儲存在人員的郵件檔案中。 如果使用者識別碼檔案通訊錄中的附件，它可以選擇性地建立為 \_MMS_IDPath 的檔案。<li>空的識別碼 （用於 「 連絡人 」） 沒有識別檔案保存庫中的存放區識別碼檔案。</li><li> 1 - Notes 通訊錄中的附件。 \_MMS_Password 屬性必須為使用者識別檔案附件</li><li>2-識別碼儲存人員的郵件檔案中。 \_MMS_UseAdminP 必須設定為 false，可讓使用者註冊期間建立的郵件檔案。\_MMS_Password 屬性必須為使用者識別檔案。</li>
| \_MMS_MailQuotaSizeLimit | 電子郵件檔案資料庫允許使用的 MB 數。 |
| \_MMS_MailQuotaWarningThreshold | 電子郵件檔案資料庫允許使用的 MB 數，超過之後就會發出警告。 |
| \_MMS_MailTemplateName | 用來建立使用者的電子郵件檔案的電子郵件範本檔案。 如果有指定範本，則會使用指定的範本建立郵件檔案。 如果未指定範本，則會使用預設範本檔案來建立檔案。 |
| \_MMS_OU | 選擇性屬性，此為認證者底下的 OU 名稱。 連絡人的這個屬性應該是空的。 |
| \_MMS_Password | 使用者的必要屬性。 此屬性包含物件識別碼檔案的密碼。 |
| \_MMS_UseAdminP | 如果應該要由 AdminP 程序在 Domino 伺服器上建立郵件檔案 (與匯出程序不同步)，則此屬性應該設定為 true。 如果此屬性設定為 false，則會為 Domino 使用者建立郵件檔案 (在匯出程序中同步進行)。 |

具有相關聯的識別檔案的使用者，\_MMS_Password 屬性必須包含一個值。 若透過 Lotus Notes 用戶端存取電子郵件，使用者的 MailServer 和 MailFile 屬性必須包含值。

若要透過網頁瀏覽器存取電子郵件，下列屬性必須包含值：

- MailFile - 必要屬性，其包含郵件檔案在 Lotus Domino 伺服器上儲存所在的路徑。
- MailServer - 必要屬性，其包含 Lotus Domino 伺服器的名稱。 這是在 Domino 伺服器上建立 Lotus 郵件檔案時要使用的名稱。
- HTTPPassword - 選擇性屬性，其包含物件的 Web 存取密碼。

若要存取不具備郵件功能的 Domino 伺服器，HTTPPassword 屬性必須包含值，且 MailFile 屬性和 MailServer 屬性可以是空的。

使用 \_MMS_ IDStoreType = 2 （存放區識別碼郵件檔案中），NotesRegistrationclass MailSystem 屬性會設定為 REG_MAILSYSTEM_INOTES (3)。

### 必要屬性

Lotus Domino 連接器在同步處理服務中主要支援四種類型的物件 (文件類型)：

- 群組
- 郵寄資料庫
- Person
- 連絡人 (沒有認證者的人員)
- 資源

本節列出要將物件匯出至 Domino 伺服器時，每個支援物件類型的必要屬性。

| 物件類型 | 必要屬性 |
| --- | --- |
| 群組 | <li>ListName</li> |
| 郵寄資料庫 | <li>FullName</li><li>MailFile</li><li>Mail 伺服器</li><li>MailDomain</li> |
| Person | <li>姓氏</li><li>MailFile</li><li>簡短名稱</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>_MMS_UseAdminP</li> |
| 連絡人 (沒有認證者的人員) | <li>\_MMS_IDRegType</li> |
| 資源 | <li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>網站</li><li>DisplayName</li><li>MailFile</li><li>Mail 伺服器</li><li>MailDomain</li> |

## 常見問題

### 結構描述偵測沒有作用

若要能夠偵測結構描述，Domino 伺服器上必須要有 schema.nsf 檔案存在。 伺服器上安裝了 LDAP 後，才會出現這個檔案。 如果偵測不到結構描述，請確認下列事項：

- Domino 伺服器的根資料夾內有 schema.nsf 檔案
- 使用者有權限能夠看到 schema.nsf 檔案。
- 強制重新啟動 LDAP 伺服器。 開啟 Lotus Domino 主控台，並使用 ‘Tell LDAP ReloadSchema’ 命令來重新載入結構描述。

### 無法看見所有次要通訊錄

Domino 連接器需依賴 Directory Assistance 功能才能找到次要通訊錄。 如果遺漏次要通訊錄，請確認如果 [協助](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) 已啟用並設定 Domino 伺服器上。

### Domino 中的自訂屬性

Domino 中有數種方式可延伸結構描述，使其顯示為連接器可使用的自訂屬性。

**方法 1：延伸 Lotus Domino 結構描述**

1. 建立一份 Domino 目錄範本 < PUBNAMES。NTF > 依照 [依照](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) （您不應該自訂範本的預設 IBM Lotus Domino 目錄） ︰
2. 開啟複製的 Domino 目錄範本 < CONTOSO。NTF > 範本只是範例中會建立 Domino 設計工具，然後遵循下列步驟 ︰
    - 按一下 [共用項目]，然後展開子表單
    - 按兩下 [$[ObjectName]InheritableSchema] 子表單 (其中 [ObjectName] 是預設結構化物件類別的名稱，例如 Person)。
    - Name 的屬性，您想要新增到 [MyPersonAtrribute] 結構描述和對應的建立欄位選取 [建立] 功能表，然後從功能表選取 'Field'。
    - 在新增的欄位中，於欄位 [屬性] 視窗上選取其類型、樣式、大小、字型和其他相關參數，以設定其屬性。
    - 讓屬性預設值和提供給該屬性的名稱保持相同 (例如，如果屬性名稱是 MyPersonAttribute，請讓預設值保有相同名稱)。
    - 以更新後的值儲存 $[ObjectName]InheritableSchema 子表單
3. 取代 Domino 目錄範本 < PUBNAMES。NTF > 與新的自訂範本 [CONTOSO。NTF] 依照 [步驟](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html)。
4. 關閉 Domino Admin，然後開啟 Domino 主控台以重新啟動 LDAP 服務，並重新載入 LDAP 結構描述：
    - Domino 主控台中插入下的命令 **Domino 命令** 文字無法重新啟動 LDAP 服務- [重新啟動工作 LDAP]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)。
    - 若要重新載入 LDAP 結構描述，請使用 Tell LDAP 命令 - Tell LDAP ReloadSchema
5. 開啟 Domino Admin，並選取 [人員和群組] 索引標籤以查看新增的屬性是否已在 Domino 新增人員中反映
6. 從 [檔案] 索引標籤開啟 Schema.nsf，並查看新增的屬性是否已反映到 dominoPerson LDAP 物件類別

**方法 2：以自訂屬性建立 auxClass 並與物件類別關聯**

1. 建立一份 Domino 目錄範本 [PUBNAMES。NTF] 依照 [依照]()http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html （永遠不會自訂範本的預設 IBM Lotus Domino 目錄） ︰
2. 在 Domino Designer 中開啟剛才建立的 Domino 目錄範本 [CONTOSO.NTF]。
3. 在左窗格中，依序選取 [共用程式碼] 和 [子表單]。
4. 按一下 [新增子表單]
5. 執行下列動作來指定新子表單的屬性：
    - 在新子表單開啟時，選擇 [設計] > [子表單屬性]
    - 在 [名稱] 屬性旁，輸入輔助物件類別的名稱，例如 TestSubform。
    - 讓 [選項] 屬性 [包含在插入子表單...對話方塊] 保持選取狀態
    - 取消選取 [選項] 屬性 [在 Notes 中呈現傳遞 HTML]。
    - 讓其他屬性保持相同，然後關閉 [子表單屬性] 方塊。
    - 儲存並關閉新的子表單。
6. 執行下列動作來新增欄位，以定義輔助物件類別：
    - 開啟剛才建立的子表單。
    - 選擇 [建立] > [欄位]。
    - 在 [欄位] 對話方塊的 [基本] 索引標籤上的 [名稱] 旁，指定任何名稱，例如 [MyPersonTestAttribute]。
    - 在新增的欄位中，選取其類型、樣式、大小、字型和相關屬性，以設定其屬性。
    - 讓屬性預設值和提供給該屬性的名稱保持相同 (例如，如果屬性名稱是 MyPersonTestAttribute，請讓預設值保有相同名稱)。
    - 以更新後的值儲存子表單，然後執行下列動作：
        - 在左窗格中，依序選取 [共用程式碼] 和 [子表單]。
        - 選取新的子表單，然後選擇 [設計] > [設計屬性]。
        - 按一下左邊的第三個索引標籤，然後選取 **傳播的設計變更這項限制**。
7. 開啟 [$[ObjectName]ExtensibleSchema] 子表單 (其中 [ObjectName] 是預設結構化物件類別的名稱，例如 Person)。
8. 插入 [資源] 並選取子表單 (您剛才建立的，例如 TestSubform)，然後儲存 [$[ObjectName]ExtensibleSchema] 子表單。
9. 取代 Domino 目錄範本 [PUBNAMES。NTF] 的新自訂範本 [CONTOSO。NTF] 依照 [步驟](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html)。
10. 關閉 Domino Admin，然後開啟 Domino 主控台以重新啟動 LDAP 服務，並重新載入 LDAP 結構描述：
    - Domino 主控台中插入下的命令 **Domino 命令** 文字無法重新啟動 LDAP 服務- [重新啟動工作 LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)。
    - 若要重新載入 LDAP 結構描述，請使用 Tell LDAP 命令 - Tell LDAP ReloadSchema
11. 開啟 Domino Admin，並選取 [人員和群組] 索引標籤以查看新增的屬性是否已在 Domino 新增人員中反映 (於 [其他] 索引標籤下)
12. 開啟從 Schema.nsf **檔案** 索引標籤，請參閱加入的屬性會反映在輔助 TestSubform LDAP 物件類別下。

**方法 3：將自訂屬性新增至 ExtensibleObject 類別**

1. 開啟放在根目錄的 [Schema.nsf] 檔案
2. 從下的左功能表中選取 [LDAP 物件類別 **所有結構描述文件** ，然後按一下 **加入的物件類別** 按鈕 ︰
3. 以 [###ExtensibleSchema] 形式提供有關想要為其延伸結構描述之物件的 LDAP 名稱 (其中 ### 是預設結構化物件類別的名稱，例如 Person)。 例如，若要延伸 Person 物件類別的結構描述，請提供 LDAP 名稱 [PersonExtensibleSchema]。
4. 提供想要為其延伸結構描述的上層物件類別名稱。 例如，若要延伸 Person 物件類別的結構描述，請提供上層物件類別名稱 [dominoPerson]：
5. 指出對應到物件類別的有效 OID。
6. 根據需求選取 [必要屬性類型] 或 [選擇性屬性類型] 欄位之下的延伸/自訂屬性：
7. 將必要的屬性加入至 ExtensibleObjectClass 之後, 按一下 **儲存並關閉** ] 按鈕。
8. 系統會為各自的預設物件類別建立具有延伸屬性的 ExtensibleObjectClass。

## 疑難排解

-   如需如何啟用記錄來疑難排解連接器資訊，請參閱 [如何啟用 ETW 追蹤連接器](http://go.microsoft.com/fwlink/?LinkId=335731)。


