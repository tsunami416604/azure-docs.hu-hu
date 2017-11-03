---
title: "Egyéni gyorsítótárazása az Azure API Management"
description: "Megtudhatja, hogyan gyorsítótárazza a cikkek gombot az Azure API Management"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 4a41e4e0be44e855ead253ad76fe5a3af52070ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="custom-caching-in-azure-api-management"></a>Egyéni gyorsítótárazása az Azure API Management
Az Azure API Management szolgáltatás rendelkezik beépített támogatása [HTTP-válasz gyorsítótár](api-management-howto-cache.md) kulcsa a forrás URL-cím használatával. A kulcs használatával kérelemfejléc módosíthatják a `vary-by` tulajdonságok. Ez akkor hasznos, a teljes HTTP-válaszok (más néven felelősséget) gyorsítótárazáshoz, de egyes esetekben célszerű csak gyorsítótár létrehozása egy részét. Az új [gyorsítótár-keresési-érték](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) és [gyorsítótár-tároló-érték](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) -szabályzatok lehetőséget biztosítanak tárolásához és lekéréséhez belül a házirend-definíciók adatait tetszőleges darabjait képes. Ez a lehetőség is értéket ad hozzá a korábban bevezetett [küldési-kérelmek](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) házirend mivel most gyorsítótárazhatja a válaszok külső szolgáltatásokból.

## <a name="architecture"></a>Architektúra
API-kezelés szolgáltatást használ egy megosztott / bérlői adatgyorsítótár, hogy az, akár méretezhető, továbbra is elérhetővé válik a ugyanaz a hozzáférést, a több egység gyorsítótárazott adatokat. Azonban több területi telepítés használatakor vannak független gyorsítótárak belül régióban. Emiatt fontos a gyorsítótár nem tekinti a tárolóban, néhány adat, csak forrását. Ha volt, és később úgy döntött, hogy a több területi telepítési előnyeit, majd haladnak, akik rendelkező ügyfelek is elveszti hozzáférését, hogy a gyorsítótárazott adatokat.

## <a name="fragment-caching"></a>Töredék gyorsítótárazása
Nincsenek bizonyos esetekben, ha a válaszokat ad vissza, olcsóbbá teszi az határozza meg, és még marad friss elfogadható időn adatok egy részét tartalmazza. Tegyük fel fontolja meg egy szolgáltatás, amely repülési foglalásokat, repülési állapot stb vonatkozó információkat biztosító légitársaság. Ha a felhasználó tagja a légitársaság pontok program, akkor az aktuális állapot és halmozott távolság vonatkozó információkat is. Eltérő tárolódhat, a felhasználóval kapcsolatos adatokat, de lehet foglalja azt repülési állapotáról és foglalások adott vissza. Ezt megteheti egy zónaaláírásnak nevezett töredék gyorsítótárazását. Az elsődleges ábrázolását adhatók vissza a forráskiszolgálóról, és jelzi, ahol a felhasználóval kapcsolatos adatokat beszúrni valamilyen token használatával. 

Vegye figyelembe a következő JSON-válasz egy API-háttérrendszerből.

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

És a másodlagos helyen erőforrás `/userprofile/{userid}` láthatóhoz hasonló,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Annak meghatározására, a megfelelő felhasználói adatok közé tartoznak, igazolnia kell a felhasználó, aki azonosításához. Ez a módszer használata függő végrehajtása. Tegyük fel, használom a `Subject` a jogcím egy `JWT` token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Ez tároljuk `enduserid` későbbi használatra környezeti változó értékét. A következő lépés annak határozzák meg, ha már rendelkezik-e a felhasználói adatok lekérése a korábbi kérelmekre, és a gyorsítótárban tárolt. A használjuk a `cache-lookup-value` házirend.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Ha nem található bejegyzés a gyorsítótár, a kulcs értékét, akkor a nem megfelelő a `userprofile` környezeti változó jön létre. A sikeres a keresési használatának ellenőrizzük a `choose` adatfolyam házirend szabályozza.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Ha a `userprofile` környezeti változó nem létezik, akkor el kell végeznie egy HTTP-kérelem lekéréséhez fogjuk.

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

Használjuk a `enduserid` összeállítani a felhasználói profil erőforrás URL-CÍMÉT. Amennyiben van a válasz, azt lekéréses kívül a válasz szövegét, és újra üzembe a környezeti változó tárolja.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Velünk, hogy végezze el a HTTP-kérelem újra, ha ugyanaz a felhasználó egy másik kérést kellene elkerüléséhez tároljuk a gyorsítótárban a felhasználói profilt.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Az érték, amely azt eredetileg megpróbálta beolvasni a pontos azonos kulcsot használva gyorsítótárában tároljuk. Az érték tárolására választjuk időtartam alapján hogyan gyakran a változtatások és a felhasználók hogyan hibatűrő történik az elavult adatokat. 

Fontos, hogy a gyorsítótár lekérdezése még mindig egy folyamaton kívül, a hálózati kérelmek, és potenciálisan továbbra is felvehetőek több tíz ideje (MS) kérésre megvalósításához. A következő előnyöket határozza meg a felhasználói profillal kapcsolatos információk, amelyek miatt az adatbázis-lekérdezések vagy több biztonsági-végpontok összesített adatait kellene számottevően hosszabb időbe telik meghatározásakor.

