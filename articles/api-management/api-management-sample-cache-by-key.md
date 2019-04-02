---
title: Egyedi gyorsítótárazás az Azure API Management szolgáltatásban
description: Ismerje meg, hogyan elemeket gyorsítótárazza a kulcs az Azure API Management által
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
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793543"
---
# <a name="custom-caching-in-azure-api-management"></a>Egyedi gyorsítótárazás az Azure API Management szolgáltatásban
Az Azure API Management szolgáltatás tartalmaz beépített támogatást nyújt a [HTTP-válaszok gyorsítótárazását](api-management-howto-cache.md) kulcsként az erőforrás URL-cím használatával. Kérelemfejlécek használatával módosíthatja a kulcsot a `vary-by` tulajdonságait. Ez akkor hasznos, teljes (más néven reprezentációinak) HTTP-válaszok gyorsítótárazáshoz, de néha hasznos csak gyorsítótár létrehozása egy részét. Az új [gyorsítótár-keresési-érték](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) és [gyorsítótár-tároló-érték](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) szabályzatok lehetővé teszi, hogy tárolására és lekérésére tetszőleges darabok belüli szabályzatdefiníciók adatokat. Ez a képesség is értéket ad hozzá a korábban bevezetett [küldési-kérelmek](/azure/api-management/api-management-advanced-policies#SendRequest) házirend mivel most gyorsítótárazhatja a külső szolgáltatásokhoz érkező válaszokat.

## <a name="architecture"></a>Architektúra
Az API Management szolgáltatás által használt egy megosztott bérlőnkénti adatok gyorsítótárazása, hogy a skála több egységig, továbbra is elérheti a azonos gyorsítótárban tárolt adatokat. Azonban ha dolgozik a több régióból álló üzemelő vannak belül a régiók mindegyike független gyorsítótárak. Fontos a gyorsítótár nem tekinti a tárolóban, ahol a szolgáltatás néhány adat, csak forrását. Ha volt, és később úgy döntött, hogy a több régióban történő üzembe helyezés előnyeit, majd, amely az utazási felhasználóval rendelkező ügyfelek előfordulhat, hogy férhet hozzá, a gyorsítótárazott adatok.

## <a name="fragment-caching"></a>Fragment gyorsítótárazás
Vannak bizonyos esetekben, ahol a visszaadott válasz tartalmazza a költséges határozza meg, és a egy ésszerű időn még friss marad adatok bizonyos része. Tegyük fel fontolja meg egy szolgáltatás, amely légitársaság, amely azzal kapcsolatos, repülési foglalásokat, repülési állapot stb. Ha a felhasználó tagja a airlines pontok program, azokat is lenne a jelenlegi állapotuk vonatkozó információkat, és a távolság halmozott. A felhasználóval kapcsolatos információkat előfordulhat, hogy tárolja, eltérő, de lehet, adja meg a flight állapotáról és foglalások visszaadott válaszokat. Ezt megteheti egy zónaaláírásnak nevezett töredék gyorsítótárazását. Az elsődleges leképezése a forráskiszolgálóról, azt, hogy ha a felhasználóval kapcsolatos információkat beszúrandó bizonyos típusú jogkivonat használatával adhatók vissza. 

Vegye figyelembe a következő JSON-választ a háttérrendszeri API.

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

És a másodlagos-erőforrás `/userprofile/{userid}` láthatóhoz hasonló

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Határozza meg a megfelelő felhasználói adatokat, az API Management kell azonosítani a végfelhasználó számára, akik. Ez a mechanizmus megvalósítási-függő. Tegyük fel, használom a `Subject` a jogcím egy `JWT` token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Az API Management tárolja a `enduserid` későbbi használatra környezeti változó értékét. A következő lépés az határozza meg, ha már rendelkezik-e a felhasználói adatokat beolvasni egy korábbi kérelemre, és a gyorsítótárban tárolt. Ehhez használja az API Management a `cache-lookup-value` házirend.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Ha nem található bejegyzés a gyorsítótár, amely a kulcs értékét, akkor nem felel meg a `userprofile` környezeti változó jön létre. Az API Management ellenőrzi a sikeres a keresési használatának a `choose` folyamat házirend szabályozza.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Ha a `userprofile` környezeti változó nem létezik, akkor az API Management lesz egy HTTP-kérés is lekérheti azt.

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

Az API Management használja a `enduserid` létrehozni a felhasználói profil erőforrás URL-CÍMÉT. Után az API Management a válasz rendelkezik, ez lekéri a tartományon kívül a válasz törzse, és vissza egy környezeti változóba menti.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Az API Management a HTTP-kérelem újra, így, ha ugyanaz a felhasználó egy másik kérést elkerüléséhez megadhatja a felhasználói profil tárolása a gyorsítótárban.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Az API Management a használatával pontos ugyanazzal a kulccsal, amely az API Management eredetileg megpróbálta beolvasni a gyorsítótárban tárolja az értéket. Az időtartam, amely az API Management úgy dönt, hogy az érték tárolása alapján hogyan gyakran a változtatások és a felhasználók hogyan hibatűrő az elavult adatokat. 

Fontos vegye figyelembe, hogy lekérése a gyorsítótárból továbbra is egy folyamaton, hálózati kérés és potenciálisan továbbra is felvehetőek tíz ezredmásodperc a kérelemre. Az előnyeit annak meghatározása, a felhasználói profil adatait, amely miatt kellene adatbázis-lekérdezések vagy több háttérrendszereket összesített adatait hosszabb ideig tart származnak.

A folyamat utolsó lépését, hogy frissítse a visszaadott válasz a felhasználói profil adatait.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Választhatja ki, az idézőjelekkel együtt a token részeként, hogy akkor is, ha nem következik be, a csere, a választ még mindig egy érvényes JSON.  

Miután együtt kombinálja ezeket a lépéseket, a végeredmény egy szabályzatot, amely az alábbihoz hasonlóan néz ki.

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

A gyorsítótár használatának elsősorban a webhelyek szolgáltatásban, HTML jön létre a kiszolgáló oldalán, hogy egyetlen lapként megjeleníthetők. Azt is lehet hasznos, ahol az ügyfelek ügyféloldali HTTP-gyorsítótárazás nem hajtható végre, vagy nem kell helyezni, amely felelős az ügyfél API-k.

Ez ugyanolyan típusú töredék gyorsítótárazás a háttér-webkiszolgálók, a Redis gyorsítótár-kiszolgáló használatával is elvégezhető, azonban az API Management szolgáltatás használatával a munka elvégzéséhez akkor hasznos, ha a gyorsítótárazott töredék érkeznek különböző háttérrendszereket, mint az elsődleges válaszok.

## <a name="transparent-versioning"></a>Transzparens verziószámozás
Általános gyakorlat egy API-t is támogatja, egyszerre több különböző megvalósítási verzió. Ha például támogatják a különböző környezeteket (fejlesztői, teszt, éles környezetben, stb.) vagy az API-t az újabb verziókra történő áttelepítéséhez az API-fogyasztókat időt biztosíthat, régebbi verzióit támogatja. 

Ügyfél fejlesztők számára, hogy módosítsa az URL-címeket, az átjárószolgáltatásnak kezelésére, ennek egyik módszere `/v1/customers` való `/v2/customers` , hogy jelenleg szeretnék használni, és a megfelelő háttérkiszolgáló URL-cím hívja az API melyik verzióját a felhasználói profil adatait tárolja. Annak megállapításához, a háttérkészlet megfelelő URL-cím egy adott ügyfél meghívásához, néhány konfigurációs adatok lekérdezéséhez szükséges. A konfigurációs adatok gyorsítótárazásával az API Management minimalizálhatja a teljesítményét, a keresés állapotát.

Az első lépéseként határozza meg a kívánt verziót konfigurálásához használt azonosító. Ebben a példában a kiválasztott a verziót, hogy a termékkulcs-előfizetés társítása. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Az API Management majd elvégzi a gyorsítótár keresési megtekintéséhez, hogy azt már beolvasni a kívánt ügyfél verziója.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Ezt követően az API Management ellenőrzi, hogy ha ezt nem találta ezt a gyorsítótárban.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Ha az API Management nem találja, az API Management kérdezi le azt.

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

A válasz törzse szöveg kinyerése a választ.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Store azt vissza a gyorsítótárban későbbi használatra.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

És végül frissíti a háttér-URL-CÍMÉT, válassza ki a kívánt az ügyfél által a szolgáltatás verzióját.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

A teljes házirend a következőképpen történik:

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

Transzparens módon szabályozhatók, mely háttérrendszer verzió frissítése és ismételt üzembe helyezése az ügyfelek nélkül az ügyfelek hozzáférnek az API-fogyasztókat engedélyezése egy elegáns megoldás, amely számos API versioning vonatkozik.

## <a name="tenant-isolation"></a>Bérlők elkülönítését
Nagyobb, több-bérlős központi telepítések egyes vállalatok hozzon létre külön csoportokat a bérlők a háttérrendszer hardver elkülönült üzemelő példányok esetében. Így minimálisra csökkenthető a háttérbeli hardverprobléma által érintett felhasználók számát. Emellett lehetővé teszi a szakaszaiban jelennek meg az új szoftververziók. Ez az architektúra háttérrendszer ideális esetben az API-fogyasztókat átlátható kell lennie. Ez lehet elérni a transzparens versioning hasonló módon alapú ugyanezzel a módszerrel, a háttérkiszolgáló URL-címet, állapotú konfigurációs API-kulcs használatával kezelésére, mert.  

Visszaadása egy előnyben részesített minden előfizetési kulcs API-verzió, helyett egy azonosítót, hogy egy bérlő vonatkozik, a hozzárendelt hardvercsoportba példában adja vissza. Adott azonosítót hozhatnak létre a megfelelő háttérkiszolgáló URL-cím használható.

## <a name="summary"></a>Összegzés
Az Azure API management-gyorsítótár végezheti a bármilyen típusú adatok tárolására szolgáló lehetővé teszi a konfigurációs adatok, amelyek hatással lehetnek a bejövő kérelem feldolgozása módon hatékony hozzáférést. Is használható, amely is kiegészítheti a válaszokat, a háttérbeli API-k által visszaadott adatok töredék tárolásához.
