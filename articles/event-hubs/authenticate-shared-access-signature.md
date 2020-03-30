---
title: Az Azure Event Hubs-hoz való hozzáférés hitelesítése megosztott hozzáférésű aláírásokkal
description: Ez a cikk bemutatja, hogyan hitelesítheti az Event Hubs-erőforrásokhoz való hozzáférést megosztott hozzáférésű aláírások használatával.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: d17026dba26b3c1cb846d60967180c29563c425d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74545594"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Az Event Hubs-erőforrásokhoz való hozzáférés hitelesítése megosztott hozzáférésű aláírásokkal (SAS)
A megosztott hozzáférésű aláírás (SAS) segítségével részletesen szabályozhatja a megosztott hozzáférés-aláírással rendelkező ügyfelek számára nyújtott hozzáférés típusát. Íme néhány, a SAS-ben beállítható vezérlő: 

- Az az időtartam, amely alatt a SAS érvényes, beleértve a kezdési és lejárati időt.
- A SAS által megadott engedélyek. Például egy SAS egy Event Hubs névtér adhat a figyelési engedélyt, de nem a küldési engedélyt.
- Csak az érvényes hitelesítő adatokat tartalmazó ügyfelek küldhetnek adatokat egy eseményközpontba.
- Egy ügyfél nem személyesíthet meg egy másik ügyfelet.
- A rouge ügyfél letiltható az adatok küldése egy eseményközpontba.

Ez a cikk az Event Hubs-erőforrások sas-i erőforrásokhoz való hozzáférés hitelesítését ismerteti. Ha többet szeretne tudni arról, hogy **miként engedélyezi** az Event Hubs-erőforrásokhoz való hozzáférést a SAS használatával, olvassa el [ezt a cikket.](authorize-access-shared-access-signature.md) 

> [!NOTE]
> A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait, ha lehetséges, a biztonsági ajánlott eljárás, ahelyett, hogy a megosztott hozzáférésű aláírások, amelyek könnyebben sérülhet. Bár továbbra is használhatja a megosztott hozzáférésű aláírások (SAS) az Event Hubs-erőforrások részletes eléréséhez való hozzáférés biztosításához, az Azure AD hasonló képességeket kínál anélkül, hogy kezelnie kellene a SAS-jogkivonatokat, vagy aggódnia kellene a sérült SAS visszavonása miatt.
> 
> Az Azure AD-integrációról az Azure Event Hubs szolgáltatásban további információt az [Event Hubs-hoz való hozzáférés engedélyezése](authorize-access-azure-active-directory.md)az Azure AD használatával című témakörben talál. 


## <a name="configuring-for-sas-authentication"></a>SAS-hitelesítés konfigurálása
Konfigurálhatja az EventHubs megosztott hozzáférés-engedélyezési szabály egy Event Hubs névtér, vagy egy entitás (eseményközpont-példány vagy Kafka-témakör egy Kafka-kompatibilis névtér eseményközpontokban). A megosztott hozzáférés engedélyezési szabályának konfigurálása egy fogyasztói csoporton jelenleg nem támogatott, de a névtéren vagy entitáson konfigurált szabályok segítségével biztonságos hozzáférést biztosíthat a fogyasztói csoporthoz. 

Az alábbi képen látható, hogyan vonatkoznak az engedélyezési szabályok a mintaentitásokra. 

