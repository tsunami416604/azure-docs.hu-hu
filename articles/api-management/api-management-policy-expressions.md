---
title: Azure API Management házirend-kifejezések | Microsoft Docs
description: Ismerje meg az Azure API Management házirend-kifejezéseit.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: fa5e84ba62896969458b84cf014e2b35ee869df7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072173"
---
# <a name="api-management-policy-expressions"></a>API Management házirend-kifejezések
Ez a cikk a házirend-kifejezések szintaxisát tárgyalja C# 7. Mindegyik kifejezés rendelkezik hozzáféréssel a implicit módon megadott [környezeti](api-management-policy-expressions.md#ContextVariables) változóhoz, valamint a .NET-keretrendszer engedélyezett [részhalmazához](api-management-policy-expressions.md#CLRTypes) .

További információk:

- Ismerje meg, hogyan adhat meg környezeti információkat a háttér-szolgáltatáshoz. Használja a [lekérdezési karakterlánc beállítása paramétert](api-management-transformation-policies.md#SetQueryStringParameter) , és [adja meg a HTTP-fejléc](api-management-transformation-policies.md#SetHTTPheader) házirendjeit az információk megadásához.
- Tekintse meg, hogyan használhatja a [JWT](api-management-access-restriction-policies.md#ValidateJWT) -szabályzat érvényesítése beállítást a jogkivonatok jogcímein alapuló műveletekhez való hozzáférés előzetes engedélyezéséhez.
- A szabályzatok kiértékelésének és az értékelések eredményeinek megtekintéséhez tekintse meg az [API-ellenőrök](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) nyomkövetésének használatát ismertető témakört.
- Tekintse meg, hogyan használhat kifejezéseket a [Get from cache](api-management-caching-policies.md#GetFromCache) és a [Store to cache](api-management-caching-policies.md#StoreToCache) policy házirendekkel a API Management válasz gyorsítótárazásának konfigurálásához. Állítsa be azt az időtartamot, amely megfelel a háttérrendszer válasz-gyorsítótárazásának, amelyet a biztonsági `Cache-Control` mentést végző szolgáltatás irányelve meghatároz.
- Lásd: tartalom szűrésének végrehajtása. Távolítsa el az adatelemeket a háttértől kapott válaszból a [vezérlési folyamat](api-management-advanced-policies.md#choose) és a [szövegtörzs](api-management-transformation-policies.md#SetBody) -szabályzatok használatával.
- A házirend-utasítások letöltéséhez tekintse meg az [API-Management-Samples/policies GitHub-](https://github.com/Azure/api-management-samples/tree/master/policies) tárházat.


## <a name="Syntax"></a>Szintaxis
Az egyutasításos kifejezések bekerülnek a `expression` - `@(expression)`ba, ahol a C# jól formázott kifejezési utasítás.

A többszörös kimutatási kifejezések a-ben `@{expression}`vannak bezárva. A többutasításos kifejezéseken belül minden kód elérési útnak `return` egy utasítással kell végződnie.

## <a name="PolicyExpressionsExamples"></a>Példák

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));
  }
  else
  {
    return null;
  }
}
```

## <a name="PolicyExpressionsUsage"></a>Használati
A kifejezések bármely API Management [házirendben](api-management-policies.md) attribútumértékként vagy szöveges értékként használhatók (kivéve, ha a házirend-hivatkozás másként nem határoz meg).

> [!IMPORTANT]
> Ha házirend-kifejezéseket használ, csak korlátozott mértékben ellenőrzi a házirend-kifejezéseket, ha a házirend meg van határozva. A kifejezéseket az átjáró futásidőben hajtja végre, a házirend-kifejezések által generált kivételek futásidejű hibát eredményeznek.

## <a name="CLRTypes"></a>A .NET-keretrendszerben engedélyezett típusok házirend-kifejezésekben
A következő táblázat a .NET-keretrendszer típusait és azok tagjait sorolja fel, amelyek a házirend-kifejezésekben engedélyezettek.

|Type|Támogatott tagok|
|--------------|-----------------------|
|Newtonsoft. JSON. formázás|Összes|
|Newtonsoft.Json.JsonConvert|Serializeobject elem, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Összes|
|Newtonsoft.Json.Linq.JArray|Összes|
|Newtonsoft.Json.Linq.JConstructor|Összes|
|Newtonsoft.Json.Linq.JContainer|Összes|
|Newtonsoft.Json.Linq.JObject|Összes|
|Newtonsoft.Json.Linq.JProperty|Összes|
|Newtonsoft.Json.Linq.JRaw|Összes|
|Newtonsoft.Json.Linq.JToken|Összes|
|Newtonsoft.Json.Linq.JTokenType|Összes|
|Newtonsoft.Json.Linq.JValue|Összes|
|System. Array|Összes|
|System.BitConverter|Összes|
|System.Boolean|Összes|
|System. byte|Összes|
|System.Char|Összes|
|System. Collections. Generic. Dictionary < TKey, TValue >|Összes|
|System.Collections.Generic.HashSet\<T>|Összes|
|System. Collections. Generic.\<ICollection illesztőfelületet T >|Összes|
|System. Collections. Generic. IDictionary < TKey, TValue >|Összes|
|System.Collections.Generic.IEnumerable\<T>|Összes|
|System. Collections. Generic.\<IEnumerator T >|Összes|
|System.Collections.Generic.IList\<T>|Összes|
|System.Collections.Generic.IReadOnlyCollection\<T>|Összes|
|System. Collections. Generic. IReadOnlyDictionary < TKey, TValue >|Összes|
|System.Collections.Generic.ISet\<T>|Összes|
|System. Collections. Generic. KeyValuePair < TKey, TValue >|Összes|
|System.Collections.Generic.List\<T>|Összes|
|System.Collections.Generic.Queue\<T>|Összes|
|System.Collections.Generic.Stack\<T>|Összes|
|System.Convert|Összes|
|System.DateTime|(Konstruktor), Hozzáadás, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, dátum, nap, DayOfWeek, DayOfYear, DaysInMonth, óra, IsDaylightSavingTime, IsLeapYear, MaxValue, ezredmásodperc, perc, MinValue, hónap, most , Elemzés, második, kivonás, ketyeg, TimeOfDay, ma, ToString, UtcNow, Year|
|System.DateTimeKind|UTC|
|System.DateTimeOffset|Összes|
|System.Decimal|Összes|
|System. Double|Összes|
|System.Exception|Összes|
|System.Guid|Összes|
|System.Int16|Összes|
|System.Int32|Összes|
|System.Int64|Összes|
|System.IO.StringReader|Összes|
|System.IO.StringWriter|Összes|
|System. LINQ. enumerable|Összes|
|System.Math|Összes|
|System.MidpointRounding|Összes|
|System .net. websegédprogram|Összes|
|System.Nullable|Összes|
|System.Random|Összes|
|System.SByte|Összes|
|System.Security.Cryptography.AsymmetricAlgorithm|Összes|
|System.Security.Cryptography.CipherMode|Összes|
|System.Security.Cryptography.HashAlgorithm|Összes|
|System.Security.Cryptography.HashAlgorithmName|Összes|
|System.Security.Cryptography.HMAC|Összes|
|System.Security.Cryptography.HMACMD5|Összes|
|System.Security.Cryptography.HMACSHA1|Összes|
|System.Security.Cryptography.HMACSHA256|Összes|
|System.Security.Cryptography.HMACSHA384|Összes|
|System.Security.Cryptography.HMACSHA512|Összes|
|System.Security.Cryptography.KeyedHashAlgorithm|Összes|
|System.Security.Cryptography.MD5|Összes|
|System.Security.Cryptography.Oid|Összes|
|System.Security.Cryptography.PaddingMode|Összes|
|System.Security.Cryptography.RNGCryptoServiceProvider|Összes|
|System.Security.Cryptography.RSA|Összes|
|System.Security.Cryptography.RSAEncryptionPadding|Összes|
|System.Security.Cryptography.RSASignaturePadding|Összes|
|System.Security.Cryptography.SHA1|Összes|
|System.Security.Cryptography.SHA1Managed|Összes|
|System.Security.Cryptography.SHA256|Összes|
|System.Security.Cryptography.SHA256Managed|Összes|
|System.Security.Cryptography.SHA384|Összes|
|System.Security.Cryptography.SHA384Managed|Összes|
|System.Security.Cryptography.SHA512|Összes|
|System.Security.Cryptography.SHA512Managed|Összes|
|System.Security.Cryptography.SymmetricAlgorithm|Összes|
|System.Security.Cryptography.X509Certificates.PublicKey|Összes|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Összes|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name (Név)|
|System. Security. kriptográfia. X509Certificates. X509|Összes|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Összes|
|System.Security.Cryptography.X509Certificates.X509ContentType|Összes|
|System. Security. kriptográfia. X509Certificates. X509NameType|Összes|
|System.Single|Összes|
|System.String|Összes|
|System.StringComparer|Összes|
|System.StringComparison|Összes|
|System.StringSplitOptions|Összes|
|System.Text.Encoding|Összes|
|System.Text.RegularExpressions.Capture|Index, hossz, érték|
|System.Text.RegularExpressions.CaptureCollection|Darabszám, elem|
|System.Text.RegularExpressions.Group|Rögzítés, sikeres|
|System.Text.RegularExpressions.GroupCollection|Darabszám, elem|
|System.Text.RegularExpressions.Match|Üres, csoportok, eredmény|
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, egyezés, egyezés, csere, felszabadulás, felosztás|
|System.Text.RegularExpressions.RegexOptions|Összes|
|System.Text.StringBuilder|Összes|
|System.TimeSpan|Összes|
|System.TimeZone|Összes|
|System.TimeZoneInfo.AdjustmentRule|Összes|
|System.TimeZoneInfo.TransitionTime|Összes|
|System.TimeZoneInfo|Összes|
|System. rekord|Összes|
|System.UInt16|Összes|
|System.UInt32|Összes|
|System.UInt64|Összes|
|System.Uri|Összes|
|System. UriPartial|Összes|
|System.Xml.Linq.Extensions|Összes|
|System.Xml.Linq.XAttribute|Összes|
|System.Xml.Linq.XCData|Összes|
|System.Xml.Linq.XComment|Összes|
|System.Xml.Linq.XContainer|Összes|
|System.Xml.Linq.XDeclaration|Összes|
|System.Xml.Linq.XDocument|Mind, kivéve: betöltés|
|System.Xml.Linq.XDocumentType|Összes|
|System.Xml.Linq.XElement|Összes|
|System.Xml.Linq.XName|Összes|
|System.Xml.Linq.XNamespace|Összes|
|System.Xml.Linq.XNode|Összes|
|System.Xml.Linq.XNodeDocumentOrderComparer|Összes|
|System.Xml.Linq.XNodeEqualityComparer|Összes|
|System.Xml.Linq.XObject|Összes|
|System.Xml.Linq.XProcessingInstruction|Összes|
|System.Xml.Linq.XText|Összes|
|System.Xml.XmlNodeType|Összes|

## <a name="ContextVariables"></a>Környezeti változó
Egy nevű `context` változó implicit módon elérhető minden házirend- [kifejezésben](api-management-policy-expressions.md#Syntax). A tagjai a `\request`szolgáltatással kapcsolatos információkat biztosítanak. Az `context` összes tag csak olvasható.

|Környezeti változó|Engedélyezett metódusok, tulajdonságok és paraméterek értékei|
|----------------------|-------------------------------------------------------|
|context|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Üzembe helyezés](#ref-context-deployment)<br /><br /> Eltelt TimeSpan – az időbélyegző és az aktuális idő közötti időtartam<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Művelet](#ref-context-operation)<br /><br /> [Termék](#ref-context-product)<br /><br /> [Kérés](#ref-context-request)<br /><br /> Kérelemazonosító GUID – egyedi kérelem azonosítója<br /><br /> [Válasz](#ref-context-response)<br /><br /> [Előfizetés](#ref-context-subscription)<br /><br /> Időbélyeg Dátum és idő – a kérés fogadása időpontra<br /><br /> Nyomkövetés: bool – jelzi, hogy a nyomkövetés be van-e kapcsolva <br /><br /> [Felhasználói](#ref-context-user)<br /><br /> [Változók](#ref-context-variables): IReadOnlyDictionary < sztring, objektum ><br /><br /> void nyomkövetés (üzenet: karakterlánc)|
|<a id="ref-context-api"></a>összefüggésben. API|Azonosító: karakterlánc<br /><br /> IsCurrentRevision: bool<br /><br />  Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Változat: karakterlánc<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verzió: karakterlánc |
|<a id="ref-context-deployment"></a>összefüggésben. Telepítési|Régió: karakterlánc<br /><br /> Szolgáltatásnév: karakterlánc<br /><br /> Tanúsítványok IReadOnlyDictionary < sztring, X509certificate2) >|
|<a id="ref-context-lasterror"></a>összefüggésben. LastError|Forrás: karakterlánc<br /><br /> Ok: karakterlánc<br /><br /> Üzenet: karakterlánc<br /><br /> Hatókör: karakterlánc<br /><br /> Szakasz: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> PolicyId: karakterlánc<br /><br /> További információ a környezetről. LastError lásd: [hibakezelés](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>összefüggésben. Művelet|Azonosító: karakterlánc<br /><br /> Metódus: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> UrlTemplate: karakterlánc|
|<a id="ref-context-product"></a>összefüggésben. Termék|API IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Csoportok IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Állapot: Enum ProductState {NotPublished, published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>összefüggésben. Kérelem|Törzse [IMessageBody](#ref-imessagebody) , `null` vagy ha a kérelem nem rendelkezik törzstel.<br /><br /> Tanúsítvány System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Fejlécek](#ref-context-request-headers): IReadOnlyDictionary < karakterlánc, karakterlánc [] ><br /><br /> IP-cím: karakterlánc<br /><br /> MatchedParameters: IReadOnlyDictionary < karakterlánc, karakterlánc ><br /><br /> Metódus: karakterlánc<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>karakterlánc-környezet Request. headers. GetValueOrDefault (headerName: karakterlánc, defaultValue: string)|headerName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> A vesszővel tagolt kérelmek fejlécének értékét `defaultValue` adja vissza, vagy ha a fejléc nem található.|
|<a id="ref-context-response"></a>összefüggésben. Válasz|Törzse [IMessageBody](#ref-imessagebody)<br /><br /> [Fejlécek](#ref-context-response-headers): IReadOnlyDictionary < karakterlánc, karakterlánc [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: karakterlánc|
|<a id="ref-context-response-headers"></a>karakterlánc-környezet Response. headers. GetValueOrDefault (headerName: karakterlánc, defaultValue: karakterlánc)|headerName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> A vesszővel tagolt válasz fejlécének értékét `defaultValue` adja vissza, vagy ha a fejléc nem található.|
|<a id="ref-context-subscription"></a>összefüggésben. Előfizetés|CreatedTime: DateTime<br /><br /> EndDate DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kulcs: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> PrimaryKey: karakterlánc<br /><br /> Értesítésiközpont: karakterlánc<br /><br /> StartDate DateTime?|
|<a id="ref-context-user"></a>összefüggésben. Felhasználói|E-mail: karakterlánc<br /><br /> FirstName: karakterlánc<br /><br /> Csoportok IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Identitások IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: sztring<br /><br /> Megjegyzés: karakterlánc<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Azonosító: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Protokollok IEnumerable < sztring\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Azonosító: karakterlánc<br /><br /> Name: karakterlánc|
|<a id="ref-imessagebody"></a>IMessageBody|Mint < T\>(preserveContent: bool = false): Where T: string, byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> A `context.Request.Body.As<T>` `T`és `context.Response.Body.As<T>` metódusok egy adott típusban lévő kérelem és válaszüzenet törzsének olvasására szolgálnak. Alapértelmezés szerint a metódus az eredeti üzenettörzs-adatfolyamot használja, és a visszatérés után elérhetetlenné teszi. Annak elkerülése érdekében, hogy a metódus a test stream egy példányán működjön, állítsa a `preserveContent` `true`paramétert a következőre:. [Itt](api-management-transformation-policies.md#SetBody) láthat egy példát.|
|<a id="ref-iurl"></a>IUrl|Gazdagép: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Port: int<br /><br /> [Lekérdezés](#ref-iurl-query): IReadOnlyDictionary < karakterlánc, karakterlánc [] ><br /><br /> QueryString: karakterlánc<br /><br /> Séma: karakterlánc|
|<a id="ref-iuseridentity"></a>IUserIdentity|Azonosító: karakterlánc<br /><br /> Szolgáltató: karakterlánc|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Fejléc: karakterlánc<br /><br /> Lekérdezés: karakterlánc|
|<a id="ref-iurl-query"></a>string IUrl. Query. GetValueOrDefault (queryParameterName: karakterlánc, defaultValue: karakterlánc)|queryParameterName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> Vesszővel tagolt lekérdezési paraméterek értékét adja `defaultValue` vissza, vagy ha a paraméter nem található.|
|<a id="ref-context-variables"></a>T környezet. Változók. GetValueOrDefault < T\>(variableName: karakterlánc, defaultValue: T)|variableName: karakterlánc<br /><br /> DefaultValue T?<br /><br /> A változó értéket adja vissza a `T` típus `defaultValue` értékre, vagy ha a változó nem található.<br /><br /> Ez a metódus kivételt jelez, ha a megadott típus nem egyezik a visszaadott változó tényleges típusával.|
|BasicAuthCredentials AsBasic (bemenet: Ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes http alapszintű hitelesítési engedélyezési kérelem fejlécének értékét tartalmazza, a metódus egy típusú `BasicAuthCredentials`objektumot ad vissza, ellenkező esetben a metódus null értéket ad vissza.|
|bool TryParseBasic (bemenet: Ez a karakterlánc, eredmény: out BasicAuthCredentials)|bemenet: karakterlánc<br /><br /> eredmény: kimenő BasicAuthCredentials<br /><br /> Ha a bemeneti paraméter egy érvényes http alapszintű hitelesítési engedélyezési értéket tartalmaz a kérelem fejlécében `true` , és az eredmény paraméter egy típusú `BasicAuthCredentials`értéket tartalmaz, ellenkező esetben a metódus visszaadja `false`a értéket.|
|BasicAuthCredentials|Password (jelszó): karakterlánc<br /><br /> UserId: string|
|JWT AsJwt (bemenet: Ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes JWT token értéket tartalmaz, a metódus egy típusú `Jwt`objektumot ad vissza, ellenkező esetben a metódus visszatér. `null`|
|bool TryParseJwt (bemenet: Ez a karakterlánc, eredmény: out JWT)|bemenet: karakterlánc<br /><br /> eredmény: kimenő JWT<br /><br /> Ha a bemeneti paraméter érvényes JWT jogkivonat-értéket tartalmaz, a metódus visszaadja `true` , és a Result paraméter egy típusú `Jwt`értéket tartalmaz, ellenkező esetben a `false`metódus visszaadja.|
|JWT|Algoritmus: karakterlánc<br /><br /> Célközönség IEnumerable < sztring\><br /><br /> Jogcímek IReadOnlyDictionary < karakterlánc, karakterlánc [] ><br /><br /> Expirationtime tulajdonságok DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kiállító: karakterlánc<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore DateTime?<br /><br /> Tárgy: karakterlánc<br /><br /> Típus: karakterlánc|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> Vesszővel tagolt jogcím-értékeket ad `defaultValue` vissza, vagy ha a fejléc nem található.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: karakterlánc, kulcs: byte [], IV: bájt [])|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG – szimmetrikus titkosítási algoritmus neve<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: System.Security.Cryptography.SymmetricAlgorithm)|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG-titkosítási algoritmus<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm, kulcs: byte [], IV: byte [])|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG-titkosítási algoritmus<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: karakterlánc, kulcs: byte [], IV: bájt [])|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG – szimmetrikus titkosítási algoritmus neve<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Szöveges karakterláncot ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: System.Security.Cryptography.SymmetricAlgorithm)|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG-titkosítási algoritmus<br /><br />Szöveges karakterláncot ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm, kulcs: byte [], IV: byte [])|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG-titkosítási algoritmus<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Szöveges karakterláncot ad vissza.|
|bool VerifyNoRevocation (bemenet: Ez a System. Security. kriptográfia. X509Certificates. X509certificate2))|Egy X. 509 lánc érvényesítését végzi a tanúsítvány visszavonási állapotának ellenőrzése nélkül.<br /><br />bemeneti-tanúsítvány objektum<br /><br />Az `true` érvényesítés sikerességét adja vissza. `false` ha az ellenőrzés sikertelen.|


## <a name="next-steps"></a>További lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Házirend-minták](policy-samples.md)
