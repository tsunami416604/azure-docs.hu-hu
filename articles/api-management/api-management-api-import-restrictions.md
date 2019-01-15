---
title: Korlátozások és ismert problémák az Azure API Management API importálása |} A Microsoft Docs
description: Ismert problémák és korlátozások az Open API, WSDL vagy WADL-formátumok használata az Azure API Management-ba való importálásuk részletei.
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
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 251caa840446e75ff13d9b4dcebcbae3a36473c8
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265447"
---
# <a name="api-import-restrictions-and-known-issues"></a>API importálási korlátozások és ismert problémák
## <a name="about-this-list"></a>Ez a lista kapcsolatban
API importálásakor, előfordulhat, hogy bizonyos korlátozások bővítményeként vagy azonosíthatja a problémákat, amelyek sikeresen importálása előtt kell-e javítani kell. Ez a cikk dokumentumok, ezek szerint vannak rendezve az API-formátuma.

## <a name="open-api"> </a>OpenAPI/Swagger

Az OpenAPI-dokumentumok importálása hibák azért küldtük Önnek, hogy előzetesen ellenőrizte. Ehhez vagy a Tervező használatával az Azure Portalon (- előtér - Tervező OpenAPI-specifikáció szerkesztője), vagy egy külső eszközzel például <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="open-api-general"> </a>Általános

* Elérési út és a lekérdezési paramétereket egyedi névvel kell rendelkezniük. (Az OpenAPI-paraméter neve csak egyedinek kell lennie egy helyen, például az elérési út, lekérdezés, fejléc. Azonban az API Management lehetővé tesszük műveletek legyen hátrányos megkülönböztetés elérési út és a lekérdezési paraméterek (amely nem támogatja az OpenAPI). That's miért szükséges paraméterek nevei a teljes URL-cím sablonon belül egyedinek kell lennie.)
* **$ref** mutatók nem hivatkozhatnak külső fájlok.
* **x-ms-elérési utak** és **x-kiszolgálók** csak támogatott bővítmények.
* Egyéni bővítmények rendszer figyelmen kívül hagyja az importálás és nem mentett, vagy megőrzi az exportálás.
* **A rekurzió** – API Management nem támogatja a megadott rekurzív módon (például önmagukra hivatkozó sémák).
* Forrás fájl URL-címe (ha elérhető) érvényes relatív URL-eket.

### <a name="open-api-v2"> </a>OpenAPI 2-es verzió

* Csak a JSON-formátum támogatott.

### <a name="open-api-v3"> </a>OpenAPI 3-as verzió

* Ha sok **kiszolgálók** meg van adva, az API Management megpróbálja válassza ki az első HTTPs URL-címet. Ha nincs semmilyen HTTPs URL – az első HTTP URL-címet. Ha nincs minden HTTP URL-címek – a kiszolgáló URL-címe üres lesz.
* **Példák** nem támogatott, de **példa** van.
* **Multipart/form-data** nem támogatott.

> [!IMPORTANT]
> Az OpenAPI-importálásról ebben a [dokumentumban](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) talál fontos információt és cikkeket.

## <a name="wsdl"> </a>WSDL
WSDL-fájlt a SOAP átmenő API-k készítése vagy folyamatában SOAP – REST API-t, a háttérrendszer használja.
* **A SOAP-kötések** -stílus "dokumentumok" és "szövegkonstans" kódolás csak a SOAP-kötések támogatottak. A rendszer nem támogatja, a "rpc" style vagy a SOAP-kódolást.
* **WSDL** – Ez az attribútum nem támogatott. Ügyfeleink a import kell egyesítése egy dokumentumot.
* **A több részből üzenetek** – az ilyen típusú üzenetek nem támogatottak.
* **A WCF wsHttpBinding** -SOAP-szolgáltatások a Windows Communication Foundation létrehozott használjon basicHttpBinding – wsHttpBinding nem támogatott.
* **Zprávy MTOM** – zprávy MTOM használó szolgáltatások <em>előfordulhat, hogy</em> működik. Hivatalos támogatási jelenleg nem kínáljuk.
* **Rekurze** -típusokat, amelyek meghatározott rekurzív módon (például lásd tömbjét magukat) APIM által nem támogatott.
* **Több névteret** – több névteret sémát is használható, de csak a célnévtér részeket meghatározására használható. Más bemeneti vagy kimeneti elemek definiálják, amely eltérő a céltól névterek nem maradnak meg. Bár az ilyen a WSDL-dokumentum importálhatók, az exportálás minden üzenet részére lesz a WSDL célnévtér.

## <a name="wadl"> </a>WADL
Jelenleg nincsenek nem WADL-importálási ismert problémák.
