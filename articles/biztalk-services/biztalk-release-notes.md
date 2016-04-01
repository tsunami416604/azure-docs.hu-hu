<properties 
    pageTitle="Azure BizTalk 服務的版本資訊 | Microsoft Azure BizTalk 服務" 
    description="" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="msftman" 
    manager="dwrede" 
    editor="cgronlun"/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="deonhe"/>

# Azure BizTalk 服務的版本資訊

Microsoft Azure BizTalk 服務的版本資訊包含此版本已知的問題。

## BizTalk 服務 11 月份更新的新增功能
* BizTalk 服務入口網站中可以啟用靜態加密。 請參閱 [啟用 BizTalk 服務入口網站中的靜止的加密](https://msdn.microsoft.com/library/azure/dn874052.aspx)。

## 更新歷程記錄

### 10 月更新

* 支援組織帳戶：  
 * **案例**︰ 您註冊使用 Microsoft 帳戶 （如 user@live.com) 的 BizTalk 服務部署。 在此案例中，只有 Microsoft 帳戶使用者可以使用 BizTalk 服務入口網站來管理 BizTalk 服務。 無法使用組織帳戶。  
 * **案例**︰ 您註冊 BizTalk 服務部署在 Azure Active Directory （如 user@fabrikam.com 或 user@contoso.com） 中使用組織帳戶。 在此案例中，只有相同組織內的 Azure Active Directory 使用者可以使用 BizTalk 服務入口網站來管理 BizTalk 服務。 無法使用 Microsoft 帳戶。  
