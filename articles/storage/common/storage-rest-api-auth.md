---
title: Azure Storage szolgáltatások REST API-műveleteket, köztük a hitelesítési hívás |} A Microsoft Docs
description: Azure Storage szolgáltatások REST API-műveleteket, köztük a hitelesítési hívás
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 647d40db87f76a9e1a13a108c5f55fac40524017
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012790"
---
# <a name="using-the-azure-storage-rest-api"></a>Az Azure Storage REST API használata

Ez a cikk bemutatja a Blob Storage szolgáltatás REST API-k és a szolgáltatás a hívás hitelesítéséhez. Írás a személy, aki semmit sem tud REST és nem tudja biztosítása a REST-hívást a szempontból, de a fejlesztő. Hogy tekintse meg a REST-hívást dokumentációja és útmutató a tényleges REST-hívást – mezőszöveg fordítása, mely mezők nyissa meg a helyét? Miután részletesebben beállítása a REST-hívást, használhatja ezt a tudást a többi tárolási szolgáltatás REST API-k használatához.

## <a name="prerequisites"></a>Előfeltételek 

Az alkalmazás egy storage-fiók blob Storage-tárolók listája. Próbálja ki az ebben a cikkben kód, a következőkre van szükség: 

* Telepítés [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) a következő számítási feladatokkal:
    - Azure-fejlesztés

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Egy általános célú tárfiókot. Ha még nem rendelkezik egy tárfiókot, [hozzon létre egy tárfiókot](storage-quickstart-create-account.md).

* Ebben a cikkben a példa bemutatja, hogyan listázhatja a tárolókat a storage-fiókban. Kimenet megtekintéséhez adja hozzá az egyes tárolókat a storage-fiókban lévő blobtárolóba, mielőtt elkezdené.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A mintaalkalmazás a C# nyelven írt konzolalkalmazással.

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Visual Studio-megoldás megnyitásához keresse meg a storage-dotnet-rest-api-with-auth mappát, nyissa meg és kattintson duplán a StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>Mit jelent a többi?

