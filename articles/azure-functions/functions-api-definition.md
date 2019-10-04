---
title: OpenAPI-metaadatok a Azure Functionsban | Microsoft Docs
description: A Azure Functions OpenAPI-támogatásának áttekintése
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 00dedd069914384a1883a54bfb433857286fb49c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097579"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>OpenAPI 2,0 metaadat-támogatás a Azure Functionsban (előzetes verzió)
A OpenAPI 2,0 (korábban hencegés) metaadatainak támogatása Azure Functions egy előzetes funkció, amellyel OpenAPI 2,0-definíciót írhat a Function alkalmazásban. Ezt a fájlt ezután a Function alkalmazás használatával futtathatja.

> [!IMPORTANT]
> Az OpenAPI előzetes verziójú funkció csak ma, az 1.x futtatókörnyezetben érhető el. 1\.x függvényalkalmazás létrehozásáról [itt találhat](./functions-versions.md#creating-1x-apps) további információt.

A [OpenAPI-metaadatok](https://swagger.io/) lehetővé teszik, hogy egy olyan függvényt, amely a különböző szoftverek széles körének használatát REST API üzemelteti. Ez a szoftver olyan Microsoft-ajánlatokat is tartalmaz, mint a PowerApps, valamint a Azure App Service, harmadik féltől származó [](https://www.getpostman.com/docs/importing_swagger)fejlesztői eszközök, például a Poster és [számos további csomag](https://swagger.io/tools/) [API apps funkcióját](../app-service/overview.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Javasoljuk, hogy kezdje a [kezdeti lépéseket ismertető oktatóanyaggal](./functions-api-definition-getting-started.md) , majd térjen vissza ehhez a dokumentumhoz, és tudjon meg többet az egyes funkciókról.

## <a name="enable"></a>OpenAPI-definíció támogatásának engedélyezése
A Function app **platform funkcióinak**API- **definíciós** lapján az összes OpenAPI beállításait konfigurálhatja.

> [!NOTE]
> A függvény API-definíciós funkciója jelenleg nem támogatott a bétaverziós futtatókörnyezet esetében.

Egy üzemeltetett OpenAPI-definíció és egy rövid útmutató meghatározásának engedélyezéséhez állítsa be az **API-definíció forrását** a **Function (előzetes verzió)** értékre. A **külső URL-cím** lehetővé teszi, hogy a függvény egy máshol üzemeltetett OpenAPI-definíciót használjon.

## <a name="generate-definition"></a>Hencegő csontváz létrehozása a függvény metaadataiból
A sablonok segítségével megkezdheti az első OpenAPI-definíció megírását. A definíciós sablon szolgáltatás egy ritka OpenAPI-definíciót hoz létre a function. JSON fájlban található összes metaadat használatával az egyes HTTP-trigger függvényeknél. További információkat kell kitöltenie az API-ról a OpenAPI- [specifikációból](https://swagger.io/specification/), például a kérések és a válaszok sablonjairól.

Részletes útmutatásért tekintse meg az [első lépéseket ismertető oktatóanyagot](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Elérhető sablonok

|Name (Név)| Leírás |
|:-----|:-----|
|Generált definíció|Egy OpenAPI-definíció, amely a függvény meglévő metaadataiból kikövetkeztethető maximális mennyiségű adatot tartalmaz.|

### <a name="quickstart-details"></a>Tartalmazott metaadatok a generált definícióban

Az alábbi táblázat a function. JSON fájl Azure Portal beállításait és a hozzájuk tartozó, a generált hencegő csontvázhoz rendelt adatait mutatja be.

|Swagger.json|Portál felhasználói felülete|Function.json|
|:----|:-----|:-----|
|[Gazdagép](https://swagger.io/specification/#fixed-fields-15)|**Function app Settings** > **app Service beállítások** >  **– Áttekintés** > **URL-cím**|*Nincs jelen*
|[Elérési utak](https://swagger.io/specification/#paths-object-29)| > **Kiválasztott http** -metódusok integrálása|Kötések Útválasztás
|[Elérési út eleme](https://swagger.io/specification/#path-item-object-32)| > **Útválasztási sablon** integrálása|Kötések Metódusok
|[Biztonság](https://swagger.io/specification/#security-scheme-object-112)|**Kulcsok**|*Nincs jelen*|
|operationID*|**Route + engedélyezett műveletek**|Route + engedélyezett műveletek|

\*A műveleti azonosító csak a PowerApps és a flow integrálásához szükséges.
> [!NOTE]
> Az x-MS-Summary bővítmény megjelenített nevet biztosít Logic Apps, PowerApps és flow-ban.
>
> További információkért lásd: [a PowerApps-hez készült hencegés definíciójának testreszabása](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>API-definíció beállítása CI/CD használatával

 Engedélyeznie kell az API-definíció üzemeltetését a portálon, mielőtt engedélyezi a verziókövetés számára az API-definíciók forrás-vezérlőelemből való módosítását. Kövesse az alábbi utasításokat:

1. Tallózással keresse meg az **API-definíciót (előzetes verzió)** a Function app-beállításokban.
   1. Állítsa be az **API-definíció forrását** a **működéshez**.
   1. Kattintson az **API-definíciós sablon létrehozása** lehetőségre, majd a **Mentés** gombra a sablon definíciójának létrehozásához a későbbi módosításhoz.
   1. Jegyezze fel az API-definíció URL-címét és kulcsát.
1. [Folyamatos integráció/folyamatos üzembe helyezés (CI/CD) beállítása](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. A \site\wwwroot\.azurefunctions\swagger\swagger.JSON.-ben módosítsa a hencegő. JSON fájlt a forrás vezérlőelemben.

Most az adattárban lévő hencegés. JSON fájl módosításait az API-definíció URL-címe és a kulcs, amelyet az 1. c lépésben feljegyzett a függvény futtat.

## <a name="next-steps"></a>További lépések
* [Első lépések oktatóanyag](functions-api-definition-getting-started.md). Próbálja ki a bemutatót egy OpenAPI-definíció működés közbeni megtekintéséhez.
* [Azure functions GitHub-tárház](https://github.com/Azure/Azure-Functions/). Tekintse meg a functions tárházat, és küldjön visszajelzést az API-definíció támogatásának előzetes verziójáról. Hozzon fel GitHub-problémát minden, amit frissíteni szeretne.
* [Azure functions fejlesztői referenciája](functions-reference.md). A kódolási függvények ismertetése és az eseményindítók és kötések meghatározása.
