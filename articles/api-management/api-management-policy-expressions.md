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
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244003"
---
# <a name="api-management-policy-expressions"></a>API Management házirend-kifejezések
Ez a cikk a házirend-kifejezések szintaxisát ismerteti a C# 7-es verziójában. Mindegyik kifejezés rendelkezik hozzáféréssel a implicit módon megadott [környezeti](api-management-policy-expressions.md#ContextVariables) változóhoz, valamint a .NET-keretrendszer engedélyezett [részhalmazához](api-management-policy-expressions.md#CLRTypes) .

További információk:

- Ismerje meg, hogyan adhat meg környezeti információkat a háttér-szolgáltatáshoz. Használja a [lekérdezési karakterlánc beállítása paramétert](api-management-transformation-policies.md#SetQueryStringParameter) , és adja meg a HTTP-fejléc házirendjeit az információk [megadásához](api-management-transformation-policies.md#SetHTTPheader) .
- Tekintse meg, hogyan használhatja a JWT-szabályzat [érvényesítése](api-management-access-restriction-policies.md#ValidateJWT) beállítást a jogkivonatok jogcímein alapuló műveletekhez való hozzáférés előzetes engedélyezéséhez.
- A szabályzatok kiértékelésének és az értékelések eredményeinek megtekintéséhez tekintse meg az [API-ellenőrök](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) nyomkövetésének használatát ismertető témakört.
- Tekintse meg, hogyan használhat kifejezéseket a [Get from cache](api-management-caching-policies.md#GetFromCache) és a [Store to cache](api-management-caching-policies.md#StoreToCache) policy házirendekkel a API Management válasz gyorsítótárazásának konfigurálásához. Állítsa be azt az időtartamot, amely megfelel a háttérrendszer válasz-gyorsítótárazásának, amelyet a biztonsági `Cache-Control` mentést végző szolgáltatás irányelve meghatároz.
- Lásd: tartalom szűrésének végrehajtása. Távolítsa el az adatelemeket a háttértől kapott válaszból a [vezérlési folyamat](api-management-advanced-policies.md#choose) és a [szövegtörzs](api-management-transformation-policies.md#SetBody) -szabályzatok használatával.
- A házirend-utasítások letöltéséhez tekintse meg az [API-Management-Samples/policies GitHub-](https://github.com/Azure/api-management-samples/tree/master/policies) tárházat.


## <a name="syntax"></a><a name="Syntax"></a>Szintaxis
Az egyutasításos kifejezések a ben `@(expression)`vannak, `expression` ahol a egy jól formázott C# Expression utasítás.

A többszörös kimutatási kifejezések a-ben `@{expression}`vannak bezárva. A többutasításos kifejezéseken belül minden kód elérési útnak `return` egy utasítással kell végződnie.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>Példák

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Használati
A kifejezések bármely API Management [házirendben](api-management-policies.md) attribútumértékként vagy szöveges értékként használhatók (kivéve, ha a házirend-hivatkozás másként nem határoz meg).

> [!IMPORTANT]
> Ha házirend-kifejezéseket használ, csak korlátozott mértékben ellenőrzi a házirend-kifejezéseket, ha a házirend meg van határozva. A kifejezéseket az átjáró futásidőben hajtja végre, a házirend-kifejezések által generált kivételek futásidejű hibát eredményeznek.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>A .NET-keretrendszerben engedélyezett típusok házirend-kifejezésekben
A következő táblázat a .NET-keretrendszer típusait és azok tagjait sorolja fel, amelyek a házirend-kifejezésekben engedélyezettek.

|Típus|Támogatott tagok|
|--------------|-----------------------|
|Newtonsoft. JSON. formázás|Összes|
|Newtonsoft. JSON. JsonConvert|Serializeobject elem, DeserializeObject|
|Newtonsoft. JSON. LINQ. Extensions|Összes|
|Newtonsoft. JSON. LINQ. JArray|Összes|
|Newtonsoft. JSON. LINQ. JConstructor|Összes|
|Newtonsoft. JSON. LINQ. JContainer|Összes|
|Newtonsoft. JSON. LINQ. JObject|Összes|
|Newtonsoft. JSON. LINQ. JProperty|Összes|
|Newtonsoft. JSON. LINQ. JRaw|Összes|
|Newtonsoft. JSON. LINQ. JToken|Összes|
|Newtonsoft. JSON. LINQ. JTokenType|Összes|
|Newtonsoft. JSON. LINQ. JValue|Összes|
|System. Array|Összes|
|System. BitConverter|Összes|
|System. Boolean|Összes|
|System. byte|Összes|
|System. char|Összes|
|System. Collections. Generic. Dictionary<TKey, TValue>|Összes|
|System. Collections. Generic.\<HashSet T>|Összes|
|System. Collections. Generic.\<icollection illesztőfelületet T>|Összes|
|System. Collections. Generic. IDictionary<TKey, TValue>|Összes|
|System. Collections. Generic.\<IEnumerable T>|Összes|
|System. Collections. Generic.\<IEnumerator T>|Összes|
|System. Collections. Generic.\<IList T>|Összes|
|System. Collections. Generic.\<IReadOnlyCollection T>|Összes|
|System. Collections. Generic. IReadOnlyDictionary<TKey, TValue>|Összes|
|System. Collections. Generic.\<ISet T>|Összes|
|System. Collections. Generic. KeyValuePair<TKey, TValue>|Összes|
|System. Collections. Generic.\<lista T>|Összes|
|System. Collections. Generic.\<üzenetsor T>|Összes|
|System. Collections. Generic.\<Stack T>|Összes|
|System. Convert|Összes|
|System. DateTime|(Konstruktor), Hozzáadás, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, dátum, nap, DayOfWeek, DayOfYear, DaysInMonth, óra, IsDaylightSavingTime, IsLeapYear, MaxValue, ezredmásodperc, perc, MinValue, hónap, most, elemzés, második, kivonás, kullancsok, TimeOfDay, ma, ToString, UtcNow, év|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Összes|
|System. decimális|Összes|
|System. Double|Összes|
|System. Exception|Összes|
|System. GUID|Összes|
|System. Int16|Összes|
|System. Int32|Összes|
|System. Int64|Összes|
|System. IO. StringReader|Összes|
|System. IO. StringWriter|Összes|
|System. LINQ. enumerable|Összes|
|System. Math|Összes|
|System. MidpointRounding|Összes|
|System .net. websegédprogram|Összes|
|System. Nullható|Összes|
|System. Random|Összes|
|System. sbyte érték|Összes|
|System. Security. kriptográfia. AsymmetricAlgorithm|Összes|
|System. Security. kriptográfia. titkosítási|Összes|
|System. Security. kriptográfia. HashAlgorithm|Összes|
|System. Security. kriptográfia. HashAlgorithmName|Összes|
|System. Security. kriptográfia. HMAC|Összes|
|System. Security. kriptográfia. HMACMD5|Összes|
|System. Security. kriptográfia. HMACSHA1|Összes|
|System. Security. kriptográfia. HMACSHA256|Összes|
|System. Security. kriptográfia. HMACSHA384|Összes|
|System. Security. kriptográfia. HMACSHA512|Összes|
|System. Security. kriptográfia. KeyedHashAlgorithm|Összes|
|System. Security. kriptográfia. MD5|Összes|
|System. Security. kriptográfia. OID|Összes|
|System. Security. kriptográfia. PaddingMode|Összes|
|System. Security. kriptográfia. RNGCryptoServiceProvider|Összes|
|System. Security. kriptográfia. RSA|Összes|
|System. Security. kriptográfia. RSAEncryptionPadding|Összes|
|System. Security. kriptográfia. RSASignaturePadding|Összes|
|System. Security. kriptográfia. SHA1|Összes|
|System. Security. kriptográfia. SHA1Managed|Összes|
|System. Security. kriptográfia. SHA256|Összes|
|System. Security. kriptográfia. SHA256Managed|Összes|
|System. Security. kriptográfia. SHA384|Összes|
|System. Security. kriptográfia. SHA384Managed|Összes|
|System. Security. kriptográfia. SHA512|Összes|
|System. Security. kriptográfia. SHA512Managed|Összes|
|System. Security. kriptográfia. SymmetricAlgorithm|Összes|
|System. Security. kriptográfia. X509Certificates. PublicKey|Összes|
|System. Security. kriptográfia. X509Certificates. RSACertificateExtensions|Összes|
|System. Security. kriptográfia. X509Certificates. X500DistinguishedName|Name (Név)|
|System. Security. kriptográfia. X509Certificates. X509|Összes|
|System. Security. kriptográfia. X509Certificates. X509certificate2)|Összes|
|System. Security. kriptográfia. X509Certificates. X509ContentType|Összes|
|System. Security. kriptográfia. X509Certificates. X509NameType|Összes|
|System. Single|Összes|
|System. String|Összes|
|System. StringComparer|Összes|
|System. Stringcomparison argumentummal|Összes|
|System. StringSplitOptions|Összes|
|System. Text. Encoding|Összes|
|System. Text. RegularExpressions. Capture|Index, hossz, érték|
|System. Text. RegularExpressions. CaptureCollection|Darabszám, elem|
|System. Text. RegularExpressions. Group|Rögzítés, sikeres|
|System. Text. RegularExpressions. GroupCollection|Darabszám, elem|
|System. Text. RegularExpressions. Match|Üres, csoportok, eredmény|
|System. Text. RegularExpressions. regex|(Konstruktor), IsMatch, egyezés, egyezés, csere, felszabadulás, felosztás|
|System. Text. RegularExpressions. RegexOptions|Összes|
|System. Text. StringBuilder|Összes|
|System. TimeSpan|Összes|
|System. TimeZone|Összes|
|System. TimeZoneInfo. AdjustmentRule|Összes|
|System. TimeZoneInfo. TransitionTime|Összes|
|System. TimeZoneInfo|Összes|
|System. rekord|Összes|
|System. UInt16|Összes|
|System. UInt32|Összes|
|System. UInt64|Összes|
|System. URI|Összes|
|System. UriPartial|Összes|
|System. xml. LINQ. Extensions|Összes|
|System. xml. LINQ. XAttribute|Összes|
|System. xml. LINQ. XCData|Összes|
|System. xml. LINQ. XComment|Összes|
|System. xml. LINQ. XContainer|Összes|
|System. xml. LINQ. XDeclaration|Összes|
|System. xml. LINQ. XDocument|Az összes, a kivételével: Load|
|System. xml. LINQ. XDocumentType|Összes|
|System. xml. LINQ. XElement|Összes|
|System. xml. LINQ. XName|Összes|
|System. xml. LINQ. XNamespace|Összes|
|System. xml. LINQ. XNode|Összes|
|System. xml. LINQ. XNodeDocumentOrderComparer|Összes|
|System. xml. LINQ. XNodeEqualityComparer|Összes|
|System. xml. LINQ. XObject|Összes|
|System. xml. LINQ. XProcessingInstruction|Összes|
|System. xml. LINQ. XText|Összes|
|System. xml. XmlNodeType|Összes|

## <a name="context-variable"></a><a name="ContextVariables"></a>Környezeti változó
Egy nevű `context` változó implicit módon elérhető minden házirend- [kifejezésben](api-management-policy-expressions.md#Syntax). A tagjai a `\request`szolgáltatással kapcsolatos információkat biztosítanak. Az `context` összes tag csak olvasható.

|Környezeti változó|Engedélyezett metódusok, tulajdonságok és paraméterek értékei|
|----------------------|-------------------------------------------------------|
|összefüggésben|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Üzembe helyezés](#ref-context-deployment)<br /><br /> Eltelt idő: TimeSpan időköz az időbélyegző és az aktuális idő értéke között<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Művelet](#ref-context-operation)<br /><br /> [Termék](#ref-context-product)<br /><br /> [Kérés](#ref-context-request)<br /><br /> Kérelemazonosító: GUID – egyedi kérelem azonosítója<br /><br /> [Válasz](#ref-context-response)<br /><br /> [Előfizetés](#ref-context-subscription)<br /><br /> Időbélyeg: dátum és idő – a kérés fogadásakor időpontra<br /><br /> Nyomkövetés: bool – jelzi, hogy a nyomkövetés be van-e kapcsolva <br /><br /> [Felhasználó](#ref-context-user)<br /><br /> [Változók](#ref-context-variables): IReadOnlyDictionary<sztring, objektum><br /><br /> void nyomkövetés (üzenet: karakterlánc)|
|<a id="ref-context-api"></a>összefüggésben. API|Azonosító: karakterlánc<br /><br /> IsCurrentRevision: bool<br /><br />  Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Változat: karakterlánc<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verzió: karakterlánc |
|<a id="ref-context-deployment"></a>összefüggésben. Telepítési|Régió: karakterlánc<br /><br /> Szolgáltatásnév: karakterlánc<br /><br /> Tanúsítványok: IReadOnlyDictionary<sztring, X509certificate2)>|
|<a id="ref-context-lasterror"></a>összefüggésben. LastError|Forrás: karakterlánc<br /><br /> Ok: karakterlánc<br /><br /> Üzenet: karakterlánc<br /><br /> Hatókör: karakterlánc<br /><br /> Szakasz: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> PolicyId: karakterlánc<br /><br /> További információ a környezetről. LastError lásd: [hibakezelés](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>összefüggésben. Művelet|Azonosító: karakterlánc<br /><br /> Metódus: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> UrlTemplate: karakterlánc|
|<a id="ref-context-product"></a>összefüggésben. Termék|API-k: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Csoportok: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Állapot: Enum ProductState {NotPublished, published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>összefüggésben. Kérelem|Törzs: [IMessageBody](#ref-imessagebody) , `null` vagy ha a kérelem nem rendelkezik törzstel.<br /><br /> Tanúsítvány: System. Security. kriptográfia. X509Certificates. X509certificate2)<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> IP-cím: karakterlánc<br /><br /> MatchedParameters: IReadOnlyDictionary<karakterlánc, karakterlánc><br /><br /> Metódus: karakterlánc<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL-cím: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>karakterlánc-környezet Request. headers. GetValueOrDefault (headerName: karakterlánc, defaultValue: string)|headerName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> A vesszővel tagolt kérelmek fejlécének értékét `defaultValue` adja vissza, vagy ha a fejléc nem található.|
|<a id="ref-context-response"></a>összefüggésben. Válasz|Törzs: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: karakterlánc|
|<a id="ref-context-response-headers"></a>karakterlánc-környezet Response. headers. GetValueOrDefault (headerName: karakterlánc, defaultValue: karakterlánc)|headerName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> A vesszővel tagolt válasz fejlécének értékét `defaultValue` adja vissza, vagy ha a fejléc nem található.|
|<a id="ref-context-subscription"></a>összefüggésben. Előfizetés|CreatedTime: dátum és idő<br /><br /> EndDate: DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kulcs: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> PrimaryKey: karakterlánc<br /><br /> Értesítésiközpont: karakterlánc<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>összefüggésben. Felhasználói|E-mail: karakterlánc<br /><br /> FirstName: karakterlánc<br /><br /> Csoportok: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Identitások: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: sztring<br /><br /> Megjegyzés: karakterlánc<br /><br /> RegistrationDate: dátum és idő|
|<a id="ref-iapi"></a>IApi|Azonosító: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Protokollok: IEnumerable<karakterlánc\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Azonosító: karakterlánc<br /><br /> Name: karakterlánc|
|<a id="ref-imessagebody"></a>IMessageBody|Mint<T\>(preserveContent: bool = false): where t: string, byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> A `context.Request.Body.As<T>` és `context.Response.Body.As<T>` metódusok egy adott típusban `T`lévő kérelem és válaszüzenet törzsének olvasására szolgálnak. Alapértelmezés szerint a metódus az eredeti üzenettörzs-adatfolyamot használja, és a visszatérés után elérhetetlenné teszi. Annak elkerülése érdekében, hogy a metódus a test stream egy példányán működjön, állítsa a `preserveContent` paramétert a `true`következőre:. [Itt](api-management-transformation-policies.md#SetBody) láthat egy példát.|
|<a id="ref-iurl"></a>IUrl|Gazdagép: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Port: int<br /><br /> [Lekérdezés](#ref-iurl-query): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> QueryString: karakterlánc<br /><br /> Séma: karakterlánc|
|<a id="ref-iuseridentity"></a>IUserIdentity|Azonosító: karakterlánc<br /><br /> Szolgáltató: karakterlánc|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Fejléc: karakterlánc<br /><br /> Lekérdezés: karakterlánc|
|<a id="ref-iurl-query"></a>string IUrl. Query. GetValueOrDefault (queryParameterName: karakterlánc, defaultValue: karakterlánc)|queryParameterName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> Vesszővel tagolt lekérdezési paraméterek értékét adja `defaultValue` vissza, vagy ha a paraméter nem található.|
|<a id="ref-context-variables"></a>T környezet. Változók. GetValueOrDefault<T\>(variableName: karakterlánc, DefaultValue: t)|variableName: karakterlánc<br /><br /> defaultValue: T<br /><br /> A változó értéket adja vissza a `T` típus `defaultValue` értékre, vagy ha a változó nem található.<br /><br /> Ez a metódus kivételt jelez, ha a megadott típus nem egyezik a visszaadott változó tényleges típusával.|
|BasicAuthCredentials AsBasic (bemenet: Ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes HTTP alapszintű hitelesítési engedélyezési kérelem fejlécének értékét tartalmazza, a metódus egy típusú `BasicAuthCredentials`objektumot ad vissza. Ellenkező esetben a metódus null értéket ad vissza.|
|bool TryParseBasic (bemenet: Ez a karakterlánc, eredmény: out BasicAuthCredentials)|bemenet: karakterlánc<br /><br /> eredmény: kimenő BasicAuthCredentials<br /><br /> Ha a bemeneti paraméter érvényes HTTP alapszintű hitelesítési engedélyezési értéket tartalmaz a kérelem fejlécében, a `true` metódus visszaadja, és az eredmény paraméter egy `BasicAuthCredentials`típusú értéket tartalmaz. Ellenkező esetben a metódus `false`visszaadja.|
|BasicAuthCredentials|Password (jelszó): karakterlánc<br /><br /> UserId: karakterlánc|
|JWT AsJwt (bemenet: Ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes JWT token értéket tartalmaz, a metódus egy típusú `Jwt`objektumot ad vissza. Ellenkező esetben a metódus `null`visszaadja.|
|bool TryParseJwt (bemenet: Ez a karakterlánc, eredmény: out JWT)|bemenet: karakterlánc<br /><br /> eredmény: kimenő JWT<br /><br /> Ha a bemeneti paraméter érvényes JWT jogkivonat-értéket tartalmaz, a metódus visszatér `true` , és az eredmény paraméter egy típusú `Jwt`értéket tartalmaz. Ellenkező esetben a metódus `false`visszaadja.|
|JWT|Algoritmus: karakterlánc<br /><br /> Célközönség: IEnumerable<sztring\><br /><br /> Jogcímek: IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> Expirationtime tulajdonságok: DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kiállító: karakterlánc<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Tárgy: karakterlánc<br /><br /> Típus: karakterlánc|
|karakterlánc JWT. jogcím. GetValueOrDefault (claimName: karakterlánc, defaultValue: karakterlánc)|claimName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> Vesszővel tagolt jogcím-értékeket ad `defaultValue` vissza, vagy ha a fejléc nem található.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: karakterlánc, kulcs: byte [], IV: bájt [])|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG – szimmetrikus titkosítási algoritmus neve<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm)|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG-titkosítási algoritmus<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm, kulcs: byte [], IV: byte [])|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG-titkosítási algoritmus<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: karakterlánc, kulcs: byte [], IV: bájt [])|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG – szimmetrikus titkosítási algoritmus neve<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Szöveges karakterláncot ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm)|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG-titkosítási algoritmus<br /><br />Szöveges karakterláncot ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm, kulcs: byte [], IV: byte [])|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG-titkosítási algoritmus<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Szöveges karakterláncot ad vissza.|
|bool VerifyNoRevocation (bemenet: Ez a System. Security. kriptográfia. X509Certificates. X509certificate2))|Egy X. 509 lánc érvényesítését végzi a tanúsítvány visszavonási állapotának ellenőrzése nélkül.<br /><br />bemeneti-tanúsítvány objektum<br /><br />Az `true` érvényesítés sikerességét adja vissza. `false` ha az ellenőrzés sikertelen.|


## <a name="next-steps"></a>További lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Házirend-minták](policy-samples.md)
