---
title: Az API-formátumok támogatásának korlátozásai és részletei
titleSuffix: Azure API Management
description: Az Open API, WSDL és WADL formátumok ismert problémáinak és korlátozásainak részletei az Azure API Managementben.
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
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513371"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importálási korlátozások és ismert problémák

## <a name="about-this-list"></a>A lista

API-importálásakor előfordulhat, hogy bizonyos korlátozásokkal találkozik, vagy azonosítja azokat a problémákat, amelyeket az importálás sikeres végrehajtása előtt ki kell javítani. Ez a cikk ezeket a korlátozásokat dokumentálja, az API importálási formátuma szerint rendezve. Azt is leírja, hogyan működik az OpenAPI exportálása.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger importálási korlátozások

Ha hibákat kap az OpenAPI-dokumentum importálásával, győződjön meg arról, hogy előzetesen érvényesítette azt. Ezt megteheti az Azure Portalon (Design - Front End - OpenAPI Specification Editor) a tervező vel, vagy egy külső gyártótól származó eszközzel, például a <a href="https://editor.swagger.io">Swagger Editor</a>eszközzel.

### <a name="general"></a><a name="open-api-general"> </a>Általános

-   Az elérési út és a lekérdezés kötelező paramétereinek egyedi névvel kell rendelkezniük. (Az OpenAPI-ban a paraméternévnek csak egy helyen belül kell egyedinek lennie, például elérési útnak, lekérdezésnek, fejlécnek. Az API Management ben azonban lehetővé tesszük, hogy a műveleteket mind az elérési út, mind a lekérdezési paraméterek megkülönböztetést alkalmazzanak (amit az OpenAPI nem támogat). Ezért van szükség arra, hogy a paraméternevek egyediek legyenek a teljes URL-sablonon belül.)
-   `\$ref`a mutatók nem hivatkozhatnak külső fájlokra.
-   `x-ms-paths`és `x-servers` az egyetlen támogatott kiterjesztések.
-   Az egyéni bővítményeket a rendszer figyelmen kívül hagyja az importáláskor, és nem menti vagy őrzi meg az exportáláshoz.
-   `Recursion`- Az API Management nem támogatja a rekurzívan definiált definíciókat (például saját magára utaló sémák).
-   A forrásfájl URL-címe (ha elérhető) a kiszolgáló relatív URL-címeire vonatkozik.
-   A rendszer figyelmen kívül hagyja a biztonsági definíciókat.
-   Az API-műveletek szövegközi sémadefiníciói nem támogatottak. Séma-definíciók vannak definiálva az API-hatókörben, és hivatkozhatók az API-műveletek kérelem- vagy válaszhatókörökben.
-   A megadott URL-paraméternek az URL-sablon részét kell, hogy legyen.
-   `Produces`kulcsszó, amely leírja az API által visszaadott MIME-típusokat, nem támogatott. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 2-es verzió

-   Csak a JSON formátum támogatott.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 3-as verzió

-   Ha `servers` sok van megadva, az API Management megpróbálja kiválasztani az első HTTPs URL-címet. Ha nincsenek HTTPs URL-címek - az első HTTP URL-t. Ha nincsenek HTTP URL-címek - a kiszolgáló URL-címe üres lesz.
-   `Examples`nem támogatott, de `example` az.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI importálási, frissítési és exportálási mechanizmusok

### <a name="add-new-api-via-openapi-import"></a>Új API hozzáadása az OpenAPI importálásán keresztül

Az OpenAPI-dokumentumban található minden egyes művelethez egy új művelet jön `operationId` létre `summary` az Azure-erőforrás nevével és a megjelenítendő név beállításával. `operationId`az alábbiakban ismertetett szabályok szerint normalizálódik. `summary`az értéket a program a szerint importálja, és hossza 300 karakterre korlátozódik.

Ha `operationId` nincs megadva (azaz nincs `null`jelen, vagy üres), az Azure-erőforrás névértéke http-metódus és `get-foo`elérési útsablon kombinálásával jön létre, például.

