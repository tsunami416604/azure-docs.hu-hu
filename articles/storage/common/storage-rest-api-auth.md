---
title: "Azure Storage szolgáltatások REST API-műveleteket, köztük a hitelesítési hívás |} Microsoft Docs"
description: "Azure Storage szolgáltatások REST API-műveleteket, köztük a hitelesítési hívás"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/27/2017
ms.author: robinsh
ms.openlocfilehash: 73921f7fd4de65513f647db92b737a79f1043182
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="using-the-azure-storage-rest-api"></a>Az Azure Storage REST API használatával

A cikkből megtudhatja, hogyan használható a Blob Storage szolgáltatás REST API-k és hogyan hitelesítheti a szolgáltatás hívása. Írás a szempontjából, aki semmit REST és nem tudja hogyan végezheti el a REST-hívást, de a fejlesztők. Azt tekintse át a REST-hívást hivatkozás dokumentációját, és azt jelenti azt, hogy egy tényleges REST-hívást – vonatkozó tudnivalókat mezőket Ugrás where? A dokumentum a REST-hívást beállítása után kihasználhatja az ennek az információnak a többi tárolási szolgáltatás REST API-k valamelyikének használatára.

## <a name="prerequisites"></a>Előfeltételek 

Az alkalmazás egy tárfiók a blob Storage tárolók listája. Próbálja ki ebben a cikkben a kódot, a következőkre van szükség: 

