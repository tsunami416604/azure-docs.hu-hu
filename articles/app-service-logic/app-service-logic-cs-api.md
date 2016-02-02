<properties
   pageTitle="C# API 應用程式"
   description="C# API 應用程式"
   services="app-service\logic"
   documentationCenter=".net"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/29/2015"
   ms.author="jehollan"/>


# C# API 應用程式

C# API 應用程式可讓您*在邏輯應用程式執行時*，輕鬆執行簡單的 C# 運算式。

## 何時應該使用此 API 應用程式？

使用此 API 應用程式的主要情況是，當您希望您撰寫的程式碼生命週期與邏輯應用程式相同，而且您「不」**想要在其他情況下呼叫程式碼。

另一方面，如果您希望程式碼可重複使用的片段與邏輯應用程式的週期獨立，則您應該使用 WebJobs API 應用程式來建立簡單的程式碼運算式，並從您的邏輯應用程式呼叫這些運算式。

最後，如果您想要包含任何其他封裝，您就必須以 Base64 編碼二進位字串 (類似 Blob 儲存體的輸出)，將組件 (.dll) 傳入連接器。 如果您想要封裝和組件有更大的彈性，WebJob 可能會是更好的選項。

使用 [JavaScript API 應用程式](app-service-logic-javascript-api.md) 如果您偏好以 JS 撰寫您的運算式。

## 建立 C# API 應用程式

若要使用 C# API 應用程式，您必須先建立 C# API 應用程式的執行個體。 這可以在建立邏輯應用程式時以內嵌方式完成，或是透過從 Azure Marketplace 選取 C# API 應用程式來完成。

## 在邏輯應用程式設計工具介面中使用 C# API 應用程式

### 觸發程序

您可以建立邏輯應用程式服務將輪詢 (以您定義的間隔)，觸發程序，它會傳回任何項目以外的其他 `false`, 、 邏輯應用程式會執行，否則會等到下一個輪詢間隔再檢查一次。

觸發程序的輸入包括:
- **C# 運算式**  -將會評估的運算式。 將函式內部叫用，而且必須傳回 `false` 時不想讓邏輯應用程式執行，並可傳回任何其他您想要執行的邏輯應用程式。 您就能夠在邏輯應用程式的動作中使用回應的內容。

例如，您有一個簡單的觸發程序，只會在每小時的 :15 和 :30 之間執行您的邏輯應用程式：

```
var d = new DateTime.Now; return (d.Minute > 15) && (d.Minute < 30);
```

### 動作

同樣地，您可以提供要執行的動作。

此動作的輸入包括:
- **C# 運算式**  -將會評估的運算式。 您必須包含 `傳回` 陳述式來取得任何內容。
- **內容物件** -可傳入觸發程序的選擇性內容物件。 您可以定義數目的屬性，但基底必須是 JObject `{...}`, ，並可透過索引鍵的名稱 (值傳遞做為 JToken 以對應至名稱) 的指令碼中參考的物件。
- **程式庫** -要包含在編譯指令碼的.dll 檔案的選擇性陣列。 陣列會使用下列結構，而且輸出時將 .dll 放在 Blob 儲存體連接器旁邊的效果最佳：

```javascript
[{"filename": "name.dll", "assembly": {Base64StringFromConnector}, "usingstatment": "using Library.Reference;"}]
```

例如，假設您使用 Office 365 觸發程序**新增電子郵件**。 該觸發程序傳回下列物件：

```javascript
{
    ...
    "Attachments" : [
        {
            "name" : "Picture.png",
            "content" : {
                "ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
                "ContentType" : "image/png",
                "ContentTransferEncoding" : "Base64"
            }
        },  
        {
            "name" : "File.txt",
            "content" : {
                "ContentData" : "Don't worry, be happy!",
                "ContentType" : "text/plain",
                "ContentTransferEncoding" : "None"
            }
        }   
    ]
}
```

但是，您想要上傳這些附件到 Yammer 文章。 不幸的是，Yammer 附件的結構描述會稍有不同。 現在，您可以剖析此邏輯應用程式內。 內容物件，只要傳入: `@triggerBody()`, ，和運算式，請傳入:

```javascript
JArray YammerAttachments = new JObject();
foreach(var obj in (JArray)Attachments)
{
    JObject att = new JObject();
    att["Content"] = obj["content"];
    att["FileName"] = obj["Name"];
    YammerAttachments.Add(att); 
}
return YammerAttachments;
```

動作會傳回在結果物件中從您的函式傳回的物件。 因此，在 Yammer API 應用程式，您可以參考 `@body('csapi').results` 的 **附件** 屬性。

## 進一步運用您的連接器

現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。 請參閱 [什麼是邏輯應用程式?](app-service-logic-what-are-logic-apps.md).

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。





[creating a logic app]: app-service-logic-create-a-logic-app.md 

