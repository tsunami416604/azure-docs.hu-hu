---
title: Az Azure API Management házirend-kifejezések |} A Microsoft Docs
description: További információ az Azure API Management házirend-kifejezések.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: b8bd6e7c77faa54a8ebf0842cf140ef8aa73e953
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834543"
---
# <a name="api-management-policy-expressions"></a>Az API Management házirend-kifejezések
Ez a cikk ismerteti a házirend-kifejezések szintaxisa C# 7. Minden egyes kifejezés férhet hozzá a implicit módon megadott [környezet](api-management-policy-expressions.md#ContextVariables) változót és a egy engedélyezett [részhalmazát](api-management-policy-expressions.md#CLRTypes) .NET-keretrendszer típusú.

További információk:

- Adja meg a háttérszolgáltatásban környezeti információkat talál. Használja a [állítsa be a lekérdezési sztring paramétereként](api-management-transformation-policies.md#SetQueryStringParameter) és [állítsa be a HTTP-fejléc](api-management-transformation-policies.md#SetHTTPheader) megadják ezt az információt a szabályzatok.
- Hogyan használhatja a [ellenőrzése JWT](api-management-access-restriction-policies.md#ValidateJWT) az előzetes engedélyezéshez műveletek a hozzáférést a házirend jogkivonat alapján.
- Hogyan használhatja egy [API Inspectorral](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) nyomkövetését hogyan értékeli ki a szabályzatok, és ezen értékelés eredménye.
- Kifejezések használatával való használatáról a [lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCache) és [Store Cache](api-management-caching-policies.md#StoreToCache) szabályzatainak használatával konfigurálhatja az API Management-válaszok gyorsítótárazását. Állítsa be, amely megfelel a válaszok gyorsítótárazását, a háttérszolgáltatás az készült biztonsági másolat szolgáltatás által megadott időtartamot `Cache-Control` irányelv.
- Tekintse meg az tartalomszűrés végrehajtásához. Adatok eltávolítása a háttérrendszer használatával-tól kapott választ a [átvitelvezérlés](api-management-advanced-policies.md#choose) és [állítsa be a szervezet](api-management-transformation-policies.md#SetBody) házirendeket.
- A házirend-utasítások letöltéséhez tekintse meg a [api-felügyeleti-samples/házirendek](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub-adattárban.


## <a name="Syntax"></a> Szintaxis
Egyetlen utasítás kifejezések parancsfájlblokkjában találhatók `@(expression)`, ahol `expression` van egy megfelelően formázott C# kifejezés utasítást.

Több utasításból álló kifejezéseket parancsfájlblokkjában találhatók `@{expression}`. Több utasításból álló kifejezésekben görbékhez kódot kell végződnie egy `return` utasítást.

## <a name="PolicyExpressionsExamples"></a> Példák

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

## <a name="PolicyExpressionsUsage"></a>Használat
Kifejezések használhatók vagy az összes API Management a szöveges értékek [házirendek](api-management-policies.md) (kivéve, ha a házirend-referencia ellenkező esetben adja meg).

> [!IMPORTANT]
> Házirend-kifejezések használata esetén nincs csak korlátozott ellenőrzése a házirend-kifejezések, ha a szabályzat van definiálva. Kifejezések futásidőben, futásidejű hiba házirend-kifejezések eredményez által előállított kivételeket az átjáró által lesznek végrehajtva.

## <a name="CLRTypes"></a> .NET-keretrendszer típusok engedélyezett a házirend-kifejezések
A következő táblázat felsorolja a .NET-keretrendszer és a tagok, amelyek a házirend-kifejezések engedélyezettek.

|Típus|Támogatott tagok|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Az összes|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Az összes|
|Newtonsoft.Json.Linq.JArray|Az összes|
|Newtonsoft.Json.Linq.JConstructor|Az összes|
|Newtonsoft.Json.Linq.JContainer|Az összes|
|Newtonsoft.Json.Linq.JObject|Az összes|
|Newtonsoft.Json.Linq.JProperty|Az összes|
|Newtonsoft.Json.Linq.JRaw|Az összes|
|Newtonsoft.Json.Linq.JToken|Az összes|
|Newtonsoft.Json.Linq.JTokenType|Az összes|
|Newtonsoft.Json.Linq.JValue|Az összes|
|System.Array|Az összes|
|System.BitConverter|Az összes|
|System.Boolean|Az összes|
|System.Byte|Az összes|
|System.Char|Az összes|
|System.Collections.Generic.Dictionary < TKey, TValue >|Az összes|
|System.Collections.Generic.HashSet<T>|Az összes|
|System.Collections.Generic.ICollection<T>|Az összes|
|System.Collections.Generic.IDictionary < TKey, TValue >|Az összes|
|System.Collections.Generic.IEnumerable<T>|Az összes|
|System.Collections.Generic.IEnumerator<T>|Az összes|
|System.Collections.Generic.IList<T>|Az összes|
|System.Collections.Generic.IReadOnlyCollection<T>|Az összes|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Az összes|
|System.Collections.Generic.ISet<T>|Az összes|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Az összes|
|System.Collections.Generic.List<T>|Az összes|
|System.Collections.Generic.Queue<T>|Az összes|
|System.Collections.Generic.Stack<T>|Az összes|
|System.Convert|Az összes|
|System.DateTime|(Konstruktor), hozzáadása, napokHozzaadasa, AddHours, AddMilliseconds, AddMinutes, AddMonths, masodpercekHozzaadasa, AddTicks, AddYears, dátum, nap, DayOfWeek, DayOfYear, DaysInMonth, óra, IsDaylightSavingTime, IsLeapYear, MaxValue, ezredmásodperces, perc, a MinValue, hónap, most , Elemzés, a második, kivonás, órajel során végbemenő, TimeOfDay, ma, ToString, UtcNow, év|
|System.DateTimeKind|(UTC)|
|System.DateTimeOffset|Az összes|
|System.Decimal|Az összes|
|System.Double|Az összes|
|System.Exception|Az összes|
|System.Guid|Az összes|
|System.Int16|Az összes|
|System.Int32|Az összes|
|System.Int64|Az összes|
|System.IO.StringReader|Az összes|
|System.IO.StringWriter|Az összes|
|System.Linq.Enumerable|Az összes|
|System.Math|Az összes|
|System.MidpointRounding|Az összes|
|System.Net.WebUtility|Az összes|
|System.Nullable|Az összes|
|System.Random|Az összes|
|System.SByte|Az összes|
|System.Security.Cryptography.AsymmetricAlgorithm|Az összes|
|System.Security.Cryptography.CipherMode|Az összes|
|System.Security.Cryptography.HashAlgorithm|Az összes|
|System.Security.Cryptography.HashAlgorithmName|Az összes|
|System.Security.Cryptography.HMAC|Az összes|
|System.Security.Cryptography.HMACMD5|Az összes|
|System.Security.Cryptography.HMACSHA1|Az összes|
|System.Security.Cryptography.HMACSHA256|Az összes|
|System.Security.Cryptography.HMACSHA384|Az összes|
|System.Security.Cryptography.HMACSHA512|Az összes|
|System.Security.Cryptography.KeyedHashAlgorithm|Az összes|
|System.Security.Cryptography.MD5|Az összes|
|System.Security.Cryptography.Oid|Az összes|
|System.Security.Cryptography.PaddingMode|Az összes|
|System.Security.Cryptography.RNGCryptoServiceProvider|Az összes|
|System.Security.Cryptography.RSA|Az összes|
|System.Security.Cryptography.RSAEncryptionPadding|Az összes|
|System.Security.Cryptography.RSASignaturePadding|Az összes|
|System.Security.Cryptography.SHA1|Az összes|
|System.Security.Cryptography.SHA1Managed|Az összes|
|System.Security.Cryptography.SHA256|Az összes|
|System.Security.Cryptography.SHA256Managed|Az összes|
|System.Security.Cryptography.SHA384|Az összes|
|System.Security.Cryptography.SHA384Managed|Az összes|
|System.Security.Cryptography.SHA512|Az összes|
|System.Security.Cryptography.SHA512Managed|Az összes|
|System.Security.Cryptography.SymmetricAlgorithm|Az összes|
|System.Security.Cryptography.X509Certificates.PublicKey|Az összes|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Az összes|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name (Név)|
|System.Security.Cryptography.X509Certificates.X509Certificate|Az összes|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Az összes|
|System.Security.Cryptography.X509Certificates.X509ContentType|Az összes|
|System.Security.Cryptography.X509Certificates.X509NameType|Az összes|
|System.Single|Az összes|
|System.String|Az összes|
|System.StringComparer|Az összes|
|System.StringComparison|Az összes|
|System.StringSplitOptions|Az összes|
|System.Text.Encoding|Az összes|
|System.Text.RegularExpressions.Capture|Index, hosszúságú érték|
|System.Text.RegularExpressions.CaptureCollection|Szám, cikk|
|System.Text.RegularExpressions.Group|Rögzíti, sikeres|
|System.Text.RegularExpressions.GroupCollection|Szám, cikk|
|System.Text.RegularExpressions.Match|Üres, csoportok, eredménye|
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, egyezik, megegyezik, cserélje le, Unescape, felosztása|
|System.Text.RegularExpressions.RegexOptions|Az összes|
|System.Text.StringBuilder|Az összes|
|System.TimeSpan|Az összes|
|System.TimeZone|Az összes|
|System.TimeZoneInfo.AdjustmentRule|Az összes|
|System.TimeZoneInfo.TransitionTime|Az összes|
|System.TimeZoneInfo|Az összes|
|System.Tuple|Az összes|
|System.UInt16|Az összes|
|System.UInt32|Az összes|
|System.UInt64|Az összes|
|System.Uri|Az összes|
|System.UriPartial|Az összes|
|System.Xml.Linq.Extensions|Az összes|
|System.Xml.Linq.XAttribute|Az összes|
|System.Xml.Linq.XCData|Az összes|
|System.Xml.Linq.XComment|Az összes|
|System.Xml.Linq.XContainer|Az összes|
|System.Xml.Linq.XDeclaration|Az összes|
|System.Xml.Linq.XDocument|Az összes, kivéve a: Betöltés|
|System.Xml.Linq.XDocumentType|Az összes|
|System.Xml.Linq.XElement|Az összes|
|System.Xml.Linq.XName|Az összes|
|System.Xml.Linq.XNamespace|Az összes|
|System.Xml.Linq.XNode|Az összes|
|System.Xml.Linq.XNodeDocumentOrderComparer|Az összes|
|System.Xml.Linq.XNodeEqualityComparer|Az összes|
|System.Xml.Linq.XObject|Az összes|
|System.Xml.Linq.XProcessingInstruction|Az összes|
|System.Xml.Linq.XText|Az összes|
|System.Xml.XmlNodeType|Az összes|

## <a name="ContextVariables"></a> Környezeti változó
Nevű változó `context` implicit módon érhető el az összes szabályzatban [kifejezés](api-management-policy-expressions.md#Syntax). A tagjai a profiljával kapcsolatos információkat biztosít a `\request`. Összes a `context` tagjai csak olvasható.

|Környezeti változó|Engedélyezett metódusok, tulajdonságok és értékei|
|----------------------|-------------------------------------------------------|
|Környezet|API: IApi<br /><br /> Környezet<br /><br /> Eltelt idő: Időtartomány - időbélyeg értékét, a pontos idő közötti időintervallum<br /><br /> LastError<br /><br /> Művelet<br /><br /> Product<br /><br /> Lekérés<br /><br /> RequestId: GUID - kérelem egyedi azonosítója<br /><br /> Válasz<br /><br /> Előfizetés<br /><br /> Időbélyeg: Dátum és idő – időpont, amikor a kérelem érkezett<br /><br /> Nyomkövetés: logikai - azt jelzi, hogy nyomkövetés be- vagy kikapcsolása <br /><br /> Felhasználó<br /><br /> Változók: IReadOnlyDictionary < karakterlánc, objektum ><br /><br /> typ void Trace(message: string)|
|context.Api|ID: karakterlánc<br /><br /> IsCurrentRevision: logikai<br /><br />  Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Verzió: karakterlánc<br /><br /> ServiceUrl: IUrl<br /><br /> Verzió: karakterlánc |
|context.Deployment|Régió: karakterlánc<br /><br /> Szolgáltatásnév: karakterlánc<br /><br /> Tanúsítványok: IReadOnlyDictionary<string, X509Certificate2>|
|context.LastError|Forrás: karakterlánc<br /><br /> OK: karakterlánc<br /><br /> Üzenet: karakterlánc<br /><br /> Hatókör: karakterlánc<br /><br /> A szakasz: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> PolicyId: karakterlánc<br /><br /> További információ a környezetben. LastError, lásd: [hibakezelés](api-management-error-handling-policies.md).|
|a környezet. A művelet|ID: karakterlánc<br /><br /> Módszer: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> UrlTemplate: karakterlánc|
|a környezet. A termék|API-kat: IEnumerable<IApi\><br /><br /> ApprovalRequired: logikai<br /><br /> Csoportok: IEnumerable < IGroup\><br /><br /> ID: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Állapot: enum ProductState {NotPublished, közzétett}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: logikai|
|context.Request|Szövegtörzs: IMessageBody<br /><br /> Tanúsítvány: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Fejlécek: IReadOnlyDictionary < string, string [] ><br /><br /> IP-cím: karakterlánc<br /><br /> MatchedParameters: IReadOnlyDictionary < karakterlánc, karakterlánc ><br /><br /> Módszer: karakterlánc<br /><br /> OriginalUrl:IUrl<br /><br /> URL-cím: IUrl|
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: karakterlánc<br /><br /> alapértelmezett érték: karakterlánc<br /><br /> Vesszővel tagolt kérelem fejléce értékeit adja vissza vagy `defaultValue` a fejléc nem található.|
|a környezet. Válasz|Szövegtörzs: IMessageBody<br /><br /> Fejlécek: IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: karakterlánc|
|karakterlánc-környezet. Response.Headers.GetValueOrDefault (headerName: karakterlánc, a DefaultValue érték: karakterlánc)|headerName: karakterlánc<br /><br /> alapértelmezett érték: karakterlánc<br /><br /> Fejléc értékek vesszővel elválasztott választ ad vissza, vagy `defaultValue` a fejléc nem található.|
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: karakterlánc<br /><br /> Kulcs: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> PrimaryKey: karakterlánc<br /><br /> Másodlagos kulcs: karakterlánc<br /><br /> Kezdődátum: DateTime?|
|context.User|E-mail: karakterlánc<br /><br /> Vezetéknév: karakterlánc<br /><br /> Csoportok: IEnumerable < IGroup\><br /><br /> ID: karakterlánc<br /><br /> Identitások: IEnumerable < IUserIdentity\><br /><br /> Vezetéknév: karakterlánc<br /><br /> Megjegyzés: karakterlánc<br /><br /> RegistrationDate: DateTime|
|IApi|ID: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Protokoll: IEnumerable < karakterlánc\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|
|IGroup|ID: karakterlánc<br /><br /> Name: karakterlánc|
|IMessageBody|T < T\>(preserveContent: logikai = false): Ahol T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> A `context.Request.Body.As<T>` és `context.Response.Body.As<T>` módszerek használhatók a kérést és választ olvasni az üzenet törzse a megadott típusú `T`. Alapértelmezés szerint a módszert használja az eredeti üzenet törzsének adatfolyam és rendereli, nem érhető el után adja vissza. A metódus egy példányát a szervezet stream alapján úgy, hogy elkerüléséhez állítsa a `preserveContent` paramétert `true`. Nyissa meg [Itt](api-management-transformation-policies.md#SetBody) példafájlt.|
|IUrl|Gazdagép: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Port: int<br /><br /> Lekérdezés: IReadOnlyDictionary < string, string [] ><br /><br /> A lekérdezési karakterlánc: karakterlánc<br /><br /> Séma: karakterlánc|
|IUserIdentity|ID: karakterlánc<br /><br /> Szolgáltató: karakterlánc|
|ISubscriptionKeyParameterNames|Fejléc: karakterlánc<br /><br /> Lekérdezés: karakterlánc|
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: karakterlánc<br /><br /> alapértelmezett érték: karakterlánc<br /><br /> Vesszővel tagolt lekérdezési paraméter értékeit adja vissza vagy `defaultValue` , ha a paraméter nem található.|
|T környezet. Variables.GetValueOrDefault < T\>(variableName: karakterlánc, a DefaultValue érték: T)|variableName: karakterlánc<br /><br /> alapértelmezett érték: T?<br /><br /> Írja be a leadott változó értékét adja vissza `T` vagy `defaultValue` Ha a változó nem található.<br /><br /> Ez a metódus kivételt jelez, ha a megadott típus nem egyezik meg a tényleges típus a visszaadott változó.|
|BasicAuthCredentials AsBasic(input: this string)|a bemeneti: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes egyszerű HTTP-hitelesítés engedélyezési kérés fejlécében értéket tartalmaz, a metódus típusú objektumot adja vissza `BasicAuthCredentials`; ellenkező esetben a metódus null értéket ad vissza.|
|logikai TryParseBasic (bemenet: a karakterlánc esetében. eredmény: BasicAuthCredentials ki)|a bemeneti: karakterlánc<br /><br /> eredmény: BasicAuthCredentials ki<br /><br /> Ha a bemeneti paraméter értéke érvényes egyszerű HTTP-hitelesítés engedélyezési a kérelem fejlécében a metódus visszaadja `true` és az eredmény paraméter típusú értéket tartalmaz `BasicAuthCredentials`; ellenkező esetben adja vissza a metódus `false`.|
|BasicAuthCredentials|Jelszó: karakterlánc<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|a bemeneti: karakterlánc<br /><br /> Ha a bemeneti paraméter érvénytelen JWT jogkivonat értéket tartalmaz, a metódus típusú objektumot adja vissza `Jwt`; ellenkező esetben adja vissza a metódus `null`.|
|logikai TryParseJwt (bemenet: a karakterlánc esetében. eredmény: ki Jwt)|a bemeneti: karakterlánc<br /><br /> eredmény: Jwt ki<br /><br /> Ha a bemeneti paraméter érvényes JWT jogkivonat értéket tartalmaz, a metódus adja vissza `true` és az eredmény paraméter típusú értéket tartalmaz `Jwt`; ellenkező esetben adja vissza a metódus `false`.|
|Jwt|Algoritmus: karakterlánc<br /><br /> Célközönség: IEnumerable < karakterlánc\><br /><br /> Jogcímek: IReadOnlyDictionary < string, string [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: karakterlánc<br /><br /> Kiállító: karakterlánc<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Tulajdonos: karakterlánc<br /><br /> Típus: karakterlánc|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: karakterlánc<br /><br /> alapértelmezett érték: karakterlánc<br /><br /> Vesszővel elválasztott értéket ad vissza a jogcímérték vagy `defaultValue` a fejléc nem található.|
|byte [] titkosítása (a bemeneti: a byte [], továbbíthatóak: karakterlánc, key: byte [], iv:byte[])|bemenet - egyszerű szöveges másolatának titkosítását<br /><br />továbbíthatóak - szimmetrikus titkosítási algoritmus neve<br /><br />kulcs - titkosítási kulcs<br /><br />IV-inicializáló vektort.<br /><br />Titkosított szövegként adja vissza.|
|byte [] titkosítása (a bemeneti: a byte [], továbbíthatóak: System.Security.Cryptography.SymmetricAlgorithm)|bemenet - egyszerű szöveges másolatának titkosítását<br /><br />továbbíthatóak - titkosítási algoritmus<br /><br />Titkosított szövegként adja vissza.|
|byte [] titkosítása (a bemeneti: a byte [], továbbíthatóak: System.Security.Cryptography.SymmetricAlgorithm, key: byte [], iv:byte[])|bemenet - egyszerű szöveges másolatának titkosítását<br /><br />továbbíthatóak - titkosítási algoritmus<br /><br />kulcs - titkosítási kulcs<br /><br />IV-inicializáló vektort.<br /><br />Titkosított szövegként adja vissza.|
|byte [] visszafejtése (bemeneti: a byte [], továbbíthatóak: karakterlánc, key: byte [], iv:byte[])|bemenet - rejtjelzésben szöveg vissza kell fejteni<br /><br />továbbíthatóak - szimmetrikus titkosítási algoritmus neve<br /><br />kulcs - titkosítási kulcs<br /><br />IV-inicializáló vektort.<br /><br />Egyszerű szövegként adja vissza.|
|byte [] visszafejtése (bemeneti: a byte [], továbbíthatóak: System.Security.Cryptography.SymmetricAlgorithm)|bemenet - rejtjelzésben szöveg vissza kell fejteni<br /><br />továbbíthatóak - titkosítási algoritmus<br /><br />Egyszerű szövegként adja vissza.|
|byte [] visszafejtése (bemeneti: a byte [], továbbíthatóak: System.Security.Cryptography.SymmetricAlgorithm, key: byte [], iv:byte[])|bemenet - rejtjelzésben szöveg vissza kell fejteni<br /><br />továbbíthatóak - titkosítási algoritmus<br /><br />kulcs - titkosítási kulcs<br /><br />IV-inicializáló vektort.<br /><br />Egyszerű szövegként adja vissza.|
|logikai VerifyNoRevocation (bemenet: a System.Security.Cryptography.X509Certificates.X509Certificate2)|Egy X.509 lánc érvényesítést végez a tanúsítvány visszavonási állapotának ellenőrzése nélkül.<br /><br />bemenet - tanúsítványobjektum<br /><br />Értéket ad vissza `true` Ha az érvényesítés sikeres; `false` sikertelen ellenőrzés.|


## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)
