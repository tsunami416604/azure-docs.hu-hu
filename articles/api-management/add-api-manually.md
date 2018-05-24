---
title: API manuális hozzáadása az Azure Portal használatával  | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet API-kat az API Management (APIM) használatával manuálisan felvenni.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: ef7cfa0f30eaaa426c312b21ce0a73aa4409d2ec
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="add-an-api-manually"></a>API hozzáadása manuálisan 

A cikk bemutatja, hogy az Azure Portal használatával hogyan adhat hozzá egy API-t manuálisan az API Management- (APIM-) példányhoz. Az üres, manuálisan meghatározható API-t gyakran használják API-k szimulálására. További részletek az API-k szimulálásáról: [API-válaszok szimulálása](mock-api-responses.md).

Ha egy meglévő API-t szeretne importálni, tekintse meg a [kapcsolódó témaköröket](#related-topics).

Ebben a cikkben létrehozunk egy üres API-t, és a [httpbin.org](http://httpbin.org) oldalt (egy nyilvános tesztelési szolgáltatást) háttérrendszeri API-ként határozzuk meg.

## <a name="prerequisites"></a>Előfeltételek

Végezze el a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>API létrehozása

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. A bal oldali menüben válassza az **+ API hozzáadása** elemet.
3. Válassza az **Üres API** elemet a listából.

    ![Üres API](media/add-api-manually/blank-api.png)
4. Adja meg az API beállításait.

    ![Beállítások](media/add-api-manually/settings.png)

    |**Name (Név)**|**Érték**|**Leírás**|
    |---|---|---|
    |**Megjelenített név**|„*Üres API*” |Ez a név a fejlesztői portálon jelenik meg.|
    |**Webszolgáltatás URL-címe** (nem kötelező)| „*http://httpbin.org*”| Ha egy API-t szeretne szimulálni, akkor előfordulhat, hogy nem ad meg semmit ebben a mezőben. <br/>Ebben az esetben mi a következőt adjuk meg: [http://httpbin.org](http://httpbin.org). Ez egy nyilvános tesztelési szolgáltatás. <br/>Ha olyan API-t szeretne importálni, amely automatikusan le van képezve egy háttérrendszerre, tekintse meg a [kapcsolódó témakörök](#related-topics) egyik témakörét.|
    |**URL-séma**|„*HTTPS*”|Ebben az esetben egy biztonságos HTTPS APIM-hozzáférést határozunk meg a háttérrendszerhez, bár magának a háttérrendszernek a hozzáférése nem biztonságos. <br/>Az ilyen forgatókönyveket (HTTPS–HTTP-kapcsolat) nevezzük HTTPS-zárásnak. Akkor lehet rá szükség, ha az API egy virtuális hálózatban található (és így tudja, hogy biztonságos a hozzáférés a HTTPS használata nélkül is). <br/>A „HTTPS-zárás” alkalmazásával számos processzorciklus megspórolható.|
    |**URL-cím utótagja**|„*hbin*”| Az utótag lesz a név, amely azonosítja az API-t ebben az APIM-példányban. Egyedinek kell lennie az APIM-példányon belül.|
    |**Termékek**|„*Korlátlan*” |Az API egy termékkel való társítással tehető közzé. Ha közzé szeretné tenni az API-t, hogy elérhető legyen a fejlesztők számára, adja hozzá egy termékhez. Ezt megteheti az API létrehozása során, vagy később is.<br/><br/>A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. <br/>A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így alapértelmezés szerint minden termékre előfizetett.<br/><br/> Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár: **Starter** és **Unlimited**.| 
5. Kattintson a **Létrehozás** gombra.

Ezen a ponton nem lesz olyan művelet az APIM-ban, amely leképeződik a háttérrendszeri API-ra. Ha olyan műveletet hív meg, amely közzé lett téve a háttérrendszeren keresztül, de az APIM-on keresztül nem, a **404**-es hibaüzenetet kapja. 

>[!NOTE] 
> Alapértelmezés szerint az API hozzáadásakor az APIM nem tesz közzé semmilyen műveletet, amíg fel nem veszi az engedélyezési listára (akkor sem, ha az API kapcsolódik valamilyen háttérszolgáltatáshoz). A háttérszolgáltatás engedélyezési listára való felvételéhez hozzon létre egy APIM-műveletet, amely leképezi a háttérműveletet.
>

## <a name="add-and-test-an-operation"></a>Művelet hozzáadása és tesztelése

Ez a szakasz bemutatja, hogyan hozhat létre egy „/get” műveletet, amelyet leképezhet a „http://httpbin.org/get” háttérműveletre.

### <a name="add-the-operation"></a>A művelet hozzáadása

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **+ Művelet hozzáadása** elemre.
3. Az **URL** szakaszban válassza a **GET** elemet, és írja be a „*/get*” karakterláncot az erőforrás mezőjébe.
4. A **Megjelenített név** mezőbe írja be a következőt: „*FetchData*”.
5. Kattintson a **Mentés** gombra.

### <a name="test-the-operation"></a>A művelet tesztelése

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