REST azt jelenti, hogy *representational állapotátvitel*. Egy adott definíciót, tekintse meg [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

Alapvetően, a REST egy olyan architektúra, mikor használhat API-k hívása, vagy API-kat kell meghívni, így. Azt, hogy mi történik, sem független, és milyen egyéb szoftvert használja küldésekor vagy fogadásakor a REST-hívások. Egy olyan alkalmazást, amely egy Mac, Windows, Linux, az Android rendszerű telefon vagy táblagép, iPhone, iPod vagy webhely futtat, és az azonos REST API-t használja az összes platformokhoz. Adatok is át lehet adni a ki és/vagy a REST API meghívásakor. A REST API-t nem gondoskodik a milyen platformról nevezzük – a fontos, az adatokat a kérésben kapott, és a válaszban megadott adatok.

Egy hasznos ismeretek, hogy hogyan használható a REST. Az Azure-termékcsoport csapatának rendszeresen kiadja az új funkciókat. Sok esetben az új funkciók érhetők el, a REST-felületen keresztül, de rendelkezik nem még lett végzetesnek **összes** a storage ügyfélkódtárai vagy a felhasználói felületen (például az Azure Portalon). Ha mindig szeretne a legújabb és legjobb technológiát használja, akkor REST tanulás szükséges. Ha szeretne írni a saját könyvtár használatával kommunikálhat az Azure Storage, vagy szeretné elérni az Azure Storage-programozási nyelv, amely nem rendelkezik az SDK-t vagy a storage ügyféloldali kódtár, használhatja a REST API-t.

## <a name="about-the-sample-application"></a>Tudnivalók a mintaalkalmazás

A mintaalkalmazás a tárfiókban lévő tárolók listája. Miután megismerte, hogyan utal. az adatokat a REST API-dokumentáció a kódot, a más REST-hívások, könnyebben felderíthesse. 

Ha megtekinti a [Blob Service REST API](/rest/api/storageservices/Blob-Service-REST-API), tekintse meg a blob Storage-hajthat végre műveleteket. A storage ügyféloldali függvénytárak is burkolókat körül a REST API-k – megkönnyítik az Ön számára tároló elérése érdekében anélkül, hogy közvetlenül a REST API-k használatával. De a fentieknek megfelelően néha használni kívánt REST API a storage ügyféloldali kódtár helyett.

## <a name="rest-api-reference-list-containers-api"></a>REST API-leírások: API-tárolót

Nézzük meg a lapot a REST API-referencia a [ListContainers](/rest/api/storageservices/List-Containers2) így ismernie az egyes mezőit forrását a kérés- és a kód a következő szakaszban a művelet.

**Kérelem metódusa**: GET. Ez a művelet a HTTP-metódus, mindenképp adja meg a kérelem objektum olyan osztályát. Ehhez a művelethez tartozó többi értéket tartalmazza, HEAD, PUT és DELETE, attól függően, az API-t hívja meg.

**Kérés URI-ja**: https://myaccount.blob.core.windows.net/?comp=list  Ez a blob storage-végpont jön `http://myaccount.blob.core.windows.net` és az erőforrás-karakterlánc `/?comp=list`.

[URI-paramétereinek](/rest/api/storageservices/List-Containers2#uri-parameters): Nincsenek további lekérdezési paraméterek ListContainers hívásakor használható. Ezeket a paramétereket néhány vannak *időtúllépési* a hívás (másodpercben), és *előtag*, szűréshez használt.

Egy másik hasznos paraméter *maxresults:* több tároló ennél az értéknél érhetők el, ha a válasz törzse fogja tartalmazni a *NextMarker* elem, amely azt jelzi, hogy a következő vissza a következő tároló a kérést. Ez a funkció használatához adja meg a *NextMarker* érték a *jelölő* paraméter a következő kérelmet győződjön meg arról, ha az URI-ban. Ha ezzel a funkcióval, az eredmények átlapozva hasonló. 

További paraméterek használatához fűzzön hozzá őket az érték, például ebben a példában az erőforrás-karakterlánc:

```
/?comp=list&timeout=60&maxresults=100
```

[Kérelem fejlécei](/rest/api/storageservices/List-Containers2#request-headers)**:** Ez a szakasz felsorolja a szükséges és választható kérelemfejlécek. A fejlécek három szükség: egy *engedélyezési* fejléc *x-ms-date* (tartalmazza a kérelem az Egyezményes világidő), és *x-ms-version* (határozza meg a többi verzióját API-t használja). Többek között *x-ms-client-request-id* a fejlécek nem kötelező – és bármit Ez a mező értéke megadható; írás a storage analytics naplók Ha naplózása engedélyezve van.

[Kérelem törzse](/rest/api/storageservices/List-Containers2#request-body)**:** Nincs nem ListContainers kérelemtörzs. Kérelem törzse az összes PUT művelet szolgál, blobok, valamint SetContainerAccessPolicy, amely lehetővé teszi, hogy a alkalmazni a tárolt hozzáférési szabályzatok egy XML-lista küldése feltöltésekor. A cikkben említett tárolt hozzáférési szabályzatok [használata közös hozzáférésű Jogosultságkódok (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Válasz állapotkódja](/rest/api/storageservices/List-Containers2#status-code)**:** Arra utasítja a bármely állapotkódok, ismernie kell. Ebben a példában egy HTTP-állapotkód: 200-as rendben. HTTP-állapotkódok teljes listáját, tekintse meg [állapotkód-definíciókat](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). A Storage REST API-k az adott hibakódok, olvassa el [REST API-val gyakori hibakódok](/rest/api/storageservices/common-rest-api-error-codes)

[Válaszfejlécek](/rest/api/storageservices/List-Containers2#response-headers)**:** Ezek közé tartozik a *tartalomtípus*; *x-ms-request-id* (a kérés azonosítója, átadott, ha van ilyen); *x-ms-version* (azt jelzi, hogy a használt Blob szolgáltatás), és a *dátum* (UTC-t, arra utasítja a kérés érkezett eldöntve).

[Választörzs](/rest/api/storageservices/List-Containers2#response-body): Ezt a mezőt egy XML-struktúra a kért adatok megadása kötelező. Ebben a példában a válasz az tárolók és a hozzájuk tartozó tulajdonságok listája.

## <a name="creating-the-rest-request"></a>A REST-kérés létrehozása

Pár megjegyzéseket előtt – biztonság, amikor az éles környezetben futó, mindig használjon HTTP helyett HTTPS. Ebben a gyakorlatban az alkalmazásában kell használnia a HTTP, a kérelmek és válaszok adatait is megtekintheti. A kérések és válaszok adatokat megtekinteni a tényleges REST-hívások, letöltheti [Fiddler](https://www.telerik.com/fiddler) vagy más hasonló alkalmazás. A Visual Studio-megoldásban a tárfiók nevét és a kulcs az osztályban kötöttek, és adja át a tárfiók nevének és a tárfiók kulcsát ListContainersAsyncREST módszer, amely a különböző összetevők a REST-kérelem létrehozásához használt módszerek . Egy valós alkalmazás esetében a tároló nevének és kulcsának lenne egy konfigurációs fájlban, a környezeti változók találhatók, vagy kérhető le az Azure Key Vaultban.

A mintaprojekt az engedélyezési fejléc létrehozásához a kód pedig egy különálló osztályt, a cél, hogy, sikerült a teljes osztály igénybe vehet, és adja hozzá a saját megoldás és azokat használja ", ami." Az engedélyezési fejléc kód a legtöbb, az Azure Storage REST API-hívásokkal működik.

Hozhat létre a kérést, amely a HttpRequestMessage objektum, nyissa meg a program.cs fájlban ListContainersAsyncREST. A kérelem létrehozásához a lépések a következők: 

* Hozzon létre az URI-t a szolgáltatás meghívása használható. 
* A HttpRequestMessage objektum létrehozása, és állítsa be az adattartalomban. A hasznos adatainak értéke null a következőnél ListContainersAsyncREST, mert azt már nem továbbítja a semmit.
* Adja hozzá a kérelem fejlécében x-ms-date és az x-ms-version.
* Az engedélyezési fejléc lekérése, és adja hozzá.

Néhány alapvető információ van szüksége: 

*  A ListContainers a **metódus** van `GET`. Ez az érték van beállítva, a kérelem hárítható el. 
*  A **erőforrás** az URI-t, amely jelzi, hogy melyik API meghívva, hogy az érték a lekérdezés része `/?comp=list`. Korábban feljegyzett információit jeleníti meg a vonatkozó referencia dokumentációs lap van az erőforrás a [ListContainers API](/rest/api/storageservices/List-Containers2).
*  Az URI-t hoz létre a tárfiók Blob service-végpont, és összefűzi az erőforrás jön létre. Az érték **kérés URI-ja** fejeződik be, hogy `http://contosorest.blob.core.windows.net/?comp=list`.
*  A ListContainers **requestBody** null értékű, és nincsenek részeként **fejlécek**.

Előfordulhat, hogy különböző API-k más paramétereket, például át *ifMatch*. Ahol ifMatch használhatja például akkor, ha a PutBlob hívásakor. Ebben az esetben ifMatch eTag értékre, és ha az Ön Etagje megegyezik a BLOB jelenlegi Etagje csak frissíti a blobot. Ha valaki más frissült, hogy a blob az ETag címke beolvasása óta, a módosítás nem bírálható felül. 

Először állítsa be a `uri` és a `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Következő lépésként hozza létre a kérést, a metódust állítjuk `GET` és biztosítják, hogy az URI-t.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adja hozzá a kérelem fejlécében x-ms-date és az x-ms-version. Ez a hely a kódban is, Itt veheti fel a híváshoz szükséges minden olyan további kérelemfejléc. Ebben a példában nincsenek további fejlécek. Egy példa egy API, amely továbbítja a további fejlécek, SetContainerACL. A Blob Storage hozzáadja a hozzáférési szint "x-ms-blob-public-hozzáférés" és az érték nevű fejléc.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Hívja meg a metódust, amely az engedélyezési fejléc hoz létre, és adja hozzá a kérelem fejlécében. Látni fogja, hogyan hozhat létre az engedélyezési fejléc a cikk későbbi részében. A metódusnév GetAuthorizationHeader, amelyhez ez a kódrészlet látható:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Ezen a ponton `httpRequestMessage` tartalmazza a REST-kérés kész, de az engedélyezési fejléceket. 

## <a name="call-the-rest-api-with-the-request"></a>A REST API-t a kérelemmel

Most, hogy a kérelem, meghívhatja a REST-kérelem elküldéséhez SendAsync. SendAsync meghívja az API-t, és megkapja a választ. Vizsgálja meg a válasz StatusCode (200-as rendben), majd a válasz elemzéséhez. Ebben az esetben egy XML-tárolók listájának kap. Nézzük meg a kódját a kérelem létrehozásához, hajtsa végre a kérést, és vizsgálja meg a választ a tárolók listájában a GetRESTRequest metódus hívása.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Ha például a Hálózatfigyelő futtatása [Fiddler](https://www.telerik.com/fiddler) SendAsync hívása küldésekor a kérések és válaszok információkat láthatja. Tekintsük át. A tárfiók neve *contosorest*.

**A kérelem:**

```
GET /?comp=list HTTP/1.1
```

**Kérelem fejlécei:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Állapot és a válaszidő visszakapott fejlécek végrehajtása után:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Válasz törzsében (XML):** A ListContainers a tárolók és a hozzájuk tartozó tulajdonságok listáját jeleníti meg.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Most, hogy megismerkedett a kérelem létrehozásához, meghívja a szolgáltatást, és elemezni az eredményeket, nézzük meg, hogyan hozhat létre az engedélyeztetési fejléc. A fejléc létrehozása bonyolult, de a jó hír az, ha működik a kódot, működik-e az összes, a Storage szolgáltatás REST API-kat.

## <a name="creating-the-authorization-header"></a>Az engedélyezési fejléc létrehozásához

> [!TIP]
> Az Azure Storage mostantól támogatja az Azure Active Directory (Azure AD) integrálása a Blob és üzenetsor-szolgáltatásokhoz (előzetes verzió). Azure ad-ben, amelyek engedélyezik a kérést az Azure Storage egy sokkal egyszerűbb megoldást kínál. Az Azure AD-vel történő hitelesítéséhez a REST-műveleteinek további információkért lásd: [hitelesítés az Azure Active Directoryval (előzetes verzió)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Az Azure AD-integráció az Azure Storage áttekintését lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md).

Van egy cikk, amely ismerteti a koncepciót tekintve (nem a kódban) végrehajtása [az Azure Storage szolgáltatásainak hitelesítése](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services).
Nézzük nyerhet ki pontosan le a cikkben van szükség, és megjeleníteni a kódot.

Először használja a megosztott kulcsos hitelesítés. Az engedélyezési fejléc formátuma a következőhöz hasonló:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Az aláírás mezőt a kivonat-alapú üzenethitelesítési kód (HMAC) a kérelem alapján létrehozott, és az SHA256 algoritmust használ, akkor a Base64 kódolás használatával kódolt számított. Nem működik, amely? (Az itt lévő lefagy, akkor még nem is hallgassa meg a word *kanonikussá* még.)

Ez a kódrészlet azt mutatja be, a megosztott kulcsos aláírás karakterlánc formátuma:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

Ezek a mezők a legtöbb ritkán használják. A Blob Storage adja meg művelet, az MD5-tel, a tartalom hossza, a Kanonikussá fejlécek és a Kanonikussá erőforrás. Üresen hagyhatja, a többi (de a put a `\n` így az tudni fogja üresek).

Mik azok CanonicalizedHeaders és CanonicalizedResource? Jó kérdést. Sőt mire kanonikussá mean? A Microsoft Word nem is ismeri fel a szó. Mi [Wikipedia szerint a kanonikussá tétele kapcsolatos](https://en.wikipedia.org/wiki/Canonicalization): *Számítógép-tudományi kanonikussá tétele (néha szabványügyi szervezet vagy normalizálási) egy folyamatot, amely rendelkezik egy "standard", "normál", illetve canonical űrlapra egynél több lehetséges reprezentáció adatok alakításával.* A normál beszél, ez azt jelenti, hogy számára (például fejlécek Kanonikussá fejlécek esetében) elemek listáját és a kötelező formátum szabványosíthatja őket. Alapvetően úgy döntött, formátumban a Microsoft, és azt egyeznie kell.

Kezdjük két szabványosított mezők, mert az engedélyezési fejléc létrehozásához szüksége van rájuk.

**Szabványosított fejlécek**

Ezt az értéket, lekérni a fejlécek, kezdje az "x - ms-", és rendezze őket, majd be egy karakterláncot formázni őket `[key:value\n]` példányok, összefűzött be egy karakterláncot. Ebben a példában a szabványosított fejlécek néznek ki: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Itt látható, hogy a kimenet létrehozására használt kódot:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Szabványosított erőforrás**

Ez a rész az aláírás karakterlánc a tárfiók a kérés által megcélzott jelöli. Ne feledje, hogy a kérelem URI-ja `<http://contosorest.blob.core.windows.net/?comp=list>`, a tényleges fióknévvel (`contosorest` ebben az esetben). Ebben a példában ezt adja vissza:

```
/contosorest/\ncomp:list
```

Ha a lekérdezés paraméterei, ez magába foglalja azokat is. Itt van a kódra, amely további lekérdezési paraméterek és a lekérdezési paraméterek több értékkel rendelkező kezelésére is alkalmas. Ne feledje, hogy ez a kód, a REST API-k minden működik, így a felvenni kívánt összes lehetőség, akkor is, ha a ListContainers metódus nem szükséges mindegyiket fejleszt.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Most, hogy a szabványosított karakterláncok vannak beállítva, hogyan hozhat létre az engedélyezési fejléc önmagában nézzük meg. Indítsa el az ebben a cikkben korábban megjelenő StringToSign formátumban, az üzenetek aláírását karakterlánc létrehozásával. A fogalom, könnyebben elmagyarázni, megjegyzések használata a kódban, tehát itt, az utoljára létrehozandó metódust, amely visszaadja az engedélyezési fejléc:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Ha ezt a kódot futtatja, az eredményül kapott MessageSignature néz ki:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

A következő AuthorizationHeader végső értéke:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

A AuthorizationHeader a kérelem fejlécében helyezni, mielőtt elküldené a válasz az utolsó fejléc.

Minden, amit tudnia a kóddal együtt történő helyreállíthatósága használhatja a Storage szolgáltatások REST API-k hívásához használandó kérelmet szeretne létrehozni egy osztály, amely ismerteti.

## <a name="how-about-another-example"></a>Mi a helyzet egy másik példa? 

Nézzük meg a kódot a ListBlobs hívja meg a tároló módosítása *tároló-1*. Ez a majdnem teljesen megegyezik a kódot, tárolók, az URI-t és a válasz elemzéséhez hogyan csak különbségek listázásához. 

Ha megtekinti a dokumentáció a [ListBlobs](/rest/api/storageservices/List-Blobs), úgy találja, hogy van-e a metódus *első* , és a RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

ListContainersAsyncREST módosítsa a kódot, amely beállítja az API-hoz az URI-t ListBlobs. A tároló neve **tároló-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Majd kezelni a választ, ahol módosíthatja a kódot a blobok, tárolók helyett kereséséhez.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Ez a minta futtatásakor az alábbihoz hasonló eredményeket kap:

**Szabványosított fejlécek:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Szabványosított erőforrás:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

A következő értékek közül az [Fiddler](https://www.telerik.com/fiddler):

**A kérelem:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Kérelem fejlécei:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Állapot és a válaszidő visszakapott fejlécek végrehajtása után:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Válasz törzsében (XML):** Az XML-válasz a blobok és a hozzájuk tartozó tulajdonságok listáját jeleníti meg. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, hogyan használatával indítson egy a blob Storage REST API-tárolók listája vagy a tárolóban lévő blobok listájának lekéréséhez. Azt is megtanulta, hogyan hozhat létre a REST API-hívás az engedélyezési aláírás, hogyan használható a REST-kérés és vizsgálata a válasz.

## <a name="next-steps"></a>További lépések

* [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
* [A Fájlszolgáltatás REST API-val](/rest/api/storageservices/file-service-rest-api)
* [Queue szolgáltatás REST API-val](/rest/api/storageservices/queue-service-rest-api)
