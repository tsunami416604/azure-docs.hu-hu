---
title: "Importálja az Azure portál használatával OpenAPI specifikáció |} Microsoft Docs"
description: "Ismerje meg az API Management OpenAPI specifikáció importálásáról."
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
ms.openlocfilehash: f0c77c6e959ca99698b3ea704756a6abf36147f3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="import-an-openapi-specification"></a>Importálja az OpenAPI specifikáció

Ez a cikk bemutatja, hogyan http://conferenceapi.azurewebsites.net?format=json, amelynek helye "OpenAPI specification" háttér-API importálásához. A háttér-API a Microsoft által biztosított és az Azure-on tárolt. A cikk azt is bemutatja, hogyan tesztelheti a APIM API.

Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * Az "OpenAPI specification" háttér-API-k importálása
> * Az API tesztelése az Azure-portálon
> * Az API-t a fejlesztői portálra tesztelése

## <a name="prerequisites"></a>Előfeltételek

Fejezze be a következő gyorsindítási: [Azure API Management példányt létrehozni](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>Importálása és a háttér-API közzététele

1. Válassza ki **API-k** a alatt **API MANAGEMENT**.
2. Válassza ki **OpenAPI specification** a a **hozzáadása egy új API** listája.
    ![OpenAPI meghatározása](./media/import-api-from-oas/oas-api.png)
3. Adja meg a megfelelő beállításokat. Az API-értékeket állíthatja be létrehozása során. Másik lehetőségként beállíthatja némelyikük később címen a **beállítások** fülre. <br/> Ha lenyomja az **lapon** némelyikét (vagy) mezők beolvasása feltöltik a megadott háttér-szolgáltatás információval.

    ![API létrehozása](./media/api-management-get-started/create-api.png)

    |Beállítás|Érték|Leírás|
    |---|---|---|
    |**OpenAPI meghatározása**|http://conferenceapi.azurewebsites.NET?Format=JSON|A szolgáltatás megvalósítása az API-t hivatkozik. Az API management erre a címre kérelmeket továbbítja.|
    |**Megjelenített név**|*Bemutató konferencia API*|Ha a szolgáltatás URL-cím beírása után nyomja meg a tab, APIM kitölti ezt a mezőt a json a alapján. <br/>Ez a név a fejlesztői portálra.|
    |**Name (Név)**|*a bemutató-konferencia-api*|Egy egyedi nevet az API-t tartalmaz. <br/>Ha a szolgáltatás URL-cím beírása után nyomja meg a tab, APIM kitölti ezt a mezőt a json a alapján.|
    |**Leírás**|Adjon meg egy leírást az API-t.|Ha a szolgáltatás URL-cím beírása után nyomja meg a tab, APIM kitölti ezt a mezőt a json a alapján.|
    |**API URL-utótag**|*konferencia*|A utótagot fűz hozzá a az alap URL-címet a API management szolgáltatás. API Management az API-k által az utótag különbözteti meg, és ezért a utótag minden API-hoz. a megadott közzétevő egyedinek kell lennie.|
    |**URL-séma**|*HTTPS*|Meghatározza, hogy mely protokollokkal az API eléréséhez. |
    |**Termékek**|*Korlátlan*| Tegye közzé az API által az API-t társít egy termék. Opcionálisan adja hozzá az új API termékre, írja be a termék nevét. Ezt a lépést többször hozzáadni az API-t több termék is kell ismételni.<br/>A termékeket társítását, egy vagy több API-k. Számos olyan API-k, és a fejlesztői portálon keresztül a fejlesztők számára biztosíthat számukra. A fejlesztők a termék az API eléréséhez először elő kell fizetnie. Fizet elő, amikor azok beolvasása, amely a termék API-k ideális előfizetés kulcsa. A APIM példányt hozott létre, ha rendszergazdaként jelentkezett már, így minden egyes termék előfizetett alapértelmezés szerint.<br/> Alapértelmezés szerint minden API Management példányt tartalmaz két minta termékek: **alapszintű** és **korlátlan**. |

4. Kattintson a **Létrehozás** gombra.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Az új APIM API tesztelése az Azure-portálon

Műveletek hívható közvetlenül a megtekintése, és az API-k működésének teszteléséhez kényelmes megoldást kínál az Azure portálon.  

1. Válassza ki az előző lépésben létrehozott API-t.
2. Nyomja meg a **teszt** fülre.

    ![API tesztelése](./media/api-management-get-started/test-api.png)
1. Kattintson a **GetSpeakers**.

    A lap megjeleníti a mezőket a lekérdezés-paraméterek, de ebben az esetben nem tudunk bármely. A lap megjeleníti a fejlécek mezők is. A fejléc egyik "Ocp-Apim-előfizetés-kulcsot", a termék, ez az API társított előfizetés kulcshoz. A APIM példányt hozott létre, ha rendszergazdaként jelentkezett már, így a kulcs automatikusan kitölti. 
4. Nyomja le az **küldése**.

    Háttér válaszol, **200 OK** és néhány adat.

## <a name="call-operation"></a>Művelet hívása a Developer portálról

Műveletek is hívható **fejlesztői portálján** API-k teszteléséhez. 

1. Válassza ki a létrehozott API a "Import és közzététele egy háttér-API" lépéssel.
2. Nyomja le az **fejlesztői portálján**.

    ![Tesztelje a fejlesztői portálján](./media/api-management-get-started/developer-portal.png)

    A "Fejlesztői portálján" hely megnyílik.
3. Válassza ki **API**.
4. Válassza ki **konferencia API bemutató**.
5. Kattintson a **GetSpeakers**.
    
    A lap megjeleníti a mezőket a lekérdezés-paraméterek, de ebben az esetben nem tudunk bármely. A lap megjeleníti a fejlécek mezők is. A fejléc egyik "Ocp-Apim-előfizetés-kulcsot", a termék, ez az API társított előfizetés kulcshoz. A APIM példányt hozott létre, ha rendszergazdaként jelentkezett már, így a kulcs automatikusan kitölti.
6. Nyomja le az **kipróbálás**.
7. Nyomja le az **küldése**.
    
    A művelet meghívása után a fejlesztői portál megjeleníti a **Válasz állapota**, a **Válasz fejlécei** és a **Válasz tartalma** minden információját.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az átalakítási és egy közzétett API védelme](transform-api.md)
