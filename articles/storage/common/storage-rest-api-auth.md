---
title: REST API-műveletek hívása megosztott kulcs engedélyezéssel
titleSuffix: Azure Storage
description: Az Azure Storage REST API-val, hogy a kérelem blob storage megosztott kulcs engedélyezése használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5c6125b850062450516e7fc0b19c2e0d5d6f577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916064"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>REST API-műveletek hívása megosztott kulcs engedélyezéssel

Ez a cikk bemutatja, hogyan hívhatja meg az Azure Storage REST API-kat, beleértve az engedélyezési fejléc kialakításának módját is. Meg van írva a szempontból a fejlesztő, aki semmit sem tud rest, és fogalmam sincs, hogyan kell egy REST hívást. Miután megtanulta, hogyan hívhat meg egy REST-műveletet, ezt a tudást kihasználva bármely más Azure Storage REST-műveletet használhat.

## <a name="prerequisites"></a>Előfeltételek

A mintaalkalmazás felsorolja a blob tárolók egy tárfiókhoz. A cikkben szereplő kód kipróbálásához a következő elemekre van szükség:

- Telepítse a [Visual Studio 2019-et](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) az **Azure fejlesztési** munkaterhelésével.

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- Általános célú tárfiók. Ha még nem rendelkezik tárfiókkal, olvassa el a Tárfiók létrehozása című [témakört.](storage-account-create.md)

- Ebben a cikkben a példa bemutatja, hogyan listázza a tárolók egy tárfiókban. A kimenet megtekintéséhez adjon hozzá néhány tárolót a blob storage a tárfiókban, mielőtt elkezdené.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A mintaalkalmazás egy C#-ban írt konzolalkalmazás.

