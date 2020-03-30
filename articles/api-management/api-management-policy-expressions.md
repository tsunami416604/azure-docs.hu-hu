---
title: Azure API Management házirend-kifejezések | Microsoft dokumentumok
description: Ismerje meg a szabályzati kifejezéseket az Azure API Managementben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244003"
---
# <a name="api-management-policy-expressions"></a>API-kezelés házirend-kifejezései
Ez a cikk a C# 7 házirend-kifejezéseit ismerteti. Minden kifejezés hozzáfér az implicit módon megadott [környezeti](api-management-policy-expressions.md#ContextVariables) változóhoz és a .NET keretrendszertípusok engedélyezett [részhalmazához.](api-management-policy-expressions.md#CLRTypes)

További információk:

- Tekintse meg, hogyan biztosíthatja a háttéradatokat a háttérszolgáltatás. Használja a [Lekérdezési karakterlánc beállítása paramétert](api-management-transformation-policies.md#SetQueryStringParameter) és a [HTTP fejlécházirendek beállítását](api-management-transformation-policies.md#SetHTTPheader) az adatok biztosításához.
- Tekintse meg, hogyan használhatja a [JWT-szabályzat érvényesítése](api-management-access-restriction-policies.md#ValidateJWT) a jogkivonatjogcímeken alapuló műveletekhez való hozzáférés előzetes engedélyezéséhez.
- Tekintse meg, hogyan használhatja az [API-felügyelő](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) nyomkövetési a szabályzatok kiértékelésének és az értékelések eredményeinek megtekintéséhez.
- Tekintse meg, hogyan használhatja a kifejezéseket a [Get from cache](api-management-caching-policies.md#GetFromCache) and Store to [cache](api-management-caching-policies.md#StoreToCache) házirendek API Management válaszgyorsítótárazás konfigurálásához. Olyan időtartamot állítson be, amely megfelel a háttérszolgáltatás válaszgyorsítótárazásának `Cache-Control` a háttérszolgáltatás direktíva által meghatározott módon.
- Tekintse meg a tartalomszűrés elvégzésének módját. Távolítsa el az adatelemeket a háttérrendszerből kapott válaszból a [Vezérlőfolyamat](api-management-advanced-policies.md#choose) és a [Testházirendek beállítása](api-management-transformation-policies.md#SetBody) használatával.
- A szabályzati utasítások letöltéséhez tekintse meg az [api-felügyeleti minták/szabályzatok](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub-tár).


## <a name="syntax"></a><a name="Syntax"></a>Szintaxis
Az egyutasításos kifejezések `@(expression)`a `expression` be vannak zárva, ahol egy jól formázott C# kifejezés.

A többutasításos kifejezések et `@{expression}`a. A többutasításos kifejezéseken belüli összes kódelérési utat utasítással `return` kell befejezni.

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
A kifejezések attribútum- vagy szöveges értékként használhatók bármely API [Management-házirendben](api-management-policies.md) (kivéve, ha a házirend-hivatkozás másként rendelkezik).

> [!IMPORTANT]
> Ha házirend-kifejezéseket használ, a házirend-kifejezések csak korlátozottan ellenőrzik, ha a házirend definiálva van. A kifejezéseket az átjáró futásidőben hajtja végre, a házirend-kifejezések által létrehozott kivételek futásidejű hibát eredményeznek.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>A házirend-kifejezésekben engedélyezett .
Az alábbi táblázat a .

|Típus|Támogatott tagok|
|--------------|-----------------------|
|Newtonsoft.Json.Formázás|Összes|
|Newtonsoft.Json.JsonKonvertál|Szerializálásobjektum, DeserializeObjektum|
|Newtonsoft.Json.Linq.Extensions (Newtonsoft.Json.Linq.Extensions)|Összes|
|Newtonsoft.Json.Linq.JArray|Összes|
|Newtonsoft.Json.Linq.JKKonstruktor|Összes|
|Newtonsoft.Json.Linq.JKonténer|Összes|
|Newtonsoft.Json.Linq.JTárgy|Összes|
|Newtonsoft.Json.Linq.JIngatlan|Összes|
|Newtonsoft.Json.Linq.JRaw|Összes|
|Newtonsoft.Json.Linq.JToken|Összes|
|Newtonsoft.Json.Linq.JTokenType|Összes|
|Newtonsoft.Json.Linq.JÉrték|Összes|
|System.Tömb|Összes|
|System.BitConverter|Összes|
|System.Boolean|Összes|
|System.Byte fájl|Összes|
|Rendszer.Karakter|Összes|
|System.Collections.Generic.Dictionary<TKey, TValue>|Összes|
|System.Collections.Generic.HashSet\<T>|Összes|
|System.Collections.Generic.ICollection\<T>|Összes|
|System.Collections.Generic.IDictionary<TKey, TValue>|Összes|
|System.Collections.Generic.IEnumerable\<T>|Összes|
|System.Collections.Generic.IEnumerator\<T>|Összes|
|System.Collections.Generic.IList\<T>|Összes|
|System.Collections.Generic.IReadOnlyCollection\<T>|Összes|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Összes|
|System.Collections.Generic.ISet\<T>|Összes|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Összes|
|System.Collections.Generic.List\<T>|Összes|
|System.Collections.Generic.Queue\<T>|Összes|
|System.Collections.Generic.Stack\<T>|Összes|
|System.Convert|Összes|
|System.DateTime|(Konstruktor), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Dátum, Nap, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Milliszekundum, Perc, MinValue, Hónap, Now , Elemzés, Második, Kivonás, Kullancsok, TimeOfDay, Ma, ToString, UtcNow, Év|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Összes|
|System.Decimal|Összes|
|System.Double (Kétszeris)|Összes|
|System.Exception|Összes|
|System.Guid fájl|Összes|
|Rendszer.Int16|Összes|
|Rendszer.Int32|Összes|
|Rendszer.Int64|Összes|
|System.IO.StringReader|Összes|
|System.IO.StringWriter|Összes|
|System.Linq.Ennumerable|Összes|
|System.Math|Összes|
|System.MidpointRounding|Összes|
|System.Net.WebUtility|Összes|
|System.Nullable|Összes|
|System.Random|Összes|
|System.SByte fájl|Összes|
|System.Security.Cryptography.AsymmetricAlgorithm|Összes|
|System.Security.Cryptography.CipherMode|Összes|
|System.Security.Cryptography.HashAlgorithm|Összes|
|System.Security.Cryptography.HashAlgorithmName|Összes|
|Rendszer.Biztonság.Kriptográfia.HMAC|Összes|
|Rendszer.Security.Cryptography.HMACMD5|Összes|
|Rendszer.Biztonság.Kriptográfia.HMACSHA1|Összes|
|Rendszer.Security.Cryptography.HMACSHA256|Összes|
|Rendszer.Security.Cryptography.HMACSHA384|Összes|
|Rendszer.Security.Cryptography.HMACSHA512|Összes|
|System.Security.Cryptography.KeyedHashAlgorithm|Összes|
|Rendszer.Security.Cryptography.MD5|Összes|
|Rendszer.Biztonság.Kriptográfia.Oid|Összes|
|System.Security.Cryptography.PaddingMode|Összes|
|System.Security.Cryptography.RNGCryptoServiceProvider|Összes|
|Rendszer.Biztonság.Kriptográfia.RSA|Összes|
|System.Security.Cryptography.RSAEncryptionPadding|Összes|
|System.Security.Cryptography.RSASignaturePadding|Összes|
|Rendszer.Security.Cryptography.SHA1|Összes|
|System.Security.Cryptography.SHA1Felügyelt|Összes|
|Rendszer.Security.Cryptography.SHA256|Összes|
|System.Security.Cryptography.SHA256Felügyelt|Összes|
|Rendszer.Security.Cryptography.SHA384|Összes|
|System.Security.Cryptography.SHA384Felügyelt|Összes|
|Rendszer.Security.Cryptography.SHA512|Összes|
|System.Security.Cryptography.SHA512Felügyelt|Összes|
|System.Security.Cryptography.SymmetricAlgorithm|Összes|
|System.Security.Cryptography.X509Certificates.PublicKey|Összes|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Összes|
|System.Security.Cryptography.X509Certificates.X500Megkülönböztetett név|Név|
|System.Security.Cryptography.X509Certificates.X509Certificate|Összes|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Összes|
|System.Security.Cryptography.X509Certificates.X509ContentType|Összes|
|System.Security.Cryptography.X509Certificates.X509NameType|Összes|
|System.Single|Összes|
|System.String|Összes|
|System.StringComparer|Összes|
|System.StringÖsszehasonlítás|Összes|
|System.StringSplitOptions|Összes|
|System.Text.Enkódolás|Összes|
|System.Text.RegularExpressions.Capture|Index, Hossz, Érték|
|System.Text.RegularExpressions.CaptureCollection|Darabszám, Cikk|
|System.Text.RegularExpressions.Group|Rögzítés, siker|
|System.Text.RegularExpressions.GroupCollection|Darabszám, Cikk|
|System.Text.RegularExpressions.Match|Üres, Csoportok, Eredmény|
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, Egyezés, Gyufa, Csere, Kioldatlan, Split|
|System.Text.RegularExpressions.RegexOptions|Összes|
|System.Text.StringBuilder|Összes|
|System.TimeSpan|Összes|
|System.TimeZone|Összes|
|System.TimeZoneInfo.AdjustmentRule|Összes|
|System.TimeZoneInfo.TransitionTime|Összes|
|System.TimeZoneInfo|Összes|
|System.Tuple fájl|Összes|
|System.UInt16|Összes|
|System.UInt32|Összes|
|System.UInt64|Összes|
|Rendszer.Uri|Összes|
|System.UriPartial|Összes|
|System.Xml.Linq.Extensions (System.Xml.Linq.Extensions)|Összes|
|System.Xml.Linq.XAttribute|Összes|
|System.Xml.Linq.XCData|Összes|
|System.Xml.Linq.XMegjegyzés|Összes|
|System.Xml.Linq.XContainer fájl|Összes|
|System.Xml.Linq.XDeclaration|Összes|
|System.Xml.Linq.XDocument|Mind, kivéve: Betöltés|
|System.Xml.Linq.XDocumentType|Összes|
|System.Xml.Linq.XElement|Összes|
|System.Xml.Linq.XName|Összes|
|System.Xml.Linq.XNamespace|Összes|
|Rendszer.Xml.Linq.XNode|Összes|
|System.Xml.Linq.XNodeDocumentOrderComparer|Összes|
|System.Xml.Linq.XNodeEqualityComparer|Összes|
|System.Xml.Linq.XObject objektum|Összes|
|System.Xml.Linq.XProcessingInstruction|Összes|
|System.Xml.Linq.XSzöveg|Összes|
|System.Xml.XmlNodeType|Összes|

## <a name="context-variable"></a><a name="ContextVariables"></a>Környezeti változó
A megnevezett `context` változó implicit módon elérhető minden [házirendkifejezésben.](api-management-policy-expressions.md#Syntax) Tagjai az adott országgal kapcsolatos `\request`információkat szolgáltatnak. Minden `context` tag csak olvasható.

|Környezeti változó|Engedélyezett metódusok, tulajdonságok és paraméterértékek|
|----------------------|-------------------------------------------------------|
|Összefüggésben|[Api](#ref-context-api): [Iapi](#ref-iapi)<br /><br /> [Környezet](#ref-context-deployment)<br /><br /> Eltelt: TimeSpan - az időbélyeg értéke és az aktuális idő közötti időintervallum<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Művelet](#ref-context-operation)<br /><br /> [Termék](#ref-context-product)<br /><br /> [Kérés](#ref-context-request)<br /><br /> RequestId: Guid – egyedi kérelemazonosító<br /><br /> [Válasz](#ref-context-response)<br /><br /> [Előfizetés](#ref-context-subscription)<br /><br /> Időbélyeg: DateTime - a kérelem beérkezésének időpontja<br /><br /> Nyomkövetés: bool - azt jelzi, hogy a nyomkövetés be van-e kapcsolva vagy ki van kapcsolva <br /><br /> [Felhasználó](#ref-context-user)<br /><br /> [Változók](#ref-context-variables): IReadOnlyDictionary<karakterlánc, objektum><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>Összefüggésben. Api|Azonosító: karakterlánc<br /><br /> IsCurrentRevision: bool<br /><br />  Név: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Verzió: karakterlánc<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verzió: karakterlánc |
|<a id="ref-context-deployment"></a>Összefüggésben. Telepítési|Régió: karakterlánc<br /><br /> Szolgáltatásnév: karakterlánc<br /><br /> Tanúsítványok: IReadOnlyDictionary<karakterlánc, X509Certificate2>|
|<a id="ref-context-lasterror"></a>Összefüggésben. Utolsó hiba|Forrás: karakterlánc<br /><br /> Ok: karakterlánc<br /><br /> Üzenet: karakterlánc<br /><br /> Hatókör: karakterlánc<br /><br /> Szakasz: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> PolicyId: karakterlánc<br /><br /> További információ a környezetről. LastError , [lásd: Hibakezelés](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>Összefüggésben. Művelet|Azonosító: karakterlánc<br /><br /> Metódus: karakterlánc<br /><br /> Név: karakterlánc<br /><br /> URLTemplate: karakterlánc|
|<a id="ref-context-product"></a>Összefüggésben. Termék|Apis: IEnumera<[IApi](#ref-iapi)\><br /><br /> JóváhagyásSzükséges: bool<br /><br /> Csoportok: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Név: karakterlánc<br /><br /> Állapot: felsorak: ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> Előfizetésszükséges: bool|
|<a id="ref-context-request"></a>Összefüggésben. Kérés|Törzs: [IMessageBody](#ref-imessagebody) vagy `null` ha a kérés nem rendelkezik törzs.<br /><br /> Tanúsítvány: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Fejlécek](#ref-context-request-headers): IReadOnlyDictionary<karakterlánc, karakterlánc[]><br /><br /> IpAddress: karakterlánc<br /><br /> MatchedParameters: IReadOnlyDictionary<karakterlánc, karakterlánc><br /><br /> Metódus: karakterlánc<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>karakterlánc-környezetben. Request.Headers.GetValueOrDefault(headerName: karakterlánc, defaultValue: karakterlánc)|headerName: karakterlánc<br /><br /> defaultValue : karakterlánc<br /><br /> Vesszővel tagolt kérelemfejléc-értékeket `defaultValue` ad eredményül, vagy ha a fejléc nem található.|
|<a id="ref-context-response"></a>Összefüggésben. Válasz|Törzs: [IMessageBody](#ref-imessagebody)<br /><br /> [Fejlécek](#ref-context-response-headers): IReadOnlyDictionary<karakterlánc, karakterlánc[]><br /><br /> StatusCode: int<br /><br /> StatusReason: karakterlánc|
|<a id="ref-context-response-headers"></a>karakterlánc-környezetben. Response.Headers.GetValueOrDefault(headerName: karakterlánc, defaultValue: karakterlánc)|headerName: karakterlánc<br /><br /> defaultValue : karakterlánc<br /><br /> Vesszővel tagolt válaszfejléc-értékeket `defaultValue` ad eredményül, vagy ha a fejléc nem található.|
|<a id="ref-context-subscription"></a>Összefüggésben. Előfizetés|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kulcs: karakterlánc<br /><br /> Név: karakterlánc<br /><br /> PrimaryKey: karakterlánc<br /><br /> SecondaryKey: karakterlánc<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>Összefüggésben. Felhasználó|E-mail cím: karakterlánc<br /><br /> Keresztnév: karakterlánc<br /><br /> Csoportok: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Identitások: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> Vezetéknév: karakterlánc<br /><br /> Megjegyzés: karakterlánc<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>Iapi között|Azonosító: karakterlánc<br /><br /> Név: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Protokollok: IEnumerable<karakterlánc\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [iSubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup csoport|Azonosító: karakterlánc<br /><br /> Név: karakterlánc|
|<a id="ref-imessagebody"></a>IMessageBody|Ahogy\><T (preserveContent: bool = false): Ahol T: karakterlánc, bájt[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> A `context.Request.Body.As<T>` `context.Response.Body.As<T>` és a módszerek egy adott típusú `T`kérés- és válaszüzenet-törzsek olvasására szolgálnak. Alapértelmezés szerint a metódus az eredeti üzenettörzs-adatfolyamot használja, és visszatérése után elérhetetlenné teszi azt. Annak elkerülése érdekében, hogy a metódus a törzsfolyam `preserveContent` egy `true`példányán működjön, állítsa a paramétert a-ra. Menj [ide,](api-management-transformation-policies.md#SetBody) hogy egy példát.|
|<a id="ref-iurl"></a>IUrl|Állomás: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Port: int<br /><br /> [Lekérdezés](#ref-iurl-query): IReadOnlyDictionary<karakterlánc, karakterlánc[]><br /><br /> QueryString: karakterlánc<br /><br /> Séma: string|
|<a id="ref-iuseridentity"></a>IUserIdentity|Azonosító: karakterlánc<br /><br /> Szolgáltató: karakterlánc|
|<a id="ref-isubscriptionkeyparameternames"></a>iSubscriptionKeyParameterNames|Fejléc: karakterlánc<br /><br /> Lekérdezés: karakterlánc|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: karakterlánc<br /><br /> defaultValue : karakterlánc<br /><br /> Vesszővel tagolt lekérdezési paraméterértékeket ad eredményül, vagy `defaultValue` ha a paraméter nem található.|
|<a id="ref-context-variables"></a>T kontextus. Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: karakterlánc<br /><br /> defaultValue: T<br /><br /> A beírt változó `T` `defaultValue` értéket adja vissza, vagy ha a változó nem található.<br /><br /> Ez a módszer kivételt okoz, ha a megadott típus nem egyezik meg a visszaadott változó tényleges típusával.|
|BasicAuthCredentials AsBasic(input: ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes HTTP-alapfokú hitelesítés engedélyezési kérelem `BasicAuthCredentials`fejlécértékét tartalmazza, a metódus egy típusú objektumot ad vissza; ellenkező esetben a metódus null értéket ad vissza.|
|bool TryParseBasic(input: ez a karakterlánc, eredmény: out BasicAuthCredentials)|bemenet: karakterlánc<br /><br /> eredmény: out BasicAuthCredentials<br /><br /> Ha a bemeneti paraméter érvényes HTTP-alaphitelesítési engedélyezési `true` értéket tartalmaz a kérelem fejlécében, a metódus ad vissza, és az eredményparaméter típusértéket `BasicAuthCredentials`tartalmaz; ellenkező esetben `false`a metódus visszatér .|
|BasicAuthCredentials|Jelszó: karakterlánc<br /><br /> UserId: karakterlánc|
|Jwt AsJwt(bemenet: ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes JWT tokenértéket tartalmaz, `Jwt`a metódus egy típusú objektumot ad vissza; ellenkező esetben `null`a metódus visszatér .|
|bool TryParseJwt(input: ez a karakterlánc, eredmény: ki Jwt)|bemenet: karakterlánc<br /><br /> eredmény: ki Jwt<br /><br /> Ha a bemeneti paraméter érvényes JWT tokenértéket tartalmaz, a metódus `true` `Jwt`visszaadja, és az eredményparaméter típusértéket tartalmaz; ellenkező esetben `false`a metódus visszatér .|
|Jwt|Algoritmus: karakterlánc<br /><br /> Közönség: IEnumerable<string\><br /><br /> Jogcímek: IReadOnlyDictionary<karakterlánc, karakterlánc[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kiállító: karakterlánc<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Tárgy: karakterlánc<br /><br /> Típus: karakterlánc|
|jwt.claims.getvalueordefault(jogcímnév: karakterlánc, defaultValue: karakterlánc)|jogcímnév: karakterlánc<br /><br /> defaultValue : karakterlánc<br /><br /> Vesszővel tagolt jogcímértékeket `defaultValue` ad eredményül, vagy ha a fejléc nem található.|
|byte[] Titkosítás(bemenet: ez a bájt[], alg: karakterlánc, kulcs:bájt[], iv:bájt[])|bemenet - titkosítandó egyszerű szöveg<br /><br />alg - szimmetrikus titkosítási algoritmus neve<br /><br />kulcs - titkosítási kulcs<br /><br />iv - inicializálási vektor<br /><br />Titkosított egyszerű szöveget ad vissza.|
|byte[] Titkosítás(bemenet: ez a bájt[], alg: System.Security.Cryptography.SymmetricAlgorithm)|bemenet - titkosítandó egyszerű szöveg<br /><br />alg - titkosítási algoritmus<br /><br />Titkosított egyszerű szöveget ad vissza.|
|byte[] Titkosítás(bemenet: ez a bájt[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|bemenet - titkosítandó egyszerű szöveg<br /><br />alg - titkosítási algoritmus<br /><br />kulcs - titkosítási kulcs<br /><br />iv - inicializálási vektor<br /><br />Titkosított egyszerű szöveget ad vissza.|
|byte[] Visszafejtés(bemenet: ez a bájt[], alg: karakterlánc, kulcs:bájt[], iv:bájt[])|bemenet - visszafejtendő cypher szöveg<br /><br />alg - szimmetrikus titkosítási algoritmus neve<br /><br />kulcs - titkosítási kulcs<br /><br />iv - inicializálási vektor<br /><br />Egyszerű szöveget ad vissza.|
|byte[] Visszafejtés(bemenet: ez a bájt[], alg: System.Security.Cryptography.SymmetricAlgorithm)|bemenet - visszafejtendő cypher szöveg<br /><br />alg - titkosítási algoritmus<br /><br />Egyszerű szöveget ad vissza.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|bemenet - visszafejtendő cypher szöveg<br /><br />alg - titkosítási algoritmus<br /><br />kulcs - titkosítási kulcs<br /><br />iv - inicializálási vektor<br /><br />Egyszerű szöveget ad vissza.|
|bool VerifyNoRevocation(bemenet: ez system.Security.Cryptography.X509Certificates.X509Certificate2)|X.509 láncellenőrzést hajt végre a tanúsítvány visszavonási állapotának ellenőrzése nélkül.<br /><br />bemenet - tanúsítványobjektum<br /><br />Akkor `true` adja vissza, ha az érvényesítés sikeres; `false` ha az érvényesítés sikertelen.|


## <a name="next-steps"></a>További lépések

A házirendekkel kapcsolatos további információkért lásd:

+ [Szabályzatok az API Managementben](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
+ [Házirendminták](policy-samples.md)
