<properties
   pageTitle="共用存取簽章概觀 | Microsoft Azure"
   description="共用存取簽章為何、其如何運作，以及如何從 Node、PHP 和 C# 中使用它們。"
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="darosa"/>

# 共用存取簽章

*共用存取簽章* (SAS) 是主要的安全性機制，包括事件中樞的服務匯流排代理傳訊 (佇列和主題)，並轉送訊息。 這篇文章討論共用存取簽章、其運作方式以及如何以平台無關的方式使用它們。

## SAS 的概觀

共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。 SAS 是所有服務匯流排服務使用的非常強大的機制。 在實際使用時，SAS 有兩個元件: *共用存取原則* 和 *共用存取簽章* (通常稱為 *語彙基元*)。

您可以找到更多有關使用服務匯流排的共用存取簽章資訊 [使用服務匯流排的共用存取簽章驗證](service-bus-shared-access-signature-authentication.md)。

## 共用的存取原則

對 SAS 應了解的一項重點是，一切都從原則開始。 針對每個原則，您決定三段資訊: **名稱**, ，**範圍**, ，和 **權限**。  **名稱** 只是該範圍內的唯一名稱。 範圍也很簡單：它是發生問題之資源的 URI。 服務匯流排命名空間範圍是完整的網域名稱 (FQDN)，例如 **`https://<yournamespace>.servicebus.windows.net/`**。

原則的可用權限大部分都易於理解：

  + 傳送
  + 接聽
  + 管理

建立原則之後，它會指派 *主索引鍵* 和 *次要金鑰*。 這些是密碼編譯增強式金鑰。 不遺失或洩漏它們-它們永遠都可在 [Azure 傳統入口網站][]。 您可以使用其中一個產生的金鑰，以及您可以隨時重新產生它們。 不過，如果您重新產生或變更原則中的主索引鍵，從其建立的任何共用存取簽章將會失效。

當您建立服務匯流排命名空間時，原則會自動建立名為整個命名空間 **RootManageSharedAccessKey**, ，而且此原則有所有的權限。 您不以登入 **根**, ，所以除非有適合的理由，否則請勿使用此原則。 您可以建立額外的原則中 **設定** 的入口網站中的命名空間] 索引標籤。 請務必注意服務匯流排中單一樹狀目錄的層級 (命名空間、佇列、事件中樞等) 只能有最多 12 個附加至它的原則。

## 共用存取簽章 (權杖)

原則本身不是服務匯流排的存取權杖。 它是來自使用主要或次要索引鍵產生的存取權杖的物件。 權杖是以下列格式仔細編寫字串而產生：

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

其中 `signature-string` 是權杖範圍的 sha-256 雜湊 (**範圍** 上一節中所述) 與附加 CRLF 與到期時間 (以秒為單位，自: `00:00:00 UTC` 1970 年 1 月)。

雜湊看起來類似下列虛擬程式碼，並傳回 32 個位元組。

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

非雜湊值位於 **SharedAccessSignature** 字串，讓收件者可以計算的雜湊，使用相同的參數，以確保它會傳回相同的結果。 URI 會指定範圍，而金鑰名稱會識別要用來計算雜湊的原則。 從安全性的觀點而言，這很重要。 如果簽章與收件者 (服務匯流排) 所計算的不符，則會拒絕存取。 此時我們可以確定寄件者可存取金鑰，並且應該被授與原則中指定的權限。

## 從原則產生簽章

您在程式碼中如何實際進行此動作？ 讓我們來看一下其中的一些。

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## 使用共用存取簽章 (於 HTTP 層級)
 
既然知道如何為服務匯流排中的任何實體建立共用存取簽章，您可準備執行 HTTP POST：

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
請記住，這適用於所有項目。 您可以為佇列、主題、訂用帳戶、事件中樞或轉送建立 SAS。 如果您對事件中樞使用每一發行者身分識別，您只需附加 `/publishers/< publisherid>`。

如果您提供寄件者或用戶端 SAS 權杖，他們不會直接有金鑰，並且他們無法回復雜湊來取得它。 因此，您可以控制他們可以存取的項目，以及時間長度。 要記住的重點是，如果您變更原則中的主索引鍵，從其建立的任何共用存取簽章將會失效。

## 使用共用存取簽章 (於 AMQP 層級)

