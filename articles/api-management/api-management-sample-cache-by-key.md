---
title: Egyéni gyorsítótárazás az Azure API Managementben
description: Ismerje meg, hogyan lehet elemeket gyorsítótárazni kulcs szerint az Azure API Managementben
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60780821"
---
# <a name="custom-caching-in-azure-api-management"></a>Egyéni gyorsítótárazás az Azure API Managementben
Az Azure API Management szolgáltatás beépített támogatást nyújt a [HTTP-válasz gyorsítótárazáshoz,](api-management-howto-cache.md) amely az erőforrás URL-címét használja kulcsként. A kulcs a `vary-by` tulajdonságok at használó kérelemfejlécekkel módosítható. Ez akkor hasznos, ha a gyorsítótárazás teljes HTTP-válaszok (más néven ábrázolások), de néha hasznos, hogy csak cache egy részét a reprezentáció. Az új [gyorsítótár-keresés-érték](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) és [a gyorsítótár-tároló-érték](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) házirendek lehetővé teszik tetszőleges adatadatok tárolását és beolvasását a házirend-definíciókon belül. Ez a képesség is hozzáadott értéket a korábban bevezetett [küldési kérelem](/azure/api-management/api-management-advanced-policies#SendRequest) házirend, mert most már cache válaszok külső szolgáltatásokból érkező válaszokat.

## <a name="architecture"></a>Architektúra
Az API Management szolgáltatás egy megosztott bérlőnkénti adatgyorsítótárat használ, így a több egységre való skálázásnak köszönhetően továbbra is hozzáférhet ugyanazokhoz a gyorsítótárazott adatokhoz. Azonban ha egy több régiós központi telepítés használata vannak független gyorsítótárak az egyes régiókban. Fontos, hogy ne kezelje a gyorsítótárat adattárként, ahol ez az egyetlen információforrás. Ha igen, és később úgy döntött, hogy kihasználja a több régióra kiterjedő telepítést, akkor az ügyfelek a felhasználók, hogy az utazási elveszítheti a hozzáférést a gyorsítótárazott adatokhoz.

## <a name="fragment-caching"></a>Töredék gyorsítótárazása
Vannak olyan esetek, amikor a visszaküldött válaszok tartalmaznak bizonyos adatrészeket, amelyek meghatározása költséges, és mégis ésszerű ideig friss marad. Vegyük például egy légitársaság által épített szolgáltatást, amely tájékoztatást nyújt a repülőjegy-foglalásról, a járat állapotáról stb. Ha a felhasználó tagja a légitársaságok pont programjának, akkor a jelenlegi állapotukra és a felhalmozott futásteljesítményükre vonatkozó információkkal is rendelkeznek. Előfordulhat, hogy ez a felhasználóval kapcsolatos információ egy másik rendszerben tárolódik, de kívánatos lehet, hogy azokat a járat állapotáról és foglalásairól visszaküldött válaszokban is felvesse. Ezt a töredékgyorsítótárazásnak nevezett folyamattal teheti meg. Az elsődleges ábrázolás visszaküldhető az eredeti kiszolgálóról valamilyen jogkivonat használatával, amely jelzi, hogy a felhasználóval kapcsolatos információkat hol kell beszúrni. 

Fontolja meg a következő JSON-választ egy háttér-API-ból.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

És másodlagos `/userprofile/{userid}` erőforrás, hogy néz ki, mint,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

A megfelelő felhasználói adatok meghatározásához az API Managementnek azonosítania kell, hogy ki a végfelhasználó. Ez a mechanizmus a végrehajtástól függ. Például egy `Subject` `JWT` jogkivonat jogcímét használom. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Az API `enduserid` Management egy környezeti változóban tárolja az értéket későbbi használatra. A következő lépés annak megállapítása, hogy egy korábbi kérelem már lekérte-e a felhasználói adatokat, és tárolta-e azokat a gyorsítótárban. Ehhez az API Management `cache-lookup-value` a szabályzatot használja.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Ha a gyorsítótárban nincs a kulcsértéknek megfelelő bejegyzés, akkor nem `userprofile` jön létre környezeti változó. Az API Management a `choose` vezérlőfolyamat-házirend használatával ellenőrzi a vizsgálat sikeresét.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Ha `userprofile` a környezeti változó nem létezik, majd az API Management http-kérelmet kell benyújtania a beolvasásához.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

Az API `enduserid` Management a felhasználói profil erőforrásURL-címének létrehozásához használja az url-címet. Miután az API Management rendelkezik a válasz, lekéri a törzs szöveget a válaszból, és tárolja vissza egy környezeti változó.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Annak elkerülése érdekében, api management attól, hogy ezt a HTTP-kérelmet újra, amikor ugyanaz a felhasználó egy másik kérelmet, megadhatja, hogy tárolja a felhasználói profilt a gyorsítótárban.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Az API Management pontosan ugyanazzal a kulccsal tárolja az értéket a gyorsítótárban, mint amivel az API Management eredetileg megpróbálta beolvasni. Az API Management által az érték tárolásához választott időtartamnak az adatok változásának és az elavult adatokkal szembeni toleránsságának függvényében kell alapulnia. 

Fontos felismerni, hogy a gyorsítótárból való lekérés még mindig folyamaton kívüli, hálózati kérelem, és potenciálisan továbbra is tíz ezredmásodpercet adhat a kéréshez. Az előnyök akkor jelentkeznek, ha a felhasználói profil adatainak meghatározása hosszabb időt vesz igénybe, mint az adatbázis-lekérdezések vagy a több háttérrendszerből származó összesítő adatok miatt.

A folyamat utolsó lépése a visszaadott válasz frissítése a felhasználói profil adataival.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Az idézőjelek et a jogkivonat részeként is megadhatja, hogy még akkor is, ha a csere nem fordul elő, a válasz továbbra is érvényes JSON marad.  

Miután egyesíti ezeket a lépéseket együtt, a végeredmény egy olyan házirend, amely úgy néz ki, mint a következő.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Ezt a gyorsítótárazási megközelítést elsősorban olyan webhelyeken használják, ahol a HTML a kiszolgálóoldalon áll, így egyetlen oldalként jeleníthető meg. Az API-kban is hasznos lehet, ahol az ügyfelek nem tudnak ügyféloldali HTTP-gyorsítótárazást végezni, vagy kívánatos, hogy ezt a felelősséget ne az ügyfélre helyezze.

Ugyanez a fajta töredék gyorsítótárazás is elvégezhető a háttér-webkiszolgálókon egy Redis gyorsítótárazási kiszolgáló használatával, azonban az API Management szolgáltatás használata a munka elvégzéséhez akkor hasznos, ha a gyorsítótárazott töredékek különböző háttérrendszerekből származnak, mint az elsődleges Válaszok.

## <a name="transparent-versioning"></a>Átlátszó verziószámozás
Bevett gyakorlat, hogy egy API több különböző implementációs verziója egyszerre támogatott. Például a különböző környezetek (fejlesztés, tesztelés, éles környezet, stb), vagy az API-verziók támogatása, hogy időt adjon az API-fogyasztók áttérni az újabb verziók. 

Ennek kezelésének egyik megközelítése, ahelyett, hogy az ügyfélfejlesztőknek az URL-címek ről az url-címekre kellene `/v1/customers` módosítaniuk, `/v2/customers` hogy a fogyasztó profiladataiban tárolják az API melyik verzióját kívánják használni, és megkell hívniuk a megfelelő háttérURL-t. A megfelelő háttér-URL-cím meghatározásához egy adott ügyfél számára, le kell kérdezni néhány konfigurációs adatot. A konfigurációs adatok gyorsítótárazásával az API Management minimálisra csökkentheti a vizsgálat teljesítményének büntetését.

Az első lépés a kívánt verzió konfigurálásához használt azonosító meghatározása. Ebben a példában úgy döntöttem, hogy a verziót társítom a termék-előfizetési kulcshoz. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Az API Management ezután gyorsítótár-kereséssel ellenőrzi, hogy már lekérte-e a kívánt ügyfélverziót.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Ezután az API Management ellenőrzi, hogy nem találta-e meg a gyorsítótárban.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Ha az API Management nem találta meg, az API Management lekéri azt.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

A választörzs szövegének kibontása a válaszból.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Tárolja vissza a gyorsítótárba későbbi használatra.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

És végül frissítse a háttér-URL-t, hogy válassza ki a szolgáltatás kívánt verzióját az ügyfél.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

A teljes szabályzat a következő:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Lehetővé teszi az API-felhasználók számára, hogy átlátható módon szabályozsák, hogy az ügyfelek mely háttérverzióhoz férnek hozzá anélkül, hogy frissíteniük kellene és újra kellene telepíteniük az ügyfeleket, elegáns megoldás, amely számos API-verziószámozási problémát kezel.

## <a name="tenant-isolation"></a>Bérlő elkülönítése
Nagyobb, több-bérlős telepítések egyes vállalatok külön bérlőcsoportokat hoznak létre a háttérhardverek különböző központi telepítésein. Ez minimálisra csökkenti azoknak az ügyfeleknek a számát, akiket a háttérrendszer hardverproblémája érint. Azt is lehetővé teszi, hogy az új szoftververziók at gördült ki szakaszokban. Ideális esetben ez a háttérarchitektúra átláthatónak kell lennie az API-fogyasztók számára. Ez az átlátszó verziószámozáshoz hasonlóan érhető el, mivel a háttérbeli URL-cím API-kulcsonkénti konfigurációs állapothasználatával történő kezelésére szolgáló ugyanazon a technikán alapul.  

Ahelyett, hogy minden egyes előfizetési kulcshoz visszaadná az API előnyben részesített verzióját, egy olyan azonosítót adna vissza, amely a bérlőt a hozzárendelt hardvercsoporthoz kapcsolja. Ez az azonosító a megfelelő háttér-URL-cím létrehozásához használható.

## <a name="summary"></a>Összefoglalás
Az Azure API felügyeleti gyorsítótárának bármilyen típusú adat tárolása lehetővé teszi a konfigurációs adatok hatékony elérését, amelyek befolyásolhatják a bejövő kérelmek feldolgozásának módját. A háttér-API-ból visszaadott, a válaszokat bővítő adattöredékek tárolására is használható.
