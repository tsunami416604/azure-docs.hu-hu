---
title: "Adja hozzá az API-k segítségével manuálisan az Azure portálon |} Microsoft Docs"
description: "Az oktatóanyag bemutatja, hogyan API Management (APIM) segítségével manuálisan ad hozzá egy API-t."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 9426839f88daece1bb688a2079b7854ccaebdc57
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="add-an-api-manually"></a>Az API-k manuális hozzáadása 

A cikkben leírt lépéseket az Azure portál segítségével az API-k manuálisan hozzá az API Management (APIM) példány szemléltetik. Egy gyakori forgatókönyv, amikor egy üres API-t létrehozni, és adja meg manuálisan szeretné akkor, ha az API-t mock szeretné. További információk az API-k mocking: [Mock API válaszok](mock-api-responses.md).

Ha egy meglévő API importálni kívánt, lásd: [kapcsolódó témakörök](#related-topics) szakasz.

Ez a cikk azt egy üres API-t létrehozni, és adja meg [httpbin.org](http://httpbin.org) (nyilvános tesztelési szolgáltatás), egy háttér-API-t.

## <a name="prerequisites"></a>Előfeltételek

Fejezze be a következő gyorsindítási: [Azure API Management példányt létrehozni](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>API létrehozása

1. Válassza ki **API-k** a alatt **API MANAGEMENT**.
2. A bal oldali menüben válassza ki a **+ Hozzáadás API**.
3. Válassza ki **üres API** a listából.

    ![Üres API](media/add-api-manually/blank-api.png)
4. Adja meg az API-beállításokat.

    ![Beállítások](media/add-api-manually/settings.png)

    |**Name (Név)**|**Érték**|**Leírás**|
    |---|---|---|
    |**Megjelenített név**|"*API üres*" |Ez a név a fejlesztői portálra.|
    |**Webszolgáltatás URL-címe** (nem kötelező)| "*http://httpbin.org*"| Ha szeretné mock egy API-t, előfordulhat, hogy meg semmit. <br/>Ebben az esetben azt adja meg [http://httpbin.org](http://httpbin.org). Ez egy olyan nyilvános tesztelési szolgáltatás. <br/>Az API-k leképezett a háttérből automatikusan importálni kívánt, lásd a témaköröket a [kapcsolódó témakörök](#related-topics) szakasz.|
    |**URL-séma**|"*HTTPS*"|Ebben az esetben annak ellenére, hogy a háttér nem biztonságos HTTP hozzáféréssel rendelkezik, azt adja meg egy biztonságos HTTPS APIM férhet hozzá a háttérben. <br/>A forgatókönyv (HTTPS – HTTP) az ilyen nevezik HTTPS-záráshoz. Akkor lehet hasznos, azt hogy létezik-e az API-t (ha tudja, hogy a hozzáférés biztonságos még akkor is, ha nem használja a HTTPS) a virtuális hálózaton belül. <br/>"HTTPS-lezárást" használni kívánt egyes CPU-ciklusok menti.|
    |**URL-utótag**|"*hbin*"| A utótag, amely azonosítja az adott API-nak a APIM példány nevét. Rendelkezik a APIM példány belül egyedinek kell lennie.|
    |**Termékek**|"*Korlátlan*" |Tegye közzé az API által az API-t társít egy termék. Ha azt szeretné, az API számára tehető közzé, és a fejlesztők számára érhető el, adja hozzá a termék. API létrehozása során teheti meg, vagy állítsa be úgy később.<br/><br/>A termékeket társítását, egy vagy több API-k. Számos olyan API-k, és a fejlesztői portálon keresztül a fejlesztők számára biztosíthat számukra. <br/>A fejlesztők a termék az API eléréséhez először elő kell fizetnie. Fizet elő, amikor azok beolvasása, amely a termék API-k ideális előfizetés kulcsa. A APIM példányt hozott létre, ha rendszergazdaként jelentkezett már, így minden egyes termék előfizetett alapértelmezés szerint.<br/><br/> Alapértelmezés szerint minden API Management példányt tartalmaz két minta termékek: **alapszintű** és **korlátlan**.| 
5. Kattintson a **Létrehozás** gombra.

Ezen a ponton még nincs APIM leképezhetőek a a háttér-API műveletek. Ha meghívja a háttérben keresztül, de nem a APIM keresztül elérhetővé művelet, kap egy **404**. 

>[!NOTE] 
> Alapértelmezés szerint amikor egy API-t, még akkor is, ha csatlakozik egy háttér-szolgáltatás APIM fog nem teszi közzé a műveleteket csak akkor engedélyezett őket. A háttér-szolgáltatás művelet engedélyezett hozzon létre egy APIM művelet, amely a háttér-művelet van leképezve.
>

## <a name="add-and-test-an-operation"></a>Hozzáadását és tesztelését egy műveletet

Ez a szakasz bemutatja, hogyan vehet fel a "/ get" művelet használhatja arra, hogy azt a háttérből "http://httpbin.org/get" művelethez.

### <a name="add-the-operation"></a>A művelet hozzáadása

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **+ Hozzáadás művelet**.
3. Az a **URL-cím**, jelölje be **beolvasása** , és adja meg "*/get*" az erőforrás.
4. Adja meg "*FetchData*" a **megjelenített név**.
5. Kattintson a **Mentés** gombra.

### <a name="test-the-operation"></a>A művelet tesztelése

A művelet tesztelése az Azure portálon. Azt is megteheti, tesztelheti, az a **fejlesztői portálján**.

1. Válassza ki a **teszt** fülre.
2. Válassza ki **FetchData**.
3. Nyomja le az **küldése**.

Megjelenik a választ, amely a "http://httpbin.org/get" műveletet hoz létre. Ha szeretné alakítani a műveleteket, lásd: [átalakító és az API védelme](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Hozzáadását és tesztelését egy paraméteres művelet

Ez a szakasz bemutatja, hogyan vehet fel a művelet, amely egy paramétert fogad. Ebben az esetben azt képezze le a "http://httpbin.org/status/200" művelet.

### <a name="add-the-operation"></a>A művelet hozzáadása

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **+ Hozzáadás művelet**.
3. Az a **URL-cím**, jelölje be **beolvasása** , és adja meg "*/status/ {code}*" az erőforrás. Igény szerint is adja meg egyes információk is társítva ezzel a paraméterrel. Adja meg például "*szám*" a **típus**, "*200*" (alapértelmezett), a **értékek**.
4. Adja meg "GetStatus" **megjelenített név**.
5. Kattintson a **Mentés** gombra.

### <a name="test-the-operation"></a>A művelet tesztelése 

A művelet tesztelése az Azure portálon.  Azt is megteheti, tesztelheti, az a **fejlesztői portálján**.

1. Válassza ki a **teszt** fülre.
2. Válassza ki **GetStatus**. Alapértelmezés szerint a kód értéke "*200*". Ez további értékek tesztelésére módosítható. Írja be például, "*418*".
3. Nyomja le az **küldése**.

    Megjelenik a választ, amely a "http://httpbin.org/status/200" műveletet hoz létre. Ha szeretné alakítani a műveleteket, lásd: [átalakító és az API védelme](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az átalakítási és egy közzétett API védelme](transform-api.md)