在前一節中，說明了如何使用 SAS 權杖搭配 HTTP POST 要求傳送資料到服務匯流排。 如您所了解，您可以使用 AMQP (進階訊息佇列通訊協定) 通訊協定來存取服務匯流排，AMQP 通訊協定在許多的案例中，都是基於效能考量而做為主要及慣用的通訊協定。 下列文件中描述搭配 AMQP 的 SAS 權杖用法 [amqp 安全性 1.0 版](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) 位於工作草稿自 2013年，但是它也支援 Azure 今天。

開始之前將資料傳送至服務匯流排，發行者需要傳送 SAS 權杖，AMQP 訊息內部定義完善的 AMQP 節點，名為 **"$cbs"** (您可以看到它類似 「 特殊 」 的佇列服務用來取得並驗證所有的 SAS 權杖)。 「 發行者 」，必須先指定 **"ReplyTo"** AMQP 訊息內部欄位; 這就是，服務將回覆權杖驗證 (發行者與服務之間的簡單要求/回覆模式) 的結果與 「 發行者 」 的節點。 此回覆節點是「動態」建立，如 AMQP 1.0 規格中所述的「動態建立遠端節點」。 檢查 SAS 權杖有效之後，發行者可以繼續並開始將資料傳送至服務。

下列步驟將說明如何傳送 SAS 權杖搭配 AMQP 通訊協定使用 [AMQP.Net Lite](http://amqpnetlite.codeplex.com) 很有用，如果您無法使用官方服務匯流排 SDK 的程式庫 (例如在 WinRT、.Net Compact Framework、.Net Micro Framework 和 Mono) 開發 & #35;。 當然，此函式庫對於了解宣告型安全性如何在 AMQP 層級運作而言非常有用，如同您了解他如何在 HTTP 層級運作一樣 (使用 HTTP POST 要求以及在標頭 "Authorization" 內部傳送的 SAS 權杖)。 不過，不用擔心！ 如果您不需要這類深入了解有關 AMQP，您可以使用官方服務匯流排 SDK 搭配.Net Framework 會替您的應用程式或 [Azure SB Lite](http://azuresblite.codeplex.com) 程式庫的所有其他平台 (如上述)。

### C&#35;

```
/// <summary>
/// Send Claim Based Security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

上述 *putcbstoken ()* 方法會接收 *連接* (AMQP Connection 類別執行個體 AMQP.Net Lite 程式庫所提供)，代表服務的 TCP 連線和 *sasToken* 參數之 sas 權杖，來傳送。 
注意: 很重要的連接，以建立 **SASL 驗證機制設定為外部** (和不使用使用者名稱和密碼，您不需要傳送 SAS 權杖時使用的預設 PLAIN)。

接下來，發行者會建立兩個 AMQP 連結來傳送 SAS 權杖並接受來自服務的回覆 (權杖驗證結果)。

AMQP 訊息因為具有眾多屬性而有點複雜，且含有比簡單訊息更多的資訊。 SAS 權杖會放在訊息內文中 (使用其建構函式)。  **"ReplyTo"** 屬性設定為接受驗證結果 (您可以變更其名稱而且它將會自動建立服務) 在接收者連結上的節點名稱。 最後三個 application/custom 屬性是由服務使用，以了解它必須執行什麼類型的作業。 它們必須 CBS 草稿規格所述的 **作業名稱** (因此 「 put-語彙基元 」)， **權杖的型別** 放 (因此"put-token")，最後 **的對象的 「 名稱 」** 的權杖套用至 (整個實體)。

在寄件者連結上傳送 SAS 權杖之後，發行者需要在接收者連結上讀取回覆。 回覆是名為應用程式屬性的簡單 AMQP 訊息 **」 狀態碼 「** 可包含 HTTP 狀態碼相同的值。 

## 後續步驟

請參閱 [服務匯流排 REST API 參考](https://msdn.microsoft.com/library/azure/hh780717.aspx) 如需有關如何使用這些 SAS 權杖。

如需服務匯流排驗證的詳細資訊，請參閱 [服務匯流排驗證與授權](service-bus-authentication-and-authorization.md)。 

SAS 在 C# 和 Java 指令碼的更多範例位於 [此部落格文章](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx)。

[Azure classic portal]: http://manage.windowsazure.com
