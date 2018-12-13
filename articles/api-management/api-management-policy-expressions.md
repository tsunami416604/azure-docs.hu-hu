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
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 8cfaacad4619bb06536d41e72b9da1eb9c160dc2
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163905"
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
  
|CLR-beli típus|Támogatott tagok|
|--------------|-----------------------|
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
|System.Collections.Generic.IReadOnlyCollection<T\>|Összes|
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Összes|
|System.Collections.Generic.ISet<TKey, TValue>|Összes|
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Kulcs-érték|
|System.Collections.Generic.List<TKey, TValue>|Összes|
|System.Collections.Generic.Queue<TKey, TValue>|Összes|
|System.Collections.Generic.Stack<TKey, TValue>|Összes|
|System.Convert|Összes|
|System.DateTime|Összes|
|System.DateTimeKind|(UTC)|
|System.DateTimeOffset|Összes|
|System.Decimal|Összes|
|System.Double|Összes|
|System.Guid|Összes|
|System.IEnumerable < T\>|Összes|
|System.IEnumerator<T\>|Összes|
|System.Int16|Összes|
|System.Int32|Összes|
|System.Int64|Összes|
|System.Linq.Enumerable<T\>|Összes|
|System.Math|Összes|
|System.MidpointRounding|Összes|
|System.Net.WebUtility|Összes|
|System.Nullable<T\>|Összes|
|System.Random|Összes|
|System.SByte|Összes|
|System.Security.Cryptography. HMACSHA384|Összes|
|System.Security.Cryptography. HMACSHA512|Összes|
|System.Security.Cryptography.HashAlgorithm|Összes|
|System.Security.Cryptography.HMAC|Összes|
|System.Security.Cryptography.HMACMD5|Összes|
|System.Security.Cryptography.HMACSHA1|Összes|
|System.Security.Cryptography.HMACSHA256|Összes|
|System.Security.Cryptography.KeyedHashAlgorithm|Összes|
|System.Security.Cryptography.MD5|Összes|
|System.Security.Cryptography.RNGCryptoServiceProvider|Összes|
|System.Security.Cryptography.SHA1|Összes|
|System.Security.Cryptography.SHA1Managed|Összes|
|System.Security.Cryptography.SHA256|Összes|
|System.Security.Cryptography.SHA256Managed|Összes|
|System.Security.Cryptography.SHA384|Összes|
|System.Security.Cryptography.SHA384Managed|Összes|
|System.Security.Cryptography.SHA512|Összes|
|System.Security.Cryptography.SHA512Managed|Összes|
|System.Single|Összes|
|System.String|Összes|
|System.StringSplitOptions|Összes|
|System.Text.Encoding|Összes|
|System.Text.RegularExpressions.Capture|Index, hosszúságú érték|
|System.Text.RegularExpressions.CaptureCollection|Szám, cikk|
|System.Text.RegularExpressions.Group|Rögzíti, sikeres|
|System.Text.RegularExpressions.GroupCollection|Szám, cikk|
|System.Text.RegularExpressions.Match|Üres, csoportok, eredménye|
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, egyezik, megegyezik, cserélje le a következőket:|
|System.Text.RegularExpressions.RegexOptions|Lefordított IgnoreCase, IgnorePatternWhitespace, többsoros, None, RightToLeft, Singleline|
|System.TimeSpan|Összes|
|System.Tuple|Összes|
|System.UInt16|Összes|
|System.UInt32|Összes|
|System.UInt64|Összes|
|System.Uri|Összes|
|System.Xml.Linq.Extensions|Összes|
|System.Xml.Linq.XAttribute|Összes|
|System.Xml.Linq.XCData|Összes|
|System.Xml.Linq.XComment|Összes|
|System.Xml.Linq.XContainer|Összes|
|System.Xml.Linq.XDeclaration|Összes|
|System.Xml.Linq.XDocument|Összes|
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
  