A [git](https://git-scm.com/) segítségével töltse le az alkalmazás egy példányát a fejlesztői környezetbe.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Visual Studio-megoldás megnyitásához keresse meg a storage-dotnet-rest-api-with-auth mappát, nyissa meg, és kattintson duplán a StorageRestApiAuth.sln fájlra.

## <a name="about-rest"></a>A REST-ről

Rest jelentése *reprezentációs állam átadása*. Egy adott meghatározás, nézd meg [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

Rest egy architektúra, amely lehetővé teszi, hogy egy szolgáltatás egy internetes protokollon keresztül, például a HTTP/HTTPS. A REST független a kiszolgálón vagy az ügyfélen futó szoftvertől. A REST API bármely olyan platformról hívható, amely támogatja a HTTP/HTTPS protokollt. Írhat egy alkalmazást, amely macen, Windowson, Linuxon, Android-telefonon vagy -táblagépen, iPhone-on, iPod-on vagy webhelyen fut, és ugyanazt a REST API-t használhatja az összes ilyen platformon.

A REST API-hoz való hívás egy kérelemből áll, amelyet az ügyfél küld, és egy választ, amelyet a szolgáltatás küld vissza. A kérelemben egy URL-címet küld arról, hogy melyik műveletet szeretné meghívni, milyen erőforrást kíván eljárni, a lekérdezési paramétereket és a fejléceket, és a hívott művelettől függően egy adathasznos adatadat-tartalommal. A szolgáltatás válasza tartalmaz egy állapotkódot, válaszfejlécek készletét, és a hívott művelettől függően egy adattartalom.

## <a name="about-the-sample-application"></a>A mintaalkalmazásról

A mintaalkalmazás felsorolja a tárolók egy tárfiókban. Miután megértette, hogy a REST API dokumentációjában szereplő információk hogyan viszonyulnak a tényleges kódhoz, a többi REST-hívás könnyebben kitalálható.

Ha megnézi a [Blob Service REST API-t,](/rest/api/storageservices/Blob-Service-REST-API)láthatja a blob storage-on végrehajtható összes műveletet. A tárolóügyfél-kódtárak a REST API-k körül burkolók – megkönnyítik a tároló elérését a REST API-k közvetlen használata nélkül. De afent említettmódon, néha a REST API-t szeretné használni a tárolóügyfél-kódtár helyett.

## <a name="list-containers-operation"></a>Tárolók listázása művelet

Tekintse át a [ListContainers](/rest/api/storageservices/List-Containers2) művelet hivatkozását. Ez az információ segít megérteni, hogy a mezők közül néhány honnan származik a kérésben és a válaszban.

**Kérelem módja**: GET. Ez az ige a kérelemobjektum tulajdonságaként megadott HTTP-módszer. Az ige további értékei közé tartozik a HEAD, A PUT és a DELETE, a hívott API-tól függően.

**Uri**kérése `https://myaccount.blob.core.windows.net/?comp=list`: .A kérelem URI jön létre a `http://myaccount.blob.core.windows.net` blob storage-fiók végpont és az erőforrás-karakterlánc. `/?comp=list`

[URI-paraméterek](/rest/api/storageservices/List-Containers2#uri-parameters): További lekérdezési paraméterek et is használhat a ListContainers hívásakor. Néhány ilyen paraméter a szűréshez használt hívás (másodpercben) és *előtag* *időkimenő* száma.

Egy másik hasznos paraméter a *maxresults:* ha több tároló áll rendelkezésre, mint ez az érték, a választörzs tartalmaz egy *NextMarker* elemet, amely jelzi a következő tárolót, amelyet a következő kérésre vissza kell adni. A szolgáltatás használatához adja meg a *NextMarker* értéket *az URI-ban,* amikor a következő kérést küldi. Ha ezt a funkciót használja, hasonló az eredményeken keresztül történő lapozáshoz.

További paraméterek használatához fűzzhozzá őket az erőforrás-karakterlánchoz az értékkel, például ebben a példában:

```
/?comp=list&timeout=60&maxresults=100
```

[Kérelemfejlécek:](/rest/api/storageservices/List-Containers2#request-headers)**:** Ez a szakasz a szükséges és a nem kötelező kérésfejléceket sorolja fel. Három fejlécre van szükség: egy *engedélyezési* fejlécre, *egy x-ms-dátumra* (tartalmazza a kérelem UTC idejét) és *x-ms-verziót* (a REST API használandó verziójának verzióját adja meg). Ha *az x-ms-client-request-id-t* nem kötelező betenni a fejlécekbe – a mező értékét bármire állíthatja; a naplózás engedélyezése esetén a storage analytics naplókba kerül.

[Kérelem](/rest/api/storageservices/List-Containers2#request-body)**törzse:** A ListContainers nem kéréstörzse. A request body az összes PUT-műveletben a blobok feltöltésekén, valamint a SetContainerAccessPolicy- en használatos, amely lehetővé teszi az alkalmazandó tárolt hozzáférési házirendek XML-listájának elküldését. A tárolt hozzáférési házirendeket a [Megosztott hozzáférésű aláírások használata (SAS) című cikk tárgyalja.](storage-sas-overview.md)

[Válasz állapotkódja](/rest/api/storageservices/List-Containers2#status-code)**:** Minden olyan állapotkódról tájékoztat, amelyet ismernie kell. Ebben a példában egy 200-as HTTP-állapotkód rendben van. A HTTP-állapotkódok teljes listáját az [Állapotkód-definíciók](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)című listában válassza ki. A Storage REST API-kra jellemző hibakódok megtekintéséhez olvassa el a [Gyakori REST API-hibakódok című témakört.](/rest/api/storageservices/common-rest-api-error-codes)

[Válaszfejlécek](/rest/api/storageservices/List-Containers2#response-headers)**:** Ezek közé tartozik *a tartalomtípus*; *x-ms-request-id*, amely a kérelem azonosítója, amelyet átadott; *x-ms-verzió*, amely a Blob szolgáltatás használt verzióját jelzi; és a *Dátum*, amely utc-ben van, és megmondja, hogy a kérelem mikor érkezett.

[Válasz törzse](/rest/api/storageservices/List-Containers2#response-body): Ez a mező egy XML-struktúra, amely a kért adatokat biztosítja. Ebben a példában a válasz a tárolók és azok tulajdonságainak listája.

## <a name="creating-the-rest-request"></a>A REST-kérelem létrehozása

Az éles környezetben való futtatás biztonsága érdekében http helyett mindig HTTPS-t használjon. Ennek a gyakorlatnak az alkalmazásában http-t kell használnia, hogy megtekinthesse a kérelem és a válasz adatait. A kérelem- és válaszinformációk megtekintéséhez a tényleges REST-hívások, letöltheti [a Fiddler](https://www.telerik.com/fiddler) vagy egy hasonló alkalmazás. A Visual Studio-megoldásban a tárfiók neve és kulcsa az osztályban van kódolva. A ListContainersAsyncREST metódus átadja a tárfiók nevét és a tárfiók kulcsát a REST-kérelem különböző összetevőinek létrehozásához használt metódusok számára. Egy valós alkalmazás, a tárfiók neve és kulcsa egy konfigurációs fájlban, környezeti változók, vagy egy Azure Key Vault lekérésre.

A mintaprojektben az engedélyezési fejléc létrehozásának kódja egy külön osztályban található. Az ötlet az, hogy lehet venni az egész osztályt, és add hozzá a saját megoldás, és használja "ahogy van." Az engedélyezési fejléc kód működik a legtöbb REST API-hívások az Azure Storage.The Authorization header code works for most REST API calls to Azure Storage.

A kérelem létrehozásához, amely egy HttpRequestMessage objektum, nyissa meg a ListContainersAsyncREST Program.cs. A kérelem létrehozásának lépései a következők:

- Hozza létre a szolgáltatás hívásához használandó URI-t.
- Hozza létre a HttpRequestMessage objektumot, és állítsa be a hasznos adat. A hasznos adat null a ListContainersAsyncREST esetében, mert nem adunk át semmit.
- Adja hozzá az x-ms-date és az x-ms-version kérelemfejléceit.
- Az engedélyezési fejléc leolvasása és hozzáadása.

Néhány alapvető információ, amire szüksége van:

- A ListContainers **method** esetében `GET`a módszer . Ez az érték a kérelem példányosításakor van beállítva.
- Az **erőforrás** az URI lekérdezési része, amely jelzi, hogy melyik `/?comp=list`API-t hívják meg, így az érték . Amint azt korábban megjegyeztük, az erőforrás a [ListContainers API-val](/rest/api/storageservices/List-Containers2)kapcsolatos információkat megjelenítő referenciadokumentációs lapon található.
- Az URI úgy épül fel, hogy létrehozza a Blob szolgáltatás végpontját az adott tárfiókhoz, és összefűzi az erőforrást. A kérelem URI-jának `http://contosorest.blob.core.windows.net/?comp=list`értéke végül a . **request URI**
- A ListContainers esetében a **requestBody** null értékű, és nincsenek további **fejlécek.**

A különböző API-k más paramétereket is átadhatnak, például *ifMatch*. Egy példa arra, hogy hol lehet használni ifMatch a PutBlob hívása. Ebben az esetben állítsa be az ifMatch-et egy eTag-re, és csak akkor frissíti a blobot, ha a megadott eTag megegyezik a blob aktuális eTag-jével. Ha valaki más frissítette a blobot az eTag beolvasása óta, a módosítás nem lesz felülbírálva.

Először állítsa `uri` be `payload`a és a .

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Ezután példányosítsa ki a `GET` kérelmet, a metódus beállítása és az URI biztosítása.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adja hozzá a `x-ms-date` kérelemfejléceket a és `x-ms-version`a hoz. Ez a hely a kódban is, ahol hozzá minden további kérelem fejlécek szükséges a híváshoz. Ebben a példában nincsenek további fejlécek. Egy példa egy API-t, amely átmegy a további fejlécek a Set Container ACL művelet. Ez az API-hívás hozzáad egy fejlécet az "x-ms-blob-public-access" és a hozzáférési szint értéke.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Hívja meg az engedélyezési fejlécet létrehozó metódust, és adja hozzá a kérelemfejlécekhez. Az engedélyezési fejléc létrehozásáról a cikk későbbi részében olvashat. A metódus neve GetAuthorizationHeader, amely ebben a kódrészletben látható:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Ezen a `httpRequestMessage` ponton tartalmazza a REST-kérelmet az engedélyezési fejlécekkel kiegészítve.

## <a name="send-the-request"></a>A kérelem elküldése

Most, hogy már megalkotta a kérelmet, meghívhatja a SendAsync metódust az Azure Storage-ba való elküldéséhez. Ellenőrizze, hogy a válasz állapotkódértéke 200-as, ami azt jelenti, hogy a művelet sikeres volt. Ezután elemezd a választ. Ebben az esetben a tárolók XML-listáját kapja meg. Nézzük meg a GetRESTRequest metódus hívásának kódját a kérelem létrehozásához, a kérelem végrehajtásához, majd vizsgáljuk meg a tárolók listájának válaszát.

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

Ha egy hálózati szimatolót futtat, például [a Fiddlert,](https://www.telerik.com/fiddler) amikor a SendAsync hívását kezdeményezi, láthatja a kérés- és válaszadatokat. Lássuk. A tárfiók neve *contosorest*.

**Kérés:**

```
GET /?comp=list HTTP/1.1
```

**Fejlécek kérése:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**A végrehajtás után visszaadott állapotkód és válaszfejlécek:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Választörzs (XML):** A listatárolók művelet, ez a tárolók listáját és azok tulajdonságait jeleníti meg.

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

Most, hogy már tisztában van azzal, hogyan hozhat létre a kérelmet, hívja meg a szolgáltatást, és elemezheti az eredményeket, nézzük meg, hogyan hozhat létre az engedélyezési fejléc. A fejléc létrehozása bonyolult, de a jó hír az, hogy ha már a kód működik, akkor működik az összes storage service REST API-k.

## <a name="creating-the-authorization-header"></a>Az engedélyezési fejléc létrehozása

> [!TIP]
> Az Azure Storage mostantól támogatja az Azure Active Directory (Azure AD) integrációját a blobok és várólisták esetében. Az Azure AD sokkal egyszerűbb élményt nyújt az Azure Storage-nak történő kérelem engedélyezéséhez. A REST-műveletek engedélyezéséhez az Azure AD használatával kapcsolatos további információkért olvassa el az [Engedélyezés az Azure Active Directoryval című témakört.](/rest/api/storageservices/authorize-with-azure-active-directory) Az Azure AD-integráció és az Azure Storage áttekintését az [Azure Storage-hoz való hozzáférés hitelesítése az Azure Active Directory használatával](storage-auth-aad.md)című témakörben találja.

Van egy cikk, amely ismerteti fogalmilag (nincs kód), hogyan [engedélyezi a kérelmeket az Azure Storage.There](/rest/api/storageservices/authorize-requests-to-azure-storage)is an article that explains conceptually (no code) how to Authorize requests to Azure Storage.

Nézzük desztillál, hogy a cikk le, hogy pontosan szükség van, és mutassa meg a kódot.

Először használja a megosztott kulcs engedélyezést. Az engedélyezési fejléc formátuma így néz ki:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Az aláírásmező egy kivonatalapú üzenethitelesítési kód (HMAC), amelyet a kérelemből hoztak létre, és amelyet az SHA256 algoritmussal számítanak ki, majd Base64 kódolással kódolnak. Felfogta? (Tarts ki, még nem is hallotta a szót *kanonizált* még.)

Ez a kódrészlet a megosztott kulcs aláírási karakterláncformátumát mutatja:

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

A legtöbb ilyen mezőt ritkán használják. A Blob storage, meg kell adnia VERB, md5, tartalom hossza, Kanonizált fejlécek, és a Kanonizált erőforrás. Tudod hagy a többit üres `\n` (de tegye a így tudja, hogy üres).

Mik azok a CanonicalizedHeaders és canonicalizedResource? Remek kérdés. Valójában mit jelent a canonicalized? A Microsoft Word még csak nem is ismeri fel szóként. Itt van, amit [a Wikipedia mond a kanonizáció](https://en.wikipedia.org/wiki/Canonicalization): A *számítástechnika, a kanonikus (néha szabványosítás vagy normalizálás) egy olyan folyamat átalakítására adatok, amelyek több mint egy lehetséges képviseletegy "standard", "normál", vagy kanonikus formában.* A normál-beszélni, ez azt jelenti, hogy a tételek listáját (például a fejlécek esetében a Kanonikus fejlécek), és szabványosítani őket a szükséges formátumban. Alapvetően a Microsoft úgy döntött, a formátumot, és meg kell, hogy megfeleljen azt.

Kezdjük azzal a két kanonizált mezővel, mert az engedélyezési fejléc létrehozásához szükségesek.

### <a name="canonicalized-headers"></a>Kanonizált fejlécek

Az érték létrehozásához olvassa be az "x-ms-" kezdetű fejléceket, és `[key:value\n]` rendezze őket, majd formázza őket példányok sorozatába, amelyeket egy karakterláncba fűz. Ebben a példában a kanonizált fejlécek a következőkre néznek ki:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

A kimenet létrehozásához használt kód:

```csharp
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

### <a name="canonicalized-resource"></a>Kanonizált erőforrás

Az aláírási karakterlánc ezen része a kérelem által megcélzott tárfiókot jelöli. Ne feledje, `<http://contosorest.blob.core.windows.net/?comp=list>`hogy a kérelem URI`contosorest` van , a tényleges fiók neve ( ebben az esetben). Ebben a példában ez a da júlva jelenik meg:

```
/contosorest/\ncomp:list
```

Ha rendelkezik lekérdezési paraméterek, ebben a példában is ezeket a paramétereket. Itt van a kód, amely további lekérdezési paramétereket és több értékkel rendelkező lekérdezési paramétereket is kezel. Ne feledje, hogy ezt a kódot úgy hozhatja össze, hogy az összes REST API-hoz működjön. Azt szeretné, hogy tartalmazza az összes lehetőséget, még akkor is, ha a ListContainers metódus nem kell mindegyik.

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
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Most, hogy a kanonizált karakterláncok be vannak állítva, nézzük meg, hogyan lehet magát az engedélyezési fejlécet létrehozni. Először hozzon létre egy karakterláncot az üzenet aláírás formátumában StringToSign korábban megjelent ebben a cikkben. Ez a fogalom könnyebb en megmagyarázni a megjegyzéseket a kódot, így itt van, a végső módszer, amely visszaadja az engedélyezési fejléc:

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
        storageAccountName + ":" + signature);
    return authHV;
}
```

A kód futtatásakor az eredményül kapott MessageSignature a következő példához hasonlóan néz ki:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Az AuthorizationHeader végleges értéke:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

A AuthorizationHeader a kérelemfejlécekben a válasz feladása előtt elhelyezett utolsó fejléc.

Ez magában foglalja mindazt, amit tudnia kell egy osztály összeállításához, amellyel létrehozhat egy kérelmet a Storage Services REST API-k hívására.

## <a name="example-list-blobs"></a>Példa: Blobok listázása

Nézzük meg, hogyan módosíthatja a kódot, hogy hívja a List Blobs művelet *konténer-1.* Ez a kód majdnem megegyezik a tárolók listázásának kódjával, az egyetlen különbség az URI és a válasz elemzési célja.

Ha megnézi a [ListBlobs referenciadokumentációját,](/rest/api/storageservices/List-Blobs)azt találja, hogy a metódus *GET,* a RequestURI pedig:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

A ListContainersAsyncREST-ben módosítsa azt a kódot, amely az URI-t a ListBlobs API-jára állítja. A tároló neve **container-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Ezután ahol kezeli a választ, módosítsa a kódot, hogy a tárolók helyett blobokat keressen.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

A minta futtatásakor a következőhez hasonló eredményeket kap:

**Kanonizált fejlécek:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Kanonizált erőforrás:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Üzenet aláírása:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Engedélyezési fejléc:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

A következő értékek a [Fiddler:](https://www.telerik.com/fiddler)

**Kérés:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Fejlécek kérése:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**A végrehajtás után visszaadott állapotkód és válaszfejlécek:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Választörzs (XML):** Ez az XML-válasz a blobok listáját és azok tulajdonságait jeleníti meg.

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

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta, hogyan kérhet a blob storage REST API-t. A kérelemmel lekérheti a tárolók listáját vagy a tárolóban lévő blobok listáját. Megtanulta, hogyan hozhat létre az engedélyezési aláírást a REST API-híváshoz, és hogyan használhatja azt a REST-kérelemben. Végül megtanulta, hogyan vizsgálja meg a választ.

## <a name="next-steps"></a>További lépések

- [A Blob szolgáltatás REST API-ja](/rest/api/storageservices/blob-service-rest-api)
- [A File szolgáltatás REST API-ja](/rest/api/storageservices/file-service-rest-api)
- [A Queue szolgáltatás REST API-ja](/rest/api/storageservices/queue-service-rest-api)
- [A Table szolgáltatás REST API-ja](/rest/api/storageservices/table-service-rest-api)
