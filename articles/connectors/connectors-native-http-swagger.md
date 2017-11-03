---
title: "Hívja a Swagger kiegészítve HTTP REST-végpontok Azure Logic Apps-összekötője |} Microsoft Docs"
description: "REST-végpontok csatlakozni a logic Apps alkalmazásokból, kiegészítve a HTTP Swagger Swagger keresztül összekötő"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 3e9229d94e96aad7b769d0e55d208d856e3b80bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-http--swagger-action"></a>Ismerkedjen meg a HTTP és a Swagger művelet

A REST-végpont keresztül az első osztályú összekötő hozhat létre egy [Swagger-dokumentum](https://swagger.io) a HTTP és a Swagger használata esetén a logic app munkafolyamat művelet. A logic apps hívni minden REST-végpont első osztályú Logic App Designer nyújthassunk is kiterjeszthető.

Az összekötők logic Apps alkalmazások létrehozásához, lásd: [új logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>A HTTP + Swagger egy eseményindító vagy egy műveletet

A HTTP + Swagger indul el, és azonos módon működik, a művelet a [HTTP-művelet](connectors-native-http.md) jelentkezik, mintha az API-struktúra és kimeneteinek Logic App Designer jobb környezetet biztosítson, de a [Swagger-metaadatok](https://swagger.io). Használhatja a HTTP + Swagger összekötő kiindulópontként. Lekérdezési eseményindító végrehajtásához, hajtsa végre a lekérdezés minta ismertetett [hozzon létre egyéni API-k hívására más API-k, a szolgáltatások és a rendszer a logic Apps alkalmazásokból](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

További információ [logic app eseményindítók és műveletek](connectors-overview.md).

Itt található példa bemutatja, hogyan használja a HTTP és a Swagger-műveleti logikai alkalmazás munkafolyamata a műveletet.

1. Válassza ki a **új lépés** gombra.
2. Válassza ki **művelet hozzáadása**.
3. A művelet a keresőmezőbe írja be **swagger** lista a HTTP + Swagger művelet.
   
    ![Válassza ki a HTTP + Swagger művelet](./media/connectors-native-http-swagger/using-action-1.png)
4. Írja be a Swagger-dokumentum URL-címe:
   
   * A Logic App Designer használatához az URL-cím HTTPS-végpontnak kell lennie, és a CORS engedélyezve van.
   * A Swagger-dokumentum nem felel meg ennek a követelménynek, ha [Azure Storage a CORS engedélyezése mellett](#hosting-swagger-from-storage) a fájlt kívánja tárolni.
5. Kattintson a **következő** olvassa el és a Swagger-dokumentumból.
6. Adja hozzá a HTTP-hívás a szükséges paramétereket.
   
    ![Teljes HTTP-művelet](./media/connectors-native-http-swagger/using-action-2.png)
7. Mentse, és tegye közzé a Logic Apps alkalmazást, kattintson a **mentése** designer eszköztáron.

### <a name="host-swagger-from-azure-storage"></a>Az Azure Storage állomás Swagger
Előfordulhat, hogy szeretne hivatkozni, amely nem található, vagy, amely nem felel meg a biztonsági és a Tervező eltérő eredetű követelményei Swagger dokumentumot. A probléma megoldásához, a Swagger-dokumentum az Azure Storage tárolja, és hivatkozni, a dokumentum a CORS engedélyezése.  

Létrehozása, konfigurálása és a Swagger-dokumentumok tárolására az Azure Storage lépései a következők:

1. [Az Azure storage-fiók létrehozása az Azure Blob storage szolgáltatással](../storage/common/storage-create-storage-account.md). Ez a lépés végrehajtásához engedélyek beállítása **nyilvános hozzáférés**.

2. A CORS engedélyezése blobot. 

   Ezzel a beállítással automatikusan konfigurálásához használható [a PowerShell parancsfájl](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. A Swagger-fájl feltöltése a blob. 

   Az ebben a lépésben végezheti el a [Azure-portálon](https://portal.azure.com) vagy egy eszköz, például a [Azure Tártallózó](http://storageexplorer.com/).

4. A dokumentum az Azure Blob storage HTTPS-kapcsolat hivatkozik. 

   A hivatkozás ezt a formátumot használja:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Technikai részletek
Az alábbiakban az eseményindítók és műveletek részleteit, amely a HTTP + Swagger összekötő támogatja.

## <a name="http--swagger-triggers"></a>HTTP + Swagger eseményindítók
Egy eseményindító egy eseményt, amely segítségével indítsa el a munkafolyamatot, amely a logikai alkalmazás van definiálva. [További tudnivalók az eseményindítók.](connectors-overview.md) A HTTP és a Swagger összekötő egy eseményindító tartozik.

| Eseményindító | Leírás |
| --- | --- |
| HTTP + Swagger |Egy HTTP-hívást, és vissza válasz tartalma |

## <a name="http--swagger-actions"></a>HTTP + Swagger műveletek
Egy művelet során, amely a logikai alkalmazás definiált munkafolyamat végzi. [Ismerje meg a műveletet.](connectors-overview.md) A HTTP és a Swagger összekötő tartozik egy lehetséges művelet.

| Műveletek | Leírás |
| --- | --- |
| HTTP + Swagger |Egy HTTP-hívást, és vissza válasz tartalma |

### <a name="action-details"></a>A művelet részletei
A HTTP és a Swagger összekötő egy lehetséges műveletet tartalmaz. Az alábbiakban az egyes műveletek, a szükséges és választható beviteli mezők és a megfelelő kimeneti részletek használatát társított információkat.

#### <a name="http--swagger"></a>HTTP + Swagger
Ellenőrizze a kimenő HTTP-kérelem a Swagger-metaadatok segítséget.
Egy csillag (*) azt jelenti, hogy a mezőt kötelező kitölteni.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Módszer * |Módszer |Használja a HTTP-műveletet. |
| URI * |URI |A HTTP-kérelem URI-Azonosítóját. |
| Fejlécek |Fejlécek |A HTTP-fejlécek tartalmazza JSON-objektum. |
| Törzs |Törzs |A HTTP-kérés törzsében. |
| Authentication |Hitelesítés |Hitelesítési kérelem használatára. További információkért lásd: a [HTTP összekötő](connectors-native-http.md#authentication). |

**Kimeneti részletei**

HTTP-válasz

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |Objektum |Válaszfejlécek |
| Törzs |Objektum |Válasz objektum |
| Állapotkód |int |HTTP-állapotkód: |

### <a name="http-responses"></a>HTTP-válaszok
Amikor különböző műveletekkel, bizonyos válaszokat kaphat. Az alábbiakban látható egy táblázat a megfelelő válaszok és leírásokat.

| Név | Leírás |
| --- | --- |
| 200 |OKÉ |
| 202 |Elfogadva |
| 400 |Helytelen kérelem |
| 401 |Nem engedélyezett |
| 403 |Tiltott |
| 404 |Nem található |
| 500 |Belső kiszolgálóhiba. Ismeretlen hiba történt. |

- - -
## <a name="next-steps"></a>Következő lépések

* [Logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md)
* [Más összekötők keresése](apis-list.md)