![Engedélyezési szabály konfigurálása](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

Ebben a példában a minta Event Hubs névtér (ExampleNamespace) két entitást rendelkezik: eh1 és topic1. Az engedélyezési szabályok mind az entitás szintjén, mind a névtér szintjén vannak definiálva.  

A manageRuleNS, sendRuleNS és listenRuleNS engedélyezési szabályok az eh1 eseményközpont-példányra és a t1 témakörre egyaránt vonatkoznak. A listenRule-eh és sendRule-eh engedélyezési szabályok csak az event hub-példányeh1 és sendRuleT engedélyezési szabály csak a témakör témakör1. 

SendRuleNS engedélyezési szabály használata esetén az ügyfélalkalmazások az eh1 és a topic1 számára is küldhetnek. SendRuleT engedélyezési szabály használata esetén csak a témakör1 részletes hozzáférését kényszeríti ki, ezért a szabályt használó ügyfélalkalmazások most már nem küldhetnek eh1-nek, hanem csak a témakörhöz1.

## <a name="generate-a-shared-access-signature-token"></a>Megosztott hozzáférésű aláírási jogkivonat létrehozása 
Minden olyan ügyfél, amely hozzáfér egy engedélyezési szabály nevének és az egyik aláíró kulcsának nevéhez, sas-jogkivonatot hozhat létre. A jogkivonat egy karakterlánc a következő formátumban történő létrehozásával jön létre:

- `se`– Token lejárati azonnali. 1970. január 1-jén 00:00:00 UTC időpont óta eltelt másodpercek egész szám (UNIX-korszak), amikor a token lejár
- `skn`– Az engedélyezési szabály neve, azaz a SAS-kulcs neve.
- `sr`– Az elérésben lévő erőforrás URI-ja.
- `sig`– Aláírás.

Az aláírás-karakterlánc az erőforrás URI-n (az előző szakaszban leírtak szerint hatókörön) számított SHA-256-os kivonat, valamint a crlf által elválasztott jogkivonat lejárati pillanatának karakterlánc-ábrázolása.

A kivonatszámítás a következő pszeudokódhoz hasonlóan néz ki, és 256 bites/32 bájtos kivonatértéket ad vissza. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

A jogkivonat tartalmazza a nem kivonatolt értékeket, így a címzett újraszámíthatja a kivonatot ugyanazzal a paraméterrel, ellenőrizve, hogy a kibocsátó rendelkezik-e érvényes aláíró kulccsal.

Az erőforrás URI-je annak a Service Bus-erőforrásnak a teljes URI-ja, amelyhez a hozzáférés igényelt. Például http://<namespace>.servicebus.windows.net/,<entityPath> vagy `sb://<namespace>.servicebus.windows.net/<entityPath>;` ez, `http://contoso.servicebus.windows.net/eventhubs/eh1`.

Az URI-t százalékkódolással kell elkönyvelni.

Az aláíráshoz használt megosztott hozzáférés engedélyezési szabályát az uri által megadott entitáson vagy annak egyik hierarchikus szülőjében kell konfigurálni. Például, `http://contoso.servicebus.windows.net/eventhubs/eh1` `http://contoso.servicebus.windows.net` vagy az előző példában.

A SAS-jogkivonat az aláírási <resourceURI> karakterláncban használt összes erőforrásra érvényes.

> [!NOTE]
> A megosztott hozzáférési szabályzat használatával létrehoz egy hozzáférési jogkivonatot az Event Hubs számára. További információt a Megosztott hozzáférés engedélyezési házirendje című [témakörben talál.](authorize-access-shared-access-signature.md#shared-access-authorization-policies)

### <a name="generating-a-signaturetoken-from-a-policy"></a>Aláírás(token) létrehozása házirendből 
A következő szakasz egy SAS-jogkivonat létrehozása megosztott hozzáférésű aláírási házirendek használatával,

#### <a name="nodejs"></a>NodeJS

```javascript
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
```

#### <a name="java"></a>Java

```java
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
#### <a name="php"></a>PHP

```php
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

#### <a name="c"></a>C#

```csharp
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

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Az Event Hubs megjelenítőinek hitelesítése a SAS-szel 
Az eseményközzétevő virtuális végpontot határoz meg egy eseményközponthoz. A közzétevő csak arra használható, hogy üzeneteket küldjön egy eseményközpontba, és ne fogadjon üzeneteket.

Általában egy eseményközpont ügyfelenként egy közzétevőt alkalmaz. Az eseményközpont bármely közzétevőjének küldött összes üzenet az adott eseményközpontban van várólistára. A közzétevők lehetővé teszik a részletes hozzáférés-vezérlést.

Minden Event Hubs-ügyfél egyedi jogkivonatot kap, amely az ügyfélre van feltöltve. A jogkivonatok úgy készülnek, hogy minden egyes egyedi jogkivonat hozzáférést biztosít a különböző egyedi közzétevőkhöz. A jogkivonatot tartalmazó ügyfél csak egy közzétevőnek küldhet, más közzétevőnek nem. Ha több ügyfél osztozik ugyanazon a jogkivonaton, akkor mindegyikük megosztja a közzétevőt.

Minden jogkivonat SAS-kulcsokkal van hozzárendelve. Általában minden jogkivonat ugyanazzal a kulccsal van aláírva. Az ügyfelek nincsenek tisztában a kulcs, amely megakadályozza, hogy az ügyfelek a tokenek gyártása. Az ügyfelek ugyanazokon a jogkivonatokon működnek, amíg le nem járnak.

Ha például az engedélyezési szabályokat csak az Event Hubs-ba küldésre/közzétételre szeretné megadni, meg kell határoznia egy küldési engedélyezési szabályt. Ez történhet névtér szinten, vagy adjon részletesebb hatókört egy adott entitásnak (eseményközpontok példánya vagy egy témakör). Az ilyen részletes hozzáféréssel rendelkező ügyfél vagy alkalmazás neve Event Hubs közzétevő. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy SAS-kulcsot a közzétenni kívánt entitáson a **küldési** hatókör hozzárendeléséhez. További információt a Megosztott hozzáférés engedélyezési házirendjei című [témakörben talál.](authorize-access-shared-access-signature.md#shared-access-authorization-policies)
2. Hozzon létre egy SAS-jogkivonatot egy adott közzétevő lejárati idejével az 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Adja meg a jogkivonatot a közzétevő-ügyfélnek, amely csak az entitásnak és a jogkivonatot biztosító közzétevőnek küldhet.

    A jogkivonat lejárta után az ügyfél elveszíti az entitásnak történő küldéshez/közzétételhez való hozzáférését. 


> [!NOTE]
> Bár nem ajánlott, lehetőség van az eszközök olyan jogkivonatokkal való felszerelésére, amelyek hozzáférést biztosítanak egy eseményközponthoz vagy egy névtérhez. Minden olyan eszköz, amely rendelkezik ezzel a jogkivonattal, üzeneteket küldhet közvetlenül az eseményközpontba. Továbbá az eszköz nem feketelistára az adott eseményközpontba való küldéstől.
> 
> Mindig ajánlott, hogy konkrét és részletes hatókörök.

> [!IMPORTANT]
> A jogkivonatok létrehozása után minden ügyfél kivan építve a saját egyedi jogkivonattal.
>
> Amikor az ügyfél adatokat küld egy eseményközpontba, a kérést a jogkivonattal címkézi. Annak megakadályozása érdekében, hogy a támadó lehallgatja és ellopja a jogkivonatot, az ügyfél és az eseményközpont közötti kommunikációnak titkosított csatornán keresztül kell történnie.
> 
> Ha egy támadó ellop egy tokent, a támadó megszemélyesítheti azt az ügyfelet, akinek a tokenjét ellopták. A közzétevő feketelistára tétele használhatatlanná teszi az ügyfelet, amíg nem kap egy új jogkivonatot, amely egy másik közzétevőt használ.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Az Event Hubs-felhasználók hitelesítése a SAS-szel 
Az Event Hubs-gyártók által létrehozott adatokból felhasznált háttéralkalmazások hitelesítéséhez az Event Hubs tokenhitelesítéshez az ügyfeleknek rendelkezniük kell az Event Hubs névteréhez vagy eseményközpont-példányához vagy témaköréhez rendelt **figyelési** jogosultságokkal. **listen** Az adatokat az Event Hubs fogyasztói csoportok használatával használja fel. Míg a SAS-házirend részletes hatókört biztosít, ez a hatókör csak az entitás szintjén van definiálva, és nem a fogyasztói szinten. Ez azt jelenti, hogy a névtér szintjén vagy az eseményközpont-példány vagy -témakör szintjén meghatározott jogosultságok az entitás fogyasztói csoportjaira lesznek alkalmazva.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [A SAS használatának engedélyezése](authenticate-shared-access-signature.md)
- [Szerepkör-alap hozzáférés-vezérlés engedélyezése (RBAC) használata](authenticate-shared-access-signature.md)
- [További információ az Eseményközpontokról](event-hubs-about.md)

Lásd a következő kapcsolódó cikkeket:

- [Az Azure-eseményközpontoknak az Azure Active Directory használatával történő hitelesítése](authenticate-application.md)
- [Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez](authenticate-managed-identity.md)
- [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával](authorize-access-azure-active-directory.md)
- [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz megosztott hozzáférési aláírásokkal](authorize-access-shared-access-signature.md)