* 當您在 Azure 傳統入口網站建立 BizTalk 服務時，您會自動在 BizTalk 服務入口網站中註冊。
 * **案例**︰ 登入 Azure 傳統入口網站建立 BizTalk 服務，然後選取 **管理** 第一次。 BizTalk 服務入口網站開啟時，BizTalk 服務自會動註冊，並且可用於您的部署。  
 請參閱 [登錄及更新 BizTalk 服務部署在 BizTalk 服務入口網站](https://msdn.microsoft.com/library/azure/hh689837.aspx)。  

### 8 月 14 日更新
* 合約與橋接器分離 – 現在，交易夥伴合約和橋接器在 BizTalk 服務入口網站中會分離。 您現在分開建立合約和橋接器，而在執行階段，橋接器會根據 EDI 訊息中的值解析為合約。 請參閱 [Azure BizTalk 服務中建立的協議](https://msdn.microsoft.com/library/azure/hh689908.aspx), ，[建立使用 BizTalk 服務入口網站是 EDI 橋接器](https://msdn.microsoft.com/library/azure/dn793986.aspx), ，[建立 AS2 橋接器使用 BizTalk 服務入口網站](https://msdn.microsoft.com/library/azure/dn793993.aspx), ，和 [橋接器如何解析協議，在執行階段？](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* 用於建立合約範本的選項已終止。  
* 對於傳送端合約，您現在可以為每個結構描述指定不同的分隔符號集。 此設定是在傳送端合約的通訊協定設定下指定。 如需詳細資訊，請參閱 [建立 X12 協議中 Azure BizTalk 服務](https://msdn.microsoft.com/library/azure/hh689847.aspx) 和 [Azure BizTalk 服務中建立 EDIFACT 協議](https://msdn.microsoft.com/library/azure/dn606267.aspx)。 兩個新的實體也為了相同目的而新增至 TPM OM API。 請參閱 [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) 和 [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx)。  
* 現在支援標準 XSD 建構，包括衍生類型。 請參閱 [使用標準 XSD 建構您的對應中](https://msdn.microsoft.com/library/azure/dn793987.aspx) 和 [使用衍生類型中對應的案例和範例](https://msdn.microsoft.com/library/azure/dn793997.aspx)。  
* AS2 支援用於訊息簽署的新 MIC 演算法，以及新的加密演算法。 [請參閱在 Azure BizTalk 服務中建立 AS2 協議](https://msdn.microsoft.com/library/azure/hh689890.aspx)。  
## 已知問題

### BizTalk 服務入口網站更新之後的連線能力問題

  當升級 BizTalk 服務來導入服務的變更時，如果 BizTalk 服務入口網站中已開啟，BizTalk 服務入口網站的連線能力可能會發生問題。  
  解決方法是重新啟動瀏覽器、刪除瀏覽器快取，或在私用模式下啟動入口網站。  

### 如果您在 BizTalk 服務專案中按一下錯誤或警告，Visual Studio IDE 找不到構件
安裝 Visual Studio 2012 Update 3 RC 1 來修正問題。  

### 自訂繫結專案參考
假設 Visual Studio 方案中的 BizTalk 服務專案有下列情形：  
* 在相同的 Visual Studio 方案中，有一個 BizTalk 服務專案和一個自訂繫結專案。 BizTalk 服務專案參考此自訂繫結專案檔。 
* BizTalk 服務專案參考自訂繫結/行為 DLL。

您在 Visual Studio 中成功「建置」方案。 然後，您「重建」或「清除」方案。 之後，當您再次重建或清除，就會發生下列錯誤 ︰  
  無法複製檔案 <Path to DLL> 以 「 bin\Debug\FileName.dll 」。 由於已有另一個處理序正在使用該檔案，所以無法存取該檔案。  

#### 因應措施
* 如果 [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) 是安裝，您可以使用下列兩個選項 ︰

  * 重新啟動 Visual Studio，或

  * 重新啟動方案。 然後，在方案上只執行「建置」。  

* 如果 [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) 不是已安裝的開啟工作管理員] 中，按一下處理程序] 索引標籤上，按一下 MSBuild.exe 程序，然後按一下 [結束處理程序] 按鈕。  

### 如果不可列印字元升級為 HTTP 標頭，則不支援從橋接器和 BizTalk 服務入口網站路由傳送至 BasicHttpRelay 端點

如果您在訊息的升級屬性中使用不可列印字元，這些訊息無法路由傳送至使用 BasicHttpRelay 繫結的轉送目的地。 此外，可供追蹤的升級屬性對 blob 而言是 URL 編碼，對目的地而言是未編碼。  

### 即使未核取 [傳送非同步 MDN] 選項，也會以非同步方式傳送 MDN  
請考慮此案例中 – 如果您選取 **傳送非同步 MDN** 核取方塊，指定要傳送非同步 MDN 的 URL，然後取消核取 **傳送非同步 MDN** 核取方塊，將 MDN 仍會傳送至指定的 URL 即使未選取此選項將傳送非同步 Mdn。  
解決方法是，您必須清除指定的 URL，再取消勾選 **傳送非同步 MDN** 核取方塊，然後部署 AS2 協議。  

### 超出有效交換的空白字元會導致空的訊息傳送至暫停端點  
如果有超出 IEA 區段的空格，解譯器會將此情形視為目前交換結束，並查看下一組空白字元當做下一個訊息。 因為這不是有效的交換，您可能會發現一個成功的訊息傳送至路由目的地，還有一個空的訊息傳送至暫停端點。  
### 在 BizTalk 服務入口網站中追蹤  
擷取的追蹤事件以 EDI 訊息處理和任何相互關聯為範圍。 如果訊息在通訊協定階段之外失敗，追蹤會顯示為成功。 在此情況下，請參閱底下的 [記錄] 區段 **詳細資料** 中的資料行 **追蹤** 的錯誤詳細資料。
X12 接收和傳送設定 ([建立 X12 協議中 Azure BizTalk 服務](https://msdn.microsoft.com/library/azure/hh689847.aspx)) 提供的通訊協定階段的相關資訊。  

### 更新合約  
BizTalk 服務入口網站可讓您在設定合約時修改身分識別的辨識符號。 這會導致屬性不一致。 比方說，合約使用 ZZ:1234567，而辨識符號為 ZZ:7654321。 在 BizTalk 服務入口網站設定檔設定中，您將 ZZ:1234567 變更為 01:ChangedValue。 您開啟合約，顯示的是 01:ChangedValue 而非 ZZ:1234567。
若要修改身分識別的辨識符號，刪除協議，請更新 **識別** 夥伴設定檔，然後重新建立協議中。  
> AZURE.WARNING 此行為影響 X12 和 AS2。  

### AS2 附件  
傳送和接收時不支援 AS2 訊息的附件。 具體來說，將會自動忽略附件，並將訊息內文當成一般 AS2 訊息來處理。  
### 資源：記住路徑  
當加入 **資源**, ，對話視窗可能不記得先前用來新增資源的路徑。 若要記得先前用過的路徑，請嘗試新增至 BizTalk 服務入口網站的網站 **受信任的網站** Internet Explorer 中。  
### 如果您重新命名橋接器的實體名稱，然後關閉專案而不儲存變更，則重新開啟實體會導致錯誤
依序假設有下列情況：  
* 將橋接器 (例如 XML 單向橋接器) 新增至 BizTalk 服務專案  

* 指定 [實體名稱] 屬性的值來重新命名橋接器。 這會以您指定的名稱來重新命名相關聯的 .bridgeconfig 檔案。  

* 關閉 .bcs 檔案 (藉由關閉 Visual Studio 中的索引標籤)，而不儲存變更。  

* 從 [方案總管] 再次開啟 .bcs 檔案。  
您會注意到相關聯的 .bridgeconfig 檔案具有您指定的新名稱，而在設計介面上的實體名稱還是舊名稱。 如果您嘗試按兩下橋接器元件，以開啟橋接器組態，您會收到下列錯誤 ︰  
  ‘<old name>' 實體的相關聯的檔案 '<old name>.bridgeconfig' 不存在  
若要避免發生這種情況下的，確定 BizTalk 服務專案中的實體重新命名之後，儲存變更。  
### 即使已經從 Visual Studio 專案中排除構件，BizTalk 服務專案仍會成功建置
假設您將構件 (例如，XSD 檔案) 新增至 BizTalk 服務專案，在橋接器組態中加入此構件 (例如，將它指定為要求訊息類型)，然後從 Visual Studio 專案中排除它。 在這種情況下，只要已刪除的構件仍留在它加入 Visual Studio 專案時的相同磁碟位置中，則建置專案不會發生任何錯誤。
### 設定橋接器時，BizTalk 服務專案不會檢查結構描述是否可用
在 BizTalk 服務專案中，如果加入至專案的結構描述又匯入另一個結構描述，BizTalk 服務專案不會檢查匯入的結構描述是否已加入至專案。 如果您嘗試建置此專案，則不會發生任何建置錯誤。
### XML 要求-回覆橋接器的回應訊息一律為 UTF-8 字元集
此版本中，來自 XML 要求-回覆橋接器的回應訊息的字元集永遠設定為 UTF-8。
### 使用者定義的資料類型
BizTalk Adapter Service 功能內的 BizTalk Adapter Pack 配接器可以在配接器作業中利用使用者定義的資料類型。
當利用使用者定義的資料類型時，請將檔案 (.dll) 複製到 drive:\Program Files\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\，或裝載 BizTalk Adapter Service 服務的伺服器上的全域組件快取 (GAC)。 否則，用戶端可能會發生下列錯誤：  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] It is recommended to use GACUtil.exe to install a file into the Global Assembly Cache. GACUtil.exe documents how to use this tool and the Visual Studio command line options.  

### Restarting the BizTalk Adapter Service Web Site
Installing the **BizTalk Adapter Service Runtime*** creates the **BizTalk Adapter Service** web site in IIS that contains the **BAService** application. **BAService** application internally uses relay binding to extend the reach of on-premise service endpoint to the cloud. For a service hosted on-premises, the corresponding relay endpoint will be registered on the Service Bus only when the on-premises service starts.  

If you stop and start an application, the configuration for auto-starting an application is not honored. So when **BAService** is stopped, you must always restart the **BizTalk Adapter Service** web site instead. Do not start or stop the **BAService** application.
### Special characters should not be used for address and entity names of LOB components
You should not use special characters for address and entity names of LOB components. If you do so, you will get an error while deploying the BizTalk Service project. For certain characters like ‘%’, the BizTalk Adapter Service website might go into a stopped state and you will have to manually start it.
### Test Map with Get Context Property
If a Transform contains a **Get Context Property** Map Operation, **Test Map** will fail. As a temporary workaround, replace the **Get Context Property** Map Operation with a String Concatenate Map Operation containing dummy data. This will populate the target schema and allow you test other Transform functionality.
### Test Map Property does not display
The **Test Map** properties do not display in Visual Studio. This can occur if the **Properties** window and the **Solution Explorer** window are not simultaneously docked. To resolve this, dock the **Properties** and the **Solution Explorer** windows.  
### DateTime Reformat drop-down is grayed out
When a DateTime Reformat Map Operation is added to the design surface and configured, the Format drop-down list may be grayed out. This can happen if the computer Display is set **Medium – 125%** or **Larger – 150%**. To resolve, set the display to **Smaller – 100% (default)** using the steps below:  
1. Open the **Control Panel** and click **Appearance and Personalization**.
2. Click **Display**.
3. Click **Smaller – 100% (default)** and click **Apply**.

The **Format** drop-down list should now work as expected.
### Duplicate agreements in the BizTalk Services Portal
Consider the following scenario:
1. Create an agreement using the Trading Partner Management OM API.
2. Open the agreement in the BizTalk Services Portal in two different tabs.
3. Deploy the agreement from both the tabs.
4. As a result, both the agreements get deployed resulting in duplicate entries in the BizTalk Services Portal

**Workaround**. Open any one of the duplicate agreements in the BizTalk Services Portal, and undeploy.  

### Bridges do not use updated certificate even after a certificate has been updated in the artifact store
Consider the following scenarios:  

**Scenario 1: Using thumbprint-based certificates for securing message transfer from a bridge to a service endpoint**  
Consider a scenario where you use thumbprint-based certificates in your BizTalk Service project. You update the certificate in the BizTalk Services Portal with the same name but a different thumbprint, but do not update the BizTalk Service project accordingly. In such a scenario, the bridge might continue to process the messages because the older certificate data might still be in the channel cache. After that, message processing fails.  

**Workaround**: Update the certificate in the BizTalk Service project and redeploy the project.  

**Scenario 2: Using name-based behaviors to identify certificates for securing message transfer from a bridge to a service endpoint** 

Consider a scenario where you use name-based behaviors to identify certificates in your BizTalk Service project. You update the certificate in the BizTalk Services Portal but do not update the BizTalk Service project accordingly. In such a scenario, the bridge might continue to process the messages because the older certificate data might still be in the channel cache. After that, message processing fails.  

**Workaround**: Update the certificate in the BizTalk Service project and redeploy the project.  

### Bridges continue to process messages even when the SQL database is offline
The BizTalk Services bridges continue to process messages for a while, even if the Microsoft Azure SQL Database (which stores the running information like deployed artifacts and pipelines), is offline. This is because BizTalk Services uses the cached artifacts and bridge configuration.
If you do not want the bridges to process any messages when the SQL Database is offline, you can use the BizTalk Services PowerShell cmdlets to stop or suspend the BizTalk Service. See [Azure BizTalk Service Management Sample](http://go.microsoft.com/fwlink/p/?LinkID=329019) for the Windows PowerShell cmdlets to manage operations.  
### Reading the XML message within a bridge’s custom code component includes an extra BOM character
Consider a scenario where you want to read an XML message within a bridge’s custom code. If you use the .NET API System.Text.Encoding.UTF8.GetString(bytes) an extra BOM character is included in the output at the beginning of the message. So, if you do not want the output to include the extra BOM character, you must use ```System.IO.StreamReader().ReadToEnd()```.
### Sending messages to a bridge using WCF does not scale
Messages sent to a bridge using WCF does not scale. You should instead use HttpWebRequest if you want a scalable client.
### UPGRADE: Token Provider error after upgrading from BizTalk Services Preview to General Availability (GA)
There is an EDI or AS2 Agreement with active batches. When the BizTalk Service is upgraded from Preview to GA, the following may occur:
* Error: The token provider was unable to provide a security token. Token provider returned message: The remote name could not be resolved.

* Batch tasks are canceled.

**Workaround**: After the BizTalk Service is updated to General Availability (GA), redeploy the agreement.  

### UPGRADE: Toolbox shows the old bridge icons after upgrading the BizTalk Services SDK
After you upgrade an earlier version of the BizTalk Services SDK, which had old icons representing the bridges, the toolbox continues to show the old icons for the bridges. However, if you add a bridge to BizTalk Service project designer surface, the surface shows the new icon.  

**Workaround**. You can work around this issue by deleting the .tbd files under <system drive>:\Users\<user>\AppData\Local\Microsoft\VisualStudio\11.0.  

### UPGRADE: BizTalk Portal update from Preview to GA might show an error indicating that the EDI capability is not available
If you are logged into the BizTalk Services Portal while the BizTalk Services is upgraded from Preview to GA, you might get the following error on the portal:  

This capability is not available as part of this edition of Microsoft Azure BizTalk Services. To use these capabilities switch to an appropriate edition.  

**Resolution**: Log out from the portal, close and open the browser, and then log into the portal.  
### UPGRADE: New tracking data does not show up after BizTalk Services is upgraded to GA
Assume a scenario where you have an XML bridge deployed on BizTalk Services Preview subscription. You send messages to the bridge and the corresponding tracking data is available on the BizTalk Services Portal. Now, if the BizTalk Services Portal and BizTalk Services runtime bits are upgraded to GA, and you send a message to the same bridge endpoint deployed earlier, the tracking data does not show up for messages sent after upgrade.  

### Pipelines v/s Bridges
Throughout this document, the term ‘pipelines’ and ‘bridges’ are used interchangeably. Both essentially mean the same thing, which is, a message processing unit deployed on BizTalk Services.  

### Concepts  

[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   