* Telepítés [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) következő munkaterheléssel:
    - Azure-fejlesztés

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Általános célú tárfiókot. Ha nem rendelkezik a storage-fiókok, hozhat létre egy a [Azure-portálon](https://portal.azure.com), [PowerShell](storage-quickstart-create-storage-account-powershell.md), vagy [Azure CLI](storage-quickstart-create-storage-account-cli.md).

* Ebben a cikkben a példa bemutatja, hogyan listázhat a tárolók a tárfiókokban. Kimeneti, vegye fel a blob storage a tárfiókban lévő elindítása előtt bizonyos tárolók.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A mintaalkalmazás egy C# nyelven írt konzolalkalmazással.

Használjon [git](https://git-scm.com/) letölteni az alkalmazást a fejlesztési környezet egy példányát. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Ez a parancs klónokat a tárházat a helyi git-mappába. Nyissa meg a Visual Studio-megoldást, keresse meg a storage-dotnet-rest-api-with-auth mappát, nyissa meg azt, és kattintson duplán arra a StorageRestApiAuth.sln. 

## <a name="why-do-i-need-to-know-rest"></a>Miért kell tudnia a többi?

Hasznos szakértelem ismerete, hogyan használhatja a többi. Az Azure termékért felelős csoport a új szolgáltatások gyakran kiadását. Számos esetben a új szolgáltatások érhetők el a REST-felületen keresztül, de rendelkezik nem még nincs illesztett keresztül **összes** a storage ügyfélkódtáraival vagy a felhasználói felület (például az Azure portálon). Ha mindig szeretne használni a legújabb és legjobb, REST tanulási feltétele. Ha szeretne írni az Azure Storage kommunikál a saját könyvtár, vagy az Azure Storage programozási nyelv, amely nem rendelkezik az SDK vagy a storage ügyféloldali kódtár elérni kívánt, használhatja a REST API-t.

## <a name="what-is-rest"></a>Mi az a többi?

REST azt jelenti, hogy *representational állapot átviteli*. Egy adott definíciót, tekintse meg [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

Alapvetően, a többi pedig használhatja, amikor az architektúrák hívja az API-k vagy a API-k elérhető meghívni. Független mindkét oldalon mi történik, és milyen egyéb szoftvert használja küldésekor vagy fogadásakor, a többi hívja. Az alkalmazás, amely a Mac, Windows, Linux, egy Android-telefonon vagy tábla, iPhone, iPod vagy webhely fut, és az azonos REST API-t használja az összes adott platformokhoz. Adatok átadhatók a kimenő és/vagy a REST API-t kell meghívni. A REST API-t nem fontos a fontos nevezik – platformtól a-adatok lettek átadva a kérelem és a válaszban szereplő megadott adatok nem.

## <a name="heres-the-plan"></a>Ez a terv

A példa projekt tartalmazza a tárolók a tárfiókokban. Hogyan felel meg a REST API dokumentációjában található információk a tényleges kód elsajátítása után más REST-hívások könnyebben kideríthesse, mi is. 

Ha megnézi a [Blob szolgáltatás REST API](/rest/api/storageservices/fileservices/Blob-Service-REST-API), tekintse meg a blob-tároló hajthat végre műveleteket. A storage ügyfélkódtáraival a REST API-k körül burkolók – Ezek megkönnyítik az Ön tároló elérése érdekében anélkül, hogy közvetlenül a REST API-k használatával. De a fentieknek megfelelően néha szeretné használni a REST API-t a storage ügyféloldali kódtár helyett.

## <a name="rest-api-reference-list-containers-api"></a>REST API-referencia: Lista-tárolók API

Vizsgáljuk meg a lap a REST API-referencia a [ListContainers](/rest/api/storageservices/fileservices/List-Containers2) művelet, így tisztában egyes mezőit forrását a kérés- és a kód a következő szakaszban található.

**Kérési módszer**: beolvasása. Ehhez a művelethez a HTTP-metódus adja meg, ha a kérelem objektum tulajdonságként. Ehhez a művelethez más érték HEAD, PUT és DELETE, attól függően, hogy az API-hívás.

**A kérelmi URI**: Ez a blob storage-fiók végpontját készült https://myaccount.blob.core.windows.net/?comp=list `http://myaccount.blob.core.windows.net` és az erőforrás-karakterlánc `/?comp=list`.

[URI-paraméterek](/rest/api/storageservices/fileservices/List-Containers2#uri-parameters): nincsenek ListContainers hívásakor használható további lekérdezési paramétereket. E paraméterek közül néhány vannak *időtúllépés* a híváshoz (másodpercben) és *előtag*, amellyel szűréséhez.

Egy másik hasznos paraméter *maxresults:* több tároló ezen értéknél érhetők el, ha az adott válasz törzse fogja tartalmazni egy *NextMarker* , amely jelzi a következő tárolót, hogy térjen vissza a következő elem a kérést. Ez a funkció használatához adja meg a *NextMarker* érték, mint a *jelölő* amikor hajt végre a következő kérelem URI azonosítójában paraméter. A szolgáltatás használata esetén az eredmények lapozást hasonló. 

További paraméterek használatához hozzáfűzése őket az erőforrás-karakterlánc értékű, például ebben a példában:

```
/?comp=list&timeout=60&maxresults=100
```

[Kérelem fejlécei](/rest/api/storageservices/fileservices/List-Containers2#request-headers)**:** Ez a rész felsorolja a szükséges és választható kérelemfejlécekben. Három a fejlécek szükség: egy *engedélyezési* fejléc, *x-ms-date* (tartalmazza a kéréshez időpontja UTC idő), és *x-ms-version* (határozza meg a többi verzióját API-t használja). Beleértve *x-ms-client-request-id* a fejlécekben nem kötelező – ez a mező értékének állíthat be semmit; írás a storage analytics naplókba Ha naplózása engedélyezve van.

[A kérelem törzsében](/rest/api/storageservices/fileservices/List-Containers2#request-body)**:** nem található kérelemtörzs a ListContainers van. Kérés törzsében használják a PUT műveleteket blobokat, valamint SetContainerAccessPolicy, amely lehetővé teszi egy XML-lista tárolt hozzáférési házirendek alkalmazásához küldését feltöltésekor. A cikkben említett tárolt hozzáférési házirendek [használatával megosztott hozzáférési aláírásokkal (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Válasz állapotkódja](/rest/api/storageservices/fileservices/List-Containers2#status-code)**:** Tells bármely állapotkódok tudnia kell. Ebben a példában a 200-as HTTP-állapotkódot rendben. A HTTP-állapotkódok teljes listájáért tekintse meg [állapotkódok definícióit](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). A Storage REST API-k jellemző hibakódok, olvassa el [közös REST API-hibakódok](/rest/api/storageservices/common-rest-api-error-codes)

[Válaszfejlécek](/rest/api/storageservices/fileservices/List-Containers2#response-headers)**:** ezek közé tartozik a *tartalomtípus*; *az x-ms-request-id* (a Kérelemazonosító átadott, ha van ilyen); *x-ms-version* (a Blob szolgáltatás használt verzióját jelzi.), és a *dátum* (UTC-t, megtudhatja, milyen idő a kérés érkezett).

[Választörzs](/rest/api/storageservices/fileservices/List-Containers2#response-body): A mező kitöltése egy XML-struktúra, a kért adatokat. Ebben a példában a rendszer választ, a tárolók és a tulajdonságok listáját.

## <a name="creating-the-rest-request"></a>A többi kérelem létrehozása

Megjegyzések – megkezdése előtt számos biztonsági éles környezetben való futtatásakor, mindig használjon HTTP helyett HTTPS. Ebben a gyakorlatban az alkalmazásában HTTP így megtekinthető a kérés- és adatokat kell használnia. A kérelem-válasz információk megtekintéséhez a tényleges REST-hívások, letöltheti a [Fiddler](http://www.telerik.com/fiddler) vagy más hasonló alkalmazás. A Visual Studio megoldás a tárfiók nevét és a kulcs szoftveresen kötött a osztály, és adja át a tárfiók nevét és a tárfiók kulcsa a ListContainersAsyncREST metódus, amely a REST-kérelem a különböző összetevők létrehozásához használt módszerek . Egy valós alkalmazás a tárfiók nevét és a kulcs lenne egy konfigurációs fájlban, a környezeti változók találhatók vagy kérhető le az Azure Key Vault.

A minta-projektben a kódot az engedélyezési fejléc létrehozásához van egy külön osztályt, a képet, hogy sikerült a teljes osztály igénybe, és adja hozzá a saját megoldás és használja az "adott állapotban." Az engedélyezési fejléc kód az Azure Storage legtöbb REST API-hívásokhoz működik.

Hozhat létre a kérelmet, amely egy HttpRequestMessage objektum, nyissa meg a program.cs fájlban ListContainersAsyncREST. A kérelem létrehozására szolgáló lépéseket a következők: 

* Hozzon létre az URI-t a szolgáltatás hívásakor használatos. 
* A HttpRequestMessage objektum létrehozása, és állítsa be a tartalom. A tartalom értéke null a ListContainersAsyncREST, mert azt még nem továbbítja a semmit.
* Vegye fel a kérelem fejlécében x-ms-date és az x-ms-version.
* Szerezze be a authorization fejlécet, és vegye fel azt.

Néhány alapvető információ van szüksége: 

*  A ListContainers a **metódus** van `GET`. Ez az érték van beállítva, a kérelem példányának létrehozásakor. 
*  A **erőforrás** az URI, amely jelzi, hogy mely API lett meghívva, ezért az érték a lekérdezés része `/?comp=list`. Ahogy azt korábban említettük, az erőforrás megtalálható-e a referencia-dokumentáció oldalán, amely az adatokat tartalmazza a [ListContainers API](/rest/api/storageservices/fileservices/List-Containers2).
*  Az URI a Blob-szolgáltatásvégpont, hogy a tárfiók létrehozásával, és az erőforrás hozzáfűzésével épül. A következő **kérelmi URI** fejeződik be, hogy `http://contosorest.blob.core.windows.net/?comp=list`.
*  A ListContainers **requestBody** null értékű, és nincs további **fejlécek**.

Előfordulhat, hogy különböző API-k más paramétereket, mint átadni *ifMatch*. Egy példa, ahol ifMatch használatával, amikor PutBlob hívása. Ebben az esetben ifMatch értékre az egy ETag-gel, és ha megadja az eTag megegyezik a jelenlegi eTag blobot csak frissíti a blob. Ha a blob beolvasása a eTag óta frissített valaki más, a módosítás nem bírálható felül. 

Első lépésként állítsa be a `uri` és a `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

A következő példányt létrehozni a kérést, a módszer beállítást `GET` és kezeléséről az URI azonosító.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Vegye fel a kérelem fejlécében x-ms-date és az x-ms-version. Ez a hely, a kódban a híváshoz szükséges további kérelem fejléc hozzáadásához is használatos. Ebben a példában a rendszer nincs további fejlécek. Egy példa, amely továbbítja az extra fejlécek az API-k SetContainerACL. A Blob-tároló hozzáadja a hozzáférési szintet "x-ms-blob-nyilvános-hozzáférés" és az érték névvel fejléc.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-04-17");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

A metódus, amely létrehozza az authorization fejlécet, és vegye fel a kérelem fejlécében. Az engedélyezési fejléc létrehozása a cikk későbbi részében látható. A metódus nevét a következő GetAuthorizationHeader, amelyen megtekintheti a következő kódrészletet:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Ezen a ponton `httpRequestMessage` tartalmazza a REST-kérelem kész, de az engedélyezési fejléceket. 

## <a name="call-the-rest-api-with-the-request"></a>A kért REST API hívása

Most, hogy a kérelem, hívása sendasync metódusok párhuzamosan a többi kérést küldeni. Sendasync metódusok párhuzamosan hívja az API-t, és megkapja a választ. Vizsgálja meg a választ StatusCode (a 200 OK), majd a válasz elemzése. Ebben az esetben kap egy XML-tárolók listájáról. A metódus hívása a GetRESTRequest hozzon létre a kérelmet, a kérelem végrehajtása, és vizsgálja meg a listán szereplő tárolókhoz válasz kódja vizsgáljuk meg.

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

Hálózatfigyelő futtatásakor például a [Fiddler](https://www.telerik.com/fiddler) sendasync metódusok párhuzamosan hívása meghozásakor információkat tekintheti meg a kérelem-válasz. Ismerkedjen meg. A tárfiók neve *contosorest*.

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

**Állapot kód- és válaszfejlécekről végrehajtása után vissza:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 04-17
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Választörzs (XML):** a ListContainers, a tárolók és a tulajdonságok listáját tartalmazza.

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

Most, hogy megismerte a kérelem létrehozása, meghívja a szolgáltatást, és az eredményeket elemezni, nézzük meg az engedélyezési fejléc létrehozásához. A fejléc létrehozása bonyolult, de a megfelelő híreket, miután működik-e a kódot, hogy működik az összes, a tárolás szolgáltatás REST API-k.

## <a name="creating-the-authorization-header"></a>Az engedélyezési fejléc létrehozása

Nincs a cikk azt ismerteti, fogalmilag (nincs kód) hogyan hajthat végre [az Azure Storage szolgáltatásainak hitelesítése](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services).
Most átalakítást pontosan le a cikkben van szükség, és a kód megjelenítése.

Először használja a megosztott kulcsos hitelesítést. Az engedélyezési fejléc formátuma így néz ki:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Az aláírás mezője a kivonat-alapú üzenethitelesítési kód (HMAC) a kérés alapján létrehozott, és az SHA-256 algoritmus használatával, majd a Base64 kódolás használatával kódolt kiszámítása. Kapott, amely? (Az ott leállását, még akkor is kérték egyes a word *kanonikussá* még.)

A kódrészletet jeleníti meg a megosztott kulcs aláírása karakterlánc formátuma:

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

Ezek a mezők ritkán használják. A Blob-tároló megadhatja a művelet, md5, tartalom hossza, a Kanonikussá fejlécek és a Kanonikussá erőforrás. A többi üresen hagyhatja (de a put a `\n` így az tudni fogja, hogy azok üres).

Mik azok a CanonicalizedHeaders és CanonicalizedResource? Jó kérdést. Valójában a funkciója kanonikussá középérték? A Microsoft Word nem is ismeri fel a szót. Mi ez [Wikipedia szerint kapcsolatos kanonizálási](http://en.wikipedia.org/wiki/Canonicalization): *a számítástechnikai, kanonizálási (néha szabványosítás vagy normalizálási) olyan adatok, amelyek egynél több lehetséges átalakítás folyamat a "standard", "normál" vagy kanonikus formába ábrázolását.* A normál beszéd, ez azt jelenti, hogy számára (például fejlécek, a Kanonikussá fejléc esetén) elemek listáját és a szükséges formátumra szabványosítására őket. Alapvetően Microsoft formátum és a azt egyeznie kell.

Kezdjük két szabványosított mezőket, mert azokat az engedélyezési fejléc létrehozásához szükségesek.

**Szabványosított fejlécek**

Ezt az értéket, a fejlécek, "x - ms-" kezdődő, és rendezést beolvasni, majd formázni őket a karakterlánc `[key:value\n]` példányok összefűzendő egyetlen karakterlánccá egyesít. Az ebben a példában a szabványosított fejlécek néznek ki: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-04-17\n
```

A kimenet létrehozásához használt kód itt látható:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
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

Az aláírás karakterlánc ezen része a tárfiókot, a kérelem által megcélzott jelöli. Ne feledje, hogy a kérelem URI-azonosítója `<http://contosorest.blob.core.windows.net/?comp=list>`, a tényleges fióknévvel (`contosorest` ebben az esetben). Ebben a példában ez vissza:

```
/contosorest/\ncomp:list
```

Ha tartalmazhat lekérdezési paramétereket, ilyenek például azok is. Ez a kód, amely további lekérdezés és a több értékkel rendelkező lekérdezés paraméterei is kezeli. Ne feledje, hogy van-e felépítése a kódot, hogy működik az összes REST API-k, ezért a szeretne minden lehetőséget, akkor is, ha a ListContainers metódus nem szükséges az összes.

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

Most, hogy a szabványosított karakterláncok van beállítva, vizsgáljuk meg magát hitelesítési fejlécéhez létrehozása. Az üzenet-aláírást karakterlánc létrehozása ebben a cikkben korábban megjelenített StringToSign formátumban megkezdéséhez. A fogalom, ezért könnyebben ismertetik a kód megjegyzéseket használatát, ezért itt nem, a végleges metódust, amely visszaadja az engedélyezési fejléc:

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

Ez a kód futtatásakor az eredményül kapott MessageSignature néz ki:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-04-17\n/contosorest/\ncomp:list
```

Itt értéke a végső AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

A AuthorizationHeader a forrásanyagát a válasz a kérelem fejlécében elhelyezett utolsó fejléc.

Minden tudnia kell, a kóddal együtt történő helyreállíthatósága segítségével hozzon létre egy kérést a Storage szolgáltatások REST API-k hívására használandó osztály, amely lefedi.

## <a name="how-about-another-example"></a>Mi a helyzet egy másik példa? 

A kód hívására ListBlobs a tároló módosítása nézzük *tároló-1*. Ez az majdnem azonos felsoroló tárolók, az URI Azonosítót, és hogyan elemezni a válasz csak különbségek a kódot. 

Ha megnézi a referenciadokumentációt tartalmaz [ListBlobs](/rest/api/storageservices/fileservices/List-Blobs), látnia, hogy van-e a metódus *beolvasása* , és a RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

ListContainersAsyncREST módosítsa a kódot az API-ra állítja az URI ListBlobs. A tároló neve **tároló-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Majd kezeli a választ, ahol módosíthatja a kódot, és azokban megkereshetik a BLOB tárolók helyett.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Ez a minta futtatásakor eredményt el a következőhöz hasonló:

**Szabványosított fejlécek:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-04-17\n
```

**Szabványosított erőforrás:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-04-17\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

A következő értékek a következők a [Fiddler](http://www.telerik.com/fiddler):

**A kérelem:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Kérelem fejlécei:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-04-17
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Állapot kód- és válaszfejlécekről végrehajtása után vissza:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-04-17
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Választörzs (XML):** az XML-válasz blobok és azok tulajdonságaival listáját jeleníti meg. 

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

Ebben a cikkben megtudta, hogyan indítson egy lekérdezést a blob storage tárolók listája vagy a tárolóban lévő blobok listájának lekéréséhez REST API-t. Azt is megtanulta, az engedélyezési aláírás a REST API-hívások, a többi kérés használatával, és vizsgálja meg a választ.

## <a name="next-steps"></a>Következő lépések

* [BLOB szolgáltatás REST API-n](/rest/api/storageservices/blob-service-rest-api)
* [File szolgáltatás REST API-n](/rest/api/storageservices/file-service-rest-api)
* [Várólista szolgáltatás REST API](/rest/api/storageservices/queue-service-rest-api)