---
title: Az Azure API Management API importálásának korlátozásai és ismert problémái | Microsoft Docs
description: Az Azure API Managementba való importálással kapcsolatos ismert problémák és korlátozások részletei az Open API, a WSDL vagy a WADL formats használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: bf39e508b8e4c883934b51fdc99eaef96caf1235
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018211"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importálási korlátozások és ismert problémák

## <a name="about-this-list"></a>A lista ismertetése

Az API-k importálásakor előfordulhat, hogy bizonyos korlátozásokkal vagy a sikeres importálás előtt meg kell határozni azokat a problémákat, amelyeket orvosolni kell. Ez a cikk ezeket a dokumentumokat az API importálási formátuma alapján rendezi.

## <a name="open-api"> </a>OpenAPI/hencegés

Ha hibaüzenetet kap a OpenAPI-dokumentum importálásakor, győződjön meg róla, hogy előzetesen ellenőrizte. Ezt megteheti, ha a tervezőt használja a Azure Portalban (OpenAPI-specifikációs szerkesztő), vagy egy harmadik féltől származó eszközzel, például a <a href="https://editor.swagger.io">hencegés szerkesztőjével</a>.

### <a name="open-api-general"> </a>Általános

-   Az elérési út és a lekérdezés kötelező paramétereinek egyedi névvel kell rendelkezniük. (A OpenAPI csak egyedinek kell lennie egy helyen belül, például elérési út, lekérdezés, fejléc. API Management azonban lehetővé tesszük, hogy a műveletek a két útvonal és a lekérdezési paraméterek (amelyek nem támogatják a OpenAPI) megkülönböztetését. Ezért a paraméterek neveinek egyedinek kell lenniük a teljes URL-sablonon belül.)
-   a ref-mutatók nem hivatkozhatnak külső fájlokra.  **\$**
-   az **x-MS-paths** és az **x-Server** az egyetlen támogatott bővítmény.
-   Az egyéni bővítményeket a rendszer figyelmen kívül hagyja az importálás során, és nem menti vagy nem őrzi meg az exportálást.
-   **Rekurzív** – API Management nem támogatja a rekurzív módon definiált definíciókat (például a magukra hivatkozó sémák).
-   A forrásfájl URL-címe (ha elérhető) a relatív kiszolgálói URL-címekre lesz alkalmazva.
-   A biztonsági definíciók figyelmen kívül lesznek hagyva.

### <a name="open-api-v2"> </a>OpenAPI 2. verzió

-   Csak a JSON formátum támogatott.

### <a name="open-api-v3"> </a>OpenAPI 3-as verzió

-   Ha sok **kiszolgáló** van megadva, API Management megpróbálja kiválasztani az első HTTPS URL-címet. Ha nincsenek HTTPs URL-címek – az első HTTP URL-cím. Ha nincs HTTP URL-cím, akkor a kiszolgáló URL-címe üres lesz.
-   A **példák** nem támogatottak, de a **példa** :.
-   **Multipart/form – az** adattípusok nem támogatottak.

> [!IMPORTANT]
> Az OpenAPI-importálásról ebben a [dokumentumban](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) talál fontos információt és cikkeket.

## <a name="wsdl"> </a>WSDL

A WSDL-fájlok SOAP-alapú áteresztő és SOAP-REST API-k létrehozásához használhatók.

-   **SOAP** -kötések – csak a "document" és a "literális" kódolású SOAP-kötések támogatottak. Az "RPC" stílus vagy a SOAP-kódolás nem támogatott.
-   **Wsdl: import** – ez az attribútum nem támogatott. Az ügyfeleknek egyetlen dokumentumba kell egyesíteni az importálást.
-   **Több részből álló üzenetek** – az ilyen típusú üzenetek nem támogatottak.
-   **WCF wsHttpBinding** – a Windows Communication Foundation által létrehozott SOAP-szolgáltatásoknak az BasicHttpBinding-wsHttpBinding használata nem támogatott.
-   **MTOM** – a MTOM- t használó szolgáltatások működhetnek. A hivatalos támogatás jelenleg nem érhető el.
-   **Rekurzív** – a APIM nem támogatja a rekurzív módon definiált (például a saját tömbre hivatkozó) típusokat.
-   **Több névtér** – több névtér is használható egy sémában, de csak a cél névtér használható az üzenetek részeinek definiálásához. A más bemeneti vagy kimeneti elemek definiálásához használt célhelyen kívüli névterek nem őrződnek meg. Habár egy ilyen WSDL-dokumentum importálható, az összes üzenet részeinek exportálásakor a WSDL cél névterét fogja használni.
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
