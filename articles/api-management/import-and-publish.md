---
title: "Importálja, és tegye közzé az első API-t az Azure API Management |} Microsoft Docs"
description: "Megtudhatja, hogyan importálhatja, és az első API Management API-t közzétenni."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: cd6ceaf5f8cdcfbde5d0d2bebb4b89488d0122e9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="import-and-publish-your-first-api"></a>Importálja, és tegye közzé az első API 

Az oktatóanyag bemutatja, hogyan importálhatja az "OpenAPI specification" háttér API-k http://conferenceapi.azurewebsites.net?format=json, amelynek helye. A háttér-API a Microsoft által biztosított és az Azure-on tárolt. 

A háttér-API API-felügyeleti (APIM) való importálása után a APIM API válik egy homlokzati a háttér-API számára. Importálja a háttér-API időben a forrás API- és a APIM API esetén azonosak. APIM lehetővé teszi a homlokzati igények szerinti testreszabása a háttér-API érintése nélkül. További információkért lásd: [átalakító, és az API védelme](transform-api.md). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az első API importálása
> * Az API tesztelése az Azure-portálon
> * Az API-t a fejlesztői portálra tesztelése

![Új API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Előfeltételek

Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>Importálása és a háttér-API közzététele

Ez a szakasz bemutatja, hogyan importálhatja, és egy OpenAPI specification háttér-API közzététele.
 
1. Válassza ki **API-k** a alatt **API MANAGEMENT**.
2. Válassza ki **OpenAPI specification** a listából.

    ![API létrehozása](./media/api-management-get-started/create-api.png)

    Létrehozás során, vagy később megnyitásával megadhatja az API értékeket a **beállítások** fülre.  

    |Beállítás|Érték|Leírás|
    |---|---|---|
    |**OpenAPI meghatározása**|http://conferenceapi.azurewebsites.NET?Format=JSON|A szolgáltatás megvalósítása az API-t hivatkozik. Az API management erre a címre kérelmeket továbbítja.|
    |**Megjelenített név**|*Bemutató konferencia API*|Ha a szolgáltatás URL-cím beírása után nyomja meg a tab, APIM kitölti ezt a mezőt a json a alapján. <br/>Ez a név a fejlesztői portálra.|
    |**Name (Név)**|*a bemutató-konferencia-api*|Egy egyedi nevet az API-t tartalmaz. <br/>Ha a szolgáltatás URL-cím beírása után nyomja meg a tab, APIM kitölti ezt a mezőt a json a alapján.|
    |**Leírás**|Adjon meg egy leírást az API-t.|Ha a szolgáltatás URL-cím beírása után nyomja meg a tab, APIM kitölti ezt a mezőt a json a alapján.|
    |**API URL-utótag**|*konferencia*|A utótagot fűz hozzá a az alap URL-címet a API management szolgáltatás. API Management az API-k által az utótag különbözteti meg, és ezért a utótag minden API-hoz. a megadott közzétevő egyedinek kell lennie.|
    |**URL-séma**|*HTTPS*|Meghatározza, hogy mely protokollokkal az API eléréséhez. |
    |**Termékek**|*Korlátlan*| Tegye közzé az API által az API-t társít egy termék. Opcionálisan adja hozzá az új API termékre, írja be a termék nevét. Ezt a lépést többször hozzáadni az API-t több termék is kell ismételni.<br/>A termékeket társítását, egy vagy több API-k. Számos olyan API-k, és a fejlesztői portálon keresztül a fejlesztők számára biztosíthat számukra. A fejlesztők a termék az API eléréséhez először elő kell fizetnie. Fizet elő, amikor azok beolvasása, amely a termék API-k ideális előfizetés kulcsa. A APIM példányt hozott létre, ha rendszergazdaként jelentkezett már, így minden egyes termék előfizetett alapértelmezés szerint.<br/> Alapértelmezés szerint minden API Management példányt tartalmaz két minta termékek: **alapszintű** és **korlátlan**. |
3. Kattintson a **Létrehozás** gombra.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Az új APIM API tesztelése az Azure-portálon

Műveletek hívható közvetlenül a megtekintése, és az API-k működésének teszteléséhez kényelmes megoldást kínál az Azure portálon.  
1. Válassza ki az előző lépésben létrehozott API-t.
2. Nyomja meg a **teszt** fülre.

    ![API tesztelése](./media/api-management-get-started/test-api.png)
3. Kattintson a **GetSpeakers**.

    A lap megjeleníti a mezőket a lekérdezés-paraméterek, de ebben az esetben nem tudunk bármely. A lap megjeleníti a fejlécek mezők is. A fejléc egyik "Ocp-Apim-előfizetés-kulcsot", a termék, ez az API társított előfizetés kulcshoz. A APIM példányt hozott létre, ha rendszergazdaként jelentkezett már, így a kulcs automatikusan kitölti. 
4. Nyomja le az **küldése**.

    Háttér válaszol, **200 OK** és néhány adat.

## <a name="call-operation"></a>Művelet meghívása a fejlesztői portálról

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

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az első API importálása
> * Az API tesztelése az Azure-portálon
> * Az API-t a fejlesztői portálra tesztelése

Előzetes következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Hozzon létre, és a termék közzététele](api-management-howto-add-products.md)