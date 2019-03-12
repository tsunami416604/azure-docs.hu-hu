---
title: REST-végpontok hívása az Azure Logic Apps |} A Microsoft Docs
description: Feladatok és a munkafolyamatok, amelyek REST-végpontokat kommunikálnak a HTTP + Swagger használatával automatizálhatja az Azure Logic Apps-összekötőt
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
tags: connectors
ms.topic: article
ms.date: 07/18/2016
ms.openlocfilehash: de61443da41f1015b2890e9052f38e25d3bed86a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551970"
---
# <a name="call-rest-endpoints-with-http--swagger-connector-in-azure-logic-apps"></a>REST-végpontokat HTTP + Swagger összekötő az Azure Logic Appsben

Egy első osztályú bármely REST-végponton keresztül-összekötőt hozhat létre egy [Swagger-dokumentumok](https://swagger.io) a HTTP + Swagger használata esetén a logikai alkalmazás munkafolyamatának műveletét. Is kiterjesztheti a logic apps bármely Logikaialkalmazás-Tervező fürtkezelési élményt REST-végpont meghívható.

Ismerje meg, hogyan hozhat létre a logic apps-összekötők, lásd: [hozzon létre egy új logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Használat HTTP + Swagger egy eseményindítót vagy műveletet

A HTTP + Swagger-trigger és műveleti azonos módon működik a [HTTP-művelet](connectors-native-http.md) teszi elérhetővé az API szerkezete és kimeneteinek Logic App Designerben jobb felhasználói élményt biztosít, de a [Swagger-metaadatok](https://swagger.io). Is használhatja a HTTP + Swagger összekötő eseményindítóként. Szeretne egy lekérdezési eseményindító végrehajtása, ha az alábbi lekérdezés mintát leírt [meghívhatnak más API-kat, szolgáltatásokat és rendszereket a logic apps egyéni API-k létrehozása](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Tudjon meg többet [logikaialkalmazás-triggerek és műveletek](connectors-overview.md).

Íme egy példa bemutatja, hogyan használja a HTTP + Swagger művelet-műveletként a logikai alkalmazás munkafolyamata.

1. Válassza ki a **új lépés** gombra.
2. Válassza ki **művelet hozzáadása**.
3. A művelet be a keresőmezőbe írja be **swagger** a lista a HTTP + Swagger művelet.
   
    ![Válassza ki a HTTP + Swagger művelet](./media/connectors-native-http-swagger/using-action-1.png)
4. Írja be az URL-címet a Swagger-dokumentumok:
   
   * Használhatja a Logikaialkalmazás-Tervező, az URL-cím HTTPS-végpontokat kell lennie, és a CORS engedélyezve van.
   * A Swagger-dokumentumok nem felel meg ennek a követelménynek, ha Azure Storage a CORS engedélyezve van a dokumentum tárolására is használható.
5. Kattintson a **tovább** olvasását és a Swagger-dokumentum jelennek meg.
6. Adja hozzá a szükséges a HTTP-hívás paramétereit.
   
    ![Teljes HTTP-művelet](./media/connectors-native-http-swagger/using-action-2.png)
7. Mentés és a logikai alkalmazás közzététele, kattintson a **mentése** a Tervező eszköztárán.

### <a name="host-swagger-from-azure-storage"></a>Gazdagép Swagger az Azure Storage-ból
Előfordulhat, hogy szeretne hivatkozni egy Swagger-dokumentumot, amely nem üzemel, illetve, amely nem felel meg a biztonsági és az eltérő eredetű vonatkozó követelményeket a tervezőben. A probléma megoldásához, a Swagger-dokumentumok tárolása az Azure Storage-ban, és hivatkozni a dokumentum a CORS engedélyezése.  

A létrehozása, konfigurálása és a Swagger-dokumentumok tárolása az Azure Storage-ban lépései a következők:

1. [Az Azure storage-fiók létrehozása az Azure Blob storage](../storage/common/storage-create-storage-account.md). Ez a lépés végrehajtásához engedélyek beállítása **nyilvános hozzáférés**.

2. A CORS engedélyezése a blobot. 

   Ezzel a beállítással automatikusan konfigurálásához használható [a PowerShell-szkript](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. A Swagger-fájl feltöltése a blobba. 

   Ez a lépés elvégzése a [az Azure portal](https://portal.azure.com) vagy egy eszköz, például [Azure Storage Explorer](https://storageexplorer.com/).

4. A dokumentum az Azure Blob storage-ban való HTTPS-kapcsolat hivatkozhat. 

   A hivatkozás a formátumot használja:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Technikai részletek
Eseményindítók és műveletek részleteit az alábbiakban, amely a HTTP + Swagger összekötő támogatja.

## <a name="http--swagger-triggers"></a>A HTTP + Swagger-eseményindítók
Egy trigger egy eseményt, amely a logikai alkalmazásban definiált munkafolyamat elindításához használható. [További információ az eseményindítók.](connectors-overview.md) A HTTP + Swagger összekötő egy eseményindító tartozik.

| Eseményindító | Leírás |
| --- | --- |
| HTTP + Swagger |Egy HTTP-hívást, és adja vissza a válasz tartalma |

## <a name="http--swagger-actions"></a>A HTTP + Swagger-műveletek
Egy műveletet, amely a logikai alkalmazásban definiált munkafolyamat által végzett művelet. [További információ azokról a műveletekről.](connectors-overview.md) A HTTP + Swagger összekötő tartalmaz egy lehetséges műveletet.

| Műveletek | Leírás |
| --- | --- |
| HTTP + Swagger |Egy HTTP-hívást, és adja vissza a válasz tartalma |

### <a name="action-details"></a>Művelet részletei
A HTTP + Swagger összekötő tartalmaz egy lehetséges műveletet. Következő információkkal szolgál a műveleteket, a szükséges és választható beviteli mezőt és a használatuk társított megfelelő kimenet részletei.

#### <a name="http--swagger"></a>HTTP + Swagger
Győződjön meg arról, kimenő HTTP-kérést a Swagger-metaadatok segítséget.
Egy csillag (*) azt jelenti, hogy a kötelező kitölteni.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Módszer * |method |HTTP-parancs használata. |
| URI * |uri azonosító |A HTTP-kérés URI azonosítója. |
| Fejlécek |A fejlécek |A HTTP-fejléceket tartalmazza JSON-objektum. |
| Törzs |törzs |A HTTP-kérelem törzse. |
| Authentication |hitelesítés |Hitelesítési kérelem használatára. További információkért lásd: a [HTTP-összekötő](connectors-native-http.md#authentication). |

**Kimenet részletei**

HTTP-válasz

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |objektum |Válaszfejlécek |
| Törzs |objektum |Válaszobjektum |
| Állapotkód |int |HTTP-állapotkód |

### <a name="http-responses"></a>HTTP-válaszok
Bizonyos válaszok irányuló hívásokhoz különböző műveleteket hajthat végre, előfordulhat, hogy kap. Következő egy táblázat a megfelelő válaszok és leírásokat.

| Name (Név) | Leírás |
| --- | --- |
| 200 |OK |
| 202 |Elfogadva |
| 400 |Hibás kérés |
| 401 |Nem engedélyezett |
| 403 |Tiltott |
| 404 |Nem található |
| 500 |Belső kiszolgálóhiba. Ismeretlen hiba történt. |

- - -
## <a name="next-steps"></a>További lépések

* [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Más összekötők keresése](apis-list.md)
