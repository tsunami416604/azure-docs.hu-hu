---
title: Csatlakozás az Azure Logic Apps a REST-végpontok
description: REST-végpontokat az automatizált feladatokat, folyamatok és munkafolyamatok monitorozása az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541584"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>REST-végpontok hívása az Azure Logic Apps használatával

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a beépített HTTP + Swagger összekötő, akkor automatizálhatja, amelyek rendszeresen meg bármely REST-végponton keresztül egy [Swagger-fájl](https://swagger.io) logikai alkalmazások létrehozásával. A HTTP + Swagger-trigger és műveleti azonos módon működik a [HTTP-eseményindító és művelet](connectors-native-http.md) , de a Logic App Designerben jobb felhasználói élményt nyújtanak teszi elérhetővé az API szerkezete és a Swagger-fájl által leírt kimenetek. Lekérdezési eseményindító implementálásához az alábbi lekérdezés mintát leírt [meghívhatnak más API-kat, szolgáltatásokat és rendszereket a logic apps egyéni API-k létrehozása](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A Swagger-fájl, amely leírja a cél REST-végpont URL-címe

  Általában a REST-végpont az összekötő működéséhez a feltételeknek kell megfelelnie:

  * A Swagger-fájl kell futhat HTTPS URL-címet, amely nyilvánosan elérhető-e.

  * Rendelkeznie kell a Swagger-fájl [eltérő eredetű erőforrások megosztása (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) engedélyezve van.

  Egy Swagger-fájl, amely nem üzemel, vagy, amely nem felel meg a biztonsági és az eltérő eredetű követelményeknek, hivatkozni is [a Swagger-fájl feltöltése a blob-tárolóba az Azure storage-fiók](#host-swagger), és így az a tárfiók CORS engedélyezése hogy a fájl hivatkozhat.

  Ez a témakör használatban a példák a [Cognitive Services Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview), ami megköveteli egy [Cognitive Services-fiók és hozzáférési kulcsát](../cognitive-services/cognitive-services-apis-create-account.md).

* Alapvető ismeretek szerezhetők [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A logikai alkalmazás, ahonnan csak szeretné, a céloldali végpont meghívására. A kezdéshez a HTTP + Swagger aktiválásához [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Használja a HTTP + Swagger-műveletet a logikai alkalmazás kezdje bármilyen kívánt eseményindító. Az ebben a példában a HTTP + Swagger eseményindító első lépéseként.

## <a name="add-an-http--swagger-trigger"></a>Adja hozzá a HTTP + Swagger-eseményindító

A beépített trigger HTTP-kérést küld egy URL-címet a Swagger-fájl, amely ismerteti a REST API-t, és a egy választ, amely tartalmazza a fájl tartalmát adja vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. A tervezőben a Keresés mezőbe írja be "swagger" a szűrőnek. Az a **eseményindítók** listáról válassza ki a **HTTP + Swagger** eseményindító.

   ![Válassza a HTTP + Swagger-trigger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Az a **SWAGGER VÉGPONTI URL-cím** mezőbe írja be az URL-címet a Swagger-fájl, és válassza ki **tovább**.

   Ebben a példában a Swagger URL-címet, amely esetében az USA nyugati régiójában található a [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Adja meg az URL-címet a Swagger-végpont](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. A tervezőben a Swagger-fájl által leírt műveleteket jeleníti meg, amikor válassza ki a használni kívánt műveletet.

   ![Műveletek a Swagger-fájl](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Adja meg a az eseményindító paraméterei, amelyek eltérőek lehetnek, amelyeket szeretne szerepeltetni a szolgáltatásvégpont hívása a kijelölt művelet alapján. Állítsa be számára, hogy milyen gyakran kívánja az eseményindító az ismétlődést a végpont meghívására.

   Ebben a példában az eseményindító, átnevezi "HTTP + Swagger aktiválása: A Face – észlelése"úgy, hogy a lépés egy leíró nevet.

   ![Művelet részletei](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Egyéb elérhető paraméterek hozzáadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt paramétereket.

   A HTTP + Swagger elérhető hitelesítési típusok kapcsolatos további információkért lásd: [hitelesítéshez a HTTP-eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Folytassa a logikai alkalmazás munkafolyamat-műveletek, amelyek futtatását, amikor az eseményindító aktiválódik.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A Tervező eszköztárán válassza **mentése**.

## <a name="add-an-http--swagger-action"></a>Adjon hozzá egy HTTP + Swagger művelet

A beépített művelet hajt végre egy HTTP-kérelem az URL-címet a Swagger-fájl, amely ismerteti a REST API-t, és a egy választ, amely tartalmazza a fájl tartalmát adja vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. Hol szeretne hozzáadni a HTTP + Swagger feladatütemezésekben műveletet, válassza **új lépés**.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. Válassza a pluszjelet ( **+** ), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A tervezőben a Keresés mezőbe írja be "swagger" a szűrőnek. Az a **műveletek** listáról válassza ki a **HTTP + Swagger** művelet.

    ![Válassza ki a HTTP + Swagger művelet](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Az a **SWAGGER VÉGPONTI URL-cím** mezőbe írja be az URL-címet a Swagger-fájl, és válassza ki **tovább**.

   Ebben a példában a Swagger URL-címet, amely esetében az USA nyugati régiójában található a [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Adja meg az URL-címet a Swagger-végpont](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. A tervezőben a Swagger-fájl által leírt műveleteket jeleníti meg, amikor válassza ki a használni kívánt műveletet.

   ![Műveletek a Swagger-fájl](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. A paraméterek értékének megadására a művelet, amelyek eltérőek lehetnek, amelyeket szeretne szerepeltetni a szolgáltatásvégpont hívása a kijelölt művelet alapján.

   Ebben a példában nincsenek paraméterei, de nevez át a műveletet az "a HTTP + Swagger művelet: A Face – azonosítása"úgy, hogy a lépés egy leíró nevet.

   ![Művelet részletei](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Egyéb elérhető paraméterek hozzáadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt paramétereket.

   A HTTP + Swagger elérhető hitelesítési típusok kapcsolatos további információkért lásd: [hitelesítéshez a HTTP-eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A Tervező eszköztárán válassza **mentése**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Gazdagép Swagger az Azure Storage-ban

Egy Swagger-fájl, amely nem üzemel, vagy, amely nem felel meg a biztonsági és az eltérő eredetű követelményeknek, hogy a fájl feltöltése a blob-tárolóba az Azure storage-fiók és a tárfiók CORS lehetővé tevő hivatkozhat. Hozzon létre, és állítsa be a Swagger-fájlok tárolására az Azure Storage-ban, kövesse az alábbi lépéseket:

1. [Hozzon létre egy Azure-tárfiókot](../storage/common/storage-create-storage-account.md).

1. Most már a BLOB CORS engedélyezése. A tárfiók menüjében kattintson a **CORS**. Az a **Blob service** lapot, adja meg ezeket az értékeket, majd **mentése**.

   | Tulajdonság | Érték |
   |----------|-------|
   | **Engedélyezett eredetek** | `*` |
   | **Engedélyezett metódusok** | `GET`, `HEAD`, `PUT` |
   | **Engedélyezett fejlécek** | `*` |
   | **Közzétett fejlécek** | `*` |
   | **Maximális kor** (másodpercben) | `200` |
   |||

   Bár ebben a példában a [az Azure portal](https://portal.azure.com), például használhat olyan eszközöket [Azure Storage Explorer](https://storageexplorer.com/), vagy automatikusan konfigurálja ezt a beállítást a minta használatával [PowerShell-parancsfájl](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Hozzon létre egy blobtárolót](../storage/blobs/storage-quickstart-blobs-portal.md). A tároló **áttekintése** ablaktáblán válassza előbb **hozzáférési szint módosítása**. Az a **nyilvános hozzáférés szintje** listáról válassza ki **Blob (névtelen olvasási hozzáférés csak blobokhoz)** , és válassza ki **OK**.

1. [A Swagger-fájl feltöltése a blob-tárolóba](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), vagy – a [az Azure portal](https://portal.azure.com) vagy [Azure Storage Explorer](https://storageexplorer.com/).

1. A blob-tárolóban fájl hivatkozik, használja a HTTPS-kapcsolat a következő ezt a formátumot, amely kis-és nagybetűket:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Összekötő-referencia

Egy HTTP + Swagger kimenetei további információt a következő eseményindítót vagy műveletet. A HTTP + Swagger irányuló hívások visszaadják az ezeket az információkat:

| Tulajdonság neve | Típus | Leírás |
|---------------|------|-------------|
| A fejlécek | object | A kérelemből a fejlécek |
| Törzs | object | JSON-objektum | Az objektum a kérelem törzsében tartalommal |
| Állapotkód | int | A kérelem az állapotkód: |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Hibás kérés |
| 401 | Nem engedélyezett |
| 403 | Tiltott |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)