---
title: Azure API Management házirend-kifejezések | Microsoft Docs
description: Ismerje meg az Azure API Management házirend-kifejezéseit. Tekintse át a példákat, és tekintse meg a további elérhető erőforrásokat
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
ms.openlocfilehash: 7117ffcbaf4eba8d83a6e968f4fed7422673610b
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844240"
---
# <a name="api-management-policy-expressions"></a>API Management házirend-kifejezések
Ez a cikk a házirend-kifejezések szintaxisát ismerteti a C# 7-es verziójában. Mindegyik kifejezés rendelkezik hozzáféréssel a implicit módon megadott [környezeti](api-management-policy-expressions.md#ContextVariables) változóhoz, valamint a .NET-keretrendszer engedélyezett [részhalmazához](api-management-policy-expressions.md#CLRTypes) .

További információk:

- Ismerje meg, hogyan adhat meg környezeti információkat a háttér-szolgáltatáshoz. Használja a [lekérdezési karakterlánc beállítása paramétert](api-management-transformation-policies.md#SetQueryStringParameter) , és adja meg a HTTP-fejléc házirendjeit az információk [megadásához](api-management-transformation-policies.md#SetHTTPheader) .
- Tekintse meg, hogyan használhatja a JWT-szabályzat [érvényesítése](api-management-access-restriction-policies.md#ValidateJWT) beállítást a jogkivonatok jogcímein alapuló műveletekhez való hozzáférés előzetes engedélyezéséhez.
- A szabályzatok kiértékelésének és az értékelések eredményeinek megtekintéséhez tekintse meg az [API-ellenőrök](./api-management-howto-api-inspector.md) nyomkövetésének használatát ismertető témakört.
- Tekintse meg, hogyan használhat kifejezéseket a [Get from cache](api-management-caching-policies.md#GetFromCache) és a [Store to cache](api-management-caching-policies.md#StoreToCache) policy házirendekkel a API Management válasz gyorsítótárazásának konfigurálásához. Állítsa be azt az időtartamot, amely megfelel a háttérrendszer válasz-gyorsítótárazásának, amelyet a biztonsági mentést végző szolgáltatás `Cache-Control` irányelve meghatároz.
- Lásd: tartalom szűrésének végrehajtása. Távolítsa el az adatelemeket a háttértől kapott válaszból a [vezérlési folyamat](api-management-advanced-policies.md#choose) és a [szövegtörzs](api-management-transformation-policies.md#SetBody) -szabályzatok használatával.
- A házirend-utasítások letöltéséhez tekintse meg az [API-Management-Samples/policies GitHub-](https://github.com/Azure/api-management-samples/tree/master/policies) tárházat.


## <a name="syntax"></a><a name="Syntax"></a>Szintaxis
Az egyutasításos kifejezések a ben vannak `@(expression)` , ahol a egy `expression` jól formázott C# Expression utasítás.

A többszörös kimutatási kifejezések a-ben vannak bezárva `@{expression}` . A többutasításos kifejezéseken belül minden kód elérési útnak egy `return` utasítással kell végződnie.

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

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Használat
A kifejezések bármely API Management [házirendben](api-management-policies.md) attribútumértékként vagy szöveges értékként használhatók (kivéve, ha a házirend-hivatkozás másként nem határoz meg).

> [!IMPORTANT]
> Ha házirend-kifejezéseket használ, csak korlátozott mértékben ellenőrzi a házirend-kifejezéseket, ha a házirend meg van határozva. A kifejezéseket az átjáró futásidőben hajtja végre, a házirend-kifejezések által generált kivételek futásidejű hibát eredményeznek.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>A .NET-keretrendszerben engedélyezett típusok házirend-kifejezésekben
A következő táblázat a .NET-keretrendszer típusait és azok tagjait sorolja fel, amelyek a házirend-kifejezésekben engedélyezettek.

|Típus|Támogatott tagok|
|--------------|-----------------------|
|Newtonsoft.Jsbekapcsolva. Formázási|Az összes|
|Newtonsoft.Json.JsonConvert|Serializeobject elem, DeserializeObject|
|Newtonsoft.Jsbekapcsolva. LINQ. Extensions|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JArray|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JConstructor|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JContainer|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JObject|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JProperty|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JRaw|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JToken|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JTokenType|Az összes|
|Newtonsoft.Jsbekapcsolva. LINQ. JValue|Az összes|
|System. Array|Az összes|
|System. BitConverter|Az összes|
|System. Boolean|Az összes|
|System. byte|Az összes|
|System. char|Az összes|
|System. Collections. Generic. Dictionary<TKey, TValue>|Az összes|
|System. Collections. Generic. HashSet\<T>|Az összes|
|System. Collections. Generic. ICollection illesztőfelületet\<T>|Az összes|
|System. Collections. Generic. IDictionary<TKey, TValue>|Az összes|
|System. Collections. Generic. IEnumerable\<T>|Az összes|
|System. Collections. Generic. IEnumerator\<T>|Az összes|
|System. Collections. Generic. IList\<T>|Az összes|
|System. Collections. Generic. IReadOnlyCollection\<T>|Az összes|
|System. Collections. Generic. IReadOnlyDictionary<TKey, TValue>|Az összes|
|System. Collections. Generic. ISet\<T>|Az összes|
|System. Collections. Generic. KeyValuePair<TKey, TValue>|Az összes|
|System. Collections. Generic. list\<T>|Az összes|
|System. Collections. Generic. üzenetsor\<T>|Az összes|
|System. Collections. Generic. stack\<T>|Az összes|
|System. Convert|Az összes|
|System. DateTime|(Konstruktor), Hozzáadás, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, dátum, nap, DayOfWeek, DayOfYear, DaysInMonth, óra, IsDaylightSavingTime, IsLeapYear, MaxValue, ezredmásodperc, perc, MinValue, hónap, most, elemzés, második, kivonás, kullancsok, TimeOfDay, ma, ToString, UtcNow, év|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Az összes|
|System. decimális|Az összes|
|System. Double|Az összes|
|System. Exception|Az összes|
|System. GUID|Az összes|
|System. Int16|Az összes|
|System. Int32|Az összes|
|System. Int64|Az összes|
|System. IO. StringReader|Az összes|
|System. IO. StringWriter|Az összes|
|System. LINQ. enumerable|Az összes|
|System. Math|Az összes|
|System. MidpointRounding|Az összes|
|System .net. websegédprogram|Az összes|
|System. Nullható|Az összes|
|System. Random|Az összes|
|System. sbyte érték|Az összes|
|System. Security. kriptográfia. AsymmetricAlgorithm|Az összes|
|System. Security. kriptográfia. titkosítási|Az összes|
|System. Security. kriptográfia. HashAlgorithm|Az összes|
|System. Security. kriptográfia. HashAlgorithmName|Az összes|
|System. Security. kriptográfia. HMAC|Az összes|
|System. Security. kriptográfia. HMACMD5|Az összes|
|System. Security. kriptográfia. HMACSHA1|Az összes|
|System. Security. kriptográfia. HMACSHA256|Az összes|
|System. Security. kriptográfia. HMACSHA384|Az összes|
|System. Security. kriptográfia. HMACSHA512|Az összes|
|System. Security. kriptográfia. KeyedHashAlgorithm|Az összes|
|System. Security. kriptográfia. MD5|Az összes|
|System. Security. kriptográfia. OID|Az összes|
|System. Security. kriptográfia. PaddingMode|Az összes|
|System. Security. kriptográfia. RNGCryptoServiceProvider|Az összes|
|System. Security. kriptográfia. RSA|Az összes|
|System. Security. kriptográfia. RSAEncryptionPadding|Az összes|
|System. Security. kriptográfia. RSASignaturePadding|Az összes|
|System. Security. kriptográfia. SHA1|Az összes|
|System. Security. kriptográfia. SHA1Managed|Az összes|
|System. Security. kriptográfia. SHA256|Az összes|
|System. Security. kriptográfia. SHA256Managed|Az összes|
|System. Security. kriptográfia. SHA384|Az összes|
|System. Security. kriptográfia. SHA384Managed|Az összes|
|System. Security. kriptográfia. SHA512|Az összes|
|System. Security. kriptográfia. SHA512Managed|Az összes|
|System. Security. kriptográfia. SymmetricAlgorithm|Az összes|
|System. Security. kriptográfia. X509Certificates. PublicKey|Az összes|
|System. Security. kriptográfia. X509Certificates. RSACertificateExtensions|Az összes|
|System. Security. kriptográfia. X509Certificates. X500DistinguishedName|Név|
|System. Security. kriptográfia. X509Certificates. X509|Az összes|
|System. Security. kriptográfia. X509Certificates. X509certificate2)|Az összes|
|System. Security. kriptográfia. X509Certificates. X509ContentType|Az összes|
|System. Security. kriptográfia. X509Certificates. X509NameType|Az összes|
|System. Single|Az összes|
|System. String|Az összes|
|System. StringComparer|Az összes|
|System. Stringcomparison argumentummal|Az összes|
|System. StringSplitOptions|Az összes|
|System. Text. Encoding|Az összes|
|System. Text. RegularExpressions. Capture|Index, hossz, érték|
|System. Text. RegularExpressions. CaptureCollection|Darabszám, elem|
|System. Text. RegularExpressions. Group|Rögzítés, sikeres|
|System. Text. RegularExpressions. GroupCollection|Darabszám, elem|
|System. Text. RegularExpressions. Match|Üres, csoportok, eredmény|
|System. Text. RegularExpressions. regex|(Konstruktor), IsMatch, egyezés, egyezés, csere, felszabadulás, felosztás|
|System. Text. RegularExpressions. RegexOptions|Az összes|
|System. Text. StringBuilder|Az összes|
|System. TimeSpan|Az összes|
|System. TimeZone|Az összes|
|System. TimeZoneInfo. AdjustmentRule|Az összes|
|System. TimeZoneInfo. TransitionTime|Az összes|
|System. TimeZoneInfo|Az összes|
|System. rekord|Az összes|
|System. UInt16|Az összes|
|System. UInt32|Az összes|
|System. UInt64|Az összes|
|System. URI|Az összes|
|System. UriPartial|Az összes|
|System.Xml. LINQ. Extensions|Az összes|
|System.Xml. LINQ. XAttribute|Az összes|
|System.Xml. LINQ. XCData|Az összes|
|System.Xml. LINQ. XComment|Az összes|
|System.Xml. LINQ. XContainer|Az összes|
|System.Xml. LINQ. XDeclaration|Az összes|
|System.Xml. LINQ. XDocument|Az összes, a kivételével: Load|
|System.Xml. LINQ. XDocumentType|Az összes|
|System.Xml. LINQ. XElement|Az összes|
|System.Xml. LINQ. XName|Az összes|
|System.Xml. LINQ. XNamespace|Az összes|
|System.Xml. LINQ. XNode|Az összes|
|System.Xml. LINQ. XNodeDocumentOrderComparer|Az összes|
|System.Xml. LINQ. XNodeEqualityComparer|Az összes|
|System.Xml. LINQ. XObject|Az összes|
|System.Xml. LINQ. XProcessingInstruction|Az összes|
|System.Xml. LINQ. XText|Az összes|
|System.Xml.XmlNodeType|Az összes|

## <a name="context-variable"></a><a name="ContextVariables"></a>Környezeti változó
Egy nevű változó `context` implicit módon elérhető minden házirend- [kifejezésben](api-management-policy-expressions.md#Syntax). A tagjai a szolgáltatással kapcsolatos információkat biztosítanak `\request` . Az összes `context` tag csak olvasható.

|Környezeti változó|Engedélyezett metódusok, tulajdonságok és paraméterek értékei|
|----------------------|-------------------------------------------------------|
|összefüggésben|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Telepítés](#ref-context-deployment)<br /><br /> Eltelt idő: TimeSpan időköz az időbélyegző és az aktuális idő értéke között<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Művelet](#ref-context-operation)<br /><br /> [Product](#ref-context-product)<br /><br /> [Kérés](#ref-context-request)<br /><br /> Kérelemazonosító: GUID – egyedi kérelem azonosítója<br /><br /> [Válasz](#ref-context-response)<br /><br /> [Előfizetés](#ref-context-subscription)<br /><br /> Időbélyeg: dátum és idő – a kérés fogadásakor időpontra<br /><br /> Nyomkövetés: bool – jelzi, hogy a nyomkövetés be van-e kapcsolva <br /><br /> [Felhasználó](#ref-context-user)<br /><br /> [Változók](#ref-context-variables): IReadOnlyDictionary<sztring, objektum><br /><br /> void nyomkövetés (üzenet: karakterlánc)|
|<a id="ref-context-api"></a>összefüggésben. API|Azonosító: karakterlánc<br /><br /> IsCurrentRevision: bool<br /><br />  Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Változat: karakterlánc<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verzió: karakterlánc |
|<a id="ref-context-deployment"></a>összefüggésben. Telepítési|Régió: karakterlánc<br /><br /> Szolgáltatásnév: karakterlánc<br /><br /> Tanúsítványok: IReadOnlyDictionary<sztring, X509certificate2)>|
|<a id="ref-context-lasterror"></a>összefüggésben. LastError|Forrás: karakterlánc<br /><br /> Ok: karakterlánc<br /><br /> Üzenet: karakterlánc<br /><br /> Hatókör: karakterlánc<br /><br /> Szakasz: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> PolicyId: karakterlánc<br /><br /> További információ a környezetről. LastError lásd: [hibakezelés](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>összefüggésben. Művelet|Azonosító: karakterlánc<br /><br /> Metódus: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> UrlTemplate: karakterlánc|
|<a id="ref-context-product"></a>összefüggésben. Termék|API-k: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Csoportok: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Állapot: Enum ProductState {NotPublished, published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>összefüggésben. Kérelem|Törzs: [IMessageBody](#ref-imessagebody) , vagy `null` Ha a kérelem nem rendelkezik törzstel.<br /><br /> Tanúsítvány: System. Security. kriptográfia. X509Certificates. X509certificate2)<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> IP-cím: karakterlánc<br /><br /> MatchedParameters: IReadOnlyDictionary<karakterlánc, karakterlánc><br /><br /> Metódus: karakterlánc<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL-cím: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>karakterlánc-környezet Request. headers. GetValueOrDefault (headerName: karakterlánc, defaultValue: string)|headerName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> A vesszővel tagolt kérelmek fejlécének értékét adja vissza, vagy `defaultValue` Ha a fejléc nem található.|
|<a id="ref-context-response"></a>összefüggésben. Válasz|Törzs: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: karakterlánc|
|<a id="ref-context-response-headers"></a>karakterlánc-környezet Response. headers. GetValueOrDefault (headerName: karakterlánc, defaultValue: karakterlánc)|headerName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> A vesszővel tagolt válasz fejlécének értékét adja vissza, vagy `defaultValue` Ha a fejléc nem található.|
|<a id="ref-context-subscription"></a>összefüggésben. Előfizetés|CreatedTime: dátum és idő<br /><br /> EndDate: DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kulcs: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> PrimaryKey: karakterlánc<br /><br /> Értesítésiközpont: karakterlánc<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>összefüggésben. Felhasználói|E-mail: karakterlánc<br /><br /> FirstName: karakterlánc<br /><br /> Csoportok: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Identitások: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: sztring<br /><br /> Megjegyzés: karakterlánc<br /><br /> RegistrationDate: dátum és idő|
|<a id="ref-iapi"></a>IApi|Azonosító: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Protokollok: IEnumerable<karakterlánc\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Azonosító: karakterlánc<br /><br /> Name: karakterlánc|
|<a id="ref-imessagebody"></a>IMessageBody|Mint<T \> (preserveContent: bool = false): where t: string, byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> A `context.Request.Body.As<T>` és `context.Response.Body.As<T>` metódusok egy adott típusban lévő kérelem és válaszüzenet törzsének olvasására szolgálnak `T` . Alapértelmezés szerint a metódus az eredeti üzenettörzs-adatfolyamot használja, és a visszatérés után elérhetetlenné teszi. Annak elkerülése érdekében, hogy a metódus a test stream egy példányán működjön, állítsa a paramétert a következőre: `preserveContent` `true` . [Itt](api-management-transformation-policies.md#SetBody) láthat egy példát.|
|<a id="ref-iurl"></a>IUrl|Gazdagép: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Port: int<br /><br /> [Lekérdezés](#ref-iurl-query): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> QueryString: karakterlánc<br /><br /> Séma: karakterlánc|
|<a id="ref-iuseridentity"></a>IUserIdentity|Azonosító: karakterlánc<br /><br /> Szolgáltató: karakterlánc|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Fejléc: karakterlánc<br /><br /> Lekérdezés: karakterlánc|
|<a id="ref-iurl-query"></a>string IUrl. Query. GetValueOrDefault (queryParameterName: karakterlánc, defaultValue: karakterlánc)|queryParameterName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> Vesszővel tagolt lekérdezési paraméterek értékét adja vissza, vagy `defaultValue` Ha a paraméter nem található.|
|<a id="ref-context-variables"></a>T környezet. Változók. GetValueOrDefault<T \> (variableName: karakterlánc, defaultValue: t)|variableName: karakterlánc<br /><br /> defaultValue: T<br /><br /> A változó értéket adja vissza a típus értékre, `T` vagy `defaultValue` Ha a változó nem található.<br /><br /> Ez a metódus kivételt jelez, ha a megadott típus nem egyezik a visszaadott változó tényleges típusával.|
|BasicAuthCredentials AsBasic (bemenet: Ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes HTTP alapszintű hitelesítési engedélyezési kérelem fejlécének értékét tartalmazza, a metódus egy típusú objektumot ad vissza, `BasicAuthCredentials` ellenkező esetben a metódus null értéket ad vissza.|
|bool TryParseBasic (bemenet: Ez a karakterlánc, eredmény: out BasicAuthCredentials)|bemenet: karakterlánc<br /><br /> eredmény: kimenő BasicAuthCredentials<br /><br /> Ha a bemeneti paraméter egy érvényes HTTP alapszintű hitelesítési engedélyezési értéket tartalmaz a kérelem fejlécében, `true` és az eredmény paraméter egy típusú értéket tartalmaz, `BasicAuthCredentials` ellenkező esetben a metódus visszaadja a értéket `false` .|
|BasicAuthCredentials|Password (jelszó): karakterlánc<br /><br /> UserId: karakterlánc|
|JWT AsJwt (bemenet: Ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes JWT token értéket tartalmaz, a metódus egy típusú objektumot ad vissza, `Jwt` ellenkező esetben a metódus visszatér `null` .|
|bool TryParseJwt (bemenet: Ez a karakterlánc, eredmény: out JWT)|bemenet: karakterlánc<br /><br /> eredmény: kimenő JWT<br /><br /> Ha a bemeneti paraméter érvényes JWT jogkivonat-értéket tartalmaz, a metódus visszaadja, `true` és a Result paraméter egy típusú értéket tartalmaz, `Jwt` ellenkező esetben a metódus visszaadja `false` .|
|JWT|Algoritmus: karakterlánc<br /><br /> Célközönségek: IEnumerable<sztring\><br /><br /> Jogcímek: IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> Expirationtime tulajdonságok: DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kiállító: karakterlánc<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Tárgy: karakterlánc<br /><br /> Típus: karakterlánc|
|karakterlánc JWT. jogcím. GetValueOrDefault (claimName: karakterlánc, defaultValue: karakterlánc)|claimName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> Vesszővel tagolt jogcím-értékeket ad vissza, vagy `defaultValue` Ha a fejléc nem található.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: karakterlánc, kulcs: byte [], IV: bájt [])|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG – szimmetrikus titkosítási algoritmus neve<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm)|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG-titkosítási algoritmus<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm, kulcs: byte [], IV: byte [])|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG-titkosítási algoritmus<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: karakterlánc, kulcs: byte [], IV: bájt [])|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG – szimmetrikus titkosítási algoritmus neve<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Szöveges karakterláncot ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm)|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG-titkosítási algoritmus<br /><br />Szöveges karakterláncot ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm, kulcs: byte [], IV: byte [])|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG-titkosítási algoritmus<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Szöveges karakterláncot ad vissza.|
|bool VerifyNoRevocation (bemenet: Ez a System. Security. kriptográfia. X509Certificates. X509certificate2))|Egy X. 509 lánc érvényesítését végzi a tanúsítvány visszavonási állapotának ellenőrzése nélkül.<br /><br />bemeneti-tanúsítvány objektum<br /><br />Azt adja vissza `true` , hogy az ellenőrzés sikeres- `false` e; ha az ellenőrzés sikertelen.|


## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Házirend-minták](policy-samples.md)
