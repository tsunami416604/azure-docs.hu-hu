---
title: API manuális hozzáadása az Azure Portal használatával  | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet API-kat az API Management (APIM) használatával manuálisan felvenni.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 644e29c3b5e37cd95280cfd2261e644b20bbda98
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "82203267"
---
# <a name="add-an-api-manually"></a>API hozzáadása manuálisan

A cikk bemutatja, hogy az Azure Portal használatával hogyan adhat hozzá egy API-t manuálisan az API Management- (APIM-) példányhoz. Az üres, manuálisan meghatározható API-t gyakran használják API-k szimulálására. További részletek az API-k szimulálásáról: [API-válaszok szimulálása](mock-api-responses.md).

Ha egy meglévő API-t szeretne importálni, tekintse meg a [kapcsolódó témaköröket](#related-topics).

Ebben a cikkben létrehozunk egy üres API-t, és a [httpbin.org](https://httpbin.org) oldalt (egy nyilvános tesztelési szolgáltatást) háttérrendszeri API-ként határozzuk meg.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>API létrehozása

1. Navigáljon a API Management szolgáltatáshoz a Azure Portal, és válassza az **API-kat** a menüből.
2. A bal oldali menüben válassza az **+ API hozzáadása** elemet.
3. Válassza az **Üres API** elemet a listából.  
    ![Üres API](media/add-api-manually/blank-api.png)  
4. Adja meg az API beállításait. A beállításokat az [első API-oktatóanyag importálásával és közzétételével](import-and-publish.md#-import-and-publish-a-backend-api) foglalkozó cikkben ismertetjük.
5. Kattintson a **Létrehozás** gombra.

Ezen a ponton nincsenek olyan API Management műveletek, amelyek a háttér-API műveleteire mutatnak. Ha olyan műveletet hív meg, amely a háttérön keresztül érhető el, de nem a API Managementon keresztül, akkor a rendszer **404**-as számot kap.

>[!NOTE] 
> Alapértelmezés szerint az API hozzáadásakor az APIM nem tesz közzé semmilyen műveletet, amíg fel nem veszi az engedélyezési listára (akkor sem, ha az API kapcsolódik valamilyen háttérszolgáltatáshoz). A háttérszolgáltatás engedélyezési listára való felvételéhez hozzon létre egy APIM-műveletet, amely leképezi a háttérműveletet.

## <a name="add-and-test-an-operation"></a>Művelet hozzáadása és tesztelése

Ez a szakasz bemutatja, hogyan hozhat létre egy „/get” műveletet, amelyet leképezhet a „http://httpbin.org/get” háttérműveletre.

### <a name="add-an-operation"></a>Művelet hozzáadása

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **+ Művelet hozzáadása** elemre.
3. Az **URL** szakaszban válassza a **GET** elemet, és írja be a „*/get*” karakterláncot az erőforrás mezőjébe.
4. A **Megjelenített név** mezőbe írja be a következőt: „*FetchData*”.
5. Kattintson a **Mentés** gombra.

### <a name="test-an-operation"></a>Műveletek tesztelése

Tesztelje a műveletet az Azure Portalon. Másik megoldásként a **Fejlesztői portálon** is elvégezheti a tesztelést.

1. Kattintson a **Teszt** fülre.
2. Válassza a **FetchData** elemet.
3. Kattintson a **Küldés** gombra.

Megjelenik a „http://httpbin.org/get” műveletre kapott válasz. A műveletek átalakításáról [az API átalakítását és védelmét](transform-api.md) ismertető témakörben olvashat.

## <a name="add-and-test-a-parameterized-operation"></a>Paraméteres művelet hozzáadása és tesztelése

Ez a szakasz bemutatja, hogyan adhat hozzá egy paramétert használó műveletet. Ebben az esetben a következőre képezzük le a műveletet: „http://httpbin.org/status/200”.

### <a name="add-the-operation"></a>A művelet hozzáadása

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **+ Művelet hozzáadása** elemre.
3. Az **URL** szakaszban válassza a **GET** elemet, és írja be a „*/status/{code}*” karakterláncot az erőforrás mezőjébe. Lehetősége van megadni a paraméterhez kapcsolódó információkat. A **TÍPUS** értéke például lehet „*Szám*”, az **ÉRTÉKEK** értéke pedig lehet „*200*” (az alapértelmezett érték).
4. A **Megjelenített név** mezőbe írja be a következőt: „GetStatus”.
5. Kattintson a **Mentés** gombra.

### <a name="test-the-operation"></a>A művelet tesztelése 

Tesztelje a műveletet az Azure Portalon.  Másik megoldásként a **Fejlesztői portálon** is elvégezheti a tesztelést.

1. Kattintson a **Teszt** fülre.
2. Válassza a **GetStatus** elemet. A kód alapértelmezett értéke „*200*”. Ezt szabadon módosíthatja, ha más értékeket is tesztelne. Írja be például, hogy „*418*”.
3. Kattintson a **Küldés** gombra.

    Megjelenik a „http://httpbin.org/status/200” műveletre kapott válasz. A műveletek átalakításáról [az API átalakítását és védelmét](transform-api.md) ismertető témakörben olvashat.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
