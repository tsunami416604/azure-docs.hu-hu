---
title: Az API-formátumok támogatásának korlátozásai és részletei
titleSuffix: Azure API Management
description: Az Azure API Management által támogatott nyílt API-, WSDL-és WADL-formátumokkal kapcsolatos ismert problémák és korlátozások részletei.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: a1c514368960d39834125bd497d05b3d9ebeae7c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640703"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importálási korlátozások és ismert problémák

## <a name="about-this-list"></a>A lista ismertetése

Az API-k importálásakor előfordulhat, hogy bizonyos korlátozásokkal vagy az importálás sikeres végrehajtása előtt meg kell határozni azokat a problémákat, amelyeket orvosolni kell. Ez a cikk ezeket a korlátozásokat az API importálási formátuma alapján rendezi. Azt is leírja, hogyan működik a OpenAPI exportálása.

## <a name="open-api"> </a>OpenAPI/hencegés importálási korlátai

Ha hibaüzenetet kap a OpenAPI-dokumentum importálásakor, győződjön meg róla, hogy előzetesen ellenőrizte. Ezt megteheti, ha a tervezőt használja a Azure Portalban (OpenAPI-specifikációs szerkesztő), vagy egy harmadik féltől származó eszközzel, például a <a href="https://editor.swagger.io">hencegés szerkesztőjével</a>.

### <a name="open-api-general"> </a>Általános

-   Az elérési út és a lekérdezés kötelező paramétereinek egyedi névvel kell rendelkezniük. (A OpenAPI csak egyedinek kell lennie egy helyen belül, például elérési út, lekérdezés, fejléc. API Management azonban lehetővé tesszük, hogy a műveletek a két útvonal és a lekérdezési paraméterek (amelyek nem támogatják a OpenAPI) megkülönböztetését. Ezért a paraméterek neveinek egyedinek kell lenniük a teljes URL-sablonon belül.)
-   a `\$ref`-mutatók nem hivatkozhatnak külső fájlokra.
-   a `x-ms-paths` és a `x-servers` az egyetlen támogatott bővítmény.
-   Az egyéni bővítményeket a rendszer figyelmen kívül hagyja az importálás során, és nem menti vagy nem őrzi meg az exportálást.
-   `Recursion` – a API Management nem támogatja a rekurzív módon definiált definíciókat (például a magukra hivatkozó sémákat).
-   A forrásfájl URL-címe (ha elérhető) a relatív kiszolgálói URL-címekre lesz alkalmazva.
-   A biztonsági definíciók figyelmen kívül lesznek hagyva.
-   Az API-műveletekhez tartozó beágyazott séma-definíciók nem támogatottak. A séma-definíciók az API-hatókörben vannak meghatározva, és az API-műveleti kérelemben vagy a válasz hatókörében is szerepelhetnek.
-   Egy meghatározott URL-paraméternek az URL-sablon részét kell képeznie.
-   `Produces` kulcsszó, amely leírja az API által visszaadott MIME-típusokat, nem támogatott. 

### <a name="open-api-v2"> </a>OpenAPI 2. verzió

-   Csak a JSON formátum támogatott.

### <a name="open-api-v3"> </a>OpenAPI 3-as verzió

-   Ha sok `servers` van megadva, API Management megpróbálja kiválasztani az első HTTPs URL-címet. Ha nincsenek HTTPs URL-címek – az első HTTP URL-cím. Ha nincs HTTP URL-cím, akkor a kiszolgáló URL-címe üres lesz.
-   a `Examples` nem támogatott, de `example`.
-   a `Multipart/form-data` nem támogatott.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI importálási, frissítési és exportálási mechanizmusai

### <a name="add-new-api-via-openapi-import"></a>Új API hozzáadása az OpenAPI import használatával

Az OpenAPI dokumentumban található minden egyes művelethez új művelet jön létre az Azure-Erőforrás neve és a megjelenítendő név alapján, `operationId` és `summary`. `operationId` érték normalizálva van az alább ismertetett szabályok követése után. `summary` értéket importálja a-as értékre, és a hossza 300 karakterre van korlátozva.

Ha `operationId` nincs megadva (azaz nem jelen, `null`vagy üres), az Azure-Erőforrás neve értéket a HTTP-metódus és az elérésiút-sablon (például `get-foo`) kombinálásával hozza létre a rendszer.