A folyamat az utolsó lépés a felhasználói profil információkkal frissítenie a visszaadott válaszban.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Az idézőjelek a jogkivonat részeként, hogy akkor is, ha nem történik, a csere, a válasz volt-e továbbra is érvényes JSON elfogadása. Ez elsősorban hibakeresési könnyebb annak volt.

Miután együtt egyesíteni ezeket a lépéseket, záró eredménye egy házirendet a következőhöz hasonló a következő egy.

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

        <!-- If we don’t find it in the cache, make a request for it and store it -->
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

Ez a megközelítés gyorsítótárazási elsősorban a webhelyeken ahol HTML jön létre a kiszolgáló oldalán, hogy egyetlen lapként megjeleníthetők. Azonban ez is hasznos lehet az API-k, ahol az ügyfelek nem ügyfél oldalán található HTTP-gyorsítótárazás, vagy nem kell elhelyezni, amely felelős az ügyfélen.

A gyorsítótárazás töredék ugyanolyan típusú is elvégezhető a háttér-webkiszolgálók, a Redis gyorsítótár-kiszolgáló használatával, azonban a munkájuk elvégzéséhez az API Management szolgáltatással akkor hasznos, ha a gyorsítótárazott töredék érkező különböző biztonsági ends mint az elsődleges válaszokat.

## <a name="transparent-versioning"></a>Transzparens versioning
Általános gyakorlat egy API-t, így egyszerre nem támogatott több különböző végrehajtási verziója. Ez lehet, hogy az fejlesztői, tesztelési, éles stb, a különböző környezetek támogatására, vagy lehet támogatására az API-t, hogy API fogyasztók újabb verziókra történő áttelepítéséhez a régebbi verzióit. 

Ahelyett, hogy az ügyfél fejlesztők számára, hogy módosítsa az URL-címei kezelnek ez egyik módszer `/v1/customers` való `/v2/customers` jelenleg kívánnak használni, és hívja meg a megfelelő háttérkiszolgáló URL-címet a API verziójának a felhasználói profil adatok tárolásához. Annak meghatározására, a megfelelő háttér URL-címet az adott ügyfél hívja, fontos bizonyos konfigurációs adatait. A konfigurációs adatok gyorsítótárazása, azt is lecsökkentheti a teljesítményét, ez a keresés során.

Az első lépés, hogy a kívánt verziójával konfigurálásához használt azonosító meghatározása. Ebben a példában a verziót, hogy a termékkulcs előfizetés társítása elfogadása. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Majd végezzük a gyorsítótárban való kereséshez megjelenítéséhez, ha azt már rendelkezik olvassa be a kívánt ügyfél verziója.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Jelenleg így ellenőrizheti, ha azt nem találta azt a gyorsítótárban.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Ha azt nem, akkor nyissa meg azt, és lekéréséhez.

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

A válasz törzsében szöveg kinyerése a választ.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Vissza tárolása a későbbi használatra a gyorsítótárban.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

És végül frissítse a háttér-URL-címet, válassza ki a szolgáltatást, az ügyfél által igényelt verziót.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

A teljesen házirendet a következőképpen történik.

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If we don’t find it in the cache, make a request for it and store it -->
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

Sok API versioning problémákat orvosló elegáns megoldás API fogyasztói transzparens módon a háttérrendszer verziószámának ügyfelek anélkül, hogy frissítse és telepítse újra az ügyfelek is hozzáférnek vezérlésére.

## <a name="tenant-isolation"></a>Bérlők elszigetelésére
A nagyobb, a több-bérlős telepítésekben egyes vállalatok bérlők külön csoportot létre háttér hardver különböző üzemelő példányok esetében. Ez minimalizálja a háttérkiszolgálón a hardver probléma által érintett felhasználók száma. Emellett lehetővé teszi az új szoftververziók való szakaszban állítható. Ideális esetben a háttér-architektúra API-felhasználók számára átlátható legyen. Ez érhető el a transzparens versioning hasonló módon, mert a háttérkiszolgáló URL-CÍMÉT egy API-kulcs konfiguráció állapota kezelésére ugyanaz a technika alapul.  

Helyett az API a minden előfizetés kulcs egy előnyben részesített verziója, az azonosítója, hogy a bérlő vonatkozik, a hozzárendelt hardvercsoport alakítanák vissza. Ilyen azonosítójú segítségével hozható létre a megfelelő háttérkiszolgáló URL-CÍMÉT.

## <a name="summary"></a>Összefoglalás
Szabadon használható az Azure API management gyorsítótár bármilyen típusú adat tárolásához lehetővé teszi, hogy a konfigurációs adatokat, amelyek hatással lehetnek a módszer egy bejövő kérelem feldolgozását hatékonyabbá teszi a hozzáférést. Is használható, amelyek is kiegészítheti a válaszokat, egy háttér-API által visszaadott adatok töredék tárolásához.

## <a name="next-steps"></a>Következő lépések
Adja meg a visszajelzést a disqus-beszélgetésben teheti szál a témakör Ha egyéb forgatókönyvek, ezek a házirendek, amelyeken engedélyezve van, vagy ha vannak forgatókönyvek szeretné-e elérése, de nem látja a jelenleg lehetségesek.