## <a name="ContextVariables"></a> Környezeti változó
Nevű változó `context` implicit módon érhető el az összes szabályzatban [kifejezés](api-management-policy-expressions.md#Syntax). A tagjai a profiljával kapcsolatos információkat biztosít a `\request`. Összes a `context` tagjai csak olvasható.
  
|Környezeti változó|Engedélyezett metódusok, tulajdonságok és értékei|
|----------------------|-------------------------------------------------------|
|Környezet|API: IApi<br /><br /> Környezet<br /><br /> Eltelt idő: Időtartomány - időbélyeg értékét, a pontos idő közötti időintervallum<br /><br /> LastError<br /><br /> Művelet<br /><br /> Product<br /><br /> Kérés<br /><br /> Kérelemazonosító: GUID - kérelem egyedi azonosítója<br /><br /> Válasz<br /><br /> Előfizetés<br /><br /> Időbélyeg: Dátum és idő – időpont, amikor a kérelem érkezett<br /><br /> Nyomkövetés: logikai - azt jelzi, hogy nyomkövetés be- vagy kikapcsolása <br /><br /> Felhasználó<br /><br /> Változók: IReadOnlyDictionary < karakterlánc, objektum ><br /><br /> typ void Trace(message: string)|
|context.Api|ID: karakterlánc<br /><br /> IsCurrentRevision: logikai<br /><br />  Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Verzió: karakterlánc<br /><br /> ServiceUrl: IUrl<br /><br /> Verzió: karakterlánc |
|a környezet. Üzembe helyezés|Régió: karakterlánc<br /><br /> Szolgáltatásnév: karakterlánc<br /><br /> Tanúsítványok: IReadOnlyDictionary < karakterlánc, X509Certificate2 >|
|context.LastError|Forrás: karakterlánc<br /><br /> OK: karakterlánc<br /><br /> Üzenet: karakterlánc<br /><br /> Hatókör: karakterlánc<br /><br /> A szakasz: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> PolicyId: karakterlánc<br /><br /> További információ a környezetben. LastError, lásd: [hibakezelés](api-management-error-handling-policies.md).|
|a környezet. A művelet|ID: karakterlánc<br /><br /> Módszer: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> UrlTemplate: karakterlánc|
|a környezet. A termék|API-kat: IEnumerable < IApi\><br /><br /> ApprovalRequired: logikai<br /><br /> Csoportok: IEnumerable < IGroup\><br /><br /> ID: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Állapot: enum ProductState {NotPublished, közzétett}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: logikai|
|a környezet. Kérelem|Szövegtörzs: IMessageBody<br /><br /> Tanúsítvány: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Fejlécek: IReadOnlyDictionary < string, string [] ><br /><br /> IP-cím: karakterlánc<br /><br /> MatchedParameters: IReadOnlyDictionary < karakterlánc, karakterlánc ><br /><br /> Módszer: karakterlánc<br /><br /> OriginalUrl:IUrl<br /><br /> URL-cím: IUrl|
|karakterlánc-környezet. Request.Headers.GetValueOrDefault (headerName: karakterlánc, a DefaultValue érték: karakterlánc)|headerName: karakterlánc<br /><br /> alapértelmezett érték: karakterlánc<br /><br /> Vesszővel tagolt kérelem fejléce értékeit adja vissza vagy `defaultValue` a fejléc nem található.|
|a környezet. Válasz|Szövegtörzs: IMessageBody<br /><br /> Fejlécek: IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: karakterlánc|
|karakterlánc-környezet. Response.Headers.GetValueOrDefault (headerName: karakterlánc, a DefaultValue érték: karakterlánc)|headerName: karakterlánc<br /><br /> alapértelmezett érték: karakterlánc<br /><br /> Fejléc értékek vesszővel elválasztott választ ad vissza, vagy `defaultValue` a fejléc nem található.|
|context.Subscription|CreatedTime: DateTime<br /><br /> Záródátum: Dátum és idő?<br /><br /> ID: karakterlánc<br /><br /> Kulcs: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> PrimaryKey: karakterlánc<br /><br /> Másodlagos kulcs: karakterlánc<br /><br /> Kezdődátum: Dátum és idő?|
|context.User|E-mail: karakterlánc<br /><br /> Vezetéknév: karakterlánc<br /><br /> Csoportok: IEnumerable < IGroup\><br /><br /> ID: karakterlánc<br /><br /> Identitások: IEnumerable < IUserIdentity\><br /><br /> Vezetéknév: karakterlánc<br /><br /> Megjegyzés: karakterlánc<br /><br /> RegistrationDate: DateTime|
|IApi|ID: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Protokoll: IEnumerable < karakterlánc\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|
|IGroup|ID: karakterlánc<br /><br /> Name: karakterlánc|
|IMessageBody|T < T\>(preserveContent: logikai = false): Ahol T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> A `context.Request.Body.As<T>` és `context.Response.Body.As<T>` módszerek használhatók a kérést és választ olvasni az üzenet törzse a megadott típusú `T`. Alapértelmezés szerint a módszert használja az eredeti üzenet törzsének adatfolyam és rendereli, nem érhető el után adja vissza. A metódus egy példányát a szervezet stream alapján úgy, hogy elkerüléséhez állítsa a `preserveContent` paramétert `true`. Nyissa meg [Itt](api-management-transformation-policies.md#SetBody) példafájlt.|
|IUrl|Gazdagép: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Port: int<br /><br /> Lekérdezés: IReadOnlyDictionary < string, string [] ><br /><br /> A lekérdezési karakterlánc: karakterlánc<br /><br /> Séma: karakterlánc|
|IUserIdentity|ID: karakterlánc<br /><br /> Szolgáltató: karakterlánc|
|ISubscriptionKeyParameterNames|Fejléc: karakterlánc<br /><br /> Lekérdezés: karakterlánc|
|karakterlánc-IUrl.Query.GetValueOrDefault (queryParameterName: karakterlánc, a DefaultValue érték: karakterlánc)|queryParameterName: karakterlánc<br /><br /> alapértelmezett érték: karakterlánc<br /><br /> Vesszővel tagolt lekérdezési paraméter értékeit adja vissza vagy `defaultValue` , ha a paraméter nem található.|
|T környezet. Variables.GetValueOrDefault < T\>(variableName: karakterlánc, a DefaultValue érték: T)|variableName: karakterlánc<br /><br /> alapértelmezett érték: T<br /><br /> Írja be a leadott változó értékét adja vissza `T` vagy `defaultValue` Ha a változó nem található.<br /><br /> Ez a metódus kivételt jelez, ha a megadott típus nem egyezik meg a tényleges típus a visszaadott változó.|
|BasicAuthCredentials AsBasic(input: this string)|a bemeneti: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes egyszerű HTTP-hitelesítés engedélyezési kérés fejlécében értéket tartalmaz, a metódus típusú objektumot adja vissza `BasicAuthCredentials`; ellenkező esetben a metódus null értéket ad vissza.|
|logikai TryParseBasic (bemenet: a karakterlánc esetében. eredmény: BasicAuthCredentials ki)|a bemeneti: karakterlánc<br /><br /> eredmény: BasicAuthCredentials ki<br /><br /> Ha a bemeneti paraméter értéke érvényes egyszerű HTTP-hitelesítés engedélyezési a kérelem fejlécében a metódus visszaadja `true` és az eredmény paraméter típusú értéket tartalmaz `BasicAuthCredentials`; ellenkező esetben adja vissza a metódus `false`.|
|BasicAuthCredentials|Jelszó: karakterlánc<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|a bemeneti: karakterlánc<br /><br /> Ha a bemeneti paraméter érvénytelen JWT jogkivonat értéket tartalmaz, a metódus típusú objektumot adja vissza `Jwt`; ellenkező esetben adja vissza a metódus `null`.|
|logikai TryParseJwt (bemenet: a karakterlánc esetében. eredmény: ki Jwt)|a bemeneti: karakterlánc<br /><br /> eredmény: Jwt ki<br /><br /> Ha a bemeneti paraméter érvényes JWT jogkivonat értéket tartalmaz, a metódus adja vissza `true` és az eredmény paraméter típusú értéket tartalmaz `Jwt`; ellenkező esetben adja vissza a metódus `false`.|
|Jwt|Algoritmus: karakterlánc<br /><br /> Célközönség: IEnumerable < karakterlánc\><br /><br /> Jogcímek: IReadOnlyDictionary < string, string [] ><br /><br /> ExpirationTime: Dátum és idő?<br /><br /> ID: karakterlánc<br /><br /> Kiállító: karakterlánc<br /><br /> NotBefore: Dátum és idő?<br /><br /> Tulajdonos: karakterlánc<br /><br /> Típus: karakterlánc|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: karakterlánc<br /><br /> alapértelmezett érték: karakterlánc<br /><br /> Vesszővel elválasztott értéket ad vissza a jogcímérték vagy `defaultValue` a fejléc nem található.|
|byte [] titkosítása (a bemeneti: a byte [], továbbíthatóak: karakterlánc, key: byte [], iv:byte[])|bemenet - egyszerű szöveges másolatának titkosítását<br /><br />továbbíthatóak - szimmetrikus titkosítási algoritmus neve<br /><br />kulcs - titkosítási kulcs<br /><br />IV-inicializáló vektort.<br /><br />Titkosított szövegként adja vissza.|
|byte [] titkosítása (a bemeneti: a byte [], továbbíthatóak: System.Security.Cryptography.SymmetricAlgorithm)|bemenet - egyszerű szöveges másolatának titkosítását<br /><br />továbbíthatóak - titkosítási algoritmus<br /><br />Titkosított szövegként adja vissza.|
|byte [] titkosítása (a bemeneti: a byte [], továbbíthatóak: System.Security.Cryptography.SymmetricAlgorithm, key: byte [], iv:byte[])|bemenet - egyszerű szöveges másolatának titkosítását<br /><br />továbbíthatóak - titkosítási algoritmus<br /><br />kulcs - titkosítási kulcs<br /><br />IV-inicializáló vektort.<br /><br />Titkosított szövegként adja vissza.|
|byte [] visszafejtése (bemeneti: a byte [], továbbíthatóak: karakterlánc, key: byte [], iv:byte[])|bemenet - rejtjelzésben szöveg vissza kell fejteni<br /><br />továbbíthatóak - szimmetrikus titkosítási algoritmus neve<br /><br />kulcs - titkosítási kulcs<br /><br />IV-inicializáló vektort.<br /><br />Egyszerű szövegként adja vissza.|
|byte [] visszafejtése (bemeneti: a byte [], továbbíthatóak: System.Security.Cryptography.SymmetricAlgorithm)|bemenet - rejtjelzésben szöveg vissza kell fejteni<br /><br />továbbíthatóak - titkosítási algoritmus<br /><br />Egyszerű szövegként adja vissza.|
|byte [] visszafejtése (bemeneti: a byte [], továbbíthatóak: System.Security.Cryptography.SymmetricAlgorithm, key: byte [], iv:byte[])|– a bemeneti - rejtjelzésben bemeneti szöveg vissza kell fejteni<br /><br />továbbíthatóak - titkosítási algoritmus<br /><br />kulcs - titkosítási kulcs<br /><br />IV-inicializáló vektort.<br /><br />Egyszerű szövegként adja vissza.|


## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)   
