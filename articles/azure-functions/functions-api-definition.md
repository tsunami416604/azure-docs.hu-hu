---
title: OpenAPI-metaadatok az Azure Functionsben
description: Az OpenAPI-támogatás áttekintése az Azure Functionsben
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: a80079574dc29c54de89f5275c65637b205742d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227417"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Az OpenAPI 2.0 metaadat-támogatása az Azure Functionsben (előzetes verzió)
Az OpenAPI 2.0 (korábban Swagger) metaadat-támogatása az Azure Functionsben egy előzetes verziójú szolgáltatás, amely segítségével OpenAPI 2.0-definíciót írhat egy függvényalkalmazáson belül. Ezután a függvényalkalmazás segítségével üzemeltetheti a fájlt.

> [!IMPORTANT]
> Az OpenAPI előzetes verziójú funkció csak ma, az 1.x futtatókörnyezetben érhető el. 1.x függvényalkalmazás létrehozásáról [itt találhat](./functions-versions.md#creating-1x-apps) további információt.

[Az OpenAPI metaadatai](https://swagger.io/) lehetővé teszik, hogy a REST API-t tároló függvényt számos más szoftver használja fel. Ez a szoftver magában foglalja a Microsoft ajánlatait, például a PowerApps-et és az [Azure App Service API-alkalmazások funkcióját,](../app-service/overview.md)a külső fejlesztői eszközöket , például [a Postmant](https://www.getpostman.com/docs/importing_swagger)és [még sok más csomagot.](https://swagger.io/tools/)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Azt javasoljuk, hogy kezdje az [első lépések oktatóanyag,](./functions-api-definition-getting-started.md) majd térjen vissza ehhez a dokumentumhoz, hogy többet tudjon meg a konkrét funkciókról.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>OpenAPI-definíció támogatás engedélyezése
Az összes OpenAPI-beállítást a függvényalkalmazás platformfunkcióinak **API-definíciója** lapján **állíthatja be.**

> [!NOTE]
> A függvény API-definíciós szolgáltatása jelenleg nem támogatott a béta-futtatási időhez.

A hosztolt OpenAPI-definíció és egy rövid útmutató definíció létrehozásának engedélyezéséhez állítsa **az API-definícióforrást** **Függvény (előzetes verzió)** értékre. **A külső URL lehetővé** teszi, hogy a függvény máshol tárolt OpenAPI-definíciót használjon.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Swagger csontváz létrehozása a függvény metaadataiból
A sablon segítségével elkezdheti írni az első OpenAPI-definíciót. A definíciós sablonszolgáltatás ritka OpenAPI-definíciót hoz létre a function.json fájlban lévő összes metaadatot használva az egyes HTTP-eseményindító függvényekhez. Az [OpenAPI specifikációból](https://swagger.io/specification/)további információkat kell kitöltenie az API-ról, például kérés- és válaszsablonokat.

A részletes útmutatást az [első lépések oktatóanyagcímű témakörben találja.](./functions-api-definition-getting-started.md)

### <a name="available-templates"></a><a name="templates"></a>Elérhető sablonok

|Név| Leírás |
|:-----|:-----|
|Létrehozott definíció|OpenAPI-definíció, amely a függvény meglévő metaadataiból kikövetkeztethető maximális mennyiségű információt tartalmazza.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Metaadatok beleszámítva a létrehozott definícióba

Az alábbi táblázat az Azure Portal beállításait és a megfelelő adatokat a function.json, ahogy a létrehozott Swagger csontváz leképezve.

|Swagger.json|Portál felhasználói felülete|Funkció.json|
|:----|:-----|:-----|
|[Állomás](https://swagger.io/specification/#fixed-fields-15)|**A Függvényalkalmazás beállításai** > **az App Service beállításai –** > **áttekintés** > **URL**|*Nincs jelen*
|[Elérési utak](https://swagger.io/specification/#paths-object-29)|**A** > **kijelölt HTTP-módszerek integrálása**|Kötések: Útvonal
|[Görbe elem](https://swagger.io/specification/#path-item-object-32)|**Integrate** > **Útvonalsablon integrálása**|Kötések: Módszerek
|[Biztonság](https://swagger.io/specification/#security-scheme-object-112)|**Kulcsok**|*Nincs jelen*|
|műveletazonosítója*|**Útvonal + Engedélyezett műveletek**|Útvonal + Engedélyezett műveletek|

\*A műveletazonosító csak a PowerApps és a Flow integrálásához szükséges.
> [!NOTE]
> Az x-ms-summary bővítmény megjelenítendő nevet biztosít a Logic Apps, a PowerApps és a Flow alkalmazásban.
>
> További információ: [A Swagger-definíció testreszabása a PowerApps alkalmazáshoz](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)című témakörben olvashat.

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>API-definíció beállítása CI/CD használatával

 Engedélyeznie kell az API-definíció üzemeltetését a portálon, mielőtt engedélyezné a forrásvezérlést az API-definíció forrásvezérlésből történő módosításához. Kövesse az alábbi utasításokat:

1. Tallózással keresse meg **az API Definition (előzetes verzió)** nézetet a függvényalkalmazás beállításaiközött.
   1. Állítsa **az API-definícióforrást** **függvényre.**
   1. Kattintson **az API-definíciós sablon létrehozása,** majd **a Mentés** gombra a későbbi módosításhoz szükséges sablondefiníció létrehozásához.
   1. Jegyezze fel az API-definíció URL-címét és kulcsát.
1. [Folyamatos integráció/folyamatos üzembe helyezés (CI/CD) beállítása.](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment)
2. Módosítsa a swagger.json parancsot a\.forrásvezérlőben a \site\wwwroot azurefunctions\swagger\swagger.json mappában.

Most a swagger.json módosításait a tárházban a függvényalkalmazás üzemelteti az API-definíció URL-címén és az 1.c lépésben észlelt kulcson.

## <a name="next-steps"></a>További lépések
* [Első lépések bemutató](functions-api-definition-getting-started.md). Próbálja ki a forgatókönyvet, hogy egy OpenAPI-definíciót működés közben láthasson.
* [Azure Functions GitHub-tárház.](https://github.com/Azure/Azure-Functions/) Tekintse meg a Functions repository visszajelzést az API-definíció támogatási előzetes verziójáról. Készíts egy GitHub-problémát minden, amit szeretne látni frissíteni.
* [Az Azure Functions fejlesztői útmutatója](functions-reference.md). Ismerje meg a kódolási funkciókat, valamint az eseményindítók és kötések definiálását.