Ha `summary` nincs megadva (azaz nincs jelen, `null`vagy üres), a `display name` érték `operationId`lesz. Ha `operationId` nincs megadva, a megjelenített név értéke a HTTP-metódus és az elérésiút-sablon kombinálásával jön létre, például `Get - /foo`.

### <a name="update-an-existing-api-via-openapi-import"></a>Meglévő API frissítése a OpenAPI-importálás használatával

A meglévő API importálása során a rendszer az OpenAPI dokumentumban leírt API-ra módosítja. A OpenAPI-dokumentum minden művelete a meglévő műveletnek megfelelően van összehasonlítva az `operationId` értékének a meglévő művelet Azure-erőforrás neveként való összehasonlításával.

Ha a rendszer egyezést talál, a meglévő művelet tulajdonságai "helyben" lesznek frissítve.

Ha egy egyezés nem található, a fenti részben ismertetett szabályok alapján új művelet jön létre. Az importálás során a rendszer minden egyes új művelet esetében megkísérli egy meglévő műveletből származó házirendek másolását ugyanazzal a HTTP-metódussal és elérésiút-sablonnal.

A rendszer törli az összes meglévő nem egyező műveletet.

A következő irányelveket követve hatékonyabbá teheti az importálást:

- Győződjön meg arról, hogy minden művelethez `operationId` tulajdonságot kell megadni.
- A kezdeti importálás után ne módosítsa `operationId`.
- Soha ne módosítsa `operationId` és HTTP metódust vagy elérésiút-sablont egyszerre.

### <a name="export-api-as-openapi"></a>API exportálása OpenAPI

Minden művelethez az Azure-Erőforrás neve `operationId`lesz exportálva, és a megjelenített név `summary`lesz exportálva.
A operationId vonatkozó normalizálás szabályai

- Kis-és nagybetűkre konvertálható.
- Cserélje le a nem alfanumerikus karakterek egyes sorozatait egyetlen kötőjeltel, például `GET-/foo/{bar}?buzz={quix}` a `get-foo-bar-buzz-quix-`ba lesz átalakítva.
- Mindkét oldalon levágja a kötőjeleket, például `get-foo-bar-buzz-quix-` lesz `get-foo-bar-buzz-quix`
- Csonkítás 76 karakterre, az erőforrás neveként legfeljebb négy karakterből állhat.
- Ha szükséges, használja a további négy karaktert a deduplikáló utótaghoz `-1, -2, ..., -999`formájában.


## <a name="wsdl"> </a>WSDL

A WSDL-fájlok SOAP-alapú áteresztő és SOAP-REST API-k létrehozásához használhatók.

-   **SOAP-kötések** – csak a "document" és a "literális" kódolású SOAP-kötések támogatottak. Az "RPC" stílus vagy a SOAP-kódolás nem támogatott.
-   **Wsdl: import** – ez az attribútum nem támogatott. Az ügyfeleknek egyetlen dokumentumba kell egyesíteni az importálást.
-   **Több részből álló üzenetek** – az ilyen típusú üzenetek nem támogatottak.
-   **WCF wsHttpBinding** – a Windows Communication Foundation által létrehozott SOAP-szolgáltatásoknak az BasicHttpBinding-wsHttpBinding használata nem támogatott.
-   **MTOM** – a MTOM-t használó <em>szolgáltatások működhetnek</em> . A hivatalos támogatás jelenleg nem érhető el.
-   **Rekurzív** – a APIM nem támogatja a rekurzív módon definiált (például a saját tömbre hivatkozó) típusokat.
-   **Több névtér** – több névtér is használható egy sémában, de csak a cél névtér használható az üzenetek részeinek definiálásához. A más bemeneti vagy kimeneti elemek meghatározására szolgáló nem a célként megadott névterek nem őrződnek meg. Habár egy ilyen WSDL-dokumentum importálható, az összes üzenet részeinek exportálásakor a WSDL cél névterét fogja használni.
-   **Tömbök** – a SOAP – Rest átalakítás csak az alábbi példában látható burkolt tömböket támogatja:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"> </a>WADL

Jelenleg nincsenek ismert WADL-importálási problémák.