Ha `summary` nincs megadva (azaz nincs `null`jelen vagy `display name` üres), az `operationId`érték a . Ha `operationId` nincs megadva, a megjelenítendő név értéke http-metódus és `Get - /foo`elérésiút-sablon kombinálásával jön létre, például .

### <a name="update-an-existing-api-via-openapi-import"></a>Meglévő API frissítése OpenAPI-importálással

Az importálás során a meglévő API-t módosítja, hogy megfeleljen az OpenAPI-dokumentumban leírt API-nak. Az OpenAPI-dokumentum minden egyes művelete a `operationId` meglévő művelethez igazodik, összehasonlítva annak értékét a meglévő művelet Azure-erőforrás nevével.

Ha egyezést talál, a meglévő művelet tulajdonságai "helyben" frissülnek.

Ha nem talál egyezést, a fenti szakaszban ismertetett szabályok szerint új művelet jön létre. Az importálás minden új műveletesetében megkísérli a házirendek másolását egy meglévő műveletből ugyanazzal a HTTP-módszerrel és elérési útsablonnal.

Az összes meglévő nem egyező művelet törlődik.

Az importálás kiszámíthatóbbá tévő érdekében kövesse az alábbi irányelveket:

- Győződjön meg `operationId` arról, hogy minden művelethez megad tulajdonságot.
- Tartózkodjon `operationId` a módosítástól az első importálás után.
- Soha `operationId` ne változtassa meg és a HTTP metódust vagy elérési utat egyszerre.

### <a name="export-api-as-openapi"></a>API exportálása OpenAPI-ként

Minden műveletesetében az Azure-erőforrás neve `operationId`egy ként lesz exportálva, a `summary`megjelenítendő név pedig .
Normalizálási szabályok a műveletazonosítóhoz

- Átalakítás kisbetűssé.
- A nem alfanumerikus karakterek minden sorozatát cserélje `GET-/foo/{bar}?buzz={quix}` le egyetlen kötőjelre, például a következővé `get-foo-bar-buzz-quix-`alakul át: .
- A vágási kötőjelek mindkét `get-foo-bar-buzz-quix-` oldalon, például`get-foo-bar-buzz-quix`
- Csonkolás 76 karakterhez, ami négy karakterrel kisebb, mint egy erőforrásnév maximális korlátja.
- Szükség esetén a maradéktalanítási utótaghoz szükség esetén a `-1, -2, ..., -999`fennmaradó négy karaktert használja a .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL fájlok létrehozásához használt SOAP pass-through és SOAP-to-REST API-k.

-   **SOAP kötések** -Csak soap kötések stílus "dokumentum" és a "literális" kódolás támogatottak. Az "rpc" stílus vagy a SOAP-kódolás nem támogatott.
-   **WSDL:Importálás** – Ez az attribútum nem támogatott. A vevőknek egyetlen dokumentumba kell egyesíteniük az importálást.
-   **Több részből álló üzenetek** – Az ilyen típusú üzenetek nem támogatottak.
-   **WCF wsHttpBinding** – A Windows Kommunikációs alaprendszermegkötéssel létrehozott SOAP-szolgáltatásoknak alapszintűHttpBinding - wsHttpBinding szolgáltatást kell használniuk.
-   **MTOM** - Az MTOM-et használó szolgáltatások működhetnek. <em>may</em> A hivatalos támogatást jelenleg nem ajánlják fel.
-   **Rekurzió** – A rekurzívan definiált típusokat (például saját tömbre hivatkoznak) az APIM nem támogatja.
-   **Több névterek** – Több névterek is használhatók egy sémában, de csak a célnévtér használható az üzenetrészek definiálására. A céltól eltérő névterek, amelyek más bemeneti vagy kimeneti elemek meghatározására szolgálnak, nem őrződnek meg. Bár egy ilyen WSDL-dokumentum importálható, az összes üzenetalkatrész exportálásakor a WSDL célnévtere lesz.
-   **Tömbök** – SOAP-TO-REST átalakítás csak az alábbi példában látható burkolt tömböket támogatja:

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

## <a name="wadl"></a><a name="wadl"> </a>WADL

Jelenleg nincsenek ismert WADL import problémák.
