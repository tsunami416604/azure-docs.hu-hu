---
title: Csatlakozás REST-végpontokhoz az Azure Logic Apps alkalmazásból
description: REST-végpontok figyelése automatizált feladatokban, folyamatokban és munkafolyamatokban az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787369"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>REST-végpontok hívása az Azure Logic Apps használatával

[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a beépített HTTP + Swagger-összekötő segítségével automatizálhatja azokat a munkafolyamatokat, amelyek rendszeresen megszólítanak bármely REST-végpontot egy [Swagger-fájlon](https://swagger.io) keresztül logikai alkalmazások létrehozásával. A HTTP + Swagger eseményindító és a művelet ugyanúgy működik, mint a [HTTP-eseményindító és a művelet,](connectors-native-http.md) de jobb élményt nyújt a Logic App Designer az API-struktúra és a Swagger-fájl által leírt kimenetek felfedésével. A lekérdezési eseményindító megvalósításához kövesse az egyéni API-k létrehozása című, az egyéni API-k létrehozása című lekérdezési [mintát, és hívja meg a logikai alkalmazásokból származó más API-kat, szolgáltatásokat és rendszereket.](../logic-apps/logic-apps-create-api-app.md#polling-triggers)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A Swagger (nem OpenAPI) fájl URL-címe, amely a cél REST-végpontot írja le

  A REST-végpontnak általában meg kell felelnie ezeknek a feltételeknek ahhoz, hogy az összekötő működjön:

  * A Swagger-fájlt nyilvánosan elérhető HTTPS-URL-címen kell üzemeltetni.

  * A Swagger-fájlban engedélyezni kell [az originek közötti erőforrás-megosztást (CORS).](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

  Hivatkozzon egy Swagger-fájl, amely nem üzemelteti, vagy amely nem felel meg a biztonsági és a határokon átnyúló követelményeknek, [feltöltheti a Swagger-fájlt egy Azure-tárfiók blobtárolójába,](#host-swagger)és engedélyezheti a CORS-t a tárfiókon, hogy hivatkozhasson a fájlra.

  Ebben a témakörben szereplő példák a [Cognitive Services Face API-t](https://docs.microsoft.com/azure/cognitive-services/face/overview)használják, [amelyhez Cognitive Services-fiók és hozzáférési kulcs](../cognitive-services/cognitive-services-apis-create-account.md)szükséges.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A logikai alkalmazás, ahonnan meg szeretné hívni a célvégpontot. A HTTP + Swagger eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) A HTTP + Swagger művelet használatához indítsa el a logikai alkalmazást bármilyen eseményindítóval, amit csak akar. Ez a példa a HTTP + Swagger eseményindítót használja az első lépésként.

## <a name="add-an-http--swagger-trigger"></a>HTTP + Swagger eseményindító hozzáadása

Ez a beépített eseményindító http-kérelmet küld egy Swagger-fájl URL-címére, amely leírja a REST API-t, és a fájl tartalmát tartalmazó választ ad vissza.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Nyissa meg az üres logikai alkalmazást a Logic App Designerben.

1. A tervező, a keresőmezőbe, írja be a "swagger", mint a szűrő. Az **eseményindítók** listájában válassza ki a **HTTP + Swagger** eseményindítót.

   ![Válassza a HTTP + Swagger eseményindítólehetőséget](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. A **SWAGGER ENDPOINT URL-címmezőjébe** írja be a Swagger-fájl URL-címét, és válassza a **Tovább**gombot.

   Ez a példa a Swagger URL-címet használja, amely az USA nyugati régiójában található a [Cognitive Services Face API-hoz:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Adja meg a Swagger-végpont URL-címét](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Amikor a tervező megjeleníti a Swagger-fájl által leírt műveleteket, válassza ki a használni kívánt műveletet.

   ![Műveletek a Swagger-fájlban](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Adja meg az eseményindító paraméterek értékeit, amelyek a kijelölt művelettől függően változnak, és amelyeket a végponthívásba szeretne felvenni. Állítsa be az ismétlődést, hogy milyen gyakran hívja meg az eseményindító a végpontot.

   Ez a példa átnevezi az eseményindítót "HTTP + Swagger trigger: Face - Detect" névre, hogy a lépés nek leíróbb neve van.

   ![Művelet részletei](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. További elérhető paraméterek hozzáadásához nyissa meg az **Új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

   A HTTP + Swagger számára elérhető hitelesítési típusokról a [Hitelesítés hozzáadása kimenő hívásokhoz című](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)témakörben talál további információt.

1. Folytassa a logikai alkalmazás munkafolyamatának felépítését olyan műveletekkel, amelyek az eseményindító aktiválódásakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

## <a name="add-an-http--swagger-action"></a>HTTP + Swagger művelet hozzáadása

Ez a beépített művelet http-kérelmet küld a Swagger-fájl URL-címére, amely leírja a REST API-t, és a fájl tartalmát tartalmazó választ ad vissza.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A HTTP + Swagger művelet hozzáadásának lépése alatt válassza az **Új lépés**lehetőséget.

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. A tervező, a keresőmezőbe, írja be a "swagger", mint a szűrő. A **Műveletek** listában válassza a **HTTP + Swagger** műveletet.

    ![HTTP + Swagger művelet kijelölése](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. A **SWAGGER ENDPOINT URL-címmezőjébe** írja be a Swagger-fájl URL-címét, és válassza a **Tovább**gombot.

   Ez a példa a Swagger URL-címet használja, amely az USA nyugati régiójában található a [Cognitive Services Face API-hoz:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Adja meg a Swagger-végpont URL-címét](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Amikor a tervező megjeleníti a Swagger-fájl által leírt műveleteket, válassza ki a használni kívánt műveletet.

   ![Műveletek a Swagger-fájlban](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Adja meg a műveletparaméterek értékeit, amelyek a kijelölt művelettől függően változnak, és amelyeket a végponthívásba szeretne felvenni.

   Ebben a példában nincsenek paraméterek, de átnevezi a műveletet a "HTTP + Swagger művelet: Face - Identify", hogy a lépés egy leíró nevet.

   ![Művelet részletei](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. További elérhető paraméterek hozzáadásához nyissa meg az **Új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

   A HTTP + Swagger számára elérhető hitelesítési típusokról a [Hitelesítés hozzáadása kimenő hívásokhoz című](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)témakörben talál további információt.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Swagger gazdagépe az Azure Storage-ban

Hivatkozhat egy Swagger-fájlra, amely nem üzemeltetett, vagy amely nem felel meg a biztonsági és a határokon átnyúló követelményeknek, ha feltölti a fájlt blobtárolóba egy Azure-tárfiókban, és engedélyezi a CORS-t az adott tárfiókon. Swagger-fájlok létrehozásához, beállításához és tárolásához kövesse az alábbi lépéseket:

1. [Hozzon létre egy Azure-tárfiókot.](../storage/common/storage-create-storage-account.md)

1. Most engedélyezze a CORS-t a blobhoz. A tárfiók menüjében válassza a **CORS lehetőséget.** A **Blob szolgáltatás** lapon adja meg ezeket az értékeket, majd kattintson a **Mentés gombra.**

   | Tulajdonság | Érték |
   |----------|-------|
   | **Engedélyezett eredetek** | `*` |
   | **Engedélyezett módszerek** | `GET`, `HEAD`, `PUT` |
   | **Engedélyezett fejlécek** | `*` |
   | **Közzétett fejlécek** | `*` |
   | **Maximális életkor** (másodpercben) | `200` |
   |||

   Bár ez a példa az [Azure Portalt](https://portal.azure.com)használja, használhat egy eszközt, például [az Azure Storage Explorert,](https://storageexplorer.com/)vagy automatikusan konfigurálhatja ezt a beállítást ezzel a [PowerShell-parancsfájllal.](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)

1. [Hozzon létre egy blob tárolót.](../storage/blobs/storage-quickstart-blobs-portal.md) A tároló **Áttekintő** ablaktábláján válassza a **Hozzáférési szint módosítása**lehetőséget. A **Nyilvános hozzáférési szint** listában válassza a **Blob (névtelen olvasási hozzáférés csak blobok esetén)** lehetőséget, majd az **OK**gombot.

1. [Töltse fel a Swagger-fájlt a blobtárolóba,](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)vagy az [Azure Portalon](https://portal.azure.com) vagy az [Azure Storage Exploreren](https://storageexplorer.com/)keresztül.

1. A blobtárolóban lévő fájlra való hivatkozáshoz használjon az ezt a formátumot követő HTTPS-hivatkozást, amely a kis- és nagybetűket is figyelembe vevő:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Összekötő-referencia

Itt további információt a http + swagger eseményindító vagy művelet kimenetek. A HTTP + Swagger hívás a következő információt adja vissza:

| Tulajdonság neve | Típus | Leírás |
|---------------|------|-------------|
| Fejlécek | objektum | A kérelem fejlécei |
| body (Törzs) | objektum | JSON-objektum | A kérelemből származó törzstartalommal rendelkező objektum |
| állapotkód | int | A kérelem állapotkódja |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Rossz kérés |
| 401 | Nem engedélyezett |
| 403 | Forbidden |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
