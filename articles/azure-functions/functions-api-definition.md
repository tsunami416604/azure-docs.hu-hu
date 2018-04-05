---
title: Az Azure Functions OpenAPI metaadatok |} Microsoft Docs
description: OpenAPI támogatása az Azure Functions áttekintése
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: b6aacc536e589a2036aba5a0784a4ba71641a59e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>OpenAPI 2.0 metaadatok támogatása az Azure Functions (előzetes verzió)
OpenAPI 2.0 (korábbi nevén Swagger) metaadat-támogatás az Azure Functions nem előzetes verziójú funkciók, melyekkel egy OpenAPI 2.0 definíciója belül egy függvény alkalmazás írása. Majd tárolhatja, hogy a fájl, a függvény alkalmazással.

[OpenAPI metaadatok](http://swagger.io/) lehetővé teszi, hogy egy függvény, amely üzemelteti a REST API számos más szoftverek számára. Ezek közé tartozik a Microsoft ajánlatokat, például a PowerApps és a [az Azure App Service API Apps szolgáltatásának](../app-service/app-service-web-overview.md), külső fejlesztői eszközök, például [Postman](https://www.getpostman.com/docs/importing_swagger), és [számos további csomag](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Javasoljuk a [használatába bevezető oktatóanyagot](./functions-api-definition-getting-started.md) és majd a dokumentum további információt a funkciók állítja azt vissza.

## <a name="enable"></a>OpenAPI definition támogatásának engedélyezése
Képes a beállításokat minden OpenAPI a **API-definíció** lap az függvény alkalmazás **Platform funkciói**.

Ahhoz, hogy az üzemeltetett OpenAPI definícióját, és a gyors üzembe helyezés definition előállítását, állítsa be **API definition forrás** való **függvény (előzetes verzió)**. **Külső URL-cím** lehetővé teszi, hogy a függvény egy OpenAPI-definíciót, amely rendelkezik a hosted máshol használatára.

## <a name="generate-definition"></a>A dbfunctionexpression hozzon létre egy Swagger vázat
Sablon segíthetnek az első OpenAPI definition írásához. A definíció sablon funkció használatával hozza létre ritka OpenAPI definícióját a metaadatok a function.json fájlban az egyes a HTTP-eseményindító funkciók. Adja meg az API-t a további információt kell a [OpenAPI specification](http://swagger.io/specification/), például a kérés- és sablonok.

Részletes útmutatásért lásd: a [használatába bevezető oktatóanyagot](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Az elérhető sablonok

|Name (Név)| Leírás |
|:-----|:-----|
|Generált meghatározása|A maximális méretű szükséges, amely meglévő dbfunctionexpression következtethető OpenAPI definíciója.|

### <a name="quickstart-details"></a>A generált definícióját tartalmazza metaadatok

A következő táblázat az Azure portál beállításai és function.json megfelelő adatok jelöli, a generált Swagger vázat van leképezve.

|Swagger.json|Portál felhasználói felület|Function.json|
|:----|:-----|:-----|
|[Gazdagép](http://swagger.io/specification/#fixed-fields-15)|**Alkalmazásbeállítások működéséhez** > **App Service-beállítások** > **áttekintése** > **URL-címe**|*Nem található*
|[Paths](http://swagger.io/specification/#paths-object-29)|**Integrálni** > **kijelölt HTTP-metódus**|Kötések: útvonal
|[Elérési út elem](http://swagger.io/specification/#path-item-object-32)|**Integrálni** > **útvonalsablonhoz**|Kötések: módszerek
|[Biztonság](http://swagger.io/specification/#security-scheme-object-112)|**Kulcsok**|*Nem található*|
|operationID*|**Útvonal + engedélyezett műveletek**|Útvonal + engedélyezett műveletek|

\*A művelet azonosító megadása kötelező, csak a PowerApps és Flow integrálásához.
> [!NOTE]
> Az x-ms-összegzés bővítmény biztosít a Logic Apps, PowerApps és Flow megjelenítendő nevét.
>
> További tudnivalókért lásd: [testre szabhatja a Swagger-definícióval PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>CI/CD segítségével állítsa be az API-definíció

 API-definíció a portálon üzemeltető verziókezelő módosítani az API-definíció verziókövetésből engedélyezése előtt engedélyeznie kell. Kövesse az alábbi utasításokat:

1. Keresse meg a **API Definition (előzetes verzió)** az függvény alkalmazás beállításaiban.
  1. Állítsa be **API definition forrás** való **függvény**.
  1. Kattintson a **készítése API definition sablon** , majd **mentése** később módosítása sablon definícióját létrehozásához.
  1. Vegye figyelembe a API-definíció URL-címe és a kulcsot.
1. [A folyamatos integrációs/folyamatos üzembe helyezés (CI/CD) beállítása](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Módosítsa a következő \site\wwwroot verziókezelő swagger.json\.azurefunctions\swagger\swagger.json.

Most, a tárházban swagger.json módosításai az függvény alkalmazást az API által üzemeltetett definíció URL-címe és a kulcs lépés 1.c.

## <a name="next-steps"></a>További lépések
* [Használatába bevezető oktatóanyagot](functions-api-definition-getting-started.md). Próbálja meg a bemutató lépéseit az egy OpenAPI lásd a műveletet.
* [Az Azure Functions GitHub-tárházban](https://github.com/Azure/Azure-Functions/). Tekintse meg a funkciók tárház küldjön visszajelzést az API definition támogatási előzetes a. A GitHub probléma létrehozása semmit meg szeretné tekinteni frissített.
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md). További tudnivalók a függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
